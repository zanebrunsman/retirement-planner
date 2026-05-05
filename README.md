# Retirement Planner

> ⚠️ **Under active development — do not rely on for financial decisions.**

This is a personal-use retirement / FIRE planner that runs entirely in your
browser. It is being built iteratively and **should not be used as the basis
for any real-world financial decision**. Numbers, formulas, and behavior may
change without notice, and known issues are still being worked on.

> 🤖 **AI-assisted development.** This planner was built with substantial help
> from AI coding assistants (writing, reviewing, and refactoring source code,
> tests, and documentation). It is a personal project, not professional
> financial advice. Verify any number that matters against an independent
> source — a licensed financial advisor, the relevant IRS or plan rules, or
> your own spreadsheet — before relying on it for a real-world decision. The
> author and the AI tools used to build this make no warranty that the
> formulas, defaults, or outputs are correct or current.

## What it does

- Models accumulation, Coast FIRE, and retirement phases year by year
- Supports multiple accounts (Brokerage, Roth IRA, Roth 401(k), Traditional
  401(k)/IRA, HSA, Custom) with per-account growth rates and contribution caps
- Three withdrawal modes:
  1. **Fixed today's $** — constant real spend, inflated forward
  2. **% of retirement portfolio (Trinity-style)** — fixed real spend locked
     at retirement
  3. **Endowment rule** — % of current balance, recalculated yearly
- Configurable waterfall (which account is drained first) with an
  auto-optimizer
- Social Security with COLA, employer match with shared annual pool, salary
  growth during accumulation
- Charts: balance over time, annual contributions (per-account + total),
  spending need vs shortfall
- Excel export of the full year-by-year projection

A user guide is included in [USER_GUIDE.md](./USER_GUIDE.md).

## Known limitations

- **Taxes are not yet modeled.** Withdrawals are shown gross of income and
  capital-gains tax (only the early-withdrawal 10% penalty on traditional
  retirement accounts before age 59.5 is applied). Treat any "Fixed annual
  spend" you enter as **pre-tax / gross income** until tax modeling lands.
- Roth 401(k) pre-59.5 withdrawals are currently treated as basis-first like
  Roth IRAs. Real Roth 401(k) plans use pro-rata basis-and-gains; this is on
  the to-do list.
- Returns are deterministic (single growth rate per account), not
  Monte Carlo.
- US-centric: contribution caps and account types follow US rules.

## Privacy

Everything runs locally in your browser. Scenarios are saved to your
browser's `localStorage`. Nothing is uploaded anywhere. There is no
analytics or tracking on this page.

This page also includes a `noindex, nofollow` meta tag and a `robots.txt`
asking search engines not to index it. Compliant crawlers will skip it;
non-compliant ones may not.

## Running locally

The page is a single self-contained HTML file. Save `index.html` to your
computer and open it in any modern browser. No server, no install, no
network calls.

## Feedback

Please don't make financial decisions based on this tool. If you spot a
bug or have a suggestion, open an issue on the repository.
