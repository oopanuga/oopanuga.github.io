---
layout: post
title: Evolution of Asynchronous Programming features in the .NET Platform
---

> The more patterns you need to use in a 

Even if you normally don't write multithreading applications, sooner or later you will find yourself dealing with some asynchronous programming, 
e.g. using an HttpClient that returns a async task. 
Multithreading is regularly scaring, it is complex, hard to debug, hard to write. I'll try to make it a bit less scaring for my friends out there.

## Lack of responsiveness

Let's start with a nice simple WPF app. We have a desktop app with a simple button:

{% highlight c# %}
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
    }

    private void Button_DisplayRecords(object sender, RoutedEventArgs e)
    {
        recordMatches.Content = "Not Implemeted yet****";
    }
}
{% endhighlight %}

When we click in that button, we want to call the FindMyPast api to get a list of historical record matches:
    
{% highlight c# %}
public class FindMyPastClient
{
    public static IList<string> GetRecords(string requestId)
    {
        WebClient webClient = new WebClient();
        string data = webClient.DownloadString("http://api.findmypast.com/recordMatches" + requestId);
        data = data.Split(new char[] { '\n' })[1];
        return data.Split(new char[] { ',' });
    }
}
{% endhighlight %}

We open a WebClient, it goes to the web and get a list of hints that we then split.
Now we are ready to use that in our button:

{% highlight c# %}
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
    }
    
    private void Button_DisplayRecords(object sender, RoutedEventArgs e)
    {
      IList<string> matches = FindMyPastClient.GetRecords(searchBox.Text);
      recordMatches.Content = string.Join(", ", matches);
    }
}
{% endhighlight %}

This works, we hit the search box and we get back a list of hints. 

## Making the App responsive in the hard way

The issue there is that the application is not responsive while the it is requesting the data.
We show that to the Product Manager and she says: _Juan, please fix that, we can't have our users waiting to se their matches_.
Since I am a clever guy, I search in stackoverlow.com about it and I find out about Threads, then I come with the following solution:

{% highlight c# %}
private void Button_DisplayRecords(object sender, RoutedEventArgs e)
{
  var search = searchBox.Text
  new Thread(delegate()
  {
    IList<string> matches = FindMyPastClient.GetRecords(search);
    recordMatches.Dispatcher.Invoke(() => recordMatches.Content = string.Join(", ", matches));
  }).Start();
}
{% endhighlight %}

We start a separate Thread to manage the request. Since we had already a thread which is the main thread of the application, we extract the value of the field before the new thread so we avoid locking.
We also use Dispatcher.Invoke inside the new Thread to update the UI component, this is done by providing a Lambda expression that will run in the UI thread.
This is working and it is responsive now.

## Using pool of threads, Task objects

The problem with the previous approach is that we need to be thinking things like: what threads are we in? Could it be locked by other thread?...
In addition everything is managed by delegate methods that make our code really difficult to write and debug.
The main issue really is predictability, this code can fail in different ways in different machines because we don't know about what threads are running in a given machine.

I google again and some results talks about using something called _Tasks_. Let's see if we can use that weird thing:

{% highlight c# %}
private void Button_DisplayRecords(object sender, RoutedEventArgs e)
{
    var search = searchBox.Text;
    Task<IList<string>> task = new Task<IList<string>>(() => FindMyPastClient.GetRecords(search));
    task.ContinueWith(completedTask => DisplayResult(completedTask.Result));
    task.Start();
}

private void DisplayResult(object result)
{
    recordMatches.Dispatcher.Invoke(() => recordMatches.Content = string.Join(", ", matches));
}
{% endhighlight %}

We encapsulated the request into a nice Task object rather than starting a new Thread. We don't have to worry about what thread we are in, we let the processor manage this task in a pool of threads, the task will be run in the next thread available. 
This leads to a better resource ussage.

We still have the issue though with the lock of the UI to update it. Therefore we still need to use the Dispatcher.Invoke.

## Using _async_ and _await_

Now let's compare for a second the first non-responsive solution with this last solution:

**Non-responsive:**

{% highlight c# %}
private void Button_DisplayRecords(object sender, RoutedEventArgs e)
{
  IList<string> matches = FindMyPastClient.GetRecords(searchBox.Text);
  recordMatches.Content = string.Join(", ", matches);
}
{% endhighlight %}

**Responsive using Tasks:**

{% highlight c# %}
private void Button_DisplayRecords(object sender, RoutedEventArgs e)
{
    var search = searchBox.Text;
    Task<IList<string>> task = new Task<IList<string>>(() => FindMyPastClient.GetRecords(search));
    task.ContinueWith(completedTask => DisplayResult(completedTask.Result));
    task.Start();
}

private void DisplayResult(object result)
{
    recordMatches.Dispatcher.Invoke(() => recordMatches.Content = string.Join(", ", matches));
}
{% endhighlight %}

The functionality is the same but, two lines of codes have suddenly become 5. The complexity of the code has increased as soon as we have introduce asynchronous features.
We can see also we are going back and forth from the main thread to other threads. 

What if we could let the compiler manage all that complexity? Fortunately we can do that since .Net 4.5 introduced **async** and **await**:

{% highlight c# %}
private async void Button_DisplayRecords(object sender, RoutedEventArgs e)
{
    var search = searchBox.Text;
    var recordMatches = await Task<IList<string>>.Run(() => FindMyPastClient.GetRecords(search));
    recordMatches.Content = string.Join(", ", matches)
}
{% endhighlight %}

We create a task to do some asynchronous operation and the main thread **awaits** for it to be finished. Then we can update things in the UI.
The **async** keyword tells the compiler, please run asynchronously what you find inside the method. In other words, an async method will run in as many threads as required.

Just for today, I'll put some comments inside the method to understand what the compiler does:

{% highlight c# %}
private async void Button_DisplayRecords(object sender, RoutedEventArgs e)
{
    //Part 1: It runs here
    var search = searchBox.Text;
    
    //Part 2: May run in a separate thread
    var recordMatches = await Task<IList<string>>.Run(() => FindMyPastClient.GetRecords(search));
    
    //Part 3: Doesn't even run here. This is written in another method by the compiler
    recordMatches.Content = string.Join(", ", matches)
}
{% endhighlight %}

This code is back to simplicity like it was in the first solution, except that it will be asynchronous and so our app will be responsive.

## Abstracting async

This later solution is great but the UI side is managing the asynchronous part. Let's focus now on the _FindMyPastClient_. If you were the backend developer you would to make your colleagues' lives easier, you'd take care of the asynchronous part so your friends in the UI would have a better time creating a nicer FrontEnd without having to worry about it.
Fortunately that is the case, Phil Hoy the architect has done a code review for their FindMyPastClient, they have created a new method:

{% highlight c# %}
public class FindMyPastClient
{
    public static async Task<IList<string>> GetRecordsAsync(string requestId)
    {
        return await Task<<IList<string>>.Run(() => GetRecords(requestId));
    }
    
    public static IList<string> GetRecords(string requestId)
    {
        WebClient webClient = new WebClient();
        string data = webClient.DownloadString("http://api.findmypast.com/recordMatches" + requestId);
        data = data.Split(new char[] { '\n' })[1];
        return data.Split(new char[] { ',' });
    }
}
{% endhighlight %}

Notice that now the new method returns a Task. In the WPF application we can simply do this:

{% highlight c# %}
private async void Button_DisplayRecords(object sender, RoutedEventArgs e)
{
    var search = searchBox.Text;
    var recordMatches = await FindMyPastClient.GetRecordsAsync(search);
    recordMatches.Content = string.Join(", ", matches)
}
{% endhighlight %}

Some benefits of this last approach:

- This abstraction has simplied the code in the caller of the client.

- Better encapsulation, calling methods don't need to worry about implementation details.

- The caller now doesn't rely on the client method, therefore this client can change without impacting the caller.


That's all for now. I hope you found it useful and foremost you are not that scare about asynchronous programming.



_This blog post is based on Venkat [Subramaniam video](https://vimeo.com/68320505) in NDC Conference_.
