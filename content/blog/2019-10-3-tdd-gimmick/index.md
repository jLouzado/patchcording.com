---
title: 'Is TDD just a gimmick?'
date: '2019-03-24T12:51:00.314Z'
---

[[toc]]

# Statement

Every day we see new frameworks rise and fall, and a certain amount of skepticism is healthy to keep from jumping on every new thing that comes along.

Having said that, something that has radically improved the quality and maintainability of the software I've written over the past year has been `Test Driven Development` (TDD for short). From my perspective writing the test **before** the implementation is not just a gimmick:

> Unit tests produced by TDD guarantee code-coverage in a way that tests written post-implementation cannot.

## Maybe it does, but why should I care about code-coverage?

Just to be clear, 100% code-coverage means that if a function is performing `N` operations, each of them are covered by a unit-test. Put another way, if someone were to delete[^del] a line of code atleast one unit-test _should_ fail.

If we have coverage we can have confidence, and with that confidence you get some effects:

- code comprehension will always take linear time even if code-complexity increases exponentially.
  - the tests will always exhaustively describe the function's behaviour, regardless of how "clever" the implementation is.
- refactoring code is much easier and faster because feedback is so much quicker
- logical errors (bugs) become much easier to spot and fix, since other people can read your tests and check if all the requirements are being fulfilled.
- changes to the codebase are guaranteed to not introduce regression bugs.

So confidence (via code-coverage) is the whole game; you will see the above benefits in your codebase to the extent that the requirements are covered, simple as that.

## What does "Produced By TDD" mean?

First, a quick recap of TDD's rules.

- If you want to write new code (or make changes to an existing unit) first write a failing test.
  - this is the `RED` phase.
- Only write as much code as is necessary to produce a failure.
  - compilation errors count as failures, and
- Only write as much code as is required to fix that failure, nothing more.
  - this is the `GREEN` phase
- Rinse and repeat till all your requirements are implemented.
- Go back over your implementation and clean-up.
  - This is the `REFACTOR` phase.

In practice it might look like this. We'll take a simple example to start with.

Say, I want to write a function `mul2` that returns the product of two arguments:

First create the test-file: `mul2.test.ts` and start writing the test:

```ts
it('should return the product', () => {
  const actual = mul2()
})
```

As soon as this is written, the compiler will complain that `mul2` doesn't exist. Only at this point do you actually create the `mul2.ts` file and write:

```ts
export const mul2 = () => {
  throw 'not implemented'
}
```

All you know is that it's a function, which is what you've declared. No implementation yet, no arguments either.

Back to the test:

```ts
it('should return the product', () => {
  const actual = mul2(2, 3)
})
```

The compiler will complain: "Function doesn't accept values". Fair enough, now is a good time to think about the "API" of our function:

- What are the order of the arguments,
  - important if the function might be curried
- Can any of them be null? do any of them have default values? What are their names?
- What's the return type of the function?

```ts
export const mul2 = (a: number, b: number) => {
  throw 'not implemented'
}
```

Since it's just a simple function, the types are pretty basic but if there were more complex behaviour, here's where we would take care of that[^typings].

Back to the test, now we can add our assertion:

```ts
it('should return the product', () => {
  const actual = mul2(2, 3)
  const expected = 6
  assert.strictEqual(actual, expected)
})
```

The test itself is now runnable. Run it, and it'll throw "Not Implemented" as we told it to. This is the point people skip to when they think of unit-tests, but it takes a lot of thinking even to get to this point; we're just so used to doing it we forget to account for it.

```ts
export const mul2 = (a: number, b: number) => {
  return a * b
}
```

With that your test will pass, and you can start with the next test!

## But this way of writing tests must be super annoying

Writing code this way is definitely a little unnatural at first, but it makes sure that nothing extraneous ever enters your code base. When people talk about Lean-Testing, and only writing the minimum tests required, this is really what that looks like; no more tests than necessary, no more code than needed.

Over time the intuition for this style will build and your compiler will be the best pair-programming partner you could ever ask for.

## Cool, but I could just write the function and then write the test afterwards

As far as I've spoken to people, no one disagrees with the need to have some unit tests. That means we all agree that it's possible to write buggy code. That implies that our unit-tests could _also_ have bugs; how would we detect that if we wrote it second?

## What do you mean by a buggy test, how can a test have a bug?

The purpose of a test is to check the correctness of the function. So anything that prevents it from doing that, is a bug. i.e. if a test isn't actually checking anything then it's not doing it's job.

Let's use the example above; let's say I finished implementing `mul2`, and then wrote the test below:

```ts
it('should return the product', () => {
  const actual = mul2(2, 3)
  const expected = 6
})
```

Notice anything? The assertion statement is missing, but the actual test will pass[^false-positive]. Here even though everything looks okay, the test itself is not trustworthy.

## What a ridiculous example, who would forget to add the assertion!

Well yes it's a bit silly, but it highlights a central problem with unit testing. Namely:

> How do you know that the test you've written is correct?

Any alternative proposed to TDD **must** be able to solve this problem or else it cannot be said to be viable.

The fact that the test is written first solves this problem. If the test is correct, it _must_ fail.

In the example above you expect it to fail, so you'll revisit the test-code when you see it's passing even if no code is present. The rules push you to prioritize confidence in the test first before starting on the change you want to make.

## Hmm... Well what about Logical Errors, TDD is not going to save me there

TDD doesn't guarantee that your code is bug-free. It just guarantees that if a line is deleted that a test will fail. Therefore it's still your responsibility to write the correct tests. For example if the requirement was to multiply two numbers, but your tests are checking if the code adds the two together then your code is still wrong.

In such a scenario, TDD helps in two ways:

- it makes such an error easier to spot during code-review
  - since your `expected` value will clearly be something different than the requirement, someone can flag it as an issue
- once the error is spotted, it becomes easier to rewrite the bad test or to add tests for missing edge cases.

Since refactoring is easier, this whole process happens much more quickly. And yes, while fixing one issue you're less likely to introduce a a regression even in this new feature.

## This is all horribly complicated! I'm a pretty good programmer, I don't need all this

I felt the same way when first starting out. Even now, when discussing some of the finer points of TDD it can feel a little overly-cautious and pessimistic. The thing is though, if there's more than 2 programmers working on a project it's just not possible to always be available for everyone.

Imagine you've written a function, and someone wants to make a change to it. If they knew that the tests were high-quality, they could just figure it out themselves and be fairly confident that they weren't doing anything wrong. In the absence of a test-suite they would (hopefully) come and sit on your head and work things out with you.

In the worst case, they're under a lot of pressure so they make the change that re-introduces some subtle bug that you'd already taken care of initially. The bug might only get discovered months later, at which point maybe the dev who introduced the problematic-change is on leave and the commit message just says "fixed issue" so now you're really screwed.

At it's best, TDD is a way to communicate your decisions without needing to write extensive documentation that no one will ever read (or maintain when they make changes). Good-quality unit tests save such an incredible amount of time, and ultimately that's more time that you have to work in peace.

## Are there any cases where one shouldn't use TDD?

For application development, almost everything is a good candidate for TDD. The one exception for frontend development would be if your function returns a particular DOM structure, there's no point writing a test like `Assert <div>Hello World!</div>` and then having a function that just returns `<div>Hello World!</div>`.

You _could_ do it, but then tomorrow if you need to return `<div>Hello <b>World!</b></div>` should the test really break? I mean, I added an enhancement, the old test should "technically" still be valid.

This doesn't mean that all view-related things cannot be done via TDD though. You might have a rock-solid reducer implemented, but if you forget to actually fire an action when a button is clicked then your app is still buggy. Packages like [react-testing-library](https://github.com/testing-library/react-testing-library) help with that problem.

## Any others?

Yes, if you're writing a new package, and there's two potential APIs that would both work equally fine but they both have different (say) performance implications based on their implementation then that might be a bad use-case for TDD.

In such a case you're really out near the edge of software-development and you're almost in research mode. Once you've sorted out architecture and explored the space sufficiently, that's when TDD might be of more use.

I can't stress this enough though, almost everything related to Application Development can be done via TDD. As much as possible, extract your business logic into pure functions and TDD the heck out of it. You'll thank yourself later.

## Fiinnnneeeee, lemme think about it

Awesome! :smiley:

If you're looking to read more about TDD, I'd highly recommend [You Don't Know TDD](https://itnext.io/you-dont-know-tdd-691efe670094) which was what I used when I was first learning about this. It's a really rich resource and he deals with much more complex examples than I have here.

In the meantime if you'd like to know how to write unit tests in such a way that they don't break randomly when you're doing some reasonable refactoring, check out this piece I wrote called [Your Tests Should Be Immutable](https://dev.to/jlouzado/tdd-your-unit-tests-should-be-immutable-119m#dont-assert-on-an-object) so that you never have to touch your tests again unless a requirement changes.

Good luck out there. :pray:

[^del]: Assume the deletion was accidental, and in such a way that the code still compiles.
[^typings]: If the typings are very complex, you might even want some way to develop _those_ using TDD, but that's another topic entirely. Check out something like [typings-checker](https://www.npmjs.com/package/typings-checker) if you're interested.
[^false-positive]: Run it in your IDE and check for yourself if you don't believe me.
