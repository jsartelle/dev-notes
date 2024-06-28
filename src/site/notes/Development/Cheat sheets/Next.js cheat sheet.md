---
{"dg-publish":true,"dg-path":"Cheat sheets/Next.js cheat sheet.md","permalink":"/cheat-sheets/next-js-cheat-sheet/","tags":["language/react"]}
---


# Data fetching

## Static vs. dynamic rendering

- **Static rendering** means the page is rendered to static HTML at build time, so the same page is served to all users.
- **Dynamic rendering** means the page is rendered on the server at the time of each request. This means the page can be customized for the user, and you can access request properties like headers and cookies.

## App Router

- within Server Components, you can use `fetch()` to get data server-side
    - set the `{ cache: 'force-cache' }` option to cache values, or `{ cache: 'no-store' }` to disable caching
        - in Next.js 14 the default is to cache all non-POST requests, but in Next.js 15 the default is not to cache, so be explicit - [more info](https://x.com/leeerob/status/1803824227704877236)
    - use the option `{ next: { revalidate: 3600 } }` to set the cache lifetime (in seconds)
- as of Next.js 15, pages are statically rendered unless the `cookies()`, `headers()`, or `searchParams` are accessed
    - in the future, pages will be able to mix static and dynamic rendering, and will be statically rendered up to a Suspense boundary - [more info](https://x.com/leeerob/status/1803904284213293327)

## Pages Router

> [!warning]
> These hooks cannot be used in the `App` component (`_app.js`).

### getStaticProps

- tells Next to **statically render** the page with the returned props

```tsx
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```

### getServerSideProps

- tells Next to **dynamically render** the page with the returned props

```tsx
// context includes things like req, query, params
export const getServerSideProps = async (context: GetServerSidePropsContext) => {
	return {
		props: {
			name: context.query.name
		},
	}
}

export default function Page({ name }: Props) {
    return (
        <Text>Hello {name}!</Text>
    )
}
```

#### Get type for server-side props

```tsx
type Props = InferGetServerSidePropsType<typeof getServerSideProps>
```

### getInitialProps

- Executes server-side on initial load (SSR), and client-side on page changes

# useEffect runs in the browser only

- If using browser-only APIs such as `window`, wrap them in `useEffect` to avoid a mismatch between server and client on hydration

# See also

- [[Development/Cheat sheets/React cheat sheet\|React cheat sheet]]
- [[Development/Cheat sheets/PicoCSS cheat sheet#Installation with frameworks\|PicoCSS cheat sheet#Installation with frameworks]]
- [Next.js with React Native Web](https://github.com/vercel/next.js/tree/canary/examples/with-react-native-web)
