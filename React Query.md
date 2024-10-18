---
tags:
  - Frontend
---
# Query Basics

- `useQuery` hook:
    - unique key for query
    - function that returns a promise that resolve data or throws error

```JavaScript
const result = useQuery('todos', fetchTodoList)
// 'todos' is key, fetTodoList is function
```

- `result` contains important states:
    - `isLoading` - query is fetching
    - `isError` - error encountered
    - `isSuccess` - query got data
    - `isIdle` - query disabled
- also has:
    - `error`
    - `data`
    - `isFetching`

# Query Keys

- used in caching
- can be anything as long as the object is serializeable

# Query Functions

- any function that returns a `Promise`
- `Promise` must resolve the data or throw and error

# Parallel Queries

- default behaviour

```JavaScript
function App () {
   // The following queries will execute in parallel
   const usersQuery = useQuery('users', fetchUsers)
   const teamsQuery = useQuery('teams', fetchTeams)
   const projectsQuery = useQuery('projects', fetchProjects)
   ...
 }
```

# Dynamic Queries in Parallel (useQueries)

```JavaScript
function App({ users }) {
   const userQueries = useQueries(
     users.map(user => {
       return {
         queryKey: ['user', user.id],
         queryFn: () => fetchUserById(user.id),
       }
     })
   )
 }
```

- `useQueries` takes a callback function that has params for each query

# Dependent (Serial) Queries

- `'projects'` query depend on `useId` which depends on the `'user'` query
- use the `enabled` option

```JavaScript
// Get the user
 const { data: user } = useQuery(['user', email], getUserByEmail)
 
 const userId = user?.id
 
 // Then get the user's projects
 const { isIdle, data: projects } = useQuery(
   ['projects', userId],
   getProjectsByUser,
   {
     // The query will not execute until the userId exists
     enabled: !!userId,
   }
 )
 
 // isIdle will be `true` until `enabled` is true and the query begins to fetch.
 // It will then go to the `isLoading` stage and hopefully the `isSuccess` stage
```

# Window Focus Refetching

- React Query automatically requests fresh data in the background when a use leaves the app and returns to stale data
- can disable globally or per-query using `refetchOnWindowFocus`

```JavaScript
// DISABLED GLOBALLY
 const queryClient = new QueryClient({
   defaultOptions: {
     queries: {
       refetchOnWindowFocus: false,
     },
   },
 })
 
 function App() {
   return <QueryClientProvider client={queryClient}>...</QueryClientProvider>
 }
```

```JavaScript
// DISABLED PER QUERY
useQuery('todos', fetchTodos, { refetchOnWindowFocus: false })
```

- React Query is smart enough to refetch data in the background when the users focus is off the component

# Paginated / Lagged Queries

- include `page` info in query

```JavaScript
const result = useQuery(['projects', page], fetchProjects)
```

- with the above, each new page is treated as a brand new query ⇒ use `keepPreviousData`

```JavaScript
function Todos() {
   const [page, setPage] = React.useState(0)
 
   const fetchProjects = (page = 0) => fetch('/api/projects?page=' + page).then((res) => res.json())
 
   const {
     isLoading,
     isError,
     error,
     data,
     isFetching,
     isPreviousData, // LETS YOU KNOW WHAT DATA THE QUERY IS CURENTLY PROVIDING
   } = useQuery(['projects', page], () => fetchProjects(page), { keepPreviousData : true })
 
   return (
     <div>
       {isLoading ? (
         <div>Loading...</div>
       ) : isError ? (
         <div>Error: {error.message}</div>
       ) : (
         <div>
           {data.projects.map(project => (
             <p key={project.id}>{project.name}</p>
           ))}
         </div>
       )}
       <span>Current Page: {page + 1}</span>
       <button
         onClick={() => setPage(old => Math.max(old - 1, 0))}
         disabled={page === 0}
       >
         Previous Page
       </button>{' '}
       <button
         onClick={() => {
           if (!isPreviousData && data.hasMore) {
             setPage(old => old + 1)
           }
         }}
         // Disable the Next Page button until we know a next page is available
         disabled={isPreviousData || !data?.hasMore}
       >
         Next Page
       </button>
       {isFetching ? <span> Loading...</span> : null}{' '}
     </div>
   )
 }
```

# Prefetching