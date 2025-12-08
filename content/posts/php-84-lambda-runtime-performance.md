---
title: "Building a PHP 8.4 Lambda Runtime: Performance Analysis"
date: 2025-11-24T12:00:00-05:00
tags: ["php", "aws", "lambda", "arm64", "performance", "serverless"]
series: ["serverless-php"]
draft: false
comments: true
---

## Why Build Your Own Runtime?

[Bref](https://bref.sh/) is the most popular way to run PHP on AWS Lambda, and for good reason—it's well-maintained and gets the job done. However, there are situations where building your own runtime makes sense: you need a specific PHP version before Bref supports it, you want particular extensions, or you simply prefer working with standard PHP patterns without additional abstractions.

This post documents a fully functional PHP 8.4 runtime running on ARM64, complete with performance benchmarks. Consider it a clearly documented alternative to Bref for teams who want complete control over their PHP Lambda environment.

## Runtime Architecture

Single Lambda layer (~33MB) containing PHP 8.4 for ARM64, minimal bundled libraries (only `libonig`), and a bootstrap script implementing the [Lambda Runtime API](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-api.html). Supports both PSR-15 HTTP handlers and callable functions.

## Performance Benchmarks

Benchmarks across multiple memory configurations on ARM64. Handler processes 10 HAL-formatted entity responses.

### Warm Invocation Performance (100 samples each)

Pure CPU performance without I/O:

| Memory | Avg | Median | P95 | P99 | Cost/1M |
|--------|-----|--------|-----|-----|---------|
| 128MB  | 4.2ms | 2ms | 17ms | 20ms | $0.0069 |
| 256MB  | 2.2ms | 2ms | 3ms | 14ms | $0.0075 |
| 384MB  | 2.1ms | 2ms | 3ms | 4ms | $0.0105 |
| 512MB  | 2.2ms | 2ms | 3ms | 6ms | $0.0145 |
| 1792MB | 2.1ms | 2ms | 3ms | 4ms | $0.0495 |

**Key findings:**
- 256MB+ configurations all average ~2ms
- 128MB is CPU-bound at 4.2ms (2x slower)
- P99 latency matters: 384MB shows 4ms vs 256MB's 14ms spikes

### Cold Start Performance (30 samples each)

| Memory | Avg Total | Avg Init | Median | Min | Max |
|--------|-----------|----------|--------|-----|-----|
| 256MB  | 288ms | 132ms | 282ms | 265ms | 333ms |
| 384MB  | 279ms | 144ms | 272ms | 244ms | 359ms |
| 512MB  | 270ms | 147ms | 266ms | 240ms | 320ms |
| 1792MB | 252ms | 138ms | 252ms | 224ms | 278ms |

**Key findings:**
- Init time: 132-147ms (relatively consistent)
- Higher memory = slightly slower init (more resources to allocate)
- Minimal benefit beyond 256MB (only 3% faster at 512MB)

### Cost Analysis

At 384MB with AWS Lambda ARM64 pricing ($0.0000133334 per GB-second):

| Scenario | Cost per 1M Requests |
|----------|---------------------|
| 100% Warm (2ms) | $0.01 |
| 1% Cold (279ms avg) | $0.024 |
| 5% Cold | $0.079 |
| 10% Cold | $0.148 |

## Memory Recommendations

**256MB** - Best cost/performance ($0.0075/1M, 2.2ms avg)
**384MB** - Lowest P99 latency (4ms vs 256MB's 14ms spikes)

Avoid 128MB (2x slower) and >512MB (no performance gain). Function uses ~42MB actual memory.

## Comparison with Bref

Comparison to Bref's [published benchmarks](https://github.com/brefphp/benchmarks):

| Aspect | This Runtime | Bref (Layer/ARM) |
|--------|--------------|------------------|
| PHP Version | Any (8.4 here) | Limited to Bref releases |
| Extensions | Full control | Pre-selected set |
| Cold Start Init | ~132-147ms | ~170ms+ |
| Warm | ~2ms | ~2ms |
| Layer Size | ~33MB | ~40-60MB |
| Abstraction | Standard PSR-7/PSR-15 | Bref-specific handlers |

## vs. Swoole/RoadRunner

[Swoole](https://www.swoole.co.uk/) and [RoadRunner](https://roadrunner.dev/) keep PHP processes alive between requests. Lambda's warm containers achieve similar performance (~2ms/request). The difference: you manage 24/7 servers vs. AWS manages infrastructure with pay-per-request and zero idle cost. Lambda wins for variable traffic; Swoole/RoadRunner for consistent high-volume workloads where you're already managing servers.

## Conclusion

PHP 8.4 on Lambda ARM64: 132-147ms cold start, 2ms warm, 33MB layer, $0.0075/1M requests at 256MB. Full control over PHP versions and extensions with standard PSR-7/PSR-15 patterns.

**Repository:** [github.com/marcguyer/lambda-php](https://github.com/marcguyer/lambda-php)

---

*This post is part of my [serverless PHP series](/series/serverless-php/).*

*This post was written collaboratively with AI assistance, practicing the iterative refinement approach it advocates.*