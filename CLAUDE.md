# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file bilingual (Thai/English) landing + ticket-purchase flow for the
"Souped Up Grand Prix VIP Village" event. The entire app — markup, CSS, and
JavaScript — lives in `index.html`. There is **no build system, no package
manager, and no dependencies**. `asset/` holds the images referenced by the
page; `.nojekyll` keeps GitHub Pages from mangling it.

The purchase/payment flow is intentionally kept identical to the sibling
project [nidss/su](https://github.com/nidss/su); when changing checkout or
payment UI, preserve that parity.

## Run & deploy

- **Run locally:** open `index.html` directly, or (recommended, so
  `localStorage` works) `python3 -m http.server` then visit the served file.
- **No lint/test/build commands exist.** After editing the inline `<script>`,
  sanity-check JS syntax by extracting the script block and running it through
  `node` (e.g. `new Function(scriptSource)`), since a broken script fails
  silently in a static page.
- **Deploy:** GitHub Pages, `main` branch / root. Live at
  https://nidss.github.io/suvip/ — pushing to `main` publishes.

## Architecture (all in `index.html`)

The page is a set of stacked `.card` sections inside `#regView`, plus two
sibling views `#payView` and `#doneView`. `showView('reg'|'pay'|'done')`
toggles their `hidden` attribute — it is the only "router".

- **`state`** (single global object) is the source of truth: `lang`, `tab`
  (`early`|`normal`), `tickets[]`, `payMethod`, `tax`, `result`, `dashPage`.
  Each ticket is created by `newTicket()`. Mutating `state` then calling the
  matching `render*()` function is the update pattern — there is no framework
  and no reactive binding.
- **Rendering** functions rebuild DOM from `state`: `renderContent()` (pricing
  cards), `renderTickets()` (one collapsible card per admission ticket, so a
  buyer can purchase several at once), `renderPay()` / `renderPayDetail()`,
  `renderDone()`. Event handlers are (re)attached inside these functions after
  each rebuild via `data-*` attributes (e.g. `data-ticket-day`,
  `data-ticket-plate`, `data-chan`).
- **Pricing/availability** is data-driven from top-of-script constants:
  `PRICES`, `PKG_DAYS`, `DAYS`, `DAY_CAP`, `DAY_BOOKED`, `NEAR_LEFT`. Seat
  availability (`dayStatus`, `availableDayIds`, `pkgAvailable`) is mock data —
  e.g. a package that spans a full day becomes unselectable. Change event days,
  prices, or capacity here, not in markup.
- **Submit → validation** lives in the `#regForm` submit handler: it validates
  the contact fields, then each ticket (package chosen, correct number of days,
  parking plate if a car), then consent. Failures mark the field `.field.invalid`
  (red border + `.err-msg`) and raise a `toast()`. On success it writes one
  record per ticket to `localStorage` via `addReg()` and moves to `pay`.
- **Persistence:** `localStorage` key `suvip_regs` (an array), through
  `loadRegs` / `saveRegs` / `addReg`. This is the mock "purchase history".

## i18n

Every user-facing string is a key in the `L` dictionary (`L.th` / `L.en`);
`t(key)` resolves against `state.lang`. `applyLang()` walks the DOM and fills:
`data-i18n` → `textContent`, `data-i18n-html` → `innerHTML`, `data-i18n-ph` →
`placeholder`. **When adding any visible text, add both `th` and `en` entries
and reference them via these attributes** rather than hardcoding — and note
labels that contain child elements (e.g. a required `*`) must wrap the
translatable text in an inner `<span data-i18n=...>`, because `data-i18n`
overwrites `textContent` of its own node.

## Host (SU) integration via postMessage

The page is designed to run standalone **or** embedded in an iframe by the
`nidss/su` host. It talks to `window.parent` only when embedded
(`window.parent !== window`):

- **Inbound:** `su:member` → `applyHostMember()` prefills the contact fields
  (`name`/`surname`/`phone`/`email` inputs) from the shared member profile and
  reveals the member button.
- **Outbound:** `suvip:complete` (on payment finish), `suvip:profile`,
  `suvip:back`, `suvip:scroll-top`, and `suvip:resize` (a `ResizeObserver`
  reports height so the host can size the iframe).

Keep new standalone features guarded so they degrade gracefully when there is
no parent frame.

## Conventions

- Per the repo owner's preference, write code comments in **Thai or English
  only** — never Japanese characters.
- Match the existing terse, single-file style (compact handlers, `el(id)`
  helper, `esc()` for any interpolated user text).
