---
{"dg-publish":true,"dg-path":"Cheat sheets/Prisma cheat sheet.md","permalink":"/cheat-sheets/prisma-cheat-sheet/","tags":["tech/databases"]}
---


# Setup with Next.js

- Create a Next.js project and publish it to Vercel
- In Vercel, select the Storage tab and create a Postgres database
- Run `npm install -g vercel@latest` to install the Vercel CLI globally
- Run `vercel env pull .env` to pull the database credentials into a *.env* file
    - Make sure to add *.env* to *.gitignore*
- Run `npx prisma init` to create a Prisma schema file (in */prisma*)
    - This will add an example database URL to *.env*, it's safe to delete it
- Edit *schema.prisma* to look like this:

```json
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url = env("POSTGRES_PRISMA_URL") // uses connection pooling
  directUrl = env("POSTGRES_URL_NON_POOLING") // uses a direct connection
}
```

- Add one or more models to *schema.prisma* - [see here](https://www.prisma.io/docs/orm/prisma-schema/data-model/models#relation-fields) for examples
- Run `npx prisma migrate dev --name init` to create the initial migration and run it against the database
- Run `npm install @prisma/client` to install the Prisma JS client
- ==Each time you update the schema in development==, update the database using one of these commands:
    - `npx prisma db push`: updates without using migrations - should only be used when iterating on schema changes locally, as it **can lead to data loss**
    - `npx prisma migrate dev`: creates a migration
- Run `npx prisma studio` to open the Prisma dashboard and view and add records
- [See here](https://www.prisma.io/docs/orm/prisma-client/queries/crud) for query examples
