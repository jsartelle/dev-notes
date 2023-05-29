---
{"dg-publish":true,"dg-path":"Notes/Common web app security vulnerabilities.md","permalink":"/notes/common-web-app-security-vulnerabilities/"}
---


# SQL Injection (sqli)

- DB can't distinguish between query statement & query parameters
- **prevention**:
    - use prepared statements & parameterized queries
        - safer than trying to escape/encode special characters
    - use an ORM
    - use allowlists for strict input validation
        - ex. if a number is expected, only allow digits

# Cross-Site Scripting (XSS)

- lets attackers inject malicious scripts into a user's browser session
- three main types:
    - stored: malicious script is stored server-side and executed when interacted with
        - ex. a profile field, forum post, comment, etc
        - may affect lots of users
    - reflected: app receives malicious data in a request and then reflects it in the response to the victim
        - usually requires victim to visit a specially crafted link
        - ex. putting a `javascript:` URL in the homepage field on their forum profile
        - ex. if the site inserts the search query into a DOM element, could do something like `https://example.com/?search=<script>alert(3)</script>`
    - DOM: malicious input is transformed by JS and written back to DOM
        - ex. site takes the user's query and uses it to construct and insert an element using `document.write`, user writes `<svg onload="...">` in their query
- **prevention**:
    - encode ALL data when output
    - filter input data when possible
        - ex. phone number fields shouldn't accept HTML
    - use framework's XSS protection
    - Content-Security-Policy

# Cross-Site Request Forgery (CSRF)

- lets malicious user induce victim into performing state-changing actions against their will
- ex: attacker loads script onto a site they control and coerces victim to visit it
    - script triggers a POST request to the vulnerable app endpoint using WithCredentials, so victim's valid cookie is included
- **prevention**:
    - unique token not known to the attacker passed along with state-changing request
        - csrf_token POST variable checked against csrf_token cookie (since attacker can't see the cookies, only send them)
        - or X-Csrf-Token header
    - Content-Type and custom headers
    - SameSite cookies
    - use CORS

## CORS

- Same Origin Policy (SOP) lets sites only make simple requests to different origins (ex. POST a form, embed an image) and can't read the responses
- CORS is a set of request/response headers that relax the SOP, triggers a preflight OPTIONS request
- CSRF payloads are usually hosted on a separate origin, which triggers the preflight, and if that fails the CSRF request is dropped
    - to enforce this use a custom header or non-standard content type (like `application/json`)
- improper CORS setup (ex. making allowed origins too wide) can open more vulnerabilities

# Server-Side Request Forgery (SSRF)

- app makes a request to another service and user input is used to build the URL
    - ex. image upload functionality that accepts a URL for a remote image
    - attacker can provide a hash (#?) in their query, causing the rest of the URL to be ignored
- lets an attacker cause the app server to make arbitrary requests (with caveats)
- can let attacker get information from app's internal network
- **prevention**:
    - proxies
        - ex. smokescreen or squid
    - custom microservice to fetch URLs
    - URL allowlists
        - use a URL parser library to validate, not regex
- pitfalls in prevention
    - checking the protocol: most protocols can be used
    - checking that host isn't "localhost", "127.0.0.1", etc
        - attacker can register a domain and register it to localhost
    - resolving domain name and checking IP
        - DNS entry can change

# Authentication/Authorization (AuthN/AuthZ)

- types of privilege escalation:
    - horizontal: two users of the same type
        - ex. one forum user can change the info of another user
    - vertical: escalating to a different role type
        - ex. forum user can access the admin panel
- do not send session information in URL parameters, as these are logged often

# Parsing Dangerous File Formats

- ex: ZIP, YAML, XML, serialized data, images
- can result in denial of service, local file inclusion (reading files from the server), SSRF, code execution
- **prevention**:
    - research the format to find known issues & edge cases
    - use a known library that defaults to good configs
    - offload parsing to an isolated service (lambda or similar)

# Server-Side Template Injection (SSTI)

- when user input is interpreted as a template string by the app
    - usually when building templates with string concatenation
- can result in [[Development/Common web app security vulnerabilities#Cross-Site Scripting (XSS)\|XSS]] or full code execution on app server
- **prevention**:
    - use template engine variables instead of string concatenation

# Insecure Dependencies

- **prevention:**
    - ensure all dependencies are updated regularly
    - check for publicly disclosed vulnerabilities
        - tools: Retire.js, npm/yarn audit, OWASP Dependency Check (Java), bundler-audit (Ruby), pip safety (Python), etc
        - integrate with CI/CD pipeline
    - look for alternatives to dependencies that are unmaintained or have bad track records
    - develop a patching strategy to periodically review & update dependencies

# See also

[[Development/Secure web application development\|Secure web application development]]

<div class="rich-link-card-container"><a class="rich-link-card" href="https://portswigger.net/web-security" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://portswigger.net/content/images/logos/academy-twittercard.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Web Security Academy: Free Online Training from PortSwigger</h1>
		<p class="rich-link-card-description">
		The Web Security Academy is a free online training center for web application security, brought to you by PortSwigger. Create an account to get started.
		</p>
		<p class="rich-link-href">
		https://portswigger.net/web-security
		</p>
	</div>
</a></div>
