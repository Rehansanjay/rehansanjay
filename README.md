## Rehan Sanjay Venkatesan

Voice AI and backend engineer. I work on the frameworks voice agents run on, and on the failures that only show up on a real phone call.

**Ten fixes merged upstream** — eight into [`livekit/agents`](https://github.com/livekit/agents), plus [`pipecat`](https://github.com/pipecat-ai/pipecat) and [`jambonz`](https://github.com/jambonz/jambonz-feature-server). Over the last 90 days I've been the **second most active outside contributor to livekit/agents**.

📍 Chennai, India · 2026 CS graduate · open to a first engineering role
🔗 [Portfolio](https://rehansanjay-portfolio.vercel.app/) · [LinkedIn](https://www.linkedin.com/in/rehansanjay-venkatesan-449925285/)

---

### Merged upstream

**In the framework itself**

- [livekit/agents#7139](https://github.com/livekit/agents/pull/7139) — `ConnectionPool.invalidate()` closed sockets that were still streaming, and a handshake in flight escaped invalidation entirely and stayed reusable with stale settings. **Eleven plugins** call it from `update_options`, so one change to the primitive repaired all of them. Reviewed line by line by a maintainer.
- [#7054](https://github.com/livekit/agents/pull/7054) — found a whole bug class, fixed all fifteen instances across library, examples and tests, and got ruff's `RUF006` **enabled in their CI** so dangling tasks cannot come back.

**Provider plugins** — the same stale-handshake bug, found by grepping siblings for the shape

- [#7132](https://github.com/livekit/agents/pull/7132) asyncai · [#7133](https://github.com/livekit/agents/pull/7133) neuphonic · [#7140](https://github.com/livekit/agents/pull/7140) cartesia, where the API version was also sent in a header that disagreed with the body it was built for

**Lifecycle and cleanup**

- [#7023](https://github.com/livekit/agents/pull/7023) — an STT connection pool that stayed open after shutdown
- [#7012](https://github.com/livekit/agents/pull/7012) — per-stream HTTP sessions leaking through a `WeakSet`
- [#7050](https://github.com/livekit/agents/pull/7050) — a discarded prewarm task rebuilding a pool *after* close
- [pipecat#5464](https://github.com/pipecat-ai/pipecat/pull/5464) — a TTS service wrote model, voice and language only into its websocket init message, so a runtime change was stored, warned about, and silently never sent
- [jambonz#1585](https://github.com/jambonz/jambonz-feature-server/pull/1585) — a TTS `stream_resumed` event sent to a different hook path than the one it resumed

Open PRs across `livekit/agents`, `pipecat`, `jambonz`, `drachtio-srf` and `drizzle-orm`.

---

### How I find them

Take a bug fixed in one place, then grep every sibling for the same shape — it is almost never alone. Then verify by mutation: revert the fix and confirm the new test fails. A test that passes both ways has told me nothing.

Most of it started with debugging my own production calls: audio arriving out of order, sessions torn down mid-sentence, a connection quietly serving the wrong voice after a settings change.

---

### Building

**Atlas** — an outbound voice agent on Twilio Media Streams and Deepgram. Barge-in, a call-session state machine, outcome classification, dialling constrained to legal calling hours, and SMS/WhatsApp on one session model. ~31,000 lines, solo.

**[InvoiceCheck.in](https://invoicecheck.in)** — GST invoice verification, live with paying users. An 11-point compliance check run before marketplaces reject an invoice, OCR auto-fill, Razorpay pay-per-check.

---

### Stack

`Python` `asyncio` `pytest` · `LiveKit Agents` `Pipecat` `jambonz` `drachtio` · `Twilio Programmable Voice & Media Streams` `SIP` `WebRTC` `Deepgram` · `TypeScript` `NestJS` `Next.js` · `Postgres` `Prisma` `Redis` `BullMQ` · `Docker` `CI/CD`
