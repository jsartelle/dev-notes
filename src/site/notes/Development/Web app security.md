---
{"dg-publish":true,"dg-path":"Web app security.md","permalink":"/web-app-security/","tags":["tech/security","tech/web"]}
---


# Vulnerabilities

## SQL Injection (sqli)

- happens when passing user-provided input into a SQL query - the user can put SQL statements in their input which then get executed
- **prevention**:
    - use prepared statements & parameterized queries
        - safer than trying to escape/encode special characters
    - use an ORM that sanitizes user input
    - use allowlists for strict input validation
        - for example, if a number is expected, only allow digits

## Cross-Site Scripting (XSS)

- when attackers inject malicious scripts into pages that other users view
- three main types:
    - *non-persistent* or *reflected*: the site receives malicious data in a request and then reflects it in the response to the victim
        - usually requires the victim to visit a specially crafted link
        - example: if the site displays the user's search query in a DOM element, could look something like `https://example.com/?search=<script>'malicious code here'</script>`
            - another example: the attacker puts a `javascript:` link in their forum profile, which runs when other users click it
        - can be *server-side* (the malicious input is sent to the server and back) or *DOM-based* (malicious input is transformed in JavaScript and written back to the DOM entirely on the client side)
    - *persistent* or *stored*: the malicious code is stored server-side and sent to other users
        - example: the attacker writes a script tag in their user profile, a forum post, a comment, etc, and that text is directly displayed as HTML for other users
        - can affect many more users since no social engineering is required, and may even be programmed to "infect" the victim's account when ran
    - *self-XSS*: an attack that relies on tricking the victim into pasting malicious code into the developer console (meaning no website vulnerability is neede)
- **prevention**:
    - sanitize output before displaying it
    - filter input data when possible - for example, a phone number field shouldn't accept HTML
    - use your framework's XSS protection - most frameworks won't let you directly render HTML from a string without jumping through hoops
    - use a [[Development/Web app security#Content-Security-Policy\|#Content-Security-Policy]] to disable untrusted scripts
    - mark cookies as `HttpOnly` so they can't be read by JavaScript running in the browser

## Cross-Site Request Forgery (CSRF)

- the attacker tricks a victim into sending an unintended request using the victim's (valid) credentials - for example, changing their password or transferring funds
    - the attacker can coerce the victim into sending a request, but the response still goes to the victim, so it can't be directly used to steal data
- happens because by default, the browser automatically includes cookies for a domain in any requests sent to that domain, even if the request comes from a different domain
- example: The attacker directs the victim to a site that they control with the contents below. The victim doesn't even have to click, because of the script that submits the form on page load.

```html
<!-- example from https://portswigger.net/web-security/csrf -->
<html>
    <body>
        <form action="https://vulnerable-website.com/email/change" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

- GET requests are even easier to exploit, because the attacker can set the URL as the source of an \<img\> tag, so as soon as the victim opens their email the request is made
- *login CSRF*: The attacker makes the victim log in to an account the attacker controls. If the victim doesn't realize, they might end up adding payment info or other sensitive details to the account.

```html
<!-- example from https://support.detectify.com/support/solutions/articles/48001048951-login-csrf -->
<form id="LoginForm" action="http://target/login.php" method="post">
    <input name="user" value="foo">
    <input name="pass" type="password" value="bar">
    <input type="submit">
</form>

<script>
    document.getElementById("LoginForm").submit();
</script>
```

- **prevention**:
    - don't use GET requests for anything that changes state
    - mark cookies as [SameSite=strict](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#controlling_third-party_cookies_with_samesite), which only sends them if the request comes from the origin site
        - `SameSite=lax` also sends the cookie if the user follows a link to the origin site (top-level navigation), but not for other requests
    - check the `Referer` header to make sure requests came from a trusted source
    - *CSRF tokens*: when the user logs in or navigates, the server sends their browser a unique token, and every state-changing request is required to include that token as a header or POST variable
        - this works because the attacker can't see the victim's cookies or other local storage
        - these tokens should have a limited lifespan to further increase security
    - require a custom header or use a non-standard content type to trigger a [[Development/Web app security#CORS (Cross-Origin Resource Sharing)\|CORS]] preflight request

## Server-Side Request Forgery (SSRF)

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

## Authentication/Authorization (AuthN/AuthZ)

- *authentication*: validating that the user is who they say they are
- *authorization*: validating that the user has permission to perform a certain action
- types of privilege escalation:
    - horizontal: two users of the same type
        - ex. one forum user can change the info of another user
    - vertical: escalating to a different role type
        - ex. forum user can access the admin panel
- do not send session information in URL parameters, as these are logged often

## Parsing Dangerous File Formats

- ex: ZIP, YAML, XML, serialized data, images
- can result in denial of service, local file inclusion (reading files from the server), SSRF, code execution
- **prevention**:
    - research the format to find known issues & edge cases
    - use a known library that defaults to good configs
    - offload parsing to an isolated service (lambda or similar)

## Server-Side Template Injection (SSTI)

- when user input is interpreted as a template string by the app
    - usually when building templates with string concatenation
- can result in [[Development/Web app security#Cross-Site Scripting (XSS)\|XSS]] or full code execution on app server
- **prevention**:
    - use template engine variables instead of string concatenation

## Insecure Dependencies

- **prevention:**
    - ensure all dependencies are updated regularly
    - check for publicly disclosed vulnerabilities
        - tools: Retire.js, npm/yarn audit, OWASP Dependency Check (Java), bundler-audit (Ruby), pip safety (Python), etc
        - integrate with CI/CD pipeline
    - look for alternatives to dependencies that are unmaintained or have bad track records
    - develop a patching strategy to periodically review & update dependencies

# Security features

## JSON Web Tokens (JWT)

- a standard for creating **signed** authentication tokens that can't be tampered with (without invalidating the signature)
    - can be **stateless**, meaning session data isn't stored on the server - if there is a separate auth service, services that check JWTs don't need to check with the auth service on every request
- three parts (`header.payload.signature`):
    - *header*: states the token type (JWT) and the algorithm used to generate the signature
    - *payload*: holds standard fields (claims) like `iat` (issued at) and `exp` (expiry time), as well as any application-specific claims like user information
        - since user info is stored in the token, it doesn't need to be retrieved from the database for every request
        - although JWTs are signed, they aren't typically encrypted (though they can be), so avoid storing sensitive information in them
    - *signature*: calculated based on the header and payload, so if either is tampered with the signature won't match
        - signatures can use either a shared secret, or public/private keys - shared secrets allow clients to generate a JWT on their own
- best to avoid storing JWTs in browser storage (except `HttpOnly` cookies), because browser extensions and other client-side JavaScript (such as that from an [[Development/Web app security#Cross-Site Scripting (XSS)\|XSS]] attack) can access them
- typically sent in the `Authorization` header with each request
- invalidation:
    - the secret can be rotated to invalidate all tokens
    - tokens can be stored in a blocklist on the server to invalidate them in case of a known security breach, but then the authentication is no longer stateless
        - use an in-memory database like Redis to auto-expire the blocklist entries when the associated token would expire, so the blocklist doesn't grow forever
    - JWTs can be made to expire quickly, but combined with a long-lived [[Development/Web app security#Refresh Tokens\|refresh token]], to keep the JWT stateless while still allowing login to be revoked (at least as quickly as the JWT expires)

## Refresh Tokens

- a long-lived token that can be exchanged to get a shorter-lived access token (such as a [[Development/Web app security#JSON Web Tokens (JWT)\|JWT]])
- if an attacker gets the refresh token, they can generate new access tokens, but there are ways to mitigate this:
    - make refresh tokens single-use, and return a new one each time it's exchanged for an access token
    - if a previously-used refresh token is reused, invalidate all tokens for that user (log them out)
- if the user manually logs out, the refresh token should be invalidated

## CORS (Cross-Origin Resource Sharing)

- by default, the Same-Origin Policy (SOP) prevents websites from accessing resources from other origins
    - sites can **embed** content like images and scripts from other origins, but can't read their contents
- CORS is a set of request and response headers that let sites relax the SOP
    - when a site tries to make a cross-origin request, the browser adds an `Origin` header with the requesting origin
    - if the server allows access, it adds an `Access-Control-Allow-Origin` header with the requesting origin to the response, which tells the browser to let the requesting site access the response data
        - servers can send `Access-Control-Allow-Origin: *` to let any site access a resource (for example, publicly hosted files), but these requests can't include credentials
- `fetch` requests must be created with `mode: 'cors'` and `credentials: 'include'` to allow sending cookies cross-origin
- "complex" requests will trigger the browser to send a preflight OPTIONS request before making the attempted request ([see here](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#simple_requests) for what constitutes a complex request)
    - to allow the request, the server must respond with an `Access-Control-Allow-Origin` header that matches the requesting domain, as well as `Access-Control-Allow-Methods` and `Access-Control-Allow-Headers` headers that match the attempted request's method and headers
        - `Access-Control-Max-Age` tells the browser how long it can cache the preflight response (by default, 5 seconds)
- improper CORS setup (ex. making allowed origins too wide) can open more vulnerabilities!

## Content-Security-Policy

- a header that servers can send to enable additional restrictions on top of SOP
    - can also be used in a \<meta\> tag, but some functionality isn't available
- can limit where resources can be loaded from and how the page behaves
- policies must include at least `default-src`, which matches all resource types that don't have their own rules set
- the policy below:
    - allows images from anywhere
    - allows media from example.org and example.net
    - allows scripts from userscripts.example.com
    - all other content must be from the same origin (note that this excludes subdomains)

```
Content-Security-Policy: default-src 'self'; img-src *; media-src example.org example.net; script-src userscripts.example.com
```

- for a full list of directives [see here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy#directives)

# See also

- [[Development/Secure development principles\|Secure development principles]]

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
