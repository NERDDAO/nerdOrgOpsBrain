
# Architecture [​](#architecture)

Farcaster has a hybrid architecture that stores identity onchain and data offchain.

![Architecture](https://docs.farcaster.xyz/learn/architecture/overview/assets/architecture.T7tCPEnC.png)

## Onchain [​](#onchain)

Farcaster's onchain systems are implemented as [contracts on OP Mainnet](./contracts). Actions are performed onchain only when security and consistency are critical. Use of onchain actions is kept at a minimum to reduce costs and improve performance.

Only a handful of actions are performed onchain, including:

- Creating an [account](./../what-is-farcaster/accounts).
- Paying rent to [store data](./../what-is-farcaster/messages#storage).
- Adding account keys for [connected apps](./../what-is-farcaster/apps#connected-apps).

## Offchain [​](#offchain)

Farcaster's offchain system is a peer-to-peer network of servers called [Hubs](./hubs) which store user data. The majority of user actions are performed offchain. These include:

- Posting a new public message.
- Following another user.
- Reacting to a post.
- Updating your profile picture.

Actions are performed offchain when performance and cost are critical. Use of offchain actions is typically preferred when consistency isn't a strict requirement. Offchain systems achieve security by relying on signatures from onchain systems.

[Previous pageApps](/learn/what-is-farcaster/apps)

[Next pageContracts](/learn/architecture/contracts)