
# Browser Redirects[

](/concepts/browser-redirects#browser-redirects)

By design, when using the `.frame` API, **Frog** only renders the HTML `<head>` and `<meta>` tags to construct a frame. It does not render a full HTML document to optimize on frame load performance.

This means that when the user navigates to your Frame URL via the browser, the browser will render a blank page.

If you want to render a full HTML document for a Frame, you can use the `browserLocation` property to redirect the user to a new URL that contains the document.

For instance, a user may reach the frame page in their browser by clicking on the link beneath the frame on Warpcast. We may want to redirect them to a different page (ie. a mint page, etc) using `browserLocation` when they arrive via their browser.

## 

Examples[

](/concepts/browser-redirects#examples)

### 

Basic (`Frog` Instance)[

](/concepts/browser-redirects#basic-frog-instance)

In the example below, when a user navigates to the `/frame` path of the website via their web browser, they will be redirected to the `/` path as `browserLocation` is set on the `Frog` instance.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog({
  browserLocation: '/'
})
 
app.frame('/frame', (c) => {
  return c.res({/* ... */})
})
```

### 

Basic (Return Property)[

](/concepts/browser-redirects#basic-return-property)

In the example below, when a user navigates to the `/frame` path of the website via their web browser, they will be redirected to the `/` path as `browserLocation` is set on the return object.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.frame('/frame', (c) => {
  return c.res({
    browserLocation: '/'
    /* ... */
  })
})
```

### 

Path Interpolation[

](/concepts/browser-redirects#path-interpolation)

We may want to redirect the user to a page based on the path.

In the example below, when a user navigates to the `/frame/:foo/:bar` (e.g. `/frame/a/b`) path of the website via their web browser, they will be redirected to the `/:foo/:bar` path (e.g. `/a/b`), provided that the `browserLocation` and the route share the same template variables (e.g. `:foo` and `:bar`).

```
import { Button, Frog } from 'frog'
 
export const app = new Frog({
  browserLocation: '/:foo/:bar'
})
 
app.frame('/frame/:foo/:bar', (c) => {
  return c.res({/* ... */})
})
```

### 

With Base Paths[

](/concepts/browser-redirects#with-base-paths)

`browserLocation` also works with `basePath` on the `Frog` instance.

In the example below, when a user navigates to the `/api/foo` path of the website via their web browser, they will be redirected to the `/foo` path.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog({
  basePath: '/api',
  browserLocation: '/:path',
})
 
app.frame('/foo', (c) => {
  return c.res({/* ... */})
})
 
app.frame('/bar', (c) => {
  return c.res({/* ... */})
})
```

To include the `basePath` in the `browserLocation` path, you can use the `:basePath` template variable.

In the example below, when a user navigates to the `/api/foo` path of the website via their web browser, they will be redirected to the `/api/foo/dev` path.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog({
  basePath: '/api',
  browserLocation: '/:basePath/:path/dev',
})
 
app.frame('/foo', (c) => {
  return c.res({/* ... */})
})
 
app.frame('/bar', (c) => {
  return c.res({/* ... */})
})
```

Last updated: 3/20/24, 6:53 PM

[

Connecting Frames (Actions)

Previousshift←](/concepts/actions)[

State Management

Nextshift→](/concepts/state-management)