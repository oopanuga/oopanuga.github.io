The Good, the Bad and the Ugly of the HTTP Archive: Performance of web sites using HTTP Archive

Hi again!

Yesterday I went to a workshop carried out by the London Web Performance Group, The Good, the Bad and the Ugly of the HTTP Archive.
I was really surprised with the content of the talk, in addition pizza and beer was provided at the end of the talk. Furthermore, a position to work in Google was announced there and they even have a lottery with free tickets for the next Velocity conference in USA.

http://www.meetup.com/London-Web-Performance-Group/events/209433702/

This same talk was held in Velocity Conference in New York this year.
The speakers were Robin Osborne @rposbo and Dean Hume @deanohume, two developers that share a common interest in Web performance.

So what is HTTP Archive?

The HTTP Archive is a vast data store of web sites (http://httparchive.org/), it collects the web content, how it is served and constructed.


It runs once every month and it collects a lot of information about performance, including load time, page size, http requests and much more.
The information is stored in MySQL and can be downloaded. The problem is the size of the file, some hundred terabytes.
But wait a minute, we have google bigquery that provides an endpoint to query this information:
https://cloud.google.com/bigquery/
Bigquery makes it really easy to query big data. We can run SQL queries really fust.

In addition, we have bigqueri.es, comunity of people sharing queries to be run for HTTP Archive.
They provide the sample sql query together with some results and discussion with other members. One can also post his own query and ask questions.

Checking performance of web sites

So Robin and Dean created a couple of benchmarking queries. They aim not only to get the best and worst web site, but more importantly, check what they were doing good or bad in terms of performance. Yesterday they shared their main conclusion.

They explained how they measured it:
-They took out the main 100 websites. This is because big companies have dedicated teams for performance, they wanted to know what they can in their daily basis as regular developers.
-Measurements: Fully Loaded time, Page downloading size, page speed (0-100), speed index (how quicly different check points in the page loading takes).
-If sites were usable and modern, if they follow best practices, and bonus if the site was responsive.
The Good:

Or how web pages went better...

Filament group: 

Very good performant and responsive page, and they blog about how they do it.
They scored 100.
His trick is reducing the critical path, this means getting the main content in the beginning, when the site is loading.
In addition they have made several tools available in github: Grunt-CriticalCSS, LoadCSS, LoadJS

Nature.com:

They score 86.
They stick to basis, 14 rules for faster-loading web sites: http://stevesouders.com/hpws/rules.php
Monitor is key for them: They monitor every new feature they release. They use: ShowSlow.com, statsD and graphite.

Zomato.com:

88
Their point is:
-Start small: A blank page is always going to be the fastest, start from there. Add only what is needed.
-Caching: Heavy use of caching, makes sense since they are a search provider. HTTP caching.

Envato marketplaces:

85
Stick to basics and aim for low hanging fruit.
They think about performance from the earliest design stage.
If users add their own images you need a performance strategy.
Consider user content generated.
