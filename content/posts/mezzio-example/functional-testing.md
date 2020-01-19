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

```php
<?php

declare(strict_types=1);

namespace FunctionalTest;

use Helmich\Psr7Assert\Psr7Assertions;
use Mezzio\Application;
use Mezzio\MiddlewareFactory;
use PHPUnit\Framework\TestCase;
use Psr\Container\ContainerInterface;

/**
 * {@inheritdoc}
 */
abstract class AbstractFunctionalTest extends TestCase
{
    use Psr7Assertions;

    /** @var ContainerInterface */
    protected static $container;

    /** @var Application */
    protected static $app;

    public static function setUpBeforeClass(): void
    {
        static::initContainer();
        static::initApp();
        static::initPipeline();
        static::initRoutes();
    }

    public static function tearDownAfterClass(): void
    {
        static::$container = null;
        static::$app = null;
    }

    /**
     * Initialize new container instance.
     */
    protected static function initContainer(): void
    {
        static::$container = require 'config/container.php';
    }

    /**
     * Initialize app.
     */
    protected static function initApp(): void
    {
        static::$app = static::$container->get(Application::class);
    }

    /**
     * Initialize pipeline.
     */
    protected static function initPipeline(): void
    {
        (require 'config/pipeline.php')(
            static::$app,
            static::$container->get(MiddlewareFactory::class),
            static::$container
        );
    }

    /**
     * Initialize routes.
     */
    protected static function initRoutes(): void
    {
        (require 'config/routes.php')(
            static::$app,
            static::$container->get(MiddlewareFactory::class),
            static::$container
        );
    }
}
```

Any simple tests that require only a minimally bootstrapped application can extend the `AbstractFunctionalTest` class and test away.

Now it gets a little bit more interesting. Here's an abstract extension that adds some framework for defining REST endpoints to be tested and assertions for testing those endpoints:

```php
<?php

declare(strict_types=1);

namespace FunctionalTest;

use Laminas\Diactoros\ServerRequest;
use Laminas\Diactoros\Stream;
use Laminas\Diactoros\Uri;
use PHPUnit\Framework\Constraint;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

/**
 * Abstract to set up functional testing via endpoint provider config.
 */
abstract class AbstractEndpointTest extends AbstractFunctionalTest
{
    /**
     * Provider for testEndpoint() method.
     *
     * @see self::testEndpoint() for provider signature
     */
    abstract public function endpointProvider(): array;

    protected function getRequest(
        string $method,
        string $uri,
        array $requestHeaders = [],
        array $body = [],
        array $queryParams = []
    ): ServerRequestInterface {
        $uri = new Uri($uri);

        if (null !== $body) {
            $bodyStream = fopen('php://memory', 'r+');
            fwrite($bodyStream, json_encode($body));
            $body = new Stream($bodyStream);
        }

        if (!empty($queryParams)) {
            $uri = $uri->withQuery(http_build_query($queryParams));
        }

        return new ServerRequest(
            [],
            [],
            $uri,
            $method,
            $body ?? 'php://input',
            $requestHeaders ?? []
        );
    }

    /**
     * @dataProvider endpointProvider
     *
     * @param Constraint[] $responseConstraints
     */
    public function testEndpoint(
        ServerRequestInterface $request,
        array $responseConstraints = []
    ): void {
        $response = static::$app->handle($request);
        $this->assertInstanceOf(ResponseInterface::class, $response);
        $this->assertResponseConstraints($responseConstraints, $response);
    }

    /**
     * @param Constraint[] $responseConstraints
     */
    protected function assertResponseConstraints(
        array $responseConstraints,
        ResponseInterface $response
    ): void {
        if (empty($responseConstraints)) {
            $responseConstraints[] = self::isSuccess();
        }
        foreach ($responseConstraints as $msg => $constraint) {
            $this->assertThat(
                $response,
                $constraint,
                is_string($msg) ? $msg : ''
            );
        }
    }
}
```
