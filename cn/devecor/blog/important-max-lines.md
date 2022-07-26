# Why we need a concrete `max-lines` limitation?

linter vs code review

## Context and Problem Statement

We're using `eslint` rule of `max-lines-per-function` to keep our function simple and foolish. However, It's annoying for linter to complain that function has exceeded the limit of lines.

### Assumptions

1. Functions should do one thing. they should do it well. they should do it only.
2. Long function is a bad smell in code
3. The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that.

### Constraints
* What's "one thing"?
* How many numbers of a function are acceptable?
* Functions are longer and longer imperceptibly

## Proposal

- daily code review
- delegate to linter

## Pros and Cons of Proposal

### [option 1] daily code review

* Good, because it's helpful to keep code clean
* Bad, because we have no agreement what's "one thing". we probably talk about it again and again.
* Bad, because we potentially let long function unattended instead of loosing limits on purpose.

### [option 2] delegate to linter

* Good, because there is a rule forces us to keep in same page
* Good, because we adjust the limitation on demand as long as we can reach a consensus.
* Bad, because you probably have to exclude dedicated function manually.
* Bad, because the bigger `max-lines`, the less benefit.

## Decision Outcome

Chosen option: "[option 2] delegate to linter". Because it aligns the edge of "one thing" through the limitation of small function. And it allows scale on demand. In addition, we should accept its shortcomings:

1. There are always a few warning that complains the limit of lines is exceeded.
2. To exclude dedicated function manually after we reach an agreement in code review.

## Links

1. [eslint disabling rules](https://eslint.org/docs/latest/user-guide/configuring/rules#disabling-rules)
