---
layout: post
title: Don't mock what you don't own
---


## Only mock types that you own

Currently I'm reading [Growing Object-Oriented Software, Guided by Tests](https://www.amazon.co.uk/Growing-Object-Oriented-Software-Guided-Signature/dp/0321503627), by Steve Freeman and Nat Pryce.
I can't recommend this book more, I should have read it long time ago. The book is an amazing explanation on how the authors design and test object-oriented software.

One of the Chapters reads **Building on Third-Party code**:

> Providing mock implementations of third-party types is of limited use when unit-testing the objects that call them. We find that tests that mock external libraries often need to be complex to get the code into the right state for the functionality we need to exercise.
The mess in such tests is telling us that the design isn't right but, instead of fixing the problem by improving the code, we have to carry the extra complexity in both code and test.

## A practical example, an http client

A few months ago I was writing a class that was using an HttpClient. Something like this:

{% highlight cs %}
public class HintsClient : IHintsClient
{
  private readonly HttpClient _httpClient;

  public HintsClient(HttpClient httpClient)
  {
    _httpClient = httpClient;
    _httpClient.BaseAddress = new Uri("http://some-address");
  }

  public IList<HintDto> GetHints(FormattedFamilyTreeDto inputFamilyTree)
  {
      return _httpClient
              .PostAsJsonAsync("endpoint-url?rows=3&type=5", inputFamilyTree)
              .Result
              .EnsureSuccessStatusCode()
              .Content.ReadAsAsync<IList<HintDto>>()
              .Result;
  }
}
{% endhighlight %}

I have written this kind of code so many times. This code does the job, using an standard .Net class HttpClient, we post some data to an endpoint and then, if the request is successful, we read the result.
Everything works as expected, but there is one problem: how do we test this? 
We could try to create a mock of the http client and define all the required methods:

{% highlight cs %}
[Fact]
public void Must_get_hints_for_a_family_fragment()
{
    var inputFamilyTree = new FormattedFamilyTreeDto();
    var response = new List<HintDto> { new HintDto(), new HintDto() };
    var responseMessage = new HttpResponseMessage
                              {
                                  Content = new StringContent(JArray.FromObject(response).ToString()),
                              };
    responseMessage.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    var httpClientFake = Substitute.For<HttpClient>();
    httpClientFake.PostAsJsonAsync(Arg.Any<string>(), inputFamilyTree)
      .Returns(Task<HttpResponseMessage>.Factory.StartNew(() => responseMessage));

    var hints = Sut.GetHints(inputFamilyTree);
    hints.Count.Should().Be(2);
}
{% endhighlight %}

We are creating a fake response message which is returned as a result of an asynchronous task which is the returned value of the post asynchronous operation.
Here we are using the NSubstitute library for mocking.

I have heard so many times: *Well, this class uses an http client, it has to be hard to test*, or, *this class doesn't need to be unit tested, it is just using an http client.*
I see tests as a good friend that tell you the good things and also the bad, things that no one else would tell you but a true friend.
In this case, the test got very hard to write, it is telling us that probably there is something wrong with our design.

## Adapter pattern to rescue

**Adapter** was described in [GoF design patterns](https://www.amazon.co.uk/Design-patterns-elements-reusable-object-oriented/dp/0201633612):

> The adapter pattern is used to provide a link between two otherwise incompatible types by wrapping the "adaptee" with a class that supports the interface required by the client.

The idea is that we create an *adapter object* that interfaces the world outside of our application.
We keep this in a thin layer, as thin as possible, so that we minimize the amount of potentially brittle and hard-to-test-code.
As stated in the *Growing object-oriented design* book, this layer define the relationship between our application and the rest of the world in our application's terms and discourages low-level technical concepts from leaking into the application domain model.

## Redesigning our client class

Let's create an adapter for the .Net http client:

{% highlight cs %}
public interface IHttpClientAdapter
{
  Task<Response> Post<TType>(string uri, TType content);
}
{% endhighlight %}

Notice how we have simplified the returned type and how we have hidden the internal details of the http client, including the asynchronous behaviour.
Now in the test we can mock this adapter interface:

{% highlight cs %}
[Fact]
public void Must_get_hints_for_a_family_fragment()
{
  var inputFamilyTree = new FormattedFamilyTreeDto();
  var fakeResponse = new List<HintDto> { new HintDto(), new HintDto() };
  var httpClientFake = Substitute.For<IHttpClientAdapter>();
  httpClientFake.Post<FormattedFamilyTreeDto>(Arg.Any<string>(), inputFamilyTree)
    .Returns(Task<Response>.FromResult(fakeResponse));

  var hints = Sut.GetHints(inputFamilyTree);
  hints.Count.Should().Be(2);
}
{% endhighlight %}

Finally the implementation of our client class looks like this after the refactoring:

{% highlight cs %}
public class HintsClient : IHintsClient
{
  private readonly IHttpClientAdapter _httpClientAdapter;

  public HintsClient(IHttpClientAdapter httpClientAdapter)
  {
    _httpClientAdapter = httpClientAdapter;
  }

  public IList<HintDto> GetHints(FormattedFamilyTreeDto inputFamilyTree)
  {
      var response = await _httpClientAdapter
              .Post("endpoint-url?rows=3&type=5", inputFamilyTree);
      return response.Hints;
  }
}
{% endhighlight %}

Creating our adapter layer has also led to a nice side effect: we have defined new types that define abstractions to the object coming from the outside world.
We have avoided one common anti-pattern, [Primitives obsession](https://sourcemaking.com/refactoring/smells/primitive-obsession).
In other words, in order to be able to test our domain logic, we have created an interface which in terms has led to a better design. Hopefully if we want to change the internal implementation of the client to a different technology, our domain logic and tests won't have to change.

## Wrapping up, Listen to the Tests:

I'd like to leave you with yet another great piece in the book, **Listen to the Tests**:

> Our experience is that, when code is difficult to test, the most likely cause is that our design needs improving. The same structure that makes the code difficult to test now will make it difficult to change in the future. By the time that future comes around, a change will be more difficult still because we'll have forgotten what we were thinking when we wrote the code.
Our response is to regard the process of writing tests as a valuable early warning of potential maintenance problems and to use those hints to fix a problem while it's still fresh.

