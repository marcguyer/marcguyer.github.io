---
title: "Building Multi-Architecture AWS Lambda Functions with Bref PHP and PostgreSQL"
date: 2024-05-15T12:00:00-04:00
tags: ["php", "aws", "lambda", "bref", "postgresql", "docker", "arm64", "apple-silicon"]
draft: false
---

## The Multi-Architecture Challenge

With the rise of Apple Silicon (ARM64) and the continued use of Intel-based (x86_64) systems, developers face increasing challenges in creating applications that work seamlessly across both architectures. This is especially true for PHP developers working with AWS Lambda, where subtle differences can lead to frustrating issues.

## Example with Bref PHP Runtime + PDO PostgreSQL Extension

To address these challenges, I've created a comprehensive example repository that demonstrates a working multi-architecture implementation of PHP Lambda functions using [Bref](https://bref.sh/) and PDO PostgreSQL. This example focuses on:

1. **Automatic architecture detection** to apply the correct configuration
2. **Multi-architecture Docker containers** for testing on both platforms
3. **Architecture-specific PHP configuration** for PDO PostgreSQL
4. **Support for both Lambda Function URLs and API Gateway**

The repository is available on GitHub: [bref-php-pdo-pgsql-example](https://github.com/marcguyer/bref-php-pdo-pgsql-example)

## Key Architectural Differences

Through extensive testing, I identified critical architecture-specific behaviors that affect PHP Lambda functions:

### PDO PostgreSQL Extension Loading

- **ARM64 (using bref/arm-php-82:2)**: PDO PostgreSQL is available by default without explicit loading
- **x86_64 (using bref/php-82:2)**: PDO PostgreSQL must be explicitly loaded with `extension=pdo_pgsql`

These differences can cause mysterious failures where a function that works perfectly in one environment fails silently in another.

### Architecture-Specific PHP Configuration

The solution involves using architecture-specific PHP .ini files:

```ini
# ARM64 Configuration
; DO NOT load pdo_pgsql here as it's already loaded by default on ARM64
; extension=pdo_pgsql

# x86_64 Configuration
; Load PDO PostgreSQL extension - REQUIRED on x86_64
extension=pdo_pgsql
```

These configurations are selected at Docker build time based on the detected architecture.

## Docker Compose for Local Testing

The example includes a complete Docker Compose setup that:

1. Automatically detects your architecture and uses the appropriate configuration
2. Provides containers for both Function URL and API Gateway handlers
3. Includes emulated x86_64 containers for testing on ARM64 machines
4. Makes it easy to test PostgreSQL connectivity in a local environment

Here's a simplified version of the architecture detection script:

```bash
#!/bin/bash

# Detect the current architecture
ARCH=$(uname -m)

if [[ "$ARCH" == "arm64" ]]; then
    echo "Detected ARM64 architecture"
    export LAMBDA_PLATFORM="linux/arm64"
    export LAMBDA_DOCKERFILE="Dockerfile"
    export LAMBDA_API_DOCKERFILE="Dockerfile.api"
else
    echo "Detected x86_64 architecture"
    export LAMBDA_PLATFORM="linux/amd64"
    export LAMBDA_DOCKERFILE="Dockerfile.x86"
    export LAMBDA_API_DOCKERFILE="Dockerfile.api.x86"
fi
```

## Two Lambda Deployment Approaches

The example demonstrates both major Lambda deployment approaches:

### 1. Lambda Function URLs

- Direct HTTP access to Lambda functions without API Gateway
- More cost-effective for simple endpoints
- Uses Bref's `Handler` interface

### 2. API Gateway

- Uses Amazon API Gateway to route requests to Lambda
- More features for API management
- Implements PSR-15's `RequestHandlerInterface`

Both handlers share common logic through the `PdoTester` class, which demonstrates comprehensive PostgreSQL connection testing.

## Key Takeaways for Developers

From building this example, I learned several important lessons:

1. **Always test on both architectures** - What works on one platform may fail on another
2. **Use architecture detection** in your build pipeline to apply the correct configurations
3. **Understand extension loading differences** between ARM64 and x86_64 Bref environments
4. **Use Docker's multi-architecture support** for comprehensive local testing

## Getting Started

To try the example yourself:

1. Clone the repository: `git clone https://github.com/marcguyer/bref-php-pdo-pgsql-example.git`
2. Start the containers: `docker compose up -d`
3. Test the function: `./invoke.sh local lambda`

The example includes detailed documentation on advanced scenarios like forcing x86_64 emulation and deploying to AWS.

## Conclusion

As more developers adopt Apple Silicon machines while still deploying to x86_64 Lambda environments, understanding these architectural differences becomes increasingly important. This example aims to provide a clear path to building PHP Lambda functions that work reliably on both architectures.

By sharing these insights and patterns, I hope to save other developers the time and frustration of debugging architecture-specific issues in their serverless PHP applications.