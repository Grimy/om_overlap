# Overlap cycles theory

This guide assumes you're already familiar with [regular cycles theory](https://www.reddit.com/r/opus_magnum/comments/7qmkv6/list_of_current_cycle_optimal_scores/).

Overlap makes it possible to use inputs every cycle, and sometimes even twice per cycle. Instead of the usual `2N+1+L`, minimum cycles for a level is given by `N-D+L`, where:

* N is the number of sets of inputs needed to make all required outputs
* D is the number of times you can double-consume limiting inputs
* L is the minimum number of cycles from spawning the Nth set of inputs to completing the level

N has the same value as in regular cycle optimization. L becomes much lower thanks to overlap, and D is entirely new. Before detailing how to compute them, we need to talk about...

## Sub-cycle ordering

Inputs, glyphs and outputs trigger twice per cycle: before and after movement. This doesn't affect normal gameplay, so it was probably done for aesthetic reasons (think of a metal moving from one projection glyph to another). For overlap solutions however, this is extremely relevant, potentially letting you double throughput.

Knowing the order in which everything happens each cycle is necessary for overlap optimization. The basic order is inputs > glyphs > outputs > movement > inputs > glyphs > outputs. Conversion glyphs (animismus, purification, dispersal, unification) only consume atoms before movement, and only produce atoms after movement. The fully detailed order is:

1) Grabs and drops.

2) Unblocked inputs spawn.

Note: Arm bases and hitboxes of conversion glyph products do not block input. Inputs spawning at the same time do not block each other. These will all cause a collision on the next round of collision detection, unless something consumes the overlapping atom before then.

3) Conversion glyphs, disposal, and projection consume; bonding, debonding, calcification, and duplication trigger. This all happens in order from bottom to top (in-game, moving a glyph puts it on top; using an external solution file editor, the last glyph listed is on top).

In this step, only atoms that were continuously unbonded since the start of the step can be consumed. This creates a delay between e.g. debonding and projection.

4) Outputs consume.

5) Window of collision detection: arms move, hitboxes for conversion glyph outputs grow from a point.
Note: fast-forward can delay collision detection by up to half a cycle.

6) Unblocked inputs spawn.

7) Conversion glyphs produce; disposal and projection consume; bonding, debonding, calcification, and duplication trigger. This all happens in order from bottom to top.

In this step, only atoms that were continuously unbonded since the start of the step can be consumed.

Note: atoms produced by conversion glyphs can't interact with glyphs below that conversion glyph, even though their hitbox formed earlier.

8) Outputs consume.

9) Increment cycle count, check for completion.

Each repetition of inputs-glyphs-outputs is known as a half-cycle or subcycle. Steps 2-4 are the B half-cycle (**B**efore movement), and steps 6-8 are the A half-cycle (**A**fter movement).

## Double-consuming

As seen above, inputs can spawn twice per cycle. However, neither movements nor conversion glyphs can clear the input in the A half-cycle.
The only options for this are output and projection (and technically disposal, but throwing away an input twice as fast still achieves nothing).
Thus, the only inputs that can be double-consumed are inputs embeddable within outputs, and elemental quicksilver (molecular quicksilver cannot
be double-consumed by projection, because debonding delays projection by a half-cycle).

The full list of useful ways to double-consume an input is:

1) output+output
2) project+project
3) output+consuming glyph
4) output+wand or project+wand

### output+output

Requires an output that can be be made from a single set of inputs without any movement nor conversion glyphs, which trivializes the level.

Used in:
* [Stabilized Water](https://files.mors.technology/Stabilized-Water-20g-3c-2a-0i-1h-1.5w-0.5r-T-O-144877.mp4)
* [Mist of Incapacitation](https://files.mors.technology/Mist-of-Incapacitation-90g-8c-5a-9i-2h-2.5w-1.5r-O-97886f.mp4)
* [Surrender Flare](https://cdn.discordapp.com/attachments/760122588143157278/1119052258341032006/Opus_Magnum_-_Surrender_Flare_20G_3_7_2023-06-16-01-55-20.gif)
* [Alcohol Separation](https://i.imgur.com/QvdicOy.mp4)

### project+project

Requires an elemental quicksilver input and projection. Always easy to set up when the requirements are met.
The main difficulty is metal parity: because two projections happen between each move,
making an iron is much easier than making two tins. But this can be solved, such as in Precision Machine Oil.

Used in:
* [Refined Gold](https://files.mors.technology/Refined-Gold-20g-15c-2a-0i-1h-1.5w-2.5r-T-O-458c6a.mp4)
* [Precision Machine Oil cycles](https://files.mors.technology/Precision-Machine-Oil-255g-12c-18a-39i-4h-4.5w-O-7d63a9.mp4)
* [Armor Filament](https://files.mors.technology/Armor-Filament-155g-13c-17a-16i-3h-7.5w-O-4ed9c8.mp4)
* [Surrender Flare](https://cdn.discordapp.com/attachments/760122588143157278/1119052258341032006/Opus_Magnum_-_Surrender_Flare_20G_3_7_2023-06-16-01-55-20.gif)
* [Very Dark Thread](https://files.mors.technology/Very-Dark-Thread-250g-14c-44a-40i-5h-9.5w-O-8298e0.mp4)
* [Silver Caustic](https://files.mors.technology/Silver-Caustic-315g-24c-51a-69i-8h-7.5w-O-c8e2d1.mp4)
* [Ravari’s Wheel](https://files.mors.technology/Ravaris-Wheel-375g-36c-58a-164i-9h-8.5w-O-4586b7.mp4)

### output+consuming glyph

Requires an elemental input that can be accepted both by a consumption glyph and by an output.
Sometimes hard to set up, making it more useful for rate solves than cycles solves.

Used in:
* [Mist of Incapacitation](https://files.mors.technology/Mist-of-Incapacitation-90g-8c-5a-9i-2h-2.5w-1.5r-O-97886f.mp4) (output+project)
* [Life-Sensing Potion rate](https://cdn.discordapp.com/attachments/760122588143157278/1143981471560904814/Opus_Magnum_-_Life-Sensing_Potion_1190G_331_375_2023-08-23-19-55-15.gif) (output+animismus)
* [Invisible Ink rate](https://files.mors.technology/Invisible-Ink-1965g-150c-704a-2406i-34h-29w-2.67r-O-b37e20.mp4) (output+animismus)
* [Curious Lipstick](https://i.imgur.com/MC2RRaf.mp4) (output+animismus)

The other consuming glyphs would theoretically work too, but have never been used: output+dispersion is not possible on any official puzzle,
while output+unification and output+purification are possible on a few but don’t save any cycles.

### output+wand, project+wand

Requires some way to bond during the B half-cycle *without* bonding during the immediately preceding A half-cycle.
Sometimes hard to set up, making it more useful for rate solves than cycles solves.

The currently known ways to do this are:
* Multi-atom input that stops being suppressed during B.
  * [Health Tonic](https://files.mors.technology/Health-Tonic-60g-8c-16a-8i-4h-4w-T-O-7d3cd9.mp4)
* Triplex bonding with an atom that becomes fire during B.
  * [Explosive Phial rate](https://media.discordapp.net/attachments/1005032287756357672/1152246566556553287/Opus_Magnum_-_Explosive_Phial_675G_35_292_2023-09-15-22-15-48.gif)
* Output of conversion glyph
  * [Mist of Hallucination](https://files.mors.technology/Mist-of-Hallucination-465g-21c-49a-81i-7h-7.5w-O-a2aa93.mp4)
  * [Stain Remover rate](https://cdn.discordapp.com/attachments/760122588143157278/1150913420602912869/Opus_Magnum_-_Stain_Remover_520G_132_171_2023-09-12-00-00-56.gif)
* Quicksilver input that projects silver to gold during A
  * [Seal Solvent rate](https://i.imgur.com/VJ3Fl70.mp4)
* Quicksilver input that projects a metal that is consumed during A
  * [Precision Machine Oil rate](https://cdn.discordapp.com/attachments/1005032287756357672/1150813910077821028/Opus_Magnum_-_Precision_Machine_Oil_1165G_43_291_2023-09-11-23-22-04.gif)
  * [Stamina Potion rate](https://cdn.discordapp.com/attachments/760122588143157278/1150491726797090949/Opus_Magnum_-_Stamina_Potion_935G_71_449_2023-09-10-20-04-22.gif)

## Latency

* Bonding (including triplex bonding), debonding, calcification, and duplication don't add any latency.

* Conversion glyphs add 1 latency, with an exception: if an input is never ever grabbed, the first conversion glyph it goes through is 0 latency.

* Any movement adds 1 latency. Usually, inputs are placed directly on top of the output, such that the last set of inputs doesn't require any movement. However, movement is still required for some specific situations.

* Dropping onto the output adds 1 latency. This is avoidable in three cases: infinite output, using a spare atom as a wand, or when the output is never grabbed.

## Theoretical minimums

Unless otherwise specified, D=0.

### Main campaign

When setting up double-consume is considered unpractical, the theoretical minimum without double-consume is indicated in parentheses.

| Level | Theoretical Minimum | Notes |
| --- | --- | --- |
| Stabilized Water | 3 | N=6, D=3 (trivial), L=0 (output is never grabbed) |
| Refined Gold | 15 | N=30 for quicksilver, D=15 (project+project), L=0 (output is never grabbed) |
| Face Powder | 13 | N=12, L=1 for drop |
| Waterproof Sealant | 12 | N=12, L=0 (water wand) |
| Hangover Cure | 19 | N=18, L=1 for drop |
| Airship Fuel | 9 | N=8, L=1 for drop |
| Precision Machine Oil | 7 (12) | N=12 for lead and qs, D=5 (output+wand via qs for lead, project+wand for qs), L=0 (water wand) |
| Health Tonic | 8 | N=12 for vitae, D=5 (output+wand via suppressed input), L=1 for movement (to set up double-consume) |
| Stamina Potion | 7 (12) | N=12 for bistabilized water, D=5 (output+wand via qs) L=0 (wand) |
| Hair Product | 25 | N=24, L=1 for drop |
| Rocket Propellant | 16 | N=15, L=1 for drop |
| Mist of Incapacitation | 8 | N=12 for air and quicksilver, D=5 (output+output for air, output+project for quicksilver), L=1 for movement (to set up double-consume) |
| Explosive Phial | 8 (10) | N=9, D=2 (output+wand via triplex), L=1 for drop |
| Armor Filament | 13 | N=24 for quicksilver, D=11 (project+project), L=0. D=12 is not possible, since that would inevitably make a copper on cycle 2 |
| Courage Potion | 13 | N=12, L=1 for drop |
| Surrender Flare | 3 | N=6, D=3 (trivial), L=0 (output is never grabbed) |
| Alcohol Separation | 5 | N=6, D=3 (trivial), L=2 for setting up a catalyst (required due to Berlo geometry) |
| Water Purifier | 22 | N=21, L=1 for drop |
| Seal Solvent | 19 | N=24 for air, D=5 (output+wand via qs), L=0 (lead wand) |
| Climbing Rope Fiber | 36 | N=36, L=0 |
| Warming Tonic | 13 | N=12, L=1 for drop |
| Life-Sensing Potion | 9 (13) | N=12, D=5 (output+animismus), L=2 for movement (to set up double-consume) and drop |
| Very Dark Thread | 13 | N=24 for quicksilver, D=11, L=0 |
| Litharge Separation | 11 | This is an output-limited level, so it doesn't follow the `N-D+L` formula. It's possible to output at most twice on cycle 1 and 2, 3x on cycle 3, and 4x on later cycles, giving a minimum of 11 cycles for the 36 required outputs |
| Stain Remover | 19 (24) | N=24 for air and water, D=5 (output+wand via purif output), L=0 (lead wand) |
| Sword Alloy | 49 | N=48, L=1 for purification |
| Invisible Ink | 20 (25) | N=24 for water, D=5 (output+animismus), L=1 for drop |
| Purified Gold | 52 | N=48, L=4 for 5 purification steps (inputs are never grabbed, output is never grabbed) |
| Alchemical Jewel | 36 | N=36 for earth, L=0 (gold wand) |
| Golden Thread | 24 | N=24 for salt, L=0 |
| Mist of Hallucination | 20 | N=24 for air, D=5 (output+wand via purif output), L=1 for drop |
| Timing Crystal | 108 | N=108 for salt, L=0 (gold wand) |
| Voltaic Coil | 24 | N=24 for lead, L=0 |
| Unstable Compound | 29 (31) | N=30, D=2 (output+wand via triplex), L=1 for drop |
| Curious Lipstick | 49 | N=54, D=5 (output+animismus), L=0 (vitae wand) |
| Universal Solvent | 13 (18) | N=18, D=5 (output+animismus), L=0 (lead wand) |

### Journal
| Level  | Theoretical Minimum | Notes |
| --- | --- | --- |
| Van Berlo's Wheel | 22 | N=21, L=1 for drop |
| Van Berlo's Chain | 24 | N=24, L=0 |
| Reactive Cinnabar | 24 | N=24 for salt, L=0 (quicksilver wand) |
| Silver Caustic | 24 | N=36 for quicksilver, D=12 (project+project), L=0 (wand) |
| Lambent II/IX | 48 | N=48 for fire, L=0 (gold wand) |
| Explorer's Salve | 13 (18) | N=18 for vitae, D=5 (output+wand via suppressed input), L=0 (wand) |
| Preservative Salt | 9 | N=9 for salt, L=0 (wand). Rate can double-consume the small input (output+wand via suppressed input) for 1.2r, but this doesn’t help cycles. |
| Sailcloth Thread | 11 | N=11 for salt, L=0 |
| Spyglass Crystal | 39 | N=39 for salt, L=0 (lead wand) |
| Buoyant Cable | 30 | N=30, L=0 |
| Ravari's Wheel | 31 (36) | N=36 for lead, D=5 (output+wand via qs), L=0 (quicksilver wand). quicksilver has N=48, D=18, making it non-limiting |
| Lubricating Filament | 30 | N=30, L=0 |
| Resonant Crystal | 39 | N=38, L=1 for drop |
| Refined Bronze | 13 | N=12 for tinstone, L=1 for movement (required since the tins in the input aren't adjacent) |
| Ablative Crystal | 15 | N=12, L=3 for movement (required to reach purification, since overlapping purification with the input would cause a collision), purification, and drop |
| Proof of Completeness | 12 | N=12, L=0 (no dispersal latency because quintessence is never grabbed, no drop latency because the outputs are never grabbed) |
| Wheel Representation | 12 | N=11, L=1 for dispersal (salt wand) |
| Synthesis via Alcohol | 8 | N=6, L=2 for movement (required because the input isn't unification-shaped) and unification |
| Universal Compound | 92 | N=96 for salt, D=5 (output+animismus or output+unification), L=1 for drop |
| General Anaesthetic | 14 (19) | N=18 for salt, D=5 (output+animismus), L=1 for drop |
| Wakefulness Potion | 13 (16) | N=16, D=3 (output+wand via suppressed input), L=0 (wand) |
| Suture Thread | 24 | N=24 for salt, L=0 |
| Blood-Stanching Powder | 37 | N=36, L=1 for drop |
| Tonic of Hydration | 28 | N=27, L=1 for drop |
| Van Berlo’s Pivots | 11 | N=11, L=0 (wand) |
| Reactive Gold | 26 | N=24, L=2 for two purification steps. Double-consuming requires suboptimal purijection, ends up not saving any cycles. |
| Assassin’s Filament | 30 | N=30 for iron, L=0 |
| Vaporous Solvent | 31 | N=36 for stabilized air, D=5 (output+wand via purif output), L=0 (lead wand) |
| Alchemical Slag | 14 | N=12, L=2 for two purification steps. Using extra inputs to avoid purification latency isn't useful |
| Explosive Victrite | 43 | N=42, L=1 for drop |
| Celestial Thread | 36 | N=36 for air, L=0 |
| Visillary Anaesthetic | 8 (13) | N=12, D=2 (output+wand), L=1 for drop |
| Animismus Buffer | 16 (21) | N=20, D=5 (output+wand via animismus output), L=1 for drop |
| Electrum Separation | 11 | Output-limited level. Maximum of 1 output per cycle from 1 to 3, and 2 per cycle afterwards |
| Hyper-Volatile Gas | 8 | N=6, L=2 for movement and drop (this isn't a tight bound; multiple movement steps are most likely required) |
| Embalming Fluid | 25 | N=24, L=1 for animismus. Alternatively, N=25, L=0 by prebuilding mors. Use vitae wands. |
| Quintessential Medium | 10 | N=10, L=0. The first two inputs go to a dispersal+unification pipeline, used to make 4 stabilized quintessences (not full outputs). A debonding+bonding pipeline outputs on 4 and 6; then the stabilized quintessences are reinjected, outputting on 7, 8, 9, 10 |
