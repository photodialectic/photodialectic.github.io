# Context: cryptography-and-auth talk rework

## File
`/workspace/photodialectic.github.io/talks/cryptography-and-auth/index.html` — a Jekyll `slidepost` talk page. Live-reloading via the `keen_banach` (jekyll/jekyll) Docker container on port 4000; verify changes with:
```
sudo docker exec keen_banach curl -s http://localhost:4000/talks/cryptography-and-auth/
```

## Visual language established (reused across slides)
- **`.secret-stage` / `.secret-lane`** — full-width 3-column grid: `Alice zone | public space | Bob zone`.
- **`.secret-zone`** — bordered box; default has a **thick orange "wall"** border (`border-right`/`border-left: 3px solid #ff8800`) facing the public space, representing a private/protected side. Modifier **`.open`** removes the wall (thin border instead) for a side holding no secret.
- **`.secret-actor`** — bold name label (Alice/Bob).
- **`.secret-line`** — plain equation-style text lines; modifiers: `.payload` (heading color), `.secret-value`/`.key-chip` (orange, private), `.signature` (blue, computed), `.received` (green, arrived-from-network data).
- **`.secret-op`** — small muted connector text (`+`, `=`, labels like "verify with public key").
- **`.key-chip`** (`.public`/private default, `.pending` for fade-in-on-fetch) — small bordered chip for key icons (🔒 private / 🔓 public). `.key-shield` gives a pulsing glow.
- **`.secret-public` / `.secret-channel` / `.trust-packet` / `.fetch-request`** — the public-space column, with animated dot/chip travel. Key classes: `.sending` (full-span one-shot, Alice↔Bob), `.fetch-out`/`fetchRequestOnce` (center↔Bob half-span, one-shot, used for "Bob fetches the public key"), `.leg-in`/`.leg-out` (MITM two-leg relay), `.looping`/`.looping-full` (ambient infinite loops, no JS needed).
- **`.sign-result`** (`.valid` green / `.invalid` red / `.unknown` muted) — final verdict line.
- **`.mitm-attacker-box`** — red-bordered box for an untrusted third party (MITM intermediary, or signature forger) sitting *in the public space*, never inside a private zone.
- Shared JS helpers (hoisted, reused everywhere): `sleep()`, `truncateBase64()`, `bufferToBase64()`, `hashText()`, `hmacText()`, `getEncryptionKeys()` (RSA-OAEP), `getSigningKeys()` (RSASSA-PKCS1-v1_5), and a generic `sendPacket(el, duration, className)` that resets an element to its base class then plays a one-shot animation.

## Slide-by-slide changes made (in order)
1. **Front matter title**: → "Authentication and Cryptography".
2. **Slide 1 (intro)**: Replaced the old login/OIDC-specific opener with **"Trust Is Hard on the Internet"** — frames the two core questions (authentication + integrity) using Alice/Bob with **no walls yet** (problem, not solution), ambient looping message + `? ? ?` unknown result.
3. **"Shared Secrets Authenticate a Payload" (HMAC)**: Rebuilt as Alice (walled) | public network | Bob (walled), **both showing the identical `🔒 winter-bridge-47` secret**. Real `crypto.subtle` HMAC. Sequence: Alice types → signs (blue) → packet sends → Bob's payload appears instantly in green → Bob computes his own signature (blue) → *then* reveals Alice's sent signature (green) for comparison → MATCH.
4. **"A Man in the Middle Is Detected"**: Same Alice/Bob/wall structure, with a red **Intermediary** box sitting in the public space (no wall access). Two-leg packet relay: intercepts, rewrites `reader→writer`, forwards Alice's stale signature. Bob computes fresh HMAC on the altered payload → MISMATCH. All real crypto, no more hard-coded fake hex.
5. **"Public and Private Keys"**: Alice (walled, `🔒 private key`, pulsing) | public space with a static `🔓 public key` | Bob (open zone). Animation reworked per feedback into a **request/response "fetch"**: a small dot travels Bob→key, then the labeled key copy travels key→Bob — explicitly NOT Alice pushing it out.
6. **"Encryption: Public Locks, Private Unlocks"**: Continues the story — Bob fetches the public key (same fetch animation), types a message, encrypts it (real RSA-OAEP), sends ciphertext right-to-left across the network, Alice receives (green) and decrypts with her never-moved private key.
7. **"Signing: Private Signs, Public Verifies"**: Bob fetches the public key; Alice signs (real RSASSA-PKCS1-v1_5) and sends payload+signature to Bob → VALID. Then a **Forger (no private key)** box appears *in the public space only* (never Alice's zone), sends a fake message with random bytes as a fake signature → Bob verifies with the same public key → INVALID. This is the trust punchline.
8. Cleaned up all now-dead legacy CSS along the way (`.mitm-flow/.mitm-node/...`, `.key-visibility/.key-vault/.key-term/...`, `.hmac-parties/.hmac-party/...`, `.sign-actor`, `.auth-diagram/.auth-box/.pulse/.delay`), keeping only what's still referenced (`.auth-arrow`/`.auth-caption` still used later; `.hash-line`/`.hash-demo` still used by the standalone SHA-256 slide).

## Verification pattern used throughout
After each edit: curl the rendered page from inside the jekyll container → check `<div>`/`</div>` counts balance → extract the `<script>` block → `node --check` it for syntax validity → grep for expected new element IDs/classes.

Example:
```bash
sudo docker exec keen_banach curl -s http://localhost:4000/talks/cryptography-and-auth/ > /tmp/opencode/out.html
python3 -c "
s = open('/tmp/opencode/out.html').read()
print('open divs:', s.count('<div'))
print('close divs:', s.count('</div>'))
"
grep -n '^<script>\|^</script>' /tmp/opencode/out.html
# then sed out the script block and: node --check extracted.js
```

## Not yet touched (still original/untouched)
- "Three Cryptographic Jobs", "Hashing Detects Changes" (SHA-256 typing demo) — untouched, still uses `.hash-demo`/`.crypto-grid`.
- "Signatures Bind Data to an Issuer", "JWT: A Signed Envelope", "Claims Need Context", "Finding the Right Public Key" (JWKS), "The OpenID Connect Flow", "Issue Your Own Session" — untouched since early in the session (only "Appendix: RSA Math" got expanded with encrypt/decrypt math early on).
- No git commits have been made — all changes are local file edits only, per this repo's policy (git is local-tracking only, no operations performed by the agent).

## Environment notes
- This is a Claudex container; the workspace-level Bash tool runs in the **outer** container, not inside the `jekyll/jekyll` service container. Use `sudo docker exec keen_banach <cmd>` to run curl/node checks against the live-reloaded site, since `curl localhost:4000` from the outer container fails (network isolation).
- Do not install browsers/Chromium for verification — static reasoning + the check pattern above has been sufficient and is preferred (confirmed with the user earlier in this session).
- Git operations (add/commit/push) should not be performed unless explicitly requested — this repo's AGENTS.md reserves git management for the user.
