---
title: "About"
date: 2019-08-03T09:42:49-04:00
draft: false
---

## Personal

I have interests, it's true. In no particular order, here are a few:

* Solving problems
* Flying - currently licensed with a single-engine land class rating
* Playing golf
* Solving problems
* Sleeping
* Waking up
* Solving problems

I live in downtown Bloomington, Indiana, with [Kate](https://www.kategalvin.com/).

## Professional

### The Past

{{< youtube 2IPAOxrH7Ro >}}

Way back in the year 2000, I cofounded [Resite Information Technology](http://resiteit.com/). Amazingly as of this writing, that site remains up. After selling the company in 2007, the name was changed to [Resite Online](http://www.resiteonline.com/). That's right, "online."

The Resite platform was written initially in PHP3 and as of 2006 was serving the automated payment acceptance needs of thousands of large apartment complexes in 40 states in the US. Resite was the very first to serve the multi-family industry with a system that enabled tenants to set up automated rent payments via credit card and ACH. I wrote 90% of the code.

In 2009, as a cofounder of [SproutBox](http://sproutbox.com), I founded [GetCheddar, Inc](https://www.getcheddar.com).

Since founding GetCheddar (formerly CheddarGetter), I have remained focused as CTO and Full Stack Architect. Cheddar is the first of it's kind to offer a true software-as-a-service recurring billing and payments platform with fully automated merchant self-signup and self-service. Cheddar specializes in providing usage tracking and billing for web application subscriptions.

The Cheddar platform was initially written in PHP5 and Zend Framework version 1. Initially hosted on the Rackspace Cloud, the entire environment was soon migrated to a proprietary PCI-compliant environment. The hosting environment migration resulted in zero downtime. Later the codebase was migrated to PHP7 and Zend Framework v2. Most recently, it was rewritten as a pure REST API using the middleware pattern via [Mezzio](https://getlaminas.org/) (FKA Zend Framework Expressive) with OAuth2. I wrote 99% of the code.

### The Present

Since 2020, I've been assisting startups with team leading and architectural and engineering challenges in cloud software systems.

#### Globalized Mobile Application REST API Platform (2021-present)

I've been leading the DevOps and infrastructure architecture for a mature, sophisticated mobile platform built as a microservices monorepo. Over 4 years and 43,964 commits, the project evolved from basic to enterprise-grade infrastructure:

**Key Technical Achievements:**
- **Blue/Green Deployment System**: zero-downtime deployment orchestration system with 7-phase pipeline, event management, and automated rollback capabilities. This production-ready system coordinates CloudFormation, Serverless Framework, Serverless Application Model (SAM) and heterogeneous database migrations (AWS DMS). The project resulted in a >70% reduction in AWS costs for the client.
- **PostgreSQL Migration**: Complete database engine migration with advanced performance tuning for 2000+ concurrent connections. Combined a legacy data layer with 2 primary databases: existing MySQL Aurora and OpenSearch Serverless source with single PostgreSQL target. 
- **Integrated Development Environment**: Multi-service, multi-language, hot-reload development with automatic secret management and LocalStack integration
- **Microservices Architecture**: Modern AWS Lambda with NodeJs, Python, and PHP runtimes. 

**Technologies Used:**

*Backend & APIs:*
- PHP/Laminas/Mezzio microservices framework
- Python for automation, tooling, and data processing
- AWS Lambda functions (Python/Node.js/PHP) with LocalStack and SAM emulation
- REST API architecture with OAuth2 authentication

*Frontend & Mobile:*
- Next.js 14 with App Router for web applications
- React Native for mobile applications
- TypeScript for type-safe development

*Databases & Storage:*
- AWS Aurora Serverless (MySQL and PostgreSQL engines)
- PostgreSQL with PostGIS for geospatial capabilities
- Redis for caching and session management
- Amazon S3 for object storage
- Amazon OpenSearch Serverless for search and analytics

*Infrastructure & DevOps:*
- AWS CloudFormation for Infrastructure as Code
- AWS CDK for cloud infrastructure provisioning
- Docker & Docker Compose for unified development environment and CI/CD
- Bitbucket Pipelines for CI/CD automation
- AWS VPC, EC2, Load Balancing, Auto Scaling
- Blue/Green deployment orchestration

*Development & Testing:*
- LocalStack for AWS service emulation
- PHPUnit and Jest and Pytest for testing frameworks
- Git with monorepo architecture
- Hot-reload development environments
- Automated secret management and injection

*Monitoring & Operations:*
- AWS CloudWatch for monitoring and logging
- Infrastructure automation with Python scripting
- Performance optimization for high-concurrency workloads
- Database migration and optimization tooling

### The Future

You can help define this. I'm currently available for hire. [Contact me](/contact)
