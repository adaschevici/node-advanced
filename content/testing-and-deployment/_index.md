+++
title = "Testing and Deployment"
date = 2021-01-26T21:11:04+01:00
draft = true
pre = "<b>4. </b>"
+++

## DevOps

The term DevOps is loosely used and it can have multiple semantics depending on the context. The way we will use it
throughout the course and what it refers to in the development lifecycle is a mindset of having a __robust pipeline for
features development__.

The pipeline relies on tight feedback loops all throughout the different product teams. Imagine if one of the product
features is retired, the benefit of the teams being able to function autonomously is that the delivery is not affected
one bit.

#### In practice

{{< mermaid >}}
graph LR
    subgraph project
        id1(team 1)-->id2(team2)
        id1(team 1)-->id1(team 1)
        id2(team 2)-->id3(team3)
        id2(team 2)-->id2(team 2)
        id3(team 3)-->id3(team 3)
    end
{{< /mermaid >}}

The team feedback loop is the automated test suite. This allows you to get feedback on changes as you
write your code. 

The tight feedback loop at the feature team layer helps with rapid turnaround and faster onboarding for new devs. Standing up a
production environment should be as easy as pushing a button(in dev speak running a script). Having these tighter
feedback loops also allows you to have a clear understanding of the definition of done, both in terms of compliance with
the __build should be green__ mantra and the visual feedback from our design system set up.

#### Testing
As far as functional testing is concerned it can be done partly in storybook to check that callbacks and event handlers
are wired in correctly, however it will require a fair amount of stubbing and mocking. The boundaries between our
features should not be crossed while writing unittests in order to not introduce change coupling.

{{< lazy-image image="test-pyramid.png" lightbox=false />}}

We should focus on having __as many unittests as possible__ for the components, but we need to also be pragmatic about it
and not allow the mocks and stubs ecosystem blow out of proportion. We want to keep the cost of unittests to a minimum
and having to maintain a mocks system can become a separate project over time.

We also want to measure test coverage and periodically delete tests that don't decrease coverage. __If you have tests
that don't add coverage, those should not exist__

