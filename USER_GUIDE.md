# Retirement Planner — User Guide

This planner is a deterministic, year-by-year projection of your retirement portfolio.
Everything runs locally in your browser — your inputs are saved only to this device's
local storage. There is no backend.

Use the inputs panel on the left to configure your scenario; results update on every
change. The right side shows charts, a year-by-year projection table, and an Excel export
button. Below is a reference for every input, control, chart, and table column.

> **Heads-up: income taxes on withdrawals are NOT yet modeled.** All withdrawal,
> spend, and balance numbers are shown *gross of income tax* — the only tax-like
> deduction the engine currently applies is the early-withdrawal penalty. See the
> *Taxes (not yet modeled)* section below for a full explanation and how to work
> around it for now.

## Top-level toolbar

**Hide / Show inputs** — Collapses the left sidebar to give the projection table more room.

**Save scenario (.json)** — Downloads a JSON file containing every input value (ages,
inflation, accounts, ranks, etc.). Useful for backup or sharing. Saved scenarios from
older versions of the app are auto-migrated when loaded.

**Load scenario** — Imports a previously-saved JSON file. Replaces all current inputs.

**Export to Excel** — Generates a .xlsx workbook with three sheets:
- *Inputs* — every input value used for the run
- *Accounts* — per-account configuration
- *Projection* — full year-by-year table including per-account columns

**Reset to defaults** — Clears your saved scenario and reverts every field to its
default value. Asks for confirmation first.

**User guide** — Opens this drawer. You're reading it now.

## Ages

**Current age** — Age you start the projection from. The first row in the table is
this age.

**Retirement age** — Age you stop contributing and start drawing down (unless Coast
FIRE is enabled, in which case contributions stop at the Coast age but the portfolio
keeps growing until retirement).

**End age (plan)** — Last age the projection runs to. Set this to your planning
horizon (commonly 90–100). The projection stops here regardless of remaining balance.

**Penalty-free withdrawal age** — Age at which early-withdrawal penalties no longer
apply (typically 59.5 in the U.S.). Withdrawals before this age from preTax accounts
or Roth gains pay the early-withdrawal penalty defined on each account.

## Coast FIRE

**Enable Coast FIRE** — When checked, contributions stop at the Coast start age, but
the portfolio continues to grow at the pre-retirement growth rate until you retire.
Useful for modeling "I've saved enough — now I just want my existing money to compound."

**Coast start age** — Age at which contributions stop. Must be earlier than your
retirement age, otherwise it has no effect.

## Inflation & compounding

**Inflation rate** — Annual inflation rate used to:
- Inflate your *Fixed annual spend* target each retirement year
- Convert between nominal $ and today's $ for the *Today $* column and summary stats
- Escalate per-account contribution caps year over year (compounded)
- Escalate Social Security benefits year over year (cost-of-living adjustment)

**Compounding frequency** — How often growth and contributions compound within each
year. Affects every account uniformly:
- *Monthly* (default) — most realistic for typical dollar-cost averaging. Annual growth
  rate `g` is split into 12 periods of rate `(1+g)^(1/12) − 1`, with one twelfth of
  the annual contribution applied at the end of each period.
- *Quarterly* — middle ground.
- *Annual* — treats the full year's contribution as a single end-of-year deposit.
  Slightly conservative because contributions get no in-year growth.

## Salary & employer match

**Salary** — Your current gross salary. Used to size the employer match pool and to
compute *percent-of-salary* contributions. Not directly added to the portfolio — only
the contribution amounts you enter on each account land in the portfolio.

**Salary growth** — Annual raise rate during the accumulation phase. Scales:
- Salary (and therefore the employer match pool)
- *Dollar-mode* contributions on every account
- *Percent-of-salary* contributions automatically (since they're a fraction of the
  grown salary)

Set to 0 to keep salary and contributions flat in nominal $ — silently conservative.

**Employer match %** — Percent of your current-year salary contributed by your
employer to match-eligible accounts. The total match pool each year is
`salary × match%`, capped at the sum of your own contributions to match-eligible
accounts. The pool is distributed across match-eligible accounts proportionally to
each account's own contribution. Match contributions are *not* counted against an
account's annual cap — they apply on top.

## Accounts

Each account is its own card. You can add, duplicate, remove, or disable accounts.
Disabled accounts are excluded from the projection but kept in the JSON so you can
toggle them back without losing the configuration.

### Account header
**Type dropdown** — Picks an account preset (Roth IRA, Traditional 401(k), HSA, etc.)
which sets the default tax treatment, penalty rate, and match-eligibility. Switch to
*Custom* to define your own.

**Display name** — Free-text label shown in the table and charts.

### Tax treatment
For preset account types (Roth IRA, Traditional 401(k), Brokerage, HSA, etc.) the
engine's internal tax-treatment flag is set automatically by the preset and is not
user-editable. The selector only appears when the account type is *Custom*, where
you can choose between three engine behaviors:
- *Taxable* — post-tax contributions, no early-withdrawal penalty. The engine does
  not currently apply income or capital-gains tax on withdrawals.
- *Roth / post-tax* — post-tax contributions form your *basis*, which is always
  penalty-free. Earnings on top (gains) carry the early-withdrawal penalty before the
  penalty-free age.
- *Pre-tax* — Traditional 401(k) / IRA / 403(b) etc. The full balance carries the
  early-withdrawal penalty before the penalty-free age. Set the penalty rate to 0 to
  model accounts like governmental 457(b) which have no penalty.

Note: this flag controls *how the engine accounts for basis vs. gains and the
early-withdrawal penalty* — not income tax. Income tax on withdrawals is not yet
modeled (see *Taxes (not yet modeled)* below).

### Balance & basis
**Balance** — Current account balance (today's $).

**Roth basis** — Sum of contributions for Roth accounts. Only basis is penalty-free
before the penalty-free age — gains on top carry the penalty rate.

### Contribution mode
**Contribution mode** — How the employee contribution is specified:
- *Dollar (monthly)* — A fixed monthly amount in today's $. Scales by salary growth
  during accumulation, so a $500/mo contribution today becomes $500 × (1+growth)^year
  at year *year*.
- *Percent of salary (annual)* — A percent of the current-year salary. Salary growth
  is applied automatically because the salary itself grows.

**Monthly contribution** / **Contribution % of salary** — Whichever field is shown
depends on the mode.

**Annual cap (optional)** — Optional cap on the *employee* contribution in today's $.
0 means uncapped. The cap escalates each year by the inflation rate (compounded), so
a $23,500 cap at 3% inflation becomes ~$31,580 in year 10. Excess contributions are
truncated (i.e. dropped). Employer match is *not* included in this cap; match is
applied on top of the capped employee contribution.

### Growth & penalty
**Pre-retirement growth** — Annual growth rate used during accumulation and Coast.

**Post-retirement growth** — Annual growth rate used after retirement age. Typically
lower than pre-retirement growth to model a more conservative allocation in retirement.

**Early-WD penalty** — Penalty rate applied when withdrawing before the penalty-free
age. Only applies to pre-tax accounts and Roth gains. Taxable accounts are never
penalized; Roth basis is always penalty-free.

**Eligible for employer match** — When checked, this account participates in the
employer match. The match pool is distributed across all eligible accounts in
proportion to each account's own contribution.

### Ranks
**Pre-penalty rank** — Withdrawal priority before the penalty-free age. Rank 1 is
drawn from first.

**Post-penalty rank** — Withdrawal priority on or after the penalty-free age. Rank 1
is drawn from first.

When *Auto-optimize* is on for the matching phase, these fields are disabled and
show the engine's recommended rank to the right of the dropdown.

## Withdrawal plan

The planner supports three withdrawal rules. Each computes annual gross spend
differently. *yearIdx* is years since you started the plan (yearIdx = 0 in your
current year). *inflFactor* = (1 + inflation)^yearIdx.

**Mode 1 — Fixed today's $**

    spend = wdFixed × inflFactor

You pick a real (today's-dollar) spend target. The nominal withdrawal grows with
inflation every year, so real spend is constant. Simple, predictable, and the
easiest mode to reason about.

**Mode 2 — % of retirement portfolio (Trinity-style)**

    spend = wdRate × portfolioAtRetirement × inflFactor

The withdrawal amount is locked in at retirement based on your portfolio's value
on the day you retire, then *inflated forward forever*. This is the classic 4%-rule
framing from the Trinity Study. Spend in real terms is constant — it does *not*
recalculate against your current balance.

Failure mode to be aware of: because spend keeps rising with inflation while the
portfolio can decline, a draw rate that looks safe over 30 years can still deplete
over very long horizons. Example: a 2.5% draw with 7% nominal growth and 3%
inflation has a real return of ~3.9%, but real spend rises ~3% per year relative
to a portfolio that can shrink in down decades — over a 70-year retirement (e.g.
endAge 120) the plan can run out even though the rate seems conservative. Stress-
test by extending endAge.

**Mode 3 — Endowment rule (% of current balance, recalculated yearly)**

    spend = wdRate × (sum of current account balances)

Each year the planner takes wdRate of whatever the portfolio is worth *right now*
(no inflation factor, no lock-in). Used by university endowments. Mathematically
self-correcting: as long as wdRate is less than the real growth rate, the
portfolio cannot run out — spending just shrinks (in real terms) during bad
stretches and rises during good ones. The trade-off is volatility in your
standard of living.

**Fixed annual spend** — Used only in Mode 1. Pre-tax (see *Taxes (not yet modeled)*
below). Inflated forward each retirement year.

**Withdrawal rate** — Used in Modes 2 and 3. In Mode 2 it's applied to the
portfolio value *at retirement* (locked). In Mode 3 it's applied to the *current*
balance every year.

**Pre-tax vs. post-tax spending (current behavior)**

Until tax modeling is implemented, treat *Fixed annual spend* (and the implied
spend in Modes 2 and 3) as **pre-tax / gross** withdrawal. The planner pulls that
full amount out of the portfolio without setting aside taxes, so if you want
$80k of after-tax spending and expect ~15% effective tax, enter ~$94k as your
fixed annual spend (or pick a wdRate that produces a comparable gross number).

Once account-type-aware tax modeling lands (see *Taxes (not yet modeled)*), this
input will switch to representing **net / post-tax** spending. The engine will
automatically gross up withdrawals — pulling enough extra from taxable buckets
to cover the tax bill so the net you receive matches your target.

## Waterfall — Pre-penalty / Post-penalty

The *waterfall* is the order in which accounts are drained to cover spending. The
planner has two independent waterfalls — one for years before the penalty-free age,
one for years on or after.

**Manual / Auto-optimize toggle**
- *Manual* — You set the rank for each account on its card. Lower numbers are drawn
  from first.
- *Auto-optimize* — The engine searches rank permutations and picks the one that
  maximizes ending balance / minimizes shortfalls. Recommended for most users.

**Cascade to next rank when bucket runs dry** — When ON (the default), once the
top-ranked account is exhausted, the engine moves to the next rank to keep covering
spending. When OFF, the engine *stops* at the empty rank and reports a shortfall
even though other accounts may still have money. Use OFF only to stress-test rigid
constraints like "never touch retirement accounts before penalty-free age."

**Tied ranks warning** — Appears in manual mode when two or more enabled accounts
share the same rank. Behavior is controlled by the *Tied ranks* setting below.

## Tied ranks

Controls how withdrawals are split when multiple accounts share the same rank.

- **Use list order (default)** — Drains the first-listed tied account first, then the
  next. Simple but order-dependent.
- **Draw proportionally** — Splits each year's withdrawal across the tied accounts
  in proportion to their available net capacity. Cascades to the next rank only when
  every account in the tied group is exhausted.

This setting affects both the pre- and post-penalty waterfalls. Untied scenarios
produce identical results in either mode.

## Social Security

**Include SS** — Toggles SS income on or off in retirement years.

**Start age** — Age at which SS benefits begin. Must be at or after retirement age
to have any effect.

**Monthly benefit (today's $)** — Your estimated monthly SS benefit in today's
dollars. Use the SSA Quick Calculator linked in the panel for an estimate. The
benefit grows each year by the inflation rate, mirroring SSA's annual COLA.

## Currency

**Display currency** — Changes the symbol and locale used for currency formatting in
the inputs, table, charts, and Excel export. The planner is currency-agnostic — it
just relabels the symbol; it does *not* convert values when you switch currencies.

## Charts

### Portfolio over time
Line chart showing your end-of-year total portfolio balance for every year in the
plan. The shaded background bands mark the four phases (Accumulate / Coast / Retire
pre-penalty / Retire post-penalty). The line is in nominal dollars. To see today's
purchasing power, use the *Today $* column in the table.

### Annual contributions
Overlapping (un-stacked) area chart of contributions by account each year, with a
*Total* line drawn on top showing the sum across all accounts. Each account's
series is rendered independently from the baseline (not additive) — the Total
line already conveys the sum, so account areas are translucent and overlap
directly. Use the toggles in the chart header to show or hide each account's
series or the Total line. Helpful for visualizing how contributions scale with
salary growth, hit caps, or stop entirely at the Coast or retirement age — and
which account each dollar is going to.

### Spending need vs shortfall
Line chart for retirement years comparing your *target spend* (red) against any
*shortfall* — i.e. spending the engine could not cover from any account or SS. A
visible shortfall line means you'll run out of money at that age under the current
assumptions.

### Pop-out button
Each chart has a small pop-out button in the top-right that opens it in a new
window. Useful for screenshots or side-by-side comparisons.

## Year-by-year projection table

The table shows one row per year from your current age through your end age.

### Common columns
- **Yr** — Year number, counting up from 1 at your current age.
- **Age** — Your age in that row.
- **Phase** — Accumulate / Coast / Retire <PenaltyAge / Retire PenaltyAge+.
- **Spend** — Target annual spending in nominal dollars (inflated from today's $).
- **SS** — Social Security income for the year (nominal $). Zero before SS start age.
- **Net WD** — Net withdrawal across all accounts *after* early-withdrawal penalties.
  This is what actually lands in your pocket and is what the engine sizes withdrawals
  against.
- **Penalty** — Total early-withdrawal penalty paid that year.
- **Short** — Shortfall: spending the engine could not cover. >0 means you ran out
  of available money at that age.
- **Total** — Total portfolio balance at end of year (nominal $).
- **Today $** — Same total, restated in today's purchasing power.

### Per-account columns
For each enabled account, two columns:
- *Withdrawal from <Account>* — Gross withdrawal that year (nominal $).
- *Balance — <Account>* — End-of-year balance (nominal $).

### Show / hide columns
The "Columns" button in the table toolbar lets you toggle column groups. Your
selection is saved to local storage.

### Pop-out button
Opens the table in a new window for printing or side-by-side review.

## Taxes (not yet modeled)

The planner currently does **not** model federal or state income tax on withdrawals.
Every withdrawal, spend, and balance figure shown in the charts and table is
*gross of income tax*. The only tax-like deduction the engine applies is the
**early-withdrawal penalty** (e.g. the 10% federal penalty on pre-59½ Traditional
401(k) / IRA withdrawals, and on Roth gains).

### What this means in practice
If your real-world target spend is, say, $80,000/yr post-tax and you'll be drawing
from Traditional 401(k) accounts, your *actual* gross withdrawal needs to be
higher than $80k to cover income tax. The planner will happily report "covered"
at $80k of gross withdrawals — but in real life you'd come up short by your
effective tax rate.

### Workaround for now
Until tax modeling is implemented, **inflate your spend target** to approximate
taxes:
- All-Roth retirement — no adjustment needed (Roth withdrawals are tax-free).
- Mostly Traditional / pre-tax — multiply your desired net spend by roughly
  *1 / (1 − effectiveTaxRate)*. For a 15% effective rate, that's about 1.18×. So
  $80k net → ~$94k gross spend target.
- Mixed sources — use a blended rate weighted by which accounts you'll draw from
  most heavily.

### Planned tax behavior (future version)
Taxes will be applied **by account type** at withdrawal time:
- **Roth (IRA + 401(k))** — tax-free; qualified withdrawals never taxed.
- **Traditional 401(k) / IRA / pre-tax** — grows tax-deferred; withdrawals taxed
  as ordinary income at your marginal bracket.
- **Brokerage / taxable** — the *gains* portion of each withdrawal taxed at
  long-term capital-gains rates; basis is returned tax-free.
- **HSA** — tax-free for qualified medical expenses; otherwise taxed as ordinary
  income (and penalized before the penalty-free age).

The engine will gross-up withdrawals so the *net* matches your spending target,
using inflation-adjusted federal brackets and an optional flat state rate.

## Tips & recipes

- **Stress-test your retirement age**: nudge *Retirement age* down by 5 years and
  see whether the *Spending vs shortfall* chart shows a shortfall before your end age.
- **Compare two scenarios**: *Save scenario* before making changes, run the new
  scenario, then *Load scenario* to revert. Use *Export to Excel* on each to compare
  side-by-side.
- **Model a bonus year**: temporarily duplicate an account, set its monthly
  contribution to your bonus / 12, and disable it after one year. (Single-year shocks
  aren't first-class — this is the workaround.)
- **Match a real IRS limit**: set the *Annual cap* on a 401(k) account to the current
  year's IRS limit (e.g. $23,500 for 2025). It will escalate with inflation each year
  to roughly track future IRS limit increases.
- **Pessimistic post-retirement growth**: set *Post-retirement growth* lower than
  *Pre-retirement growth* to model a more conservative allocation after you retire.

## Frequently asked questions

**Where is my data stored?**
In your browser's local storage on this device only. There is no server. Use *Save
scenario* to back up or share.

**Why does the same scenario give a different number after a refresh?**
It shouldn't — the projection is deterministic given the inputs. If you see a change,
check whether *Auto-optimize* is on for either waterfall (it sometimes finds a
slightly different equally-optimal rank assignment on re-evaluation).

**Why is my Roth IRA penalty $0 even though I'm under the penalty-free age?**
Roth withdrawals come out of *basis* first, which is always penalty-free. Penalties
only apply once you start withdrawing *gains*. The table breaks this down per row.

**Does the planner model taxes?**
No income / capital gains taxes. The planner is best treated as pre-tax of those —
you should set your spending target in pre-tax dollars and your account growth rates
in nominal pre-tax terms. Early-withdrawal *penalties* are modeled (per account).

**Does Social Security taxation get modeled?**
No — SS income is treated as gross. If a meaningful portion of your SS would be
taxed in your scenario, lower the *Monthly benefit* by your expected effective tax
rate to compensate.

**Why don't my contributions match `monthly × 12` in the first year?**
A few reasons: (1) the employer match is added on top, (2) salary growth scales
contributions starting at year 1, not year 0, but in year 0 the factor is 1, so
year-0 contribution should equal monthly × 12 + match. Check the *Annual cap* — if
it's set and your contribution exceeds it, the engine truncates.
