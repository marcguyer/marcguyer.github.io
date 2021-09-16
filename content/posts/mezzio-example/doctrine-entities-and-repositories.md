---
title: "Mezzio Example: Doctrine Entities and Repositories"
date: 2021-09-15T16:25:49-05:00
draft: false
toc: false
images:
tags:
  - php
  - mezzio
  - laminas
  - prophecy
  - phpunit
  - doctrine
  - oauth2
series:
  - "Mezzio Example"
---

## Overview

This post brings several pieces of supporting libraries together. 

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

TLDR; All errors required suppression notation to pass. Check this diff for the details: https://github.com/marcguyer/mezzio-doctrine-oauth2-example/commit/5640b45bb942d577af0f4e3606f30bc91b6105c5

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

... to be continued ... 
