---
title: "Mezzio Example: Testing Doctrine Entities and Repositories"
date: 2021-09-16T16:25:49-05:00
draft: false
comments: true
toc: true
images:
tags:
  - php
  - mezzio
  - laminas
  - phpunit
  - doctrine
  - oauth2
series:
  - "Mezzio Example"
---

## Overview

In this post, we'll see how to create a comprehensive testing framework for Doctrine Entities and Repositories. This is a continuation of our [Mezzio Example series](/posts/mezzio-example/doctrine-entities-and-repositories/) where we previously created our domain model for an OAuth2-enabled application.

Let's make sure our Entities and Repositories have full test coverage. The domain model is a significant part of any dynamic application so the code should be in good shape. We don't want any bugs here.

Since Doctrine plays by a strict set of rules, we can create a simple testing framework based on those assumptions. We know the naming convention of the getters and setters, and we know the relationship between entity properties and those accessors. Further, we can use Doctrine's own ClassMetadata to ensure that we cover everything.

This post will demonstrate a reusable abstract test class that leverages PHP reflection and Doctrine's metadata to automatically test every property, getter, and setter in our entities with minimal boilerplate code.

The framework is a magic box of sorts. As I said, some rules must be followed and the assumptions must hold true for this test framework to be of value. If it doesn't work for one of your properties, or you have some extra methods in there unrelated to class properties and their accessors, you can always add test methods to test those outliers. 

## A Doctrine Entity Test Abstract

{{< collapse link="DataTest\Entity\AbstractEntityTest.php" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/test/DataTest/Entity/AbstractEntityTest.php" ref="df7e770f32137ec1c26bad02eb971421058f5a63" %}}
{{< /collapse >}}

### Entities to Support OAuth2 Requirements

We'll start by defining a minimum domain model via PHP8 Attributes and Doctrine's new attribute mapping driver. In the short-term, we only need to model what's necessary for our application's authentication layer using OAuth2. For now, we're creating a minimal set of objects to comply with the needs of our supporting libraries' interfaces. We need a minimum of 6 entities and their corresponding repositories:

* User
* OAuthClient
* OAuthAuthCode
* OAuthAccessToken
* OAuthRefreshToken
* OAuthScope

Mezzio includes an implementation of Alex Bilbie's wonderful, spec-compliant [OAuth2 Server](https://oauth2.thephpleague.com/) as [an adapter](https://docs.mezzio.dev/mezzio-authentication-oauth2/) to the [Mezzio authentication abstraction middleware component](https://docs.mezzio.dev/mezzio-authentication/). Both the server library and the Mezzio authentication component provide various interfaces for which we'll need to provide concrete implementations. We'll do that while building our domain model with [Entities](#entities) and [Repositories](#repositories).

### Psalm Changes Required for Error Level 1

#### Entity Changes

TLDR; All errors required suppression notation to pass. Check this diff for the details: https://github.com/marcguyer/mezzio-doctrine-oauth2-example/commit/5640b45bb942d577af0f4e3606f30bc91b6105c5

#### Repository Changes

https://github.com/marcguyer/mezzio-doctrine-oauth2-example/commit/df7e770f32137ec1c26bad02eb971421058f5a63

## Entities

An Entity is a persist-able PHP data object with an identity. Generally speaking, an entity represents a database table as a native PHP object with properties that correspond with the table columns.

We'll create a set of entities fulfilling the minimum requirements of both Mezzio Authentication and the corresponding OAuth2 library.

### User Entity

A _User_ represents an unique individual person in our domain model. The _User_ entity will need to implement both the OAuth2 Server library user interface and Mezzio Authentication component's user interface:

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Entities\UserEntityInterface">}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Entities/UserEntityInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

{{< collapse link="Mezzio\Authentication\UserInterface" >}}
{{% githubfile repo="mezzio/mezzio-authentication" path="/src/UserInterface.php" ref="1.2.1" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Entity\User" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Entity/User.php" ref="5640b45bb942d577af0f4e3606f30bc91b6105c5" %}}
{{< /collapse >}}

### OAuthClient Entity

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Entities\ClientEntityInterface" >}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Entities/ClientEntityInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

{{< collapse link="Mezzio\Authentication\UserInterface" >}}
{{% githubfile repo="mezzio/mezzio-authentication" path="/src/UserInterface.php" ref="1.2.1" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Entity\OAuthClient" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Entity/OAuthClient.php" ref="5640b45bb942d577af0f4e3606f30bc91b6105c5" %}}
{{< /collapse >}}

### OAuthAuthCode Entity

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Entities\AuthCodeEntityInterface" >}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Entities/AuthCodeEntityInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Entity\OAuthAuthCode" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Entity/OAuthAuthCode.php" ref="5640b45bb942d577af0f4e3606f30bc91b6105c5" %}}
{{< /collapse >}}

### OAuthAccessToken Entity

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Entities\AccessTokenEntityInterface" >}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Entities/AccessTokenEntityInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Entity\OAuthAccessToken" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Entity/OAuthAccessToken.php" ref="5640b45bb942d577af0f4e3606f30bc91b6105c5" %}}
{{< /collapse >}}

### OAuthRefreshToken Entity

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Entities\RefreshTokenEntityInterface" >}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Entities/RefreshTokenEntityInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Entity\OAuthRefreshToken" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Entity/OAuthRefreshToken.php" ref="5640b45bb942d577af0f4e3606f30bc91b6105c5" %}}
{{< /collapse >}}

### OAuthScope Entity

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Entities\ScopeEntityInterface" >}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Entities/ScopeEntityInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Entity\OAuthScope" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Entity/OAuthScope.php" ref="5640b45bb942d577af0f4e3606f30bc91b6105c5" %}}
{{< /collapse >}}

## Repositories

### User Repository

The _User_ repository will need to implement both the OAuth2 Server library user repository interface and Mezzio Authentication component's user repository interface:

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Repositories\UserRepositoryInterface">}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Repositories/UserRepositoryInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

{{< collapse link="Mezzio\Authentication\UserRepositoryInterface" >}}
{{% githubfile repo="mezzio/mezzio-authentication" path="/src/UserRepositoryInterface.php" ref="1.2.1" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Repository\UserRepository" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Repository/UserRepository.php" ref="df7e770f32137ec1c26bad02eb971421058f5a63" %}}
{{< /collapse >}}

### OAuthClient Repository

The _Client_ repository will need to implement only the OAuth2 Server library client repository interface:

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Repositories\ClientRepositoryInterface">}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Repositories/ClientRepositoryInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Repository\OAuthClientRepository" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Repository/OAuthClientRepository.php" ref="df7e770f32137ec1c26bad02eb971421058f5a63" %}}
{{< /collapse >}}

### OAuthAuthCode Repository

The _AuthCode_ repository will need to implement only the OAuth2 Server library authcode repository interface:

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Repositories\AuthCodeRepositoryInterface">}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Repositories/AuthCodeRepositoryInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Repository\OAuthAuthCodeRepository" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Repository/OAuthAuthCodeRepository.php" ref="df7e770f32137ec1c26bad02eb971421058f5a63" %}}
{{< /collapse >}}

### OAuthAccessToken Repository

The _AccessToken_ repository will need to implement only the OAuth2 Server library accesstoken repository interface:

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Repositories\AccessTokenRepositoryInterface">}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Repositories/AccessTokenRepositoryInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Repository\OAuthAccessTokenRepository" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Repository/OAuthAccessTokenRepository.php" ref="df7e770f32137ec1c26bad02eb971421058f5a63" %}}
{{< /collapse >}}

### OAuthRefreshToken Repository

The _RefreshToken_ repository will need to implement only the OAuth2 Server library refreshtoken repository interface:

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Repositories\RefreshTokenRepositoryInterface">}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Repositories/RefreshTokenRepositoryInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Repository\OAuthRefreshTokenRepository" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Repository/OAuthRefreshTokenRepository.php" ref="df7e770f32137ec1c26bad02eb971421058f5a63" %}}
{{< /collapse >}}

### OAuthScope Repository

The _Scope_ repository will need to implement only the OAuth2 Server library scope repository interface:

#### Implemented Interfaces

{{< collapse link="League\OAuth2\Server\Repositories\ScopeRepositoryInterface">}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Repositories/ScopeRepositoryInterface.php" ref="8.3.2" %}}
{{< /collapse >}}

#### Implementation

{{< collapse link="Data\Repository\OAuthScopeRepository" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/Data/Repository/OAuthScopeRepository.php" ref="df7e770f32137ec1c26bad02eb971421058f5a63" %}}
{{< /collapse >}}

## Conclusion

This post has explored a simple framework for easily testing Doctrine Entities and Repositories without writing much code. By leveraging Doctrine's metadata system and class reflection, we can automate testing of property getters and setters, ensuring our domain model is well-tested and reliable.