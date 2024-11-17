---
{"dg-publish":true,"dg-path":"Cheat sheets/React Query cheat sheet.md","permalink":"/cheat-sheets/react-query-cheat-sheet/"}
---


<div class="rich-link-card-container"><a class="rich-link-card" href="https://tanstack.com/query/latest" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://tanstack.com/_build/assets/og-pGgYlhyc.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">TanStack Query</h1>
		<p class="rich-link-card-description">
		Powerful asynchronous state management, server-state utilities and data fetching. Fetch, cache, update, and wrangle all forms of async data in your TS/JS, React, Vue, Solid, Svelte & Angular applications all without touching any "global state"
		</p>
		<p class="rich-link-href">
		https://tanstack.com/query/latest
		</p>
	</div>
</a></div>

- helps with fetching, caching, synchronizing, and updating remote data (server state)
    - can also be used for async data fetching that doesn't involve the network, such as AsyncStorage
- can be used alongside a client-side state manager like Redux or [[Development/Cheat sheets/MobX cheat sheet\|MobX]], but if most of your state is asynchronous data from a server, it's simpler to just use React Query and [[Development/Cheat sheets/React cheat sheet#Context\|Context]]

# Queries

- queries run in parallel
- query results are cached based on the query key
    - if two components both use a query with the same key:
        - the second component to mount will immediately receive the cached data
        - if the data is stale, a fetch will happen to update the data
        - once the data is updated, both components will re-render with the new data
- by default:
    - queries will retry up to 3 times if they fail
    - cached data is always considered stale (`staleTime === 0`)
    - stale queries are refreshed when:
        - a new instance of the query happens
        - the window is refocused
        - the network is reconnected

```tsx
import { useQuery } from '@tanstack/react-query'

function App() {
  const allTodos = useQuery({ queryKey: ['todos'], queryFn: fetchTodoList })

  const todoNumberFive = useQuery({ queryKey: ['todo', { id: 5 }], queryFn: fetchTodoById)
}

async function fetchTodoById({ queryKey }) {
    const [_key, { id }] = queryKey
    const response = await fetch(`/todos/${id}`)
    if (!response.ok) {
        /* need to manually throw if the request errors */
        throw new Error('Failed to fetch todo')
    }
    return response.json()
}
```

- if you need to execute a dynamic number of queries (ex. one for each user, when the number of users can change), you can use the `useQueries` helper, which accepts an array of [[Development/Cheat sheets/React Query cheat sheet#Query options\|queryOptions]] objects, and returns an array of [[Development/Cheat sheets/React Query cheat sheet#Query results\|query results]]

```tsx
function App({ users }) {
  const userQueries = useQueries({
    queries: users.map((user) => {
      return {
        queryKey: ['user', user.id],
        queryFn: () => fetchUserById(user.id),
      }
    }),
  })
}

```

- ongoing fetches can be cancelled manually (ex. by a button press)

```tsx
queryClient.cancelQueries({ queryKey: ['todos'] })
```

# Query options

- `queryKey`: a unique way to identify the data that the query fetches
    - the query key is an array that can contain strings, IDs, or objects with additional options
    - any variables the query function uses to fetch the data, such as an ID, should be part of the query key
    - pagination can be achieved by including the page in the query key, but since changing the query key results in a brand new query, this will cause the query to flip back from `success` to `pending` - this can be fixed using `placeholderData` (see below)
- `queryFn`: a function that returns a promise with the data or an error
    - remember that `fetch` doesn't throw on request errors, so you'll have to manually throw
    - the query function is passed a context object with the following keys:
        - `queryKey`
        - `signal`: an [AbortSignal](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal) that can be used to cancel the query
        - `meta`: whatever data was passed into `queryOptions.meta
- `enabled`: whether the query is ready to run
    - this can be used to make a query dependent on another query finishing, though it's better to change the backend so the queries can be done in parallel if possible
- `initialData`: provide initial data for the query, which will be cached as if it had been fetched
    - can provide a function (which will be called exactly once) if the data is expensive to calculate
- `initialDataUpdatedAt`: JS timestamp (milliseconds since 1/1/1970) when the initial data was updated, used with `staleTime` to calculate if the initial data is fresh
    - if omitted, the data is treated as if it was just fetched
- `placeholderData`: same as `initialData` but not persisted to the cache, useful for fake placeholder data
    - can also pass a function of the form `(previousData, previousQuery) => data`
        - this is useful for pagination, to keep showing the previous data while the new page of data loads - React Query exports a function called `keepPreviousData` that simply does `(previousData) => previousData`
- `staleTime`: how long (in ms) the data is considered "fresh" (defaults to 0)
- `gcTime`: how long (in ms) to hold on to unused cache data before deleting it (default 5 minutes)
    - if within this time, another query with a key matching the cached data appears, it can use the cached data
- `refetchInterval`: interval (in ms) to refetch the query data
    - or a function, `(query) => number`
- `refetchIntervalInBackground`: if true, the refetchInterval will apply to backgrounded apps too
- `refetchOnMount`, `refetchOnWindowFocus`, `refetchOnReconnect`: whether to refetch when the given event happens
    - `true`: refetches if the data is stale
    - `false`: doesn't refetch
    - `'always'`: refetches regardless of whether the data is stale
    - `(query) => boolean | 'always'`: calculate based on the query
- `networkMode`:
    - `online`: only fetches if you have a network connection
        - if you lose connection while the fetch happens, it will pause retries until you come back online
    - `offlineFirst`: will run the query function once if you're offline, but pause retries
        - useful if you have a service worker that intercepts requests for caching, or if you use HTTP caching - if the cache returns a result the fetch will succeed, but if there is a cache miss it won't keep retrying
    - `always`: will always fetch, so `fetchStatus` will never be `paused`
        - useful if you aren't actually using the network, and are just fetching from ex. AsyncStorage
- `retry`: adjust the retry logic on query failures
    - `number`: retry that many times (default 3)
    - `false`: don't retry
    - `true`: retry infinitely
    - `(failureCount, error) => boolean`: custom retry logic
- `retryDelay`: delay between retries
    - `number`: number of milliseconds
    - `(attemptIndex) => number`: lets you ramp up the time between retries
        - the default is to start at 1000ms and double after each retry, but not exceed 30s total
- `refetchOnWindowFocus`: by default, if the user leaves and returns to your app and a query is stale, the data refreshes in the background, set this to `false` to disable that
- `meta`: an object with additional information that is passed around with the query (ex. in the query function context)
- `select`: can be used to pull out only a portion of the result data, so your component doesn't re-render if data that it doesn't use changes
    - `(data) => any`
    - `select` will re-run if the data changes or ==if the select function itself changes==, so declare it outside the component or wrap it in [[Development/Cheat sheets/React cheat sheet#useCallback\|useCallback]]

---

- #todo default options
- you can use the `queryOptions` helper to type query options objects

```tsx
import { queryOptions } from '@tanstack/react-query'

function groupOptions(id: number) {
  return queryOptions({
    queryKey: ['groups', id],
    queryFn: () => fetchGroups(id),
    staleTime: 5 * 1000,
  })
}

// usage:

useQuery(groupOptions(1))
useSuspenseQuery(groupOptions(5))
useQueries({
  queries: [groupOptions(1), groupOptions(2)],
})
queryClient.prefetchQuery(groupOptions(23))
queryClient.setQueryData(groupOptions(42).queryKey, newGroups)
```

# Query results

- `status`: whether we have any **data** - `'pending' | 'error' | 'success'`
- `fetchStatus`: whether the **queryFn** is running - `'fetching' | 'paused' | 'idle'`
    - for example, `status === 'success' && fetchStatus === 'fetching'` means the query is re-fetching in the background
    - `paused` means the query wants to fetch but can't, ex. if there's no network connection
- `isLoading`: equal to `isPending && isFetching`, can be used to show a loading spinner only when a query fetches for the first time (useful if the query starts disabled)
- `isRefetching`: equal to `!isPending && isFetching`, true if the query is fetching but not for the first time
- `data`: if `status === 'success'`, the data
- `dataUpdatedAt`: the timestamp of the last successful fetch
- `isStale`: whether the data is stale
- `isPlaceholderData`: true if the data came from the `placeholderData` query option
- `error`: if `status === 'error'`, the error object
- `errorUpdatedAt`: the timestamp of the last error
- `failureCount`: number of failures/retries, reset to 0 when the query succeeds
- `failureReason`: reason for the last query retry
- `refetch`: function to manually refetch the query
    - takes an options object with these options:
        - `throwOnError`:
            - `false` (default): errors are just logged
            - `true`: throw on errors
        - `cancelRefetch`:
            - `true` (default): cancel the current fetch before triggering the new fetch
            - `false`: if there is a fetch currently running, don't run the new fetch

# Query invalidation

- queries can be invalidated (marked as stale) manually

```tsx
// Invalidate every query in the cache
queryClient.invalidateQueries()
// Invalidate every query with a key that starts with `todos`
queryClient.invalidateQueries({ queryKey: ['todos'] })
```

# Update the cache manually

- if the given data is `undefined`, the cache won't be updated

```tsx
queryClient.setQueryData(['todo', { id: 5 }], data)

// partially update the old data
queryClient.setQueryData(['todo', { id: 5 }], (oldData) => ({ ...oldData, complete: true }))
```

# Infinite queries

- *infinite queries* can load more data incrementally, ex. for infinite scrolling
- when the query is refreshed, each page of data is fetched sequentially
- there can only be a single fetch happening at a time, so calling `fetchNextPage` while a fetch is running can overwrite a background data refresh
- the `maxPages` option can limit how many pages are kept in cache at once

```tsx
import { useInfiniteQuery } from '@tanstack/react-query'

function Projects() {
  const fetchProjects = async ({ pageParam }) => {
    const res = await fetch('/api/projects?cursor=' + pageParam)
    return res.json()
  }

  const {
    data,
    error,
    fetchNextPage,
    hasNextPage,
    isFetching,
    isFetchingNextPage,
    status,
  } = useInfiniteQuery({
    queryKey: ['projects'],
    queryFn: fetchProjects,
    initialPageParam: 0,
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
  })

  return status === 'pending' ? (
    <p>Loading...</p>
  ) : status === 'error' ? (
    <p>Error: {error.message}</p>
  ) : (
    <>
      {data.pages.map((group, i) => (
        <React.Fragment key={i}>
          {group.data.map((project) => (
            <p key={project.id}>{project.name}</p>
          ))}
        </React.Fragment>
      ))}
      <div>
        <button
          onClick={() => fetchNextPage()}
          disabled={!hasNextPage || isFetchingNextPage}
        >
          {isFetchingNextPage
            ? 'Loading more...'
            : hasNextPage
              ? 'Load More'
              : 'Nothing more to load'}
        </button>
      </div>
      <div>{isFetching && !isFetchingNextPage ? 'Fetching...' : null}</div>
    </>
  )
}
```

- for APIs that don't return a cursor, `getNextPageParam`/`getPreviousPageParam` also receive the current page's param

```tsx
return useInfiniteQuery({
  queryKey: ['projects'],
  queryFn: fetchProjects,
  initialPageParam: 0,
  getNextPageParam: (lastPage, allPages, lastPageParam) => {
    if (lastPage.length === 0) {
      return undefined
    }
    return lastPageParam + 1
  },
  getPreviousPageParam: (firstPage, allPages, firstPageParam) => {
    if (firstPageParam <= 1) {
      return undefined
    }
    return firstPageParam - 1
  },
})

```

# Suspense

- `useSuspenseQuery`, `useSuspenseInfiniteQuery`, `useSuspenseQueries` provide Suspense-enabled queries
- instead of the `status` and `error` objects, [[Development/Cheat sheets/React cheat sheet#Suspense\|Suspense]] and [[Development/Cheat sheets/React cheat sheet#Error Boundaries\|Error Boundaries]] will be used
- all Suspense queries inside the same component are fetched in serial
- `placeholderData` can't be used - to avoid the Suspense fallback being shown if the query key changes (ex. during pagination), wrap updates that change the query key in [[Development/Cheat sheets/React cheat sheet#startTransition and useTransition\|startTransition]]
- errors are only thrown if there is no data in the cache, so if you want to error even if a successful fetch has happened, you'll need to check `error && !isFetching` and throw an error manually
- to retry failed suspense queries, use the `QueryErrorResetBoundary` component and `useQueryErrorResetBoundary` hook
    - `useQueryErrorResetBoundary` resets any query errors within the nearest `QueryErrorResetBoundary` component, or globally if there are no boundaries

```tsx
import { QueryErrorResetBoundary } from '@tanstack/react-query'
import { ErrorBoundary } from 'react-error-boundary'

const App = () => (
  <QueryErrorResetBoundary>
    {({ reset }) => (
      <ErrorBoundary
        onReset={reset}
        fallbackRender={({ resetErrorBoundary }) => (
          <div>
            There was an error!
            <Button onClick={() => resetErrorBoundary()}>Try again</Button>
          </div>
        )}
      >
        <Page />
      </ErrorBoundary>
    )}
  </QueryErrorResetBoundary>
)

```

- **experimental**: [fetch data on the server (in client components) using useSuspenseQuery](https://tanstack.com/query/latest/docs/framework/react/guides/suspense#suspense-on-the-server-with-streaming)

# Mutations

- used to create/update/delete data

```tsx
function App() {
  const mutation = useMutation({
    mutationFn: (newTodo) => {
      return axios.post('/todos', newTodo)
    },
  })

  return (
    <div>
      {mutation.isPending ? (
        'Adding todo...'
      ) : (
        <>
          {mutation.isError ? (
            <div>An error occurred: {mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>Todo added!</div> : null}

          <button
            onClick={() => {
              mutation.mutate({ id: new Date(), title: 'Do Laundry' })
            }}
          >
            Create Todo
          </button>
        </>
      )}
    </div>
  )
}
```

- mutations do not retry by default, but you can pass `retry` and `retryDelay` options just like [[Development/Cheat sheets/React Query cheat sheet#Query options\|queries]]
- if `throwOnError` is set to true (or a function that returns true), mutation errors will be thrown and propagate to the nearest [[Development/Cheat sheets/React cheat sheet#Error Boundaries\|Error Boundary]]
- by default all mutations run in parallel, even multiple calls to the same mutation
    - you can pass a `scope` object with an `id` property, and all mutations with the same scope will run in serial
    - ex. `useMutation({ mutationFn: addTodo, scope: { id: 'todo' } })`
- `useMutation` returns an object with the following properties:
    - `status`: `'idle' | 'pending' | 'error' | 'success'`
        - `pending` means the mutation is currently running
    - `submittedAt`
    - `data`
    - `error`
    - `failureCount`
    - `failureReason`
    - `reset`: a function that clears `error` and `data`
    - `mutate`: function to trigger the mutation, can accept a single variable
    - `mutateAsync`: same as `mutate`, but returns a promise that resolves with the data on success, and rejects on error
    - `variables`: the argument passed to `mutate/mutateAsync`
        - can be used along with the `status` field to show data optimistically
- pass the following event handlers to `useMutation` or `mutate/mutateAsync:
    - `onMutate`: mutation is about to happen
    - `onError`
    - `onSuccess`: useful to [[Development/Cheat sheets/React Query cheat sheet#Query invalidation\|invalidate related queries]], or [[Development/Cheat sheets/React Query cheat sheet#Update the cache manually\|update the cache]] on updates
    - `onSettled`: runs on error or success
