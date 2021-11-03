- FIP: 0
- title: RFC process definition
- author: Črt Ahlin (@crtahlin)
- status: active
- created: 2021-09-09

# Summary

This is a special, genesis FIP, defining the rules and process for all the following FIPs.


# Fairdrive improvement proposals

Some non-substantial changes to Fairdrive protocol can be managed through a GitHub pull request workflow.

Substantial changes must be put through a design process and build a consensus among the Fair Data Society community and sub-teams handling a particular area.

The Fairdrive improvement proposal (FIP) process is intended to provide a path for new specifications and features to be added to the Fairdrive protocol (FDP). This should increase confidence of all the stakeholders of the direction the Fairdrive protocol evolution.


## When to follow the FIP process

The FIP process must be followed with:

- new specifications as part of Fairdrive protocol,
- substantial changes to existing specifications.

Example of substantial change might be:

- adding features or changing interfaces for the Fairdrive protocol.


## Before creating a FIP

Low quality proposals or those for features already rejected or that do not fit into the roadmap, will be rejected. Therefore, before submitting a FIP, pursue feedback from community and stakeholders in general. To go through the process smoothly, work will have to be done towards building a consensus. 

The idea for the FIP could be talked over on the [Fair Data Society Discord](https://discord.gg/KrVTmahcUA), discussing the topic on [GitHub Discussions](https://github.com/fairDataSociety/fds-rfcs/discussions), or even posting "pre-FIPs" on one of the channels.

As a rule of thumb, receiving encouraging feedback from long-standing project developers, and particularly members of the relevant sub-team is a good indication that the FIP is worth pursuing.


## The FIP process

TL;DR: The acceptance of a FIP is achieved by its merge into the FIP repository as a markdown file. At that point the FIP becomes active. 

1. Author: Fork the [FIP repository](https://github.com/fairDataSociety/fds-rfcs/).
2. Author: Copy `0000-template.md` to `text/0000-my-feature.md` (where "my-feature" is descriptive). Don't assign a FIP number yet.
3. Author: Fill in the FIP. Take care to be convincing in the motivation part and  understanding of the proposed design and its impact and drawbacks. 
4. Author: Submit a pull request (PR). 
5. Author: Use the pull request number to change the `0000-`prefix of file and it's internal header to the PR number.
6. Sub-team: The pull request will be assigned to the relevant sub-team to triage it and assign it to a particular member.
7. Author: Before being accepted, you should be prepared to revise the FIP and incorporate feedback from various stakeholders. Build consensus and integrate feedback, try to gain broad support. 
8. Sub-team: The relevant sub-team will use the PR comment thread to discuss or summarize the discussion around the FIP. 
9. Author: You can make edits to the PR, make changes as new commits and leave comments explaining them. Specifically, do not squash or rebase commits after they are visible on the pull request.
10. Sub-team: At some point, a member of the subteam will propose a "motion for final comment period" (FCP), along with a *disposition* for the FIP (merge, close, or postpone).
    - This happens when the sub-team is in the position to make a decision. Consensus among all the stakeholders involved in the feedback is not needed, but strong opposition against the FIP outside of the sub-team should not exist. Sub-team members use their best judgement when to take this step.
    - It the discussion preceding the FCP was lengthy, the motion could be preceded by a "summary comment" of the current state of discussion and major points of disagreement.
    - To enter the FCP, **all** the members of the sub-team must agree.
11. The FCP phase lasts **10** calendar days. It is advertised widely, for all the stakeholders to have a chance to lodge objections. 
12. Sub-team: The FIP can then be either merged, closed, postponed or if substantial new arguments or ideas are raised, the FCP can be canceled and the FIP goes back into discussion. A merged FIP becomes "active".


## The FIP life-cycle

After the FIP becomes active, it might get implemented in a reference implementation, though there is no guarantee of that. 

In general, FIPs should not be substantially changed after becoming active, though minor changes can be submitted as amendments. Substantial changes should be new FIPs, with a note and status of being superseded added to the original FIP. What constitutes a substantial change is up to the discretion of the sub-team. 

The whole process before a FIP becoming active and afterwards is represented by the diagram below.

<img width="769" alt="image" src="https://user-images.githubusercontent.com/1554520/140074959-f2860de5-cccb-45e0-853f-905ff4a40a75.png">


## Reviewing FIPs

When the FIP is in the pull request phase, the sub-team may meet the author and/or relevant stakeholders to discuss the proposal. A summary of the discussions should be posted to the pull request.

Final decisions by the sub-team are made after the benefits and drawbacks are well understood. If the reasoning is not clear from the thread in the PR, the sub-team will add a comment describing the rationale.


## Implementing a FIP

The author of the FIP is not obligated to implement it. Anyone can pick up on an implementation of a FIP, as a reference implementation. If implemented as such, a FIP is added a note and its status changes to implemented.


## FIP postponement

A FIP marked as postponed might not have been relevant at the time of discussion, but might be at a later time. Postponed pull request may be opened when the time is right. Relevant sub-team members should be contacted about that. 


## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
