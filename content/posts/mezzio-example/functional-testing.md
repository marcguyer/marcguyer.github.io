---
title: "Mezzio Example: Functional Testing"
date: 2019-12-01T14:28:49-05:00
draft: false
toc: false
images:
tags:
  - php
  - mezzio
  - laminas
  - prophecy
  - phpunit
series:
  - "Mezzio Example"
aliases:
  - "/posts/mezzio-example/testing/"
---

## Where do we start?

It's a good choice to start any application with a solid structure for testing the application with automated testing tools. That's why the first post in this series is about testing. Any well-tested application will typically have more lines of testing code than actual application code. Starting with a good structure for testing will pay dividends down the road.

In this post, I'll show a basic setup for testing Mezzio applications. We'll get to some more advanced testing topics in later posts.

## Legacy ZF Testing Tools

Those experienced with [Laminas MVC](https://docs.laminas.dev/laminas-mvc/) (FKA Zend Framework MVC) applications are likely familiar with the [laminas-test](https://docs.laminas.dev/laminas-test/) library, or [Zend\\Test](https://framework.zend.com/manual/2.4/en/modules/zend.test.introduction.html) for ZF v2 and maybe even [Zend_Test](https://framework.zend.com/manual/1.12/en/zend.test.introduction.html), the old ZF v1 testing framework.

The documentation for these components allude to _unit testing_ your application using these tools. That, unfortunately is a misnomer. All of the testing libraries provided by Laminas are geared for _integration testing_ or _functional testing_ your code, but not really _unit testing_. The difference between unit tests and integration tests is dramatic. Those differences have been blogged about at length. Check the goog for more information about the different types of tests.

## A Basic Example

Let's start with an example functional test of your new complete Mezzio application middleware stack. This example should be somewhat familiar if you're used to how [laminas-test](https://docs.laminas.dev/laminas-test/) works with old MVC apps. We'll test the ping route that comes with the [Mezzio Skeleton Application](https://github.com/mezzio/mezzio-skeleton).

You may have noticed that skeleton comes with some basic tests of the simple handlers that come with the skeleton. We'll show those in a later post. For now, we're going to start with a functional test of the `GET /api/ping` REST endpoint from end to end. This test will exercise the entire stack so we can prove that the endpoint "works".

I've created a couple of simple abstract classes that all of my functional tests can extend, making writing new tests fast and easy. These tests use real instances of your application components -- the dependency injection container and the application itself including the middleware pipeline and the routes. In fact the base class might remind you of an application bootstrap like that found in `public/index.php` in a Mezzio skeleton app.

Here's the base class for all functional tests. You'll note that this does nothing except initialize the application to be tested. Some assumptions must hold true for this to work out of the box. In short, if you start with the Mezzio skeleton, you should be all set. If the paths to your container/pipeline/routes config is non-standard, you might have to do some extra work -- perhaps a symlink or two.

{{<highlight php>}}
{{% githubfile url="https://api.github.com/repos/marcguyer/mezzio-doctrine-oauth2-example/contents/test/FunctionalTest/AbstractFunctionalTest.php" %}}
{{</highlight>}}

Any simple tests that require only a minimally bootstrapped application can extend the `AbstractFunctionalTest` class and test away.

Now it gets a little bit more interesting. Here's an abstract extension that adds some framework for defining REST endpoints in a PHPUnit `@dataProvider` to be tested and the constraints to assert for those endpoints:

{{<highlight php>}}
{{% githubfile url="https://api.github.com/repos/marcguyer/mezzio-doctrine-oauth2-example/contents/test/FunctionalTest/AbstractEndpointTest.php" %}}
{{</highlight>}}

Now we can actually define a test. Here's a simple implementation of ab endpoint test for `GET /api/ping`. Note that going forward, we only need to define an array of endpoints to be tested:

{{<highlight php>}}
{{% githubfile url="https://api.github.com/repos/marcguyer/mezzio-doctrine-oauth2-example/contents/test/FunctionalTest/PingTest.php" %}}
{{</highlight>}}

The result of the above example is a test of the `GET /api/ping` route, asserting simply that:

1. Response code is >=200 and <300
2. Response body contains JSON with a property called `ack` with a value of the current time in UNIX timestamp format.
