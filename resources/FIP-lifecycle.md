---
tags: FIP/RFC, Fairdrive
---

# Fairdrive Improvement Proposal Lifecycle

```mermaid
graph TD


subgraph Idea phase
    -1(idea)

    0(confirmed idea)
end

subgraph Acceptance phase
    a(DRAFT )

    b(DISCUSSION)

    c(POSTPONED)

    d(FINAL COMMENT PERIOD)

    e(CLOSED)
end

subgraph Active phase
    f(ACTIVE)

    g(IMPLEMENTED)

    h(DROPPED)

    i(SUPERSEDED)
end


-1 -- preliminary research --> 0

0 -- commit, pull request -->a

a -- sub-team confirms for discussion --> b

b -- motion for FCP --> d

d -- not suitable --> e

d -- currently not relevant --> c

d -- accepted --> f

f -- reference implementation --> g

f -- no longer relevant --> h

g -- no longer relevant --> h

f -- newer available --> i

g -- newer available --> i

d -- needs work --> b

c -- relevant again --> b

```