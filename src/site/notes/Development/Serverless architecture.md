---
{"dg-publish":true,"dg-path":"Notes/Serverless architecture.md","permalink":"/notes/serverless-architecture/","tags":["articles/dev-process","tech/web"]}
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
