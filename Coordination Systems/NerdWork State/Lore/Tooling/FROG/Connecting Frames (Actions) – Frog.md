 
# Connecting Frames (Actions)[

](/concepts/actions#connecting-frames-actions)

An overview of linking frames together

When building Frames, it is likely that you may want to connect them together (via the [Frame Specification's `post_url`](https://docs.farcaster.xyz/reference/frames/spec#optional-properties)).

**Frog** makes it intuitive to connect Frames together with a concept called **Actions**.

## 

Preface[

](/concepts/actions#preface)

Let's start by building a couple of frames below: one that asks the user for their favorite fruit, and another that displays the user's response.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
// Frame to capture user's favorite fruit.
app.frame('/', (c) => {
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Select your favorite fruit:
      </div>
    ),
    intents: [
      <Button value="apple">Apple</Button>,
      <Button value="banana">Banana</Button>,
      <Button value="mango">Mango</Button>
    ]
  })
})
 
// Frame to display user's response.
app.frame('/submit', (c) => {
  const { buttonValue } = c
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Selected: {buttonValue}
      </div>
    )
  })
})
```

Right now, these frames are not linked to each other. This means, that if a user navigates to `/` and interacts with the buttons, the frame will just `POST` to itself instead of `POST`ing to `/submit`.

## 

Frame-level Actions[

](/concepts/actions#frame-level-actions)

To connect these frames together, we can utilize the [`action` property](/concepts/actions) on the handler return type. You can think of the `action` property as an alias for [`post_url` in the Frame Specification](https://docs.farcaster.xyz/reference/frames/spec#optional-properties).

```
// Frame to capture user's favorite fruit.
app.frame('/', (c) => {
  return c.res({
    action: '/submit',
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Select your favorite fruit:
      </div>
    ),
    intents: [
      <Button value="apple">Apple</Button>,
      <Button value="banana">Banana</Button>,
      <Button value="mango">Mango</Button>
    ]
  })
})
 
// Frame to display user's response.
app.frame('/submit', (c) => {
  const { buttonValue } = c
// ...
```

Now, when a user clicks on a button in the first frame, the `POST` request will be sent to `/submit` instead of `/`.

```
// Frame to capture user's favorite fruit.
app.frame('/', (c) => {
  return c.res({
    action: '/submit',
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Select your favorite fruit:
      </div>
    ),
    intents: [
      <Button value="apple">Apple</Button>,
       ^ 1. User clicks.      <Button value="banana">Banana</Button>,      <Button value="mango">Mango</Button>
    ]
  })
})
 
// Frame to display user's response.
app.frame('/submit', (c) => { 
  const { buttonValue } = c
          ^ 2. "Apple"  return c.res({    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Selected: {buttonValue}
      </div>
    )
  })
})
```

As you can see, the example above is another variant of the "Select Fruit" example shown on the [Getting Started](/getting-started) page, except that these frames hold "less" state, yet yield the same behavior.

## 

Button-level Actions[

](/concepts/actions#button-level-actions)

We can also set actions on individual buttons via the `action` prop on `<Button>`. This is useful when you want to link to different frames based on the button clicked.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Select your favorite fruit:
      </div>
    ),
    intents: [
      <Button 
        action="/apples"
        value="red lady"
      >
        Apple
      </Button>,
      <Button 
        action="/banana"
        value="goldfinger"
      >
        Banana
      </Button>,
      <Button 
        action="/mango"
        value="honey gold"
      >
        Mango
      </Button>
    ]
  })
})
 
app.frame('/apples', (c) => {
  // ...
})
 
app.frame('/banana', (c) => {
  // ...
})
 
app.frame('/mango', (c) => {
  // ...
})
```

Last updated: 3/20/24, 6:53 PM

[

Images & Intents

Previousshift←](/concepts/images-intents)[

Browser Redirects

Nextshift→](/concepts/browser-redirects)