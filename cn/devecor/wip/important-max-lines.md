# Why we need a concrete `max-lines` limitation?

## Context and Problem Statement

We're using `eslint` rule of `max-lines-per-function` to keep our function simple and foolish. However, It's annoying for linter to complain that function has exceeded the limit of lines.

### Assumptions

1. Functions should do one thing. they should do it well. they should do it only.
2. Long function is a bad smell in code
3. The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that.

### Constraints
* What's the meaning of "one thing"?
* How many numbers of a function is acceptable?
* Functions are longer and longer imperceptibly

## Proposal

- daily code review
- delegate to linter

## Pros and Cons of Proposal <!-- optional  -->

### [option 1]

[example | description | pointer to more information | …] <!-- optional -->

* Good, because [argument a]
* Good, because [argument b]
* Bad, because [argument c]
* … <!-- numbers of pros and cons can vary -->

### [option 2]

[example | description | pointer to more information | …] <!-- optional -->

* Good, because [argument a]
* Good, because [argument b]
* Bad, because [argument c]
* … <!-- numbers of pros and cons can vary -->

## Decision Outcome

## Links <!-- optional -->

1. [eslint disabling rules](https://eslint.org/docs/latest/user-guide/configuring/rules#disabling-rules)
