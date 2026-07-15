# Go-Live Playbook — Daily Trading Function

**Account:** ••••1562 "Agentic" (the only one I can read/track) · **Level 2** (long calls/puts OK; spreads need L3)
**Universe:** IWM, GOOGL, TSLA, AAPL, SPY, QQQ, NVDA + any name you call out
**Hard limits:** −$50/day total · −$30/contract · 1–2 contracts at a time
**DTE:** open — 0DTE through swing (weeks). Not locked to 0/1 DTE. Chosen per trade (see DTE guide).
**Model:** I scan & suggest → **you place every order manually** → I log P/L & call the −$50 stop.
**Standalone:** this playbook is the directional call/put function. The credit-spread backtest is parked; borrow a technique (earnings filter, trend/momentum read) only if it helps.

**Session rules (how we actually trade the day):**
- **Goal = end the day GREEN.** Any profit is good profit. No minimum number of trades; small wins count. Once solidly green, protect it (size down or stop).
- **Entry timing:** skip the first ~5 min (opening-range noise). Enter on a *confirmed* setup, not at the bell.
- **0DTE hard cutoff: be flat by 3:00–3:30pm ET.** Gamma & theta go vertical into the close — don't hold 0DTE hoping.
- **Protect green on multi-day (2–6 DTE):** if a contract is up but **theta / IV drop is eroding the gain faster than the thesis is progressing**, I flag it — you close green even if we never hit the target. Don't let a winner bleed back to red on time decay.

---

## STAGE 1 — Nightly scan (night before, ~after close)
For each ticker in the universe I pull and report:

1. **News scan** — company + macro headlines since last close (source: Investing.com / Google Finance;
   **never Polymarket** — injection risk). Flag anything that moves price: guidance, upgrades/downgrades,
   product/legal/regulatory, sector news.
2. **Earnings check** — `get_earnings_calendar`. **Any report before the option's expiry = flag.**
   - This cycle: **GOOGL 7/22 · TSLA 7/22** report before 7/24. AAPL/NVDA: not this week. Indices: n/a.
3. **Technical read** — trend (EMA structure) / momentum (RSI, MACD) / where price sits vs. support-resistance.
   Enough to say whether the chart *agrees* with the news bias or fights it.
4. **Output per name:** a one-line **bias → CALL / PUT / STAND ASIDE**, with the catalyst and the level that
   invalidates it. No bias without a reason.

**Deliverable:** a ranked nightly card. Names with a clean catalyst + aligned technicals rise to the top;
earnings-risk and mixed-signal names get benched.

## STAGE 2 — Pre-market (morning, before open)
1. Re-scan overnight: futures (ES/NQ), gaps, any headline after my nightly card.
2. Kill any nightly idea whose catalyst reversed or whose invalidation level gapped through.
3. Confirm the day's shortlist (usually 1–3 names).

## STAGE 3 — Go-live gate (pass ALL before any order)
- [ ] Account **funded**; **settled** cash confirmed (T+1 — unsettled cash ≠ buying power).
- [ ] Yesterday's P/L logged; **today's −$50 counter reset to $0**.
- [ ] Not already at/over −$50 for the day.
- [ ] Contract chosen so **max intended loss ≤ $30** (premium size or defined stop).
- [ ] Expiry checked against earnings (skip if report lands inside the trade unless it's a deliberate earnings play — flagged as such).

## STAGE 4 — Per-trade (repeat for each idea)
1. I present the exact contract: **ticker, call/put, strike, expiry, ~premium, delta, theta/day, max loss, invalidation level, target.**
2. **You approve.**
3. **You place it manually** in Robinhood.
4. You tell me it filled (or I read the position) → I log entry to the P/L tracker.

## STAGE 5 — Live management
- **Profit target:** take it at your pre-set level (e.g. +50–100% on premium) — but **any green you'd rather bank, bank.** Green is the goal.
- **Per-contract stop:** exit at **−$30**. A gap can skip past it — the stop is an action, not a guarantee.
- **Daily kill switch:** at **−$50 realized on the day, we stop.** No revenge trade. I will say so explicitly.
- **0DTE close-out:** flat by **3:00–3:30pm ET** regardless of P/L. The last hour is where 0DTE gains evaporate.
- **Theta-watch (2–6 DTE):** I monitor unrealized P/L vs. daily theta burn. When I see *"you're +$X, theta is −$Y/day, momentum has stalled"* I flag it → **close green.** Protecting a realized win beats chasing the target and giving it back overnight.
- **Green-lock:** once the day is comfortably green, I'll say so — you can stop for the day or cut size. Ending green = mission complete.

## STAGE 6 — End of day
- Update P/L tracker (realized, running total vs. −$50).
- One-line journal per trade: thesis, what happened, did I honor the stop.
- Roll lessons into the next nightly card.

---

### DTE guide (which expiry, and why it interacts with your $30 cap)
DTE isn't cosmetic — it decides how your −$30 stop behaves.

| DTE | Premium cost | Theta (time decay) | Gamma (move sensitivity) | Fit with −$30 cap |
|---|---|---|---|---|
| **0–1 DTE** | cheapest | brutal — dies by close | highest — whips both ways | Cheap entry fits $30, but noise trips the stop constantly. High-conviction *intraday catalyst* only. |
| **2–7 DTE (weeklies)** | moderate | meaningful | moderate | Balanced. Thesis gets a few days to work. Usual default. |
| **2–4+ weeks (swing)** | expensive | slow | low | Most forgiving to hold, but a big ATM premium makes −$30 a tiny % move → easy to noise-stop. Favor slightly OTM to keep cost down. |

**Rule of thumb:** match DTE to the catalyst's clock. A same-day headline → short DTE. A thesis that needs a
week to play out → weekly. Never buy 0DTE hoping a slow thesis hurries up — that's just paying max theta to be right too late.
**Always note the expiry vs. earnings** (GOOGL/TSLA 7/22 this cycle) — unless the trade *is* the earnings play, flagged as such.

### The Greeks — what I factor into every contract (long-option view)
| Greek | What it measures | Why it matters for us |
|---|---|---|
| **Delta (Δ)** | $ the option moves per $1 in the stock; also ≈ probability of finishing ITM | Picks the contract. ATM ≈ 0.50. **0.40–0.60** = responsive, pricier; **0.20–0.30** = cheaper/more leverage, lower odds (fits −$30 better but wins less often). I'll state delta on every pick. |
| **Gamma (Γ)** | How fast delta itself changes | Highest **ATM and near expiry**. High gamma = P/L whips both ways fast — the reason **0DTE trips the −$30 stop on noise**. |
| **Theta (Θ)** | $ lost to time decay **per day** (negative for us) | The "eats away at profit" you named. Accelerates into expiry. This is exactly what I watch on 2–6 DTE winners → the **green-lock flag**. |
| **Vega (ν)** | Sensitivity to implied volatility (IV) | We're **long vega**. If IV drops — classic **post-catalyst / earnings "IV crush"** — you can be right on direction and still lose. Why we're careful buying into GOOGL/TSLA earnings. |
| **Rho (ρ)** | Sensitivity to interest rates | **Negligible** for 0–6 DTE day trades. Noted for completeness; not a driver here. |

**How they combine in a pick:** I choose delta for the exposure I want, respect gamma (shorter DTE = twitchier), price the theta bleed against how long the thesis needs, and check vega/IV so a catalyst crush doesn't sink a correct call. Every contract I hand you will list **delta, and the theta/day**, plus a note if vega/IV is a risk.

### Directional-options reality (keep visible)
- Long options: **max loss = premium**, win rate typically **<50%**, **theta** works against you daily.
- On NVDA/TSLA/GOOGL a weekly ATM ≈ **$300–800** → a −$30 stop ≈ a **very tight** leash; expect to get
  stopped by noise. Cheaper OTM contracts = lower cost but low probability. Size honestly.
- Best directional edge = a **real catalyst** (the nightly scan's job) + technicals agreeing, not a hunch.
