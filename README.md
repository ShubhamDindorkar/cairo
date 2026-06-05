# Cairo

A fresh desktop application project using Tauri, Next.js.

## ✨ Key Features
Cairo integrates community-endorsed best practices with powerful tooling out of the box:

### Code Quality Assurance
- Community-recommended ESLint configuration for Next.js projects
- Rust best practices enforced through Clippy linter for Tauri

### Tauri
Tauri is great to make secure cross platform application backed by Rust!
It will load an HTML page inside a Webview and give the ability to do system call with IPC.
If you are familiar with electron or nextron you can see it as a very good replacement with smaller bundle size, smaller memory usage and more secure.

That make Next.js the perfect fit for bundle React application with Tauri since it comes with great Static-Site Generation SSG capability that will allow us to generates static files that will be included in the final binary.

The benefit of using Next.js SSG mode is pre-rendered React code in static HTML/JavaScript.
This means your app will load faster.
React doesn't have to render the HTML on the client-side but will hydrate it on the first load if needed.

The downside is that we cannot use `getServerSideProps` or use any type of data fetching for rendering our page for a request.
Instead we will use `getStaticProps` to generate our page at build time.

Note that if you still want the power of Rust for generate your page you may have a look at Neon.
It will allow you to call Rust code from Node.js!

## 📦 Installation
Be sure you have NodeJS and Rust installed on your system.

See [Tauri prerequisites](https://tauri.app/v1/guides/getting-started/prerequisites) to prepare your system to build Tauri.

Clone or fork this repository:

```bash
git clone https://github.com/shubhamdindorkar/cairo.git
cd cairo
```

Install node dependencies:

```bash
npm install
```

## 🎨 Developing
To get started you only need one command:

```bash
npm run dev
```

This will start both Tauri and Next.js in development mode.

Note that tauri is waiting for an http server to be alive on `localhost:3000`.
It's the default Next.js port while running in development.

You can modify the port by updating `src-tauri/tauri.conf.json`.

```json
"beforeDevCommand": "npm run next dev -- -p 8080",
"devUrl": "http://localhost:8080"
```

### Source structure
- `src-next/` are where Next.js files are located.
- `src-tauri/` contain Tauri source files.



## ⚡ Production
To build in production you can do it in a single command. This will build and export Next.js and build Tauri for your current environnement.

```bash
npm run tauri build
```

Look into `src-tauri/tauri.conf.json` to tweak the settings, and refer to Tauri building documentation for more information.

## ⚠️ Warning
If you are new to Next.js beware when working with it in development!
It will start a Nodejs server in background in order to have HMR (Hot Module Replacement) capability but also SSR (Server Side Rendering). That mean your React/Typescript code have two execution context:

### On the Server
Code is executed by Node.js runtime.
There is no notion of window or navigator it's part of Browser API.
You cannot call Tauri API in this context since Tauri injection happen in the Browser side.

### On the Browser
Code is executed by the Tauri Webview.
Tauri API will work fine and any other Browser API package `d3.js` for example.

Note that your production code will alway be running in a Browser side context.
Since we use the SSG feature from Next.js no Node.js server will be packaged in production.

### `ReferenceError: navigator is not defined`
This error can occur when importing `@tauri-apps/api` for example.
There is 2 workarounds that you can use:

#### Dynamic component method

Create your component:

```tsx
import React from 'react'
import { window } from '@tauri-apps/api';

const { appWindow } = window;

export default function MyComponent() {
  return (
    <div>
      {appWindow.label}
    </div>
  )
}
```

Import your component:

```tsx
import dynamic from "next/dynamic";

const MyComponent = dynamic(() => import("./path/to/my/component"), {
  ssr: false,
});
```

#### Is browser method

```tsx
import { invoke } from '@tauri-apps/api/core'

const isBrowser = typeof window !== 'undefined'

if (isBrowser) {
  /// Code will only execute on browser side
}
```

In general to safely invoke Tauri API you should use it in `componentDidMount`, `useEffect` or on user based events that will be alway executed in client side.

## 📚 Documentation
To learn more about Tauri and Next.js, take a look at the following resources:

- [Tauri Guides](https://tauri.app/v1/guides/) - guide about Tauri.
- [Tauri API](https://tauri.app/v1/api/js/) - discover javascript Tauri api.
- [Next.js Documentation](https://nextjs.org/docs) - learn more about Next.js.
- [Next.js Tutorial](https://nextjs.org/learn) - interactive Next.js tutorial.
