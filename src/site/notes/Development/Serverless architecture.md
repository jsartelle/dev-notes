---
{"dg-publish":true,"dg-path":"Serverless architecture.md","permalink":"/serverless-architecture/","tags":["tech/dev-process","tech/web"],"dg-note-properties":{"created":"2023-08-04T11:25:58-05:00","modified":"2025-06-16T22:25:55-05:00","tags":["tech/dev-process","tech/web"]}}
---


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
