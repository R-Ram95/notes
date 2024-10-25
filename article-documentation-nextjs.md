# What is Next.js?

[[this-is-a-new-note]]

- React framework for production
- full stack framework for React
- Gives a clear guide on how to structure files and write code
- main goal is to make web applications faster for users??

## Key Features and Benefits

- server-side rendering → prepare content of page on server instead of client, i.e. data is added to content BEFORE being sent to the client
- server-side rendering is better for SEO ⇒ should be used for public pages
- allows us to pre-render react pages and components on the server
- Alternative to server-side rendering is ReactDomServer
- better load performance for users ⇒ they don’t need to wait for data to come to the client to display content
- File-based Routing:
    - Typically in React, routing prevents the browser from sending a request when the url changes and instead renders different content ⇒ change what is shown on the screen without sending extra requests
    - defines pages and routes with files and folders instead of code

# Using Next.js

- **pages directory** - uses files for routing ⇒ files in the pages directory become pages
    - e.g. `pages/posts/first-post.js` matches the `/posts/first-post`
    - `pages/index.js` matches the `/` route
- Client side navigation ⇒ use the `Link` component from `next/link`
- **public directory** - for static assets like images and `robots.txt`

## Code Splitting & Prefetching

- code splitting is automatic in Next.js
- each page only loads what’s required for the page ⇒ when the home page renders, code for other pages is not served
- whenever `Link` components appear, Next.js **prefetches** the code for the linked pages ⇒ by the time the link is clicked, the code for the page is loaded
- use the `<a` tag to link to external links outside the Next.js app

## CSS Modules

- naming convention: `[name].module.css`
- locally scope CSS by automatically creating unique class names ⇒ allows you to use the same CSS class name in different files without worrying about collisions

  

## Pre-rendering

- by default Next.js pre-renders every page
- Next.js generates the HTML for each page in advance vs. it being done by client-side JS
- **Hydration** - JS code runs to make page interactive when browser loads the page
- Two forms: Static Generation and Server-Side-Rendering
    - **Static Generation** - HTML is generated at **build time** and reused at each request
        
        
    - **Server-side Rendering** **- HTML generated at each request**
        
        
- You can choose which type of pre-rendering you use for **each page**
    
    
    

  

## Static Site Generation

# With Data - getStaticProps

- use **async** function `getStaticProps`
    - `getStaticProps` runs at build time
    - only runs on server ⇒ can write queries in `getStaticProps` without it being sent to client
    - cannot use data that is only available during runtime, e.g. query paramters
    - can only be exported from pages
    - must use SSR if data frequently changes
- inside function, fetch external data related to the page

```JavaScript
export default function Home(props) { ... }

export async function getStaticProps() {
  // Get external data from the file system, API, DB, etc.
  const data = ...

  // The value of the `props` key will be
  //  passed to the `Home` component
  return {
    props: ...
  }
}
```

## getStaticPaths()

- returns an object that describes all the possible paths

```JavaScript
export async function getStaticPaths() {
	return {
		fallback: false, // false means it supports all supported ids
		paths: [
			{ 
				params: {
					id: 'id1'
				},
				params: {
					id: 'id2'
				}
			}
		]
	}
}
```

## Server-side Rendering - getServerSideProps

- for fetching data at **request time**
- uses `getServerSideProps`
    
    ```JavaScript
    export async function getServerSideProps(context) {
      return {
        props: {
          // props for your component
        },
      };
    }
    ```
    
    - context contains request params
- runs for **every** incoming request
- `context` object gives access to `request` and `response` objects
- should only use `getServerSideProps` when you **need** access to the request object

## Client-side Rendering

- used if we do not need pre-rendered data
- statically generate parts of page that do not require external data
- when page loads, fetch external data on the client side using JS
    
    
- works well for user dash boards ⇒ private, user-specific page and SEO is not relevant

## Dynamic Routes


  

# Next13

## File-based Routing

NextJS has built-in routing in the form of file-based routing, i.e. routing is on the basis of files and folders.

- main folder is the pages directory ⇒ any file in the pages directory becomes a route
- files named `index` are routed to the root of the directory


- corresponds to `/profile`

Nested routes

- corresponds to `/users/user1` & `/users/users2`
    
    

Dynamic routes ⇒ `/pages/[slug].js`

- corresponds to `/product/product1`, `/product/product2`, etc.
- the `slug` part is dynamic and will render the productid page based on the route

Catch all routes ⇒ `/pages/[...params].js`

- the params will catch the params of any route ⇒ `/pages/page/2/user1`
    - page/2/user1 can be extracted as `params` in an array
    - `userRouter` hook to extract params
        
        ```JavaScript
        import { useRouter } from "next/router";
        
        function Blog() {  
        	const router = useRouter();
          const { params = [] } = router.query;
        }
        ```
        

## Pages

Page - React component in the `pages` directory

- each page is associated with a route based on the files name
- could also use `index` in a named folder to correspond to the route of the folders name

NextJS pre-renders every page ⇒ HTML is generated server-side

- each HTML page has minimal JS code ⇒ when the page is loaded, the JS code runs and makes the page interactive (hydration)
- two types:
    - SSG (build-time) ⇒ HTML can be cached in a CDN
    - SSR(request-time) ⇒

## Rendering ⇒ Server and Client Rendering

- Before React 18, app was rendered using React entirely on the client side
- Next allowed you to break app down into pages which are pre-rendered on the server by generating HTML which is then sent to the client to be hydrated by React
    - this required additional JS to make the initial HTML interactive
- with Server and Client side components ⇒ React can render on both the server and client
- by default, components in the App directory are Server Components ⇒ reduces JS sent to the client

### Static Rendering (SSG and ISR)

Static-Site-Generation

- `getStaticProps` & `getStaticPaths` ⇒ Next12
- default in Next13 ⇒ components are automatically rendered at build time and can be cached in a CDN for quicker access

Incremental Site Regeneration

- Post build-time
- uses the `invalidate` prop for Next12
- export `revalidate=``_timeInSeconds_` ⇒ use `revalidate` prop in `fetch()` ⇒ only used in `page.tsx, layout.tsx`

- both server and client components can be pre-rendered at build time
- can be cached, cache and be revalidated

Client Side ⇒ component HTML and JSON pre-rendered and cached on server ⇒ cached result is sent to client for hydration

Server Side ⇒ component is rendered by React on the server ⇒ payload is used to generate HTML. The rendered payload is also used to hydrate components on the Client

  

# References

> [!info] Getting Started | Next.js  
> The Next.  
> [https://beta.nextjs.org/docs/getting-started](https://beta.nextjs.org/docs/getting-started)
