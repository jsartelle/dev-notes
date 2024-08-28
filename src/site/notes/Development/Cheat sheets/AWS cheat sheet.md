---
{"dg-publish":true,"dg-path":"Cheat sheets/AWS cheat sheet.md","permalink":"/cheat-sheets/aws-cheat-sheet/"}
---


# Serverless basics


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/notes/serverless-architecture/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





- Less management of scaling - don’t have to worry about how many pods are running, etc
- Managed services (AWS Lambda, Dynamo DB, CloudWatch Logs)
    - Aurora Serverless - managed MySQL/Postgres that can also handle auto horizontal scaling, sharding
- Server farm handles ingress, spins up small containers & handle scaling automatically
- Edge caching
- Only pay for what you use
- Can have per-developer production environments
- More locked in to one provider
- Code will be the same, minus composition root (entry point)
    - only the last mile changes


</div></div>


# Lambda

- run serverless functions on demand in response to events, such as:
    - updates to application state, like a user putting an item in their shopping cart
    - on demand via HTTP requests
    - changes to [[Development/Cheat sheets/AWS cheat sheet#S3\|#S3]] buckets
    - table updates in [[Development/Cheat sheets/AWS cheat sheet#DynamoDB\|#DynamoDB]]
    - state transitions in [[Development/Cheat sheets/AWS cheat sheet#Step Functions\|#Step Functions]]
- you only pay for used compute time
- smaller functions are better because they're faster to *cold start* (start up in a brand new container)
    - containers stick around for a bit after finishing, if the function runs again in this container this is a *warm start*
    - some people schedule lambdas to run every few minutes to ensure warm starts, [[Development/Cheat sheets/AWS cheat sheet#SST\|#SST]] has built-in cronjobs to do this
    - code around the actual lambda function will only run once per container start, this can be used to setup things like database connections
- integrates with other AWS services - ex. pull files from S3 buckets
- can use any third-party library, natively supports many languages (including Node)
- code and dependencies are packaged and uploaded to an S3 bucket
    - there is a limit on the total package size, if you need larger you can use a Docker image

# S3

- key-value cloud object storage
    - *objects*: files + metadata
    - *buckets*: containers for objects
- offers versioning, replication across regions, per-object permissions with [[Development/Cheat sheets/AWS cheat sheet#IAM\|#IAM]]
- *data lake*: stores data in its original form, relational or non-relational
    - schema-on-read: the data schema doesn't need to be defined until it's read
- S3 Glacier: meant for archiving data that's rarely accessed, cheaper storage prices but higher data access costs
    - also has even cheaper plans with slower retrieval times (minutes-hours instead of milliseconds)

# Aurora

- MySQL and PostgreSQL compatible relational database

# DynamoDB

- serverless NoSQL database
    - key-value and document data models
- don't need to worry about updates, maintenance, manual scaling, etc.
- global tables: replicated across multiple regions, can read and write to any replica
- DynamoDB Streams - capture every change so that event-driven applications can respond to them
- continuous (per-second) or on-demand backups, encrypted at rest

# Redshift

- SQL data warehouse solution
- *data warehouse*: stores relational data in a concrete schema (schema-on-write)
    - harder to scale than a data lake

# CloudFront

- CDN

# Step Functions

- visual programming (like Power Automate or UE Blueprints) for distributed applications
- example use cases:
    - automate ETL (data ingestion/transformation) pipelines
    - orchestrate multiple [[Development/Cheat sheets/AWS cheat sheet#Lambda\|#Lambda]] functions into microservices
    - process large datasets in parallel
    - create workflows for security incident response

# API Gateway

- lets you create and manage APIs (HTTP, REST or WebSocket) that call [[Development/Cheat sheets/AWS cheat sheet#Lambda\|#Lambda]] functions or HTTP endpoints
    - HTTP APIs are cheaper, but don't support some features of REST APIs, like edge optimization and API key management
- manages traffic, CORS, authorization and access control, throttling, monitoring, version management
    - can use [[Development/Cheat sheets/AWS cheat sheet#IAM\|#IAM]] or [[Development/Cheat sheets/AWS cheat sheet#Cognito\|#Cognito]] for authorization
    - can run multiple versions of the same API simultaneously

# Simple Queue Service (SQS)

- messaging queue
- *producers*: components that send messages to the queue
- *consumers*: components that receive messages from the queue
- while a message is being processed, it's hidden from other requests for the duration of the user-set visibility timeout (default 30 seconds)
    - it's the responsibility of a message consumer to delete the message once it's done processing

## Other message services

- Amazon SNS (Simple Notification Service): lets messages be sent to multiple subscribers through *topics*
    - good when immediate response is needed, such as real-time user engagement or alarm systems
    - doesn't hold messages if subscribers are offline
- Amazon MQ: for migrating from existing message brokers like RabbitMQ

# Identity and access management (IAM)

- fine-grained permissions system used across AWS products
- can define different roles with different permissions

# Cognito

- user directory and authentication server
- two components:
    - user pools: for authorizing users of your app or API
    - identity pools: for authorizing users to access your AWS resources
        - you can have a user sign in to a user pool to authenticate them, then exchange a user pool token with the identity pool to get credentials for AWS services
            - can assign [[Development/Cheat sheets/AWS cheat sheet#IAM\|#IAM]] roles based on rules or group membership in the user pool
        - you can also offer custom authentication, or no authentication (for anonymous access)

# SST

- third-party tool for setting up and deploying full-stack apps easily on AWS (and other cloud providers)
    - infrastructure as code
    - supports various frontends (Next.js, Astro, Solid), cronjobs, storage buckets, databases, queues, and more
