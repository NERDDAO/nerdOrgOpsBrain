 
# Securing Frames[

](/concepts/securing-frames#securing-frames)

There are a couple of things you can do to secure frames:

1. [Frame Data Verification](/concepts/securing-frames#frame-data-verification)
2. [Signing State](/concepts/securing-frames#signing-state)

## 

Frame Data Verification[

](/concepts/securing-frames#frame-data-verification)

By default, frame verification is **turned on** provided you have supplied a [Hub to the `Frog` instance](/concepts/securing-frames#supplying-hub-configuration).

When a frame responds to a `POST` request from a Farcaster Client, we may want to verify the body of the request against a Farcaster Hub to make sure that the contents of the `frameData` (such as `buttonIndex`, `fid`, `castId`, etc) legitimately belong to the user that interacted with it.

```
app.frame('/', (c) => {
  const { frameData } = c
  const { buttonIndex, fid, castId } = frameData
  return c.res({/* .... */})
})
```

The body of a `POST` request contains a **signed message** (`trustedData`), and an **unsigned message** (`untrustedData`), which is eventually converted to the `frameData` object in context. This may contain things like:

- The index of the button clicked (`buttonIndex`)
- Value of the text input (`inputText`)
- The user's FID (`fid`)

To verify frames with **Frog**, we can utilize the `verify` flag on either the `Frog` instance, or the frame return object.

### 

Supplying Hub Configuration[

](/concepts/securing-frames#supplying-hub-configuration)

We can set Hub configuration to use for verification by providing it to the `Frog` instance.

Frog currently supports [`neynar`](/hubs/neynar) as a built-in Hub.

```
import { Frog } from 'frog'
import { neynar } from 'frog/hubs'
 
export const app = new Frog({
  hub: neynar({ apiKey: 'NEYNAR_FROG_FM' })
})
```

Feel free to use our Neynar API Key: `"NEYNAR_FROG_FM"`.

However, please note that this API Key is for development purposes only – it is prone to rate-limiting. It is recommended to use your own API Key in production. [See more](https://neynar.com/#get-started).

You can also supply your own Hub API URL:

```
import { Frog } from 'frog'
 
export const app = new Frog({
  hub: { apiUrl: 'https://myhub.com' }
})
```

### 

Silent Verification[

](/concepts/securing-frames#silent-verification)

We can verify "silently" by setting the `verify` flag to `"silent"` on the `Frog` instance.

This means the frame will go through verification, but not throw an error if it fails. Instead, the frame will receive `verified: false` in its context.

The `"silent"` flag is an inversion of control to the consumer. Only use `"silent"` if you know what you are doing.

```
import { Frog } from 'frog'
 
export const app = new Frog({
  verify: 'silent'
})
 
app.frame('/', (c) => {
  const { frameData, verified } = c
  const { buttonIndex, fid, castId } = frameData
  if (!verified) console.log('Frame verification failed')
  return c.res({/* .... */})
})
```

## 

Signing State[

](/concepts/securing-frames#signing-state)

We can sign [derived state](/concepts/state-management) (from `deriveState`) in a frame to ensure that state is not tampered with between frame actions.

Frog internally uses [JSON Web Signatures (JWS)](https://en.wikipedia.org/wiki/JSON_Web_Signature) to sign state, with a **HS256** algorithm.

If a `secret` is provided to the `Frog` instance, then state will be signed automatically.

src/index.ts.env

src/index.ts

```
import { Frog } from 'frog'
 
const app = new Frog({
  secret: process.env.FROG_SECRET
})
```

Last updated: 3/20/24, 6:53 PM

[

State Management

Previousshift←](/concepts/state-management)[

Deployment

Nextshift→](/concepts/deployment)