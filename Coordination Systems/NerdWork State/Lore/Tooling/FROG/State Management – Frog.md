 

#  State Management[

](/concepts/state-management#state-management)

State Management in **Frog** looks a bit different to what you might be used to with client-side web frameworks such as: React, Vue, Svelte, etc. The critical difference is that **Frog** has no sense of "reactivity" and "interactivity" as it is purely server-side image rendering.

State Management in **Frog** is achieved through "state derivation", and we can use the `deriveState` function to derive new state based on the previous state.

Essentially, `state = deriveState(previousState)`.

Here's an example of a simple counter:

```
import { Button, Frog } from 'frog'
 
type State = {
  count: number
}
 
export const app = new Frog<{ State: State }>({
  initialState: {
    count: 0
  }
})
 
app.frame('/', (c) => {
  const { buttonValue, deriveState } = c
  const state = deriveState(previousState => {
    if (buttonValue === 'inc') previousState.count++
    if (buttonValue === 'dec') previousState.count--
  })
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Count: {state.count}
      </div>
    ),
    intents: [
      <Button value="inc">Increment</Button>,
      <Button value="dec">Decrement</Button>,
    ]
  })
})
```

**Tip**

`deriveState` can also be asynchronous:

```
app.frame('/', (c) => {
  const { deriveState, frameData } = c  
  const state = await deriveState(async previousState => {
    previousState.count = await fetchCount(frameData.fid)
  })
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Count: {state.count} 
      </div>
    )
  })
})
```

Last updated: 3/20/24, 6:53 PM

[

Browser Redirects

Previousshift←](/concepts/browser-redirects)[

Securing Frames

Nextshift→](/concepts/securing-frames)