## Hi, I'm Tirth Shah

High school senior in Clarksburg, MD, finishing an A.A.S. in Cloud Computing & Networking at Montgomery College (4.00 GPA) through dual enrollment. I build full-stack, AI, and embedded systems, and I care most about the parts that have to be right: data accuracy, payments, and failure handling.

[LinkedIn](https://www.linkedin.com/in/tirth-shah-4923a3404) · tirthashah@gmail.com

---

### PatientLens: meal photo to a validated diet-quality score

*Private repository. Described here without proprietary code or study materials.*

**Problem.** Colon cancer survivors are told to eat better, but most nutrition apps only count calories. Clinicians care about diet *quality*, which is measured with the USDA's Healthy Eating Index (HEI). Scoring HEI by hand from food logs is slow, and patients rarely do it.

**What I built.** I designed it in Figma and built it solo:
- An Expo mobile app with scan, history, trends, insights, and profile tabs
- An Express API and an admin review dashboard
- The analysis pipeline, which works in four steps:
  1. A vision model (Gemini 2.5 Flash) identifies the foods in a photo and returns structured JSON.
  2. The server validates that JSON and matches each food to a USDA FNDDS code in a 7,083-food dataset.
  3. It sums nutrients by portion.
  4. A standalone HEI-2010 library scores the meal across 12 components.
- A follow-up step that finds the patient's weakest component and asks for one specific food swap instead of generic advice

**Hardest technical decision.** I didn't use the model's nutrient estimates. Vision models are good at naming food and bad at numbers, so the model only *identifies* food and every number comes from USDA reference data. Scoring is also density-based (per 1,000 kcal), not quantity-based, which made missing calorie data a real problem. Dividing by an unknown total would inflate a score. I chose an explicit 500 kcal fallback plus per-100g calorie and sodium fallbacks, so a partial result stays bounded and predictable instead of silently wrong.

**How I tested it.**
- Unit tests check the HEI scoring rules against the official formulas.
- I checked food matching by hand on 50+ real meals, focusing on composite and regional dishes where the matches drifted.
- The API returns distinct responses for invalid input (400), no food detected (422), and upstream rate limiting (429), so each failure can be handled and logged on its own.

**What I learned.** It matters where the truth lives. The model's job is narrow, the reference data is the source of truth, and every fallback is a choice I have to document and defend. I'm now drafting a study design and IRB materials with an oncology fellow at MedStar Georgetown.

`TypeScript` `React Native / Expo` `Node.js / Express` `PostgreSQL` `Gemini API` `Python` `Figma` `pnpm monorepo`

---

### PTK Chapter Platform & Stripe Payments API

**Problem.** My Phi Theta Kappa chapter (80+ members) ran on scattered documents and collected dues by hand. Officers needed to update the site without a developer, and the chapter needed a way to take payments that couldn't double-charge anyone or lose a record.

**What I built.** I'm the only developer.
- The live public site has 11 pages.
- An admin CMS with 11 views lets officers manage applications, events, posts, scholarships, and the contact inbox.
- A serverless REST API runs on Netlify Functions and stores data in Netlify Blobs.
- Dues are collected through Stripe Checkout:
  - Prices live on the server, and the client only sends an item ID.
  - Every checkout request needs an idempotency key, so a retry or double-click returns the same session.
  - A webhook with verified signatures records the outcome.

**Hardest technical decision.** I treated Stripe webhooks as at-least-once and out of order, not as a clean sequence of events. The handler dedupes by event ID and never moves a payment from `paid` back to `expired`. It moves delayed methods from `processing` to `paid`, and it records any session whose total doesn't match the catalog as `amount_mismatch` instead of counting it. The webhook reads the raw request body before anything parses it, because the signature covers the exact bytes.

**How I tested it.** 14 offline unit tests run with fake keys and never contact Stripe. They cover server pricing, input validation, required idempotency keys, valid versus tampered signatures, duplicate and out-of-order events, amount mismatches, and forged or expired admin tokens.

**What I learned.** Payment code is mostly about the unhappy paths. Most of the work went into the cases where requests repeat, arrive late, or get tampered with. I also replaced a weak admin check with signed, expiring tokens after realizing the old one trusted any token with the right prefix.

`React` `TypeScript` `Vite` `Tailwind` `Radix UI` `TanStack Query` `Zod` `Netlify Functions` `Stripe API`

---

### Also

- **TS Vault**: about 700 lines of C++ firmware for an Arduino room-security system. It runs as a five-state machine and combines a motion sensor with a calibrated proximity sensor. It has a touch PIN pad and a hand-written HTTP server for phone control.
- **JoinTheBridge**: a registration platform built with Next.js and Supabase for a financial-literacy initiative. Row-level security lets the public submit registrations while only organizers can read them.
- **Certifications**: CompTIA Network+, A+, and IT Operations Specialist; C-TECH Copper & Fiber. Security+ is in progress.
