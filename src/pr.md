Thanks for your very careful review and feedback. I have some thoughts on your comments:

As I understand your comments, you raise 2 design questions and have identified a bug in my implementation. With respect to the design questions:

1. Handling overlapping matches: I could see something more sophisticated here, but yes, I think this (relatively simple) approach works well in practice (at least in my use cases). I could see an argument for improving this by halting next/prev match movement when you reach the limit of the file (or, wraparound, but that would be super confusing).

2. Selections moving outside of the viewport: this seems like an inherent design limitation of multiple selections, and it is the primary reason they can't replace keyboard macros for all use cases. This issue can arise without the incremental search feature: just add more cursors for a given search term than can fit in the window. My expectation as a user would be that this would "work" even if I can't see the cursors, I wouldn't want an implementation to just drop cursors when they are out of the viewport. Thus my preference would be that things continue as normal irrespective of viewport, and it's the users responsibility to recognize that this could pose problems.

Both of these, however, are indeed preferences, and one could improve the implementation by adding command options to change how the implementation works. (e.g. `deleteSelectionsOutsideViewport: true` or `stopWhenMatchReachesLimits: true`, the latter problem would be a sensible default).

Then there's the bug you point out. Basically, if I understand correctly, you're talking about a scenario in which there are a different number of search tokens in the neighborhood of each cursor, so when you search forwards and backwards the cursor could be at a different position relative to the start of the search. I think the immediate issue is fixable:

However, I am trying to imagine a scenario in which multi-cursor searching would actually be useful for that sort of situation. In general the benefit of searching with multiple selections is that you can identify tokens in the neighborhood of a symbol, on the basis of shared syntactic structure at the different locations (e.g. quickly move to the next parenthesis, move to the next if statement, move to the next else statement); if there are a different number of said tokens near one of the cursors, then the structure of the text near the cursor is necessarily different, and so it's not clear that any mutations you made at those location would be useful.

My point is that any situation where the cursors cross paths or go in different directions would mean that you are editing text within each selection location that isn't similar in structure and the whole point of multi-selection would disappear. I think the implementation should handle those situations gracefully ( and clearly there is some work to improve that ) but it doesn't seem like you would want it to be useful in that situation.

I don't think implementing this only for the use case of `t` or `f` from vim would be very helpful, due to the use cases mentioned above (e.g. searching for `else` or `if`).