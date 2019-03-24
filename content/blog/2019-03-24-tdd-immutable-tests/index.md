---
title: 'TDD: Your Unit Tests Should Be Immutable'
date: '2019-03-24T12:51:00.314Z'
---

# Picture this...

- Your project has been following TDD from the very start
- You're tasked with refactoring a function (to improve performance, say)
- You make a relatively minor change, and run the tests
- Your tests for that unit pass, but then a bunch of unit tests in some random component fail!
  - "What the hell is happening, these components aren't even related!" I hear you say.

To fix it you will need to change those tests in those unrelated units, and that's a big problem. Your tests need to be _immutable to refactoring_ and the above scenario is a `Test Smell`.

# Why is this a problem?

There are lots of benefits to TDD, but one that doesn't get as much airtime is that:

> A good test-suite gives you **confidence** about the behaviour of your system.

However that confidence is NOT guaranteed by tooling, it comes from the fact that you followed TDD. You wrote a test, it failed, then you wrote some code and it passed. Therefore, that code works.

That means that the only reason you trust your codebase is because you trust your fellow developers to have _also_ followed TDD. Which is to say, that relying on your test-suite is not a matter of Fact, but a matter of Trust.

Hence once a test-suite exists, we need to protect our Trust in it by minimizing changes to it.

Coming back to our hypothetical situation from earlier, Refactoring (by definition) is not supposed to be a change in behaviour. If you're now forced to update test-code in unrelated units, you now need to _manually_ validate those units and (all their usages) before trust is restored in the system.

# Reasons for Problem

For example, your unit originally set an internal state's `canProceed` and you refactored it to `shouldProceed`. These other units are failing because they were asserting on the value of `canProceed`.

If you go into those failing tests and update the tests with the new assumption it's _highly_ likely that an error will get made and you might assert `false` instead of `true` or any number of other issues. You might even accidentally delete a test (in more complex scenarios). The worst part of it is that your test suite can't save you from these mistakes because it can't self-test; the Light going Green doesn't mean anything if the test implementation is giving you a false-positive.

# Desired Behaviour

The only reason to change a test should be if the required behaviour of the system changes. In all other cases, we expect the tests to be `Immutable`.

# Achieving Test Immutability

Since we don't have a test-suite for our test-suite, there's increased pressure to follow clean-code guidelines while implementing it. Once tests are written the intention is that they should be encased in ember, so we should aim to get it right the first time.

Here are some suggestions:

## Name your tests explicitly

Bad: `test('should set correct headers'),`

Good: `` test('should set the `device` header to `mobile`') ``

If you're writing the test name and it's running a bit lengthy, you might be trying to test too many things or might have too many state-values to list out.

That's okay, favour an explicit name over a short sentence. Atleast it's clear. And anyway, you can fix that in the next point.

## Group your tests semantically

- Read up on [RSpec](https://lmws.net/describe-vs-context-in-rspec)
  - use `describe` to denote the behaviour you're testing,
  - use `context` to group tests by common assumptions or world-states

```javascript
describe("launch the rocket", () => {
    context("all ready", () => {
        test('should launch rocket")
    })
    context("not ready", () => {
        test('should do nothing")
    })
})
```

- Your tests statements should ideally read like documentation,
  - This has the added benefit of making your tests easier to review
- Breaking things up in this way also avoids long test-names
  - it also becomes easier to locate and debug failing tests during refactoring

## Single Assert Per Test

- Once your test names are discrete, have only a single `assert` per test
- If multiple asserts are "necessary", you're probably trying to check multiple things
  - the downside is that these extra things aren't documented in the test-name
  - tomorrow those extra asserts might get removed because they don't look important
- avoid all this drama, just keep a single assert per test.

## Don't assert on an Object

Take the following code as an example:

```javascript
describe('createRequest', () => {
  test('set correct headers', () => {
    const actual = createRequest(
      { _csrf: 'abcd', __csrf: 'pqrst' }, // cookies
      {
        // params
        url: 'AAA',
        variables: { A: 1, B: 2 },
      }
    )
    const expected = {
      'X-CSRF': 'pqrst',
      'content-type': 'application/json',
      device: 'pwa',
    }
    assert.deepEqual(actual.headers, expected)
  })
})
```

"This looks fine", I hear you say, "What's the Issue?". Well, this is basically just a hidden version of having multiple asserts per test.

- Here there's three asserts:
  - one for the `X-CSRF` header,
  - one for the `content-type` header, and
  - one for the `device`.
- To make matters worse, the test title doesn't give you any information about what the desired output is.

Refactor your tests to something like this:

```javascript
describe('createRequest', () => {
  describe('headers', () => {
    test('should set X-CSRF header correctly', () => {
      const actual = createRequest(
        {_csrf: 'abcd', __csrf: 'pqrst'}, //cookies
        {
          //params
          ...
        }
      )
      const expected = 'pqrst'
      assert.equal(actual.headers['X-CSRF'], expected)
    })
    test('should set `content-type` correctly', () => {
      const actual = createRequest(
        {}, //cookies
        {
          //params
          ...
        }
      )
      const expected = 'application/json'
      assert.equal(actual.headers['content-type'], expected)
    })
    test('should set `device` header correctly', () => {
      const actual = createRequest(
        {}, //cookies
        {
          //params
          ...
        }
      )
      const expected = 'pwa'
      assert.equal(actual.headers.device, expected)
    })
  })
})
```

## Scrutinize your Testing-Code more than your Application Code

- Your tests basically contain all the information about the design of your new component
  - Not only it's behaviour, but it's interface and it's dependencies
- If the testing code is solid, well written, and exhaustive, refactoring Application Code will be trivial!
  - If there's a gap in the tests, that's something that will only be felt in an integration test (or in Production)
- This is a Process thing and is up to the team on how to enforce it

## Manage your branches well

- Be extra careful when resolving Merge-Conflicts in test-code
- Again, the test-code can't test itself.
- If a change has been made to an existing test, the fact that it passes doesn't prove anything.

# Summary

Ultimately maintaining the test-suite is a matter of Discipline, Code-Guidelines and good Code-Review process as well as a reliable set of Team-mates in a trustful environment.

As individual developers I claim that if we're going to be a craftsmen about anything, we should apply ourselves to the testing code. Our future selves will thank us for it.

Good luck out there. :)

# References

- [TDD Done Right - Tests Immutable to Refactor](https://developer20.com/tdd-done-right/)
- [Unit Testing Smells](https://dzone.com/articles/unit-testing-smells-what-are-your-tests-telling-yo)
- [TDD | Martin Fowler](https://martinfowler.com/bliki/TestDrivenDevelopment.html)
- [UncleBob Expecting Professionalism](https://youtu.be/BSaAMQVq01E?t=2105) (35:05 - 41:40)
