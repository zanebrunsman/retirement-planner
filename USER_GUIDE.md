# Retirement Planner — User Guide

This planner is a deterministic, year-by-year projection of your retirement portfolio.
Everything runs locally in your browser — your inputs are saved only to this device's
local storage. There is no backend.

Use the inputs panel on the left to configure your scenario; results update on every
change. The right side shows charts, a year-by-year projection table, and an Excel export
button. Below is a reference for every input, control, chart, and table column.

> **AI-assisted development.** This planner was built with substantial help from
> AI coding assistants (writing, reviewing, and refactoring source code, tests,
> and documentation). It is a personal project under active development and is
> **not professional financial advice**. Verify any number that matters against
> an independent source — a licensed financial advisor, the relevant IRS or plan
> rules, or your own spreadsheet — before relying on it for a real-world
> decision. The author and the AI tools used to build this make no warranty
> that the formulas, defaults, or outputs are correct or current.

> **Heads-up: income taxes on withdrawals are NOT yet modeled.** All withdrawal,
> spend, and balance numbers are shown *gross of income tax* — the only tax-like
> deduction the engine currently applies is the early-withdrawal penalty. See the
> *Taxes (not yet modeled)* section below for a full explanation and how to work
> around it for now.

## Top-level toolbar

The toolbar is **sticky** — it stays pinned to the top of the screen while you
scroll the projection table or charts, so the primary actions are always one
click away.

**Hide / Show inputs** — Collapses the left sidebar to give the projection table more room.

**Undo / Redo** — Two small icon buttons immediately right of *Hide inputs*. They
undo or redo the last input edit, mirror the `Cmd/Ctrl+Z` and `Shift+Cmd/Ctrl+Z`
(or `Cmd/Ctrl+Y`) keyboard shortcuts, and are greyed out when there's nothing to
undo or redo. Hovering shows how many steps are currently available in each
direction.

**Save scenario (.json)** — Downloads a JSON file containing every input value (ages,
inflation, accounts, ranks, etc.). Useful for backup or sharing. Saved scenarios from
older versions of the app are auto-migrated when loaded. After clicking Save, a
small *Saved Ns ago* indicator appears next to the button so you can see at a
glance when you last exported your work.

**Load scenario** — Imports a previously-saved JSON file. Replaces all current inputs.

**Save to library / Saved scenarios** — In-browser scenario library. *Save to
library* lets you name the current scenario and store it locally; *Saved
scenarios* opens the manage view where you can load, delete, or wipe entries.
The library is capped at **20 scenarios** (each ~2–4 KB) so it cannot balloon
your browser storage. Saving the same name twice overwrites in place. Saved
scenarios live in this browser only — they don't sync across devices, and
opening the planner in a different browser or on a different device starts you
with an empty library. To move scenarios between devices, use *Save scenario
(.json)* and *Load scenario* instead. The library is preserved by the regular
*Reset to defaults* flow; only *Clear all data* (in the Reset menu) wipes it.

**Compare scenarios** — Opens a comparison modal that lets you stack the
current scenario against up to two other saved scenarios. Each non-current
slot has a *Load from library…* dropdown (when the library has entries) plus
the original *Choose file…* button for `.json` imports. The modal shows a KPI
table (portfolio at retirement, runs-out age, contributing years, first-year
withdrawal, ending balance, total penalty paid), a small **Monte Carlo
summary** row (success rate + median depletion age, computed with 1,000
trials per scenario), and three overlaid charts (portfolio over time, annual
contributions, out-of-pocket spending). The current scenario is always pinned
to the first slot.

**Print / PDF** — Opens your browser's native print dialog after stripping the
app chrome (sidebar, toolbar, MC controls). Pick *Save as PDF* in the print
dialog destination to get a clean PDF of the projection table, KPI tiles, and
portfolio chart. The dark theme is forced to light during printing so ink and
contrast stay sensible. Closing the print dialog restores the normal layout.

**Export to Excel** — Generates a .xlsx workbook with three sheets:
- *Inputs* — every input value used for the run
- *Accounts* — per-account configuration
- *Projection* — full year-by-year table including per-account columns

**Deep settings** — Opens a centered modal with advanced settings (Monte Carlo
defaults, manual waterfall ranks, currency, chart palette). Click the backdrop
or press Esc to close.

**Theme toggle (sun / moon icon)** — Sits to the right of *Export to Excel*.
A single click flips between light and dark mode. The icon previews where
clicking will take you (moon while in light, sun while in dark). On a fresh
first visit the planner respects your operating system's color-scheme
preference, so dark-mode OS users start in dark; thereafter the choice
persists with the rest of your scenario.

**Reset** — Opens a small menu with three choices:
- *Reset to defaults* — clears your **current scenario** and reverts every
  field to its default value. Saved-library entries are kept. Asks for
  confirmation first.
- *Run setup wizard* — reopens the first-time setup wizard so you can re-enter
  your scenario step by step instead of editing fields one at a time. Useful
  after a major life change (new job, marriage, retirement).
- *Clear all data* — nukes the current scenario **and** every entry in your
  saved-scenario library. The escape hatch when you want a totally clean
  slate. Cannot be undone.

**User guide** — Opens this drawer. The drawer has a search box and a
jump-to-section dropdown so you can find topics quickly without scrolling.
Matches in the body are highlighted as you type, with a small `n / total`
counter and ‹ › chevrons next to the search field for stepping through hits
(Enter advances, Shift+Enter goes back, Esc closes the drawer). The
jump-to-section dropdown is also a *scrollspy* — as you scroll the guide, it
updates automatically to reflect whichever section is currently in view, so
you can always tell where you are in the document. Picking the same
section twice in a row — e.g. after scrolling away — jumps you back to
it; the dropdown no longer ignores re-selections.

## Editing inputs — undo, redo, and validation

**Undo / redo** — Every change you make to an input field is recorded. You can
trigger undo or redo three ways:
- The two icon buttons in the sticky toolbar (right of *Hide inputs*)
- The keyboard shortcuts `Cmd/Ctrl+Z` / `Shift+Cmd/Ctrl+Z` / `Cmd/Ctrl+Y`
- Both stay in sync — the buttons grey out when their stack is empty and
  show the available step count on hover.

History goes back up to 50 edits and is cleared automatically when you reset.
Undo or redo also clears any Monte Carlo fan chart you'd already run, since the
result belonged to a different set of inputs.

Age fields **don't** have native browser-input undo, so the keyboard shortcut
is the only way to revert an unintentional edit. Field-level undo (the kind
your browser provides while typing inside a single text input) still works the
same as before — the planner-level undo only kicks in once focus leaves the
field.

**Validation banners** — A yellow banner appears at the top of a section when
the inputs are structurally inconsistent (e.g. retirement age earlier than
current age, Social Security claim before current age, Coast age outside the
working window). The projection still runs so you can see the effect, but the
banner flags the issue until you fix it. Validation is structural only — no
tax-law or contribution-limit checks. For 401(k) / IRA / HSA contribution
caps, see the [IRS retirement plan limits page](https://www.irs.gov/retirement-plans/plan-participant-employee/retirement-topics-contributions).

**Drag-and-drop account reordering** — Each account card has a drag handle
(`⋮⋮`) on the left of its header. Click and drag to reorder accounts. The new
order is reflected immediately in the projection and Excel export. Listed
order also acts as the tiebreaker when two accounts share the same withdrawal
rank.

**Collapsed account summaries** — Account cards collapse to a single line
showing name, balance, monthly contribution, and growth rate. Click the
header to expand and edit. New accounts you add start expanded so you can
fill them in right away.

**Help tips** — Most field labels have a `?` button next to them. Click for a
popover explanation that goes deeper than the inline hint text.

**Theme & chart palette** — The light/dark theme is toggled from the sun /
moon icon in the sticky toolbar (a fresh first visit follows your operating
system's color-scheme preference). The chart palette (Okabe–Ito, Tableau-10,
Calm — all designed for color-vision accessibility) lives in *Deep settings*.

## Setup wizard

First-time visitors are greeted by a 10-step setup wizard that walks through
every core input — ages, inflation, salary, employer match, accounts,
withdrawal plan, Social Security, and Coast FIRE — with plain-language
explanations and pre-filled defaults. Each step starts with reasonable values
so you can move quickly through the ones that don't apply.

You can re-run the wizard at any time from the **Reset** button in the toolbar.
The wizard never opens automatically once you've saved a scenario — only on a
truly fresh first visit. To skip the wizard and start with the planner's
default scenario, click *Skip / use defaults* in the wizard header.

Finishing the wizard writes your assembled scenario to this browser's local
storage. Your scenario is private to this device — nothing is uploaded
anywhere.

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

> Coast FIRE is now toggled in **Deep settings → Coast FIRE**. When enabled,
> a *Coast start age* input slides into this section between *Current age* and
> *Retirement age*.

## Coast FIRE

**Enable Coast FIRE** lives in **Deep settings → Coast FIRE**. When enabled, a
*Coast start age* input appears in the Ages section between *Current age* and
*Retirement age*.

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
employer to a single routed account. The match pool each year is
`salary × match%`, capped at the routed account's own annual contribution
(you can't match more than you put in). Match contributions are *not* counted
against the routed account's annual cap — they apply on top.

The destination account is configured in **Deep settings → Employer match
routing**. The dropdown lists every enabled account and defaults to *Auto*,
which resolves in this order:

1. The account you explicitly pick in the dropdown (if any).
2. The first enabled Traditional 401(k).
3. The first enabled account of any type.
4. If no accounts are enabled, the match contributes nothing.

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
pre-penalty / Retire post-penalty).

**Chart-units toggle** — Above the chart, a segmented `[Nominal $ | Today's $]`
control restates *every* series on the chart in the chosen units — each
per-account line, the total, and (when Monte Carlo has been run) every
percentile band and the median trial. Toggling it does not change any other
chart or the projection table.

**Y-axis zoom** — Three small buttons in the chart's top-right control the
Y-axis ceiling: `+` zooms in (lowers the ceiling, useful for inspecting
the early-accumulation years when later balances dwarf them), `−` zooms
out, and `⟲` resets to the data-driven default. Each step changes the
ceiling by a factor of 1.5. The default ceiling is *Total × 1.1* — the
deterministic Total line plus 10% headroom — regardless of whether the
Monte Carlo fan is visible; when the fan is on, any portion that pokes
above the ceiling is simply clipped. The lower bound stays at $0. Any
input change or a flip of the *Nominal $ / Today's $* toggle
automatically resets the zoom step to its default, so you don't end up
staring at a clipped chart of a brand-new scenario.

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

### Monte Carlo fan chart
When you run a Monte Carlo simulation (see the Monte Carlo section below), the
*Portfolio over time* chart gains translucent percentile bands showing where your
balance is likely to land across thousands of randomized trials. The wider light
band marks the 5th–95th percentile range; the darker inner band marks the 25th–75th.
A dashed line plots the median trial. The deterministic projection lines stay
on top so you can compare the single-line estimate to the distribution. Bands
automatically disappear when you change any input that would affect the
simulation (results would be stale relative to the new scenario); re-run
Monte Carlo to refresh them. *Multi-age sweep* runs are special: the bands
stay fresh as you slide the *Retirement age* control anywhere within the
sweep range — see the *Multi-age sweep* section below.

## Monte Carlo simulation

The deterministic projection assumes every account earns its growth rate exactly,
every year, forever. Real markets don't work that way — a bad sequence of returns
early in retirement can deplete a portfolio that would have survived comfortably
with the same long-term *average* return spread differently across years. This is
called **sequence-of-returns risk**, and it's the single biggest blind spot of
any single-line retirement projection.

Monte Carlo simulation tackles this by running your scenario thousands of times
with randomized returns each year, then summarizing the distribution of
outcomes. It's optional and opt-in — you have to click *Run Monte Carlo* in the
panel below the projection charts. Until then, the planner shows only the
deterministic line, just as before.

### How to read it
- **Success rate** — the percentage of trials in which your portfolio never ran
  out of money during retirement. 90%+ is generally considered a solid plan;
  75–89% is workable but worth pressure-testing; below 75% suggests you should
  retire later, save more, or spend less.
- **Fan chart bands** on the *Portfolio over time* chart — the wide light band
  is the 5th–95th percentile range (90% of trials land inside it), the darker
  inner band is the 25th–75th (the middle half of trials), and the dashed line
  is the median trial. The deterministic projection sits on top for direct
  comparison.
- **Ending-balance percentile table** — what your portfolio is worth in
  today's dollars at end-of-plan, sorted by percentile. Compare the 10th
  (bad outcome) and 90th (good outcome) to gauge the spread.
- **Depletion-age histogram** — for trials that did run out of money, when did
  it happen? Failures clustered at the tail end (e.g. age 90+) are far less
  scary than failures starting in your 70s.

### Volatility inputs
The Monte Carlo panel (next to the *Run* button) has two global inputs:

- **Pre-retirement volatility** — sigma during accumulate / coast.
- **Post-retirement volatility** — sigma after retirement age.

These drive the random sampling for *every* account by default. Sensible defaults:

- **Stock-heavy** (Roth IRA, 401(k), Brokerage): 15% pre / 10% post
- **Bond / mixed**: 5% pre / 4% post
- **Cash / Savings**: 1% pre / 1% post

#### Per-account override
In **Deep settings → Monte Carlo**, toggle **Use per-account volatility** to
switch from the two global inputs above to the *Pre-retirement volatility* and
*Post-retirement volatility* fields on each account card. When that toggle is
on, the global inputs in the MC panel grey out so it's clear they're inactive.
Setting an account's volatility to 0 makes it behave deterministically.

### Return model — Lognormal vs Historical bootstrap

**Deep settings → Monte Carlo → Return model** picks how the simulator
generates each year's random return. Two choices:

- **Lognormal (i.i.d., default).** Each year independently draws from a
  bell curve centered on the account's growth rate, scaled by the
  configured volatility. Fast, simple, and the textbook default — but it
  assumes years have no memory of one another, so back-to-back bad years
  show up only by coincidence. This understates *sequence-of-returns risk*
  for early retirees.

- **Historical bootstrap (1928–2023 real returns).** Instead of generating
  returns from a formula, the simulator resamples blocks of consecutive
  years from the actual market record (Robert Shiller's S&P 500 + 10-year
  Treasury dataset, deflated by CPI so all returns are *real*). For each
  trial, a random start year is picked and the next *N* years come straight
  from history; when the block ends, a new random start is picked. This
  preserves volatility clustering — a 1929 draw also drags 1930–31 along
  with it — and the genuine fat tails of the historical record. Block
  length defaults to 5 years (the academic standard) and is configurable
  in the same panel.

  Account classification: each account is treated as **stock-like**
  (assumed return ≥ 6%) or **bond-like** (< 6%) based on its `growthPre` /
  `growthPost`. Stock-like accounts pull the year's S&P 500 real return;
  bond-like accounts pull the 10-year Treasury real return. This sidesteps
  asking you to specify a per-account stock/bond split.

**When to use which.** Lognormal is the right starting point — it's faster
and the bell-curve assumption is fine when you're just sliding inputs
around to get a feel for trade-offs. Switch to bootstrap when you're
pressure-testing a real plan: bootstrap success rates are typically
5–15 percentage points lower than lognormal for aggressive early-retirement
scenarios, and that gap is exactly the sequence-of-returns risk you want
to see before committing. Bootstrap also exposes specific historical
decades — 1929, 1966, 2000 — that would have killed otherwise-plausible
plans, so the depletion-age histogram becomes a much sharper diagnostic.

### Trial count
The Trials selector in the Monte Carlo panel offers 1,000 / 2,000 /
5,000 / 10,000 / 25,000 / 50,000 trials. Default is 10,000 for a
single-age run, and drops to 2,000 when the *Multi-age sweep*
checkbox is on (the sweep does up to 41 per-age runs in parallel,
so fewer trials per age keeps the total cost reasonable). More
trials = smoother percentile bands and more stable success-rate
estimates, but longer compute time. 10,000 is plenty for typical
planning; 25,000 or 50,000 is useful when stress-testing tail
outcomes (5th/95th percentile become much more stable at higher
counts). The selector remains editable in either mode — override the
default if you want.

While a run is in progress the *Run* button shows a progress bar that fills
from left to right with a percentage label, so you can see the simulation is
working even on slower devices.

### Show MC fan toggle
Once you've run Monte Carlo, the *Portfolio over time* chart shows a
**Show MC fan** checkbox. Untick it to hide the percentile fan while keeping
the Monte Carlo *median* (P50) line visible — useful when account balance
lines are dwarfed by the fan envelope.

### Stale results
Monte Carlo runs in a Web Worker and the results are cached against a hash of
your inputs. The moment you edit any field, the cached result is flagged
*Stale* — the fan-chart bands are hidden, and the success pill plus the
p5/p95 ranges on the KPI tiles disappear entirely until you re-run.
Toggling theme or chart palette does *not* mark Monte Carlo stale
(those are presentation-only changes and don't affect the underlying
simulation).

When *Multi-age sweep* is on, a sweep result keeps the fan, the success
pill, and the KPI ranges fresh across the whole sweep range — you can
nudge the *Retirement age* slider anywhere within ±20 years of the age
you swept and the panel will silently swap to the cached result for the
new age, no re-run required. Editing any non-retirement-age input
(salary, spend, growth rates, etc.) discards the sweep cache the same
way it discards a single-age result.

### Monte Carlo on the KPI tiles
Once a Monte Carlo run is fresh, three of the headline KPI tiles surface
additional information drawn from the trial distribution:

- **Portfolio at retirement** and **Plan ending balance** — each tile
  gains a two-line `↑ p95` / `↓ p5` range below the deterministic
  figure, showing the 95th- and 5th-percentile outcomes from Monte
  Carlo (matching units: nominal $ for *Portfolio at retirement*,
  today's $ for *Plan ending balance*). The range grays out when
  results are stale.
- **Portfolio runs out** — below the deterministic *Runs out at age _*
  pill, a second pill summarizes the Monte Carlo outcome. Severity is
  encoded by the pill *colour*; the line order and phrasing are the
  same in every failing tier so the numbers don't reorder when you
  nudge the retirement-age slider.
  - **Green** when at least 95% of trials make it to plan-end
    ("95% chance: lasts to age _"). No subtitle.
  - **Yellow** when 50–94% of trials succeed.
  - **Red** when fewer than half succeed.

  Yellow and red pills both show the same two lines:
  - **"50% of trials run out before age _"** as the headline — the
    typical failure age, which you can read as "half the simulated
    paths run out by this point".
  - **"5% of trials run out before age _"** as the subtitle — the
    bad-tail age, the threshold below which only 1-in-20 of the
    simulated paths run out.

  Note that with a high success rate it is normal for the 5% age
  to land *later* than the 50% age — the few failing trials cluster
  near plan-end rather than spreading across early ages. When at
  least 95% of trials succeed there is no widely shared failure age
  to report, so the green pill collapses to the single "lasts to
  age _" line.

### Multi-age sweep (±20 years)
Turning on the **Multi-age sweep** checkbox in the MC panel runs an
entire range of retirement ages — *retirement age ± 20 years*, in 1-year
steps, clipped to your *current age* and *end age*. The sweep runs ages
in parallel using a worker pool sized to your CPU (up to 8 workers),
so a 41-age sweep typically finishes in well under a minute on a modern
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

The sweep cache is held in memory only — it isn't persisted across
browser sessions and isn't included in saved scenario files. Toggle the
checkbox off to discard it.

### What the simulation models
Returns are sampled per account per year from a lognormal distribution centered
on that account's growth rate (pre-retirement vs post-retirement) with the
specified sigma. Contributions, salary growth, employer match, withdrawal
waterfall, Social Security, and inflation all run through the same engine as
the deterministic projection — only the per-year per-account return is
randomized. Inflation itself is held at the input value (not stochastic) so
you're isolating market-return uncertainty.

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
