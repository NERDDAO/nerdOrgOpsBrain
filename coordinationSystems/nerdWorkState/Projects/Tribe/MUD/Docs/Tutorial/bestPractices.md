

# System Best Practices

## Security considerations[](#security-considerations)

### Avoid the root namespace if possible[](#avoid-the-root-namespace-if-possible)

A `System` in the root namespace costs slightly less gas because it uses the `World` context, including storage, and does not need to call the `World` back to read or write information. However, because it runs in the `World` context, there are security risks in writing such a `System`.

- A root namespace `System` can overwrite information in tables. This means, for example, it can create [any delegation that it wants](/world/account-delegation) by modifying `world__UserDelegationControl`.
- Any [ETH owned by a namespace in the `World`](/world/balance) is actually stored in the `World` and therefore can be sent anywhere by such a `System`.
- A root namespace `System` can call any other `System` with arbitrary values for [`_msgSender()`](/world/reference/world-context#_msgsender) and [`_msgValue()`](/world/reference/world-context#_msgvalue). This means it can perform any action while pretending to be any user.

This violates the security principle of [_least privilege_ (opens in a new tab)](https://csrc.nist.gov/glossary/term/least_privilege). Ideally, you should avoid running in the root namespace.

🚫

If you _have_ to use a `System` in the root namespace, avoid using [`delegatecall` (opens in a new tab)](https://docs.soliditylang.org/en/v0.8.16/introduction-to-smart-contracts.html#delegatecall-callcode-and-libraries), except for libraries whose security you verified. Any contract you call with `delegatecall` is going to inherit the `World` context, and have unlimited permissions on your application.

## Design consideraions[](#design-consideraions)

### One call, one action[](#one-call-one-action)

If you want a transaction to do multiple actions (for example: register, start a game, and then perform the first move in the game), you don't need a `DoThis_DoThat_AndThen_DoTheOtherThing` function. Just use [batch calls](/world/batch-calls), which preserve the message sender (in `_msgSender()).

### Use delegation for user agents[](#use-delegation-for-user-agents)

If a `System` is going to act on the users' behalf, it will need to call other `System`s, probably in other namespaces. The way to do this is to request users to [delegate their permissions](/world/account-delegation). This is necessary because:

- If you call another `System` through the `World`, the message sender (`_msgSender()`) is the calling `System`. The called `System` has no way of knowing if it can trust the caller `System` to represent the wishes of the ultimate user. See [the Solidity documentation (opens in a new tab)](https://docs.soliditylang.org/en/v0.8.25/security-considerations.html#tx-origin) for an in-depth explanation.
- If you `delegatecall` another `System` directly you keep `_msgSender()`'s value, but the called `System` is only allowed to perform actions allowed to the calling `System`.

### Don't send ETH with your calls[](#dont-send-eth-with-your-calls)

The way [ETH balances](/world/balance) work in MUD, _all_ ETH is stored in the `World` contract and internally accounted as belonging to namespaces. If your `System` is in the root namespace it has access to this ETH, but if you transfer out except through [`BalanceTransferSystem` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/modules/init/implementations/BalanceTransferSystem.sol), you need to update the accounting table. If your `System` is not in the root namespace it does not have direct access to the ETH anyway. You _have_ to use `BalanceTransferSystem`.

## Implementation considerations[](#implementation-considerations)

### Use libraries to bypass the contract size limit[](#use-libraries-to-bypass-the-contract-size-limit)

If you have a large `System` and you come up against the [contract size limit (opens in a new tab)](https://ethereum.org/en/developers/docs/smart-contracts/#limitations), don't split it in two. Instead, use [public libraries (opens in a new tab)](https://docs.soliditylang.org/en/latest/contracts.html#libraries). Libraries are called using [`delegatecall` (opens in a new tab)](https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html#delegatecall-and-libraries), so they have the same permissions as the calling contract. This includes the ability to write to MUD tables when the calling `System` is permitted to do so.

[Advanced features](/guides/emojimon/6-advanced "Advanced features")[Contracts](/templates/typescript/contracts "Contracts")

---

MIT 2023 © MUD

System Best Practices – MUD