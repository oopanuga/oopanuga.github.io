---
layout: post
title: Test Driven Development
---

> TDD is not writing unit tests, TDD is a way of writing code.

_Writing a test first? Why? The code is not even implemented, I don´t know how I´m going to do that.
At least let me create my empty classes before I start writing those tests._

That was my thinking when somebody told me to do TDD.
_Ok, let me read something about that test first stuff..._

## Test Driven Development, the basics.

_Test Driven Development_ (TDD) is a software development process that relies on the repetition of a very short development cycle. We start by breaking down a user story into smaller chunks, the smaller, the better.
Each of these chunks are taken and expressed in the form of a test. 

This test should be initially failing, the functionality is not yet there, right? Failing to compile is a failing test as well. We make this test pass by writing the minimum amount of code to pass that test, of course it means making it to compile. After that we refactor the code written so far to make improvements, including tests and production code. Let´s take a closer look to the Algorithm:

![Image description](/images/tdd/TDDAlg.PNG)

1. *Add a test*: Let´s take one of the smaller bits on which we broke our requirement (happy path and exceptions). We write a test for it. To write a test we must understand the specification, that´s where the power of TDD starts, we would name the test to describe clearly what we are just about to develop. This is a differentiating feature of test-driven development versus writing unit tests after the code is written: it makes the developer focus on the requirements before writing the code, a subtle but important difference.

2. *Run all tests and see if the new one fails*: This validates that the test harness is working correctly, that the new test does not mistakenly pass without requiring any new code, and that the required feature does not already exist. The new test should also fail for the expected reason.

3. *Write some code*: The next step is to write the minimum code that causes the test to pass. The new code written at this stage is not perfect and may pass the test in a vague way. It doesn´t matter at this stage, it will improved later. At this point, the only purpose of the written code is to pass this test. Don´t think in any other use case, don´t think the typical _if this other thing happens_. Write down it and we´ll take it later.

4. *Run tests*: Great! All of the requirements so far are met. If some of the tests break we must readjust the code written so far until they do.

5. *Refactor code*: Everything is working so far, now we need to clean up all the code. According to the book by _Martin Fowler_, _Refactoring is a disciplined technique for restructuring an existing body of code, altering its internal structure without changing its external behavior_.
So that´s our boundary, we don´t have to modify already developed behavior, we don´t have to add more behavior. We have to improve the code. In that sense, we will check if we are following [Design Principles](http://javicaria.github.io/NDC-Oslo-CoreSoftwareDesignPrinciples). In order to meet these principles we may follow the set of practices described in Martin Fowler´s book, we may apply a design pattern, like Strategy pattern. We can do it safely because we have all of the tests written so far checking that we don´t break anything. We can be confident that this stage is not altering any existing functionality.

6. *Repeat*: We take another little bit of functionality and we start all over again. We do it until every requirement is met.

This technique was developed by _Kent Beck_, and stated in his book _Test-Driven Development by Example_ in 2003. TDD encourages simple designs and inspires confidence. I wrote a [post](http://javicaria.github.io/Uncle-Bob-TDD-Alive/) explaining the benefits of using TDD. But for me one of my favorites is the following:

Imagine we work developing a huge CRM system. Every time we make a little change in the system, we should test manually everything to make sure everything works. That´s when the idea of unit testing comes, we write tests for the functionality, automatic tests that will be always running, checking that everything still works.

But what if we take it further? Why don´t we allow those tests that describe the behavior that must be implemented, to drive our development. The benefit is straight: the resulting production code will satisfy every user requirement doing that the simplest way possible. We won´t over engineer, we would just write the minimum code.

> I encourage you to try test-driven development, and write unit tests because I personally have found them to be the most beneficial practice I have ever followed in my career. And the one secret to this is that tests are just as much about design as they are about insuring quality. Scott Allen

## _Ok, I got it , let me try..._

It all sounds reasonable by the book, but we know practicing is the hardest part. Let´s exercise it with the following exercise. Do you want to do some pair programming with me?

We are writing an application on which we can rate restaurants. We pick the next task and it states the following:

{% highlight c# %}
The business wants to get the average rating of a restaurant. They want to try different approaches and see which one works the best. For now we have two different ways:

1. One approach to calculating the overall rating for a restaurant would be to just take the simple average of the rating property for the last N number of reviews where N is something an administrator can configure, so it might be the last 10 reviews or the last 100 reviews.
 
2. Another approach would be to compute a weighted mean where the most recent reviews are more heavily weighted. That would benefit a restaurant that is improving after a bad start and getting better reviews. It might also benefit a customer who is thinking about going to a restaurant that used to have great quality, but is now on the decline.
{% endhighlight %}

If we don´t use TDD we would just sit down with a blank piece of paper and design the class structure for this. Instead we will use TDD from scratch. We don´t even have a vague idea on how to do this, we just know the requirements.

For this example we are going to use a framework called _MSpec_, [Machine Specification](https://github.com/machine/machine.specifications), instead of other popular ones (MSTest, JUnit or NUnit). I like the way it expresses the tests (BDD style, more about it later). I´m using C# and Visual Studio Community edition.

### Add a test

We start by creating a new test class:

{% highlight c# %}
using Machine.Specifications;

namespace RestaurantReview.Tests
{
    [Subject("")]
    public class Class1
    {
        Establish context = () => 
        {
            //Arrange part of the test
        };

        Because of = () => //Hit subject under test

        It  = () => //Assertions
    }
}
{% endhighlight %}

In the _Subject_ we describe the context, which can be the name of the subject under test (_sut_) or a string literal description. I don´t like strings in code so we can write the name of the class. First decision to make: What name do we choose for the class? Why don´t we postpone this? Let´s describe first what we want to test.

The class naming convention is to use _Sentence_snake_case_ and to start with the word _When_. So let's replace that _Class1_ with our test description. Do you like the following one? _When_computing_rating_for_one_review_

So we have some rating. Do we like _RestaurantRater_ as our sut? Let´s go for it. Bear in mind that at this stage we are designing, don´t spend now so much time thinking in the perfect name, this is an iterative process and we can rename things later.

We initialize a new _ReaturantRater_ in the _Establish_:

{% highlight c# %}
using Machine.Specifications;

namespace RestaurantReview.Tests
{
    [Subject("RestaurantRater")]
    public class When_computing_result_for_one_review
    {
        Establish context = () => 
        {
            _sut = new RestaurantRater();
        };

        Because of = () => //Hit subject under test

        It  = () => //Assertions

        static RestaurantRater _sut;
    }
}
{% endhighlight %}

It doesn´t compile. 

> Our goal is to get the simplest code that compiles once you are relatively happy with the names and the design. 

Let´s create the Rater. I´m running Resharper so I hit _Alt+Enter_ and it creates an empty class for us:

{% highlight c# %}
namespace RestaurantsReview
{
    public class RestaurantRater
    {
    }
}
{% endhighlight %}

Do we need to send something to the RestaurantRater? Yes, we need a bunch of RestaurantReviews (let´s assume we have already the definition of Restaurant and RestaurantReview done for previous Requirements). We can inject the list of restaurant reviews when creating the Rater or when calculating the Rate. Let´s do for now Constructor injection. For this first test let´s do the simplest scenario, one single review with one Rating:

{% highlight c# %}
using Machine.Specifications;

namespace RestaurantReview.Tests
{
    [Subject("RestaurantRater")]
    public class When_computing_result_for_one_review
    {
        Establish context = () =>         
        {
            var data = new Restaurant();
            data.Reviews = new List<RestaurantReview>();
            data.Reviews.Add(new RestaurantReview(){Rating = 4});
            _sut = new RestaurantRater(data);
        };

        Because of = () => //Hit subject under test

        It  = () => //Assertions
    }
}
{% endhighlight %}

Again, it doesn´t compile so we hit _Alt+Enter_ again and Resharper asks if we want to add a parameter in the constructor:

{% highlight c# %}
namespace RestaurantsReview
{
    public class RestaurantRater
    {
    	private readonly Restaurant _restaurant;

        public RestaurantRater(Restaurant restaurant)
        {
            _restaurant = restaurant;
        }
    }
}
{% endhighlight %}

We need to hit some logic in RestaurantRater to compute the rating, _Compute_? (Remember DRY principle, Don´t Repeat Yourself). Do we need something to compute it? Let´s take the simplest approach, let´s send the number of reviews to take into account. Time to write some code in the Because of MSpec:

{% highlight c# %}
using Machine.Specifications;

namespace RestaurantReview.Tests
{
    [Subject("RestaurantRater")]
    public class When_computing_result_for_one_review
    {
        Establish context = () =>         
        {
            var data = new Restaurant();
            data.Reviews = new List<RestaurantReview>();
            data.Reviews.Add(new RestaurantReview(){Rating = 4});
            _sut = new RestaurantRater(data);
        };

        Because of = () => _sut.Compute(10);

        It  = () => //Assertions
    }
}
{% endhighlight %}

Again it doesn´t compile. But we have Resharper:)

{% highlight c# %}
namespace RestaurantsReview
{
    public class RestaurantRater
    {
    	private readonly Restaurant _restaurant;

        public RestaurantRater(Restaurant restaurant)
        {
            _restaurant = restaurant;
        }

        public void Compute(int numberOfReviews)
        {
            throw new System.NotImplementedException();
        }
    }
}
{% endhighlight %}

Now I wonder, how would we test the result of Compute? In other words, we need to think what would do the compute.
Why don´t we return a _RatingResult_ definition?

So let´s go back to the test and expect a RatingResult with the Rating of that single review:

{% highlight c# %}
using Machine.Specifications;

namespace RestaurantReview.Tests
{
    [Subject("RestaurantRater")]
    public class When_computing_result_for_one_review
    {
        Establish context = () =>         
        {
            var data = new Restaurant();
            data.Reviews = new List<RestaurantReview>();
            data.Reviews.Add(new RestaurantReview(){Rating = 4});
            _sut = new RestaurantRater(data);
        };

        Because of = () => _result = _sut.Compute(10);

        private It set_restaurant_rating = () => _result.Rating.ShouldEqual(4);

        static RestaurantRater _sut;
        static RatingResult _result;
    }
}
{% endhighlight %}

It doesn´t compile and from the test we modify the returning type of Compute (guess who helps us?).

Now we have the test completely written and everything compiles.

Please notice how we have created a bunch of classes, RatingResult and RestaurantRater, just by trying to build a compiling test that satisfy the requirement. We have decided names and outcomes of the different classes from the tests as another consumer of the class. In addition, don´t you feel that this is possibly the best we could have written, much more than one written after the code?

### Run all tests and see if the new one fails.

Yes, it fails. We were focused on writing the minimum amount of code to make a compiling test. 

### Write some code.

Guess what we need to do to make it pass? Right, let´s return one RatingResult with Rating equals to 4:

{% highlight c# %}
namespace RestaurantsReview
{
    public class RestaurantRater
    {
    	private readonly Restaurant _restaurant;

        public RestaurantRater(Restaurant restaurant)
        {
            _restaurant = restaurant;
        }

        public RatingResult Compute(int numberOfReviews)
        {
            return new RatingResult(){Rating = 4};
        }
    }
}
{% endhighlight %}

### Run tests 

Great it passes!

But wait a moment, this way of passing a test is completely ridiculous.

Ok... However, this is one of the keys of doing TDD: do the simplest possible thing to make a test pass, and then you just keep adding tests that will test more conditions. 
We're going to change the implementation of Compute, that for sure, but along the way, as we make these changes, we are going to write tests for it, and they're going to make sure that we're making the right changes, and that as we're adding things and adding features and reconstructing the code, we're not breaking anything. 

### Refactor code

If we take a look at what we have so far, I´m quite happy. I like the naming and the classes involved. It is too early to do any other refactoring. Let´s move on to more scenarios.

### Repeat 

Now we have a running test, let´s get into speed and add more test cases.

#### Several reviews for a Restaurant:

{% highlight c# %}
using System.Collections.Generic;
using Machine.Specifications;
using Reviews;

namespace RestaurantReview.Tests
{
    [Subject("RestaurantRater")]
    public class When_computing_result_for_two_reviews
    {
        Establish context = () =>
        {
            var data = new Restaurant();
            data.Reviews = new List<Reviews.RestaurantReview>();
            data.Reviews.Add(new Reviews.RestaurantReview() { Rating = 4 });
            data.Reviews.Add(new Reviews.RestaurantReview() { Rating = 6 });
            _sut = new RestaurantRater(data);
        };

        Because of = () => _result = _sut.Compute(10);

        It sets_average_rating = () => _result.Rating.ShouldEqual(5);

        static RestaurantRater _sut;
        static RatingResult _result;
    }
}
{% endhighlight %}

Now we have a restaurant with two reviews. It fails. Let´s write some code to make it pass. The simplest possible code I can imagine is the following:

{% highlight c# %}
namespace RestaurantsReview
{
    public class RestaurantRater
    {
    	private readonly Restaurant _restaurant;

        public RestaurantRater(Restaurant restaurant)
        {
            _restaurant = restaurant;
        }

        public RatingResult Compute(int numberOfReviews)
        {
        	var ratingResult = new RatingResult();
            ratingResult.Rating = (int) _restaurant.Reviews.Average(r => r.Rating);
            return ratingResult;
        }
    }
}
{% endhighlight %}

Now both of the tests pass, it´s time to refactor. The logic is quite simple yet, but we see some duplication in the tests´ Establish. 

Something really key in TDD is that Tests are as important as the logic. They need to be in the best of the shapes, we don´t want to test to be another maintainance nightmare. Code standards apply to tests as well. 

In order to avoid dupplication in our Establish, we can create a new method that creates a restaurant and use it in the two tests. Because of the way MSpec works, with one class for every test scenario, we need to create a base class for the two tests. What do you think about the following solution:

{% highlight c# %}
using System.Linq;
using Machine.Specifications;
using Reviews;

namespace RestaurantReview.Tests
{
    [Subject("RestaurantRater")]
    public class When_computing_result_for_one_review : TestContext
    {
        Establish context = () => 
        {
            var restaurant = CreateRestaurant(ratings: 4);
            _sut = new RestaurantRater(restaurant);
        };

        Because of = () => _result = _sut.Compute(10);

        It set_restaurant_rating = () => _result.Rating.ShouldEqual(4);

        static RestaurantRater _sut;
        static RatingResult _result;
    }

    [Subject("RestaurantRater")]
    public class When_computing_result_for_two_reviews : TestContext
    {
        Establish context = () =>
        {
            var restaurant = CreateRestaurant(ratings: new []{4, 6});
            _sut = new RestaurantRater(restaurant);
        };

        Because of = () => _result = _sut.Compute(10);

        It sets_average_rating = () => _result.Rating.ShouldEqual(5);

        static RestaurantRater _sut;
        static RatingResult _result;
    }

    public class TestContext
    {
        protected static Restaurant CreateRestaurant(params int[] ratings)
        {
            var restaurant = new Restaurant();
            restaurant.Reviews = ratings.Select(r => new Reviews.RestaurantReview { Rating = r }).ToList();
            return restaurant;
        }
    }
}
{% endhighlight %}

Now we can easily create one restaurant with any number of ratings. This is a helper method that can be used in any other test. The linq operation takes the list of ratings and transforms it into a list of reviews
Every test still passes and we can follow with the next case.

(I´m using by the way something called [NCrunch](http://javicaria.github.io/NCrunch/) the ultimate TDD Tool so I don´t have to run any test manually. Do you think it is cheating? I don´t think so, I just try to work faster.)

#### Taking the last N number of reviews: 

So far we haven´t considered the N number of reviews in the code. Let´s write some tests for it, we can pass a restaurant with 5 reviews and compute just 3:

{% highlight c# %}
[Subject("RestaurantRater")]
public class When_computing_result_for_restaurant_with_five_reviews_considering_last_three_reviews : TestContext
{
	Establish context = () =>
    {
        var restaurant = CreateRestaurant(ratings: new[] { 4, 6, 5, 1, 1 });
        _sut = new RestaurantRater(restaurant);
    };

    Because of = () => _result = _sut.Compute(3);

    It sets_average_rating_of_last_three_reviews = () => _result.Rating.ShouldEqual(5);

    static RestaurantRater _sut;
    static RatingResult _result;
}
{% endhighlight %}

NCrunch is showing a red dot telling that the test doesn´t pass, that´s a good sign, let´s make it pass.

{% highlight c# %}
using System.Linq;

namespace Reviews
{
    public class RestaurantRater
    {
        private readonly Restaurant _restaurant;

        public RestaurantRater(Restaurant restaurant)
        {
            _restaurant = restaurant;
        }

        public RatingResult Compute(int numberOfReviews)
        {
            var ratingResult = new RatingResult();
            ratingResult.Rating = (int)_restaurant.Reviews.Take(numberOfReviews).Average(r => r.Rating);
            return ratingResult;
        }
    }
}
{% endhighlight %}

We just take the N number of reviews and we have another test passing.

For the next step, we would start testing boundary conditions: What happens when we rate a restaurant with zero reviews, or if we rate a restaurant that has a negative review? Should we throw exceptions? How do we deal with odd numbers? 
To keep this post simpler, let´s assume every of these is done.
Let´s take the next way of calculating the rating of a restaurant and how it impacts the design.

#### Implementing the weighted mean rate:

Let´s write a test first for this case:

{% highlight c# %}
using System.Linq;
using Machine.Specifications;
using Reviews;

namespace RestaurantReview.Tests
{
    [Subject("RestaurantRater")]
    public class When_computing_weighted_average_for_two_reviews : TestContext
    {
        Establish context = () =>
        {
            var restaurant = CreateRestaurant(ratings: new[] { 3, 9 });
            _sut = new RestaurantRater(restaurant);
        };

        Because of = () => _result = _sut.ComputeWeightedAverage(3);

        It sets_rating_with_recent_review_weighted_twice_heavier = () => _result.Rating.ShouldEqual(4);

        static RestaurantRater _sut;
        static RatingResult _result;
    }
}
{% endhighlight %}

We have added a new ComputeWeightedAverage into the RestaurantRater. We have to write the recent review twice as heavily as the previous review. In that scenario we have 3 and 9 as reviews ratings but we expect 4 because the 3 is more heavily weighted. Let´s write a dirty implementation to satisfy this requirement:

{% highlight c# %}
public RatingResult ComputeWeightedAverage(int numberOfReviews)
{
    var result = new RatingResult();
    var restaurantReviews = _restaurant.Reviews.ToArray();
    int counter = 0;
    int total = 0;

    for (int i = 0; i < restaurantReviews.Count(); i++)
    {
	if (i < restaurantReviews.Count() / 2)
        {
            counter += 2;
            total += restaurantReviews[i].Rating;
        }
        else
        {
            counter += 1;
            total += restaurantReviews[i].Rating;
        }
    }

    result.Rating = total / counter;
    return result;
}
{% endhighlight %}

Now the test pass but we have a lot cases to take into account, for example what happens when the number of reviews is even? How do we truncate? This is the typical case where we will go back to the business and ask so many clarifications, then go back and write a test case for it.

For this post let´s concentrate on high level design. 

One thing that really bothers me is how the RestaurantRater is responsible for computing a simple rating and a weighted rating. In addition we know this is an area really proned to change because we already know that the business will likely want to apply new approaches, they will add new algorithms and change existing algorithms. If we need to go back to the RestaurantRater and change the algorithms inside there or add new ones, then things will be really difficult to manage. We need a way to have easy-to-change algorithms while making the tests still to pass. This is the typical scenario to apply the _Strategy Pattern_. The basic idea is to have a common interface for the algorithm and having multiple implementations of the algorithm.

We start by doing some refactoring from the tests. Let´s inject an interface _IRatingAlgorithm_ into the _RestaurantRater_ Compute method that will take care of the computations. From the _RestaurantRater_ perspective we don´t care about the concrete implementations of the Algorithm, all we need is any Algorithm. 
This is the ideal scenario to use the so called Mock objects. The benefit of using a mock is that if the interface adds new members, we don´t need to modify the mock object.
I use RhinoMocks for that. 

{% highlight c# %}
[Subject("RestaurantRater")]
    public class When_computing_result_for_one_review : TestContext
    {
        Establish context = () => 
        {
            var restaurant = CreateRestaurant(ratings: 4);
            _sut = new RestaurantRater(restaurant);

            _averageRatingAlgorithm = MockRepository.GenerateMock<IRatingAlgorithm>();
        };

        Because of = () => _result = _sut.Compute(_averageRatingAlgorithm, 10);

        It set_restaurant_rating = () => _result.Rating.ShouldEqual(4);

        static RestaurantRater _sut;
        static RatingResult _result;
        static IRatingAlgorithm _averageRatingAlgorithm;
    }
    {% endhighlight %}

The interface doesn´t exist, we use Resharper to generate it.
We need to mock the method that will be hitten in the algorithm, we can choose the name from the test:

{% highlight c# %}
[Subject("RestaurantRater")]
    public class When_computing_result_for_one_review : TestContext
    {
        Establish context = () => 
        {
            var restaurant = CreateRestaurant(ratings: 4);
            _sut = new RestaurantRater(restaurant);

            _averageRatingAlgorithm = MockRepository.GenerateMock<IRatingAlgorithm>();
            _averageRatingAlgorithm.Stub(a => a.Compute(restaurant.Reviews)).Return(new RatingResult() {Rating = 4});
        };

        Because of = () => _result = _sut.Compute(_averageRatingAlgorithm, 10);

        It set_restaurant_rating = () => _result.Rating.ShouldEqual(4);

        static RestaurantRater _sut;
        static RatingResult _result;
        static IRatingAlgorithm _averageRatingAlgorithm;
    }
    {% endhighlight %}

See how we inject the mock object into the Compute method and still do the same assertion. To make it pass we need to let the algorithm do compute and assign the rating:

{% highlight c# %}
using System.Linq;

namespace Reviews
{
    public class RestaurantRater
    {
        private readonly Restaurant _restaurant;

        public RestaurantRater(Restaurant restaurant)
        {
            _restaurant = restaurant;
        }

        public RatingResult Compute(IRatingAlgorithm algorithm, int numberOfReviews)
        {
            return algorithm.Compute(_restaurant.Reviews.ToList());
        }
    }
}
{% endhighlight %}

Great it passes. Let´s arrange the tests to make them pass... 

> Wait! Can I touch existing tests? But of course, in general, if a test is not relevant after a refactor or the functionality has changed, we can modify tests. We can even delete tests, but treat this case carefully (deleting tests is not fixing breaking tests).

{% highlight c# %}
using System.Linq;
using Machine.Specifications;
using Reviews;
using Rhino.Mocks;

namespace RestaurantReview.Tests
{
    [Subject("RestaurantRater")]
    public class When_computing_result_for_one_review : TestContext
    {
        Establish context = () => 
        {
            var restaurant = CreateRestaurant(ratings: 4);
            _sut = new RestaurantRater(restaurant);

            _averageRatingAlgorithm = MockRepository.GenerateMock<IRatingAlgorithm>();
            _averageRatingAlgorithm.Stub(a => a.Compute(restaurant.Reviews)).Return(new RatingResult() {Rating = 4});
        };

        Because of = () => _result = _sut.Compute(_averageRatingAlgorithm, 10);

        It set_restaurant_rating = () => _result.Rating.ShouldEqual(4);

        static RestaurantRater _sut;
        static RatingResult _result;
        static IRatingAlgorithm _averageRatingAlgorithm;
    }

    [Subject("RestaurantRater")]
    public class When_computing_result_for_two_reviews : TestContext
    {
        Establish context = () =>
        {
            var restaurant = CreateRestaurant(ratings: new []{4, 6});
            _sut = new RestaurantRater(restaurant);

            _averageRatingAlgorithm = MockRepository.GenerateMock<IRatingAlgorithm>();
            _averageRatingAlgorithm.Stub(a => a.Compute(restaurant.Reviews)).Return(new RatingResult() { Rating = 5 });
        };

        Because of = () => _result = _sut.Compute(_averageRatingAlgorithm, 10);

        It sets_average_rating = () => _result.Rating.ShouldEqual(5);

        static RestaurantRater _sut;
        static RatingResult _result;
        static IRatingAlgorithm _averageRatingAlgorithm;
    }

    [Subject("RestaurantRater")]
    public class When_computing_weighted_average_for_two_reviews : TestContext
    {
        Establish context = () =>
        {
            var restaurant = CreateRestaurant(ratings: new[] { 3, 9 });
            _sut = new RestaurantRater(restaurant);

            _weightedRatingAlgorithm = MockRepository.GenerateMock<IRatingAlgorithm>();
            _weightedRatingAlgorithm.Stub(a => a.Compute(restaurant.Reviews)).Return(new RatingResult() { Rating = 4 });
        };

        Because of = () => _result = _sut.Compute(_weightedRatingAlgorithm, 3);

        It sets_weighted_rating = () => _result.Rating.ShouldEqual(4);

        static RestaurantRater _sut;
        static RatingResult _result;
        static IRatingAlgorithm _weightedRatingAlgorithm;
    }

    [Subject("RestaurantRater")]
    public class When_computing_result_for_restaurant_with_five_reviews_considering_last_three_reviews : TestContext
    {
        Establish context = () =>
        {
            var restaurant = CreateRestaurant(ratings: new[] { 4, 6, 5, 1, 1 });
            _sut = new RestaurantRater(restaurant);

            _algorithm = MockRepository.GenerateMock<IRatingAlgorithm>();
            _algorithm.Stub(a => a.Compute(restaurant.Reviews)).Return(new RatingResult() { Rating = 5 });
        };

        Because of = () => _result = _sut.Compute(_algorithm, 3);

        It sets_average_rating_of_last_three_reviews = () => _result.Rating.ShouldEqual(5);

        static RestaurantRater _sut;
        static RatingResult _result;
        static IRatingAlgorithm _algorithm;
    }

    public class TestContext
    {
        protected static Restaurant CreateRestaurant(params int[] ratings)
        {
            var restaurant = new Restaurant();
            restaurant.Reviews = ratings.Select(r => new Reviews.RestaurantReview { Rating = r }).ToList();
            return restaurant;
        }
    }
}
{% endhighlight %}

I don´t like two things of the current tests:

1. We have redundant tests: From the RestaurantRater perspective we don´t care anymore about the specific rating algorithm used. Therefore we don´t need specific test cases for the different algorithms in this context.

2. The test that checks that only the N first reviews are considered still passes. If we look RestaurantRater we aren´t doing that. This is because we are mocking the entire rating calculations in the test.

In order to fix the first issue we can delete some tests of RestaurantRater but I don´t want to loose those checks: these checks are correct from the business perspective. Instead, what we can do is move them to a new test class to check the specific algorithm. 

Mmmm... We don´t even have concrete implementations of the algorithm, all we have is an interface and mock objects! Damn it:)

We create a couple of test classes, one for every type of rating computation. We cut and paste those redundant tests so I don´t loose those business requirements. These are the tests for the Simple algorithm:

{% highlight c# %}
using System.Collections.Generic;
using System.Linq;
using Machine.Specifications;
using Reviews;
using Rhino.Mocks;

namespace RestaurantReview.Tests
{
    [Subject("SimpleRatingAlgorithm")]
    public class When_computing_result_for_one_review : RatingAlgorithmTestContext
    {
        Establish context = () => 
        {
            _reviews = CreateReviews(ratings: 4);

            _sut = new SimpleRatingAlgorithm();
        };

        Because of = () => _result = _sut.Compute(_reviews);

        It set_restaurant_rating = () => _result.Rating.ShouldEqual(4);

        static RatingResult _result;
        static IRatingAlgorithm _sut;
        static ICollection<Reviews.RestaurantReview> _reviews;
    }

    [Subject("SimpleRatingAlgorithm")]
    public class When_computing_result_for_two_reviews : RatingAlgorithmTestContext
    {
        Establish context = () =>
        {
            _reviews = CreateReviews(ratings: new[] { 4, 6 });

            _sut = new SimpleRatingAlgorithm();
        };

        Because of = () => _result = _sut.Compute(_reviews);

        It sets_average_rating = () => _result.Rating.ShouldEqual(5);

        static RatingResult _result;
        static IRatingAlgorithm _sut;
        static ICollection<Reviews.RestaurantReview> _reviews;
    }    

    public class RatingAlgorithmTestContext
    {
        protected static ICollection<Reviews.RestaurantReview> CreateReviews(params int[] ratings)
        {
            return ratings.Select(r => new Reviews.RestaurantReview { Rating = r }).ToList();
        }
    }
}
{% endhighlight %}

SimpleRatingAlgorithm doesn´t exist yet, so let´s create that from the tests. We can place the logic that we wrote in the previous implementation:

{% highlight c# %}
using System.Collections.Generic;
using System.Linq;
using Reviews;

namespace RestaurantsReview
{
    public class SimpleRatingAlgorithm : IRatingAlgorithm
    {
        public RatingResult Compute(ICollection<RestaurantReview> reviews)
        {
            var ratingResult = new RatingResult();
            ratingResult.Rating = (int)reviews.Average(r => r.Rating);
            return ratingResult;
        }
    }
}
{% endhighlight %}

We do the same for the Weighted rating, arrange tests in a test class and move out the logic into its own algorithm:

{% highlight c# %}
using System.Collections.Generic;
using Machine.Specifications;
using RestaurantsReview;
using Reviews;

namespace RestaurantReview.Tests
{
    [Subject("WeightedAverageRating")]
    public class When_computing_weighted_average_for_two_reviews : RatingAlgorithmTestContext
    {
        Establish context = () =>
        {
            _reviews = CreateReviews(ratings: new[] { 3, 9 });
            _sut = new WeightedAverageRating();
        };

        Because of = () => _result = _sut.Compute(_reviews);

        It sets_weighted_rating = () => _result.Rating.ShouldEqual(4);

        static RatingResult _result;
        static IRatingAlgorithm _sut;
        private static ICollection<Reviews.RestaurantReview> _reviews;
    }
}
{% endhighlight %}

{% highlight c# %}
using System.Collections.Generic;
using System.Linq;
using Reviews;

namespace RestaurantsReview
{
    public class WeightedAverageRating : IRatingAlgorithm
    {
        public RatingResult Compute(ICollection<RestaurantReview> reviews)
        {
            var result = new RatingResult();
            var restaurantReviews = reviews.ToArray();
            int counter = 0;
            int total = 0;

            for (int i = 0; i < restaurantReviews.Count(); i++)
            {
                if (i < restaurantReviews.Count() / 2)
                {
                    counter += 2;
                    total += restaurantReviews[i].Rating;
                }
                else
                {
                    counter += 1;
                    total += restaurantReviews[i].Rating;
                }
            }

            result.Rating = total / counter;
            return result;
        }
    }
}
{% endhighlight %}

## _Mocks, why would I do that?_

A mock object is an object programmed with expectations about how it will communicate with other components. Those expectations can vary a lot, and there are a lot of assertions we can make. In other words, mocks can help designing the communication between classes.

We have one example in our Restaurant Reviews application...

We have taken a decision: the first N reviews to consider will be filtered by RestaurantRater for now, the algorithm will do its calculation with the reviews it gets (separation of concerns). 

But that was the test that we knew wasn´t really doing the check. To check that properly one of the ways is by mocking the algorithm and asserting on the reviews it gets. Let´s try to do that:

{% highlight c# %}
[Subject("RestaurantRater")]
    public class When_computing_result_for_restaurant_with_five_reviews_considering_last_three_reviews : RestaurantRaterTestContext
    {
        Establish context = () =>
        {
            var restaurant = CreateRestaurant(ratings: new[] { 4, 6, 5, 1, 1 });

            _algorithm = MockRepository.GenerateMock<IRatingAlgorithm>();
            _algorithm.Stub(a => a.Compute(restaurant.Reviews)).Return(new RatingResult() { Rating = 5 });

            _sut = new RestaurantRater(restaurant);
        };

        Because of = () => _sut.Compute(_algorithm, 3);

        It sets_average_rating_of_last_three_reviews = () =>
        {
            var relevantReviews = _algorithm.GetArgumentsForCallsMadeOn(a => a.Compute(null), mo => mo.IgnoreArguments())[0][0]
                as ICollection<Reviews.RestaurantReview>;
            relevantReviews.Count().ShouldEqual(3);
        };

        static RestaurantRater _sut;
        static IRatingAlgorithm _algorithm;
    }
{% endhighlight %}

Please notice how our It part is bigger. This test is a bit harder to read but I feel much more confident with it. We get the reviews sent over the algorithm (by using RhinoMocks, GetArgumentsForCallsMadeOn) and count the number of it. It should match the N parameter (3 in this case) configured in the RestaurantRater.Compute.
This is why we should use Mocks carefully, it makes test less readable.

Now we have a failing test, let´s try to fix that:

{% highlight c# %}
using System.Linq;

namespace Reviews
{
    public class RestaurantRater
    {
        private readonly Restaurant _restaurant;

        public RestaurantRater(Restaurant restaurant)
        {
            _restaurant = restaurant;
        }

        public RatingResult Compute(IRatingAlgorithm algorithm, int numberOfReviews)
        {
            return algorithm.Compute(_restaurant.Reviews.Take(numberOfReviews).ToList());
        }
    }
}
{% endhighlight %}

Voilà! Job done. Passing tests.

I hope with this example you´ve seen how we´ve done TDD from scratch and how while trying to make test pass and refactoring, all of the structure evolved including the usage of the Strategy pattern.

You may would have ended with the same production code without TDD, you may not. But, can you imagine doing UnitTests for that code after having written it? With TDD it was much more fun.

## _Right, it works! But it was a simple example, I can´t do that for complex systems. Furthermore what happens when we are a bunch developers creating something new? Do we start on our own from scratch with TDD?_

TDD makes even more sense for complex systems, where we get a big requirement, difficult to manage, with lots of complexity. That´s where Agile methodologies started, to break that big project into smaller requirements, we don´t plan the whole thing, we pick piece by piece in different sprints and get feedback after every little iteration.

TDD takes the same approach at the user story level. We will break that user story into smaller chunks and we will iterate through them. It will allow us to implement the minimum code for that small step and refactor to make a bigger structure as we add more functionality.
When the complexity of a system is big and we even don´t know how to start solving the problem, we can just use the same approach we use for the Restaurant Rater.

If you are playing with a new technology or you aren´t sure if the approach you are taking will be feasible, you can do a quick prototype without TDD and when you see it is Ok, then you can delete the prototype and follow that direction with TDD. 
In those cases, sometimes I do TDD as well. The idea is that we want to use "Visual Studio" like we use a whiteboard. Without TDD someone would give me a software requirement, and I'd go to the whiteboard and start trying to diagram out some classes and methods that I think I would need. I'm going to do that in "Visual Studio" instead because "Visual Studio" gives me a compiler, and I can use tests to validate that the code works. Since this is a prototype phase and I'm not sure what direction I'm going just yet, I would focus in the functionality for my prototype.

One can argue that TDD works well when there is a single person working in a single requirement, but what happens when there are several people on that? Some people like to start with a prototype in that case. Then everybody is aware of the direction to follow, but we do TDD from there.

TDD works really well with pair programming. One programmer can drive to make a failing test and make it pass, then swap the driver.

Read more about different approaches to use TDD that we followed in something called [Global Day of Code Retreat](http://javicaria.github.io/Global-Day-of-Coderetreat/).

## _All I work on is bugs and legacy code, I need to fix a bug, how can I do TDD?_

It´s true, TDD is test driven development, so that legacy code wasn´t developed with that philosophy in mind. Most of the times, Legacy code doesn´t even have unit tests around it, and sometimes the quality of that legacy code is not even right (remember those 4000-lines-long methods?). Legacy code is not easy to test.

To sum up:

> We can´t use TDD to unit test Legacy code.

In that case all we can do is Unit Testing. One of the approaches that _Michael Feathers_ recommends in his book _Working Effectively with Legacy code_ when he needs to touch some legacy code, before any other thing, he writes a bunch of unit tests around it. Then we can safely apply refactoring techniques, making it easier and safer to modify. This is a great book if you work mainly doing hot fixes for existing code.

The same applies when we have to add a little bit of functionality to the legacy code: we can loosely couple the new code and do TDD for our new feature. Mockings and Stubs can help a lot with that.

The same happens when we write the code first and then we need to add unit tests for it, sometimes it´s really hard to do those tests for the piece of code we´ve just written. I´m sure it wasn´t easy and you sometimes you ended up not writing those tests because it was nearly impossible. That´s not pretty but no worries, I´ve done the same. With TDD you don´t have the same problem.

If you don´t even have even a testing framework when you start a new job, go and search for it. Are you a Cobol developer and you start a new project without unit tests? Go and search for "Unit test cobol" in Google, I bet there is something out there for you.

## _This TDD is outdated, now I need to use BDD, isn´t it?_

To TDD or BDD? That is the question.

For me the answer is quite straight, it is the same thing with different names.
BDD arose apparently because unit tests written in TDD style didn´t describe the user´s desired behavior correctly, it was quite too technical.
I have never seen such a problem, I try to do TDD describing behavior in the unit tests.
Here you have a naming conversion between TDD and BDD:

Test Driven Development        ->    Behavioral Driven Development

Arrange ->                            Given

Act       ->                         When

Assert      ->                       Then

So basically they are the same thing with different names. In our example we followed a more BDD approach by using MSpec tests. Let me show you how those tests are translated using MSTests, with a more pure TDD approach, and you can compare the two of them.

Microsoft Tests:

{% highlight c# %}
        [TestMethod]
        public void Computes_result_for_two_reviews()
        {
            var data = new Restaurant();
            data.Reviews = new List<RestaurantReview>();
            data.Reviews.Add(new RestaurantReview() { Rating = 4 });
            data.Reviews.Add(new RestaurantReview() { Rating = 8 });

            var rater = new RestaurantRater(data);
            var result = rater.ComputeRating(10);

            Assert.AreEqual(6, result.Rating);
        }
{% endhighlight %}

Machine Specification tests:
{% highlight c# %}
[Subject("RestaurantRater")]
    public class When_computing_result_for_two_reviews
    {
        Establish context = () =>
        {
            var data = new Restaurant();
            data.Reviews = new List<Reviews.RestaurantReview>();
            data.Reviews.Add(new Reviews.RestaurantReview() { Rating = 4 });
            data.Reviews.Add(new Reviews.RestaurantReview() { Rating = 6 });
            _sut = new RestaurantRater(data);
        };

        Because of = () => _result = _sut.Compute(10);

        It sets_average_rating = () => _result.Rating.ShouldEqual(5);

        static RestaurantRater _sut;
        static RatingResult _result;
    }
{% endhighlight %}

Isn´t it the same?

## Conclusion

I think that was more than enough for now about TDD. I´m always trying to convince people to use TDD because it´s one of those methodologies that really changed my way of programming. Like for you, it was really hard to learn in the beginning, but stick with me on this: the more you practice it, the more you see the benefit.

All I explained here was my way of doing things and how I face the different challenges, but that´s just mine. I´m looking forward to do some pair programming with you and learn your way. I´m pretty sure that in some years I´ll be questioning those things I´ve explained here. But that´s change, and change is life. We are agile so we don´t have any problem embracing the change.

Keep coding, keep doing TDD!
