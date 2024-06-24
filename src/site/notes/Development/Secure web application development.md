---
{"dg-publish":true,"dg-path":"Notes/Secure web application development.md","permalink":"/notes/secure-web-application-development/","tags":["tech/security","tech/web"]}
---


# Access and Auditing

## Authentication - who is this user?

- harden user auth flows
    - password strength libraries like zxcvbn
    - breached credential DB (Have I Been Pwned)
    - MFA
    - rate limiting & logging
- avoid solely SMS-based auth (SIM-swapping attacks)
- ensure users can invalidate sessions
- audit SSO implementations (SAML and OAuth)

## Authorization - what is this user allowed to do?

- document auth controls
    - build a doc as the "source of truth" for auth decisions
- roles should implement least-privilege
- don't rely on hard-to-guess IDs instead of auth controls
- log spikes in failures

## Admin Interfaces

- keep admin interfaces separate from user-facing apps
    - ideally on internal network or behind identity-aware proxy
- implement detailed logging & audit trail
- require MFA for auth
    - sensitive actions should require re-auth

## Logging

- centralized log management
    - compromising an individual server shouldn't allow editing or removal of existing logs
- log all sensitive operations
- don't log user secrets - passwords, API keys, PII, etc
    - don't log full requests
- audit logs periodically

# Secure Design Principles

## Centralize common security functions

- validation, encoding, canonicalization, etc
- implement once, review thoroughly, write tests, reuse

## Enforce authentication & authorization by default

- shouldn't need to be manually added to each endpoint
- default to strictest ruleset and opt-out as needed
- robust test coverage & easy auditing

## Always treat user data as untrusted

- don't assume other services and integrations are passing in legit info - validate data at every trust boundary
- ensure state is correct at every step in the process

## Don't rely on client-side security controls

- frontend validation should be a usability feature, not security
- anything coming from the browser is potentially untrustworthy

## Isolate dangerous code

- common attacks for XML, YAML, image formats, etc
- isolate parsing into separate sandboxed microservices

## Fail closed by design

- default to closed state on failures
- if data can't be validated, assume it's bad and stop processing
- if a downstream service can't be reached, assume a failed response (especially w/ auth services)

## Avoid unnecessary cryptography

- don't use it if you don't need it, don't write your own if you do
    - ex. if you don't need to recover a user's plaintext credentials, don't encrypt them, store them as one-way hashes
- opaque tokens vs JWTs
    - if you can issue a random (opaque) token and store session data on server, do it to avoid dealing with signature attacks & claim validation

# Integrating AppSec into Development

- difficult to implement security without repeatable processes
- reach out to security early & often

## Threat modeling

- table-top exercise to talk through app architecture and security boundaries and identify risks
    - map out all components, users of each, connections between components, details of data storage, logging

## Pull request review

- security reviews can take place during normal PR review process
- useful for smaller changes that modify sensitive code
- trigger a security review if PR includes:
    - new dependencies & integrations
    - auth logic
    - encryption
    - external programs/shell commands
    - file manipulation
    - new service endpoints
    - direct handling of sensitive data

## Automated tooling

- dependency checkers
- static analysis
- dynamic analysis
- can run on PR, deployment, or scheduled

## Dependency tracking

- track what's deployed, which services are using which libraries, vulnerabilities, keep up to date

## Patching vulnerabilities

- assess worst-case impact, likelihood that someone will find it
- if possible review logs
- search codebase for similar patterns

# See also

- [[Development/Common web app security vulnerabilities\|Common web app security vulnerabilities]]
