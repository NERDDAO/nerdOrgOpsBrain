 
# Routing
 

Overview of Routing in Frog

Similar to `.post`, `.get`, `.put`, etc in other Web Frameworks, **Frog** uses `.frame` to define a new frame route.

Internally, the frame route automatically handles `GET` and `POST` reqs sent by Farcaster clients such as Warpcast to get frame metadata.

## 

Basic[

](/concepts/routing#basic)

```
import { Frog } from 'frog'
 
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

**Tip**

A frame handler can also be asynchronous (ie. `async (c) => { ... }`).

## 

Grouping[

](/concepts/routing#grouping)

You can group routes by using the `app.route` method.

src/index.tsxsrc/example.tsx

src/index.tsx

```
import { Frog } from 'frog'
import { app as exampleApp } from './example'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({/* ... */})
})
 
app.route('/example', exampleApp)
```

## 

Base Path[

](/concepts/routing#base-path)

You can specify a base path for your app with the `basePath` constructor parameter.

src/index.tsx

```
import { Frog } from 'frog'
 
export const app = new Frog({ basePath: '/api' })
```

## 

Path Parameters[

](/concepts/routing#path-parameters)

You can extract path parameters using `context.req.param()`

```
app.frame('/user/:name', (c) => {
  const name = c.req.param('name')
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        gm, {name}
      </div>
    ),
  })
})
```

You can extract all parameters at once:

```
app.frame('/posts/:id/comment/:commentId', (c) => {
  const { id, commentId } = c.req.param()
  // ...
})
```

You can specify optional parameters:

```
Matches `/api/animal` and `/api/animal/:type`app.frame('/api/animal/:type?', (c) => {  const { id, commentId } = c.req.param()
  // ...
})
```

## 

Regular Expressions[

](/concepts/routing#regular-expressions)

```
app.frame('/post/:date{[0-9]+}/:title{[a-z]+}', (c) => {
  const { date, title } = c.req.param()
  // ...
})
```

## 

Hono Routes (GET, POST, etc)[

](/concepts/routing#hono-routes-get-post-etc)

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

Last updated: 3/20/24, 6:53 PM

[

Overview

Previousshift←](/concepts/overview)[

Images & Intents

Nextshift→](/concepts/images-intents)