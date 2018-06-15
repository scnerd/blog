---
layout: post
title: Design for Filecoin Miner
---

# Overview

I've been looking into cost-efficient ways to get into mining Filecoin or Storj, primarily by packing as much storage into as cheap a computer as possible. Here's the basic design I've come up with:

 * Main Components:
   * Main Processor: Pine64 Rock64 4GB ($45)
   * Storage: 4x WD easystore or MyBook 8TB ($169/ea)
 * Additional Components:
   * USB3 Hub (ideally 4 ports, powered may be optional)
   * 12V >=8A DC power supply
   * 12V to 5V DC converter
 
The Rock64 should have its USB3 port run to a 4-port USB3 hub (~$15), which then runs to each of the external drives. Storj, at least, requires 1GB of RAM per node, and each node is capped at 8TB of storage, hence why I
only suggest a 4-port hub instead of just packing as many drives onto the machine as possible. This also strikes some sort of balance between network throughput and storage capacity.

Storj, at least, runs on top of an existing file system, so from here I'd install Ubuntu on the Rock64 and set up a ZFS mount to mash all the drives into a single file system. Storj could then be directed to set up
four nodes within that ZFS directory. Ordinarily, I'd suggest using RaidZ to ensure that data doesn't get lost, but IPFS and Storj already handle data redundancy; you'd basically be throwing away an entire drive of capacity
with the only advantage being that you wouldn't be serving up any *less* data when one of them drives (you'd just always be serving up less data than if not).

Using ZFS has some additional advantages, though;
 it stripes data across drives by default, potentially maximizing data throughput;
 striping the data will also likely balance the storage and retrieval load across the drives more evenly, which should reduce the likelihood of wearing out one substantially before the others,
 and also might prevent them from spinning down.
My personal 5TB drive that I use for media storage spins down to save power quite frequently, and can take 5+ seconds to spin back up,
 which is unacceptable latency on a network where competitive retrieval speed is critical.
Keeping the drives spinning full time on an active node should substantially increase performance, which, at least on Filecoin's architecture, directly correlates to how much you'll earn.

All told, this should permit you to set up a little box that can store and serve up to 32TB of data. The total cost of construction is $600-700, and the power draw of ~100W can be expected to cost <$10/mo.

# Rate of Return

Storj provides a quick article discussing [how much a miner can expect to make](https://blog.storj.io/post/110416906278/how-much-will-i-make-from-driveshare).
Besides the power statistics they offer in that article, I'll also use their estimation that an individual miner can (optimistically, IMO) expect to make $0.006-0.007/GB/mo, assuming their storage is fully utilized.
That seems really optimistic to me, as such a network inherently draws value from having excess storage;
 I expect that there will be substantially more storage capacity than data needing to be stored on the network.
Also, if selling disk space becomes profitable, like Bitcoin, people will jump at the opportunity and optimize their approaches, and the market will become much more competitive.
So let's be a bit more pessimistic and assume we can only make $0.0025/GB/mo that we make available.
This isn't very pessimistic, but let's roll with it for the moment.
By offering up 32TB, we could hope to make $80/mo in revenue.
Assuming the rig takes 100W 24/7, at the US average cost of ~$0.12/KWHr, our overhead is ~$9/mo, which leaves us with a profit of ~$71/mo.
Assuming we can make this much consistently, we can hope to recoup our investment within 9-10 months, and for the next few years while the system keeps working, it's all profit.

One other consideration is response time and throughput, though.
Especially if we set up multiple of these rigs to try to multiply our income, then network bandwidth could become a serious consideration.
Let's say we attempt to alleviate this by upgrading to Gigabit internet.
For me, doing so would cost me an additional ~$50/mo, plus about $100 for a modem that supports Gigabit internet.
Additionally, we'd need a switch with enough gigabit ports to handle all of our mining rigs, which let's estimate runs us another $50.
If we only run a single rig, this drives our profit down to $21/mo with an initial cost of $800-900.
Assuming we could maintain that, it'd likely take us several years just to recoup the costs, by which point it's possible that the hard drives are failing and need replacing; clearly, this wouldn't be sustainable.
However, if we ran 4 rigs, we'd be up at ~$230/mo with an initial cost of $2,500, which would be recouped in about a year.
At 16 rigs, we'd be at at over $1000/mo, and again about a year to recoup our substantial startup costs of ~$10,000.
You'd also have the warm fuzzy feeling of knowing that you have half a petabyte of storage in your home.
As we add more rigs, we at best can hope to get back to about 10 months to recoup costs, but after that first year, our profit becomes substantial... not enormous, but enough to be a comfortable addition to any lifestyle.

Of course, all of these numbers are very loose approxmiations.
Actual income will depend heavily on a large number of wildly uncontrollable factors:

 * How much data actually gets stored on your nodes
 * How often that data gets requested
 * How close you are to data requestors
 * How much cryptocurrency people are offering for storage space
 * Environmental factors (outside temperature, fluctuations to power costs, etc.)
 * Relative value of the Storj or Filecoin to more useful currencies, such as BTC or USD

Speaking of which, my expectation is that that the cost of any storage-based coin will trend downward.
In general, I expect it would become more valuable if the performance of the network was worth the extra cost, or if supply became sparse relative to demand (which I consider unlikely).
Likewise, I expect it would become less valauble as supply increases and more people are willing to sell high-quality storage services for less money.
As such, my suggestion would be that, unless you're an early adopter in the first few years where the value might be chaotic, you consider funelling your Filecoin or Storj coin off to something like BTC or ETH unless you plan to spend it.
Especially if the value of the storage coins deteriorates, this will help make sure you get what your services are worth at the time rather than losing the value of your historic services.

# Rethinking Power

You could then plug in each of these into a power strip, but my guess is that doing so would cost a bit more power in the long run than trying to consolidate the power sources, since you're running 5 or 6 individual power bricks.
Large HDD's like this typically take 12V, and the Rock64 takes 5V (as does the USB hub, most likely), which fortunately are very common voltages to work with.
Also, you need to consider overall power draw; the Rock64 asks for a 2.5A power supply at least, so let's plan to have 15W available for it.
The HDD's could take up to 20W to run each.
All told, then, we want at least 75W, probably 100W to give ourselves some headroom.
You can easily get an AC->12V DC power supply that provides this much power (you'll need at least 8A), then there are fairly cheap 12V to 5V DC converters that could draw from this to power the Rock64 (and the hub, if desired).
With some fancy wire splicing, cutting up of the HDD power cables, and some sockets to fit into the Rock64 and hub, you could run the 12V output to the 5V converter and all of the HDD's in parallel, and run the 5V line to the computer.
These power supplies would cost probably an additional extra $30, but if they can shave off 10% of your power usage by being more efficient than the cheap bricks, then you'd be saving ~$2-3/mo, so it should pay for itself in about a year.
Also, if you get overpowered supplies, which aren't that much more expensive generally, you could run multiple rigs off a single power supply, making it even more worthwhile.
You're also saving the cost of the Rock64 power supply, which I didn't add in to its cost, and the cost of the power strips you'd need anyway to plug in so many wall warts.
All told, it's probably pretty close to a wash in the short run, and has the potential to at least save you a bunch of wires running around the room.

# Summary

I haven't actually built one of these rigs yet, so I can't be sure that the whole setup will work.
Also, there are so many factors in profitability that it's hard to tell how much you could actually expect to earn from such a setup.
If storage is actually worth what the Storj article suggested, you could easily pull in several hundred or a few thousand dollars a year; if my estimate was too optimistic, though, you could be out of pocket just as much.
As with any investment (and, unlike the Bitcoin "investors" you hear about, this really is a way to invest in the storage cryptocurrency movement), there's no guarantee of performance, just opportunity.
