# Overlap cycles theory

This guide assumes you're already familiar with [regular cycles theory](https://www.reddit.com/r/opus_magnum/comments/7qmkv6/list_of_current_cycle_optimal_scores/).

Overlap makes it possible to use inputs every cycle, and sometimes even twice per cycle, so instead of the usual `2N+1+L`, minimum cycles for a puzzle is given by `N-D+L`, where:

* N is the number of sets of inputs needed to make all required outputs
* D is the number of times you can double-consume limiting inputs
* L is the minimum number of cycles from spawning the Nth set of inputs to completing the puzzle

N has the same value as in regular cycle optimization. L becomes much lower thanks to overlap, and D is entirely new. Before detailing how to compute them, we need to talk about...

## Sub-cycle ordering

Inputs, glyphs and outputs trigger twice per cycle: before and after movement. This doesn't affect normal gameplay, so it was probably done for aesthetic reasons (think of a metal moving from one projection glyph to another). For overlap solutions however, this is extremely relevant, potentially letting you double throughput.

Knowing the order in which everything happens each cycle is necessary for overlap optimization. The basic order is inputs > glyphs > outputs > movement > inputs > glyphs > outputs. Conversion glyphs (animismus, purification, dispersal, unification) only consume atoms before movement, and only produce atoms after movement. The fully detailed order is:

1) Grabs and drops.

2) Unblocked inputs are flagged.
Note: arm bases and hitbox of conversion glyph output will not block input.  These will cause a collision on the next round of collision detection.

3) Inputs flagged in 2 spawn (this allows overlapping inputs to both spawn).

4) Atoms that are not bonded to anything are flagged.

5) Conversion glyphs, disposal, and projection consume; bonding, debonding, calcification, and duplication trigger. This all happens in order from bottom to top (in-game, moving a glyph puts it on top; using an external solution file editor, the last glyph listed is on top).

In this phase, only atoms that were flagged during 4 can be consumed. This creates a delay between debonding and projection.

6) Outputs consume.

7) Window of collision detection: arms move, hitboxes for conversion glyph outputs grow from a point.
Note: fast-forward can delay collision detection by up to half a cycle.

8) Unblocked inputs are flagged.

9) Inputs flagged in 8 spawn.

10) Atoms that are not bonded to anything are flagged.

11) Conversion glyphs produce; disposal and projection consume; bonding, debonding, calcification, and duplication trigger. This all happens in order from bottom to top.

In this phase, only atoms flagged in 10 can be consumed.

Note: atoms produced by conversion glyphs can't interact with glyphs below that conversion glyph, even though their hitbox formed earlier.

12) Outputs consume.

13) Increment cycle count, check for completion.

I sometimes refer to half-cycles: steps 1-6 are "the first half-cycle", steps 7-13 are "the second half-cycle".

## Double consuming

As seen above, inputs can spawn twice per cycle. However, this requires the input glyph to be cleared twice per cycle, which is only possible in a limited number of cases:

* Trivial double-consume: in levels like Stabilized Water, where an output can be made from a set of inputs without any movement nor conversion glyphs, the output will naturally trigger twice per cycle.
* Projection + projection: projection triggers twice per cycle, making it one of the easiest ways to double-consume. This is of course only applicable to quicksilver. 
* Anything + output: conversion glyphs only consume in the first half-cycle, so unlike projection, they can't double-consume on their own. They can however be combined with an output consuming in the second half-cycle.
* Output + another output: this is only applicable to levels with multiple outputs sharing a common atom.
* Output + same output: this is requires using a non-limiting multi-atom input as a wand, making it rarely applicable.
* Anything + disposal : this is rarely useful, but a multi-atom input can only be double-consumed if *all* its atoms are double-consumed, so using disposal to handle stray atoms can actually speed things up.

## Latency

* Bonding (including triplex bonding), debonding, calcification, and duplication don't add any latency.

* Conversion glyphs add 1 latency, with an exception: if an input used is never ever grab, the first conversion glyph is 0 latency.

* Any movement adds 1 latency. It's often (but not always) possible to place the input on top of the output, such that the last set of inputs don't require any movement.

* Dropping onto the output adds 1 latency. This is avoidable in three cases: infinite output, using a spare atom as a wand, or when the output is never grabbed.

## Theoretical minimums

Unless otherwise specified, D=0. Infinites never have drop latency.

### Main campaign

| Level  | Theoretical Minimum | Notes |
| --- | --- | --- |
| Stabilized Water | 3 | N=6, D=3 (trivial), L=0 (output is never grabbed) |
| Refined Gold | 15 | N=30 for quicksilver, D=15 (projection+projection), L=0 (output is never grabbed) |
| Face Powder | 13 | N=12, L=1 for drop |
| Waterproof Sealant | 12 | N=12, L=0 (water wand) |
| Hangover Cure | 19 | N=18, L=1 for drop |
| Airship Fuel | 9 | N=8, L=1 for drop |
| Precision Machine Oil | 12 | N=12 for lead, L=0 (water wand) | Quicksilver has N=12, L=1, but it can be double-consumed, making it non-limiting |
| Health Tonic | 8 | N=12 for vitae, D=5 (output+same output), L=1 for movement (required to set up the double-consume) |
| Stamina Potion | 12 | N=12 for bistabilized water, L=0 (wand) |
| Hair Product | 25 | N=24, L=1 for drop |
| Rocket Propellant | 16 | N=15, L=1 for drop |
| Mist of Incapacitation | 8 | N=12 for air and quicksilver, D=5 (output+output for air, projection+output for quicksilver), L=1 for movement |
| Explosive Phial | 10 | N=9, L=1 for drop |
| Armor Filament | 13 | N=24 for quicksilver, D=11 (projection+projection), L=0 | D=12 is not possible, since that would inevitably make a copper on cycle 2 |
| Courage Potion | 13 | N=12, L=1 for drop |
| Surrender Flare | 3 | N=6, D=3 (trivial), L=0 |
| Alcohol Separation | 3 | N=6, D=3 (trivial), L=0 |
| Water Purifier |22 | N=21, L=1 for drop |
| Seal Solvent | 24 | N=24 for air, L=0 (lead wand) |
| Climbing Rope Fiber | 36 | N=36, L=0 |
| Warming Tonic | 13 | N=12, L=1 |
| Life Sensing Potion | 12 | N=12, D=1 (animismus+output), L=1 |
| Very Dark Thread | 13 | N=24 for quicksilver, D=11, L=0 |
| Litharge Separation | 11 | This is an output-limited level, so it doesn't follow the `N-D+L` formula | You can output at most twice on cycle 1 and 2, 3x on cycle 3, and 4x on later cycles, giving a minimum of 11 cycles for the 36 required outputs |
| Stain Remover | 24 | N=24 for air and water, L=0 (lead wand) |
| Sword Alloy | 49 | N=48, L=1 for purification |
| Invisible Ink | 23 | N=24, D=2 (animismus+output), L=1 for drop |
| Purified Gold | 52 | N=48, L=4 for 5 purification steps, -1 because input is never grabbed | No drop latency because output is never grabbed |
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
| Silver Caustic | 24 | N=36 for quicksilver, D=12 (projection+projection), L=0 (wand) |
| Lambent II/IX | 45 | N=48 for fire, D=3 (output+output), L=0 (gold wand) |
| Explorer's Salve | 14 | N=18 for vitae, D=5 (output+same output), L=1 for movement (required to setup the double-consume) |
| Preservative Salt | 9 | N=9 for salt, L=0 (wand) |
| Sailcloth Thread | 11 | N=11 for salt, L=0 (wand) |
| Spyglass Crystal | 39 | N=39 for salt, L=0 (lead wand) |
| Buoyant Cable | 30 | N=30, L=0 |
| Ravari's Wheel | 30 | N=48 for quicksilver, D=18 (projection+projection), L=0 (lead wand) |
| Lubricating Filament | 30 | N=30, L=0 |
| Resonant Crystal | 39 | N=38, L=1 for drop |
| Refined Bronze | 13 | N=12 for tinstone, L=1 for movement |
| Ablative Crystal | 15 | N=12, L=3 for movement, purification, and drop |
| Proof of Completeness | 12 | N=12, L=0 | No dispersal latency because quintessence is never grabbed | No drop latency because the outputs are never grabbed |
| Wheel Representation | 12 | N=11, L=1 for dispersal (salt wand) |
| Synthesis via Alcohol | 8 | N=6, L=2 for movement and unification |
| Universal Compound | 96 | N=96 for salt, L=0 (lead wand) |
| General Anaesthetic | 19 | N=18 for salt, L=1 for drop |
| Wakefulness Potion | 16 | N=16, L=0 (wand) |
| Suture Thread | 24 | N=24 for salt, L=0 |
| Blood-Stanching Powder | 37 | N=36, L=1 for drop |
| Tonic of Hydration | 28 | N=27, L=1 for drop |
| Van Berlo’s Pivots | 8 | N=11, D=3 (partially output+output, partially disposal+output), L=0 (wand) |
| Reactive Gold | 26 | N=24, L=2 for two purification steps | Non-overlap optimization uses N=26 to avoid purification latency, but this isn't required here |
| Assassin’s Filament | 30 | N=30 for iron, L=0 |
| Vaporous Solvent | 36 | N=36 for stabilized air, L=0 (lead wand) |
| Alchemical Slag | 14 | N=12, L=2 for two purification steps | Using extra inputs to avoid purification latency isn't useful |
| Visillary Anaesthetic | 11 | N=12, D=2 (animismus+output), L=1 for drop | Animismus+output requires overlapping a calcification on the earth input | To keep access to earth, put a duplication below that calcification |
| Electrum Separation | 11 | Output-limited level | Maximum of 1 output per cycle from 1 to 3, and 2 per cycle afterwards |
| Hyper-Volatile Gas | 8 | N=6, L=2 for movement and drop | (This isn't a tight bound; multiple movement steps are most likely required) |
| Quintessential Medium | 10 | N=10, L=0 | The first two inputs go to a dispersal+unification pipeline, used to make 4 stabilized quintessences (not full outputs) | A debonding+bonding pipeline outputs on 4 and 6; then the stabilized quintessences are reinjected, outputting on 7, 8, 9, 10 |
