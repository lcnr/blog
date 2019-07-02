# Releasing rubot and what's next

I have spend the last few weeks working on a generic game bot
which can be used in many different games without requiring a lot of work.
As this bot is now finally ready for use, I want to summarize what I have learned,
my general experience using rust and how I am going to
keep improving this controller.

## What exactly is this crate

[rubot] is a library which contains a bot capable of solving decision trees and
a trait which must be implemented to generate a decision tree for a game.
This should make it easy to add a somewhat decent bot to a game without
spending too much time implementing and testing an algorithm. 

[rubot]: https://github.com/lcnr/rubot
