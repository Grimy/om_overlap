# Overlap cycles theory

This guide assumes you're already familiar with [regular cycles theory](https://www.reddit.com/r/opus_magnum/comments/7qmkv6/list_of_current_cycle_optimal_scores/).

Overlap makes it possible to use inputs every cycle, and sometimes even twice per cycle, so instead of the usual `2N+1+L`, minimum cycles for a level is given by `N-D+L`, where:

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

I sometimes refer to half-cycles: steps 1-4 are "the first half-cycle", steps 5-9 are "the second half-cycle".

## Double consuming

As seen above, inputs can spawn twice per cycle. However, this requires the input glyph to be cleared twice per cycle, which is only possible in a limited number of cases:

* Trivial double-consume: in levels like Stabilized Water, where an output can be made from a set of inputs without any movement nor conversion glyphs, the output will naturally trigger twice per cycle.
* Projection + projection: projection triggers twice per cycle, making it very easy to double-consume with it. This is only applicable to single-atom quicksilver inputs.
* Consuming glyph + output: conversion glyphs only consume in the first half-cycle, so unlike projection, they can't double-consume on their own. They can however be combined with an output consuming in the second half-cycle.
* Output + another output: this requires multiple outputs sharing a common atom. Additionally, it must be possible to complete the outputs on different half-cycles, otherwise they’d just bond together.
* Output + same output: this requires using a non-limiting multi-atom input as a wand (or, alternatively, using duplication and triplex to do conditional bonding). Literally only applicable to Health Tonic.

Note that a multi-atom input can only be double-consumed if *all* its atoms are double-consumed on the same cycle.

## Latency

* Bonding (including triplex bonding), debonding, calcification, and duplication don't add any latency.

* Conversion glyphs add 1 latency, with an exception: if an input is never ever grabbed, the first conversion glyph it goes through is 0 latency.

* Any movement adds 1 latency. Usually, inputs are placed directly on top of the output, such that the last set of inputs doesn't require any movement. However, movement is still required for some specific situations.

* Dropping onto the output adds 1 latency. This is avoidable in three cases: infinite output, using a spare atom as a wand, or when the output is never grabbed.

## Theoretical minimums

Unless otherwise specified, D=0.

### Main campaign

| Level  | Theoretical Minimum | Notes |
| --- | --- | --- |
| Stabilized Water | 3 | N=6, D=3 (trivial), L=0 (output is never grabbed) |
| Refined Gold | 15 | N=30 for quicksilver, D=15 (projection+projection), L=0 (output is never grabbed) |
| Face Powder | 13 | N=12, L=1 for drop |
| Waterproof Sealant | 12 | N=12, L=0 (water wand) |
| Hangover Cure | 19 | N=18, L=1 for drop |
| Airship Fuel | 9 | N=8, L=1 for drop |
| Precision Machine Oil | 12 | N=12 for lead, L=0 (water wand). Quicksilver has N=12, L=1, but it can be double-consumed, making it non-limiting |
| Health Tonic | 8 | N=12 for vitae, D=5 (output+same output), L=1 for movement (required to set up the double-consume) |
| Stamina Potion | 12 | N=12 for bistabilized water, L=0 (wand) |
| Hair Product | 25 | N=24, L=1 for drop |
| Rocket Propellant | 16 | N=15, L=1 for drop |
| Mist of Incapacitation | 8 | N=12 for air and quicksilver, D=5 (output+output for air, projection+output for quicksilver), L=1 for movement (required to setup the double-consume) |
| Explosive Phial | 10 | N=9, L=1 for drop |
| Armor Filament | 13 | N=24 for quicksilver, D=11 (projection+projection), L=0. D=12 is not possible, since that would inevitably make a copper on cycle 2 |
| Courage Potion | 13 | N=12, L=1 for drop |
| Surrender Flare | 3 | N=6, D=3 (trivial), L=0 (output is never grabbed) |
| Alcohol Separation | 3 | N=6, D=3 (trivial), L=0 (output is never grabbed) |
| Water Purifier |22 | N=21, L=1 for drop |
| Seal Solvent | 24 | N=24 for air, L=0 (lead wand) |
| Climbing Rope Fiber | 36 | N=36, L=0 |
| Warming Tonic | 13 | N=12, L=1 for drop |
| Life Sensing Potion | 12 | N=12, D=1 (animismus+output), L=1 for drop |
| Very Dark Thread | 13 | N=24 for quicksilver, D=11, L=0 |
| Litharge Separation | 11 | This is an output-limited level, so it doesn't follow the `N-D+L` formula. It's possible to output at most twice on cycle 1 and 2, 3x on cycle 3, and 4x on later cycles, giving a minimum of 11 cycles for the 36 required outputs |
| Stain Remover | 24 | N=24 for air and water, L=0 (lead wand) |
| Sword Alloy | 49 | N=48, L=1 for purification |
| Invisible Ink | 24 | N=24 for water, L=0 (salt wand). The spare salt for the wand comes from animismus+output double-consuming. |
| Purified Gold | 52 | N=48, L=4 for 5 purification steps (inputs are never grabbed, output is never grabbed) |
| Alchemical Jewel | 36 | N=36 for earth, L=0 (gold wand) |
| Golden Thread | 24 | N=24 for salt, L=0 |
| Mist of Hallucination | 24 | N=24 for air, L=0 (lead wand) |
| Timing Crystal | 108 | N=108 for salt, L=0 (gold wand) |
| Voltaic Coil | 24 | N=24 for lead, L=0 |
| Unstable Compound | 31 | N=30, L=1 for drop |
| Curious Lipstick | 49 | N=54, D=5 (animismus+output), L=0 (vitae wand) |
| Universal Solvent | 17 | N=18, D=1 (animismus+output), L=0 (lead wand) |

### Journal
| Level  | Theoretical Minimum | Notes |
| --- | --- | --- |
| Van Berlo's Wheel | 22 | N=21, L=1 for drop |
| Van Berlo's Chain | 24 | N=24, L=0 |
| Reactive Cinnabar | 24 | N=24 for salt, L=0 (quicksilver wand) |
| Silver Caustic | 22 | N=36 for quicksilver, D=14 (10x projection+projection, 4x projection+output), L=0 (wand) |
| Lambent II/IX | 48 | N=48 for fire, L=0 (gold wand) |
| Explorer's Salve | 18 | N=18 for vitae, L=0 (wand) |
| Preservative Salt | 9 | N=9 for salt, L=0 (wand) |
| Sailcloth Thread | 11 | N=11 for salt, L=0 |
| Spyglass Crystal | 39 | N=39 for salt, L=0 (lead wand) |
| Buoyant Cable | 30 | N=30, L=0 |
| Ravari's Wheel | 36 | N=36 for lead, L=0 (quicksilver wand). quicksilver had N=48, D=18, making it non-limiting |
| Lubricating Filament | 30 | N=30, L=0 |
| Resonant Crystal | 39 | N=38, L=1 for drop |
| Refined Bronze | 13 | N=12 for tinstone, L=1 for movement (required since the tins in the input aren't adjacent) |
| Ablative Crystal | 15 | N=12, L=3 for movement (required to reach purification, since overlapping purification with the input would cause a collision), purification, and drop |
| Proof of Completeness | 12 | N=12, L=0 | No dispersal latency because quintessence is never grabbed. No drop latency because the outputs are never grabbed |
| Wheel Representation | 12 | N=11, L=1 for dispersal (salt wand) |
| Synthesis via Alcohol | 8 | N=6, L=2 for movement (required because the input isn't unification-shaped) and unification |
| Universal Compound | 92 | N=96 for salt, D=5 (animismus+output), L=1 for drop |
| General Anaesthetic | 14 | N=18 for salt, D=5 (animismus+output), L=1 for drop |
| Wakefulness Potion | 16 | N=16, L=0 (wand) |
| Suture Thread | 24 | N=24 for salt, L=0 |
| Blood-Stanching Powder | 37 | N=36, L=1 for drop |
| Tonic of Hydration | 28 | N=27, L=1 for drop |
| Van Berlo’s Pivots | 11 | N=11, L=0 (wand) |
| Reactive Gold | 26 | N=24, L=2 for two purification steps. Non-overlap optimization uses N=26 to avoid purification latency, but this isn't required here |
| Assassin’s Filament | 30 | N=30 for iron, L=0 |
| Vaporous Solvent | 36 | N=36 for stabilized air, L=0 (lead wand) |
| Alchemical Slag | 14 | N=12, L=2 for two purification steps. Using extra inputs to avoid purification latency isn't useful |
| Explosive Victrite | 43 | N=42, L=1 for drop |
| Celestial Thread | 36 | N=36 for air, L=0 |
| Visillary Anaesthetic | 13 | N=12, L=1 for drop. Double-consuming isn't possible: both animismus+output and output+output would require overlapping a calcification on an elemental input, permanently losing access to that element |
| Animismus Buffer | 20 | N=20, L=1 for drop. Animismus+output double-consume doesn’t work because animismus requires unbonding but output requires bonding.  |
| Electrum Separation | 11 | Output-limited level. Maximum of 1 output per cycle from 1 to 3, and 2 per cycle afterwards |
| Hyper-Volatile Gas | 8 | N=6, L=2 for movement and drop (this isn't a tight bound; multiple movement steps are most likely required) |
| Embalming Fluid | 25 | N=24, L=1 for animismus. Alternatively, N=25, L=0 by prebuilding mors. Use vitae wands |
| Quintessential Medium | 10 | N=10, L=0. The first two inputs go to a dispersal+unification pipeline, used to make 4 stabilized quintessences (not full outputs). A debonding+bonding pipeline outputs on 4 and 6; then the stabilized quintessences are reinjected, outputting on 7, 8, 9, 10 |
