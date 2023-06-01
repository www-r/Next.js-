# Routing Fundamentals
## terminology 
[](https://nextjs.org/_next/image?url=%2Fdocs%2Fdark%2Fterminology-component-tree.png&w=3840&q=75)
  - Tree: a convention for visualizing a hierarchical structure
  - Subtree : part of a tree, starting at a new root(first) and ending at the leaves(last)
  - Root : the FIRST node in a tree or subtree
  ex) root layout 
  - Leaf : nodes in a subtree that have NO CHILDREN
  ex) the last segment in a URL path
  [](https://nextjs.org/_next/image?url=%2Fdocs%2Fdark%2Fterminology-url-anatomy.png&w=3840&q=75)
  - URL Segment : part of the URL path delimited by slashes
  - URL Path : part of the URL that comes after the domain(composed of segments)

## the app directory 
: the App Router takes priority over the Pages Router; Routes across directories should not resolve to the same URL path

## Roles of Folders and Files
- folders are used to define routes. -> more: ```Defining routes```
- files are used to create UI that is shown for a route segment

## Route Segments
: each folder in a route represents a route segment( each route segment is mapped to a corresponding segment in a URL path)

## Nested Routes
: to create a nested route, nest folders inside each other

## File Conventions ( a set of special files to create UI with specific behaviour in nested routes)
- layout : shared UI
- page : unique UI of a route and make routes publicily accessible
- loading : loading UI
- not-found : not found UI
- error : error UI
- global-error : global error UI
- route : server-side API endpoint
- template : specialized re-rendered Layout UI
- default : fallback UI for ```Parallel Routes```

## Component Hierarchy
```html
<Layout>
  <Template>
    <ErrorBoundary fallback={<Error/>}>
      <Suspense fallback={ <Loading/>}>
        <ErrorBoundary fallback={<NotFound/>}>
          <Page/>
        </ErrorBoundary>
      </Suspense>
    </ErrorBoundary>
  </Template>
</Layout>
```

## Colocation
: option to colocate files inside folders in the app directory
(while folders define routes, **only the contents returned by page.js or route.js are publically addressable**)
[](https://nextjs.org/_next/image?url=%2Fdocs%2Fdark%2Fproject-organization-colocation.png&w=3840&q=75)
-> more: ```Project Organization and Colocation```

## Server-Centric Routing with Client-side Navigation
- unlike the pages directory which uses client-side routing, the App Router uses server-centric routing to align with Server Components and data fetching on the server
=> the client doesn't have to download a route map and the same request
- although routing is sever-centric, the router uses client-side navigation with the <Link/> :resembling the behavior of a SPA 
=> means when a user navigates to a new route, the browser will not reload the page, instead the URL will be updated and Next.js will only render the segments that change
- as users navigate around the app, the router will store the result of the React Server Component payload in an **in-memory client-side cache**. 
=> means the cache of a previously fetched segment can be re-used, further improving performance -> more: ```Linking and Navigating```

## Partial Rendering
- when navigating between sibling routes, next.js will only fetch and render the layouts and pages in routes that change. It will not re-fetch or re-render anything above the segments in the subtree
=> means routes that share a layout will be preserved
=> reduces the amount of data transferred and execution time, leading to improved performance.
[](https://nextjs.org/_next/image?url=%2Fdocs%2Fdark%2Fpartial-rendering.png&w=3840&q=75)


## Advanced Routing Patterns
- ### Parellel Routes : show two or more pages simultaneously in the same view that can be navigated independently ( can use for split views - dashboards)
- ### Intercepting Routes : allow to intercept a route and show it in the context of another route( can use when keeping the context for the current page is important - seeing all tasks while editing one task or expanding a photo in a feed)

# Defining Routes

- Creating Routes
[](https://nextjs.org/_next/image?url=%2Fdocs%2Fdark%2Fdefining-routes.png&w=3840&q=75)
- Creating UI
: special file conventions are used to create UI for each route segment -> ```File Conventions```

# Pages and Layouts

## Pages
Page: UI that is unique to a route, can define pages by exporting a component from a page.js 
- a page is always the leaf of the route subtree
- .js, .jsx, .tsx file extensions can be used for Pages
- is Server Components by default but can be set to a Client Component
- can fetch data

## Layouts
: UI that is shared between multiple pages
- on navigation, preserve state, remain interactive, and do not re-render
- any route segments can **optionally** define its own Layout
- Server Components by default but can be set to a Client Component
- can fetch data
- passing data between a parent layout and its children is not possible, however can fetch same data in a route more than once and React will automatically dedupe the requests without affecting performance
- do not have access to the current route segments. to access route segments, use ```useSelectedLayoutSegment```or ```useSelectedLayoutSegments```in a Client Component

### Root Layout (Required)
- defined at the top level of the app directory
- applies to all routes
- enables to modify the initial HTML returned from the server
- root layout must define <html> and <body> 
- can use the built-in SEO support to manage <head> 
- can use route groups to create multiple root layouts
[](https://nextjs.org/_next/image?url=%2Fdocs%2Fdark%2Froute-group-multiple-root-layouts.png&w=3840&q=75)

### Nesting Layouts
- defined inside a folder 
- nested by default(in the file hierarchy) => means they wrap child layouts via children prop
- can use Route Groups to opt specific route segments in and out of shared layouts

### Templates
- can be defined by exporting template.js
- should accept a children prop
- are similar to layouts(wrap each child layout or page), but templates create a new instance for each of their children on navigation
=> means that when a user navigates between routes that share template, a new instance of the component is mounted, DOM elements are recreated, state is not preserved, and effects are re-synchronized
(unlike layouts persists across routes and maintain state)

- #### cases when templates would be more suitable than layouts (use only when have specific reasons)
  - enter/exit animations using CSS or animation libraries
  - features that rely on useEffect(- logging page views) and useState(-a per-page feedback form)
  - to change the default framework behavior
  (Suspense Boundaries inside layouts only show fallback the first time the Layout is loaded but Template is shown on each navigation)

### Modifying <head>
- **should not manually add <head> such as <title> to root layouts**
- metadata can be defined by exporting a metadata object or ```generateMetadata``` in a layout.js or page.js
```
import { Metadata } from 'next';
 
export const metadata: Metadata = {
  title: 'Next.js',
};
 
export default function Page() {
  return '...';
}
```

# Linking and navigating
# Route Groups
# Dynamic Routes
# Loading UI and Streaming
# Parallel Routes
# Intercepting Routes
# Route Handlers
# Middleware
# Project Organization
# Internationalization