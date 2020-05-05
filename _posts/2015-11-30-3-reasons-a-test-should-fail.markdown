---
layout: post
title:  "The Only Three Reasons My Unit Test Should Fail"
date:   2015-11-30
categories: [TDD]
permalink: /2015/11/30/3-reasons-a-test-should-fail/
---

When I practice TDD, my goal is to design a system with a test suite that
encourages good design and enables confident refactoring. One of the ways I do
that is by keeping in mind when a test should fail. Ideally, I only want to see
test failures for one of these three reasons:

1. The unit’s not built yet
2. The unit’s public interface changes
3. The unit’s behavior changes

I consider these rules. If they are all true, I must not see a test failure. If
any of them are not true, I must see a test failure.

## Reason 1: The unit’s not built yet

Of course a test for a thing that doesn’t exist will fail. But this is a failure
that I must see for each test at least once. It is how I know the test is
actually testing what I think it’s testing. It’s surprisingly easy to write a
test for something after it’s built, but write it in a way that it can never
actually fail (and is therefore useless). This is one of the reasons why I
prefer to write my tests first.

## Reason 2: Public Interface Changes

If I am testing an object’s method, and the way I init the object or call the
method changes, my test should fail. This seems obvious, but notice that I only
want my tests to fail if my public interface changes. I don’t want my tests to
depend on private implementation details. This means my tests are a consumer of
my internal API. In other words, my tests interact with my code the same way my
production code interacts with it. This is what puts good design pressure on my
system and is the driving force behind the idea that “code that’s easy to test
is easy to write/read/change”. It also keeps my code easier to refactor, which
I’ll get to later.

## Reason 3: Behavior Changes

If the behavior of my unit changes, my test should fail. This also seems
obvious, but again, I want to dig in to what this means for my system.

For example, consider this unit:

```
class Greeter(object):
  def greet(self, name):
    return "Hello %s" % name
```

My test might look something like this:

```
def test_greet_says_hello_to_name(self):
  greeter = Greeter()
  self.assertEqual(greeter.greet("Justin"), "Hello Justin")
```

If greet changes to write to a display instead of returning a string, this test
will fail. That’s good because this is a change in behavior. That change will
require a change to any consumer of the unit, and my test is a consumer of that
unit just like the production code is.

## Confident Refactoring

I use these rules to keep good design pressure on my system and keep things easy
to refactor. For example, let’s say we do want to write to a display in that
Greeter class. One implementation might look like this:

```
class Greeter(object):
  def greet(self, name):
    sys.stdout.write("Hello %s" % name)
```

I often see code like this tested with a mock in this way:

```
@patch('greeter.sys.stdout')
def test_greet_says_hello_to_name(self, stdout):
  greeter = Greeter()
  greeter.greet("Justin")
  stdout.write.assert_called_once_with("Hello Justin")
```

But this breaks one of my rules: this test is coupled to a private
implementation detail of my unit. There’s nothing in my public interface that
conveys my usage of sys.stdout yet my test knows to mock exactly that module. If
that implementation changes, my test will fail, even though my public interface
and the behavior has not changed.

This makes my code hard to refactor. When I’m refactoring, I only want to look
at the implementation. I want to be free to change private details without fear
of breaking tests (and therefore breaking production code) as long as I’m not
changing the behavior or the public interface. That’s not true here. If I change
my usage of sys.stdout to something else, I will get a surprising test failure.

What does this mean? I can’t think of a good way to test this without mocking,
but I don’t want to use a mock that breaks a rule, so in this case, I think my
test is telling me my design needs improvement. I would choose to fix this by
promoting the display object to the public interface. That lets me inject a mock
in my test without breaking a rule:

```
class Greeter(object):
  def __init__(self, display):
    self.display = display

  def greet(self, name):
    self.display.write("Hello %s" % name)
```

Now my test can still mock the display, but it does it by being a normal
consumer of the object, just the same as any production code:

```
def test_greet_says_hello_to_name(self, stdout):
  display = Mock()
  greeter = Greeter(display)
  greeter.greet("Justin")
  display.write.assert_called_once_with("Hello Justin")
```

Now it is clear that if I change how I print to a display, I will need to update
any consumer of the greeter object (including the tests), because the display
object is part of the public interface. The rules have led me to a better design
that uses the Dependency Inversion Principle.

Only letting my tests use the public interface of my units has given the unit
more flexibility outside of my tests, and made it clear to me what is and is not
a safe refactoring.

