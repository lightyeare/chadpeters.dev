---
title: Global vs Local time
publishDate: 17 April 2025
description: Overview of date and times on a global and local scale.  
slug: dates-times-timezones-offsets
---

No matter where you are on the globe, we can all agree that we are experiencing this moment in time together. Now that moment passed, we are now in this moment, and another is coming. The sun might be rising for some, high overhead for others, just setting for others or even the middle of the night, but we all share each moment globally. 

What if we want to refer to a specific moment that has passed or that will be in the future? We need a global time standard to serve as a reference point that we can use to talk about dates and times. That global time standard in modern times is a 24-hour Coordinated Universal Time (UTC). As I write this, the UTC is Thursday, April 17 2025 at 3:02 PM. Using this as a reference point, you can think back to what you were specifically doing on Thursday, April 17 2025 at 3:02 PM UTC. It is a global reference point in time for all of us. 

However, as I sit here in Central PA I've recently finished my mid-morning cup of coffee and lunch time is still an hour away. Why is UTC 3:02 PM?

UTC is equivalent to Greenwich Mean Time (GMT), which is the [mean solar time] (https://www.britannica.com/science/mean-solar-time) at the Royal Observatory in Greenwich, London. So it's 3:02 PM in London but my clock says it is 11:02 AM in Central PA. This difference is because we've found it easier for us to arrange our time according to the movement of the sun across the sky. This led to the creation of Time Zones. Time zones are geographic regions where everyone in that region has the same local time.

When we think about time, we have to think about a _global_ moment, or instant, in time and our _local_ time. You might be a person who lives, works, and plays in one time zone and so does everyone else you know. After all, time zones can encompass pretty large geographic areas. If that is you, you don't need to think about global instants all that much. But if you are a person who lives near the edge of two time zones and often crosses them, you'll be thinking about global and local time quite a bit. 

How are local times related to a global time? Since UTC is constant, local times are determined by an Offset to UTC. My local date and time can be expressed "globally" as Thursday, April 17 2025, 11:02 AM UTC-4֫. -4 is the Offset in hours from UTC. No matter when you are reading this, you can determine what your local time was as I was writing this. You can use your offset on April 17 2025 in the time zone you were in and my offset of -4 to determine your local time. 

> Offsets can be expressed in different ways. You might also see GMT-0400. This allows us to express offsets that aren't on the hour. There are offsets at the half and three-quarter hour. Offsets range from -1800 to 1800.

For instance, if you were in Serbia in Europe on April 17 2025, your offset would have been UTC+2. To convert my local time of 11:02 AM UTC-4 to the global UTC instant, subtract -4 hours from 11:02 AM. This would bring you to 3:02 PM UTC (or UTC+0 as it is sometimes expressed). Then to convert from the global UTC instant to Serbia local time, add +2 hours. The local time in Serbia would have been 5:02 PM when the local time in Central PA was 11:02 AM on April 17 2025. 
Does that mean I can add 6 hours to my local time in Central PA if I want to know the local time in Serbia any time of the year? No, I cannot. This is where it gets tricky. Timezones and Offsets have a many to many relationship. At any given time, offset are shared by more than one time zone. A time zone can also have more than one offset in any given year. Offsets are set by the laws of the governments in a time zone. This means the offsets could change at any time. Also keep in mind that I'm not saying that one government controls the offset in a whole time zone. Rather, governments in the same time zone can, and do, set the offset for their jurisdiction as it suits them.

If we look at the continental United States, we have four generalized time zones - Pacific, Mountain, Central and Eastern. Each of those time zones have two offsets depending on the time of year - one for Standard Time and one for Daylight Savings Time (DST). This creates eight actual time zones in the continental United States which change depending on the time of year. That does not mean that only four of the time zones are "active" at one time. Arizona follows Mountain Standard Time all year long. So there are five active time zones in the Continental US during DST and four during Standard Time. To keep us on our toes, the Navajo Nation in Arizona does follow the DST schedule.

To help to bring this into focus, let's look at two specific global instants in time as a computer might express them - 2025-11-02T05:30:00Z and 2025-11-02T06:30:00Z. These can be more clearly written as November 02 2025 5:30 AM UTC+0 and November 02 2025 6:30 AM UTC+0. What are the local times in the different time zones in the Continental US for these instants?

| Global Instant | Time Zone | Local Time | Offset | 
| :-------- | :------- | :------- | :------- |
| 2025-11-02T05:30:00Z | Los Angeles (Pacific Daylight Time) | 11/01/2025 23:30:00 | -07 |
| 2025-11-02T06:30:00Z | Los Angeles (Pacific Daylight Time) | 11/01/2025 23:30:00 | -07 |
| 2025-11-02T05:30:00Z | Denver (Mountain Daylight Time) | 11/01/2025 23:30:00 | -06 |
| 2025-11-02T06:30:00Z | Denver (Mountain Daylight Time) | 11/02/2025 00:30:00 | -06 |
| 2025-11-02T05:30:00Z | Chicago (Central Daylight Time) | 11/02/2025 00:30:00 | -05 |
| 2025-11-02T06:30:00Z | Chicago (Central Daylight Time) | 11/02/2025 01:30:00 | -05 |
| 2025-11-02T05:30:00Z | NY (Eastern Daylight Time) | 11/02/2025 01:30:00 | -04 |
| 2025-11-02T06:30:00Z | NY (Eastern Standard Time) | 11/02/2025 01:30:00 | -05 |

What are some things that we can observe? Even though at the global UTC instants it is November 2nd, in Los Angeles it is still November 1st. Moving to Denver in Mountain Time, we see the change to the new calendar day take place between the global times of 5:30 AM UTC+0 and 6:30 AM UTC+0 which is Denver local time 11:30 PM on November 1 and 12:30 AM on November 2. Chicago looks like we would expect with the local times being one hour ahead of Denver, and they each have the same offset.

Then we hit New York. Both global times of 5:30 AM UTC+0 and 6:30 AM UTC+0 are 1:30 AM in New York local time. This is because at 2 AM New York local time, New York will change from Eastern Daylight Time (EDT) to Eastern Standard Time (EST). The clocks turn back one hour, and 1:30 AM will occur twice that day. If you live in Chicago and I said "An event will happen on November 2nd, 2025 at 1:30 AM in New York" and you wanted to translate that to your Chicago local time so you could set an alarm, you would need to ask "Which 1:30 AM are you referring to?" or "What will the offset be at that 1:30 AM?" If it is the EDT offset of UTC-4 (the first 1:30 AM), then you would know to set your alarm for 12:30 AM local time. The offset for Chicago at that time is UTC-5. If the EDT offset was UTC-5 (the 2nd 1:30 AM), then you would set your alarm for 1:30 AM local time.

Notice that for an hour on November 2nd, Chicago and New York will have the same local time even though they are in different time zones. The change from DST to Standard Time occurs at 2 AM in each time zone's local time. Chicago and New York will share the same offset for an hour before it becomes 2 AM local time in Chicago. At that time Chicago will change from Central Daylight Time to Central Standard time. Chicago local time will again be one hour behind New York.  
The transitions between Standard and DST happen in the middle of the night when most of us our sleeping. Personally, never in the many years I've been alive have I had to ask "Which 1:30 AM are you referring to?". I also live about 600 miles from the next time zone. In my personal life, I rarely have to consider time zones.

My professional life is a different story. I work with people in different time zones in the US and even people on the other side of the globe. Even more relevant, we are starting a project  around appointments.

Imagine if you lived in the Eastern time zone a few miles from the Central time zone. You have a friend who lives in the Central time zone, and you want to meet your friend for coffee at 10 AM. Obviously, you will need to agree on which 10 AM to meet. 10 AM in Central time zone (which is 11 AM in the Eastern time zone), or 10 AM in Eastern time zone (9 AM in the Central time zone). This is a straightforward example that many people deal with on a daily basis.
What if I wanted to store date and times in a way that people who might be in different time zones could see the appointment times in their local time? For example, assuming today is October 9, 2025, how would I store an appointment on November 12, 2025 11:00:00 AM for a user in the Eastern time zone? How would that appointment need to be stored so that it would show in the correct local time for a user in the Central time zone?
I couldn't store 2025-11-12 11:00:00. That gives no information about how I could arrive at the local time in any time zone. Is that 11 AM Eastern or 11 AM Central?

I could correct this problem by storing the offset. When asking a computer about the current time, we can get the current offset. I could store 2025-11-12 11:00:00 UTC-4, which is the current offset for Eastern time on October 9th. We can then convert it to any user's local time using their offset, right?
Unfortunately not. Like we've discussed earlier, on November 2, 2025 we change from DST to Standard time. If we were to store the offset of UTC-4 with the appointment, a user in the Eastern time zone checking on their appointment time on November 4th would see the appointment time show as 10 AM because their current offset is now UTC-5. At least they wouldn't be late!

To store appointments so they can be accurately converted to any local time, we need to know what the offset will be at the time of the appointment. With so many rules governing time zones, how can we know what an offset is going to be in the future (or the past) in a particular time zone?
We have libraries that can help us do that. One library that we use is NodaTime. NodaTime keeps tables of all the past, present, and future time zone rules. We can use to these rules make calculations. Their rules are based on the IANA (sometimes referred to as Olson) time zone database. Given a local date and time and the IANA time zone, we can calculate the global instant an appointment will occur in the future (or occurred in the past). If we store this global instant, when we know any user's IANA time zone, we can calculate the appointment in that user's local time.

Problem solved, right? 👎🏻 Remember that I said governments set the offsets for their jurisdictions. They can, and do, change the laws that govern offsets. We can calculate a global instant for a future appointment using the rules that exist right now. However, the appointment time global instant that we stored may no longer be correct if a government changes their offset rules before the appointment time. Granted, this is an edge case, but we need to be aware that this could happen if we are calculating and storing a global instant. If we want to be as close to 100% sure we are always giving the correct local appointment time, we have two options. We can store the local time and IANA time zone and calculate the global instant when needed. Second, we need to recalculate and update the global instant appointment time when rules change.

Hopefully this has been helpful to clarify dates and times on a global and local scale. It has helped me solidify my understanding as we look toward working with scheduling and appointments. Have a great morning/afternoon/evening/night/overnight!



Add in "causal"













