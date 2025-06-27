---
title: Linq Include...What does it include?
slug: entity-framework-core-linq-include
publishDate: 27 June 2025
description: (Mis)understanding Linq's Include statement
---

One of our ace QA engineers came to me this week with a bug he had found. When an inbound call comes in, we want to match the inbound phone number to a Lead that has a child Phone record that matches that phone number and is marked active. Instead, it was matching to Leads that have a child Phone that matches the phone number but were marked inactive. 

I reviewed the query and at first glance everything looked ok to me:

```csharp
var lead = db.Leads
    .Include(x => x.Phones.Where(phone => phone.Active))
    .Where(lead => lead.Active && lead.Phones.Any(phone => phone.PhoneNumber==inboundPhone))
	.ToList();
```

Since we weren't getting the right behavior, it's clear everything was not ok. I am used to using `Include`, but I wasn't entirely sure the purpose of the `Where` clause in the `Include`. Apparently it doesn't filter out Leads that have Inactive Phones. 

I ran SQL Query Profiler to capture the query that was being generate by this Linq query. The exact query wasn't important, but one thing I noticed was - it was using a LEFT JOIN to join Phones to Leads. 

LEFT JOINs will return all Parent rows regardless of whether there are matching Child rows. After a bit of research, bouncing ideas off of a collegue and one console project using the latest version of Entity Framework Core later, I now understand what is happening with our query. 

When writing LINQ queries, you use `Include` to tell Entity Framework Core to construct a query that will bring back those included child records in the results as part of the Parent. If you don't use `Include`, the collections in your results will be empty. 

For instance, if I write this code: 

```csharp
var leads = db.Leads.Where(x=>x.Active);

foreach (var lead in leads)
{
	foreach (var phone in lead.Phones)
	{
		Console.WriteLine(phone.Active);
	}    
}
```

Nothing will ever be written to the Console because the Phone collection for each Lead doesn't get hydrated. When I change the query to:

```csharp
var leads = db.Leads
	.Where(x=>x.Active)
	.Include(x=>x.Phones);
```

Now it will write out the value of the Active property for each Phone in every Lead. The `Include` tells Entity Framework Core to construct a query that will hydrate the Phone collection for each Lead when returning the results from the DB.

You can also turn on lazy loading in order to hydrate each Phone collection on each Lead. However, in our case EF Core has to execute a query on the DB for each Lead to get it's Phone collection. This could cause performance issues if you had a large number of Leads being returned. When lazy loading is turned on, using `Include` fetches all the necessary data in just one query. 

So what is the purpose of adding a `Where` clause to an `Include`? The `Where` clause in the `Include` filters the child collection. Let's look at our original query, but let's switch the order of the `Include` and the query's `Where` because I think it is more clear. 

```csharp
var lead = db.Leads
    .Where(lead => lead.Active && lead.Phones.Any(phone => phone.PhoneNumber==inboundPhone))
	.Include(x => x.Phones.Where(phone => phone.Active))
	.ToList();
```
This query returns all Leads where the Lead is Active and any of the Lead's Phones match the inboundPhone. For each Phone collection in each Lead that is returned, it will return all Phones that are Active.

Let's look at a sample data set. These are all the Leads joined to their Phones. 

| Lead.FirstName | Lead.Active | Phone.PhoneNumber | Phone.Active | 
| :----------- | :---------- | :---------- | :---------- |
| Bob	| 1	| 5551212 |	1 | 
| Bob	| 1	| 2221313 |	0 | 
| Sally	| 1	| 5551212 |	0 | 
| Bill	| 1	| 3331313 |	1 | 
| Eric	| 1	| 4441313 |	1 | 
| Eric	| 1	| 5551212 |	0 | 
| Andy	| 0	| 5551212 |	1 | 

When I run the query in the Database that gets generate from my Linq, I get these results when I use "5551212" as the inboundPhone:

| Lead.FirstName | Lead.Active | Phone.PhoneNumber | Phone.Active | 
| :----------- | :---------- | :---------- | :---------- |
| Bob | 1 | 5551212 | 1 |
| Sally | 1 | NULL | NULL |
| Eric | 1 | 4441313 | 1 |

Let's compare the results to the sample data set:

- Bob looks correct. The Lead is Active, the Phone number matches the inboundPhone, and that Phone is Active. We aren't returning Bob's Phone row where the PhoneNumber does not match the inboundPhone. 

- Sally is in the results because the Lead is Active and she has a Phone Number that matches the inboundPhone. However, her Phone columns are NULL because the `Where` clause in the `Include` says we only want Phone rows that are Active. Even though her Phone row is Inactive, because the `Include` generates a LEFT JOIN, it is going to return the Lead whether or not there are Phone rows match what is in the `Include` `Where` clause.

- Bill is not included because his Lead doesn't have a Phone that matches the inboundPhone.

- Eric is included because his Lead meets the filter criteria in the `Where` clause for the Lead - his lead is active and he has a Phone number that matches the inboundPhone. The Phone row that gets returned with the Lead is the Phone row that is Active because the `Include` filter that gets applied to the Phone collections says return Active Phone rows. The filter doesn't care what the PhoneNumber is, only that it is Active.

- Andy is not being returned because his Lead is not Active.

We can re-write the query to give us the results that should be returned. 

```csharp
var lead = db.Leads
    .Where(lead => lead.Active && lead.Phones.Any(phone => phone.PhoneNumber==inboundPhone && phone.Active))
	.Include(p => p.Phones)
	.ToList();
```

If we run the query in the DB we get this result:

| Lead.FirstName | Lead.Active | Phone.PhoneNumber | Phone.Active | 
| :----------- | :---------- | :---------- | :---------- |
| Bob | 1 | 5551212 | 1 |
| Bob | 1 | 2221313 | 0 |

Now we can see that only Bob is being returned. He is the only Lead that is Active and has any child Phone rows that are Active and match the inboundPhone. Both of his Phone records will be returned with his Lead because we didn't add any filter to the `Include`. 

If we don't want all of Bob's phone rows to be returned, and instead we only want the rows that are Active and match the inboundPhone, we can change the query to:
 
```csharp
var lead = db.Leads
    .Where(lead => lead.Active && lead.Phones.Any(phone => phone.PhoneNumber==inboundPhone && phone.Active))
	.Include(p => p.Phones.Where(phone => phone.PhoneNumber == inboundPhone && phone.Active));
```

To be clear, we are now telling EF that:
- Filter the Leads to only include Leads that are Active and have at least one Phone that matches the inboundPhone and is Active
- When returning the Phones for each Lead, we only want you to return the Phones that match the inboundPhone and are Active

Another fun thing you can do is use the `Include` to order your child collections.

```csharp
var lead = db.Leads
	.Include(x => x.Phones
		.Where(phone => phone.Active)
		.OrderBy(p => p.PhoneNumber));
```

These Leads will be unordered, but each Phone collection in each Lead will be order by the PhoneNumber.

I'll leave you with one final note. One important thing to remember about Linq `Include` is only use it when you actually need to work with the child collection. Just because you refer to the child collection in the `Where` clause of the query, doesn't mean you need to `Include` that child collection. For instance, if I only wanted the FirstNames of all the Leads that have at least one Active Phone:

```csharp
var lead = db.Leads
    .Where(x=> x.Phones.Any(phone => phone.Active))
    .Select(l => l.FirstName)
    .ToList();	
```

There is no need to add `.Include(x=>x.Phone)` to my query since I am only going to work with the properties on the Lead. 

I hope this has been helpful in better understanding the purpose and usage of the Linq `Include`. 
