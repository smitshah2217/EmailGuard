# EmailGuard — 10-Member Video Presentation Script (Google Meet)

**Format:** 10 members, one by one, each shares their screen and explains one part.
**Total time:** ≈ 22 minutes of speaking + handovers (≈ 25 min with screen-switching).
**Companion docs:** `DEMO_GUIDE.md` (all 18 measured demo results) in this same folder.

> Replace `Member 1 … Member 10` with your names before the call.
> Lines inside `> quotes` are **exact words to say**. Lines in *[brackets]* are screen actions — do not read them aloud.
> Read your part aloud 2–3 times before the Meet. Speak slowly; ~130 words per minute is normal.

---

## 1. Who covers what (whole project, nothing left out)

| # | Member | Topic | Code shown on screen | Live demo | Time |
|---|---|---|---|---|---|
| 1 | ______ | Introduction, problem, architecture, tech stack | GitHub repo + README | — | 2:00 |
| 2 | ______ | Frontend dashboard tour (React UI, all 5 pages) | Running app + `frontend/src` | File **10** (MALICIOUS 44) | 2:00 |
| 3 | ______ | Request flow, API, email parsing, safety-by-design, database | `main.py`, `service.py`, `email_parser.py` | File **03** (attachment), file **09** mention | 2:00 |
| 4 | ______ | SPF check | `spf_checker.py` | Files **01 / 06-dang / 07** | 2:00 |
| 5 | ______ | DKIM verification | `dkim_verifier.py` | **safe/06** vs **dangerous/08** | 2:00 |
| 6 | ______ | DMARC + "reported vs verified" | `dmarc_evaluator.py` | File **04**, file **10** from History | 2:30 |
| 7 | ______ | S/MIME signature verification | `smime_verifier.py` | **16** vs **17** pair | 2:00 |
| 8 | ______ | PGP + body hashes + crypto report | `pgp_verifier.py`, `crypto.py` | Files **15, 13, 14** | 2:00 |
| 9 | ______ | URL analysis (passive + active, look-alike detection) | `url_analyzer.py` + URL scanner | 2 URLs + file **11** mention | 2:30 |
| 10 | ______ | Risk engine, testing & evaluation, limitations, conclusion | `risk/engine.py`, `config.py`, terminal | File **17** factor card + `pytest` | 3:00 |

**Coverage check:** problem ✓ architecture ✓ frontend ✓ API ✓ parser ✓ database ✓ SPF ✓ DKIM ✓
DMARC ✓ S/MIME ✓ PGP ✓ hashes ✓ URL analysis ✓ TLS/redirect probing ✓ risk scoring ✓ tests ✓
evaluation ✓ demo bank (all 18 files appear) ✓ limitations & future scope ✓

---

## 2. Before the Meet — preparation (30 minutes before)

### 2.1 One member (suggest Member 2) prepares the running app
```bash
# Terminal 1 — backend
cd emailguard/backend
python -m venv .venv && source .venv/bin/activate     # Windows: .venv\Scripts\Activate.ps1
pip install -r requirements-dev.txt
uvicorn app.main:app --host 0.0.0.0 --port 8000

# Terminal 2 — frontend
cd emailguard/frontend
npm install
npm run dev -- --host 0.0.0.0          # open the http://localhost:5173 link it prints
```

### 2.2 ⭐ The backup trick (very important)
**Before the professor joins, upload all 18 files from `demo_emails/` once.**
They are saved in **Analysis history**. During the video, if any live upload is slow or DNS
misbehaves, that member simply opens the saved report from History — zero waiting, same screen.

### 2.3 Every member
- [ ] Keep **your demo files** in one folder, ready to drag.
- [ ] Open **your code file** in a browser tab on GitHub (`github.com/smitshah2217/EmailGuard`) at **125–150% zoom** — code must be readable on video.
- [ ] If you demo the app, run it locally (commands above) **or** use Member 2's already-open History reports when it is your turn to share — decide this in rehearsal.
- [ ] Turn on **Do Not Disturb**; close WhatsApp/Telegram/desktop notifications.
- [ ] In Meet, share **"A window"** or **"A tab"**, never "Your entire screen".
- [ ] **Leave the "Probe public links" toggle OFF** in the app — all script numbers assume passive mode.
- [ ] Member 1 (host) starts **recording** and keeps time. Everyone stays muted except the current speaker.
- [ ] When you finish, say your **handover line**, stop sharing, mute. Next member shares immediately. Silent gaps look bad on video — the handover line is the next person's cue.

### 2.4 If a live number differs from the script
SPF/DMARC/DKIM read **live DNS**, so rare small changes are possible. Say:
> "Sir, these three checks read live DNS policies, so a small variation is possible. Our measured
> value, recorded in the demo guide, is ___." — then open the History report. **Never argue with the screen.**

---

## 3. THE SCRIPT

### Member 1 — Introduction, problem & architecture ⏱ ~2:00
*[Share: GitHub repo home page → scroll README slowly → open the `emailguard` folder]*

> "Good morning sir. We are a team of ten, and our project is **EmailGuard** — an email and URL
> security analyser.
>
> The problem we address is simple. In a phishing email, everything the user *sees* can be forged:
> the sender's name, the From address, even headers that claim 'authentication passed'. A normal
> user cannot manually verify SPF, DKIM, DMARC, digital signatures and every link. So EmailGuard
> does it in one place: you upload an `.eml` file, or paste a URL, and it produces an evidence-based
> report with a safety score from 0 to 100 — and, importantly, the **reasons** behind the score.
>
> Two principles run through our whole project, and you will hear them again and again today.
> One: **we verify things ourselves and never trust what the file claims about itself** — every
> result is labelled *independently verified* or *receiver-reported*. Two: **'cannot verify' is
> never shown as 'malicious'** — if a check cannot run, we say exactly that, with zero penalty.
>
> Technically, the frontend is **React with TypeScript**, built with Vite. The backend is **Python
> with FastAPI**, using libraries like dkimpy, pyspf and dnspython, plus OpenSSL for S/MIME.
> Reports are stored in a **SQLite database** through SQLAlchemy. The backend is organised as
> independent analyser modules, and each of us will now explain one layer — starting with the
> dashboard itself. Over to ______ (Member 2)."

**If sir asks** *"Is this a real-time mail filter?"* → "No sir, it analyses uploaded `.eml` files and URLs; it is an analysis/teaching prototype, not a live mail gateway."

---

### Member 2 — Frontend dashboard tour ⏱ ~2:00
*[Share: the running app. Click through pages as you name them. Demo: upload `demo_emails/dangerous/10_danger_lookalike_phishing.eml`]*

> "Thank you. This is our dashboard, built in **React 18 and TypeScript**. It has five pages.
> **Overview** shows live statistics from the database — how many emails were analysed, and how
> many were safe, suspicious or malicious, with charts drawn using Recharts.
> *[Click Email analyzer]*
> The **Email analyzer** is where we upload an `.eml` file. Let me upload a phishing sample.
> *[Upload file 10, wait for the report]*
> The verdict is **MALICIOUS, safety score 44**. Notice the report is not just a number. Here are
> the authentication cards — SPF, DKIM, DMARC, S/MIME, PGP. Here every link in the email is listed
> with its own analysis — this one, `paypa1-security.com`, is flagged as a **look-alike of paypal**.
> And here is the most important card: **'Why this score?'** — every factor with its exact weight:
> look-alike domain plus 25, suspicious URL plus 20, and so on. Nothing is a black box.
> *[Click the remaining pages quickly]*
> The **URL scanner** analyses a single link — Member 9 will demo it. **Analysis history** reopens
> any past report from the database. **How it works** documents our methodology and honest
> limitations, inside the product itself.
>
> The frontend talks to the backend through simple REST calls in `api.ts`, and `types.ts` mirrors
> the backend's report format, so the two sides cannot silently drift apart.
> So — when I clicked 'analyze', what actually happened inside? ______ (Member 3) will explain."

**If sir asks** *"Why React?"* → "Component-based UI, TypeScript type-safety with the API contract, and fast dev cycle with Vite, sir."

---

### Member 3 — Request flow, email parsing & safety-by-design ⏱ ~2:00
*[Share: `backend/app/main.py`, then `service.py`, then `analyzers/email_parser.py`. Demo: upload `demo_emails/safe/03_safe_attachment_metadata.eml`]*

> "When a file is uploaded, it hits our **FastAPI** endpoint `POST /api/analyze/email`. Other
> endpoints are `/api/analyze/url`, `/api/history`, and `/api/dashboard/stats` — the data Member 2
> showed. The API validates strictly: only `.eml` files, **maximum 10 MB** — a bigger file gets
> HTTP 413 — and an unparseable file gets 422 instead of crashing.
>
> `service.py` then runs a fixed pipeline: **parse → authenticate → cryptography → analyse every
> URL → score the risk → save the report**.
> *[Show email_parser.py]*
> My part, the **parser**, uses Python's standard `email` package. It extracts the headers — From,
> To, Reply-To, Return-Path, the `Received` chain and `Authentication-Results` — walks the MIME
> structure, decodes the body, and pulls out every URL from text and HTML links.
> *[Upload file 03]*
> Two safety decisions I want to highlight. First, **attachments are metadata-only** — filename,
> type, size. They are **never opened, extracted or executed**, so analysing a malicious email
> cannot infect the analyser. Second, the **email body is not stored** in the report or database —
> only a character count and hashes, which Member 8 will explain. So the tool never becomes a
> store of private mail.
>
> The parser also supplies small signals. In file 09, the **Reply-To domain differs from the From
> domain** — a classic phishing trick — so replies would go to the attacker; that contributes to
> its SUSPICIOUS 72 verdict.
>
> Now the authentication trio. First, SPF — over to ______ (Member 4)."

**If sir asks** *"What if the .eml is malformed or huge?"* → "10 MB hard limit with 413, parse failures return 422 with a short message; the server never crashes on user input, sir."

---

### Member 4 — SPF ⏱ ~2:00
*[Share: `analyzers/spf_checker.py`. Demo: upload `dangerous/07_danger_spoofed_bank_spf_fail.eml`; mention 01 and 06-dang (open from History if short on time)]*

> "SPF — Sender Policy Framework — answers one question: **was this mail server allowed to send for
> this domain?** The domain publishes a DNS TXT record like `v=spf1 ... -all`, listing its
> authorised servers. We perform a real evaluation with the **pyspf** library, using the connecting
> IP, the envelope sender and the HELO name, against live DNS.
>
> *[Upload file 07]* This email claims to be from ICICI Bank. ICICI publishes a strict `-all`
> policy, and the sending IP is not in their list — so SPF **FAIL**, plus 15 risk. File 06 spoofs
> Gmail, whose policy ends in `~all`, so the result is **SOFTFAIL** — 'probably not authorised' —
> a smaller plus 7. And genuine file 01 **passes**, minus 5.
>
> Now the honest engineering problem. SPF needs the **actual connecting IP of the SMTP session** —
> and an uploaded `.eml` file does not reliably contain it. So our code records **where the inputs
> came from**: from a trusted receiver it is *independent*; supplied by the operator it is
> labelled as such; scraped from the file's own `Received` headers it is labelled
> *extracted-from-headers — can be forged*. If we have nothing trustworthy, we return
> **CONTEXT_UNAVAILABLE** with zero penalty, instead of guessing.
>
> One limit: SPF checks the **envelope** domain, not the From address the user sees, and it breaks
> when mail is forwarded. Connecting it to the visible From is DMARC's job — Member 6. But first,
> the signature check, DKIM — ______ (Member 5)."

**If sir asks** *"Pass, SoftFail, Fail — difference?"* → "The domain's own policy decides: `-all` means hard Fail, `~all` is SoftFail — we score them +15 and +7 respectively, sir."

---

### Member 5 — DKIM ⏱ ~2:00
*[Share: `analyzers/dkim_verifier.py`. Demo: `safe/06_safe_dkim_key_unavailable.eml` then `dangerous/08_danger_tampered_dkim_signature.eml`]*

> "DKIM is a **cryptographic signature** on the email. The sending domain signs selected headers
> and the body with its private key, and publishes the public key in DNS. Anyone can then check:
> did this domain really sign *this exact content*?
>
> We verify with the **dkimpy** library against the **original raw bytes** of the file — never a
> re-saved copy, because even one changed byte breaks the hash, correctly.
>
> Here we found and fixed a subtle bug. dkimpy returns the same `False` for two very different
> situations: 'the public key is **not published** in DNS' and 'the signature **does not match**'.
> Earlier, both became FAIL — meaning we would accuse a sender of forgery when actually we simply
> could not check. Now we perform the DNS key lookup ourselves through dkimpy's hook.
>
> *[Upload safe/06]* This file's selector is not published, so the result is **KEY_UNAVAILABLE** —
> reported honestly, **zero risk**, final verdict SAFE 100.
> *[Upload dangerous/08]* In this file, Gmail's real key **was** fetched — and the signature
> genuinely fails, because the body was tampered after signing. That is a true **FAIL**, plus 15,
> verdict SUSPICIOUS 58.
>
> Same library, same `False` — two completely different meanings. That distinction is our fix.
> A limit: mailing lists and forwarders can legitimately break DKIM, so a failure adds 15 points;
> it never convicts alone. Now, how SPF and DKIM combine under DMARC — ______ (Member 6)."

**If sir asks** *"If DKIM passes, is the email safe?"* → "No sir — it only proves the domain signed that content. A signed phishing email is still phishing; our UI states exactly that."

---

### Member 6 — DMARC + reported vs verified ⏱ ~2:30
*[Share: `analyzers/dmarc_evaluator.py`. Demo: upload `safe/04_safe_untrusted_reported_headers.eml`; reopen file 10 from History]*

> "DMARC connects everything to the address the **user actually sees**. The domain publishes a
> policy at `_dmarc` in DNS — `none`, `quarantine` or `reject`. DMARC passes only if SPF **or**
> DKIM passes **and is aligned** — meaning the authenticated domain matches the visible From
> domain, under relaxed or strict rules which we both implement. We fetch the record with
> dnspython, including the organizational-domain fallback using publicsuffix2. A pass gives
> minus 10, a fail plus 20 — our strongest authentication signal.
>
> Two contributions here. First, a bug we fixed: 'domain publishes **no DMARC record**' — a DNS
> NXDOMAIN — used to fall through and get scored like a failure. That is wrong: many genuine small
> domains simply never publish DMARC. Now that case is **NO_POLICY**, and a failed lookup is
> **DNS_ERROR** — both inconclusive, **zero risk**. But we added a separate, sharper signal: if the
> visible From domain **does not exist at all** — no MX, A, AAAA or NS records — that is a forgery
> indicator: `sender_domain_unresolvable`, plus 10. You saw it in Member 2's file 10.
>
> *[Upload file 04]* Second, the most important idea in our project. This file's headers *claim*
> `spf=pass, dkim=pass, dmarc=pass`. But those headers sit inside a file the **attacker**
> controls — anybody can type them. So EmailGuard labels them **'Reported'**, runs its **own**
> DMARC computation — which **fails** — and the email keeps risk 15. **Reported is not verified.**
>
> Next, certificate-based signatures: S/MIME — ______ (Member 7)."

**If sir asks** *"Why is no-DMARC-record zero but nonexistent-domain plus 10?"* → "No policy is just missing configuration, sir; a domain that cannot receive mail at all is a forged-sender indicator. Two different observations, two different treatments."

---

### Member 7 — S/MIME ⏱ ~2:00
*[Share: `analyzers/smime_verifier.py`. Demo: `crypto/16_smime_signed_selfsigned.eml` then `dangerous/17_danger_tampered_smime_signature.eml`]*

> "S/MIME signs email with an **X.509 certificate**, using the CMS format. Our verifier asks
> OpenSSL **two separate questions**. Question one, with `-noverify`: is the signature
> **mathematically valid** — has the content changed since signing? Question two, with the CA
> bundle: is the certificate **chain trusted**? We also extract and display the signer
> certificate — subject, issuer, validity.
>
> Keeping the two questions separate matters, and this pair of files shows why.
> *[Upload file 16]* This message has a **real signature that verifies** — `signature_valid:
> true`. But the certificate is **self-signed**, so: **CERT_UNTRUSTED**, and **zero risk** — SAFE
> 100. A valid signature proves the message was not altered; it does **not** prove who the sender
> is.
> *[Upload file 17]* Same certificate — but the body was **edited after signing**. Now OpenSSL
> reports the signature broken: **SIGNATURE_INVALID**, plus 25, and the email ends at **MALICIOUS
> 6**.
>
> Honestly, this entire signed path was originally broken in our code: the signature-only command
> was built by deleting items from the full command list, which threw an `IndexError`, so *every*
> signed email showed 'verifier error'. We rebuilt both commands independently, and our automated
> tests now generate a real key and certificate, sign a message, tamper it, and check both
> outcomes — Member 10 will show the tests. What we never do is **decrypt** — that needs the
> recipient's private key, as ______ (Member 8) will explain with PGP."

**If sir asks** *"Why is an untrusted certificate zero risk?"* → "Self-signed certificates are normal inside companies and demos, sir. Untrusted identity is a limitation, not an attack; *tampering* is the attack — that's the +25."

---

### Member 8 — PGP + hashes + crypto report ⏱ ~2:00
*[Share: `analyzers/pgp_verifier.py`, then `analyzers/crypto.py`. Demo: `crypto/15_pgp_signed_detected.eml`; open 13/14 from History]*

> "PGP is the other email cryptography standard, based on a web of trust instead of certificate
> authorities. Our verifier runs **GnuPG in an isolated temporary home directory**, and it only
> trusts keys from a **configured keyring**. Deliberately, we **never auto-import a key that
> arrives inside the email** — otherwise an attacker would simply attach their own key and define
> their own trust, making the check meaningless.
>
> *[Upload file 15]* This message is PGP-signed. On this machine `gpg` is not installed, so the
> result is **GPG_UNAVAILABLE** — detected, reported honestly, **zero penalty**. With gpg and the
> keyring configured, the same code returns PASS, key-untrusted, or signature-invalid. Files 13
> and 14 are **encrypted** — S/MIME and PGP. Both are reported as *encrypted, not decrypted*: we
> cannot, and should not, decrypt someone else's mail without the recipient's private key.
>
> *[Show crypto.py]* Finally, my module assembles the report's cryptography section and computes
> **SHA-256 and SHA-512 hashes of the decoded body**. I want to be precise about what a hash
> proves: **only that the bytes have not changed**. It is an integrity fingerprint for comparing
> two copies of a message. It does **not** authenticate the sender — anyone can hash anything.
> Our report states this scope explicitly, because mislabelling a hash as 'authentication' is a
> very common project mistake, which we were careful to avoid.
>
> Authentication covered, signatures covered — what about the links inside the mail? ______
> (Member 9)."

**If sir asks** *"What does the hash prove, then?"* → "Integrity only, sir — that two copies are byte-identical. Identity comes from DKIM/S/MIME/PGP, not from a hash."

---

### Member 9 — URL analysis ⏱ ~2:30
*[Share: the app's URL scanner page, then `analyzers/url_analyzer.py`. Demo: paste both URLs]*

> "Every link extracted from an email — and any URL pasted into this scanner — goes through the
> same analyser. By default it is **passive**: it makes **zero network requests** and only
> examines the URL string itself. We check: only http/https allowed; international domains
> converted to punycode so homoglyph tricks become visible; suspicious keywords like *login* or
> *verify*; raw IP addresses; private or loopback targets; unusually deep subdomains;
> percent-encoding — file 12 hides the letter 'v' as `%76` in 'verify'; non-standard ports; and
> plain HTTP.
>
> The flagship check is **look-alike domain detection**. We take the domain, undo common tricks —
> digit `1` becomes letter `l`, `0` becomes `o`, and so on — then fuzzy-match it with
> **rapidfuzz** against 15 real brand domains, flagging similarity of 0.77 or higher, while
> always excluding the genuine brand domains themselves.
>
> *[Paste `https://paypa1-security.com/login`]* Result: **MALICIOUS 41** — recognised as similar to
> paypal. *[Paste `https://icicibank.com/login`]* The **real** bank with a login path: **SAFE
> 86**. A keyword alone is a weak signal worth only plus 6 — it can never convict by itself.
>
> There is an optional **active mode**, off by default behind two switches, which sends a HEAD
> request to inspect redirects and the TLS certificate. Its guardrails: DNS is resolved first and
> if **any** address is private or loopback, the probe is **refused before connecting** — that is
> SSRF protection; you can see it refuse the `127.0.0.1` link in file 11. Standard ports only,
> 2.5-second timeout, maximum 3 redirects, maximum 3 probed links per email. And our report
> reminds the user: a valid TLS certificate only encrypts — it does not make a site trustworthy.
>
> Honest limit: we never download or render the page and use no blocklists, so a clean-named,
> brand-new phishing domain can pass these passive checks. Finally — how do all these signals
> become one score? ______ (Member 10)."

**If sir asks** *"Your scanner itself could be abused to attack internal servers?"* → "That's SSRF, sir — we resolve DNS first and refuse any non-public address before opening a socket, allow only ports 80/443, and cap redirects and timeouts."

---

### Member 10 — Risk engine, testing, limitations & conclusion ⏱ ~3:00
*[Share: file 17's report (History) → `app/config.py` weights → `risk/engine.py` → terminal]*

> "Everything you have seen ends in my module, the **risk engine**. It sums transparent,
> configurable weights — *[show config]* these are the actual defaults, and they can be overridden
> by an environment variable. Repeated signals are capped — for example `suspicious_url` counts at
> most twice, so fifty bad links cannot inflate the score without limit. Risk is clamped between
> 0 and 100; **safety equals 100 minus risk**. Safety 80 or above is SAFE, 50 or above
> SUSPICIOUS, below that MALICIOUS, with recommendations ALLOW, WARN and QUARANTINE.
>
> *[Show file 17 factors]* File 17, end to end: invalid signature 25, look-alike domain 25,
> suspicious URL 20, nonexistent sender domain 10, limited evidence 8, sensitive keywords 6 —
> total risk 94, safety **6**. Every point is accounted for. And note what is *absent*: every
> 'cannot verify' status — NO_POLICY, KEY_UNAVAILABLE, GPG_UNAVAILABLE, CERT_UNTRUSTED —
> contributes exactly **zero**.
>
> *[Run: `python -m pytest -q`]* Our evidence: **60 automated tests, all passing**, including
> tests that generate a real RSA key, really sign a message with OpenSSL, tamper it, and verify
> the detection. Our evaluation script scores four labelled fixtures — 82, 82, 58 and 44 — all
> classified correctly, at roughly 55 milliseconds per email. We state clearly: that small set is
> **illustrative, not a benchmark**. We also built an 18-file demo bank with a measured guide —
> including how results degrade honestly with **no internet**.
>
> Limitations, plainly: the weights are documented starting points, not calibrated probabilities;
> an uploaded file alone cannot prove SPF; we do no machine learning, no reputation feeds, no
> attachment sandboxing. Future scope: deploy behind a real trusted mail receiver, add threat-
> intelligence feeds, and an ML classifier alongside — not instead of — these explainable rules.
>
> To conclude: EmailGuard verifies before it trusts, explains every point it awards, and says
> 'I cannot verify this' instead of guessing. Thank you, sir. We welcome your questions — each of
> us will answer for our own part."

**If sir asks** *"Are your weights scientifically derived?"* → "No sir — they are documented, configurable engineering choices. Our contribution is that every point is visible and tunable, not hidden inside a model."

---

## 4. Q&A routing — who answers what

| If the question is about… | Member who answers |
|---|---|
| Overall idea, scope, architecture, tech choices | 1 |
| UI/UX, pages, charts, React/TypeScript | 2 |
| API, file limits, parsing, attachments, privacy/database | 3 |
| SPF, DNS records, `~all` vs `-all`, forwarding | 4 |
| DKIM, signatures, KEY_UNAVAILABLE vs FAIL | 5 |
| DMARC, alignment, policies, forged headers | 6 |
| S/MIME, certificates, OpenSSL, CERT_UNTRUSTED vs INVALID | 7 |
| PGP, keyrings, encryption, hash meaning | 8 |
| URL checks, look-alikes, SSRF, TLS limits | 9 |
| Scoring/weights, thresholds, tests, accuracy claims, future scope | 10 |

**Golden rule for Q&A:** if you don't know, say "Sir, that is outside what we implemented; our
report lists it under limitations." Never invent a capability.

## 5. If sir gives you only ~15 minutes (cut list)

Keep all 10 speakers, cut *demos*, not people:
- M2: skip Overview detail, go straight to file 10.
- M3: drop file 09 mention; keep file 03.
- M4: upload only file 07; mention 01/06 verbally.
- M6: open file 04 and 10 **from History** (no uploads).
- M8: open all three files from History.
- M9: one URL only (`paypa1-security.com/login`); state the icici result verbally.
- M10: show pytest result as a pre-run terminal or screenshot instead of running live.

## 6. Final checklist (host reads this before recording)

- [ ] Backend + frontend running; `/api/health` shows `"status":"ok"`.
- [ ] All 18 demo files pre-uploaded → History ready as backup.
- [ ] "Probe public links" toggle **OFF**.
- [ ] Each member: demo folder ready, code tab open at 125–150% zoom, notifications off.
- [ ] Recording ON; everyone muted except current speaker.
- [ ] Names filled into this script; handover lines name the next member.
- [ ] Three lines everyone can fall back on:
  1. "We **verify** results ourselves; we never trust what the file claims."
  2. "**Cannot verify** is never scored as **malicious**."
  3. "It is an **explainable prototype**, not a calibrated detector — and the report says so."
