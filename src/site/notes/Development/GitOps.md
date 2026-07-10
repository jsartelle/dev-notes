---
{"dg-publish":true,"dg-path":"GitOps.md","permalink":"/git-ops/","dg-note-properties":{"created":"2023-08-04T11:25:58-05:00","modified":"2025-06-16T22:25:55-05:00"}}
---


- **declarative**: instead of telling the system what to *do*, you describe what you *want* and it figures out how to get there
- **versioned**: can easily roll back the whole system state to a previous version, state changes can be reviewed through PRs
- instead of pushing changes to deployment, changes are **automatically applied** from the source
- software agents **continuously observe** the *actual* system state and try to reconcile it with the desired state
- improved observability & security, simpler disaster recovery
