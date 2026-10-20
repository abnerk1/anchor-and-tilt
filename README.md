# Anchor & Tilt

**A deterministic ETF allocation rubric, packaged as an AI skill. Same answers in, same portfolio out.**

A low-cost global core *anchors* the portfolio. At most two conviction bets *tilt* it. Everything in between (risk profile, home bias, real estate, gold and Bitcoin, guardrails) follows written rules, so an AI agent cannot improvise.

## The problem it solves

Ask a large language model for "a good ETF portfolio" twice and you will often get two different answers. Ask it with a different model and you get a third. For anything that touches someone's savings, that variability is a bug.

Anchor & Tilt turns an investment philosophy into an explicit rubric that an AI agent must follow step by step:

- **Intake before advice.** The agent must collect seven parameters (risk profile, spending currency, home bias, tilts, alternatives, real estate, horizon) and may not infer them.
- **A fixed build order.** Allocations are reserved in a set sequence, and the global core absorbs whatever is left. Order is what makes the output reproducible.
- **Hard guardrails and red flags.** Limits that override preferences, and checks that reject a recommendation outright.
- **No silent assumptions.** Any number not documented in the rubric has to be flagged in the output.

The rubric was run on Claude, ChatGPT, Gemini, and Grok while it was being written. Places where the models came back with different answers pointed to rules that still needed to be spelled out.

## How it works

```
Intake (7 questions)
  │
  ├─ 1. Risk profile ............ fixed income / equity split (80/20, 50/50, 20/80)
  ├─ 2. Home bias ............... 10–15%, routed by country classification, capped at 20%
  ├─ 3. Real estate ............. optional, 5–10%
  ├─ 4. Commodities/alternatives  2.5% each, up to 2 (aggressive) or 1 (moderate/conservative)
  ├─ 5. Tilts ................... at most 2, investor's choice
  ├─ 6. Satellites .............. optional single-country bet, max 5%, with disclaimer
  ├─ 7. Anchor .................. VT absorbs the equity remainder, BNDW the fixed-income remainder
  └─ 8. Check ................... sums to 100%, limits respected, red flags cleared
```

When a preference conflicts with the risk profile, a fixed priority order decides, and the agent has to say what it cut and why. A conservative investor who asks for 10% in Bitcoin gets 2.5% and an explanation, never a silent yes or a silent no.

## Example

An aggressive investor in Brazil who wants home bias, real estate, gold and Bitcoin, and two tilts (multifactor and technology):

| Fund | Role | Target | Range |
|---|---|---|---|
| VT | Global equity anchor | 35% | 30–40% |
| BNDW | Global fixed-income anchor | 15% | 10–20% |
| VWO | Home bias, emerging equities | 10% | 5–15% |
| VWOB | Home bias, emerging debt | 5% | 0–10% |
| VNQ | US real estate | 5% | 0–10% |
| VNQI | International real estate | 5% | 0–10% |
| LRGF | Tilt: multifactor | 10% | 5–15% |
| VGT | Tilt: technology | 10% | 5–15% |
| IAU | Gold | 2.5% | 0–6% (layer) |
| IBIT | Bitcoin | 2.5% | 0–6% (layer) |
| **Total** | | **100%** | |

More cases, including a conservative investor whose request is capped by the rubric, are in [`examples/`](examples/).

## Design decisions worth discussing

- **Home bias follows the currency you will spend in, not your nationality.** Being born somewhere does not create future expenses there. Elective home bias (e.g., a Ukrainian temporarily in Israel who plans to return) is allowed. Frontier markets get no home bias, because no diversified ETF exists and single-country bets are not allowed in the core.
- **The core is a remainder, not a target.** VT and BNDW absorb whatever is left after preferences, so "35% VT" is an outcome of one case, not a rule.
- **Real estate at 5–10%, not the 25% of some popular frameworks.** Real estate carries equity-like risk with returns closer to fixed income, and any departure from a well-known framework has to be justified with data.
- **Choosing between competing ETFs works like a sports league table.** A win on a criterion earns 3 points and a tie earns 1. Liquidity, AUM, and fee weigh the most. The rubric documents why IAU beat GLD, why BNDW beat BND and AGG, and the one case where AUM alone was decisive (IBIT).
- **The 3% rule targets single underlying assets** (one commodity, one coin, one stock), not diversified single-country ETFs.
- **Known gaps are written down.** The agent is told not to resolve them on its own.

## Use it

**As an AI skill.** [`skills/anchor-and-tilt/SKILL.md`](skills/anchor-and-tilt/SKILL.md) follows the `SKILL.md` format used by Claude skills. Load it into a compatible client and ask for a portfolio, or for an audit of one you already hold.

**As a document.** The same file reads as a plain-English specification. You can paste it into any chat model as a system prompt.

## Limitations

- The fund universe is US-listed ETFs, mostly Vanguard and similar low-cost funds. Availability and taxes differ by country of residence.
- Splits and targets come from calibrated judgment (e.g., the 80/20 home-bias heuristic), not from an optimizer.
- Three known gaps are open (see the end of the skill).
- Fund data such as fees, AUM, and liquidity change over time. The rubric requires confirming figures in primary sources before delivery.

## Disclaimer

Educational project. The output of this rubric is not individual investment advice. Investment decisions are the sole responsibility of the investor.

## Author

Abner Araujo. Finance professional (ANBIMA C-PRO I), building rules-based investment tooling with AI agents.

Related: [skills-b3](https://github.com/abnerk1/skills-b3), deterministic valuation skills for Brazilian stocks and REITs (FIIs), and [global-equity-valuation](https://github.com/abnerk1/global-equity-valuation), the same rules-based approach for US stocks and ADRs.

## License

[MIT](LICENSE).
