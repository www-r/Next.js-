# Server Components
instead of React Rendering whole application client-side, react now gives the flexibility to choose where to render components based on their purpose
[](https://nextjs.org/_next/image?url=%2Fdocs%2Fdark%2Fthinking-in-server-components.png&w=3840&q=75)

- Why Server Components?(Advantages of using them over Client Components)
  - allows developers to better leverage(to get as much advantage or profit as possible) server infrastructure 
  - the initial page load is faster
  - the client-side Javascript bundle size is reduced 
  => the base client-side runtime is cacheable and predictable in size & does not increase as application grows
- all components inside the App Router are Server Components **by default** ( can optionally opt-in to Client by using "use client")

# Client Components
- enable to add client-side interactivity to application
- **is prerendered on the server and hydrated on the client**

## "use client"
- sits between server-only and client code
- is placed at the top of the file(**above imports**)
- once defined => all modules imported into it, including child components are considered part of the client bundle
- doesn't have to be defined in every file => only needs to be defined once, at the "entry point" (because all modules imported into it are considered a Client Component)

# When to use Server & Client Components
|--|Server Component|Client Component|
|--|--|--|
|fetch data|O|X|
|access backend resources(directly)|O|X|
|keep sensitive infos on the server (access tokens, API keys, etc.)|O|X|
|keep large dependencies on the server / reduce client=side javascript|O|X|
|add interactivity(onClick(), etc.)|X|O|
|use State and Lifecycle Effects|X|O|
|use browser-only APIs|X|O|
|use custom hooks that depend on the state, effects, or browser-only APIs|X|O|
|use React Class component|X|O|

- why React Class component?

# Patterns
1. Moving Client Components to the leaves
2. Composing Client and Server Components: can be comnbined in the same component tree: 
  - all Server Components are rendered before sending the result of the client 
  ( this includes Server Components nested inside Client Components)
  - Client Components are rendered in the rendered Server Components
3. Nesting Server Components inside Client Components
  - importing Server Components into Client Components is not supported
  => instead, Pass Server Components to Client Components as **Props**
  => use the React ```children``` prop to create the 'hole'
  => the parent component has no knowledge of what ```children``` is
  => Server & Client Components are decoupled and can be rendered independently
( the very same strategy of 'lifting content up' has been used to avoid state changes in a parent component re-renderring and imported nested child component)
4. Passing props from Server to Client Components(Serialization)
- props passed from the Server to Client Components need to be serialized => values such as functions, Dates, etc, cannot be passed directly to Client Components
5. Keeping Server-Only Code out of Client Components (Poisoning)
```
export async function getData() {
  const res = await fetch('https://external-service.com/data', {
    headers: {
      authorization: process.env.API_KEY,
    },
  });
 
  return res.json();
}
```
=> because the env. variable API_KEY is not prefixed with NEXT_PUBLIC, it's private variable that can only be accessed on the server. Next.js replaces private environmnent variables with the empty string in client code to prevent leaking secure information. 
=>  can use ```server only``` package ( client-only exists can be used to mark modules that contain client-only-code  - for example, code that accesses the window object)

6. Data Fetching
- recommend fetching data in Server Components 

7. Third-party packages
- don't have to wrap most third-party components with "use-client" since it's likely  you'll be using them within Client Components. 
- one exception, is **provider components**, since they rely on React state and context, and are typically needed at the root of an  application.
- can optimize package by using 'using client' deeper in the tree, allowing the imported modules to be part of the Server Component module graph.

# Context
: most react applications rely on context to share data between components, either directly via ```createContext```, or indirectly via ```provider``` components imported from third-party libraries.
=> **context is fully supported within Client Components, but it cannot be created or consumed directly within Server Components**
  1. Using context in Client Components
  All of the context APIs are fully supported within Client Components, but **context providers are typically rendered near the root of an application to share global concerns**. Trying to create a context at the root of application will cause an error. 
  => to fix this, Create context and render its provider inside of a Client Component
  ```
  // app/theme-provider.tsx
  'use client';
  import { createContext } from 'react';
  export const ThemeContext = createContext({});
  export default function ThemeProvider({ children }) {
  return <ThemeContext.Provider value="dark">{children}</ThemeContext.Provider>;
  }
  ```
  ```
    // app/layout.tsx
  import ThemeProvider from './theme-provider';
  export default function RootLayout({
  children,
  }: {
  children: React.ReactNode;
  }) {
  return (
    <html>
      <body>
        <ThemeProvider>{children}</ThemeProvider>
      </body>
    </html>
  );
  }
  ```
  - **should render providers as deep as possible in the tree- notice how ThemeProvider only wraps {children} instead of the entire <html>.**
2. Sharing fetch requests between Server Components
  - recommend colocating data fetching alongside the component that consumes data. fetch requests are automatically deduped in Server Components.  