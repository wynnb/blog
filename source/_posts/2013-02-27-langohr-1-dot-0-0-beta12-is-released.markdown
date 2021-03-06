---
layout: post
title: "Langohr 1.0.0-beta12 is released"
date: 2013-02-27 16:37
comments: false
categories:
  - releases
  - langohr
---

## TL;DR

Langohr is a [Clojure RabbitMQ client](http://clojurerabbitmq.info) that embraces [AMQP 0.9.1 Model](http://www.rabbitmq.com/tutorials/amqp-concepts.html).

`1.0.0-beta12` is a development milestone release. We recommend all users to upgrade to it.


## Changes between Langohr 1.0.0-beta11 and 1.0.0-beta12

### Clojure-friendly Return Values

Previously functions such as `langohr.queue/declare` returned the underlying
RabbitMQ Java client responses. In case a piece of information from the
response was needed (e.g. to get the queue name that was generated by
RabbitMQ), the only way to obtain it was via the Java interop.

This means developers had to learn about how the Java client works.
Such responses are also needlessly unconvenient when inspecting them
in the REPL.

Langohr `1.0.0-beta12` makes this much better by returning a data structure
that behaves like a regular immutable Clojure map but also provides the same
Java interoperability methods for backwards compatibility.

For example, `langohr.queue/declare` now returns a value that is a map
but also provides the same `.getQueue` method you previously had to use.

Since the responses implement all the Clojure map interfaces, it is possible to use
destructuring on them:

``` clojure
(require '[langohr.core  :as lhc])
(require '[langohr.queue :as lhq])

(let [conn    (lhc/connect)
      channel (lhc/create-channel conn)
      {:keys [queue] :as declare-ok} (lhq/declare channel "" :exclusive true)]
  (println "Response: " declare-ok)
  (println (format "Declared a queue named %s" queue)))
```

will output

```
Response:  {:queue amq.gen-G9bmz19UjHLBjyxhanOG3Q, :consumer-count 0, :message_count 0, :consumer_count 0, :message-count 0}
Declared a queue named amq.gen-G9bmz19UjHLBjyxhanOG3Q
```

### langohr.confirm/add-listener Now Returns Channel

`langohr.confirm/add-listener` now returns the channel instead of the listener. This way
it is more useful with the threading macro (`->`) that threads channels (a much more
common use case).

### langohr.exchange/unbind

`langohr.exchage/unbind` was missing in earlier releases and now added.

### langohr.core/closed?

`langohr.core/closed?` is a new function that complements `langohr.core/open?`.

### langohr.queue/declare-server-named

`langohr.queue/declare-server-named` is a new convenience function
that declares a server-named queue and returns the name RabbitMQ
generated:

``` clojure
(require '[langohr.core  :as lhc])
(require '[langohr.queue :as lhq])

(let [conn    (lhc/connect)
      channel (lhc/create-channel conn)
      queue   (lhq/declare-server-named channel)]
  (println (format "Declared a queue named %s" queue))
```

### More Convenient TLS Support

Langohr will now correct the port to TLS/SSL if provided `:port` is
`5672` (default non-TLS port) and `:ssl` is set to `true`.



## Change Log

[Langohr change log](https://github.com/michaelklishin/langohr/blob/master/ChangeLog.md) is available on GitHub.


## Langohr is a ClojureWerkz Project

[Langohr](http://clojurerabbitmq.info) is part of the [group of libraries known as ClojureWerkz](http://clojurewerkz.org), together with

 * [Elastisch](http://clojureelasticsearch.info), a minimalistic well documented Clojure client for ElasticSearch
 * [Welle](http://clojureriak.info), a Riak client with batteries included
 * [Monger](http://clojuremongodb.info), a Clojure MongoDB client for a more civilized age
 * [Neocons](http://clojureneo4j.info), a client for the Neo4J REST API
 * [Quartzite](http://clojurequartz.info), a powerful scheduling library

and several others. If you like Langohr, you may also like [our other projects](http://clojurewerkz.org).

Let us know what you think [on Twitter](http://twitter.com/clojurewerkz) or on the [Clojure mailing list](https://groups.google.com/group/clojure).


[Michael](http://twitter.com/michaelklishin) on behalf of the [ClojureWerkz](http://clojurewerkz.org) Team
