# Retirement Planner — User Guide

This planner is a deterministic, year-by-year projection of your retirement
portfolio. Everything runs locally in your browser — your inputs are saved
only to this device's local storage. There is no backend.

Use the inputs panel on the left to configure your scenario; results update
on every change. The right side shows charts, a year-by-year projection
table, and an Excel export button.

> **AI-assisted development.** This planner was built with substantial help
> from AI coding assistants (writing, reviewing, and refactoring source code,
> tests, and documentation). It is a personal project under active
> development and is **not professional financial advice**. Verify any
> number that matters against an independent source — a licensed financial
> advisor, the relevant IRS or plan rules, or your own spreadsheet — before
> relying on it for a real-world decision. The author and the AI tools used
> to build this make no warranty that the formulas, defaults, or outputs
> are correct or current.

> **Heads-up: income taxes on withdrawals are NOT yet modeled.** All
> withdrawal, spend, and balance numbers are shown *gross of income tax* —
> the only tax-like deduction the engine currently applies is the
> early-withdrawal penalty. See the *Tax modeling* section for the full
> explanation and the workaround for now.

---

This guide is grouped into six topical clusters that match the in-app
*User guide* drawer: *Getting Started*, *Inputs*, *Monte Carlo*,
*Sweeps & Compare*, *Reading Results*, and *Export & Operations*. Each
section below has a small `?` icon next to the matching control in the
app — clicking it deep-links here.

## Quickstart

Three steps will get you a usable plan in about five minutes.

**1. Enter your basic inputs.** First-time visitors are greeted by a
10-step setup wizard that walks through every core input — ages,
inflation, salary, employer match, accounts, withdrawal plan, Social
Security, and Coast FIRE — with plain-language explanations and pre-filled
defaults. Each step starts with reasonable values so you can move quickly
through the ones that don't apply. To skip the wizard and start with the
planner's default scenario, click *Skip / use defaults* in the wizard
header. You can re-run the wizard at any time from the **Reset** button
in the toolbar.

**2. Read the deterministic projection.** Once inputs are in, the right
side immediately shows a *Portfolio over time* chart, a *Spending need vs
shortfall* chart, three KPI tiles, and a year-by-year table. The
deterministic projection assumes every account earns its growth rate
exactly, every year. Use it to sanity-check the shape of the plan: is the
portfolio still growing into retirement? Is spending fully covered? Does
the table show a shortfall before your end age?

**3. Stress-test with Monte Carlo.** When the deterministic projection
looks plausible, scroll to the *Monte Carlo simulation* panel and click
*Run Monte Carlo*. The default — 10,000 trials, lognormal returns — runs
in a couple of seconds and adds percentile bands to the chart, p5/p95
ranges to the KPI tiles, and (when relevant) the Monte-Carlo runs-out
pill. For a real plan, also try *Historical bootstrap* and *Drift-corrected
bootstrap* (see *Return models*) — they typically lower success rates by
5–15 percentage points for aggressive early-retirement scenarios, which
is exactly the sequence-of-returns risk you want to see before
committing.

**Sticky toolbar.** The top toolbar stays pinned while you scroll. From
left to right: hide/show inputs, undo/redo, **Save** (opens the
scenario library in save mode — also exposes *Download as .json*),
**Load** (opens the library in manage mode — also exposes
*Upload .json…*), **Compare scenarios**, **Export** (drop-down with
*Export to Excel…* and *Print / PDF*), theme toggle,
⚙︎ gear (Deep settings), user guide, reset menu. Editing inputs — undo
and redo work via the toolbar buttons or `Cmd/Ctrl+Z` /
`Shift+Cmd/Ctrl+Z`; history goes back up to 50 edits and clears on reset.

## Ages and horizon

**Current age** — Age you start the projection from. The first row in the
table is this age.

**Retirement age** — Age you stop contributing and start drawing down
(unless Coast FIRE is enabled, in which case contributions stop at the
Coast age but the portfolio keeps growing until retirement).

**End age (plan)** — Last age the projection runs to. Set this to your
planning horizon (commonly 90–100). The projection stops here regardless
of remaining balance.

**Penalty-free withdrawal age** — Age at which early-withdrawal penalties
no longer apply (typically 59½ in the U.S.). Withdrawals before this age
from preTax accounts or Roth gains pay the early-withdrawal penalty
defined on each account.

**Coast FIRE** is now toggled in **Deep settings → Coast FIRE**. When
enabled, contributions stop at the *Coast start age*, but the portfolio
continues to grow at the pre-retirement growth rate until you retire.
Useful for modeling "I've saved enough — now I just want my existing
money to compound." A *Coast start age* input slides into the Ages
section between *Current age* and *Retirement age*.

**Validation banners.** A yellow banner appears at the top of a section
when inputs are structurally inconsistent (e.g. retirement age earlier
than current age, Social Security claim before current age, Coast age
outside the working window). The projection still runs so you can see
the effect, but the banner flags the issue until you fix it. Validation
is structural only — no tax-law or contribution-limit checks. For
401(k) / IRA / HSA contribution caps, see the
[IRS retirement plan limits page](https://www.irs.gov/retirement-plans/plan-participant-employee/retirement-topics-contributions).

## Income and savings

**Salary** — Your current gross salary. Used to size the employer match
pool and to compute *percent-of-salary* contributions. Not directly added
to the portfolio — only the contribution amounts you enter on each
account land in the portfolio.

**Salary growth** — Annual raise rate during the accumulation phase.
Scales:

- Salary (and therefore the employer match pool)
- *Dollar-mode* contributions on every account
- *Percent-of-salary* contributions automatically (since they're a
  fraction of the grown salary)

Set to 0 to keep salary and contributions flat in nominal $ — silently
conservative.

**Employer match %** — Percent of your current-year salary contributed by
your employer to a single routed account. The match pool each year is
`salary × match%`, capped at the routed account's own annual contribution
(you can't match more than you put in). Match contributions are *not*
counted against the routed account's annual cap — they apply on top.

The destination account is configured in **Deep settings → Employer match
routing**. The dropdown lists every enabled account and defaults to
*Auto*, which resolves in this order:

1. The account you explicitly pick in the dropdown (if any).
2. The first enabled Traditional 401(k).
3. The first enabled account of any type.
4. If no accounts are enabled, the match contributes nothing.

**Inflation rate** — Annual inflation rate used to:

- Inflate your *Fixed annual spend* target each retirement year
- Convert between nominal $ and today's $ for the *Today $* column and
  summary stats
- Escalate per-account contribution caps year over year (compounded)
- Escalate Social Security benefits year over year (cost-of-living
  adjustment)

**Compounding frequency** — How often growth and contributions compound
within each year. Affects every account uniformly:

- *Monthly* (default) — most realistic for typical dollar-cost averaging.
  Annual growth rate `g` is split into 12 periods of rate
  `(1+g)^(1/12) − 1`, with one twelfth of the annual contribution applied
  at the end of each period.
- *Quarterly* — middle ground.
- *Annual* — treats the full year's contribution as a single end-of-year
  deposit. Slightly conservative because contributions get no in-year
  growth.

## Spending

The planner supports three withdrawal rules. Each computes annual gross
spend differently. *yearIdx* is years since you started the plan
(yearIdx = 0 in your current year). *inflFactor* = (1 + inflation)^yearIdx.

**Mode 1 — Fixed today's $**

    spend = wdFixed × inflFactor

You pick a real (today's-dollar) spend target. The nominal withdrawal
grows with inflation every year, so real spend is constant. Simple,
predictable, and the easiest mode to reason about.

**Mode 2 — % of retirement portfolio (Trinity-style)**

    spend = wdRate × portfolioAtRetirement × inflFactor

The withdrawal amount is locked in at retirement based on your
portfolio's value on the day you retire, then *inflated forward forever*.
This is the classic 4%-rule framing from the Trinity Study. Spend in
real terms is constant — it does *not* recalculate against your current
balance.

Failure mode to be aware of: because spend keeps rising with inflation
while the portfolio can decline, a draw rate that looks safe over 30
years can still deplete over very long horizons. Stress-test by
extending end age.

**Mode 3 — Endowment rule (% of current balance, recalculated yearly)**

    spend = wdRate × (sum of current account balances)

Each year the planner takes wdRate of whatever the portfolio is worth
*right now* (no inflation factor, no lock-in). Used by university
endowments. Mathematically self-correcting: as long as wdRate is less
than the real growth rate, the portfolio cannot run out — spending just
shrinks (in real terms) during bad stretches and rises during good ones.
The trade-off is volatility in your standard of living.

**Pre-tax vs. post-tax spending (current behavior).** Until tax modeling
is implemented, treat *Fixed annual spend* (and the implied spend in
Modes 2 and 3) as **pre-tax / gross** withdrawal. The planner pulls that
full amount out of the portfolio without setting aside taxes, so if you
want $80k of after-tax spending and expect ~15% effective tax, enter
~$94k as your fixed annual spend (or pick a wdRate that produces a
comparable gross number). See *Tax modeling* for the full explanation.

## Social Security

**Include SS** — Toggles SS income on or off in retirement years.

**Start age** — Age at which SS benefits begin. Must be at or after
retirement age to have any effect.

**Monthly benefit (today's $)** — Your estimated monthly SS benefit in
today's dollars. Use the SSA Quick Calculator linked in the panel for an
estimate. The benefit grows each year by the inflation rate, mirroring
SSA's annual COLA.

SS taxation is **not** modeled — SS income is treated as gross. If a
meaningful portion of your SS would be taxed in your scenario, lower the
*Monthly benefit* by your expected effective tax rate to compensate. See
*Tax modeling* below.

## Accounts

Each account is its own card. You can add, duplicate, remove, or disable
accounts. Disabled accounts are excluded from the projection but kept in
the JSON so you can toggle them back without losing the configuration.

**Drag-and-drop reordering.** Each account card has a drag handle (`⋮⋮`)
on the left of its header. Click and drag to reorder accounts. The new
order is reflected immediately in the projection and Excel export.
Listed order also acts as the tiebreaker when two accounts share the
same withdrawal rank.

### Header

**Type dropdown** — Picks an account preset (Roth IRA, Traditional
401(k), HSA, etc.) which sets the default tax treatment, penalty rate,
and match-eligibility. Switch to *Custom* to define your own.

**Display name** — Free-text label shown in the table and charts.

### Tax treatment

For preset account types (Roth IRA, Traditional 401(k), Brokerage, HSA,
etc.) the engine's internal tax-treatment flag is set automatically by
the preset and is not user-editable. The selector only appears when the
account type is *Custom*, where you can choose between three engine
behaviors:

- *Taxable* — post-tax contributions, no early-withdrawal penalty. The
  engine does not currently apply income or capital-gains tax on
  withdrawals.
- *Roth / post-tax* — post-tax contributions form your *basis*, which is
  always penalty-free. Earnings on top (gains) carry the early-withdrawal
  penalty before the penalty-free age.
- *Pre-tax* — Traditional 401(k) / IRA / 403(b) etc. The full balance
  carries the early-withdrawal penalty before the penalty-free age. Set
  the penalty rate to 0 to model accounts like governmental 457(b) which
  have no penalty.

Note: this flag controls *how the engine accounts for basis vs. gains
and the early-withdrawal penalty* — not income tax. Income tax on
withdrawals is not yet modeled (see *Tax modeling*).

### Balance, basis, contributions

**Balance** — Current account balance (today's $).

**Roth basis** — Sum of contributions for Roth accounts. Only basis is
penalty-free before the penalty-free age — gains on top carry the
penalty rate.

**Contribution mode** — How the employee contribution is specified:

- *Dollar (monthly)* — A fixed monthly amount in today's $. Scales by
  salary growth during accumulation, so a $500/mo contribution today
  becomes $500 × (1+growth)^year at year *year*.
- *Percent of salary (annual)* — A percent of the current-year salary.
  Salary growth is applied automatically because the salary itself
  grows.

**Annual cap (optional)** — Optional cap on the *employee* contribution
in today's $. 0 means uncapped. The cap escalates each year by the
inflation rate (compounded), so a $23,500 cap at 3% inflation becomes
~$31,580 in year 10. Excess contributions are truncated (i.e. dropped).
Employer match is *not* included in this cap; match is applied on top of
the capped employee contribution.

### Growth & penalty

**Pre-retirement growth** — Annual growth rate used during accumulation
and Coast.

**Post-retirement growth** — Annual growth rate used after retirement
age. Typically lower than pre-retirement growth to model a more
conservative allocation in retirement.

**Early-WD penalty** — Penalty rate applied when withdrawing before the
penalty-free age. Only applies to pre-tax accounts and Roth gains.
Taxable accounts are never penalized; Roth basis is always penalty-free.

### Withdrawal waterfall

The *waterfall* is the order in which accounts are drained to cover
spending. The planner has two independent waterfalls — one for years
before the penalty-free age (*Pre-penalty rank*) and one for years on or
after (*Post-penalty rank*). Rank 1 is drawn from first.

**Manual / Auto-optimize toggle**

- *Manual* — You set the rank for each account on its card.
- *Auto-optimize* — The engine searches rank permutations and picks the
  one that maximizes ending balance / minimizes shortfalls. Recommended
  for most users.

**Cascade to next rank when bucket runs dry** — When ON (the default),
once the top-ranked account is exhausted, the engine moves to the next
rank to keep covering spending. When OFF, the engine *stops* at the
empty rank and reports a shortfall even though other accounts may still
have money.

**Tied ranks** — When two or more enabled accounts share the same rank:
*Use list order* drains the first-listed tied account first; *Draw
proportionally* splits each year's withdrawal across the tied accounts
in proportion to their available net capacity, cascading only when every
account in the tied group is exhausted.

## Tax modeling

The planner currently does **not** model federal or state income tax on
withdrawals. Every withdrawal, spend, and balance figure shown in the
charts and table is *gross of income tax*. The only tax-like deduction
the engine applies is the **early-withdrawal penalty** (e.g. the 10%
federal penalty on pre-59½ Traditional 401(k) / IRA withdrawals, and on
Roth gains).

### What this means in practice

If your real-world target spend is, say, $80,000/yr post-tax and you'll
be drawing from Traditional 401(k) accounts, your *actual* gross
withdrawal needs to be higher than $80k to cover income tax. The planner
will happily report "covered" at $80k of gross withdrawals — but in real
life you'd come up short by your effective tax rate.

### Workaround for now

Until tax modeling is implemented, **inflate your spend target** to
approximate taxes:

- All-Roth retirement — no adjustment needed (Roth withdrawals are
  tax-free).
- Mostly Traditional / pre-tax — multiply your desired net spend by
  roughly *1 / (1 − effectiveTaxRate)*. For a 15% effective rate, that's
  about 1.18×. So $80k net → ~$94k gross spend target.
- Mixed sources — use a blended rate weighted by which accounts you'll
  draw from most heavily.

### Planned tax behavior (future version)

Taxes will be applied **by account type** at withdrawal time:

- **Roth (IRA + 401(k))** — tax-free; qualified withdrawals never taxed.
- **Traditional 401(k) / IRA / pre-tax** — grows tax-deferred;
  withdrawals taxed as ordinary income at your marginal bracket.
- **Brokerage / taxable** — the *gains* portion of each withdrawal taxed
  at long-term capital-gains rates; basis is returned tax-free.
- **HSA** — tax-free for qualified medical expenses; otherwise taxed as
  ordinary income (and penalized before the penalty-free age).

The engine will gross-up withdrawals so the *net* matches your spending
target, using inflation-adjusted federal brackets and an optional flat
state rate. SS taxation will be modeled at the same time.

## Monte Carlo overview

The deterministic projection assumes every account earns its growth rate
exactly, every year, forever. Real markets don't work that way — a bad
sequence of returns early in retirement can deplete a portfolio that
would have survived comfortably with the same long-term *average* return
spread differently across years. This is called **sequence-of-returns
risk**, and it's the single biggest blind spot of any single-line
retirement projection.

Monte Carlo simulation tackles this by running your scenario thousands
of times with randomized returns each year, then summarizing the
distribution of outcomes. It's optional and opt-in — you have to click
*Run Monte Carlo* in the panel below the projection charts. Until then,
the planner shows only the deterministic line.

**Trial count.** The Trials selector offers 1,000 / 2,000 / 5,000 /
10,000 / 25,000 / 50,000 trials. Default is 10,000 for a single-age run
and drops to 2,000 when the *Multi-age sweep* checkbox is on (the sweep
does up to 41 per-age runs in parallel, so fewer trials per age keeps
the total cost reasonable). More trials = smoother percentile bands and
more stable success-rate estimates, but longer compute time. 10,000 is
plenty for typical planning; 25,000 or 50,000 is useful when
stress-testing tail outcomes (5th/95th percentile become much more
stable at higher counts).

While a run is in progress the *Run* button shows a progress bar that
fills from left to right with a percentage label, so you can see the
simulation is working even on slower devices.

**Stale results.** Monte Carlo runs in a Web Worker and the results are
cached against a hash of your inputs. The moment you edit any field, the
cached result is flagged *Stale* — the fan-chart bands are hidden, and
the success pill plus the p5/p95 ranges on the KPI tiles disappear
entirely until you re-run. Toggling theme or chart palette does *not*
mark Monte Carlo stale (those are presentation-only changes and don't
affect the underlying simulation). Switching display currency also keeps
the result fresh because no underlying numeric input changes.

**What the simulation models.** Returns are sampled per account per year
according to the configured *Return model* (see below). Contributions,
salary growth, employer match, withdrawal waterfall, Social Security,
and inflation all run through the same engine as the deterministic
projection — only the per-year per-account return is randomized.
Inflation itself is held at the input value (not stochastic) so you're
isolating market-return uncertainty.

## Return models

**Deep settings → Monte Carlo → Return model** picks how the simulator
generates each year's random return. Two top-level choices, with a
sub-toggle on the second:

- **Lognormal (i.i.d., default).** Each year independently draws from a
  bell curve centered on the account's growth rate, scaled by the
  configured volatility. Fast, simple, and the textbook default — but it
  assumes years have no memory of one another, so back-to-back bad years
  show up only by coincidence. This understates *sequence-of-returns
  risk* for early retirees.

- **Historical bootstrap (1928–2023 real returns).** Resamples blocks of
  consecutive years from the actual market record (Robert Shiller's
  S&P 500 + 10-year Treasury dataset, deflated by CPI so all returns are
  *real*). Preserves volatility clustering and the genuine fat tails of
  the historical record. See *Historical bootstrap* for mechanics, and
  *Drift-corrected bootstrap* for the opt-in sub-toggle that aligns the
  bootstrap's long-run mean with your account's growth assumption.

**When to use which.** Lognormal is the right starting point — it's
faster and the bell-curve assumption is fine when you're just sliding
inputs around to get a feel for trade-offs. Switch to bootstrap when
you're pressure-testing a real plan: bootstrap success rates are
typically 5–15 percentage points lower than lognormal for aggressive
early-retirement scenarios, and that gap is exactly the
sequence-of-returns risk you want to see before committing. Bootstrap
also exposes specific historical decades — 1929, 1966, 2000 — that would
have killed otherwise-plausible plans, so the depletion-age histogram
becomes a much sharper diagnostic.

## Historical bootstrap

When *Historical bootstrap* is selected, for each trial the simulator
picks a random start year and the next *N* consecutive years of
real returns come straight from the historical record (S&P 500 for
stock-like accounts, 10-year Treasury for bond-like). When the block
ends, a new random start is picked. Block length defaults to 5 years;
see *Block length* for tuning.

**Account classification.** Each account is treated as **stock-like**
(assumed return ≥ 6% real) or **bond-like** (< 6% real) based on its
`growthPre` / `growthPost`. Stock-like accounts pull the year's S&P 500
real return; bond-like accounts pull the 10-year Treasury real return.
This sidesteps asking you to specify a per-account stock/bond split.

When bootstrap mode is selected, *Deep settings* shows an **Account
classification (bootstrap mode)** block listing every enabled account
with its assumed return and its resulting *stock-like* / *bond-like*
label. To flip an account between the two series, edit its **Growth
pre** value in the sidebar so it crosses the 6%-real cutoff.

**Volatility inputs are ignored in bootstrap mode** — both the global
pre/post sigmas next to the *Run* button and the per-account override
toggle. Volatility in bootstrap mode comes from the historical record
itself, not from the sigma fields. The corresponding inputs grey out so
this is visible. Switch back to *Lognormal* to use the volatility knobs.

**Bootstrap is real-return.** Historical returns in this dataset are
already inflation-adjusted. The downstream engine still applies its own
inflation factor to spending, so combining real-return shocks with the
engine's inflation produces a coherent "stress against history"
simulation. The drift between the historical mean and your assumed
growth rate is intentional — bootstrap is meant to expose what actually
happened, not what was expected. If you'd rather align the long-run
mean with your assumption while keeping the historical *shape*, see the
next section.

## Drift-corrected bootstrap

**Drift-corrected bootstrap** is an opt-in sub-toggle inside the
*Historical bootstrap* setting (Deep settings → Monte Carlo). When off
(the default), bootstrap behavior is byte-identical to the original
historical bootstrap described above.

When on, each historical real return is rescaled so the bootstrap's
long-run **geometric** mean matches your account's assumed growth rate
while the *shape* of the historical distribution (volatility, skew, fat
tails, year-to-year clustering) is preserved up to a constant
multiplier. The math is intentionally simple:

    scale = (1 + assumed_geom) / (1 + historical_geom)
    r'    = (1 + r) × scale − 1

The historical geometric means used as the reference point are computed
from the same 1928–2023 dataset:

- S&P 500 real ≈ 9.2% (`STOCK_GEOM_MEAN_REAL`)
- 10-year Treasury real ≈ 2.0% (`BOND_GEOM_MEAN_REAL`)

The classifier (stock-vs-bond via the 6%-real cutoff) still picks which
historical series each account follows; the drift step then nudges that
series toward the magnitude of the user's assumption, separately for
*pre-* and *post-retirement* growth (since `growthPre` and `growthPost`
can land on opposite sides of the equity floor and so might pick
different historical series).

**When to use it.** Use drift correction when you want the
sequence-of-returns *risk shape* of the historical record but you're
modeling an asset whose long-run expected return differs from what U.S.
markets delivered 1928–2023 — for example, an international portfolio,
a bond-heavy allocation with non-Treasury credit exposure, or an
explicit forward-looking view ("I expect 5% real on stocks going
forward, not 9%"). Without drift correction, the bootstrap's long-run
mean simply *is* the historical mean, regardless of what you put in
*Pre-retirement growth*. With drift correction, the bootstrap's long-run
mean tracks your input, and the histogram of trial-by-trial returns
inherits the historical shape.

**When to leave it off.** Leave it off when you specifically want
"what would have happened to a U.S. stock/bond portfolio under the
1928–2023 record" — the textbook bootstrap stress test. The historical
record's high stock mean drives high success rates; turning on drift
correction with a 5% assumed return will (correctly) lower them.

**Diagnostic.** Compare the success rate with drift correction *off*
versus *on*. The gap is exactly your model risk: how much of your plan's
robustness comes from assuming the U.S. equity market repeats its
historical performance.

## Block length

**Block length** (Deep settings → Monte Carlo) controls how many
consecutive historical years are pulled at a time during bootstrap.
Default is 5 years (the academic standard).

**Why blocks at all.** A 1-year block is just IID resampling — it loses
the autocorrelation that makes 1929 → 1930 → 1931 a contiguous
catastrophe. Longer blocks preserve that volatility clustering: when a
1929 draw lands, 1930 and 1931 are dragged along with it, which is
exactly the kind of multi-year sequence the lognormal model misses.

**Tuning.** 5 is the standard for U.S. equity / Treasury data and is a
reasonable default. Longer blocks (10+) preserve more autocorrelation
but at the cost of resampling diversity — with only ~96 historical
years, a 10-year block covers ~10% of the dataset, so trials become
more correlated with each other. Shorter blocks (1–3) approach IID
behavior and weaken the sequence-of-returns signal. Stick with 5 unless
you have a specific reason to change it.

Block length applies to both *Historical bootstrap* and *Drift-corrected
bootstrap*; the only difference between the two is the per-year scaling
applied after the block is drawn.

## Volatility

The Monte Carlo panel (next to the *Run* button) has two global
volatility inputs — used **only when Return model is set to Lognormal**:

- **Pre-retirement volatility** — sigma during accumulate / coast.
- **Post-retirement volatility** — sigma after retirement age.

These drive the random sampling for *every* account by default. Sensible
defaults:

- **Stock-heavy** (Roth IRA, 401(k), Brokerage): 15% pre / 10% post
- **Bond / mixed**: 5% pre / 4% post
- **Cash / Savings**: 1% pre / 1% post

**Per-account override.** In **Deep settings → Monte Carlo**, toggle
**Use per-account volatility** to switch from the two global inputs to
the *Pre-retirement volatility* and *Post-retirement volatility* fields
on each account card. When that toggle is on, the global inputs in the
MC panel grey out so it's clear they're inactive. Setting an account's
volatility to 0 makes it behave deterministically.

> **Bootstrap mode ignores volatility inputs.** When *Return model* is
> set to *Historical bootstrap* (with or without drift correction), all
> volatility inputs — both the global pre/post sigmas and the
> per-account override toggle — are **ignored** and greyed out.
> Volatility in bootstrap mode comes from the historical record itself,
> not from the sigma fields.

## Multi-age sweep

Turning on the **Multi-age sweep** checkbox in the MC panel runs an
entire range of retirement ages — *retirement age ± 20 years*, in 1-year
steps, clipped to your *current age* and *end age*. The sweep runs ages
in parallel using a worker pool sized to your CPU (up to 8 workers), so
a 41-age sweep typically finishes in well under a minute on a modern
laptop.

While the sweep is running, the *Run* button shows `Running MC… N / M
ages` with a slim progress bar beneath it; a *Cancel* button lets you
bail out at any point. When complete, two visualisations appear below
the single-age fan and tables:

- **Sweep chart** — four lines on a dual-axis grid: P-success (green,
  left axis 0–100%) and median / p5 / p95 ending balance in today's $
  (right axis). The currently-selected retirement age is highlighted
  with a dashed vertical line so you can see exactly which row drives
  the headline KPIs.
- **Sweep heatmap** — one row per swept age, four columns (P-success,
  median, p5, p95), each column independently colour-graded so the
  trade-off is visible at a glance: red → amber → green for success
  rate, pale → saturated for dollar metrics.

**Live retire-age scrubbing.** A sweep result keeps the fan, the
success pill, and the KPI ranges fresh across the whole sweep range —
you can nudge the *Retirement age* slider anywhere within ±20 years of
the age you swept and the panel will silently swap to the cached result
for the new age, no re-run required. Editing any *non*-retirement-age
input (salary, spend, growth rates, etc.) discards the sweep cache the
same way it discards a single-age result.

The sweep cache is held in memory only — it isn't persisted across
browser sessions and isn't included in saved scenario files. Toggle the
checkbox off to discard it.

## Scenario compare

**Compare scenarios** (toolbar) opens a comparison modal that lets you
stack the current scenario against up to two other saved scenarios.
Each non-current slot has a *Load from library…* dropdown (when the
library has entries) plus the original *Choose file…* button for `.json`
imports. The modal shows:

- A KPI table — portfolio at retirement, runs-out age, contributing
  years, first-year withdrawal, ending balance, total penalty paid.
- A small **Monte Carlo summary** row — success rate plus median
  depletion age, computed with 2,000 trials per scenario.
- Three overlaid charts — portfolio over time, annual contributions,
  out-of-pocket spending.

The current scenario is always pinned to the first slot. Use this to
A/B test changes without overwriting your live inputs: save the
baseline, edit the live scenario, then open compare to see the
difference side by side.

## Scenario library

**Save / Load** (toolbar) opens an in-browser scenario library. *Save*
lets you name the current scenario and store it locally; *Load* opens
the manage view where you can load, delete, or wipe entries. The
library is capped at **20 scenarios** (each ~2–4 KB) so it cannot
balloon your browser storage. Saving the same name twice overwrites in
place.

Saved scenarios live in this browser only — they don't sync across
devices, and opening the planner in a different browser or on a
different device starts you with an empty library. To move scenarios
between devices, use the **Download as .json** button inside the Save
modal, then **Upload .json…** inside the Load modal on the other
device. The library is preserved by the regular *Reset to defaults*
flow; only *Clear all data* (in the Reset menu) wipes it.

For a full-fidelity, version-portable backup, use **Download as .json**
from inside the Save modal — it downloads a single JSON file
containing every input value (ages, inflation, accounts, ranks, etc.).
Saved scenarios from older versions of the app are auto-migrated when
loaded.

## KPIs

Three headline KPI tiles sit above the projection chart and summarize
the deterministic run, with Monte Carlo overlays added once you've run
MC:

- **Portfolio at retirement** — Nominal $ end-of-year balance the year
  you retire. With a fresh MC result, the tile gains a two-line
  `↑ p95` / `↓ p5` range below the deterministic figure showing the
  95th- and 5th-percentile outcomes from Monte Carlo.
- **Plan ending balance** — Today's $ end-of-year balance at end age.
  With a fresh MC result, the same `↑ p95` / `↓ p5` range appears
  underneath in matching units (today's $).
- **Portfolio runs out** — A pill showing the deterministic *runs out
  at age _* outcome. With a fresh MC result, a second pill below
  summarizes the Monte Carlo distribution. Severity is encoded by the
  pill *colour*; the line order and phrasing are the same in every
  failing tier so the numbers don't reorder when you nudge the
  retirement-age slider.

  - **Green** — at least 95% of trials make it to plan-end ("95% chance:
    lasts to age _"). No subtitle. See *First success age* for how this
    threshold is determined.
  - **Yellow** — 50–94% of trials succeed.
  - **Red** — fewer than half succeed.

  Yellow and red pills both show the same two lines:
  - "**50% of trials run out before age _**" — the typical failure age
    (half the simulated paths run out by this point).
  - "5% of trials run out before age _" — the bad-tail age (only 1-in-20
    of the simulated paths run out before this).

  With a high success rate it's normal for the 5% age to land *later*
  than the 50% age — the few failing trials cluster near plan-end rather
  than spreading across early ages.

The MC ranges grey out when results are stale.

## Projection chart

Line chart showing your end-of-year total portfolio balance for every
year in the plan. The shaded background bands mark the four phases
(Accumulate / Coast / Retire pre-penalty / Retire post-penalty).

**Chart-units toggle.** Above the chart, a segmented
`[Nominal $ | Today's $]` control restates *every* series on the chart
in the chosen units — each per-account line, the total, and (when Monte
Carlo has been run) every percentile band and the median trial.
Toggling it does not change any other chart or the projection table.

**Y-axis zoom.** Three small buttons in the chart's top-right control
the Y-axis ceiling: `+` zooms in (lowers the ceiling, useful for
inspecting the early-accumulation years when later balances dwarf
them), `−` zooms out, and `⟲` resets to the data-driven default. Each
step changes the ceiling by a factor of 1.5. The default ceiling is
*Total × 1.1* — the deterministic Total line plus 10% headroom —
regardless of whether the Monte Carlo fan is visible; when the fan is
on, any portion that pokes above the ceiling is simply clipped. The
lower bound stays at $0. Any input change or a flip of the
*Nominal $ / Today's $* toggle automatically resets the zoom step to
its default.

**Monte Carlo fan.** When you've run MC, the chart gains translucent
percentile bands. The wider light band marks the 5th–95th percentile
range; the darker inner band marks the 25th–75th. A dashed line plots
the median trial. The deterministic projection lines stay on top so you
can compare the single-line estimate to the distribution. Bands
automatically disappear when you change any input that would affect the
simulation; re-run Monte Carlo to refresh them. *Multi-age sweep* runs
keep the bands fresh as you slide the *Retirement age* control anywhere
within the sweep range.

**Show MC fan toggle.** Once you've run Monte Carlo, the chart shows a
*Show MC fan* checkbox. Untick it to hide the percentile fan while
keeping the Monte Carlo *median* (P50) line visible — useful when
account balance lines are dwarfed by the fan envelope.

**Other charts.** Below the projection chart:

- *Annual contributions* — Overlapping (un-stacked) area chart of
  contributions by account each year, with a *Total* line drawn on top.
- *Spending need vs shortfall* — Line chart for retirement years
  comparing your *target spend* (red) against any *shortfall*. A
  visible shortfall line means you'll run out of money at that age
  under the current assumptions.

Each chart has a small pop-out button that opens it in a new window for
screenshots or side-by-side comparisons.

## Year-by-year table

The table shows one row per year from your current age through your end
age.

### Common columns

- **Yr** — Year number, counting up from 1 at your current age.
- **Age** — Your age in that row.
- **Phase** — Accumulate / Coast / Retire <PenaltyAge / Retire
  PenaltyAge+.
- **Spend** — Target annual spending in nominal dollars (inflated from
  today's $).
- **SS** — Social Security income for the year (nominal $). Zero before
  SS start age.
- **Net WD** — Net withdrawal across all accounts *after*
  early-withdrawal penalties. This is what actually lands in your
  pocket and is what the engine sizes withdrawals against.
- **Penalty** — Total early-withdrawal penalty paid that year.
- **Short** — Shortfall: spending the engine could not cover. >0 means
  you ran out of available money at that age.
- **Total** — Total portfolio balance at end of year (nominal $).
- **Today $** — Same total, restated in today's purchasing power.

### Per-account columns

For each enabled account, two columns:

- *Withdrawal from <Account>* — Gross withdrawal that year (nominal $).
- *Balance — <Account>* — End-of-year balance (nominal $).

### Show / hide columns

The "Columns" button in the table toolbar lets you toggle column groups.
Your selection is saved to local storage.

### Pop-out button

Opens the table in a new window for printing or side-by-side review.

## First success age

In the *Multi-age sweep* result, the **first success age** is the
earliest retirement age in the swept range whose Monte-Carlo success
rate is **≥ 95%**. It's the headline answer to "when can I retire with
high confidence?" given your current inputs and assumptions.

**How it's computed.** The sweep produces one P-success per swept age.
The engine scans those values from young to old and returns the lowest
age where P-success crosses the 95% line. If no age in the range
clears 95%, no first-success age is reported (the headline pill stays
red or yellow).

**Why 95% and not 100%.** Reaching 100% requires every single trial,
including the 1929 / 1966 / 2000 cohort, to make it to plan-end. That's
an extraordinarily high bar that rejects retirement plans most
practitioners would consider sound. 95% — one failure in twenty — is
the threshold the green KPI pill uses, and it's where the
sweep's *first success* line gets drawn.

**Reading it with bootstrap.** Bootstrap success rates are typically
5–15 percentage points lower than lognormal for aggressive
early-retirement plans. The first-success age under bootstrap is often
2–5 years later than under lognormal — that gap is the
sequence-of-returns risk made tangible. Run the sweep under both models
to see how much your plan's "earliest safe retire age" depends on which
return model you trust.

## Excel export

**Export → Export to Excel…** (toolbar) generates a formatted `.xlsx`
workbook. The exact set of sheets depends on whether you've run Monte
Carlo:

- **Summary** — Headline KPIs, scenario name, and the retirement-age
  rollup. Frozen header rows so the title block stays visible while you
  scroll.
- **Inputs** — Every input value used for the run, grouped by section.
- **Accounts** — Per-account configuration (type, balance, growth,
  caps, ranks, volatility).
- **Year-by-year** — Full year-by-year table including per-account
  columns. Embedded portfolio-over-time chart references this sheet.
- **MC-Percentiles** *(when MC has been run)* — Per-age P5 / P25 / P50 /
  P75 / P95 ending balances in today's $, plus a headline merged cell
  summarizing success rate and depletion-age percentiles.
- **MC-Trial-details** *(opt-in)* — Per-trial depletion ages. Gated
  behind the *Include MC trial details* checkbox in the Excel export
  modal because the trial count can be large.
- **Sweep** *(when Multi-age sweep has been run)* — Per-age success%,
  p10/p50/p90 ending balance in today's $.
- **Charts** — High-resolution PNG snapshots of every chart that's
  currently on screen at export time: the deterministic projection,
  annual contributions, spending need vs shortfall, Monte Carlo fan
  (when MC has been run), and the multi-age sweep chart + heatmap
  (when the sweep has been run). The first row of the sheet is an
  italic note explaining that the images are a static point-in-time
  capture — they will *not* update if you edit values elsewhere in the
  workbook. Snapshots match the on-screen palette and zoom level.

Each sheet has a coloured tab and a frozen header row to make
navigation between sheets visually obvious. The two charts on the
*Summary* sheet are *native* Excel charts that reference the
*Year-by-year* sheet directly, so you can edit the underlying data and
the charts update. The *Charts* sheet, by contrast, holds static PNG
images — useful for screenshots and printing, but they don't refresh
when the data changes.

**Export options modal.** When you've run Monte Carlo and pick
*Export to Excel…* from the Export menu, a small modal appears so you
can choose whether to include the optional *MC-Trial-details* sheet
(large; useful for ad-hoc analysis). Without an MC result, the modal
is skipped and the file downloads directly with the four base sheets.

**Currency.** The display currency you selected in the planner is used
throughout the workbook; the planner is currency-agnostic and just
relabels the symbol — it does *not* convert values when you switch
currencies.

## Print and PDF

**Export → Print / PDF** (toolbar) opens your browser's native print
dialog after swapping the on-screen layout for a clean print-friendly
summary. Pick *Save as PDF* in the print dialog destination to get a
PDF you can email or archive.

The print layout is intentionally compact but expands as needed when
you've run additional analysis:

- **Page 1** — Title block, headline KPIs (portfolio at retirement,
  ending balance, runs-out age) including any fresh Monte Carlo p5/p95
  ranges, a structured digest of every input value used for the run
  (ages, inflation, salary, withdrawal mode, Social Security,
  accounts), and the deterministic *Portfolio over time* projection
  chart.
- **Charts page** — A dedicated page collecting the rest of the
  on-screen charts as embedded images: annual contributions, spending
  need vs shortfall, Monte Carlo fan (when MC has been run), and the
  multi-age sweep chart + heatmap (when the sweep has been run). Only
  the charts that are actually rendered on screen at print time are
  included, so the page sizes itself naturally to whatever analysis
  you've completed.
- **Year-by-year page(s)** — Condensed table covering the full
  projection. Zebra striping keeps the rows readable at print
  resolution.

The embedded chart images use the same palette and zoom level you see
on screen, so the printed output matches the live view exactly. The
dark theme is forced to light during printing so ink and contrast stay
sensible, and the in-app sidebar, toolbar, and Monte Carlo controls
are hidden. Closing the print dialog restores the normal layout.

## Data privacy

The planner is a fully local-first single-page app. There is no backend
and no analytics:

- **Inputs and library scenarios** live in your browser's local storage
  on this device only. Switching browsers or devices starts you with an
  empty library.
- **Excel and PDF exports** are generated entirely in the browser — the
  file appears in your *Downloads* folder without any server round-trip.
- **No telemetry / analytics.** The planner does not record which
  buttons you click, how often you run Monte Carlo, or how long you
  spend on the page. There is no opt-out toggle because there is
  nothing to opt out of.
- **Search engines.** The Pages deployment ships with a `noindex` meta
  tag so the live URL is excluded from search-engine indexes by default.

To move scenarios between devices, use **Download as .json** inside
the Save modal and **Upload .json…** inside the Load modal — the file
lives on your machine, you share it manually.

## Limitations

A short list of what the planner does **not** do, so you know when to
reach for a different tool or a financial advisor:

- **Income tax on withdrawals is not modeled.** Every figure is gross
  of income tax. See *Tax modeling* for the workaround until this lands.
- **Capital-gains tax on taxable / brokerage withdrawals is not
  modeled.** Same workaround applies.
- **Social Security taxation is not modeled** — SS income is treated as
  gross. Lower the *Monthly benefit* by your expected effective tax
  rate to compensate.
- **Required Minimum Distributions (RMDs) are not modeled.** The
  withdrawal waterfall pulls only what's needed to cover your spend
  target; it does not force RMDs out of pre-tax accounts at age 73 / 75.
- **Healthcare and ACA subsidy modeling.** None.
- **Single inflation rate.** Inflation is a single user input applied to
  spending, contribution caps, and SS COLA alike. The engine does not
  model healthcare inflation separately.
- **Inflation is deterministic.** Even in Monte Carlo, only investment
  returns are randomized — inflation stays at the input value. This
  isolates market-return uncertainty but understates real-world risk.
- **No state-by-state property / income tax variation.**
- **Single-currency display, currency-agnostic engine.** Switching the
  *Display currency* relabels the symbol; it does not convert balances.
- **Account contribution limits are not policed.** The IRS limit isn't
  hard-coded — set the *Annual cap* on each account yourself if you
  want it enforced. See the
  [IRS retirement plan limits page](https://www.irs.gov/retirement-plans/plan-participant-employee/retirement-topics-contributions).
- **Validation is structural only** (e.g. retirement age vs current
  age) — not legal/regulatory.
- **Single-year shocks aren't first-class.** The workaround is to
  duplicate an account, set its contribution to the shock amount,
  enable for one year, then disable.
- **Bootstrap dataset is U.S.-only (1928–2023).** International
  diversification, emerging markets, and forward-looking views are
  partially addressable via *Drift-corrected bootstrap* — see that
  section.

This list is the deliberate one. If something on it would block you
from using the planner for a real decision, treat the result as
indicative and verify with a licensed financial advisor.

