---
layout: post
title:  "To DRY or not to DRY"
date:   2017-10-16
categories: DRY
---

Suppose you’re writing some code for a zoo, and one of your business rules requires you to write something like this:

```python
if animal == 'dog' or animal == 'wolf':
    canine = True
```

You do some stuff with animal and then later end up writing this:

```
if another_animal == 'dog' or another_animal == 'wolf':
    canine = True
```

There’s some duplication here. You learned the “Don’t Repeat Yourself” (DRY)
principle so you refactor that code to use a `is_canine` function. But what
triggered this instinct to DRY things up? Well, you noticed some duplication.
What was duplicated? Well, the code I guess?

Now suppose you’re working on the zoo’s web app and you have a request handler
for creating animal records. It accepts a request, validates the input, creates
an Animal instance, saves it, and responds with a 201 (if created) or 400 (if
invalid).

Later you add another request handler for creating tickets. It accepts a
request, validates the input, creates a Ticket instance, saves it, and responds
with a 201 (if created) or 400 (if invalid). The code for this looks almost
identical to the animal request handler. This triggers your DRY instinct again,
so you make a `create_resource` function that accepts request data and the
resource class

Now it’s so easy to add new handlers that create other types of resources!
Until a new requirement comes in: you must check the schedule before creating a
ticket so you don’t overbook the zoo. You decide to accept a `pre_create`
callback in `create_resource` to handle this.

The next requirement is that you must create a FeedingSchedule for each Animal
that is created. No problem, a `post_create` callback can handle that.

But then that has to communicate with the kitchen’s API, which is slow,
requiring the animal request to run asynchronously. Maybe an async=True flag?
Oh, and tickets shouldn’t actually return a 400 status if the input is bad,
they should create an in-progress ticket, and…

Your `create_resource` function now has a pile of parameters, flags, and
callbacks. It’s hard to use and even harder to change. You look back to figure
out how this happened: You DRYed up some duplicated code, but then the
requirements kept changing and your beautiful and simple abstraction got gross.

How can you avoid this? The key is to know when to DRY your code. It’s not
about duplicated code, it’s about duplicated knowledge. For example, when you
wrote `is_canine`, you created one source of truth for the knowledge of what
makes something a canine, which seems reasonable. But when you created
`create_resource`, you unintentionally created one source of truth for the
knowledge of how every resource gets created, and not all resources are created
the same way.

You will have much greater success with the DRY principle if you stop thinking
in terms of reuse, and start thinking in terms of change. Abstractions couple
logic, and you should only couple things that change for the same reason. The
logic for creating an animal and creating a ticket changed for different
reasons, and those changes were painful because their logic was coupled.

Remember, when deciding if you should apply DRY, it’s not about saving future
keystrokes, it’s about keeping one source of truth. It’s not about reuse, it’s
about change (like so much in programming).

It’s not about avoiding duplicated code, it’s about avoiding duplicated
knowledge
