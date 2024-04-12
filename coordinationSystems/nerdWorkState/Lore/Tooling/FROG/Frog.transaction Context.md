# Frog.transaction Context[

](/reference/frog-transaction-context#frogtransaction-context)

The `c` object is the parameter of the route handlers. It contains context for the transaction route.

```
import { Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  return c.send({/* ... */})
})
```

**Tip**

A transaction handler can also be asynchronous (ie. `async (c) => { ... }`).

## 

address[

](/reference/frog-transaction-context#address)

- **Type**: `string | undefined`

The address of a wallet that sends a transaction.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  const { address } = c
  return c.send({/* ... */})
})
```

## 

buttonIndex[

](/reference/frog-transaction-context#buttonindex)

- **Type**: `number`

The index of the button that was previously clicked.

For example, if the user clicked `"Banana"`, then `buttonIndex` will be `2`.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Select a fruit.
      </div>
    ),
    intents: [
      <Button.Transaction
        target="/send-ether"
        value="apple"
      >
        Apple
      </Button.Transaction>,
      <Button.Transaction
        target="/send-ether"
        value="banana"
      >
        Banana
      </Button.Transaction>,
    ]
  })
})
 
app.transaction('/send-ether', (c) => {
  const { buttonIndex } = c
  return c.send({/* ... */})
})
```

## 

buttonValue[

](/reference/frog-transaction-context#buttonvalue)

- **Type**: `string`

The value of the button that was previously clicked.

For example, if the user clicked `"Banana"`, then `buttonValue` will be `"banana"`.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Select a fruit.
      </div>
    ),
    intents: [
      <Button.Transaction
        target="/send-ether"
        value="apple"
      >
        Apple
      </Button.Transaction>,
      <Button.Transaction
        target="/send-ether"
        value="banana"
      >
        Banana
      </Button.Transaction>,
    ]
  })
})
 
app.transaction('/send-ether', (c) => {
  const { buttonValue } = c
  return c.send({/* ... */})
})
```

## 

contract[

](/reference/frog-transaction-context#contract)

- **Type**: `TransactionResponseFn<ContractTransactionParameters>`

Contract transaction response. [See more.](/reference/frog-transaction-response#contract-transaction-ccontract)

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/mint', (c) => {
  return c.contract({/* ... */})
})
```

## 

frameData[

](/reference/frog-transaction-context#framedata)

- **Type**: `FrameData`

Data from the frame that was passed via the `POST` body from a Farcaster Client. [See more.](https://docs.farcaster.xyz/reference/frames/spec#frame-signature-packet)

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  const { frameData } = c
  const { castId, fid, messageHash, network, timestamp, url } = frameData
  return c.send({/* ... */})
})
```

## 

initialPath[

](/reference/frog-transaction-context#initialpath)

- **Type**: `string`

Initial/start path of the frame set.

If the user clicks `<Button.Reset>`, they will be directed to this URL.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  const { initialPath } = c
  return c.send({/* ... */})
})
```

## 

inputText[

](/reference/frog-transaction-context#inputtext)

- **Type**: `string`

The value of the input that was previously interacted with.

For example, if the user typed `"Banana"`, then `inputText` will be `"Banana"`.

```
import { Button, Frog, TextInput } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Send Ether
      </div>
    ),
    intents: [
      <TextInput placeholder="Value (ETH)" />,
      <Button target="/send-ether">Send</Button>,
    ]
  })
})
 
app.transaction('/send-ether', (c) => {
  const { inputText } = c
  return c.send({/* ... */})
})
```

## 

previousButtonValues[

](/reference/frog-transaction-context#previousbuttonvalues)

- **Type**: `string[]`

The data of the previous intents.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        Select your fruit
      </div>
    ),
    intents: [
      <Button.Transaction
        target="/send-ether"
        value="apple"
      >
        Apple
      </Button.Transaction>,
      <Button.Transaction
        target="/send-ether"
        value="banana"
      >
        Banana
      </Button.Transaction>,
    ]
  })
})
 
app.transaction('/send-ether', (c) => {
  const { buttonValue, previousButtonValues } = c
  console.log(previousButtonValues)
  ['apple', 'banana']  return c.send({/* ... */})})
```

## 

previousState[

](/reference/frog-transaction-context#previousstate)

- **Type**: `State`

The state of the previous frame.

```
import { Button, Frog } from 'frog'
 
type State = {     
  values: string[] 
} 
 
export const app = new Frog<{ State: State }>({ 
  initialState: { 
    values: [] 
  } 
}) 
 
app.frame('/', (c) => {
  const { buttonValue, deriveState } = c
  const state = deriveState(previousState => { 
    if (buttonValue) previousState.values.push(buttonValue)
  })
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        {values.map(value => <div>{value}</div>)}
      </div>
    ),
    intents: [
      <Button value="apple">Apple</Button>, 
      <Button value="banana">Banana</Button>,
      <Button.Transaction target="/send-ether">Send</Button.Transaction>,
    ]
  })
})
 
app.transaction('/send-ether', (c) => {
  const { previousState } = c
  return c.send({/* ... */})
})
```

## 

req[

](/reference/frog-transaction-context#req)

- **Type**: `Request`

[Hono request object](https://hono.dev/api/request).

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  const { req } = c
  return c.res({/* ... */})
})
```

## 

res[

](/reference/frog-transaction-context#res)

- **Type**: `TransactionResponseFn<TransactionParameters>`

Raw transaction response. [See more.](/reference/frog-transaction-response#raw-transaction-cres)

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  return c.res({/* ... */})
})
```

## 

send[

](/reference/frog-transaction-context#send)

- **Type**: `TransactionResponseFn<SendTransactionParameters>`

Send transaction response. [See more.](/reference/frog-transaction-response#send-transaction-csend)

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  return c.send({/* ... */})
})
```

## 

var[

](/reference/frog-transaction-context#var)

- **Type**: `HonoContext['var']`

Extract a context value that was previously set via [`set`](/reference/frog-transaction-context#set) in [Middleware](/concepts/middleware).

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.use(async (c, next) => {
  c.set('message', 'Frog is cool!!')
  await next()
})
 
app.transaction('/send-ether', (c) => {
  const message = c.var.message
  return c.send({/* ... */})
})
```

## 

verified[

](/reference/frog-transaction-context#verified)

- **Type**: `boolean`

Whether or not the [`frameData`](/reference/frog-transaction-context#framedata) (and [`buttonIndex`](/reference/frog-transaction-context#buttonindex), [`buttonValue`](/reference/frog-transaction-context#buttonvalue), [`inputText`](/reference/frog-transaction-context#inputtext)) was verified by the Farcaster Hub API.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  const { verified } = c
  return c.send({/* ... */})
})
```

## 

url[

](/reference/frog-transaction-context#url)

- **Type**: `string`

Current URL.

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.transaction('/send-ether', (c) => {
  const { url } = c
  return c.send({/* ... */})
})
```

Last updated: 3/20/24, 6:53 PM

[

Response

Previousshift←](/reference/frog-frame-response)[

Response

Nextshift→](/reference/frog-transaction-response)