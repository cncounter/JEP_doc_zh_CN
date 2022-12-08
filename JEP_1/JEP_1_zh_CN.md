# JEP 1: JDK增强提案(JDK Enhancement-Proposal)以及路线流程(Roadmap Process)

| 文档信息  | 值 |
| :------: | :------------------------------------------------------------ |
| 负责人员    | Mark Reinhold                                               |
| 文档类型    | 过程(Process)                                                |
| 设计范围    | JDK                                                         |
| 文档状态    | 使用中(Active)                                               |
| 讨论邮箱    | `discuss@openjdk.java.net`                                  |
| 创建时间    | 2011/06/23 20:00                                            |
| 更新时间    | 2018/03/30 15:46                                            |
| 相关问题    | [JDK-8046185](https://bugs.openjdk.java.net/browse/JDK-8046185) |


> **备注**: 本文档的大部分内容, 已被 [JEP 2.0 Process Proposal](http://cr.openjdk.java.net/~mr/jep/jep-2.0-02.html) 所替代, 使用 JDK bug系统中的自定义 "JEP" 问题类型来进行创建和维护。 详情请参阅 [the proposal](http://cr.openjdk.java.net/~mr/jep/jep-2.0-02.html)。 该提案最终将被纳入本文件。


## 概述

本文档定义了一套流程, 用于收集、审查、分类和记录 JEP 的结果, 包括JDK增强提案, 以及相关的工作, 例如流程和基础设施改进等等。


## Goals

The primary goal of this process is to produce a regularly-updated list of proposals to serve as the long-term Roadmap for JDK Release Projects and related efforts. The Roadmap should extend at least three years into the future so as to allow sufficient time for the most complex proposals to be investigated, defined, and implemented.

A secondary goal of this process is to define a uniform format and a central archive for enhancement proposals so that they are easy for all interested parties to find, read, comment upon, and contribute to. Proposal documents evolve as work upon them progresses so that, in the end, a completed proposal serves as an authoritative, though not necessarily self-contained, record of what was changed and why.

This process is open to every OpenJDK Committer. Decisions about specific proposals will be made in a transparent manner but are ultimately up to the OpenJDK Lead.

This process does not in any way supplant the Java Community Process. The JCP remains the governing body for all standard Java SE APIs and related interfaces. If a proposal accepted into this process intends to revise existing standard interfaces, or to define new ones, then a parallel effort to design, review, and approve those changes must be undertaken in the JCP, either as part of a Maintenance Review of an existing JSR or in the context of a new JSR.

## Overview

An *enhancement* is an effort to design and implement a nontrivial change to the JDK code base or to do some other kind of work whose goals, progress, and result are worth communicating broadly. A JDK Enhancement Proposal (hereinafter "JEP") should be drafted for any work that meets at least one of the following criteria:

- It requires two or more weeks of engineering effort,
- It makes a significant change to the JDK, or to the processes and infrastructure by which it is developed, or
- It is in high demand by developers or customers.

The *JDK Roadmap* is the list of JEPs that have been judged as candidates for current and future JDK Release Projects by the OpenJDK Lead. The JEPs in the Roadmap are ordered according to their dependences upon other JEPs and their suggested start dates, if any.

That a particular JEP appears in the Roadmap means only that it is the proposal of record from a technical perspective. There is no guarantee that anyone will work on it, much less that its end result will appear in any JDK Release Project.

It's up to individual Committers, often in consultation with their employing organizations, to figure out who will work on which JEPs.

It's up to Group Leads, Area Leads, and the OpenJDK Lead to ascertain whether sufficient Committers and other Contributors have signed up to do all of the work required to complete a JEP so that it may considered to be *funded*. This work includes not just design and implementation but also, as appropriate, QA test development, TCK test development, documentation, and any other activities that might be necessary.

If a JEP defines a feature to be integrated into the JDK then, once it is funded, it can be targeted to a specific JDK Release Project by the OpenJDK Lead.

## Making decisions and building consensus

The OpenJDK Lead ultimately decides which JEPs to accept for inclusion into the Roadmap. The JDK is, however, a very large and complex system; no one person can hope to understand every area and component in complete, expert-level detail. The OpenJDK Lead therefore relies upon the demonstrated expertise of Reviewers, Group Leads, and Area Leads when evaluating incoming proposals.

> NOTE: "Area Lead" is not a term defined in the proposed OpenJDK Community Bylaws. The Area Leads are OpenJDK Members who have cross-Group expertise and responsibilities. They are appointed, informally, by the OpenJDK Lead.

Much of the work behind a successful JEP will, then, involve building consensus for the proposal by convincing some Reviewers to review it and some Group or Area Leads to endorse it.

This process does not mandate any particular method for reaching that goal. It is expected, however, that the typical new proposal will start as an idea explored informally and shaken out within a specific Group, then drafted as a JEP for further review and comment, then endorsed by that Group's Lead and later the relevant Area Lead, and then submitted for acceptance by the OpenJDK Lead. Discussions along the way will usually take place in e-mail, but review meetings may be useful for particularly large or contentious proposals. The results of any such meetings should be reported to the JEP's discussion list for the record.

Endorsement of a JEP by a Group or Area Lead is meant to be a reasonably strong statement. It should be considered as equivalent to the statement "I will argue that this JEP should be funded."

When a Reviewer reviews a JEP or when a Group or Area Lead endorses a JEP then that fact is recorded in the JEP itself. One may consider the list of JEPs endorsed by a Group Lead to be the Roadmap for that Group and, likewise, the list of JEPs endorsed by an Area Lead to be the Roadmap for that Area.

## Wacky ideas

This process is explicitly open to aggressive, outside-the-box, and even completely wacky ideas. Such ideas often require significant up-front research, experimentation, and socialization before they're ready to be proposed as enhancements to the JDK itself. The work of refining an idea can be carried out in one or more exploratory research proposals. The end result of a research JEP is not working code in the JDK but rather a documented deeper understanding of the problem being addressed and its solution space, hopefully with enough detail to support a concrete enhancement proposal.

## Process states

A successful JEP passes through the following states:

- Draft --- In circulation by the author for initial review and consensus-building
- Posted --- Entered into the JEP Archive by the author for wider review
- Submitted --- Declared by the author to be ready for evaluation
- Candidate --- Accepted for inclusion in the Roadmap by the OpenJDK Lead
- Funded --- Judged by a Group or Area Lead to be fully funded
- Completed --- Finished and delivered

The author of a proposal can move it forward from Draft to Posted and from Posted to Submitted. The author can also move it backward, from Funded to either Candidate or Posted, and from Candidate to Posted.

The author of a proposal can move it from any state except Completed or Rejected to

- Withdrawn --- Withdrawn by the author; may be re-drafted later

A Group Lead, an Area Lead, or the OpenJDK Lead can move a proposal from Candidate to Funded and from Funded to Completed.

The OpenJDK Lead can move a proposal forward from Submitted to Candidate, and from Submitted, Candidate, or Funded to

- Rejected --- Identified as an idea that's not worth pursuing, or as an idea that's so unlikely to be funded that it's not worth maintaining in the Roadmap

Long-lived Informational and Process JEPs, such as the JEP you are reading right now, can be in one additional state:

- Active -- Approved for publication by the OpenJDK Lead

## Proposal format

JEP documents are written in plain text, using the [Markdown lightweight markup language](http://daringfireball.net/projects/markdown/basics). Their exact format is defined in [JEP 2](https://bugs.openjdk.java.net/browse/JDK-8046186). A sample JEP draft is available [here](http://cr.openjdk.java.net/~mr/jep/draft-mcimadamore-inference-01.md).

Common data about a JEP is written at the top of the file in an RFC-822-like format. Lines preceded with "+" are required in order to submit a proposal. [JEP 2](https://bugs.openjdk.java.net/browse/JDK-8046186) describes the header lines in detail, but here is a summary:

```
+Title: <title>
+Author: <author's full name>
 Organization: <employer name>
+Created: YYYY/MM/DD
+Type: Feature | Research | Infrastructure | Process
 State: <see above>
+Exposure: Open | Closed
+Component: <area>/<component>
+Scope: SE | JDK | Implementation
 JSR: <number, if an active JSR>
 RFE: <number of primary RFE, if any> (<secondary RFE> ...)
+Discussion: <mailing-list address>
 Start: YYYY/QN
 Depends: <draft names or JEP numbers>
 Blocks: <draft names or JEP numbers>
 Effort: XS | S | M | L | XL
 Duration: XS | S | M | L | XL
+Template: 1.0
```

This line is added or amended when a Reviewer is satisfied with a JEP:

```
Reviewed-by: <reviewer's full name>, ...
```

This line is added or amended when a Group or Area Lead endorses a JEP:

```
Endorsed-by: <endorser's full name>, ...
```

This line is added when a proposal is posted:

```
JEP: <number>
```

A Group or Area Lead adds this line when moving a proposal to the Funded state:

```
Funded-by: <org or individual name(s)>
```

When a funded proposal describing a feature intended for a JDK Release Project is targeted to a specific release then the OpenJDK Lead adds this line:

```
Release: <number>
```

## Mechanics

The JEP Archive is published at http://openjdk.java.net/jeps.

To submit a JEP for posting send a completed [template](https://openjdk.org/jeps/2) in e-mail, as a text/plain attachment, to [jep dash submit at openjdk dot java dot net](mailto:jep dash submit at openjdk dot java dot net). An actual human will sanity-check your JEP, allocate a number for it, and post it to the archive.

After your JEP has been posted you can update it yourself by cloning the JEP repository at http://hg.openjdk.java.net/jep/jeps, editing its file, and committing and then pushing your changes. When committing a change to an existing JEP please start the first line of the commit message with the unadorned JEP number followed by a colon. When you push your change a server-side repository hook will update the affected public web pages.

For example, to fix a typo in a hypothetical JEP 42:

```
$ hg clone http://hg.openjdk.java.net/jep/jeps
destination directory: jeps
requesting all changes
adding changesets
adding manifests
adding file changes
added 54 changesets with 60 changes to 23 files
updating to branch default
23 files updated, 0 files merged, 0 files removed, 0 files unresolved
$ cd jeps
$ emacs jep-42.md ...
$ hg ci -m "42: Fix typo"
$ hg push ssh://hg.openjdk.java.net/jep/jeps
searching for changes
remote: adding changesets
remote: adding manifests
remote: adding file changes
remote: added 1 changesets with 1 changes to 1 files
remote: snapshot taken
remote: changes published to http://openjdk.java.net/jeps
remote: notifying jep-changes@openjdk.java.net
$
```

## Acknowledgements

Thanks to Brian Goetz, Paul Hohensee, Georges Saab, Dalibor Topic, and Mikael Vidstedt for comments on drafts of this document. Acknowledgement is also due to the Python community, from whose [PEP process](http://python.org/dev/peps/pep-0001/) many useful ideas were adopted.
