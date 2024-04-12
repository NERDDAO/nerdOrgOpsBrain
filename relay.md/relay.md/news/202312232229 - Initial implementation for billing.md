---
creation_date: 2023-12-23 22:29
modification date: Saturday 23rd December 2023 22:29:15
relay-document: 464df67a-99ec-4e7a-9596-02ebdfe698a5
relay-to:
  - news@relay.md
summary: 
tags:
  - relaymd/journal
type: uniquenote
---

# Billing Implementations
After some disappointments from other payment providers, we ended up integrating with Stripe.com and, wow, their API is just great.
Today, I have finally deployed billing on production and made the first payment for the `relay.md` team. Works like a charm. Now we need to polish some internal processes and properly process some additional webhooks we receive from stripe.com.