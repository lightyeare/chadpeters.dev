---
title: LINQ's OrderBy Distinct Gotcha
publishDate: 10 September 2024
description: LINQ OrderBy and then Distinct won't give you the results you are expecting.
slug: linq-orderby-distinct
---

_This post was originally published on the [RIMdev Blog](https://rimdev.io/distinct-orderby-gotcha)_

> Update: Originally published in 2016 but still relevant as I ran across the issue again recently with a current version of .NET. They aren't likely to change the implementation of Distinct, are they? 😏

The boss called me over the other day to consult on an issue we were having with a result set not sorting as expected. We quickly found the code producing the results and it was relatively straight forward:

```csharp
var query = (from plan in _context.PlanTable
             from benefit in plan.BenefitTable
             where plan.PlanId == request.PlanId
             select new PlanBenefitsResult
             {
                Id = benefit.BenefitId,
                PlanId = plan.PlanId,
                Benefit = benefit.Benefit,
                SentencesSortOrder = benefit.SentencesSortOrder,
             })
             .OrderBy(x => x.SentencesSortOrder)
             .Distinct();
```

### The Problem

We dropped a break point on the code and examined the results. `SentencesSortOrder` values of 0 were displaying at the end of the result set! I examined the data in the database and the result set was in the same order as the records in the database. I added an `Order By SentencesSortOrder` to the query and, as expected, values of 0 were now first in the SQL query result.

We briefly threw around a few theories about why our results in the code were not being ordered, and the boss wondered aloud if Distinct() was messing with the OrderBy. A quick google search confirmed that theory. 

In short, the expected behavior of the IQueryable Distinct operator is that it returns an _unordered_ sequence of unique items. When implemented in LinqToSql, this means that OrderBy will be ignored when it is followed by Distinct because it can't guarantee that your results will still be ordered as you specified.

### The Solution

The solution then is simple:

```csharp
var query = (from plan in _context.PlanTable
             from benefit in plan.BenefitTable
             where plan.PlanId == request.PlanId
             select new PlanBenefitsResult
             {
                Id = benefit.BenefitId,
                PlanId = plan.PlanId,
                Benefit = benefit.Benefit,
                SentencesSortOrder = benefit.SentencesSortOrder,
             })
             .Distinct() //Distinct must preceed OrderBy
             .OrderBy(x => x.SentencesSortOrder);
```

## Conclusion

Be careful when using `Distinct()` and `OrderBy()` on IQueryables. If you don't specify them in the correct order you may not get the results you expect.

_This post was originally published on the [RIMdev Blog](https://rimdev.io/distinct-orderby-gotcha)_