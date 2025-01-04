---
{"dg-publish":true,"dg-path":"Notes/Notes/GraphQL.md","permalink":"/notes/notes/graph-ql/"}
---


<div class="rich-link-card-container"><a class="rich-link-card" href="https://graphql.org/learn/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://graphql.org/img/og-image.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Introduction to GraphQL | GraphQL</h1>
		<p class="rich-link-card-description"></p>
		<p class="rich-link-href">
		https://graphql.org/learn/
		</p>
	</div>
</a></div>

# General

- served from a single endpoint (unlike REST APIs)
- the shape of the result mirrors the shape of the query
- only returns the fields you explicitly request, so no versioning is needed

# Types

- define types with fields
    - supported scalar types: `Int`, `Float`, `String`, `Boolean`, `ID` (a string that is unique per object)
        - implementations can add custom scalar types as well
    - all fields default to nullable

```graphql
type Query {
  me: User
}
 
type User {
  id: ID
  name: String
}

enum Episode {
  NEWHOPE
  EMPIRE
  JEDI
}

type Character {
  # ! means the field is non-nullable
  name: String!
  # this means a non-nullable array of Episode objects
  # which are also non-nullable (so the array can't contain nulls)
  appearsIn: [Episode!]!
  # this means the `friends` array can be null, but it can't contain
  # null values
  friends: [String!]
}

type Starship {
  id: ID!
  name: String!
  # fields can have arguments, optionally with default values
  length(unit: LengthUnit = METER): Float
}
```

## Interfaces

- you can define interfaces and have types implement them, and create fields that return any type which implements that interface

```graphql
interface Character {
  # any type that implements Character must have at least these fields
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
}

type Human implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  starships: [Starship]
  totalCredits: Int
}
 
type Droid implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  primaryFunction: String
}
```

## Union types

- similar to interfaces, but don't declare shared fields
    - this means you need to use a [[Development/Notes/GraphQL#Inline fragments\|inline fragment]] to access any fields on the union type

```graphql
union SearchResult = Human | Droid | Starship
```

## `Query` and `Mutation` types

- every GraphQL service has a `Query` type, which defines the entry point (or root) of every query

```
type Query {
  hero(episode: Episode): Character
  droid(id: ID!): Droid
}

query {
  # these are pulled from the type `Query`
  hero {
    name
  }
  droid(id: "2000") {
    name
  }
}
```

- there is an optional `Mutation` type that works the same way for [[Development/Notes/GraphQL#Mutations\|#Mutations]]

## Input types

- used to define the data passed into [[Development/Notes/GraphQL#Mutations\|#Mutations]]
- can't have arguments on their fields
- can't mix input and output types in the same schema, but input type fields can refer to other input types

```graphql
input ReviewInput {
  stars: Int!
  commentary: String
}
```

# Queries

- when a query is made, the GraphQL service on the server calls the functions to get the requested fields

```graphql
{
  me {
    name
  }
}
```

- result:

```json
{
  "me": {
    "name": "Luke Skywalker"
  }
}
```

- the above syntax is a shorthand for queries, the full syntax includes a keyword (*query*, *mutation*, or *subscription*) and a name for logging

```
query YourName {
  me {
    name
  }
}
```

- if a field refers to an object or collection of objects, the query can drill into those child objects
    - there's no way to pass a wildcard and get all the fields of an object, because that could lead to circular references

```graphql
{
  hero {
    name
    # `friends` is an array of objects, and we want the `name` field
    # from each of those objects
    friends {
      name
    }
  }
}
```

```json
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

- fields can take arguments
    - this includes scalar fields, so data transformation can be done on the server

```graphql
{
  human(id: "1000") {
    name
    height(unit: FOOT)
  }
}
```

# Aliases

- you can rename fields in the result, which lets you query the same field with different arguments
    - the format is `alias: fieldName`

```graphql
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}
```

```json
{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    },
    "jediHero": {
      "name": "R2-D2"
    }
  }
}
```

# Fragments

- you can create reusable fragments to avoid having to duplicate parts of queries
    - fragments can access [[Development/Notes/GraphQL#Variables\|#Variables]] declared in the query or mutation

```graphql
# `hero` is of type Character
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  appearsIn
  friends {
    name
  }
}
```

## Inline fragments

- types can be union types - in the below example, `hero` can be a `Human` or `Droid` type
- you can use inline fragments to access different data depending on the concrete type
    - you can also use a named fragment, since they have types attached

```graphql
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    ... on Droid {
      primaryFunction
    }
    ... on Human {
      height
    }
  }
}
```

- this is the same as

```graphql
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    ...droidFunction
    ...humanHeight
  }
}

fragment droidFunction on Droid {
    primaryFunction
}

fragment humanHeight on Human {
    height
}
```

- if the types share a common interface, you can create an inline fragment for that interface

```graphql
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    # Human and Droid both implement the Character interface
    ... on Character {
      friends
    }
  }
}
```

# Variables

- let you pass arguments separately so you don't need to modify the GraphQL query in your client-side code (like SQL query parameters)
- variables are declared as arguments to the query
    - variables are optional by default, required variables are marked with `!` after the type
    - variables can have default values

```graphql
query HeroNameAndFriends($episode: Episode = JEDI) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

```json
{
  "episode": "JEDI"
}
```

# Directives

- let you add conditional logic to queries
    - implementations can add their own directives with additional functionality

```graphql
query Hero($episode: Episode, $withFriends: Boolean!, $noHeight: Boolean) {
  hero(episode: $episode) {
    name
    # only include if $withFriends is true
    friends @include(if: $withFriends) {
      name
    }
    # don't include if $noHeight is true
    height @skip(if: $noHeight)
  }
}
```

# Mutations

- used with [[Development/Notes/GraphQL#Variables\|#Variables]] to mutate data, and let you define what data is returned
- ==query fields run in parallel, but mutation fields run in series== (to avoid race conditions)

```graphql
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
}
```

```json
{
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie!"
  }
}
```

- result:

```json
{
  "data": {
    "createReview": {
      "stars": 5,
      "commentary": "This is a great movie!"
    }
  }
}
```

# Meta fields

## `__typename`

- returns the name of the object type at that point, so you can tell different types apart on the client

```graphql
{
  search(text: "an") {
    __typename
    ... on Human {
      name
    }
    ... on Droid {
      name
    }
    ... on Starship {
      name
    }
  }
}
```

```json
{
  "data": {
    "search": [
      {
        "__typename": "Human",
        "name": "Han Solo"
      },
      {
        "__typename": "Human",
        "name": "Leia Organa"
      },
      {
        "__typename": "Starship",
        "name": "TIE Advanced x1"
      }
    ]
  }
}
```

## `__schema`

- when used at the root, returns a representation of the query schema
    - `types` lists all the available types (see [[#`__type`]])
    - the `queryType` field represents the root [[#`Query` and `Mutation` types|Query type]]

```graphql
{
  __schema {
    types {
      name
    }
  }
}
```

## `__type`

- get information on a specific type
    - `name`: the type name
    - `kind`: the kind (`OBJECT`, `INTERFACE`, etc.)
    - `fields`: each of the fields on the type
    - `ofType`: meta info for wrapper types like `ID` and lists
    - `description`: a description for documentation

```graphql
{
  __type(name: "Droid") {
    name
    fields {
      name
      type {
        name
        kind
        ofType {
          name
          kind
        }
      }
    }
  }
}
```

## Others

- `__TypeKind`
- `__Field`
- `__InputValue`
- `__EnumValue`
- `__Directive`

# Resolver functions

- every field on every type has a function (called a *resolver*) on the server which is called to get the value
    - resolvers can be asynchronous, and GraphQL will wait for all Promises to resolve before returning
    - most GraphQL libraries will let you omit the resolver if it simply returns a property of the same name as the field

```js
Query: {
  human(obj, args, context, info) {
    return context.db.loadHumanByID(args.id).then(
      userData => new Human(userData)
    )
  }
}
```

- resolvers take four arguments:
    - `obj`: the previous object, often not used for fields on the root [[#`Query` and `Mutation` types|Query type]]
    - `args`: the arguments for the current field
    - `context`: holds context info like the currently logged in user, or a database connection
    - `info`: holds field-specific information and schema details

# Tools

<div class="rich-link-card-container"><a class="rich-link-card" href="https://graphql.org/graphql-js/running-an-express-graphql-server/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://graphql.org/img/og-image.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Running an Express GraphQL Server | GraphQL</h1>
		<p class="rich-link-card-description">

		</p>
		<p class="rich-link-href">
		https://graphql.org/graphql-js/running-an-express-graphql-server/
		</p>
	</div>
</a></div>

- Express server setup, including a browser-based GraphQL IDE/explorer

<div class="rich-link-card-container"><a class="rich-link-card" href="https://relay.dev/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://relay.dev/img/relay.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Relay</h1>
		<p class="rich-link-card-description">

		</p>
		<p class="rich-link-href">
		https://relay.dev/
		</p>
	</div>
</a></div>

- React client
