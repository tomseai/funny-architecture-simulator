# The Funny Architecture Simulator

A single-file browser toy for feeling why architecture is not slider math.

You move sliders for architectural forces like complexity, state, async/events,
coupling, operational maturity, blast radius, deployment scale, and coordination
novelty. The simulator does not recommend an architecture. It shows how the
field changes when one force rerates the others.

The core rule:

```text
A bad simulator outputs an answer.
This one outputs a feeling.
```

## Run

Open `index.html` in a browser. There is no build step, no package install, and
no runtime dependency.

If you prefer serving it locally:

```powershell
python -m http.server 8000
```

Then open `http://localhost:8000/index.html`.

## What To Try

- Drag `Async / event-driven` upward and watch observability, debugging pain,
  failure surface, and the silent bill change.
- Click `Throw a wildcard` and see how one external event moves several forces.
- Use `Black Friday`, then `The senior who knew it all leaves`, to see the
  failure surface snap onto a plateau.
- Toggle the agent-trust tint to see where machine help is safer or riskier.
- Open `Show the toy model` to inspect the formulas instead of trusting the toy.
- Copy a shareable URL to preserve the current slider state.

## Features

- Eleven sliders: eight force sliders plus three Grady axes.
- A 2D decision field with keep-together, draw-boundary, and not-ripe regions.
- Marginal leverage bars under each slider.
- Consequence meters for observability need/capability/gap, debugging pain, and
  cognitive load.
- A "why did this move?" panel that shows causal deltas after changes.
- Failure-surface threshold proximity checks.
- Silent bill receipt for async/event-driven choices.
- Agent-trust heat map and optional tint on the main field.
- Wildcard deck with random draw and explicit deck view.
- Presets and named-hash shareable state.
- Inspectable toy model and per-slider info popovers.

## Design Guardrails

This is a lens, not a calculator.

It must not:

- print "use microservices" or any other hard recommendation,
- hide the model,
- pretend the numbers are real architecture,
- give a confident verdict for every configuration.

The honest output is often: decide reversibly, keep the option open, and wait
for real pressure.

## Files

- `index.html` - the complete simulator.
- `funny-architecture-simulator.md` - the full design/spec narrative.
- `LICENSE` - MIT license.

## License

MIT.
