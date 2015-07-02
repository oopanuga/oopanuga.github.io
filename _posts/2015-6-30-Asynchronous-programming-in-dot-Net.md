---
layout: post
title: Evolution of Asynchronous Programming features in the .NET Platform
---

> The more patterns you need, the less your framework is helping you.

Even if you normally don't write multithreading applications, sooner or later you will find yourself dealing with some asynchronous programming, 
e.g. using an HttpClient that returns an async task. 
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

The issue there is that the application is blocked while we are requesting the data (if we try to move the window it doesn't work).
Let's pretend that we show that to the Product Manager and she says: 
_Juan, please fix that, we can't have our users waiting to se their matches_.

I search in Google about it and I find Threads, and I come up with the following solution:

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

We start a separate Thread to manage the request. Since we had the main application thread, we extract the value of the field of the UI, before our new thread start executing.

We also use *Dispatcher.Invoke* inside the new Thread to update the UI component. We provide a Lambda expression that will run in the UI thread. This is working and it is responsive now. I can move around the window while requesting the data.

## Using pool of threads, Task objects

We get another User Story, we need to add another button and another search field. Initially I try to use the same approach. The problem with that is: we need to think on what threads are we in? Could it be locked by other thread?...
In addition everything is managed by delegate methods that make our code really difficult to write and debug.

The main issue really is **predictability**, this code can fail in different ways in different machines, we don't know what threads are running in a given machine.

I google again and I end up in StackOverflow, where people talk about something called **Tasks**. Let's see if we can use that weird thing:

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

Rather than starting a new Thread, we encapsulate the request into a nice Task object. Now we don't have to worry about what thread we are in, simply we let the processor manage our task in a pool of threads, the task will be running in the next thread available. This leads to a better resource ussage.

## Using _async_ and _await_

Let's compare for a second the first non-responsive solution with this last solution:

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

The functionality is the same but, two lines of code have suddenly become 5. The complexity of the code has increased as soon as we have introduce asynchronous features.

What if we could let the compiler manage all that complexity and our code became simple again? Fortunately .Net 4.5 provides some syntactic sugar:  **async** and **await** keywords:

{% highlight c# %}
private async void Button_DisplayRecords(object sender, RoutedEventArgs e)
{
    var search = searchBox.Text;
    var recordMatches = await Task<IList<string>>.Run(() => FindMyPastClient.GetRecords(search));
    recordMatches.Content = string.Join(", ", matches)
}
{% endhighlight %}

We still use the task but the main thread **await** for this task to be finished. Then it can go on and update things in the UI. 

The **async** keyword tells the compiler: 

*This method is async, please run asynchronously what you find inside it.*

The compiler will run this method in as many threads as required.

I'll put some comments inside the method to understand what the compiler does:

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

This code is back to simplicity like it was in the first solution, except that it will be asynchronous and our app will be responsive.

## Abstracting async

This later solution is great but the UI is carrying all the asynchronous complexity in its shoulders. 
Fortunately Phil Hoy, the architect, has done a code review for the *FindMyPastClient*, he says:

> Please embrace the async features in the _FindMyPastClient_ api. Then the UI guys don't have to take care of all the heavy dutty.

Let's try to make Phil happy:

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

C# has provided wonderful features to make your code very similar to secuencial code, but managing concurrency with a very nice syntax sugar. 

One may argue that **async and await** hide everything and people don't have to understand what's going on. 
In my opinion, you still need to know the basics but once you have understand, then you don't need to worry anymore about tedious code and let the compiler generating that for you.

I hope you found this article useful, foremost you are not that scared anymore about asynchronous programming in C#. I feel like I'm going to use these concepts write now:)

**await new Task(() => Console.Log("Have a great day!"));**


----
_This blog post is based on Venkat [Subramaniam video](https://vimeo.com/68320505) in NDC Conference_.
