

Overview[

](/concepts/overview#overview)

High-level overview of the Frog API

**Frog** is built on top of [Hono](https://hono.dev/) (a fast & lightweight Web Framework), extended with a first-class API for Farcaster Frames.

If you are familiar with other Web Frameworks such as [Express](https://expressjs.com/), [Koa](https://koajs.com/), or [Fastify](https://fastify.dev/), then the **Frog** (and Hono) API should seem familiar to you.

At a high-level, the most basic **Frog** application looks like this:

src/index.tsx

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  const { buttonValue, status } = c
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        {status === 'initial' ? (
          'Select your fruit!'
        ) : (
          `Selected: ${buttonValue}`
        )}
      </div>
    ),
    intents: [
      <Button value="apple">Apple</Button>,
      <Button value="banana">Banana</Button>,
      <Button value="mango">Mango</Button>
    ]
  })
})
```

**Tip**

Hover over the variables above to get a feel for the API.

Below, we will break down high-level concepts of the above example.

## 

Imports[

](/concepts/overview#imports)

Before we can instantiate an application, we need to import the `Frog` class.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
// ...
```

**Frog** also exports a set of [Intents](/intents/button) (e.g. `<Button>`, `<TextInput>`, etc).

## 

Instantiation[

](/concepts/overview#instantiation)

We instantiate a new **Frog** application by calling the `Frog` class.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  const { buttonValue, status } = c
  // ...
```

**Why do we export `app`?**

We export `app` so that the [Dev Server](/getting-started#add-scripts-to-packagejson) (`frog dev`) can use it.

You can pass optional [constructor parameters](/reference/frog#constructor-parameters) to `new Frog()` to:

- Set a [Base Path](/reference/frog#basepath) for your application,
- Set a [Redirect URL](/reference/frog#browserlocation) if the user is coming from a browser,
- Set a custom [Hub](/reference/frog#hub),
- Set custom [Hono Options](/reference/frog#honooptions),
- Set [Initial State](/reference/frog#initialstate) for your frame,
- Specify whether or not frames should be [verified](/reference/frog#verify).

## 

Routing[

](/concepts/overview#routing)

Similar to `.post`, `.get`, `.put`, etc in other Web Frameworks, **Frog** uses `.frame` to define a new frame route.

Internally, the frame route automatically handles `GET` and `POST` requests sent by Farcaster clients such as Warpcast to get frame metadata.

```
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({/* ... */})
})
```

**Tip**

A frame handler can also be asynchronous (ie. `async (c) => { ... }`).

You can also define multiple routes by specifying alternative paths.

```
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({/* ... */})
})
 
app.frame('/foo', (c) => {
  return c.res({/* ... */})
})
 
app.frame('/bar', (c) => {
  return c.res({/* ... */})
})
```

You can also define `GET`, `POST`, etc routes via the [Hono](https://hono.dev) instance.

```
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({/* ... */})
})
 
app.hono.get('/healthcheck', (c) => {
  return c.text('ribbit')
})
```

This only scratches the surface of Routing in **Frog**. For more advanced routing, check out the [Routing](/concepts/routing) section.

## 

Context[

](/concepts/overview#context)

The `c` parameter in the frame route contains properties about the frame, such as:

- The [frame data](/reference/frog-frame-context#framedata) (ie. `trustedData`/`untrustedData`),
- The [button index/value](/reference/frog-frame-context#buttonvalue) or [input value](/reference/frog-frame-context#inputvalue) that was interacted with,
- A function to [derive and set state](/reference/frog-frame-context#derivestate),
- The [initial/start path](/reference/frog-frame-context#initialpath) of the frame,
- The [current URL](/reference/frog-frame-context#url) of the frame,
- and [more](/reference/frog-frame-context).

```
export const app = new Frog()
 
app.frame('/', (c) => {
  const { buttonValue, inputText, status, verified } = c
  return c.res({/* ... */})
})
```

[Read more on Context](/reference/frog-frame-context).

## 

Images & Intents[

](/concepts/overview#images--intents)

Farcaster Frames have two main UI components: an **Image**, and a set of **Intents**:

![Images & Intents](https://frog.fm/concepts/overview/images-intents.png)

We can represent this in the **Frog** API via the `image` and `intents` properties of the frame route:

src/index.tsx

```
app.frame('/', (c) => {
  const { buttonValue, status } = c
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        {status === 'initial' ? (
          'Select your fruit!'
        ) : (
          `Selected: ${buttonValue}`
        )}
      </div>
    ),
    intents: [
      <Button value="apple">Apple</Button>,
      <Button value="banana">Banana</Button>,
      <Button value="mango">Mango</Button>
    ]
  })
})
```

[Read more on Images & Intents](/concepts/images-intents).

## 

Dev Server & Devtools[

](/concepts/overview#dev-server--devtools)

To get a live preview of our frames, we can attach the Frog Devtools to our app.

src/index.tsx

```
import { devtools } from 'frog/dev'
import { serveStatic } from 'frog/serve-static'
 
export const app = new Frog()
 
app.frame('/', (c) => { ... })
 
devtools(app, { serveStatic })
```

Then, spin up a local dev server using the `frog dev` command:

```
frog dev
```

This will start a dev server at [`http://localhost:5173`](http://localhost:5173).

Then you can head to the `/dev` route ([`http://localhost:5173/dev`](http://localhost:5173/dev)) to see a live preview (with live reloading) of your frame.

![Frog Devtools](https://frog.fm/concepts/overview/devtools.png)

[Learn more about Frog's built-in devtools](/concepts/devtools).

Last updated: 3/20/24, 6:53 PM

[

Getting Started

Previousshift←](/getting-started)[

Routing

Nextshift→](/concepts/routing)