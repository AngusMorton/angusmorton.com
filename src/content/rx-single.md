---
layout: post
title: "rx.Single"
author: Angus
tags: ["android"]
date: "2018-05-06T23:46:37.121Z"
draft: false
image: 
---

`rx.Single` is a **beta** feature in [RxJava](https://github.com/ReactiveX/RxJava) that represents a single value, rather than a stream of values that an `rx.Observable` represents. This is useful in simplifying the mental model required for consumers of your API (e.g. a Retrofit service).

###### rx.Observable
 * Respond with an error.
 * Never respond.
 * Respond successfully with no data and end.
 * Respond successfully with a single value and end.
 * Respond successfully with multiple values and end.
 * Respond successfully with one or more values and don't end.

###### rx.Single
 * Respond with an error.
 * Never respond.
 * Respond successfully with a single value.

### Example

Consider a public API that retrieves a user's profile from a network API.

```
Observable<Profile> getProfile();
```

The behavior of `getProfile()` is not immediately obvious - can `getProfile()` emit multiple values? when does it end? Maybe it emits a new `Profile` when it gets updated?

What if we use `rx.Single` instead?

```
Single<Profile> getProfile();
```

Now the behavior of `getProfile()` explicit - the consumers only need to consider a single value, and that behavior is enforced by the Java type system.


### TL;DR
Consider using `rx.Single` when an API/Service layer should only emit one item.

### See
 * [ReactiveX Specification](http://reactivex.io/documentation/single.html)
 * [RxJava Github Issue](https://github.com/ReactiveX/RxJava/issues/1594)
 * [Retrofit Support Issue](https://github.com/square/retrofit/issues/969)