# ◊ HONESTLY · verified search

> A sovereign single-file answer engine.
> Every factual claim 3-vote adversarially verified before you read it. Refuted claims listed openly. Konomi-signed answer envelopes. MIT.
> **Perplexity tells you. We prove it.**

**Live:** https://sjgant80-hub.github.io/honestly

---

## What it does

Ask a question · HONESTLY:

1. **Searches** the web via your BYOK key (Tavily / Brave / Serper / Exa · all free tiers work)
2. **Extracts** factual claims from the sources via your BYOK LLM (Claude / OpenAI / Gemini)
3. **Adversarially verifies** each claim with 3 independent passes · different framings (refute / confirm / neutral)
4. **Signs** the final answer envelope against your Konomi Ed25519 keypair
5. **Shares** as a base64-encoded URL that anyone can cryptographically verify against your public key

Claims that survive get rendered. Claims that don't get listed openly under "❌ refuted" · the same honesty pattern that won attention for [nhs-reinjection](https://github.com/sjgant80-hub/nhs-reinjection).

---

## Why it kills Perplexity

| | Perplexity | HONESTLY |
|---|---|---|
| price | $20/mo Pro | $0 · MIT · BYOK keys |
| hallucination | known issue | 3-vote adversarial · refuted listed openly |
| privacy | their server logs queries | sovereign · your keys · zero backend |
| sharing | their URL (their data) | Konomi-signed envelope · cryptographically yours |
| trust | their brand | every claim's verifier-votes shown · provenance hashable |

The killer angle isn't UX. It's the **honesty layer**. Perplexity sounds confident about claims that don't survive scrutiny. HONESTLY makes the scrutiny part of the deliverable.

---

## Setup (~2 minutes)

1. Open https://sjgant80-hub.github.io/honestly
2. Settings tab · paste a search API key (Tavily 1000/mo free is recommended) + an LLM key (Claude/OpenAI/Gemini)
3. Save
4. Ask anything

(Or hit **load demo answer** on the home page to see the flow with placeholder data · no keys needed.)

---

## Search providers (pick one, BYOK)

| Provider | Free tier | Why pick it |
|---|---|---|
| **Tavily** | 1000/mo | best default · clean API · LLM-aware results |
| **Brave** | 2000/mo | most generous free tier · neutral search |
| **Serper** | 2500/mo | Google's index via API |
| **Exa** | 1000/mo | semantic search · best for research queries |

---

## LLM providers (pick one for synthesis, optional second for verification)

| Provider | Why |
|---|---|
| **Anthropic Claude** | best reasoning · use Sonnet for synthesis, Haiku for verification |
| **OpenAI GPT** | broad knowledge · GPT-4o for synthesis, 4o-mini for verification |
| **Google Gemini** | cheapest at scale · Flash for synthesis, Flash-Lite for verification |
| **WebLLM offline** | 2GB Llama download · works without internet after first load (v1.1 wiring) |

Splitting synthesis (premium model) from verification (cheap model) cuts per-answer cost ~70% with no quality loss.

---

## The audit chain

Every answer produces an envelope:

```json
{
  "envelope": {
    "kind": "honestly-answer-v1",
    "issuer": "<your-public-key-hex>",
    "issuedAt": "ISO timestamp",
    "question": "...",
    "claimsTotal": 7,
    "claimsConfirmed": 5,
    "claimsRefuted": 1,
    "finalHash": "<sha256>",
    "sources": [{ "title": "...", "url": "..." }],
    "summary": ["claim 1", "claim 2", "claim 3"]
  },
  "signature": "<ed25519-detached-hex>"
}
```

Anyone with your public key can verify the signature:

```js
const sodium = await import('https://esm.run/libsodium-wrappers').then(m => (m.default || m).ready.then(() => m.default || m));
const ok = sodium.crypto_sign_verify_detached(
  sodium.from_hex(envelope.signature),
  sodium.from_string(JSON.stringify(envelope.envelope)),
  sodium.from_hex(envelope.envelope.issuer)
);
```

If `ok === true`, this exact answer was minted by this exact identity at this exact time. No platform can revoke it. No server has to be trusted.

---

## Pro-slot · for builders

The engine is 3 swappable functions documented in [`src/pro-slot.md`](./src/pro-slot.md):

| Slot | What | Replace with |
|---|---|---|
| `search()` | source fetch | your own scraper · MCP web-search tool · custom corpus |
| `synthesise()` | claim extraction | your fine-tuned model · domain-specific extractor |
| `verify()` | adversarial 3-vote | ensemble of multiple models · human-in-the-loop · specialist verifier |

Drop in your engine, keep the UI/audit/signing.

---

## Roadmap

- **v1.0** (you're here) · single-file PWA · all 5 phases working · BYOK keys · mock fallback
- **v1.1** · WebLLM offline rules-verifier · 2GB Llama in browser
- **v1.2** · `honestly-worker` · Cloudflare Worker that does search+synth+verify so the user only needs ONE backend key (theirs) and the front-end stays public
- **v1.3** · `honestly-sdk` · npm ESM package · the pipeline reusable in any context
- **v1.4** · `honestly-shim` · CDN `<script>` adds "verified search" to any site
- **v1.5** · `honestly-mcp` · MCP stdio server · Claude Code / Cursor call HONESTLY mid-build
- **v1.6** · `honestly-obsidian` · plugin · run a query, paste cited claims into your vault
- **v2.0** · federated verifier network · multiple Konomi-signed verifier peers vote on claims (the actual web-of-trust pattern)

Same playbook as GroundLevel · the PWA is the seed.

---

## Sister tools

HONESTLY is built on the same audit-chain + Konomi doctrine as:

- [groundlevel](https://github.com/sjgant80-hub/groundlevel) · sovereign legal toolkit
- [operator](https://github.com/sjgant80-hub/operator) · structured strategy consulting
- [nhs-reinjection](https://github.com/sjgant80-hub/nhs-reinjection) · the NHS s.172 thesis
- [kard](https://github.com/sjgant80-hub/kard) · sovereign card game (Ed25519 trophies)
- [fall-substrate](https://github.com/sjgant80-hub/fall-substrate) · research → tool pipeline
- [fall-verify](https://github.com/sjgant80-hub/fall-verify) · adversarial verification endpoint

Your Konomi keypair is the same one signing trophies in KARD, engagements in OPERATOR, case files in GroundLevel. One identity. Every tool. Sovereign.

---

## Caveats

- **Not omniscient.** A 3-vote LLM verifier can still get fooled by sources that uniformly misrepresent something. Refuted-claims-listed-openly is the partial answer · ultimate truth still requires human judgment.
- **CORS-limited fetching.** v1.0 uses snippets returned by the search API · no full-page fetches in browser. v1.2 worker enables full-page reads.
- **Konomi seed is your responsibility.** Wipe browser data without exporting · identity is gone.

---

## Licence

MIT · use freely · fork freely · re-skin freely.

**◊·κ=φ⁴ · Perplexity tells you · we prove it · the verification IS the deliverable.**
