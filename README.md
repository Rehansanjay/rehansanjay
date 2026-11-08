## Rehan Sanjay Venkatesan

Voice AI and backend engineer. I work on the frameworks voice agents run on, and on the failures that only show up on a real phone call.

**Thirty-three fixes merged upstream, across eleven repositories** — twelve into [`livekit/agents`](https://github.com/livekit/agents), four each into [`cosmo`](https://github.com/socratic-ai/cosmo-ai) and [`pipecat-gnani`](https://github.com/Gnani-AI-Mintlify/pipecat-gnani), four into [`pipecat`](https://github.com/pipecat-ai/pipecat), two each into [`jambonz`](https://github.com/jambonz/jambonz-feature-server) and [`SitePing`](https://github.com/NeosiaNexus/SitePing), one each into [`drachtio`](https://github.com/drachtio/drachtio-srf), [`pipecat-respeecher`](https://github.com/respeecher/pipecat-respeecher) and [`twenty`](https://github.com/twentyhq/twenty), and one each into the [Varis SDK](https://github.com/usevaris/sdk-ts) and [CLI](https://github.com/usevaris/cli).

📍 Chennai, India · 2026 CS graduate · available for paid contract work, and open to a first engineering role
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
- [#7341](https://github.com/livekit/agents/pull/7341) — LiveAvatar built its own `aiohttp` session instead of the job's shared one, so the session outlived the job that owned it
- [#7237](https://github.com/livekit/agents/pull/7237) — respeecher retired whole connection pools on a model change, leaving idle sockets for the old model open and orphaning a stream's socket in a pool nothing closes
- [#7052](https://github.com/livekit/agents/pull/7052) — a realtime session tracked and cancelled four background tasks and discarded two more, which the loop holds only weakly. Collected early, a `generate_reply(user_input=...)` waits forever; surviving past `aclose()`, it sends on a stream that is gone
- [pipecat#5632](https://github.com/pipecat-ai/pipecat/pull/5632) — an STT service rebuilt its config on a settings change, but the running gRPC stream took its config once at open, so a mid-session language change kept transcribing with the old one
- [pipecat#5464](https://github.com/pipecat-ai/pipecat/pull/5464) — a TTS service wrote model, voice and language only into its websocket init message, so a runtime change was stored, warned about, and silently never sent
- [pipecat#5762](https://github.com/pipecat-ai/pipecat/pull/5762) — an image service left its GenAI client open on shutdown. It had sat unmergeable for five weeks after upstream rewrote the request path underneath it; I rebased onto the rewrite, kept their version and re-applied the lifecycle hooks, and it merged within a day
- [pipecat#6521](https://github.com/pipecat-ai/pipecat/pull/6521) — a telephony serializer raised on any transport message that was not a dict, so one malformed frame from the provider took the call down instead of being ignored

**SIP and telephony**

- [pipecat#6573](https://github.com/pipecat-ai/pipecat/pull/6573) — Pipecat's telephony serializers covered Twilio, Telnyx, Plivo, Exotel, Genesys and Vonage, and not jambonz, so a jambonz call could not drive a Pipecat agent at all. The `listen` verb negotiates sample rate and mix type per call and states them in a JSON preamble before any audio, so the serializer reads them off the connection rather than taking them as config, and refuses a `mixType` it cannot play rather than returning audio at the wrong speed. jambonz commissioned it.
- [jambonz#1585](https://github.com/jambonz/jambonz-feature-server/pull/1585) — a TTS `stream_resumed` event sent to a different hook path than the one it resumed
- [jambonz#1584](https://github.com/jambonz/jambonz-feature-server/pull/1584) — a call transferred between feature servers re-sent trying, ringing and in-progress statuses the application had already received
- [drachtio-srf#240](https://github.com/drachtio/drachtio-srf/pull/240) — proxied responses built their header strip list from the request option, so the wrong headers were stripped and forwarded

**In vendors' own SDKs** — the plugin health check finds these, then I send the fix

- [`pipecat-gnani`](https://github.com/Gnani-AI-Mintlify/pipecat-gnani) — four merged: a renamed import that broke the package outright, a TTS websocket that never reconnected after an interruption, STT sockets leaked on failed handshakes, and support for two newer models
- [`pipecat-respeecher`](https://github.com/respeecher/pipecat-respeecher) — the renamed-import break

Open PRs across `livekit/agents`, `pipecat`, `litellm` and `drizzle-orm`, plus fixes still open with Simplismart, Rumik, SonexLabs, Replicate, Supertonic, Maya Research and Cosmo.

### Measured, not argued

**[pipecat-plugin-health](https://github.com/Rehansanjay/pipecat-plugin-health)** — a nightly check that installs every community Pipecat plugin in the docs, imports it and constructs every service class it defines, against the latest release and against `main`, so a breaking change shows up before users hit it. It now tracks 75 plugins: as of 6 November, against pipecat 1.15, six are broken, three cannot be installed at all and eight are pinned to an older pipecat. Four of the six broke on the same renamed import, and every broken plugin has a fix open or merged. The two vendors who took the fix, Gnani and Respeecher, both read green today; the rest have sat open since September, which is its own finding about where a fix is worth sending.

It also taught me that a released wheel answers the user's question and not the maintainer's: it called one maintainer's plugin broken for two weeks after they had fixed it, because PyPI still served the old version. It now builds from each plugin's own repository too, guarded so that a wrongly resolved repository reports `unknown` rather than crediting a fix to the wrong codebase — [write-up](https://github.com/Rehansanjay/pipecat-plugin-health/blob/main/reports/2026-10-18-the-released-wheel-is-not-the-truth.md).

**[livekit-stt-halfopen](https://github.com/Rehansanjay/livekit-stt-halfopen)** — how long each of the nineteen streaming STT plugins in `livekit/agents` takes to notice a websocket that went half-open: the provider vanished without a FIN or an RST, so nothing arrives and nothing fails. Three fail over in about 46 seconds. Two detect it and tell no one — one reconnects into the same dead state every 46 seconds forever, which looks healthy and leaves the caller unheard for the whole call. Eight never notice.

It overturned three of my own published claims. I had written off baseten, fireworksai and gradium as already safe because each wraps its read in `asyncio.wait_for(..., timeout=5)` — but the handler just `continue`s, so it polls rather than bounding anything, and all three sat at 75 seconds. That is what the measurement was for.

**[livekit-tts-halfopen](https://github.com/Rehansanjay/livekit-tts-halfopen)** — the same question for the twenty streaming TTS plugins, where a dead socket means the agent goes mute rather than deaf, which a caller reads as a dropped line. Nine fail over inside the timeout they were given, three eventually, and four never notice at all.

The answer turned out to be the opposite one. I expected `heartbeat=` to decide it again and started by scanning for it — 18 of the 20 pass none. That predicts almost nothing: what separates the groups is whether a read timeout reaches `ws.receive()`. `fishaudio` has a heartbeat and no read timeout, so it takes 46.8 s where `cartesia` takes 10.0 s with no heartbeat at all. The workload is why: a quiet caller is normal for STT, so a read timeout is the wrong instrument there, while a TTS stream handed text that returns nothing is never normal. The four that never notice are fixed in [#8035](https://github.com/livekit/agents/pull/8035).

---

### How I find them

Take a bug fixed in one place, then grep every sibling for the same shape — it is almost never alone. Then verify by mutation: revert the fix and confirm the new test fails. A test that passes both ways has told me nothing.

Most of it started with debugging real calls on my own voice agent: audio arriving out of order, sessions torn down mid-sentence, a connection quietly serving the wrong voice after a settings change.

---

### Building

**Atlas** — an outbound voice agent on Twilio Media Streams and Deepgram. Barge-in, a call-session state machine, outcome classification, dialling constrained to legal calling hours, and SMS/WhatsApp on one session model. ~31,000 lines, solo.

**[InvoiceCheck.in](https://invoicecheck.in)** — GST invoice verification, live. An 11-point compliance check run before marketplaces reject an invoice, OCR auto-fill, Razorpay pay-per-check.

---

### Stack

`Python` `asyncio` `pytest` · `LiveKit Agents` `Pipecat` `jambonz` `drachtio` · `Twilio Programmable Voice & Media Streams` `SIP` `WebRTC` `Deepgram` · `TypeScript` `NestJS` `Next.js` · `Postgres` `Prisma` `Redis` `BullMQ` · `Docker` `CI/CD`
