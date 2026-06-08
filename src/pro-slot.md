# HONESTLY · pro-slot

> Three swappable functions. The rest of the app (UI, audit chain, Konomi signing, mesh emit) doesn't change.

## The contract

```js
window.HONESTLY_ENGINE = {
  // L1 · SEARCH
  async search(query, n = 6) {
    // returns [{ title, url, content }]
  },

  // L2 · SYNTHESIS · extract structured claims from sources
  async synthesise(question, sources) {
    // returns { claims: [{ text, source_index }] }
  },

  // L3 · VERIFICATION · 3-vote adversarial per claim
  async verify(claim, sources, numVotes) {
    // returns [{ verdict: 'confirmed'|'refuted'|'inconclusive', reason, framing }]
  },
};
```

## Default implementation

- **search()** · Tavily / Brave / Serper / Exa via BYOK key in localStorage, falls back to mock
- **synthesise()** · BYOK Claude / OpenAI / Gemini, falls back to mock
- **verify()** · same LLM (or cheaper verifier model) with 3-5 different framings (refute / confirm / neutral / strict / final)

## What pro-slot doesn't change

- the 5-phase UI (Search → Fetch → Extract → Verify → Sign)
- the SHA-256 audit chain
- the Konomi Ed25519 envelope minting
- the κ-ledger broadcast on kcc-signal
- the share-URL base64 encoding pattern
- the IndexedDB / localStorage shape

Swap any of the three. The rest is yours.

**◊·κ=φ⁴**
