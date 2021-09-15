---
title: "Mezzio Example: Domain Model"
date: 2020-01-19T16:25:49-05:00
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

This post brings several pieces of supporting libraries together. We'

We'll start by defining a minimum domain model via Doctrine's XML mapping driver. In the short-term, we only need to model what's necessary for our application's authentication layer using OAuth2. We'll get into some detail about this domain model in the next post in the series, [OAuth2 Basics](/posts/mezzio-example/oauth-basics). For now, we're creating a minimalist set of objects to comply with our supporting libraries. We need a minimum of 6 entities and their corresponding repositories:

* User
* OAuthClient
* OAuthAuthCode
* OAuthAccessToken
* OAuthRefreshToken
* OAuthScope

Mezzio includes an implementation of Alex Bilbie's wonderful, spec-compliant [OAuth2 Server](https://oauth2.thephpleague.com/) as [an adapter](https://docs.mezzio.dev/mezzio-authentication-oauth2/) to the [Mezzio authentication abstraction middleware component](https://docs.mezzio.dev/mezzio-authentication/). Both the server library and the Mezzio authentication component provide various interfaces for which we'll need to provide concrete implementations. We'll do that while building our domain model with [Entities](#entities) and [Repositories](#repositories).

## Entities

An Entity is a persist-able PHP data object with an identity. Generally speaking, an entity represents a database table as a native PHP object with properties that correspond with the table columns.

### User Entity

A _User_ represents an unique individual person in our domain model. The _User_ entity will need to implement both the server library user interface and Mezzio authentication component's user interface:

{{< collapse link="League\OAuth2\Server\Entities\UserEntityInterface" >}}
{{% githubfile repo="thephpleague/oauth2-server" path="/src/Entities/UserEntityInterface.php" ref="8.0.0" %}}
{{< /collapse >}}


{{% githubfile repo="mezzio/mezzio-authentication" path="/src/UserInterface.php" ref="1.1.0" %}}

{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/src/App/src/Handler/PingHandler.php" ref="master" %}}

* [League\OAuth2\Server\Entities\UserEntityInterface](https://github.com/thephpleague/oauth2-server/blob/8.0.0/src/Entities/UserEntityInterface.php)
* [Mezzio\Authentication\UserInterface](https://github.com/mezzio/mezzio-authentication/blob/1.1.0/src/UserInterface.php)



## Repositories
