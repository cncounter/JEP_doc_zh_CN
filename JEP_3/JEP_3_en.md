# JEP 3: JDK Release Process

| Owner      | Mark Reinhold                                               |
| ---------- | ----------------------------------------------------------- |
| Type       | Process                                                     |
| Scope      | JDK                                                         |
| Status     | Active                                                      |
| Discussion | jdk dash dev at openjdk dot java dot net                    |
| Created    | 2018/06/19 16:58                                            |
| Updated    | 2022/06/09 17:45                                            |
| Issue      | [8205352](https://bugs.openjdk.java.net/browse/JDK-8205352) |

## Summary

Define the process by which contributors in the OpenJDK Community produce time-based, rapid-cadence JDK feature releases.

## Quick reference

This table is provided here for easy access; the terminology it uses is defined below.

> |           | Candidates                                            | Fix                                                          | Drop                     | Defer                                                        | Enhance?                                                     |
> | --------: | :---------------------------------------------------- | :----------------------------------------------------------- | :----------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
> | **RDP 1** | [Current P1–P3 Targeted P1–P3](http://j.mp/jdk-rdp-1) | Current P1–P3 Targeted P1–P3 if time P1–P5 doc/test bugs     | All P4–P5 Targeted P1–P3 | Current P1–P2,  with [approval](https://openjdk.org/jeps/3#Bug-Deferral-Process) | With [approval](https://openjdk.org/jeps/3#Late-Enhancement-Request-Process) |
> | **RDP 2** | [Current P1–P2 Targeted P1–P2](http://j.mp/jdk-rdp-2) | Current P1–P2,  with [approval](https://openjdk.org/jeps/3#Fix-Request-Process) P1–P5 doc/test bugs | All P3–P5 Targeted P1–P2 | Current P1–P2,  with [approval](https://openjdk.org/jeps/3#Bug-Deferral-Process) | With [approval](https://openjdk.org/jeps/3#Late-Enhancement-Request-Process) |
> |    **RC** | [Current P1 Targeted P1](http://j.mp/jdk-rc)          | Current P1,  with [approval](https://openjdk.org/jeps/3#Fix-Request-Process) | All P2–P5 Targeted P1    | Current P1,  with [approval](https://openjdk.org/jeps/3#Bug-Deferral-Process) | No                                                           |

## Overview

Ongoing JDK development takes place in the main-line repository of the [JDK Project](http://openjdk.java.net/projects/jdk), [`github.com/openjdk/jdk`](https://github.com/openjdk/jdk). This repository is always open for new work.

Every six months, in June and December, we initiate the release cycle for the next JDK feature release, hereinafter referred to as JDK $N. We fork the main-line repository into a *stabilization repository*, `jdk/jdk$N`, and use that repository for the remaining work needed to stabilize the release. That work proceeds over the next three months in three phases, described below:

- Rampdown Phase One [(RDP 1)](https://openjdk.org/jeps/3#rdp-1)[ [candidate bugs](http://j.mp/jdk-rdp-1) ]
- Rampdown Phase Two [(RDP 2)](https://openjdk.org/jeps/3#rdp-2)[ [candidate bugs](http://j.mp/jdk-rdp-2) ]
- Release-Candidate Phase [(RC)](https://openjdk.org/jeps/3#rc)[ [candidate bugs](http://j.mp/jdk-rc) ]

The durations of the phases can vary from release to release, but as an example the phases for [JDK 11](http://openjdk.java.net/projects/jdk/11) are four weeks for RDP 1, three weeks for RDP 2, and five weeks for RC.

Each successive phase narrows the set of bugs that we examine, and subjects actions taken on those bugs to an increasingly-higher level of review. This ensures that, in each phase, we fix the bugs that need to be fixed at that time. It also ensures that we understand why we’re not fixing some bugs that perhaps ought to be fixed but, for good reason, are better left to a future release. The phases thus make use of two approval processes, also described below:

- [Bug-Deferral Process](https://openjdk.org/jeps/3#Bug-Deferral-Process)[ [pending requests](http://j.mp/jdk-defer-pending) ]
- [Fix-Request Process](https://openjdk.org/jeps/3#Fix-Request-Process)[ [pending requests](http://j.mp/jdk-fix-pending) ]

The overall feature set is frozen at RDP 1. No further JEPs will be targeted to the release after that point.

Late, low-risk enhancements that add small bits of missing functionality or improve usability are permitted with approval in RDP 1 and RDP 2, especially when justified by developer feedback or JCP EG support, but the bar is very high in RDP 1 and extraordinarily high in RDP 2. You can request approval for a late enhancement via a third process:

- [Late-Enhancement Request Process](https://openjdk.org/jeps/3#Late-Enhancement-Request-Process)[ [pending requests](http://j.mp/jdk-enhancement-pending) ]

## Candidate bugs

Each phase is driven by a list of *candidate bugs*. The candidate bugs in each phase are at or above that phase’s *priority threshold*, which starts at P3 for RDP 1 and then increases to P2 for RDP 2 and P1 for RC. Each candidate bug is either

- A *current bug*, discovered in an early-access build of JDK $N and reported against that release via the *Affects Version* field, or
- A *targeted bug*, reported against some past release but targeted, via the *Fix Version* field, to JDK $N.

A *critical* bug is a current bug whose priority is either P1 or P2 (in RDP 1 and RDP 2) or P1 (in RC).

Queries for the candidate bugs for each phase are defined in JBS. To summarize:

> |           | Priority | Critical | Query                                   |
> | --------: | :------- | :------- | :-------------------------------------- |
> | **RDP 1** | ≥ P3     | ≥ P2     | [j.mp/jdk-rdp-1](http://j.mp/jdk-rdp-1) |
> | **RDP 2** | ≥ P2     | ≥ P2     | [j.mp/jdk-rdp-2](http://j.mp/jdk-rdp-2) |
> |    **RC** | = P1     | = P1     | [j.mp/jdk-rc](http://j.mp/jdk-rc)       |

## Actions in a phase

In each phase we aim to *fix*, *drop*, or *defer* each candidate bug. If you’re responsible for a candidate bug then please take one of the following actions:

- **Fix** If the bug is current then develop a fix and either integrate it when ready (in RDP 1) or request approval to integrate it via the [fix-request process](https://openjdk.org/jeps/3#Fix-Request-Process) (RDP 2 and RC). In RDP 1, if the bug is targeted and if time permits, develop and integrate a fix when ready.
- **Drop** If the bug is targeted, but not critical, then drop it from the release by either
  - Clearing the *Fix Version* field, or
  - Setting the *Fix Version* field to $N + 1 if you’re reasonably confident that you’ll fix the bug in the next release, or
  - Setting the *Fix Version* field to `tbd` if you’ve determined that the fix will not make the next release but might make some later release.
- **Defer** If the bug is critical but cannot be fixed in time, or is too risky to fix, then request approval to defer the bug from the release via the [bug-deferral process](https://openjdk.org/jeps/3#Bug-Deferral-Process).

In any case, do not change the priority of a bug in order to remove it from the candidate list. The priority of a bug should reflect the importance of fixing it independent of any particular release, as has been standard practice for the JDK for many years.

### Non-candidate bugs

If you’re responsible for a non-candidate bug that’s targeted to JDK $N via the *Fix Version* field then please drop it by either clearing that field, or setting it to $N + 1, or setting it to `tbd`, as above. There’s no need to defer such bugs via the deferral process.

### Test and documentation bugs

Bugs of any priority whose fixes only affect tests, or test-problem lists, or documentation may be fixed in RDP 1 and RDP 2. If you have a fix for such a bug then you don’t need to request approval in order to integrate it, but please do make sure that the issue has a [`noreg-self` or `noreg-doc` label](http://openjdk.java.net/guide/changePlanning.html#noreg), as appropriate.

## Bug-Deferral Process

This process applies from [RDP 1](https://openjdk.org/jeps/3#rdp-1) until the end of the release.

### Requesting a deferral

If you own a bug that will not be fixed in the current phase of development then you can request a deferral as follows: Update the JBS issue to add a comment whose first line is “Deferral Request”. In that comment briefly describe the reason for the deferral (*e.g.*, insufficient time, complexity or risk of fix, *etc.*). Add the label `jdk$N-defer-request` to the issue, substituting the actual release version number for `$N`.

Deferrals will not be granted for TCK issues identified by the label `tck-red-$N`, except possibly when new TCK tests are involved. Deferrals are unlikely for bugs that prevent release testing.

### Reviewing deferral requests

The [Area Leads, relevant Group Leads, and the JDK Project Lead](http://openjdk.java.net/projects/jdk/leads) will review the [pending deferral requests](http://j.mp/jdk-defer-pending) on a regular basis, several times per week. One of them will take one of the following actions:

- Approve the request by adding the label `jdk$N-defer-yes`.
- Reject the request by adding the label `jdk$N-defer-no`, along with a comment describing the reason for this action.
- Request more information by adding the label `jdk$N-defer-nmi` (“`nmi`” = “needs more information”), along with a comment describing what information is requested.
- In any case, **do not** remove the `jdk$N-defer-request` label.

JBS query for pending requests: [`j.mp/jdk-defer-pending`](http://j.mp/jdk-defer-pending)

### Responding to actions taken on your deferral request

- If you’re asked to provide more information for a deferral request then please do so in a new comment in the issue, and then remove the `jdk$N-defer-nmi` label so that we see that it’s ready for re-review.
- If your request is approved then no further action on your part is required.

## Fix-Request Process

This process applies from [RDP 2](https://openjdk.org/jeps/3#rdp-2) until the end of the release.

### Requesting approval to integrate a fix

Before you spend too much time on a fix for a P1 or P2 bug, seek advice from a Group or Area Lead, on an appropriate mailing list, to make sure that fixing the bug in this release is actually a reasonable idea.

When you are nearly ready with a fix then update the JBS issue to add a comment whose first line is “Fix Request”. In that comment briefly describe why it’s important to fix this bug, explain the nature of the fix, estimate its risk, describe its test coverage, and indicate who has reviewed it. If you have a webrev for the fix then include a link to that in the comment; otherwise, attach the patch for the fix to the JBS issue. Add the label `jdk$N-fix-request` to the issue, substituting the actual release version number for `$N`.

### Reviewing fix requests

The [Area Leads, relevant Group Leads, and the JDK Project Lead](http://openjdk.java.net/projects/jdk/leads) will review the [pending fix requests](http://j.mp/jdk-fix-pending) on a regular basis, at least weekly to start and more frequently as we approach the GA date. In case of an urgent situation you are welcome to contact an appropriate reviewer directly in order to solicit a prompt review.

A reviewer will take one of the following actions:

- Approve the request by adding the label `jdk$N-fix-yes`, along with a comment recording their approval whose first line is “Fix request approved”.
- Reject the request by adding the label `jdk$N-fix-no`, along with a comment describing the reason for this action whose first line is “Fix request rejected”.
- Request more information by adding the label `jdk$N-fix-nmi` (“`nmi`” = “needs more information”), along with a comment describing what information is requested whose first line is “Fix request NMI”.
- In any case, **do not** remove the original `jdk$N-fix-request` label.

JBS query for pending fix requests: [`j.mp/jdk-fix-pending`](http://j.mp/jdk-fix-pending)

### Responding to actions taken on your fix request

- If you’re asked to provide more information for a fix request then please do so in a new comment in the issue, and then remove the `jdk$N-fix-nmi` label so that we see that it’s ready for re-review.
- If your request is approved then proceed to complete the fix and integrate it.
- If your request is rejected then you may appeal that decision to the Project Lead.
- In any case, **do not** remove the original `jdk$N-fix-request` label.

## Late-Enhancement Request Process

This process applies from [RDP 1](https://openjdk.org/jeps/3#rdp-1) until the end of the [RDP 2](https://openjdk.org/jeps/3#rdp-2).

### Requesting approval for a late enhancement

If you wish to integrate an enhancement in RDP 1 or RDP 2 then you can request approval as follows: Update the JBS issue to add a comment whose first line is “Late Enhancement Request”. In that comment describe the risk level, a brief justification that quotes actual developer feedback if possible, and your best estimate of the date by which you’ll integrate it. Add the label `jdk$N-enhancement-request` to the issue, substituting the actual release version number for `$N`.

Enhancements to tests and documentation during RDP 1 and RDP 2 do not require approval, as long as the relevant issues are identified with a [`noreg-self` or `noreg-doc` label](http://openjdk.java.net/guide/changePlanning.html#noreg), as appropriate.

### Reviewing enhancement requests

The JDK Project Lead or a delegate, in case of absence, will review the [pending enhancement requests](http://j.mp/jdk-enhancement-pending) on a regular basis, several times per week. They will take one of the following actions:

- Approve the request by adding the label `jdk$N-enhancement-yes`.
- Reject the request by adding the label `jdk$N-enhancement-no`, along with a comment describing the reason for this action.
- Request more information by adding the label `jdk$N-enhancement-nmi` (“`nmi`” = “needs more information”), along with a comment describing what information is requested.
- In any case, **do not** remove the `jdk$N-enhancement-request` label.

JBS query for pending requests: [`j.mp/jdk-enhancement-pending`](http://j.mp/jdk-enhancement-pending)

### Responding to actions taken on your enhancement request

- If you’re asked to provide more information for an enhancement request then please do so in a new comment in the issue, and then remove the `jdk$N-enhancement-nmi` label so that we see that it’s ready for re-review.
- If your request is approved then update the issue’s due date to the expected completion date.