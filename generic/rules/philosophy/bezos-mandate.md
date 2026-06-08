---
id: bezos-mandate
title: The Bezos API Mandate
description: Jeff Bezos's 2002 internal Amazon mandate that forced every team to expose capabilities through service interfaces — and accidentally created AWS. Reconstructed by Steve Yegge in his 2011 Google Platforms Rant.
labels: [architecture, platform, history, apis]
see_also: [agent-accessible-interfaces]
---

# The Bezos API Mandate

## The Mandate (2002)

Jeff Bezos sent an internal memo to Amazon around 2002. Nobody knows the exact original
text — it was reconstructed from memory by Steve Yegge in a 2011 Google+ post that
accidentally went public. The reconstructed mandate:

> 1. All teams will henceforth expose their data and functionality through service interfaces.
> 2. Teams must communicate with each other through these interfaces.
> 3. There will be no other form of interprocess communication allowed: no direct linking,
>    no direct reads of another team's data store, no shared-memory model, no back-doors
>    whatsoever. The only communication allowed is via service interface calls over the network.
> 4. It doesn't matter what technology they use. HTTP, Corba, Pubsub, custom protocols —
>    doesn't matter.
> 5. All service interfaces, without exception, must be designed from the ground up to be
>    externalizable. That is to say, the team must plan and design to be able to expose the
>    interface to developers in the outside world. No exceptions.
> 6. Anyone who doesn't do this will be fired.
> 7. Thank you; have a nice day!

Point 7 was a joke. Point 6 was not.

Bezos assigned a West Point graduate, ex-Army Ranger, former Walmart CIO named Rick Dalzell
to enforce it. Rick used the phrase "hardened interface" a lot. Everyone made forward progress
and made sure Rick knew about it.

## Why It Mattered

The mandate was not about technology. It was about discipline.

Amazon in 2001 was a product company — teams talked to each other through proprietary,
undocumented, internal channels. Direct database reads. Shared memory. Back-doors. This is
the natural state of any fast-moving engineering organization: people optimize for local
velocity, not global composability.

Bezos's insight — which he had before most of his engineers — was that Amazon needed to be
a platform, not a product. And you cannot build a platform from components that were not
designed to be composed.

The enforcement mechanism was blunt: fire people who don't comply. This is not a common
approach. It worked.

## The Accidental Product

The internal service interfaces Amazon built to sell books turned out to be good enough to
rent to other people. AWS was not a strategic initiative — it was a byproduct of an internal
discipline policy. The Elastic Compute Cloud, the Relational Database Service, S3: all of
these emerged from teams that had been forced to treat their infrastructure as a
programmable platform.

You wouldn't really think that an online bookstore needs to be an extensible, programmable
platform. But that's what the mandate produced.

## The Rant (2011)

Steve Yegge worked at Amazon for six years, then Google for six years. In October 2011 he
wrote an internal Google+ post comparing the two companies. He accidentally set it to public.
It went viral within hours.

The core argument: Google is better than Amazon at almost everything — engineering culture,
hiring, infrastructure, tooling. But Amazon beats Google in one dimension: **platform thinking**.

Google builds products. Amazon builds platforms that products run on. This is not a product
decision — it is a cultural one, enforced top-down.

Yegge's diagnosis of why Google+ was failing: it was a product pretending to be a platform.
Facebook succeeded not because Facebook the social network was better than Google+, but
because Facebook built a platform and Facebook the social network was the killer app on it.
You cannot separate them. The killer app is a product. The platform is the moat.

His conclusion: platform thinking requires a specific discipline — you must design every
interface as if external developers will use it, *even when you don't know who those
developers are or how they'll use it*. Google had not internalized this. Amazon had been
forced to.

## The Principles That Remain

**No back-doors.** Bezos's rule 3 is good architecture regardless of era. Direct database
reads, shared memory, and proprietary protocols are not just technical debt — they are
platform debt. They prevent composition.

**Externalizable by default.** Designing an interface "just for internal use" is the same
mistake Amazon teams made before the mandate. The cleanup never happens. Design for the
external consumer from day one, even if the consumer is hypothetical.

**The platform is the product.** Amazon's internal platform became AWS. Facebook's platform
became the moat that let the social network win despite being technically inferior to Google+.
The platform is not a foundation for the product — it *is* the product, eventually.

**Enforcement requires mandate.** Platform thinking does not emerge organically in fast-moving
teams. Local incentives point the other direction: move fast, ship features, use the shortcut.
Someone has to enforce the discipline. Bezos used Rick Dalzell. Your organization will need
its own version of that.

---

*The original Google+ post is gone. It lives on as `chitchcock/1281611` on GitHub Gist and in university distributed systems course materials (e.g. UW CSE 452).*
