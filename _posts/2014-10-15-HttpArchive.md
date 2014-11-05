---
layout: post
title: Performance of web sites using HTTP Archive
---

Hei igjen, Hi again, Hola de nuevo!

Yesterday I went to a [workshop](http://www.meetup.com/London-Web-Performance-Group/events/209433702/) carried out by the _London Web Performance Group_, *The Good, the Bad and the Ugly of the HTTP Archive*.
I was really surprised with the content of the talk, in addition pizza and beer was provided at the end and a position to work in Google was announced there. They even did a lottery with free tickets for the next _Velocity Conference_ in USA.

This same talk was held in the _Velocity Conference_ in New York this year.
The speakers were Robin Osborne [@rposbo](https://twitter.com/rposbo) and Dean Hume [@deanohume](https://twitter.com/DeanoHume), two developers that share a common interest in Web performance.

## So what is HTTP Archive?

The [HTTP Archive](http://httparchive.org/) is a vast data store of web sites, it collects the web content, how it is served and constructed.

![Image description](/images/httpArchive/httparchive.png)

It runs once every month and it collects a lot of information about performance, including _load time_, _page size_, _http requests_ and much more.
The information is stored in _MySQL_ and can be downloaded. The problem is the size of the file, some hundred terabytes.
But wait a minute, we have [Google Bigquery](https://cloud.google.com/bigquery/) that provides an endpoint to query this information.
_Bigquery_ makes it really easy to query big data. We can run SQL queries really fast.

In addition, we have [bigqueri.es](bigqueri.es), comunity of people sharing queries to be run for HTTP Archive.
They provide the sample sql query together with some results and discussion with other members. One can also post his own query and ask questions.

### Checking performance of web sites using the HttpArchive

So Robin and Dean created a couple of benchmarking queries. They aimed not only to get the best and worst web site, but more importantly, check what they were doing good or bad in terms of performance. Yesterday they shared their main conclusion.

They explained how they measured it:

- They took out the main 100 websites. This is because big companies have dedicated teams for performance, they wanted to know what they can in their daily basis as regular developers.
- Measurements: Fully Loaded time, Page downloading size, page speed (0-100), speed index (how quicly different check points in the page loading takes).
- If sites were usable and modern, if they follow best practices, and bonus if the site was responsive.

## The Good (_Or how web pages went better_)

### Filament group: 

Very good performant and responsive page, and they blog about how they do it.
They scored 100.
His trick is *reducing the Critical Path*, this means getting the main content in the beginning, when the site is loading.
In addition they have made several tools available in github: _Grunt-CriticalCSS_, _LoadCSS_, _LoadJS_.

### Nature.com:

They score 86.
They *stick to basis*, [14 rules for faster-loading web sites](http://stevesouders.com/hpws/rules.php)
*Monitor* is key for them: They monitor every new feature they release. They use: _ShowSlow.com, statsD and graphite_.

### Zomato.com:

88
Their points are:
- *Start small*: A blank page is always going to be the fastest, start from there. Add only what is needed.
- *Caching*: Heavy use of caching, makes sense since they are a search provider. HTTP caching.

### Envato marketplaces:

85
*Stick to basics* and aim for low hanging fruit.
They think about performance from the earliest design stage.
If users add their own images you need a performance strategy. Consider user content generated.

## The bad

In this case they didn´t ask directly to the web sites, to avoid dissapointing them.
Instead, Robin and Dean asked themselves, _if we could do one single thing to improve these sites´ performance, what would it be?_

### Welovefashion.it:

In this case lots of data is downloaded to the client when page loads:
Simple trick, enable compression.

### Gu-japan.com

15.5 MB images are loaded on start-up.
717 http requests
And this is because a carousel of images is loaded in the main page.
One simple trick: Remove carousels, it has been studied that 1% users care about carousel.
If you don´t believe check [ShouldIUseACarousel.com](ShouldIUseACarousel.com)

### Gamepedia.com

They have user's generated content, such images that aren´t scaled. Trick: serve scaled images

## The Ugly

_Can it go even worse?_

### Sailboatlistings.com
They score 44.
The reason is that the build 15000 dom elements in the beginning, because of an everlasting scrolling in the home page.

### colorsbycherry.com

Scored 14!!
It takes 1 min loading completely the home page (because of lots of images).

### CallOfDutty

It´s a great page, with reasonable measures, but... they scored 11 out of 100.
The reason is because they have a video looping in the background, is not streaming, it downloads the entire video, again and again.

## Performance in the build process

One great part of the talk. Robin and Dean explained that performance can be measured and automated in the build process.
We can use [PSI](https://github.com/addyosmani/psi/), that measures performance, automate it into the build, so it fails if speed is lower than some thresshold.

The problem with PSI is that it needs to expose a public url, and that means that functionality needs to be released. So what happens if we want to automate our own main current branch:
We can use [NGROK](https://ngrok.com/), which creates a secure tunnel to localhost, and then PSI can run using it.

So that´s it for today.

Keep coding and being performant!