---
title: "Mezzio Example: Psalm Introduction"
date: 2021-09-15T14:28:49-05:00
draft: false
toc: false
images:
tags:
  - php
  - mezzio
  - laminas
  - psalm
series:
  - "Mezzio Example"
aliases:
  - "/posts/mezzio-example/psalm-intro/"
---

## What is Psalm?

[Psalm](https://psalm.dev) is a static analysis tool for PHP that helps you identify problems with your code. If you use it, you'll become a better coder. I promise. 

### Learn more about Psalm

Here's a great read -- the story of the origins of Psalm at Vimeo and the value it provided the team. 

https://psalm.dev/articles/fixing-code-that-aint-broken

In this post, I'll show a basic setup for static analysis with Psalm in Mezzio applications.

## Configuration

{{< collapse link="psalm.xml" >}}
{{% githubfile repo="marcguyer/mezzio-doctrine-oauth2-example" path="/psalm.xml" ref="b1b781a85eeef46c4f7168ea0670219dd40753af" %}}
{{< /collapse >}}

## Changes Required for Psalm Error Level 1

It's probably easiest to read the diff here:

https://github.com/marcguyer/mezzio-doctrine-oauth2-example/commit/b1b781a85eeef46c4f7168ea0670219dd40753af

## What's Next?

The [next post in this series](/posts/mezzio-example/doctrine-entities) adds Doctrine Entity classes and tests. We'll create only those models necessary for a baseline OAuth2 implementation.
