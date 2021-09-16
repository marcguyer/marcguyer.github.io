---
title: "Mezzio Example: Functional and Unit Testing"
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
  - "/posts/mezzio-example/functional-testing/"
---

## Where do we start?

It's a good choice to start any application with a solid structure for testing the application with automated testing tools. That's why the first post in this series is about testing. Any well-tested application will typically have more lines of testing code than actual application code. Starting with a good structure for testing will pay dividends down the road.

In this post, I'll show a basic setup for testing Mezzio applications. We'll get to some more advanced testing topics in later posts.

## Legacy ZF Testing Tools

Those experienced with [Laminas MVC](https://docs.laminas.dev/laminas-mvc/) (FKA Zend Framework MVC) applications are likely familiar with the [laminas-test](https://docs.laminas.dev/laminas-test/) library, or [Zend\\Test](https://framework.zend.com/manual/2.4/en/modules/zend.test.introduction.html) for ZF v2 and maybe even [Zend_Test](https://framework.zend.com/manual/1.12/en/zend.test.introduction.html), the old ZF v1 testing framework.

The documentation for these components allude to _unit testing_ your application using these tools. That, unfortunately is a misnomer. The testing component provided by Laminas is geared for _integration testing_ or _functional testing_ your MVC stack, but not really _unit testing_. The difference between unit tests and integration tests is dramatic. Those differences have been blogged about at length. Check the goog for more information about the different types of tests.

## A Basic Example Functional Test

Let's start with an example functional test of your new complete Mezzio application middleware stack. This example should be somewhat familiar if you're used to how [laminas-test](https://docs.laminas.dev/laminas-test/) works with MVC apps. We'll test the ping route that comes with the [Mezzio Skeleton Application](https://github.com/mezzio/mezzio-skeleton).

You may have noticed that skeleton comes with some basic tests of the simple handlers that come with the skeleton. We'll show those in a later post. For now, we're going to start with a functional test of the `GET /api/ping` REST endpoint from end to end. This test will exercise the entire stack, so we can prove that the endpoint is functional under the conditions we've set up in the test.

I've created a simple abstract class that all of my functional tests can extend, making writing new tests fast and easy. These tests use real instances of your application components -- the dependency injection container and the application itself, including the middleware pipeline and the routes. In fact the base class might remind you of an application bootstrap like that found in `public/index.php` in a Mezzio skeleton app.

### Abstract for all Functional Tests

{{< collapse link="AppFunctionalTest\AbstractFunctionalTest" collapse="in" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/test/AppFunctionalTest/AbstractFunctionalTest.php" ref="0ded9322d8fc3c770a23ef9a7478c7862d8edc86" %}}
{{< /collapse >}}

You'll note that this does nothing except initialize the application to be tested. Some assumptions must hold true for this to work out of the box. In short, if you start with the official Mezzio skeleton, you should be all set. If the paths to your container/pipeline/routes config is non-standard, you might have to do some extra work -- perhaps a symlink or two.

Any simple tests that require a minimally bootstrapped application can extend the `AbstractFunctionalTest` class and test away.

Let's define a test. 

### Functional Test Ping Handler

Here's a simple implementation of a functional endpoint test for `GET /api/ping`.

Note this extends the above `AppFunctionalTest\AbstractFunctionalTest`.

{{< collapse link="AppFunctionalTest\PingTest" collapse="in" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/test/AppFunctionalTest/PingTest.php" ref="0ded9322d8fc3c770a23ef9a7478c7862d8edc86" %}}
{{< /collapse >}}

The result of the above example is a functional test of the `GET /api/ping` route, asserting simply that we receive the expected response body.

## A Basic Example Unit Test

Now we're going to establish full test coverage on the `App\Handler\PingHandler` unit.

{{< collapse link="AppTest\Handler\PingTest" collapse="in" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/test/AppTest/Handler/PingHandlerTest.php" ref="0ded9322d8fc3c770a23ef9a7478c7862d8edc86" %}}
{{< /collapse >}}

## What's Next?

The [next post in this series](/posts/mezzio-example/psalm-introduction) introduces static analysis with [Psalm](https://psalm.dev).
