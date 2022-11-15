---
layout: post
title:  "Solving the Jane Street Mock Interview Problem"
date:   2022-11-14 06:00:01 -0400
categories: Programming
---

Jane Street, the technically innovative trading firm in NYC, posted a software engineering mock interview on YouTube.
The [50 minute video][EngineeringMockInterviewYouTubeURL] problem solved a program to convert units from meters-to-inches
and hours-to-seconds. The problem seemed interesting (and straightforwward enough) so I paused the video at 6:30 right
after the requirements were described and decided to give it a go in Python.

The requirements are for the program to be given a list of "conversion facts" in the form of a tuple. Conversion facts for meters and
feet are:

- `["m", 3.28, "ft"]`
- `["ft", 12, "in"]`

And conversion facts for hours and minutes are:

- `["hr", 60, "min"]`
- `["min", 60, "sec"]`

I thought about the conversion facts as a list of lists, like this:

- `[ ["m", 3.28, "ft"], ["ft", 12, "in"], ["hr", 60, "min"], ["min", 60, "sec"] ]`

With that data structure there appeared to be a recursive nature to the solution, meaning to convert from meters to
inches would require a conversion through feet: 1 meter = (3.28 feet/meter * 12 inches/foot).

There are two other properties of the facts worth noting:

- Each fact has an implied unit of one:
  - 1 meter = 3.28 feet.
  - 1 hour = 60 minutes.
- If we have facts to compute meters-to-inches then we should be able to compute the inverse inches-to-meters.
So given the fact `["ft", 12, "in"]` we can imply an inch is 1/12th of a foot or `["in", 1/12, "ft"]`.

I decided to include the definition of facts in the constructor of a Python class called `UnitConversion`. This approach
avoiding passing a list of facts into every call of a conversion method. The class includes an `addFacts()` method so
classes can easily be created with different lists of facts.

My solution includes a `convert()` method that is called with conversion query like this which asks for the conversion
of 10 meters to inches:

`UnitConversion.convert([10, "m", "in"])`

In support of a recursive approach, it occurred to me that asking to convert any value to the same units, say,
10 inches to inches (ie, `[10, "in", "in"]` in a query) would return the same value; this became the base case
of the recusive solution.

- `UnitConverstion.convert([10, "in", "in"]) = [10, "in"]`

[EngineeringMockInterviewYouTubeURL]: https://www.youtube.com/watch?v=VfbFJISCP3g
