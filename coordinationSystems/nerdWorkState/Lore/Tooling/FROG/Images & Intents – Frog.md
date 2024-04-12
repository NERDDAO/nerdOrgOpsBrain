 
# Images & Intents 

Farcaster Frames have two main UI components:

- an **Image**, and
- a set of **Intents**

![Images & Intents](https://frog.fm/concepts/images-intents/images-intents.png)

## 

Image[

](/concepts/images-intents#image)

In Farcaster Frames, an **Image** behaves similarly to an [Open Graph image](https://ogp.me/), and shares the same constraints.

**Frog** uses [Satori](https://github.com/vercel/satori) & [Resvg](https://github.com/yisibl/resvg-js) under-the-hood to compile JSX elements to a static image. Check out Vercel's [Open Graph Image Generation page](https://vercel.com/docs/functions/og-image-generation) to learn more about the internals of **Frog's** image renderer, and the constraints and best practices for creating OG images.

In **Frog**, an **Image** can be rendered via the `image` return property:

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

You can use the [OG Image Playground](https://og-playground.vercel.app/) to quickly prototype your image.

You can also specify an image URL:

src/index.tsx

```
import { Button, Frog } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  const { buttonValue, status } = c
  return c.res({
    image: 'https://i.ytimg.com/vi/R3UACX5eULI/maxresdefault.jpg',
    intents: [
      <Button value="apple">Apple</Button>,
      <Button value="banana">Banana</Button>,
      <Button value="mango">Mango</Button>
    ]
  })
})
```

### 

Image Options[

](/concepts/images-intents#image-options)

**Frog** also exposes options that you can use to customize the image.

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
    imageOptions: { width: 600, height: 600 },
    intents: [
      <Button value="apple">Apple</Button>,
      <Button value="banana">Banana</Button>,
      <Button value="mango">Mango</Button>
    ]
  })
})
```

You can also pass a set of default options to the `Frog` constructor:

src/index.tsx

```
import { Button, Frog } from 'frog'
 
export const app = new Frog({
  imageOptions: { height: 600, width: 600 }
})
```

|Property|Type|Default|Description|
|---|---|---|---|
|`width`|`number`|`1200`|The width of the image.|
|`height`|`number`|`630`|The height of the image.|
|`emoji`|`"twemoji"` `"blobmoji"` `"noto"` `"openmoji"`||The emoji set to use.|
|`debug`|`boolean`|`false`|Debug mode flag.|
|`fonts`|`Font[]`|–|The fonts to use.|
|`headers`|`HeadersInit`|–|Headers for the image response.|

[Read more on Image Options.](https://vercel.com/docs/functions/og-image-generation/og-image-api#fonts-parameters-within-options)

## 

Intents[

](/concepts/images-intents#intents)

**Intents** are the interactive elements in a Farcaster Frame. They are the buttons and/or text inputs that users can interact with.

**Frog** currently supports the following **Intents**:

- [`<Button>`](/intents/button)
- [`<Button.Link>`](/intents/button-link)
- [`<Button.Mint>`](/intents/button-mint)
- [`<Button.Redirect>`](/intents/button-redirect)
- [`<Button.Reset>`](/intents/button-reset)
- [`<TextInput>`](/intents/textinput)

In **Frog**, **Intents** are rendered via the `intent` return property:

src/index.tsx

```
import { Button, Frog, TextInput } from 'frog'
 
export const app = new Frog()
 
app.frame('/', (c) => {
  const { buttonValue, inputText, status } = c
  const fruit = inputText || buttonValue
  return c.res({
    image: (
      <div style={{ color: 'white', display: 'flex', fontSize: 60 }}>
        {status === 'initial' ? (
          'Select your fruit!'
        ) : (
          `Selected: ${fruit}`
        )}
      </div>
    ),
    intents: [
      <TextInput placeholder="Enter a custom fruit..." />,
      <Button value="apple">Apple</Button>,
      <Button value="banana">Banana</Button>,
      <Button value="mango">Mango</Button>
    ]
  })
})
```

Last updated: 3/20/24, 6:53 PM

[

Routing

Previousshift←](/concepts/routing)[

Connecting Frames (Actions)

Nextshift→](/concepts/actions)