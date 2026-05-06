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

> 🛠️ **Deployment branch.** The live GitHub Pages site is currently built
> from the `development` branch, not `main`. New work lands on `development`
> first and is fast-forwarded to `main` once it's been smoke-tested.

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
- Side-by-side compare modal with up to three scenarios — KPI table,
  overlaid charts, and a live Monte Carlo summary row (success rate +
  median depletion age, computed in a Web Worker per slot)
- Named scenario library (up to 20 entries, stored in your browser) for
  quickly switching between or comparing saved scenarios
- Print / PDF view that hides controls and prints the dashboard plus the
  year-by-year table in a clean, paper-friendly layout

A user guide is included in [USER_GUIDE.md](./USER_GUIDE.md).

## Known limitations

- **Taxes are not yet modeled.** Withdrawals are shown gross of income and
  capital-gains tax. The only tax-like deduction the engine applies is the
  per-account early-withdrawal penalty (defaults to 10% on pre-tax accounts
  and Roth gains before the penalty-free age, but is configurable per
  account). Treat any "Fixed annual spend" you enter as **pre-tax / gross
  income** until tax modeling lands.
- Roth 401(k) pre-59.5 withdrawals are currently treated as basis-first like
  Roth IRAs. Real Roth 401(k) plans use pro-rata basis-and-gains; this is on
  the to-do list.
- Returns can be projected deterministically (a single growth rate per
  account) or stochastically via Monte Carlo simulation. Two return
  models are available: **Lognormal** (i.i.d. draws from a bell curve
  per account, fast and the default) and **Historical bootstrap**
  (block-resampled real returns from the Shiller S&P 500 + 10-year
  Treasury dataset, 1928–2023, which preserves volatility clustering and
  fat tails). Per-account volatility, 1,000–50,000 trials, and a
  multi-age retirement sweep are all supported. In bootstrap mode the
  volatility inputs are intentionally inactive — yearly returns come
  from the historical record itself — and Deep settings shows a
  per-account stock-like / bond-like classification readout based on a
  6%-real cutoff applied to each account's growth rate.
- US-centric: contribution caps and account types follow US rules.

## Privacy

Everything runs locally in your browser. Scenarios are saved to your
browser's `localStorage`. Nothing is uploaded anywhere. There is no
analytics or tracking on this page.

This page also includes a `noindex, nofollow` meta tag and a `robots.txt`
asking search engines not to index it. Compliant crawlers will skip it;
non-compliant ones may not.

## Running locally

This Pages repo only ships the *built* output — hashed JS/CSS bundles plus a
Web Worker for Monte Carlo. ES module + cross-origin rules mean it will not
run by double-clicking `index.html` over `file://`. Two ways to run it
locally:

**Option 1 — serve the built bundle.** Clone this repo and serve the
directory with any static server, for example:

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then open the printed `http://localhost:...` URL.

**Option 2 — build from source.** The source repo (separate from this Pages
repo) is a Vite + React + TypeScript project. Clone it, then:

```bash
npm install
npm run dev      # hot-reloading dev server
# or
npm run build    # produces a dist/ directory equivalent to this Pages repo
```

Either way, no backend or network calls are required at runtime — the app
stays entirely client-side.

## Feedback

Please don't make financial decisions based on this tool. If you spot a
bug or have a suggestion, open an issue on the repository.
