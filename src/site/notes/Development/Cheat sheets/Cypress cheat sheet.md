---
{"dg-publish":true,"dg-path":"Cheat sheets/Cypress cheat sheet.md","permalink":"/cheat-sheets/cypress-cheat-sheet/","tags":["tech/testing","language/javascript"]}
---


- pass environment variables using the `e2e.env` config key, and access them using `Cypress.env('key')`
- if testing across origins (for example, with a redirect), wrap any code that needs to access the secondary origin in `cy.origin`
    - all arguments must be passed into the callback manually using the `args` option

```ts
it('should redirect to the login page on another origin`, () => {
   // base url http://example.com/
    cy.visit('/login')

    const loginDomain = Cypress.env('AUTH_DOMAIN')

    cy.origin(loginDomain, { args: { loginDomain } }, ({ loginDomain }) => {
        cy.url().should('include', loginDomain)
    })
})
```

- use the [start-server-and-test](https://www.npmjs.com/package/start-server-and-test) package to start the dev server and run tests in one command
