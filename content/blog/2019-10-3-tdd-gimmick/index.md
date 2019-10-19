---
title: 'Is TDD just a gimmick?'
date: '2019-03-24T12:51:00.314Z'
description: 'Does it really make a difference if I write the test after the implementation?'
---

# Is TDD A Gimmick?

[[toc]]

## What's the big deal?

Something that has radically improved the quality and maintainability of the software I've written over the past year has been `Test-Driven Development` (TDD for short). My claim is that writing the test **before** the implementation is not just a gimmick:

> Unit tests produced by TDD guarantee code-coverage in a way that tests written post-implementation cannot.

As developers I understand that there's a healthy amount of skepticism we all have so let's go through some of the common responses I get when people hear that claim.

## Maybe it does, but why should I care about code-coverage?

Just to be clear, 100% code-coverage means that if a function is performing `N` operations, each of them are covered by a unit-test. Put another way, if someone were to delete[^del] a line of code at least one unit-test _should_ fail.

If we have coverage we can have confidence, and with that confidence you get some effects:

- effort to comprehend a piece of code will always grow more slowly than the complexity of that code, because:
  - there's always going to be at least one example of every potential usage, and
  - you get an opportunity to play around with the code and see how it affects the test suite.
- refactoring code will be much easier and faster because feedback is so much quicker
- logical errors (bugs) will be much easier to spot and fix, since other people can read your tests and check if all the requirements are being fulfilled.
- changes to the codebase are guaranteed to not introduce regression bugs.

So confidence (via code-coverage) is the whole game; you will see the above benefits in your codebase to the extent that the requirements are covered, simple as that.

## But I could just write the function and then the tests and get the same coverage

Almost everyone I've spoken to agrees that we need unit tests because we know bugs can creep into our code. Meanwhile unit-tests are also code, don't we need to guard against bugs there?

Writing a test after the implementation leaves the possibility open that the test itself is buggy.

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

## What a ridiculous example, that would never happen!

Well yes it's a bit silly, but it highlights a central problem with unit testing. Namely:

**How do you know that the test you've written is correct?**

Any alternative proposed to TDD **must** be able to solve this problem or else it cannot be said to be viable.

The fact that the test is written first solves this problem. If the test is correct, it _must_ fail.

In the example above you expect it to fail, so you'll revisit the test-code when you see it's passing even if no code is present. The rules push you to prioritize confidence in the test first before starting on the change you want to make.

## What about Logical Errors, TDD won't save me there

TDD doesn't guarantee that your code is bug-free. It just guarantees that if a line is deleted that a test will fail. Therefore it's still your responsibility to write the correct tests. For example if the requirement was to multiply two numbers, but your tests are checking if the code adds the two together then your code is still wrong.

In such a scenario, TDD helps in two ways:

- it makes such an error easier to spot during code-review
  - since your `expected` value will clearly be something different than the requirement, someone can flag it as an issue
- once the error is spotted, it becomes easier to rewrite the bad test or to add tests for missing edge cases.

Since refactoring is easier, this whole process happens much more quickly. And yes, while fixing one issue you're less likely to introduce a regression.

## This is all horribly complicated! I'm a pretty good programmer, I don't need all this

That might be true, you might not need tests in order to produce good quality code. However, you're not the only person on your team. Also, you're probably not going to be on that team forever; tomorrow you might move on to another project or even to another company. What happens then?

Writing tests is a way to communicate[^communicate] the behaviour of the code without needing to write extensive documentation that no one will ever read (or maintain). Good-quality unit tests save an incredible amount of dev-hours, and can help a team to maintain a stable velocity as it grows.

## What exactly are good-quality tests?

One key aspect of a "good" test would be that they are [immutable to refactoring]. That means that once a test is written, it shouldn't change as long as the requirements don't change.

## Okay, but how does that translate to "saved" dev-hours?

One of hardest parts of building software is simply managing the communication overhead as the team scales. The number of connections between people is proportional to $N^2$ for $N$ members. So very quickly, it becomes almost impossible to keep everyone in sync if it requires talking to everyone about everything. Various solutions like "write documentation" or "write code that is so clear it doesn't need documentation" are all theoretically possible but are largely pipe-dreams. If it's possible to have a test suite for something, it's the ultimate safety-net and allows developers to make changes without worrying.

## You say TDD is a timesaver, but updating old tests is a huge-waste of time!

If you're following TDD correctly, you shouldn't find yourself updating old tests. Once you write a test, they should be [immutable to refactoring] i.e. it won't change as long as the API and the requirements haven't changed.

If you find yourself doing TDD but also going back over old-tests a lot, then a couple of things might be going wrong:

### The Function's API needs improvement

When you're writing that first test, spend more time designing your unit's signature to be extensible. Think of the [Open-Closed Principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle), but applied to a function instead of a class.

We'll get into the specifics of writing that first test [further down](#your-examples-are-a-bit-weird).

### You're writing too much code too soon

Let's say you're trying to implement an expression: $A+!B$ (A or not-B). The first would be:

```ts
it('should return true if A is true')
```

What should the implementation be? Your impulse might be to write the following:

```ts
if (A) return true
```

However, the least code required to make this test pass would simply be:

```ts
return true
```

It sounds ridiculous for simple examples, but when writing complex algorithms it's common to fall into the trap of over-engineering. However, [no abstraction is better than the wrong one](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction) and being disciplined about writing minimal code helps us get all the examples working before trying to solve for other concerns like performance, reusability, maintainability, etc.

In a way, TDD pushes us to focus on one thing at a time so that we can be completely focused on it. One test at a time, one solution at a time, and then refactor for one purpose at a time. When you're doing one thing, you don't have to hold everything in your mind since you know that the test suite has your back.

For a more complex example check out [The Missing Practical Step in TDD](https://itnext.io/the-missing-practical-step-by-step-test-driven-development-a7140ca4b71)

### Your tests are too specific, or too restrictive

This might sound funny, but it's possible for tests to be overly specific. We've touched on this before, but tests should really be agnostic of implementation. In other words they should care about _behaviour_ and not be asserting on structure.

The obvious example would be the test from the earlier section where we're asserting a DOM structure but there's other ways that a test can be too restrictive.

Let's say you're building a [Flux]-like App (using something like React-Redux), and you need some code to emit an `Action`. Your first test might be:

```ts
it('should fire Action-A1 when a X is true')
```

and your test might be:

```ts
assert.strictEqual(actual, action.of(A1))
```

Now, you write a new test:

```ts
it('should fire Action-A2 when X is true')
```

and you'll write the second test as follows:

```ts
assert.strictEqual(actual, action.of(A2))
```

The problem is that now you're stuck; test1 and test2 are both mutually exclusive because something can't be strictly equal to two things at the same time. In this case, saying `strictEqual` is overly specific; it's saying "this and only this is allowed to happen".

What might be closer to your intent is to say "whatever else is also happening, I _also_ want this to happen". So you could rewrite your tests using some kind of `contains` utility as such:

```ts
assert.isTrue(actual.contains(action.of(A1)))
```

Now tomorrow if any number of actions are fired, your test is making sure that this action is also present. And if you've followed TDD so far, extra actions won't be fired from anywhere.

## Does that mean there any cases where one shouldn't or can't use TDD?

For application development, almost everything is a good candidate for TDD. One exception is where it's not possible to write a test without being over-specific. For example there's little value in writing a test like `Assert that function returns <div>Hello World!</div>` and then having a function that returns `<div>Hello World!</div>`.

You _could_ do it, but then tomorrow if you need to return `<div>Hello <b>World!</b></div>` should the test really break? I mean, I've only added an enhancement, the old test should "technically" still be valid.

The above example doesn't mean that all view-related things are automatically not eligible for TDD. To take an example of an App written in the [Flux] pattern, you might have a rock-solid reducer ready and waiting but then you might forget to actually fire an action from the view when a button is clicked. Packages like [react-testing-library](https://github.com/testing-library/react-testing-library) are good for use-cases like that, and can provide coverage for your views too.

## Any others?

Yes, if you're writing a new package, and there's two potential APIs that would both work equally fine but they both have different (say) performance implications then that might be a bad use-case for TDD. Tests can't help you choose between two different function signatures, for example. In such a case you're really out near the edge of software-development and you're in research mode. TDD will of more use once you've explored the space sufficiently and sorted out the architecture.

Speaking of architecture, structure your code so that more things are unit-testable, otherwise you end up with scenarios where you have to compulsively mock everything and you're testing "structure", rather than "behaviour"[^behaviour-over-structure].

I can't stress this enough though, almost everything related to Application Development can be done via TDD. As much as possible, irrespective of the structure of your application, extract your business logic into pure functions and TDD the heck out of it. You'll thank yourself later.

## Your examples are a bit weird

That's a great question, this is something like [Wittgenstein's Beetle](https://medium.com/@fagnerbrack/wittgenstein-s-beetle-in-software-engineering-dcea89a5db92) where everyone has a thing in their box called a beetle and can only see their own box's contents. So then people might say "TDD is good" or "TDD is bad" and be talking about entirely different things. Here's what I mean when I'm talking about TDD:

A quick recap of TDD's rules from [Uncle Bob](http://www.javiersaldana.com/articles/tech/refactoring-the-three-laws-of-tdd):

- You are not allowed to write any production code unless it is to make a failing unit test pass.
- You are not allowed to write any more of a unit test than is sufficient to fail; and compilation failures are failures.
- You are not allowed to write any more production code than is sufficient to pass the one failing unit test.

Therefore,

- First, write only enough of a unit test to fail.
  - `RED` phase
- Write only enough production code to make the failing unit test pass.
  - `GREEN` phase.
- Repeat till all requirements are met, refactor as needed.

In practice it might look like this. We'll take a simple example to start with. Say, I want to write a function `mul2` that returns the product of two arguments:

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

## This way of writing tests seems super annoying

Writing code this way is definitely a little unnatural at first, but it makes sure that nothing extraneous ever enters your code base. When people talk about Lean-Testing, and only writing the minimum tests required, this is really what that looks like; no more tests than necessary, no more code than needed.

Over time your intuition for this style will grow and your compiler will come to feel like a helpful (if slightly pedantic) aide who just wants the best for you and your code. :)

## Where would I go to learn more?

If you're looking to read more about TDD, I'd highly recommend [You Don't Know TDD](https://itnext.io/you-dont-know-tdd-691efe670094) which was what I used when I was first learning about this. It's a really rich resource and he deals with much more complex examples than I have here.

In the meantime if you'd like to know how more about writing tests that don't break during refactoring, check out:

> [Your Tests Should Be Immutable](https://dev.to/jlouzado/tdd-your-unit-tests-should-be-immutable-119m)

Feel free to hit me up on [Twitter] if you have any questions and good luck out there. :pray:

[^del]: Assume the deletion was accidental, and in such a way that the code still compiles.
[^typings]: If the typings are very complex, you might even want some way to develop _those_ using TDD, but that's another topic entirely. Check out something like [typings-checker](https://www.npmjs.com/package/typings-checker) if you're interested.
[^false-positive]: Run it in your IDE and check for yourself if you don't believe me.
[^behaviour-over-structure]: This is similar to the previous example, where we don't want to write a test to guarantee a particular DOM structure. Check out [the tragedy of 100% code-coverage](https://dev.to/danlebrero/the-tragedy-of-100-code-coverage) for more.
[^communicate]: One caveat, don't try to communicate the "Why" of a change using tests. Tests are about behaviour, not purpose.

[immutable to refactoring]: https://dev.to/jlouzado/tdd-your-unit-tests-should-be-immutable-119m
[flux]: https://www.freecodecamp.org/news/an-introduction-to-the-flux-architectural-pattern-674ea74775c9/
[twitter]: https://twitter.com/jlouzado
