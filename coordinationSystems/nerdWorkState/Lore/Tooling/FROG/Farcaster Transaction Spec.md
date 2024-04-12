# Goals

---

1. Allow frames to trigger onchain transactions from Farcaster apps.
2. Minimize risk to users from malicious frames

# Proposal

---

At a high level:

1. A frame may have a button of action type `tx` with a `target` property that has a URL.
2. The app makes a frame post request to the URL to fetch the tx data.
3. The app forwards the tx data to the user’s wallet.
4. The wallet returns the user to the app with the tx id, if successful.
5. The app makes a frame post request with the tx id, if successful.

![Screenshot 2024-02-28 at 8.16.28 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/d62a7b91-4f6c-4165-9b66-f43be1a81fdc/Screenshot_2024-02-28_at_8.16.28_AM.png)

### Example

A user may rent storage units on Farcaster by interacting with the Storage Registry contract. Alice wants to build a frame to make it easy to do from the feed.

She creates a button with the following tags:

```tsx
<meta property="fc:frame:button:1" content="Rent Storage" />
<meta property="fc:frame:button:1:action" content="tx" />
<meta property="fc:frame:button:1:target" content="<https://frame.example.com/rent_storage>" />
```

When the button is clicked, the app POST’s to [`frame.example.com/rent_storage`](http://frame.example.com/rentStorage) with a frame signature packet. By inspecting the packet, alice’s servers can verify the user that requested the transaction and adjust the response. The frame signature packet includes an `address` field with the user’s connected wallet address.

Alice’s server then returns a transaction encoded as JSON:

```tsx
{
  chainId: "eip155:10",
  method: "eth_sendTransaction",
  params: {
    abi: [...], // JSON ABI of the function selector and any errors
    to: "0x00000000fcCe7f938e7aE6D3c335bD6a1a7c593D",
    data: "0x783a112b0000000000000000000000000000000000000000000000000000000000000e250000000000000000000000000000000000000000000000000000000000000001",
    value: "984316556204476",
  },
};
```

Alice can host this URL on her frame server and produce the tx calldata herself. Contracts can host calldata producing url’s to make it easier for frame devs. For example, [farcaster.xyz](http://farcaster.xyz) could host an endpoint that returns calldata for the storage contract and alice’s frame calls it directly.

Finally, the app lets the user pass this transaction into their wallet and execute it.

## Security Considerations

---

Attackers may use frames to trick users into signing dangerous transactions. Some common attacks might be:

1. **Phishing** — A frame returns a malicious drainer or ETH transfer, but pretends it’s a free mint.
2. **Spoofing** — Uniswap hosts endpoints that return tx calldata for swaps. A malicious frame creates a fake “wBTC” ERC-20. It asks uniswap to generate the transaction and the user assumes it is the “real” wBTC since it came via Uniswap.
3. **Authorization Exploits** — Fido protocol lets users stake to earn interest and set a recovery address to withdraw funds later. A malicious frame encourages users to stake but sets its own address as the recovery.
4. **Domain Hijacking** — An attacker hijacks a tx domain’s DNS and returns malicious calldata.

There is no foolproof way to protect against all these attacks. Clients may adopt the following strategies to minimize the impact of such attacks:

1. Public allowlists and banlists for frame server URLs.
2. Transaction simulation in wallets and/or Farcaster apps.
3. Using the social graph to determine if a frame is trustworthy or not.
4. Educating user about best practices before engaging with frame transactions.
5. Encouraging users to only use transaction data from trusted domains.

Warpcast plans to implement these safeguards, and it’s implementation is described below.

## Attribution

---

Apps may want to attribute an onchain transaction to give credit to the sources that originated it. They can add a [calldata suffix](https://viem.sh/docs/contract/writeContract#datasuffix) to frame tx identifying the origin, similar to [Seaport order attributions](https://www.notion.so/Seaport-Order-Attributions-ec2d69bf455041a5baa490941aad307f?pvs=21).

Farcaster apps should append 5 additional bytes of calldata identifying:

- The protocol (an `0xfc` byte)
- The Farcaster ID of the client application

```solidity
abi.encodePacked(
  bytes1(0xfc),
  uint32(clientFid),
);
```

For example, a tx from Warpcast (!9152) encodes to the calldata suffix:

```solidity
0xfc000023c0
```

# Specification

---

### Frame Buttons

- A frame’s `fc:frame:button:$idx:action` may be set to `tx` to indicate a Tx Button.
- A Tx Button must have a `target` which points to a valid Frame Transaction URL.
- A Tx Button may specify an `post_url` , which overrides the top level frame `post_url`.

### Frame Tx Button Rendering

- Apps must visually indicate that a tx button will request a wallet transaction.
- Apps must display the button label provided by the frame.
- Apps must reject frame buttons with missing properties.

### Frame Transaction URLs

A _Frame Transaction URL_ is a URL reference to a location that returns tx calldata. It:

- Must be an HTTPS URL that may include query parameters.
- Must respond `200 OK` to an HTTPS `POST` request with JSON specifying a transaction.

The JSON response must match `TransactionTargetResponse` type with:

- `chainId` — A CAIP-2 chain ID to identify the tx network (e.g. Ethereum mainnet)
- `method` — A method ID to identify the type of tx request. (e.g. `"eth_sendTransaction"`)
- `attribution` — Optional. Return `false` to omit the [calldata attribution suffix](https://www.notion.so/Frame-Transactions-Public-9d9f9f4f527249519a41bd8d16165f73?pvs=21). If this value is `undefined` or `true`, clients will append the attribution suffix.
- `params` — Specific parameters for `chainId` and `method`

```tsx
type TransactionTargetResponse {
  chainId: string;                  
  method: "eth_sendTransaction";
  attribution?: boolean;
  params: EthSendTransactionParams;
}
```

**Ethereum Params** If the method is `eth_sendTransaction` and the chain is an Ethereum EVM chain, the param must be of type `EthSendTransactionParams`:

- `to` — transaction to address
- `abi` — JSON ABI which **must** include encoded function type and **should** include potential error types. Can be empty.
- `value` — value of ether to send with the transaction in wei. Optional.
- `data` — transaction call data. Optional.

```tsx
type EthSendTransactionParams {
  abi: Abi | [];
  to: Hex;                   
  value?: string;
  data?: Hex;             
}
```

### Frame Button Clicks

When a user clicks a frame transaction button, the Farcaster app:

1. Must fetch the tx data from the `target` URL by `POST`ing a frame message including the user’s connected wallet address.
2. Should present tx details to the user for confirmation.
3. Must relay to the user’s wallet to execute when confirmed.
4. Must show a success or failure state to the user based on the wallets response.
5. Must `POST` a frame message to the `post_url` including a transaction ID if it succeeds.

### **FrameActionBody Message**

The action body is extended with optional transaction id and address properties. For Ethereum, transaction id must be the transaction hash. For other chains, this is not yet specified. For Ethereum, address must be the user’s connected wallet address. For other chains, this is not yet specified.

```protobuf
message FrameActionBody {
  bytes frame_url = 1;      // The URL of the frame app
  bytes button_index = 2;   // The index of the button that was clicked
  CastId cast_id = 3;       // The cast which contained the frame URĽ
  bytes input_text = 4;     // The text from the user input (if any)
  bytes state = 5;          // Serialized frame state value
  bytes transaction_id = 6; // Transaction ID
  bytes address = 7;        // User's connected address
}
```

### Security Checks

Apps should consider the following mechanisms to protect users against malicious transactions:

1. Transaction simulation.
2. Domain allowlisting and banlisting to stop known attackers.
3. Social graph analysis to detect potential bad or untrusted actors.
4. Educating users about the potential dangerous of transactions and using a wallet with limited balances.

# Warpcast Implementation

---

_This is not part of the spec, but likely useful for developers and other clients to understand._

Warpcast’s goals with rolling out frames are to:

1. Keep users safe.
2. Deliver a great user experience.
3. Make it easy for developers to launch transaction frames on day one.

## UX

1. A user’s first interaction with a frame transaction will trigger an educational modal.
2. Every transaction frame button will have a special glyph just like redirect buttons.
3. Warpcast will show a warning if a user is interacting with a frame transaction from an untrusted source and unknown domain (see Warning Exceptions and Domain Lists below)
4. Warpcast will then route the transaction to a wallet via RainbowKit (web) or Mobile Wallet Protocol (mobile)

### Warpcast Web

We plan to use RainbowKit to connect to a user’s wallets. This will use the standard RainbowKit adapter and we plan to show a transaction simulation wherever we can.

![Screenshot 2024-03-07 at 3.49.40 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/c37edfdd-a604-416a-9134-036408aa8910/Screenshot_2024-03-07_at_3.49.40_PM.png)

![Screenshot 2024-03-07 at 3.50.30 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/91d5b0fd-9933-49b1-8e6c-789fc9181764/Screenshot_2024-03-07_at_3.50.30_PM.png)

![Screenshot 2024-03-07 at 3.52.02 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/7f2d2e58-5ebc-41d4-b9a1-c86cab969ac2/Screenshot_2024-03-07_at_3.52.02_PM.png)

### Warpcast Mobile

We plan to start using [Mobile Wallet Protocol](https://github.com/MobileWalletProtocol/wallet-mobile-sdk) to interact with wallets.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/fa4c67a2-e6e1-4603-815d-6f527c65791e/Untitled.png)

Mobile Wallet Protocol is only supported by Coinbase Wallet today. You will need to have it installed to make transactions on mobile.

We chose MWP over other protocols because it has a much better UX:

1. It works 100% of the time.
2. It is much, much faster.
3. It redirects users back to Warpcast automatically.

## Timeline

_Here’s a tentative timeline of how the rollout might work. Assume T-0 is launch day._

| Mar 7th | Warpcast is code complete.

A test transaction frame is created and broadcast by the team to perform an end to end test. The frame validator also allows testing frame transactions e2e. | | --- | --- | | Mar 7th | Users and domains may apply to be on the allowlist for tx frames.

All users and domains may create and share transaction frames, but frames shared by users not on the allowlist may show a warning when other users interact with them. | | Mar 8th | Frame transactions launch on L2s.

Warpcast supports frame with transactions on Base, OP Mainnet and Zora. Any frames posted with transactions will now appear in feeds. | | TBD | Frame transactions launch on L1.

Warpcast supports frame transactions on L1 ETH after some testing and validation of our security systems. Likely weeks to months. |

** Zora is not fully supported by some services we rely on, needs more investigation.*

## Transaction Previews

(1) Warpcast will simulate transactions and show the user a preview. If the user approves, the transaction will be sent to their wallet.

![Screenshot 2024-03-07 at 3.38.06 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/bc3a44e1-661c-48e9-aadc-ad1b8403550a/Screenshot_2024-03-07_at_3.38.06_PM.png)

(2) If our simulation detects potential minor issues, users will be shown some warnings first.

_The warning text shown here is illustrative for mocks, and simpler human readable errors will be shown to the user._

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/017d185c-4f55-4bb7-9edc-b72d31df4797/Untitled.png)

(3) If our simulation detects major issues, users will be encouraged to close the transaction:

_The warning text shown here is illustrative for mocks, and simpler human readable errors will be shown to the user._

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/204791db-f556-44a3-ad89-bcd117538b94/Untitled.png)

## New Domain Warnings

Warpcast will show warnings for frames from new domains. Users will see the following prompt before the transaction preview:

Frames will be reviewed within 24 hours of the first transaction. If the frame is deemed to be safe, the warning label will be removed. Other warnings may still appear if the simulation finds issues.

![Screenshot 2024-03-07 at 3.33.23 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/49351873-d0a3-48a0-ab26-1a550ad9842c/Screenshot_2024-03-07_at_3.33.23_PM.png)

# Extensions

---

_These are features we don’t plan to ship with v1, but plan to support after._

### Transactions

- Support an `eth_signTypedDataV4` method for signature requests.
- Support Solana transactions.

### Attribution

Extend attribution suffix to include domain FID, author FID, and a 4-byte URL tag in the following form. This format is backwards compatible, since we will append additional data:

```solidity
abi.encodePacked(
  bytes1(0xfc),
  uint32(clientFid),
  uint32(domainFid),
  uint32(authorFid),
  bytes4(keccak256(frameUrl))
);
```

For example, a tx from Warpcast (!9152) via a Neynar Frame Builder app (!6131) authored by user `horsefacts.eth` (!3621) from URL [`https://frames.neynar.com/f/4e7e674e/97bc53ef`](https://frames.neynar.com/f/4e7e674e/97bc53ef) encodes to the 17-byte calldata suffix:

```solidity
0xfc000023c0000017f300000e2580ed0024
```

# Changelog

---

- 3/6: Make several tweaks to language, add mockups, remove user allowlisting in favor of domain allowlisting
- 3/2: Replace “human readable ABI” with JSON ABI in transaction data response
- 3/2: Remove domain association signature from transaction data response
- 2/28: Updated Warpcast proposal
- 2/28: Updated transaction URL request format to receive Frame message.
- 2/28: Updated transaction URL response format. Added CAIP-2 chain ID, domain association, human readable signature. Removed transaction description.
- 2/28: Added onchain attribution spec.
- 2/28: Added optional `button:$idx:post_url` for `tx` actions.
- 2/28: Added frame-to-frame transactions example.

# FAQ

---

**Why is there no ‘from’ field on the transaction url response?**

We don’t have a mechanism to guarantee that a request originates from a particular address since some wallets allow users to switch the caller.

**Why does the transaction url response return encoded calldata instead of decoded data?**

While decoded data may be marginally more developer-readable, the downsides outweigh the benefits since encoding is often not straightforward and introduces more potential for errors.

**Why are frame transactions launching on L2’s first?**

Typically, users tend to have fewer valuable assets on L2s and are also more likely to be using L2s in Farcaster. Starting here lets us battle test our implementation before moving to L1s.

**How should clients handle server errors?**

There currently isn’t a spec for how to treat errors returned from the server, but this is something we want to work on in a followup.

# Appendix

---

## Frame-to-frame transactions

Dapps may serve calldata to untrusted frames from a known domain, but they must be careful about the kind of transactions they are willing to serve. As an alternative they can also provide a frame-to-frame API that gives them more control over frame UI and context.

Frames can pass control flow to the known domain by using the known domain’s API as their frame’s `post_url`. The known domain can return frame UI to construct and execute the transaction. This is a stronger trust context, since the dapp is in full control of both the frame UI and transaction data.

Benefits to the dapp:

- Full control over UI and transaction handling.
- Ability to use frame message data to attribute a transaction to Farcaster IDs (for example, paying a referral reward to the origin frame author and granting reward points to the user who posted it).
- Ability to use frame message data as context for transactions. (for example, using user’s preferred wallet in-app based on FC identity).

See the example below, showing an end-to-end flow for purchasing an NFT from an OpenSea listing. (Note that the “Seaport Frame API” does not exist, it’s a hypothetical example).

![frame_to_frame_transactions.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ae150b5-547b-47f9-9f29-638305dbba7a/38db4d88-ace8-4278-b74c-e611a6c34649/frame_to_frame_transactions.png)