---
title: "Mezzio Example: Introduction"
date: 2019-11-04T15:08:36-04:00
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
---

## What is this?

Let's create a REST API with [Laminas](https://getlaminas.org/)  [Mezzio](https://getexpressive.org/). In this series, you'll learn how to create a REST API from the ground up. We'll use Mezzio as the base framework, Doctrine for the data layer, OAuth2 for authentication and authorization, PHPUnit for automated testing, and several other libraries along the way.

## Source Code

I've created a new bare-bones application starting with the [Mezzio Skeleton Application](https://github.com/zendframework/zend-expressive-skeleton) where all the code shown in this series lives. You can install it in your local environment and hack on it all you want. You're also welcome to contribute to the example application via PR. Check it out here:

[Example Application with Mezzio, Doctrine, and OAuth2](https://github.com/marcguyer/mezzio-doctrine-oauth2-example)

## Mezzio

[Mezzio](https://getexpressive.org/) is a micro framework using the middleware pattern. Middleware is a bit of code that sits between a HTTP request and response. I find it quite refreshing after working with the complexities of MVC for many years. Since [the PHP community has standardized](https://www.php-fig.org/psr/) [HTTP requests & responses via PSR-7](https://www.php-fig.org/psr/psr-7/), [dependency injection containers via PSR-11](https://www.php-fig.org/psr/psr-11/), and the [HTTP request handler and middleware interfaces via PSR-15](https://www.php-fig.org/psr/psr-15/) (among others), PHP has seen growth in modernizing legacy applications using the new tools. Mezzio has taken full advantage of the new standards and serves as the basis for much of that new growth.

I won't go into any more detail. Since you're here, you've probably already chosen Mezzio. If you have any questions at all, please don't hesitate to leave a comment in Disqus below.

## Doctrine

[Doctrine](https://www.doctrine-project.org/) is a collection of several related packages concerned with the data layer of a PHP application. Doctrine makes it possible to create applications that can run on any supported database engine via the [Database Abstraction Layer (DBAL)](https://www.doctrine-project.org/projects/dbal.html) and makes working with data objects refreshingly easy with the [Object Relational Mapper (ORM)](https://www.doctrine-project.org/projects/orm.html).

## OAuth2

[OAuth2](https://oauth.net/2/) is the current standard framework for building authorization into an application. The Mezzio framework comes with an implementation of OAuth2 using the wonderful [OAuth2 Server implementation from the League of Extraordinary Packages](https://oauth2.thephpleague.com/). That implementation comes out of the box with a basic [PDO](https://www.php.net/pdo) setup for the data layer. In this series, I'll show how we can use Doctrine instead.

## PHPUnit

[PHPUnit](https://phpunit.de/) is the de facto standard framework for automated testing in PHP applications. The [Mezzio Skeleton Application](https://github.com/zendframework/zend-expressive-skeleton) comes with a basic PHPUnit setup.

## What's Next?

The [next post in this series](/posts/example-application-with-mezzio-testing) gets into setting up a good application foundation with automated testing.
