---
title: "Architecture Decision Records"
date: 2023-01-16
mermaid: true
cover: /posts/images/12-adr-cover.jpeg
tags:
- Architecture
- Documenation
- ADR
---

# Overview

An Architecture Decision Record (ADR) is a short text document that describes a significant desiccation the development team made about the structure or architecture of the software product they are building or planning to build. Each ADR contains information about the context, the decision made and its consequences as well as its current status.

ADRs are a standard practice across the software industry. [ThoughtWorks](https://www.thoughtworks.com/) lists architectural decision records as “adopt” in their technology radar series. New templates, variations and methodologies are constantly emerging as teams refine the approach.

# Benefits

ADRs offer significant benefits:
 - Support better team alignment on the chosen architecture
 - Ease the onboarding on feature team members
 - Outline strategic direction of the product
 - Push the team to create better documentation and keep it up to date

Not using Architecture Decision Records give rise to several **anti-patterns**:
 - No decision is made because teams are afraid of making the wrong choice
 - A decision is made but without context and the team does not understand fully why
 - A decision is made and quickly forgotten about so in the future its not clear why

# Cons

Since ADRs are just text documents they are hard to analyse automatically. They are made with the development team perspective in mind which means they are difficult to understand for non-technical stakeholders. Since there is no one default template or tooling to use, they are complicated to write for novices.

# Process

{{< mermaid >}}
flowchart TD
start[Decision is identified ] --> create[Owner create ADR] --> submit[Submit for review] --> review{Team review}
review -- Yes --> accept[Accepted]
review -- No --> reject[Rejected] -- Improve --> submit
{{< /mermaid >}}

The process for creating ADRs is similar the standard process of issue/story tracking. They are created by an 'owner', then they are reviewed and approved by the whole team.

Everyone should be able to create an ADR but once he does, he becomes the owner and should maintain and communicate the content.

Once a team accepts an ADR, it is **immutable**. If any changes are needed, a new ADR should be created. 

# Best practices

**Have regular review meetings** The process of creating ADR may be intense especially at the beginning of the project. It's recommended to have regular meetings for reviewing them after the daily standup 

**Store ADRs in a central easy accessible location** Every team member should have access to all ADRs. Its good practice to store them in GIT repository or centralised wiki service.

**Start slow** If you have existing project that does not have any ADRs, a good idea is to start creating them about any new decisions and then gradually document the old ones. Creating the whole ADR log at once is a daunting task.

# Sample templates

[Decision record template by Michael Nygard](https://github.com/joelparkerhenderson/architecture-decision-record/blob/main/templates/decision-record-template-by-michael-nygard/index.md)

In each ADR file, write these sections:

```

Title

Status
What is the status, such as proposed, accepted, rejected, deprecated, superseded, etc.?

Context
What is the issue that we're seeing that is motivating this decision or change?

Decision
What is the change that we're proposing and/or doing?

Consequences
What becomes easier or more difficult to do because of this change?
```

For more templates check this GitHub repo:
https://github.com/joelparkerhenderson/architecture-decision-record

# Tools

[A command-line tool for working with a log of Architecture Decision Records](https://github.com/npryce/adr-tools)

# Read more

[AWS ADR guide](https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/welcome.html)

[Homepage of the ADR GitHub organization](https://adr.github.io/)

[Why you should be using architecture decision records to document your project by RedHat](https://www.redhat.com/architect/architecture-decision-records)