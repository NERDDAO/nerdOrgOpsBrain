# Middleware[

](/concepts/middleware#middleware)

Since Frog is built on top of [Hono](https://hono.dev), Frog supports Hono's middleware pattern.

Middleware works before and after the `.frame` [handler](/reference/frog-frame#handler) by allowing you to manipulate the request and response before and after dispatching respectively. We recommend checking out the Hono [documentation on Middleware](https://hono.dev/guides/middleware) for a more in-depth understanding.

## 

Overview[

](/concepts/middleware#overview)

There are two ways to inject middleware:

1. Globally, on the [App-level](/concepts/middleware#app-level).
2. Isolated, on a [Route-level](/concepts/middleware#route-level).

### 

App-level[

](/concepts/middleware#app-level)

We can inject middleware on the `app` level using the `.use` method. This means middleware will be injected for all routes under `app`.

```
import { Button, Frog } from 'frog'
import { neynar, type NeynarVariables } from 'frog/middlewares'
 
export const app = new Frog()
   ↓ 1. Inject middleware onto the app.  .use(    neynar({
      apiKey: 'NEYNAR_FROG_FM',
      features: ['interactor', 'cast'],
    }),
  )
 
2. All routes on `app` will inject the middleware.
```

### 

Route-level[

](/concepts/middleware#route-level)

We can inject middleware on a route level:

```
import { Button, Frog } from 'frog'
import { neynar } from 'frog/middlewares'
 
const neynarMiddleware = neynar({
  apiKey: 'NEYNAR_FROG_FM',
  features: ['interactor', 'cast'],
})
 
export const app = new Frog()
 
               ↓ Inject middleware onto the route.app.frame('/', neynarMiddleware, (c) => {  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        {c.var.interactor?.displayName}
      </div>
    ),
  })
})
```

**Tip**

You can also include more than one middleware on a route!

```
app.frame(
  '/', 
  neynarMiddleware,
  fooMiddleware,
  barMiddleware,
  c => { /* ... */ }
)
```

## 

Built-in Middlewares[

](/concepts/middleware#built-in-middlewares)

### 

Neynar[

](/concepts/middleware#neynar)

Frog comes with a built-in middleware for [Neynar](https://neynar.com) which allows you to easily integrate Neynar features (such as the interactor of your frame, and frame cast) into Frog context.

```
import { Button, Frog } from 'frog'
import { neynar } from 'frog/middlewares'
 
export const app = new Frog()
   ↓ 1. Inject `neynar` middleware onto the app.  .use(    neynar({
      apiKey: 'NEYNAR_FROG_FM',
      features: ['interactor', 'cast'],
    }),
  )
  .frame('/', (c) => {
            ↓ 2. Use `c.var` to access Neynar variables!    const { displayName, followerCount } = c.var.interactor || {}    console.log('cast: ', c.var.cast)
    console.log('interactor: ', c.var.interactor?: {
    activeStatus: "active" | "inactive";
    custodyAddress: string;
    displayName: string;
    fid: number;
    followerCount: number;
    followingCount: number;
    object: "user";
    pfpUrl: string;
    profile: {
        ...;
    };
    username: string;
    verifications: string[];
    verifiedAddresses: {
        ...;
    };
    viewerContext?: {
        ...;
    } | undefined;
} | undefinedThe user who interacted with the frame.interactor)
    return c.res({
      image: (
        <div
          style={{
            alignItems: 'center',
            color: 'white',
            display: 'flex',
            justifyContent: 'center',
            fontSize: 48,
            height: '100%',
            width: '100%',
          }}
        >
          Greetings {displayName}, you have {followerCount} followers.
        </div>
      ),
    })
  })
```

Feel free to use our Neynar API Key: `"NEYNAR_FROG_FM"`.

However, please note that this API Key is for development purposes only – it is prone to rate-limiting. It is recommended to use your own API Key in production. [See more](https://neynar.com/#get-started).

## 

Custom Middleware[

](/concepts/middleware#custom-middleware)

You can write your own Frog middleware. This is great if you want to share common logic across or frames or if you are developing a SDK for Frog users to hook into their frames.

```
import { Frog } from 'frog'
 
export const app = new Frog()
 
// Custom logger
app.use(async (c, next) => {
  console.log(`[${c.req.method}] ${c.req.url}`)
  await next()
})
 
// Add a custom header
app.use('/foo/*', async (c, next) => {
  await next()
  c.header('x-message', 'Only called for `/foo` and `/foo/bar` frames.')
})
 
app.frame('/', (c) => {/* ... */})
 
app.frame('/foo', (c) => {/* ... */})
 
app.frame('/foo/bar', (c) => {/* ... */})
```

## 

Community Middleware[

](/concepts/middleware#community-middleware)

Middleware is one of the most powerful pieces of Frog. This section showcases community-built middleware that you can use in your Frog apps.

If you've built a middleware for Frog, feel free to [submit a PR](https://github.com/wevm/frog) to add it to this list.

### 

Airstack Allow List Middleware[

](/concepts/middleware#airstack-allow-list-middleware)

The [Allow List Middleware](https://github.com/Airstack-xyz/airstack-frames-sdk/tree/main?tab=readme-ov-file#allow-list-middleware) allows you to build an allow list that only allows certain users to interact a Farcaster Frames. The allow list can be built based on various onchain criterias, including having > X followers, attended certain POAPs, holding certain ERC20/721/1155 tokens, and if a user certain Farcaster users.

To use this middleware, you must first install the [Airstack Frames SDK](https://www.npmjs.com/package/@airstack/frames).

```
import { allowList, TokenBlockchain } from "@airstack/frames"
 
const app = new Frog()
 
const allowListMiddleware = allowList({
  allowListCriteria: {
    eventIds: [166577],
    tokens: [
      {
        tokenAddress: "0xe03ef4b9db1a47464de84fb476f9baf493b3e886",
        chain: TokenBlockchain.Zora,
      },
    ],
  },
});
 
app.use('/', allowListMiddleware)
```

### 

Airstack Onchain Data Middleware[

](/concepts/middleware#airstack-onchain-data-middleware)

The [Onchain Data Middleware](https://github.com/Airstack-xyz/airstack-frames-sdk/tree/main?tab=readme-ov-file#onchain-data-middleware) enables you to easily inject various onchain data to your Frames, including Farcaster user details, followings, followers, channels, token balances, POAPs, and more.

To use this middleware, you must first install the [Airstack Frames SDK](https://www.npmjs.com/package/@airstack/frames).

```
import { onchainData, TokenBlockchain } from "@airstack/frames"
 
const app = new Frog()
 
const onchainDataMiddleware = onchainData({
  env: "dev",
  features: {
    userDetails: {},
    erc20Mints: {
      chains: [TokenBlockchain.Polygon],
      limit: 1,
    },
  },
});
 
app.use('/', onchainDataMiddleware)
```

### 

Pinata FDK Analytics Middleware[

](/concepts/middleware#pinata-fdk-analytics-middleware)

The [Frog Analytics Plugin](https://docs.pinata.cloud/farcaster/fdk#frog-analytics-plug-in) allows you to hook into Pinata Frame analytics.

```
import { PinataFDK } from 'pinata-fdk'
 
const app = new Frog()
 
const fdk = new PinataFDK({
  pinata_jwt: '<YOUR_PINATA_JWT>',
  pinata_gateway: '<YOUR_PINATA_GATEWAY>'
})
 
app.use('/', fdk.analyticsMiddleware({
  frameId: 'my-frame-id',
  customId: 'my-custom-id',
}))
```

Last updated: 3/20/24, 6:56 PM

[

Devtools

Previousshift←](/concepts/devtools)[

Transactions

Nextshift→](/concepts/transactions)