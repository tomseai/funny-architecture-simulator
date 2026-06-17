# The Funny Architecture Simulator

_A toy you drag around until you feel why architecture is not slider-math._

A single-page, no-build browser toy. You move sliders for the forces that actually drive architecture — complexity, state, distribution, change rate, blast radius, team maturity, coupling — and watch the whole field reconfigure. Then you throw in a random shock and watch one variable rerate everything at once.

The premise: complexity is hidden coupling and discontinuity. This lets you feel it with your hands.

---

## The point — and the trap

The essay's deepest warning is the cemetery: judgment dies the moment you mechanize it into a tool that hands you the answer. A simulator with sliders that prints **"use microservices"** is exactly that tombstone — and it would be wrong, because real architecture does not reduce to a score.

So this toy lives or dies by one line:

```
A bad simulator outputs an answer.
This one outputs a feeling —
how violently the whole field flips when you nudge one slider.
```

The goal is never to compute an architecture. It is to make the body learn what the essay argues in prose: that forces are coupled, that change is discontinuous, that a single new requirement can rerate everything, and that the honest output is often _"you can't decide this yet."_

If at any point it starts to feel like a calculator you trust, it has failed. The design fights that on purpose (see **The honesty layer**).

---

## What it must teach (the payload)

Every interaction should land one of these, viscerally:

- **Hidden coupling.** Drag one slider; watch several weights move. Forces are not independent — that is what complexity _is_.
- **Discontinuity.** Push a slider slowly; nothing, nothing, then _snap_ — the regime jumps. _The size of the diff does not bound the size of the consequence._
- **Unknown unknowns.** Throw a wildcard; the field reconfigures and the recommendation region sometimes flips. The thing that wrecks the design is the variable you did not set.
- **Restraint.** The "decide reversibly / not ripe yet" region is large and frequent. Often the honest move is _don't decide._
- **Operational reality.** The same configuration flips from _recommended_ to _scheduled incident_ when team maturity drops, with nothing else changed.
- **Where you are standing.** Your position lives in a risk × complexity × irreversibility space — and the map itself moves, not just your dot.

---

## Inputs (the sliders)

Normalized `0..1` unless noted. These are the forces from the essay, nothing invented.

| Slider          | Low end                   | High end                          |
| --------------- | ------------------------- | --------------------------------- |
| `complexity`    | trivial CRUD              | inherently hard domain            |
| `familiarity`   | never been built          | built ten thousand times          |
| `state`         | stateless                 | owns durable, authoritative truth |
| `async`         | synchronous call          | event-driven / distributed        |
| `changeRate`    | changes yearly            | changes weekly                    |
| `coupling`      | independent               | entangled with neighbors          |
| `blastRadius`   | a missed tweet            | someone dies / business ends      |
| `opMaturity`    | can't run a cron reliably | mature platform, tracing, on-call |
| `irreversibility` | trivial to undo         | data-migration / rewrite to undo  |
| `deploymentScale` | one box / few users     | many regions / traffic / instances |
| `coordinationNovelty` | known app shape      | novel coordination / unclear structure |

`blastRadius` and `opMaturity` are the two that most often surprise people, because they are about _consequence_ and _the team_, not about the code. Keep them visually prominent.

---

## The wildcard deck (the random shocks)

A button: **"Throw a wildcard."** Draw a card; it nudges _several_ sliders at once and animates the field reconfiguring. This is the "random variable that changes the weight of everything" — made into a game.

```
"A Second Team Appears"          coupling ↑↑ · ownership → cross-team
"Compliance Lands"               state ↑ · irreversibility ↑ · blastRadius ↑
"Black Friday"                   async ↑↑ · state ↑↑ · deploymentScale ↑
"The Senior Who Knew It All Leaves"   opMaturity ↓↓
"Novel Domain, No Prior Art"     familiarity ↓↓ · coordinationNovelty ↑
"It's Just an Internal Tool"     blastRadius ↓↓ · irreversibility ↓   (relief card)
```

The teaching favorite is **"The Senior Who Knew It All Leaves."** Nothing about the code changes — only `opMaturity` drops — and a design that was recommended a second ago turns into a liability. That is the operational-maturity lesson the essay states in one sentence, now felt.

Cards should be easy to add. The deck is the most fun-per-line part of the project; let it grow. Future candidates: provider outage, live schema migration, surprise compliance audit, or a data-retention rule that turns reversible work into irreversible work.

---

## Where you're standing — Grady's three axes

Booch's point: most architecture advice contradicts itself because people stand in different corners of the same space, each correct where they stand. Three axes locate you. They do not move your dot in the field — they reshape what your position _means_ and how much the toy should trust itself.

- **Risk** — does failure annoy, or does it kill? (already in the model as `blastRadius`.)
- **Familiarity** — has this been built ten thousand times, or never? (new — and the most underrated.)
- **Complexity, and which kind** — scale of _deployment_ (global traffic, many instances) versus scale of _architecture_ (novel coordination, structure no one has solved). People conflate these constantly, and the conflation sends effort to the wrong place. Crucially these are **not two ends of one axis**: you can be high on either independently — a high-traffic CRUD app is high-deployment / low-novelty; a coordination problem for ten users is the reverse. So the toy splits them into two independent sliders, `deploymentScale` and `coordinationNovelty`, rather than one misleading "complexity kind" slider that would imply a trade-off that does not exist.

`blastRadius` and `complexity` were already sliders; locating yourself fully adds three more — `familiarity`, `deploymentScale`, and `coordinationNovelty`. `familiarity` is the most underrated, because it governs how much you can lean on prior art — and how much you can trust a machine. `deploymentScale` feeds the operational bill (observability need); `coordinationNovelty` feeds structural novelty, and therefore agent trust.

### The agent-trust overlay (the feature Grady unlocks)

This is where the simulator quietly becomes a lesson about AI too. Booch's corners predict exactly where an agent is reliable and where it is a dangerous oracle:

```
high familiarity · low risk · ordinary structure   → green. Let the agent run.
low familiarity · high risk · novel structure       → red. The agent is fluent and wrong.
```

Grady's panel is the heat map: green where you are in the lobby (well-trodden, cheap to be wrong, lots of prior art), red where you are in the stacks (novel, expensive, nobody has solved this). It is a slice through the current structural novelty. The main field toggle only tints the current field by the current `agentTrust`; it does not pretend to recalculate trust across the whole decision surface. Drag `familiarity` down or `blastRadius` up and watch the green retreat. That is the _automated fluency_ warning from the essay — the model's confidence is highest exactly where it should be trusted least.

---

## The toy model (illustrative — built to be distrusted)

The model is **on display**, not hidden. A panel shows these formulas so no one mistakes the toy for truth. This is the design eating its own dog food: a mechanized tool survives the cemetery only by refusing to pretend it computes the answer.

It does not output a box name. It outputs two tensions and a decision-pressure reading.

```
boundaryCost        = cost of drawing a hard (network) boundary here
                    ≈ (async + state + 0.5) × (1.5 − opMaturity)
                      // distribution makes boundaries expensive;
                      // a mature team pays less for them.

stayingTogetherCost = friction of NOT drawing a boundary
                    ≈ changeRate × coupling × (0.5 + complexity)
                      // high-change, entangled, complex code hurts to keep together.

failureSurface      = NONLINEAR — a threshold, not a slope
                    ≈ if (async > 0.6 && state > 0.6 && opMaturity < 0.4)
                          → high plateau
                      else
                          → low, gentle
                      // this is the discontinuity. Drag async slowly and watch it SNAP.

decisionPressure    = how much this decision demands to be made now
                    ≈ (blastRadius + irreversibility) / 2
                      // low → cheap and reversible → delay and stay flexible;
                      // high → costly and hard to undo → decide deliberately.
                      // (shown in the UI as the field's fat "not ripe" band.)

structuralNovelty   = 0.45 × coordinationNovelty + 0.30 × complexity + 0.25 × (1 − familiarity)

agentTrust          = where a machine can be trusted here  (Grady's overlay)
                    ≈ familiarity × (1 − blastRadius) × (1 − structuralNovelty)
                      // high where it's well-trodden, cheap to be wrong, structurally
                      // ordinary. This is the automated-fluency heat map: the agent's
                      // fluency stays high exactly where your trust should drop.

observabilityNeed   = the observability bill you bought
                    ≈ async × (0.5 + 0.5 × state)
                      × (0.5 + 0.3 × coupling + 0.2 × deploymentScale)

observabilityGap    = observabilityNeed − opMaturity
                      // need is demand; opMaturity is capability.
                      // red when the team cannot pay the bill.

debuggingPain       ≈ asyncKnee(async) + 0.4 × coupling
                      // knee at async 0.5; clamps at 1.

cognitiveLoad       ≈ (coupling + 0.25 × coordinationNovelty)
                      × (0.4 + 0.6 × async)
                      × (0.5 + 0.5 × state)
                      // local reasoning cost per safe change.
```

**Position in the field:**

- x-axis = `stayingTogetherCost` (right → splitting starts to pay)
- y-axis = `boundaryCost` (up → splitting gets expensive)
- A dot = where you are. Shaded regions = _keep together · draw a boundary · not ripe, decide reversibly._

**The crucial move:** the region boundaries themselves shift with `opMaturity` and `blastRadius`. The same dot in the same spot means _module_ for a mature team and _scheduled incident_ for an immature one. The map moves, not just the dot. That is the whole point — and it is what a static decision-tree could never teach.

**Force-weight bars:** show active leverage, not importance: current exposure weighted by local sensitivity. They stay low when a force is almost absent, can still warn that a first nudge matters, and can drop after a cost saturates. Coupling, made literal.

---

## The consequences readout (what you feel later)

The sliders are the decision. These meters are the _bill_ — the downstream costs that are invisible at decision time and only felt months later, which is exactly why people underweight them. The toy's job is to drag the bill back to the moment of choice.

They are derived outputs, not new sliders. The sharpest are **gaps** — need minus capability — because a gap is where the incident lives.

- **Observability need / capability / gap.** Event-driven and distributed work separates cause from effect; you cannot follow a stack trace, so the _need_ for logging, tracing, and correlation spikes. The UI shows the split directly: `need 20 · cap 50 · 30 headroom` or `need 49 · cap 5 · gap +44`. `async`, `state`, `coupling`, and `deploymentScale` raise need; `opMaturity` is capability. When need outruns capability, the meter goes red. This is the truest thing the toy can say about event-driven systems: the moment you go async, you have silently signed up for an observability budget nobody put on a slider.

- **Debugging difficulty** — nonlinear. In-process synchronous code is debugged by stepping. Cross into async / distributed / event-driven and you are "debugging a conversation transcript, not a stack trace." The meter sits low, then jumps at the boundary. The same feature is trivial to debug as a module and a nightmare as a distributed event flow — drag `async` across the threshold and watch it snap.

- **Cognitive load / locality** — deliberately _not_ "code cleanliness." The worldview file is explicit: clean-as-tidy is a trap; what matters is local reasoning and change locality. So this meter measures how scattered one behavior becomes, and how much you must hold in your head to change it safely — driven by coupling, distribution, state, and `coordinationNovelty`. A "cleanliness score" would smuggle aesthetics back in and contradict the essay; this measures the thing that actually hurts.

Illustrative formulas (inspect and distrust, like the rest):

```
observabilityNeed  ≈ async × (0.5 + 0.5·state) × (0.5 + 0.3·coupling + 0.2·deploymentScale)
observabilityGap   ≈ observabilityNeed − opMaturity         // red when positive
debuggingPain      ≈ asyncKnee(async) + 0.4·coupling         // knee at async 0.5
cognitiveLoad      ≈ (coupling + 0.25·coordinationNovelty)
                       × (0.4 + 0.6·async)
                       × (0.5 + 0.5·state)      // held-in-head per safe change
```

These belong in the MVP, not a later phase — they are the payload. The demonstration that sells the whole toy: drag `async` up and three meters light at once. One force rerates the felt cost of everything.

### The causality readout

The UI also includes a small **"why did this move?"** panel after each committed slider drag, preset, or wildcard. It compares the previous model state to the current one and shows causal deltas:

```
You moved Async / event-driven +20
Observability need  +10
Observability gap   +10
Debugging pain      +48
Failure surface      +5
Cognitive load       +4
Agent trust    unchanged
```

The point is to show that different sliders move different halves of the same story. `async` buys observability need; `opMaturity` pays capability; the gap is the difference.

### Threshold proximity and silent bill

The failure-surface meter shows its own snap conditions:

```
one condition away from snap:
async>0.6 ✓ · state>0.6 ✓ · opMaturity<0.4 ✗
```

This makes the Black Friday → senior-leaves story readable: Black Friday raises async/state and gets you one condition away; the senior leaving drops operational maturity and completes the snap.

When `async` rises, a **silent bill** receipt appears: distributed tracing, correlation IDs, replay/debug story, cross-service log aggregation, and eventually on-call burden the team may not be able to staff. This is intentionally concrete: it names the costs that were hidden inside the architectural style.

---

## Outputs / visualization

1. **The field** — 2D plot, moving dot, shifting shaded regions, a fat _"not ripe — decide reversibly"_ zone.
2. **Weight bars** — active leverage of each force; watch which active nudge is exerting pressure right now.
3. **Failure-surface meter** — sits calm, then jumps on a plateau. The discontinuity organ.
4. **A sentence, never a verdict** — e.g. _"Reversible and cheap to be wrong here — so don't decide; build the reversible thing and wait for pressure."_ It describes the shape of the decision; it never names the box.
5. **Agent-trust overlay** — a toggle that paints the field green where a machine can be trusted (well-trodden, cheap to be wrong) and red in the stacks. The fluency heat map.
6. **Consequences readout** — observability need / capability / gap, debugging pain, cognitive load. The bill, shown at decision time instead of months later.
7. **Why did this move?** — causal deltas after a committed change, so the user sees which downstream costs moved and which did not.
8. **Threshold proximity** — the snap conditions displayed as checkmarks, so discontinuity is inspectable instead of magical.
9. **Silent bill** — a concrete receipt for going async/event-driven: tracing, correlation IDs, replay/debug story, log aggregation, and on-call burden.
10. **Decision pressure** — the renamed `ripeness` reading: low means cheap and reversible, high means costly and hard to undo.
11. **Saturation notes** — when a force is already maxing out the damage channel, the UI says so instead of making a shrinking leverage bar look like a bug.
12. **Per-slider info buttons** — small help affordances that explain what each slider actually does in the model, not generic definitions.

---

## The honesty layer (non-negotiable)

What keeps this on the right side of the cemetery:

- A permanent line on screen: **"This is a lens, not a calculator. The model is a toy. Inspect it; distrust it."**
- The model formulas are **visible** in the UI and the per-slider info buttons state what each force actually moves. If you can inspect the math, you cannot worship the output.
- It **never prints a hard recommendation** ("use a service"). The strongest thing it says is a region and a tension.
- The **"not ripe"** region is large by design. A toy that always resolves is a slot machine; this one frequently, honestly shrugs.

---

## What it must never do

```
- Print "use microservices" (or any box name) as an answer.
- Hide its model.
- Produce a confident verdict on every configuration.
- Pretend the number means real architecture.
```

If a future version drifts toward any of these, it has become the thing the essay warns against.

---

## Build plan

Single `index.html`. Vanilla JS + SVG (or canvas). No framework, no build step, no dependencies. Double-click to run; one file to share.

> **Status (2026-06-16):** all six phases built in [`index.html`](index.html) — eight force sliders plus three Grady axes, the field with shifting regions + fat "not ripe" zone, live leverage bars (active leverage made visible), the failure-surface snap, the three consequence meters, the wildcard deck, Grady's `familiarity` / `deploymentScale` / `coordinationNovelty` axes, the agent-trust heat map ("Grady's corner"), presets, URL-shareable state, and the inspectable model. Pedagogical readouts added on top: per-slider info buttons, the observability need / capability / gap breakdown, a "why did this move?" causality panel, failure-surface threshold proximity (`async>0.6 ✓ · state>0.6 ✓ · opMaturity<0.4 ✗`), a decision-pressure reading (the renamed `ripeness`), per-force saturation notes, and a silent-bill receipt for going async. Review fixes (P0): the wildcard now draws at **random** (the explicit deck sits behind a "Show deck" toggle); the region lines genuinely **move with `opMaturity`** via a `safetyPenalty` term, so the "map moves, not just the dot" claim now holds in code as well as text; the observability meter turns amber/red once need outruns the team (a positive gap is no longer green); and the Grady heat map recomputes structural novelty per cell. Review fixes (P1): `aria-live="polite"` on the dynamic readouts, a `textarea`/`execCommand` clipboard fallback for `file://`, a **dynamic senior-leaves note** (it only claims the failure surface "snapped" when it actually does — otherwise it says the team has less capacity to pay the bill), a higher 720px mobile breakpoint for the two SVG panels, and an **agent-trust tint toggle** on the main field (default off, "view mode" only; the Grady panel stays beside it so the primary lesson is never overridden). P2 polish done: custom click/tap **info popovers** (Escape + click-outside to close, `aria-expanded`/`aria-controls`, replacing the native `title`), a **snap animation** on the discontinuity (field pulse + failure-meter shake, gated by `prefers-reduced-motion`), **smooth dot tweening** between states, and a **named hash format** (`#c=40&st=40&…`) that still migrates old positional links. Deliberately left out: theme-preference persistence and a snap **sound** — both would need storage or an autoplay gesture the toy intentionally avoids. Phases 1–6 are complete.

1. **MVP** — the six load-bearing sliders (`async`, `state`, `coupling`, `changeRate`, `opMaturity`, `blastRadius`), the 2D field with a moving dot, weight bars, and the three consequence meters (observability need / capability / gap, debugging pain, cognitive load). The one gesture that must land: drag `async` up — the dot moves, three meters jump at once, and the sentence reads _"you just bought an observability bill,"_ not "use X." If that single drag makes someone say _"one slider didn't move one thing, it moved the whole world,"_ the toy works. Everything else — `familiarity`, Grady's axes, the agent-trust overlay, the wildcard deck — is layer two, added only after that first feeling is confirmed real.
2. **Regions + the "not ripe" zone** — shaded field, regime label that includes "decide reversibly."
3. **Wildcard deck** — the random shocks, with a little animation as the field reconfigures.
4. **Grady's axes + agent-trust overlay** — the `familiarity`, `deploymentScale`, and `coordinationNovelty` sliders, and the green/red trust heat map.
5. **Honesty layer** — visible/inspectable model, the lens-not-calculator banner, no hard verdicts.
6. **Polish** — presets (`greenfield startup`, `regulated enterprise`, `internal tool`), named-hash shareable state, custom info popovers, snap animation, smooth dot tweening, and accessibility/reduced-motion handling. Sound is deliberately left out.

---

## Why it is worth building

Most architecture teaching is prose; people nod and forget. This makes one idea unforgettable by letting you cause it: **architecture is the management of coupled, discontinuous forces, and a single new variable can rerate the whole thing.** You do not read that. You drag a slider and feel the floor move.

And by being a mechanized tool that refuses to mechanize the _answer_, it quietly proves the essay's hardest claim — that the box should name itself, and the simulator's job is only to show you the forces doing the naming.

```
Drag the forces. Watch the weight move.
Let the box name itself — the simulator won't do it for you.
```
