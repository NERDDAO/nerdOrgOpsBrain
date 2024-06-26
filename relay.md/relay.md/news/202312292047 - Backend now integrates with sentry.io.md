---
creation_date: 2023-12-29 20:47
data_creació: 2024-06-25 09:24
data_modificació: 2024-06-25 17:20
modification date: Friday 29th December 2023 20:47:48
relay-document: 9c59bfae-a7bc-4444-b476-16f7c26d0f28
relay-to:
  - news@relay.md
summary: Feedback for authentication errors, fixes and integration with sentry.io
tags:
  - relaymd/journal
type: uniquenote
---

# Sentry.io integration for faster error handling
Thanks to some excellent feedback from our first testers, we already fixed a few bugs that caused some testers issues. Primiarily, we have resolved authentication problems with Github's Oauth integration and should be more robust now.

## Consequences
Our code base has about 80% test coverage, but some things (like integration with external services) cannot be fully tested and may raise exceptions every now and then.
In order for us to be notified of those, and not have to bother the user to send us a crash report, we have integrated sentry.io into our web and api service. In short, sentry.io is an application performance monitoring & error tracking that helps us see clearer, solve quicker, & learn continuously.
We still enjoy reading our users' feedback, but hopefully, we can now come back to them *faster* and letting them know that a fix is on the way sooner.

Happy bug hunting :)