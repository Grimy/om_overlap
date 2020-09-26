# Overlap cycles theory

This guide assumes you're already familiar with [regular cycles theory](https://www.reddit.com/r/opus_magnum/comments/7qmkv6/list_of_current_cycle_optimal_scores/).

Overlap makes it possible to use inputs every cycle, and sometimes twice per cycle, so instead of the usual `2N+1+L`, minimum cycles for a puzzle is given by `N-D+L`, where:

* N is the number of sets of inputs needed to make all required outputs
* D is the number of times you can double-consume limiting inputs
* L is the minimum number of cycles from spawning the Nth set of inputs to completing the puzzle

N has the same value as in regular cycle optimization. L becomes much lower thanks to overlap, and D is entirely new. Before detailing how to compute them, we need to talk about...

## Sub-cycle ordering

(copy-paste goes here)

## Double consuming

As seen above, inputs can spawn twice per cycle. However, this requires the input glyph to be cleared twice per cycle, which is only possible in a limited number of cases:

* Trivial double-consume: in puzzles like Stabilized Water, where an output can be made from a set of inputs without any movement nor conversion glyphs, the output will naturally trigger twice per cycle.
* Projection + projection double-consume: unlike the conversion glyphs, projection triggers twice per cycle, which allows quicksilver input 
* Conversion + output
* Output + another output:
* Output + same output: this is not normally possible, but it can be done by using a non-limiting multi-atom input as a wand.
