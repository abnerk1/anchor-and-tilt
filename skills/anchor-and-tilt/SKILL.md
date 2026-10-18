---
name: anchor-and-tilt
description: Builds and audits global ETF portfolios with the Anchor & Tilt rubric (Bogle-inspired, low-cost global core, at most 2 tilts, home bias routed by country classification, a commodities/alternatives layer, numeric guardrails, and red flags). Use it whenever the request involves building, reviewing, critiquing, or adjusting an ETF portfolio, asset allocation, portfolio weights, choosing between competing ETFs, investor risk profile, home bias, sector/factor tilts, or exposure to gold/Bitcoin, even if the user does not name the rubric and even if they only ask for "a quick portfolio suggestion." Also use it to audit an existing portfolio against the rubric.
---

# Anchor & Tilt: ETF Allocation Rubric

This skill applies a predefined judgment framework to build and evaluate ETF portfolios. The goal is to remove run-to-run variability: given the same set of investor answers, the resulting portfolio must be the same.

General rule of conduct: **do not improvise rules that are not written here.** When information is missing, ask the user or state the assumption explicitly in the output (see "Known gaps"). Silently inventing a number is the worst failure mode of this skill.

---

## Required flow

**Intake first** (Layer B). Never jump straight to the portfolio: the personal parameters (risk profile, country, tilts) cannot be derived from general knowledge.

**Build order.** Follow exactly this sequence; it is what makes the result deterministic:

1. **Risk profile** → sets fixed income + equities = 100% (C1)
2. **Home bias** → reserves the domestic exposure (C2 + D2)
3. **Real estate** → reserves the chosen exposure (C5)
4. **Commodities/alternatives** → reserves according to the risk profile (C4)
5. **Tilts** → reserves up to 2 tilts (C3)
6. **Optional satellites** → EWZ and similar, if requested (C6)
7. **VT and BNDW absorb the remainder**: VT takes the equity remainder, BNDW the fixed-income remainder
8. **Check**: total = 100%, no limit breached, risk profile respected, red flags checked

**Which sleeve each component comes from**: emerging-market home bias is split (VWO → equities, VWOB → fixed income); developed-market home bias is entirely equities; real estate, tilts, satellites, and the commodities/alternatives layer all come out of the **equity** sleeve.

**Priority hierarchy.** When the investor's preference conflicts with the profile limit, the higher-priority rule wins:

1. Risk profile → 2. Fixed income/equity split → 3. Home bias → 4. Diversification → 5. Real estate/commodities/alternatives → 6. Tilts → 7. Satellites

A conservative investor may *ask* for 10% Bitcoin and 20% in tilts; the agent **applies the profile limit and states explicitly what was reduced and why**. It neither obeys nor silently ignores the request.

**Worked close-out** (aggressive, Brazil, 2 tilts, real estate 10%, 2 commodities): fixed income 20% = VWOB 5% + BNDW 15%; equities 80% = VWO 10% + real estate 10% + commodities 5% + tilts 20% + VT 35%. Total 100%, which reproduces the model portfolio.

---

## Layer A: Philosophy (invariants; non-negotiable)

- **Bogle-inspired base**: low cost, broad diversification, total-market exposure before concentrated bets.
- **At most 2 tilts** per portfolio. Which tilts is the investor's subjective choice; what is fixed is the maximum count. If the investor wants no tilt, that percentage goes back to the core (VT).
- **Home bias is never implemented through a single-country bet.** The instrument is always a diversified ETF, routed by country classification (Layer D). Single-country bets (e.g., EWZ) exist only as a separate satellite exception, with a mandatory disclaimer.
- **Stated purpose of home bias**: exposure to the investor's economic region, **not** exposure to specific domestic companies. The origin is behavioral (the Brazilian investor asks "where is Vale? where is Petrobras?"), and this sleeve helps people stay with the strategy. But the diversified instrument does not deliver material domestic exposure (Brazil is ~4-5% of VWO; a 10% VWO target yields ~0.5% Brazil in the portfolio). The rubric accepts this tension deliberately. **The output must not promise concrete domestic exposure that the instrument does not deliver.**
- **Horizon**: long-term income and accumulation. No trading and no market timing (except the tilts, which are the openly discretionary component).
- **Volatility is not penalized by itself.** What is penalized is uncompensated or non-diversifiable risk.

---

## Layer B: Intake parameters (ask, never infer)

Collect all of them before building:

1. **Risk profile**: conservative, moderate, or aggressive.
2. **Future spending/retirement currency**: the currency in which the person will actually spend the money over the long run. This is the criterion that defines home bias, because it is what justifies it economically. If the person cannot answer, use **tax residence** as a proxy. **Do not ask for nationality**: being born in a country does not create future spending exposure there.
3. **Home bias**: yes or no? In a country other than the country of residence (elective home bias)?
4. **Tilts**: how many (0, 1, or 2) and which ones.
5. **Commodities/alternatives**: yes or no, and which ones (e.g., gold, Bitcoin).
6. **Real estate**: yes or no.
7. **Investment horizon.**

---

## Layer C: Calculation rules

### C1. Fixed income / equity split

Mental model: the efficient frontier. Because the exact optimal point is unknown, the split is a guess calibrated by risk profile:

| Profile | Fixed income (range) | Equities (range) | Neutral point (use as target) |
|---|---|---|---|
| Conservative | 75–85% | 15–25% | **80 / 20** |
| Moderate | 40–60% | 40–60% | **50 / 50** |
| Aggressive | 15–25% | 75–85% | **20 / 80** |

Rationale: in pure logic, the aggressive investor would be 100% equities; they love returns and do not care about risk. The fixed-income sleeve exists because an advisory role has to keep their feet on the ground, not because fixed income wins on points. The conservative profile follows the inverse logic.

### C1b. VT and BNDW are residual components

The 35% VT in the model portfolio is **not a universal rule**. It is the result of the specific aggressive case after every preference has been met. VT absorbs the remainder of the equity sleeve; BNDW absorbs the remainder of the fixed-income sleeve (BNDW must also absorb, because there may be additional exposure inside fixed income itself, such as VWOB).

An aggressive investor without tilts, home bias, or alternatives may hold more than 35% VT; with everything switched on, less. The 30–40% range describes the model portfolio; it is not a universal limit.

### C2. Home bias

- **Default target 15%**; **10%** for a moderate profile in a developed market. Set by the combination of risk profile and country classification (table D2). **This is not individual optimization**; it is a deterministic lookup.
- Never exceed **20%**. Acceptable range: 10% to 20% (10, 12.5, 15, 17.5, and 20 are all valid).
  - *Precision note: the reference to "Pareto"/80-20 is a conceptual heuristic, not a mathematical derivation. The range and its midpoint came from judgment calibration.*
- **Elective home bias** (a country other than the country of residence) is legitimate and **does not use up either of the 2 tilts**. It is **substitutive, not cumulative**: it redistributes the existing home-bias budget instead of adding on top. Someone living in Brazil who also wants US home bias does not end up with 15% + 15% = 30%, but with something like 10% Brazil + 5% US = 15%. **Total home-bias cap: 20%.** Example of legitimacy: a Ukrainian living temporarily in Israel because of the war is a tax resident of Israel but may keep home bias in Ukraine, because the intention to return keeps future spending exposure there. Run table D2 on the **elected** country.
- **The frontier rule always wins**: if the country (elected or of residence) is a frontier market, home bias is **not included**, even if the person wants it, and the percentage goes to the core (VT). There is no suitable diversified ETF, and the philosophy forbids implementing home bias through a single country. In the Ukrainian example above, home bias therefore drops.
- **No home bias**: the percentage goes back to the core (VT).

### C3. Tilts

- At most 2. The investor chooses which.
- They are the **deliberately discretionary** component of the portfolio ("the spice"): personal conviction bets, explicitly reversible if they underperform. The rest of the portfolio is systematic.
- In the reference case (the author's own portfolio), the split was 10%/10% because of equal conviction, low correlation between the two (which makes an equal split feel win-win), the human preference for round numbers, and operational convenience (it makes percentages easier to compute on an uneven dollar amount). It could be 15/5 if conviction were unequal; ask whether conviction is equal or unequal.

### C4. Commodities / alternative assets

- **Bitcoin is classified separately as a crypto/alternative asset, not a commodity**, but for portfolio construction it shares the same maximum budget as this layer.
- **Aggressive**: up to **2** positions in this layer. Total range 0% to 6%. Fixed target of **2.5% each**.
- **Moderate**: up to **1** position. Range 0% to 3%. Fixed target **2.5%**.
- **Conservative**: up to **1** position, target **2.5%** (same rule as moderate). Being allowed does not mean having to; ask, and if the person does not want it, it stays at 0% and the amount returns to the core.
- Which commodity is a personal preference (gold vs. silver vs. other); no rule prescribes gold.

### C5. Real estate

An **optional** complement to equities, not a mandatory asset class. If the person does not want it, the percentage goes to the rest of the equity sleeve.

- **Aggressive**: range 5% to 15%, target **10%**, via **VNQ + VNQI** split **50/50** (10% → VNQ 5% + VNQI 5%).
- **Moderate**: range 0% to 10%, target **5%**, via **REET** (single global fund).
- **Conservative**: same range/target as moderate (target 5%). ⚠️ Conflicting instrument; see Known gaps #1.
- **Intensity scale** (disclaimer trigger): 0% none; 1–5% low; 5–10% moderate; **>10% high**. A conservative investor asking for >10% receives: "You want high exposure to real estate, but your risk profile limits this asset class."

Rationale (use it if the user questions the departure from popular frameworks such as ARCA, which suggests 25%): real estate carries risk close to equities but returns closer to fixed income. Over the long run it loses to equities, because a company that rents a space usually earns more than the rent it pays. **General principle: every weighting recommendation must be grounded in data, especially when it departs from a well-known popular framework.**

### C6. Numeric guardrails (limits, not scores)

- **The 3% rule** applies to **exposure to a single underlying asset**: ETFs/trusts that track a single underlying asset (e.g., IAU → gold, IBIT → Bitcoin) or individual stock picking (e.g., NVDA). It **does not apply** to diversified ETFs, even thematic or single-country ones (e.g., EWZ, which holds more than 40 stocks), precisely because they are already diversified internally.
- **Satellite/tactical exception** (e.g., EWZ): an optional position **capped at 5%**, taken out of the **VT** slice. It is not part of the structural home-bias budget nor of the tilts. It only enters when the investor (1) explicitly asks for exposure to the country, (2) accepts the concentration, and (3) receives the disclaimer that it is substantially riskier than the diversified equivalent (e.g., VWO).

### C7. Weight format

- Each allocation is given as a **range + target**: "VT between 30% and 40% (target: 35%)."
- **Targets must add up to 100%.**
- **Range = target ± 5 percentage points.** E.g., target 35% → 30-40%; target 10% → 5-15%.
- **Targets are multiples of 5.** Exception: commodities (C4), with a fixed 2.5% target and their own ranges; they do not follow ±5, because 2.5% is already smaller than 5. When a moderate investor picks 1 commodity, the 2.5% comes out of the core (VT), which acts as the flexible slice that absorbs the leftover fraction so the total stays at 100%.
- **Fallback for an undocumented target**: when a range exists but no explicit target, use the **range midpoint rounded to the nearest multiple of 5**, and **state in the output that it was assumed by fallback**, giving the user the chance to correct it.

---

## Layer D: Instruments

### D1. How to choose between competing ETFs ("League Table")

When two or more ETFs compete for the same slot, compare them criterion by criterion like a league table: **a win on a criterion = +3 points; a tie = +1**. The one with the most points wins; it does not need to win every criterion.

Criteria and relative weight:

| Criterion | Weight |
|---|---|
| Liquidity | High: nearly a knockout criterion, not just a score |
| AUM | High: first filter |
| Cost (expense ratio) | High: second filter; when AUM and fee point to different candidates, **fee usually wins** |
| Tracking error | Medium |
| Fit with the role in the portfolio | High |

**Practical tiebreaker**: look first at who has the largest AUM, then who has the lowest fee. If the same candidate leads both, it wins outright. If they diverge, the lower fee usually prevails.

**Documented exception, IBIT**: it is not the lowest-fee fund in its segment, but it won by leading on liquidity, AUM, and tracking error (2nd best in the segment, behind only FBTC). When a candidate dominates several criteria at once, that can outweigh a fee disadvantage.

### D2. Home-bias routing by country classification

| Country classification | Fund(s) | Aggressive | Moderate | Conservative |
|---|---|---|---|---|
| Emerging (e.g., Brazil) | VWO (equities) + VWOB (fixed income) | 10% VWO + 5% VWOB (15%) | 5% VWO + 10% VWOB (15%) | 15% total (⚠️ split not defined; see Known gaps #2) |
| Developed: Europe | VGK | 15% | 10% | 10% |
| Developed: Pacific (e.g., Japan, Australia) | VPL | 15% | 10% | 10% |
| Developed: outside Europe/Pacific/US | VEA | 15% | 10% | 10% |
| United States | VTI | 15% | 10% | 10% |
| Frontier | none | **No home bias**, even if the person wants it | **No home bias** | **No home bias** |

**Why emerging and developed markets have different structures** (deliberate, not an inconsistency): emerging exposure can be implemented in both equities *and* fixed income, via VWO and VWOB, so it totals 15% and creates home bias in both asset classes. In developed markets, the v1 default implementation is mainly in equities, because of instrument availability.

### D3. Documented slot winners

Use these when the slot matches; do not re-derive:

| Slot | Winner | Deciding criterion |
|---|---|---|
| Global equity core | VT | League Table |
| Global fixed income | BNDW | Fee + operational simplicity (avoids manually rebalancing BND vs. BNDX); beat BND/AGG |
| Emerging-market debt | VWOB | League Table: EMB led comfortably on AUM but lost on total points |
| Gold | IAU | Fee: beat GLD despite smaller AUM |
| Bitcoin | IBIT | AUM (the only case where AUM was directly decisive) |
| Real estate (10% allocation, aggressive) | VNQ + VNQI | Fee: the pair is cheaper than REET |
| Real estate (5% allocation, moderate) | REET | Simplicity: the pair's fee advantage does not justify holding 2 positions in a small slice |

For slots **not** listed here (e.g., a sector tilt the investor asks for, a commodity other than gold), **do not invent**: search, within the rubric's fund universe (Vanguard and equivalent low-cost, high-liquidity funds), for the ETF that best represents the requested exposure, apply D1, and **state in the output that the choice was derived by League Table, not pre-documented**. Example: healthcare tilt → VHT.

---

## Validation: red flags that reject the recommendation

Run these before delivering. Any one of them invalidates the result:

- **Insufficient liquidity**: average daily volume below **US$ 1 million**. This is the nearly-knockout criterion; the ETF needs a clear exit door.
- A numeric figure that cannot be confirmed in a primary source.
- A ticker that does not exist or has been discontinued.
- An allocation that does not add up to 100%.
- A recommendation that ignores the asset's role in the portfolio (e.g., proposing an asset highly correlated with the core as if it were a diversifier).
- Exposure to a single underlying asset above 3% without being an explicitly flagged satellite exception.
- A single-country bet recommended **without** the elevated-risk disclaimer.

---

## Output format

There is no rigid prose template. The quality of the explanation ("why this combination works") is tacit, comparable to explaining why a note fits the context of a song or why a combination works in a dish.

What is mandatory:

1. Each fund with **range + target**, and the targets adding up to 100%.
2. Rationale for each fund, anchored in the criteria of this skill.
3. **Explicit statement of every assumption** made by fallback or by derivation outside what is documented.
4. Mandatory disclaimers when applicable (single-country bet; high real estate in a conservative profile).

---

## Known gaps: do not resolve on your own

Ask the user or state the assumption prominently in the output:

1. **Real estate instrument for the conservative profile**: the instrument rule calls for REET for 5% allocations (simplicity), but the 50/50 split decision was proposed as VNQ 2.5% + VNQI 2.5%. Both cannot hold at once, and 2.5% per fund also collides with the multiples-of-5 rule.
2. **VWO/VWOB split for the conservative profile** (emerging-market home bias, 15% total) is not defined.
3. **Instruments outside D3** (other commodities, other tilt sectors): derive them via D1 and flag them.
