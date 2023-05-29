---
{"dg-publish":true,"dg-path":"Cheat sheets/Next.js cheat sheet.md","permalink":"/cheat-sheets/next-js-cheat-sheet/","created":"","updated":""}
---


# Data fetching hooks

> [!warning]
> These hooks cannot be used in the `App` component (`_app.js`).

## getStaticProps

- Tells Next to statically render the page with the returned props

```tsx
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```

## getServerSideProps

- Tells Next to pre-render (SSR) the page on each request with the returned props
- Server-side code can be included in this block

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

### Get type for server-side props

```tsx
type Props = InferGetServerSidePropsType<typeof getServerSideProps>
```

## getInitialProps

- Executes server-side on initial load (SSR), and client-side on page changes

# useEffect runs in the browser only

- If using browser-only APIs such as `window`, wrap them in `useEffect` to avoid a mismatch between server and client on hydration

# See also

- [[Development/Cheat sheets/React cheat sheet\|React cheat sheet]]
- [[Development/Cheat sheets/React Native cheat sheet\|React Native cheat sheet]]
