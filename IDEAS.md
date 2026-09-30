# BNB Hack: Tokenized Stocks Edition: two ideas, backed by data

> **TL;DR**
>
> | | **Idea 1: PARITY** | **Idea 2: STIPEND** |
> |---|---|---|
> | One line | *Shares, not tokens.* A best-execution layer that converts Ondo, bStock and xStocks into real share units, sends each order to the issuer with the best true price, and blocks trades that fall into data traps. | *The AI wealth manager paid only from your dividends.* An Agent Studio agent whose entire income is a capped cut of dividends, measured on-chain from share multipliers and enforced by a vault contract. |
> | Blind spot it attacks | Unit of account and data integrity. The same ticker means 10× different amounts of stock depending on the issuer, and the APIs disagree with the chain. | Nobody uses the multiplier as a dividend ledger. It tracks dividend yield at **r = 0.917**. |
> | Special prize it targets | Best Use of Agentic Wallet / Wallet Skills | Best Use of BNB Agent Studio (identity, runtime, **self-funding via x402**) |
> | Pattern from past winners it uses | Precise financial primitive + upstream fix to the sponsor's own tooling (Meld, PRECEDENCE, Tilt) | AI bounded by on-chain policy + paid x402 calls + verifiable profit and loss (Faktura, Flattora, Watchdog, Infinite Money Glitch) |
>
> Both ideas avoid the weekend-gap idea completely. Both are spot-only and run on BSC mainnet.

---

## 1. What 87 past winners tell us

Source: `projects.jsonl` / `hackathons.jsonl` (15 hackathons, Jan–Sep 2026). Reproduce with `python3 research/analyze_winners.py`.

| Signal | Data | What it means for us |
|---|---|---|
| The sponsor's stack carries real weight | 77/77 winners with a known rating used sponsor tech as **load-bearing** | Every Binance module we touch has to do real work. No logo integrations. |
| Evidence you can verify | **57%** of winners (50/87) are built around receipts, proofs, attestations or audit trails. It shows up in 7 first-place projects. | On-chain guards and receipts beat dashboards. |
| AI bounded by on-chain policy | **29%** of winners, including **4 first places** (Faktura, Watchdog, Flattora, GATE402) | The LLM proposes and the contract decides. |
| RWA precision wins | RWA was only 7% of winners, but 2 of those 6 took **1st** (Faktura, Tilt Protocol). PRECEDENCE took 3rd as "an unusually precise financial primitive". | Precise primitives beat generic "AI trader" projects. |
| Generic agent marketplaces and escrow | 9% of winners, **zero** first places | Avoid. |
| Generic trading or portfolio agents | ATLAS, ORCA and Sui Jarvis all placed 3rd–4th | "AI that trades" on its own doesn't take 1st. |
| Fixing the sponsor's own tool | Meld won **1st** at KeeperHub by finding waste in the sponsor's workflows and shipping an upstream fix | This hackathon weights the DX report at **25%**. Real bugs plus a PR to Binance's repo hit that directly. |
| Most winners start fresh | 74/87 had no commits before the event | Starting now is normal. |

## 2. What the live market says (snapshot 2026-09-30 19:35 UTC, US regular session)

Pulled from the same public endpoints the Binance skills use, plus BSC RPC. Raw JSON is in `research/snapshot-2026-09-30/`. Reproduce with `python3 research/fetch_snapshot.py && python3 research/analyze_snapshot.py`.

1. **A token is not a share, and each issuer uses different units.** BSC has 517 tokenized tickers: 458 Ondo, 87 bStock, 130 xStocks. 38 tickers are listed by all three issuers and 120 by at least two. 242 of the 458 Ondo tokens have a multiplier other than 1.
   - `NFLX`: Ondo **10.0** shares per token, bStock 1.0, xStocks 1.0 in the list API. The xStocks dynamic API says 10.0.
   - `CRWD`: Ondo **4.0**. `SOXS`: Ondo **0.1017** (reverse split).
2. **The API disagrees with itself and with the chain.** For NVDAx, the list API says the multiplier is `1.000000`, the dynamic API says `1.000918`, and the token contract's on-chain `multiplier()` says `1.001701`. That's three answers for one token. 17 of 103 issuer–ticker pairs disagree between list and dynamic. On-chain vs dynamic, 12 of 38 xStocks tokens disagree, while bStock matches in 38 of 38.
3. **The obvious cross-issuer "arbitrage" is an illusion.** Compare raw token prices and you see gaps of 899% (NFLX), 869% (GME), 713% (MRVL) and 563% (IBM). Normalize to share units against the US reference price and the gaps disappear:
   - Ondo: median **+0.004%**, mean |premium| 0.046%
   - bStock: median **+0.062%**, mean |premium| 0.072%
   - xStocks on BSC: median −1.17%, with outliers of −90% / +865%. These are **stale marks on a ghost market.**
4. **Trading is concentrated in one issuer.** 24h on-chain volume across the 38 triple-listed tickers: **bStock $48.3M, Ondo $4.3M, xStocks $96.** A naive arbitrage bot would buy into those $96 pools.
5. **The metadata has gaps.**
   - Only Ondo returns `marketStatus`. bStock and xStocks return `null` for all 38.
   - `liquidity` reads $0 for almost every token, yet NVDAB made ~2,968 transfers in 22 minutes through router contracts (LI.FI, CoW settlement).
   - bStock exposes `uiMultiplier()` on-chain. xStocks exposes `multiplier()`. Ondo exposes **no** on-chain multiplier, but does expose `compliance()` and `tokenPauseManager()`.
6. **The multiplier is a dividend ledger.** Across 26 Ondo tokens on BSC, multiplier growth tracks the stock's dividend yield with **Pearson r = 0.917**. Examples: PFE +6.09% multiplier vs 5.98% yield, KO +2.39% vs 2.95%, TSLA and AMZN 0.00% vs 0%. Dividends are silently reinvested, users never see them, and they can be measured on-chain.

**What this means:** this month's baskets, DCA bots, rebalancers, spread monitors and "AI traders" will mostly read raw `price` and assume one token equals one share. The biggest hidden risk in this asset class right now is not the weekend gap. It's **wrong units and bad data.** Both ideas are built on that finding.

> ⚠️ Verify on day one with your API key: the authenticated RWA Data API docs describe `referencePrice` as *"per-share converted price derived from on-chain token price"*. If that's literally true, then every "on-chain vs reference spread" monitor (one of the suggested ideas) is comparing the token price with itself. The real underlying price we used is `stockInfo.price` from the public dynamic endpoint.

## 3. Where the crowd will be

The track page lists 10 example ideas, and most entries will cluster around them. We mapped each to our view:

| Crowded cluster | Our read |
|---|---|
| Weekend gap / market-hours arb / reference-spread monitor | Excluded on purpose. The data above also shows the spread signal is mostly a units problem. |
| Cross-protocol arb (bStock vs Ondo) | In share units it's ≤0.1% during market hours. The big "gaps" are traps. **Parity turns this idea into a safety product.** |
| NL strategy agent / earnings agent / TradFi-crypto rebalancer | These will look alike and be judged on build quality. **Stipend gives an agent a business model and on-chain limits.** |
| DCA / thematic baskets / "first stock" onboarding | Consumer UX will be crowded. Parity's "buy in shares" flow stands out because the units are actually correct. |
| MCP / SDK wrapper | Wrappers are commodities. Parity's MCP server is useful because it's a **correctness layer**, not a wrapper. |

---

## Idea 1: PARITY: "Shares, not tokens."

### The pitch
Robinhood users think in shares and dollars, and they expect best execution. On-chain they get three issuers, each with its own units, disagreeing metadata and ghost pools. Parity is the **consolidated tape and share-true order router for tokenized equities on BSC**. You say "buy half a share of NVDA" or "$50 of Apple". Parity quotes every issuer in real share units, picks the best true price, and settles through an on-chain guard that reverts if you'd get fewer **shares** than promised.

### Product surfaces
1. **Consumer app** (mobile-first; Binance Wallet / Agentic Wallet)
   - *Consolidated quote*: for each issuer, the true price per share (`price / multiplier`), price impact at your order size from a Trading API quote ladder (for example $10 / $100 / $1k), gas, trading status, attestation freshness (`protections` from underlying-profile), and an integrity grade. One button.
   - *Receipt in shares*: "You own **0.4998 NVDA** (via NVDAB) · paid +0.06% vs NYSE last."
   - *Portfolio in shares across issuers*: "1.73 AAPL = 1.20 via AAPLon + 0.53 via AAPLB". Dividends are shown as shares received, taken from multiplier growth.
2. **ShareGuard** (Solidity, BSC mainnet)
   - `swapForShares(route, stockToken, minSharesOut, maxPremiumBps, signedRef)`: runs the aggregator calldata from the Trading API, then asserts `Δbalance × multiplier(stockToken) ≥ minSharesOut`. This is **slippage protection measured in shares**, not tokens. As far as we can find, no DEX router offers it.
   - `MultiplierRegistry` adapters: bStock → `uiMultiplier()`, xStocks → `multiplier()`, Ondo → signed feed. The Ondo feed only increases, except for registered split events, and each change is capped by the expected dividend.
   - Reverts when the issuer's pause manager reports a pause, or when the signed reference price shows a premium above `maxPremiumBps`.
   - Execution: a pull-through router, or an EIP-7702 batch so a normal wallet can swap and assert in one transaction.
3. **Trap Shield / Integrity Score** per issuer and ticker:
   - multiplier agreement across list API, dynamic API and on-chain
   - premium vs `stockInfo.price` during regular hours
   - on-chain transfers and volume in the last 24h
   - whether the token has status coverage
   - age of the latest attestation report

   Graded A–F and shown in the app and API. The NVDAx 3-way mismatch and the $96 xStocks market are the live demo.
4. **Agent surfaces** (Wallet Skills special)
   - `share-true-trading` skill in the `binance-skills-hub` format. It sits in front of `baw market-order quote/swap`: resolve ticker → pick issuer → convert shares to amount → check integrity → confirm. ShareGuard is called through `baw contract-call preview/execute` (dev mode), with previews shown to the user.
   - MCP server with `consolidated_quote`, `shares_of(address)`, `integrity(ticker)` and `route(ticker, shares)`.
   - A **b402 pay-per-call** endpoint, so other hackathon agents can pay Parity to avoid unit mistakes.
5. **Upstream PR to `binance/binance-skills-hub`**, following Meld's approach. The `binance-tokenized-securities-info` skill still says Ondo is "currently the only supported tokenized stock provider", while `binance-agentic-wallet` documents `type=1/2/3`. The PR adds the multiplier source-of-truth rules and the xStocks and bStock cases.

### How each Binance module is used
| Module | Role in Parity |
|---|---|
| RWA Data API | Token and issuer lists, `tokenToShareRatio`, `protections` (attestation and collateral URLs), `statusInfo` and corporate-action reasons, underlying market data |
| Market API | Candles and real-time prices for the crypto leg (pay in BNB or USDT) and for sanity bands |
| Trading API | Quote ladder at several sizes per issuer (the actual price-impact curve), swap calldata, approvals, MEV protection |
| Transaction API | **Every route is simulated before it's shown.** Failed simulations lower the integrity grade. Broadcast. |
| Wallet API | Balances → portfolio in shares |
| b402 | Paid integrity and quote endpoint for other agents |
| Agentic Wallet / Skills | The `share-true-trading` skill, and `contract-call` for ShareGuard |
| BSC | ShareGuard, MultiplierRegistry, and live trades of a few dollars across ≥2 issuers |

### Why it wins
- **Technical (30%)**: uses all modules, a contract that enforces a new invariant, three multiplier sources reconciled, and fork tests against real tokens.
- **Creativity (25%)**: takes the suggested "cross-protocol arb" and shows with data that it's a trap. Share-denominated slippage is a new primitive.
- **DX report (25%)**: every bug Parity catches is a finding the judges asked for (see §DX). The upstream PR proves it.
- **UX (20%)**: shares, dollars and best execution are concepts non-crypto users already know. That's the "head to head with Web2" the brief asks for.

### 4-minute demo
1. Hook (0:00): "Is one NFLX token one Netflix share?" Ondo 10.0 / bStock 1.0. A naive bot shows GME **+869% arb**.
2. Parity (0:40): the consolidated quote shows the real gap is +0.06%, and xStocks is flagged as a ghost market.
3. Buy (1:30): "$5 of NVDA" → routes to bStock. ShareGuard transaction on BscTrace. Receipt in shares.
4. Protection (2:20): a ShareGuard **revert** when the signed premium exceeds the bound (dry-run through the Transaction API).
5. Agent (3:00): in Claude Code with the skill: "buy half a share of Apple, cheapest issuer". Previews, then executes.
6. Close (3:40): portfolio in shares, with dividends shown as shares. Link to the upstream PR.

---

## Idea 2: STIPEND: "An AI wealth manager paid only from your dividends."

### The pitch
Robo-advisors charge 0.25–1% a year **out of your principal**, whether or not they add value. On BSC, dividends are silently reinvested into each token's multiplier, which makes them **measurable on-chain income**. Stipend is a vault plus an Agent Studio agent:
- The agent manages your tokenized stock portfolio under rules the contract enforces.
- **Its only income is a capped share of the dividends your holdings actually earned.** It can never touch principal.
- It pays for its own data and inference (x402/b402) out of that income, and publishes an on-chain salary slip. If it's not worth its keep, it runs out of money where everyone can see it.

### Mechanics
- **StipendVault** (per user; holds allowlisted Ondo, bStock and xStocks tokens)
  - Share-equivalent holdings: `shares = tokens × m`. Dividend accrual since the last checkpoint: `Δshares = tokens × (m_now − m_last)`.
  - The agent can claim `feeBps × Δshares`, converted to tokens `= feeBps × tokens × (m_now − m_last) / m_now`. The claim is sold through the Trading API into the agent's wallet.
  - **Split firewall**: a naive version of this formula would treat NFLX's 1 → 10 multiplier as a 900% "dividend". At a 30% fee it would hand the agent **27% of your whole position in one claim** (`0.3 × 9/10`). Any multiplier jump above a band (for example 3% per update) is held out of income. It needs a registered corporate action (`stock_split` in `statusInfo.reasonMsg`) and a timelock. That demo line tells judges we did the math.
  - Agent actions go through `execute(route)` and must satisfy these checks:
    - share-true value after the trade ≥ value before × (1 − maxSlippage)
    - allowlisted tokens only
    - daily turnover cap
    - no trading while `ASSET_PAUSED`, and none while `ASSET_LIMITED (earnings)` unless the mandate allows it
    - concentration caps
  - The user keeps withdraw-all and a kill switch. The agent holds only a session key.
- **Multiplier oracle**: bStock and xStocks are read on-chain. Ondo updates are signed by a keeper, **bounded by `dividendYield × Δt`**, timelocked 24h, and the user can veto. The agent can't raise its own pay.
- **Agent (BNB Agent Studio)**
  - **ERC-8004 identity**, and a reputation feed covering tracking error vs mandate, fees taken and turnover.
  - **ERC-8183 task interface**, so users hire it with tasks like "set my mandate", "rebalance" or "explain this quarter".
  - Autonomous runtime.
  - **Pays for itself via x402/b402**: buys market data and inference, and posts a monthly on-chain "salary slip" with income, costs and runway.
- **Mandates in plain English, turned into on-chain policy**. For example, "60% dividend payers, 30% QQQ, 10% BNB, never >10% in one name" is compiled into vault constraints. The LLM writes the rules and the contract enforces them. This is the Faktura / Flattora pattern.
- **Issuer choice**: rebalances go to the issuer with the best share-true price. Because multiplier math is built in, a split can't confuse drift calculations.

### Honest economics (shown in the app as "break-even AUM")
Assume a portfolio yielding about 4.5% (PFE 5.98%, VZ 6%, T 4.7%, PEP 4.45%, CVX 3.42% from the snapshot) and a 30% stipend:

| Portfolio | Dividends/yr | Agent income/yr | ≈ x402 calls/day at $0.001 |
|---|---|---|---|
| $1,000 | $45 | $13.50 | ~37 |
| $10,000 | $450 | $135 | ~370 |
| $50,000 | $2,250 | $675 | ~1,850 |

A lean agent (daily checks plus a weekly LLM review) can live on $1.10 a month. Say this openly. Judges reward honesty, and the break-even curve *is* the product insight.

### Beyond agents
The same contract can pay the stipend to a **person** instead of an agent. For example, a diaspora family funds a dividend portfolio that streams income home as USDT while the principal stays invested. Mention this in one slide as the general version.

### How each Binance module is used
| Module | Role in Stipend |
|---|---|
| RWA Data API | Multipliers, `dividendYield` (oracle bands), `statusInfo` corporate actions (split firewall, pause rules), attestations |
| Market API | Drift and volatility inputs, crypto leg |
| Trading API | Rebalances and converting stipend to USDT, MEV-protected |
| Transaction API | Simulate every agent action against vault invariants before signing |
| Wallet / DeFi API | Portfolio state. Optionally, the agent parks its own USDT float in a BSC Earn position. |
| b402 / x402 | The agent's spending, which is the "self-funding" in the Agent Studio criteria |
| Agent Studio | ERC-8004 identity, ERC-8183 tasks, managed runtime, auto-registered MCP |
| Agentic Wallet | Agent key with daily limits, and `contract-call` into the vault |

### Why it wins
- **Agent Studio special**: "self-funding via x402" isn't bolted on. It's the business model.
- **Creativity (25%)**: nobody else treats the multiplier as income. r = 0.917 makes the claim concrete.
- **Technical (30%)**: vault invariants, a bounded oracle, a split firewall, and an agent that can't pay itself more.
- **UX (20%)**: "It only gets paid when your stocks pay you" makes sense to anyone who has seen a robo-advisor fee.

### 4-minute demo
1. Hook (0:00): "Your tokenized stocks paid you dividends this year. Did you notice?" Show the PFE multiplier at 1.0609.
2. Hire (0:40): hire the agent through an ERC-8183 task, then write a plain-English mandate. Show the compiled policy.
3. Deposit (1:30): a few dollars of dividend payers go into the vault on mainnet, with a live rebalance.
4. Salary slip (2:20): dividend accrual → the agent claims its capped cut → pays x402 for data. Runway meter.
5. Attack (3:00): simulate a 10× split. The naive formula would pay the agent 27% of your position in one claim, and the firewall holds it. Then the user kill-switch.

---

## Head to head, and a recommendation

These are subjective estimates against the published rubric:

| Criterion (weight) | Parity | Stipend |
|---|---|---|
| Technical (30%) | 9 | 9 |
| Creativity (25%) | 8 | 9.5 |
| DX report (25%) | 10 | 8.5 |
| Product & UX (20%) | 9 | 7.5 |
| **Weighted** | **9.0** | **8.7** |
| Special prize | Wallet Skills | Agent Studio |

**Recommendation:** make **Parity** the entry if you want the highest expected placement. It has more certain UX, and the DX report comes naturally from building it. If you'd rather chase the higher ceiling and the Agent Studio special, submit **Stipend and build Parity's share-math core first as its internal engine** (`MultiplierRegistry` + integrity checks). Stipend needs that core anyway, and the split firewall depends on it.

## Execution plan (in order; the deadline is deliberately left out)

| Phase | Parity | Stipend |
|---|---|---|
| P0: Truth layer | Ingest the 3 issuers; multiplier adapters (on-chain + API); integrity scoring; snapshot store | Same core; add the dividend-accrual ledger and split detection |
| P1: Quotes and simulation | Trading API quote ladder per issuer; Transaction API simulation of each route; consolidated quote | Simulate agent actions against vault invariants |
| P2: Contracts | `ShareGuard` + `MultiplierRegistry`; Foundry **BSC mainnet-fork** tests using real NVDAB/NVDAon/NVDAx; deploy; $2–5 live trades on ≥2 issuers | `StipendVault` + bounded oracle + split firewall; fork tests including a synthetic 10× split; deploy; small live deposit |
| P3: Product | "Buy in shares" flow, portfolio in shares, Trap Shield | Mandate → policy compiler, salary slip, runway meter, kill switch |
| P4: Agent layer | Wallet Skill, MCP, b402 endpoint, upstream PR | Agent Studio deploy (ERC-8004/8183), x402 spending, reputation feed |
| P5: Proof | Demo video, BscTrace links, DX report | Same |

### Feasibility checks already done
- **The token layer does not block contracts.** On 2026-09-30, `research/transfer_check.py` simulated real holders sending NVDAon, NVDAB and NVDAx to a brand-new wallet and to a brand-new contract. All six transfers passed, and an overdraw control reverted as expected. All three issuers use a **blocklist/sanctions model** (Ondo: `isBlocked`/`isSanctioned` via `compliance()`; bStock: `addToBlocklist`/`sanctionedAddresses`; xStocks: `sanctionsList()` + `isPaused()`), not an allowlist of approved holders. Any address can hold unless it is listed. Re-run the script before deploying, because the lists can change.
- **The real gate is off-chain eligibility, not the contract.** Issuers push geographic and eligibility enforcement onto the app offering the product. The bStocks FAQ expects a country-eligibility API that isn't publicly documented. xStocks puts KYC and geography on the venue. Ondo attaches eligibility representations to secondary buyers, and redemption requires issuer KYC. See "Eligibility" below.
- On-chain multipliers can be read for bStock and xStocks. Ondo needs a feed. Its `tokenPauseManager()` exists, so pause checks can be done on-chain.
- The skill docs show `limit-order` returning `Ondo-related tokens cannot be traded`. Plan for market orders plus guards on Ondo.

### Eligibility: how each idea handles it
Transfers work, but an app that helps people buy these tokens takes on the issuer's user-eligibility duties. Neither idea can make that go away, so both are designed not to be a new distributor.
- **Parity**: execution goes through the user's own **Binance Web3 Wallet / Agentic Wallet and the Trading API**. The venue that already onboarded the user places the trade. Parity supplies the quote, the integrity grade and the ShareGuard check. Quotes, integrity scores and the MCP data need no eligibility at all. The app also blocks the hackathon's restricted regions itself. *To verify with your key:* whether Trading API bStock quotes and swaps already enforce region for the wallet or key. Log the answer in the DX report either way.
- **Stipend**: this is the more exposed of the two, because an agent managing someone's securities for a fee looks like investment management. Build it as a **policy module on the user's own smart account** (EIP-7702 / ERC-7579 session key) instead of a separate vault, so the tokens never leave the user's wallet and the agent only holds a capped, revocable key. Trades still go through the Binance wallet stack. For the hackathon, demo only with your own funds.
- **Both**: no issuer mint or redemption (that requires issuer KYC). Secondary-market only, small amounts from your own wallet, with the restricted-regions gate on.

## The DX report (25%): write it yourself, as you go
The rules reject AI-generated reports, so **keep a timestamped human log from the first minute**. That covers time to first successful call, each error message copied verbatim, and page URL plus section for every doc problem. The items below are leads we found from outside with public endpoints. **Confirm each one yourself with your key before it goes in the report:**
- Three different multiplier values for the same token across list API, dynamic API and on-chain (xStocks).
- `liquidity` = 0 on tokens with thousands of router transfers per hour.
- `marketStatus` returned only for Ondo tokens.
- `tokenInfo.volume24h` is the **US stock** volume, not on-chain volume. The skill docs admit this, but the field name misleads.
- RWA Data API docs list `platformId: "ondo" | "bstock"` only, while xStocks are `type=2` in the public list.
- The tokenized-securities skill says Ondo is "the only supported provider", which contradicts the agentic-wallet skill.
- The `referencePrice` definition (see §2 warning).
- xStocks on BSC: stale prices with ~$0 volume that are still returned as tradable (`TRADING`).

## Reproduce
```bash
python3 research/analyze_winners.py                        # hackathon-winner patterns
python3 research/fetch_snapshot.py                         # fresh public-API + on-chain snapshot
python3 research/analyze_snapshot.py research/snapshot-<date>
```
Numbers in this document come from `research/snapshot-2026-09-30/` (fetched 19:35 UTC, US regular session).
