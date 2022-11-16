---
layout: post
title:  "Solving the Jane Street Software Engineering Mock Interview Problem"
date:   2022-11-15 06:00:01 -0400
categories: Programming
---

[Jane Street][JaneStreetWebsiteURL], the technically innovative trading firm in NYC, posted a software engineering
mock interview on YouTube. The [50 minute video][EngineeringMockInterviewYouTubeURL] problem solved a program to
convert units from meters-to-inches and hours-to-seconds. The problem caught my interest (and seemed straightforward
enough) so I paused the video at 6:30 right after the requirements were described and decided to give it a go in Python.

The requirements are for the program to be given a list of "conversion facts" in the form of a tuple. Conversion facts for meters and
feet are:

- `["m", 3.28, "ft"]`
- `["ft", 12, "in"]`

And conversion facts for hours and minutes are:

- `["hr", 60, "min"]`
- `["min", 60, "sec"]`

I thought about the conversion facts as a list of lists, like this:

```
[ ["m", 3.28, "ft"], ["ft", 12, "in"],
  ["hr", 60, "min"], ["min", 60, "sec"] ]
```

With that data structure a recursive solution seemed possible, meaning to convert from meters to
inches would require a conversion through feet: 1 meter = (3.28 feet/meter * 12 inches/foot).

There are two other properties about facts worth noting:

- Each fact has an implied unit of one:
  - 1 meter = 3.28 feet.
  - 1 hour = 60 minutes.
- If we have facts to compute meters-to-inches then we should be able to compute the inverse inches-to-meters.
So given the fact `["ft", 12, "in"]` we can imply an inch is 1/12th of a foot or `["in", 1/12, "ft"]` in tuple format.

I decided to include the definition of facts in the constructor of a Python class called `UnitConversion`. This approach
avoided passing a list of facts into every conversion call. The class includes an `addFacts()` method so instances can be
created with different lists of facts as needed.

My solution includes a `convert()` method that is passed a conversion query. To convert 10 meters to inches the method
would be called like this:

```
uc = UnitConversion()
conversionResult = uc.convert([10, "m", "in"])
```

In support of a recursive approach, it occurred to me that asking to convert any value to the same units, say,
10 inches to inches (ie, `[10, "in", "in"]` in a query) would return the same value; this became the base case
for the recursion.

```
uc.convert([10, "in", "in"]) returns [10, "in"]
```

Here is the algorithm for a recursive solution:

```
    def convert(self, query):
        conversionResult = self._computeConversion(self.Facts, query)
        if conversionResult is None:
            conversionResult = self._computeConversion(self.InverseFacts, query)
        return "not convertible!" if conversionResult is None else conversionResult

    def _computeConversion(self, facts, query):
        queryValue = query[0]
        queryUnit = query[1]
        conversionUnit = query[2]
        conversionValue = None

        if queryUnit == conversionUnit:
            return [queryValue, conversionUnit]

        fact = self.getFact(facts, queryUnit)
        if self._isNotDefined(fact):
            return conversionValue
        value = fact[1]
        unit = fact[2]

        conversionValue = self._computeConversion(facts, [queryValue * value, unit, conversionUnit ])

        return conversionValue
```

The `convert()` method first attempts to resolve the query from the facts that were given. If a solution isn't found,
`convert()` then attempts to resolve the query against "inverse facts" which are the facts that are implied from the
given facts. If no solution is found, from either facts or inverse facts, then `"not convertible!"` is returned.

Inverse facts are dynamically computed as a property from a list comprehension like this:

```
    @property
    def InverseFacts(self):
        return [ [fact[2], 1/fact[1], fact[0]] for fact in self.Facts ]
```

A fact such as `["ft", 12, "in"]` would return `["in", 1/12, "ft"]` as an inverse fact.

After I had a working solution I went back and finished watching the YouTube video. The mock interview landed on a
different design based on a dictionary data structure representing a connected graph and a breath first algorithm
(an alternative to the list-of-lists and recusive algorithm in my solution).

The mock interview implemented a solution in approximately 40 minutes (excluding the time to state requirements and
summarize at the end). I worked on the recursive solution for a total of 3-4 hours over two days. My solution
includes 12 unit tests containing over 50 asserts (considering some tests call assert statements dynamically within
a loop to cover all scenarios). Although I could have productively reasoned about a recursive solution in a conversation,
I would have found it very difficult to write anything close to executable code without the feedback of the edit-run-debug
and TDD (Test Driven Development) cycles.

Source code for the recursive solution and unit tests can be [downloaded from GitHub][UnitConversionGitRepoURL].

[JaneStreetWebsiteURL]: https://www.JaneStreet.com
[EngineeringMockInterviewYouTubeURL]: https://www.youtube.com/watch?v=VfbFJISCP3g
[UnitConversionGitRepoURL]: https://github.com/WebMettle/UnitConversion
