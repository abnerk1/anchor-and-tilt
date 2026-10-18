# Contributing

Thanks for wanting to improve the rubric. A few rules keep it deterministic.

## Principles

1. **The rubric is the product.** Changes go into `skills/anchor-and-tilt/SKILL.md`. Do not add rules elsewhere.
2. **No silent rules.** A new rule must be explicit, with a number or a lookup table, and a rationale.
3. **Data before opinion.** Any change to a weight or a fund choice must cite primary sources: fund issuer pages, prospectuses, or SEC filings. This matters most when the change departs from a well-known framework.
4. **Preferences never override the risk profile.** Keep the priority hierarchy intact.

## Proposing a change

- Open an issue describing the case where the current rubric gives a wrong or ambiguous answer. Include the intake answers and the expected portfolio.
- In the pull request, bump the version (v1 → v1.1), update `examples/` if the change affects them, and show the before/after allocation.
- Closing one of the **Known gaps** is the most welcome kind of contribution.

## Checklist

- [ ] Targets in every example still add up to 100%
- [ ] Every new fund choice is justified by the League Table (D1) with sourced figures
- [ ] No personal data, API keys, or credentials in the diff
