# Wärmewende Tycoon — Game Review & Improvement Roadmap

*Status: July 2026 · reviewed against `waermewende-tycoon-v4.html` (commit `bc6aa82`)*

## 1. Where the game stands

**What already works well**

- The theme is a goldmine and the writing carries it: Detlef, Frau Krauses Streuselkuchen, Vattenfail, „Insolvenz in Eigenverwaltung" — the flavor text is the game's strongest asset. Every new mechanic should feed it more material.
- The two-tier acquisition (cheap Kesseltausch to lock a customer vs. expensive Wärmepumpen-Contracting) is a genuinely good core decision.
- The iso city looks great, is readable at a glance (smoke = free, flame = kessel, sprout = done), and the seeded-map sharing is a nice touch.
- Solid technical base: single file, no build, mobile + desktop, deterministic city gen.

**Old backlog check:** the three items from the PR #1 handover backlog (touch-tap conflict, render perf, `?seed=` URLs) are shipped, as is the PR #2 desktop UX pass. There are no open issues. Everything below is new.

## 2. Design problems to solve

These are the root causes behind "fun plateaus after the first 20 minutes":

1. **The game is decided in month 6.** Survive the early cash crunch and the win is inevitable — rate income snowballs, nothing scales against you. There is no mid- or late-game pressure, only waiting.
2. **Kessel-first is close to a dominant strategy.** Kesseltausch is instant (no installer time!), locks the customer against competitors, pays back in ~13 months, and the later upgrade is *guaranteed* (no dice roll, no pitch cost, −15% capex). Direct WP contracting risks losing the pitch AND the building. The interesting choice collapses.
3. **The endgame is a poach-grind.** 100% requires taking *every* competitor building at a fee premium and a −22% chance penalty — the last hour is repetitive clicking with no new decisions.
4. **Events are slot-machine flavor, not gameplay.** All 12 events resolve themselves; the player never chooses anything.
5. **Buildings are interchangeable.** Only `units` and `floors` differ. Type (Altbau vs. Platte vs. Villa) is purely cosmetic.
6. **No persistence.** A full run takes 30–60 min of real time and a reload loses everything. This blocks every "bigger" ambition.

## 3. Roadmap

### Tier 1 — Quick wins (small diffs, big return)

| # | Feature | Why / How |
|---|---------|-----------|
| 1.1 | **Autosave (localStorage)** | Serialize `S` + seed each tick; „Weiterspielen"-button on the intro. Prerequisite for everything "bigger". Store the map seed so the city regenerates identically, then overlay saved building states. |
| 1.2 | **Difficulty select** on intro: 🌱 Azubi / 🔧 Meister / 🔥 Sanierungsfall | Scales `startMoney` (600k/500k/380k), `compProb`, event weighting, and subsidy volatility. "Challenging but not too hard" becomes the player's own dial. |
| 1.3 | **Decision events** (~6 to start) | Reuse the event pipeline but open a sheet with 2 choices. E.g. „🏛️ Der Bürgermeister will eine Kooperation: 20.000 € spenden für Ruf +10, oder ablehnen (Ruf −3)?" · „👴 Wärme & Söhne bietet dir sein Kundenportfolio an: 180.000 € für 3 Gebäude?" · „📺 TV-Team im Heizkeller: live zusagen (50/50 Ruf ±10) oder absagen?" Instant agency, maximum flavor-per-line-of-code. |
| 1.4 | **Score & grade on the end screen + local highscore** | Score from speed, cash, rep, CO₂; grade A–F with a snarky title („Kesselflüsterer" … „Praktikant der Wärmewende"). Highscore table per difficulty in localStorage. Gives replay a reason. |
| 1.5 | **Kessel rebalance** | Kesseltausch now occupies installers too (e.g. 3 WE/Monteur-equivalent, fast but not free) **or** gets a capacity cap („dein Kessellager: 2 Tauschaktionen parallel"). Upgrade keeps guaranteed success but adds a small pitch („Bestandskundenpflege"). Keeps the strategy viable, kills the auto-pilot. |
| 1.6 | **Repayable loans** | Add „Kredit tilgen" (pay 250k+10% to remove one). Fixes the strictly-toxic-money design and adds a real finance decision. |
| 1.7 | **Cap `S.news`** at 60 entries | Unbounded array growth over a long session (minor, one line). |

### Tier 2 — Core gameplay depth (the "more various" middle)

| # | Feature | Design sketch |
|---|---------|---------------|
| 2.1 | **Building traits by type** | *Altbau* 🏚️: must buy „Sanierung" first (or WP chance −25% & rate −20%). *Villa* 🏛️: Denkmalschutz — approval takes 2 extra months, but prestige: +2 Ruf on completion. *Platte* 🏨: bulk bonus, −10% capex. *Reihenhäuser* 🏘️: WEG chaos, chance −10% but cheap. Suddenly the map is a puzzle, not a checklist. |
| 2.2 | **Klimaziele (soft deadlines)** | City targets: 30% by 2032, 60% by 2038, 100% by 2045. Hit → subsidy bonus + Ruf; miss → subsidy cut, competitor PR win. Creates pacing pressure without a hard fail. Show next target under the thermometer. |
| 2.3 | **Wartung & Störungen** | Active WPs occasionally break (❄️ toast on the building). An installer is blocked for the month fixing it — or ignore it: rate pauses, Ruf −2. Creates the classic tycoon tension *grow vs. maintain* and gives late-game installers a job. |
| 2.4 | **Competitors fight back** | After month 24, competitors may court your *Kessel* customers (WP customers stay safe). You get a warning toast + 2 months to upgrade them or lose them. Turns the kessel base from a parking lot into something you defend — and fixes the "nothing threatens me" mid-game. |
| 2.5 | **Contract expiry for competitors** | Competitor contracts run out after 24–36 months → building returns to „frei" (no poach fee). De-grinds the endgame; poaching stays as the *impatient* option. |
| 2.6 | **Research tree instead of one R&D button** | 4–5 techs, one active research at a time, takes months: „WärmeWumme 3000" (+12% rate) · „Propan-Kompakt" (Altbau without Sanierung) · „Flüsterkondensator" (Villa penalty removed) · „Monteur-Akademie" (+2 WE/Monteur) · „Smart-Grid-Bonus" (+8% rate at energy >1.2). |
| 2.7 | **Statistik-Tab** | Line chart (money, decarb %, customers) over months + totals. Cheap SVG, huge "tycoon feel". |

### Tier 3 — Big bets (the "bigger")

| # | Feature | Design sketch |
|---|---------|---------------|
| 3.1 | **Fernwärme layer** | Build a Heizzentrale on a free lot (~300k), lay Leitungen along roads (per-tile cost), connected buildings convert at −40% capex and +rate. A whole infrastructure-puzzle layer on the existing grid — the map's roads finally *mean* something. |
| 3.2 | **Districts** | Altstadt (Denkmalschutz cluster), Neubaugebiet (easy, low rates), Industrie (1–2 special buildings with Abwärme: adjacent conversions cheaper), Plattenbau-Viertel (JV magnets). Gives the bigger map texture instead of more-of-the-same. |
| 3.3 | **Bigger map + special buildings** | 5×6 or 6×6 blocks; landmarks with unique deals & mechanics: Freibad 🏊 (huge capex, seasonal rate), Brauerei 🍺 (Abwärme source), Rathaus 🏛️ (Ruf-Turbo, tender-only), Krankenhaus 🏥 (redundancy requirement: needs 2. Wärmepumpe). Needs zoom-to-minimap or district jump buttons. |
| 3.4 | **Kampagne: nächste Stadt** | The „Nochmal! Nächste Stadt 🌍" button becomes real: 3 cities (Kesselhausen → Ölfurt → Gasbach-Oberdorf), each bigger with a twist (mountain town: no Fernwärme; big city: aggressive competitors). Carry over one perk of choice. Uses seeds + autosave from Tier 1. |
| 3.5 | **Endlos-/Sandbox-Modus** | After 100%: keep playing for score — energy market swings, Wartung economy, competitors return. Cheap once 2.3/2.4 exist. |

### Explicitly not recommended

- **Multiplayer / backend anything** — the single-file, GitHub-Pages-hosted architecture is a feature. Keep it.
- **Real-money-style idle mechanics** (prestige resets, offline earnings) — wrong genre; this is a scenario game with an ending.
- **Framework rewrite** — vanilla JS is fine at this scale. If the file gets unwieldy, split into `<script src>` modules, still no build step.

## 4. Balance notes (current numbers)

- WP payback ≈ 13 months (1.820 €/WE eff. capex ÷ 140 €/WE rate), Kessel payback ≈ 13.3 months but risk-free and instant → see 1.5.
- `chanceOf` floor of 12% with poach −22% makes late-game poaching feel like a slot machine; consider floor 20% for poaching once rep > 70 („dein Ruf eilt dir voraus").
- Salaries never scale: 6+ installers at flat 5.000 € trivialize late game. Suggest +5% per hire beyond the 4th (Fachkräftemangel) — pairs with 2.3 giving them work.
- `eventProb` 0.25/month is good; with decision events, split pools ~60% auto / 40% decision.
- Suggested target: a "Meister" run should be winnable in 2035–2040 game time with 1–2 genuine near-death moments.

## 5. Suggested order of attack

1. **Foundation:** 1.1 autosave + 1.7 — unlocks longer games.
2. **Fun sprint:** 1.3 decision events + 1.4 score/highscore + 1.2 difficulty.
3. **Balance sprint:** 1.5 + 1.6 + 2.4 + 2.5 — fixes dominant strategy, dead mid-game, grindy endgame in one pass.
4. **Depth sprint:** 2.1 building traits + 2.2 Klimaziele + 2.6 research tree.
5. **Big map release:** 3.2 + 3.3, then 3.1 Fernwärme.
6. **Campaign** (3.4/3.5) as the capstone.
