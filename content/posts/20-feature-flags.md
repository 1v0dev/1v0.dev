---
title: "Feature flags"
date: 2023-05-05
mermaid: false
tags:
- Feature flags
- Software architecture
keywords:
  - Feature flags
  - Software architecture
---

Feature flags (or feature toggles) are an approach in software development used to enable or disable features and code within the application without the need for a new deployment. They are typically implemented as a simple conditional `if-else` statements but various tools exit to support managing the flags.

<!--more-->

Here is a very simple example:
```java
boolean myFeatureFlag = FeatureFlag.isEnabled("my_feature_flag");

if (myFeatureFlag) {
    // Run code for feature enabled
} else {
    // Run code for feature disabled
}
```

# Separating Release and Deploy

One of the greatest benefits of using feature flags is that they allow you to separate the Release and Deploy steps in
the development workflow. By toggling off a flag you can make a portion of the code/functionality of the software inaccessible. Therefore, you can deploy that code to production, minimizing its impact. Then after testing and verification, you can release that functionality to the users without needing to do separate deploy.

# Testing strategies

When you develop using feature flags, the more advanced testing and release strategies become easier to implement.
For example, you can do *A/B testing* by enabling the flag for certain users or *canary releases* by gradually toggling
the flag while verifying that everything is running smoothly.

# Emergency fixes and incident handling

Another benefit of feature flags is that they can be used to handle errors. If you release a new feature that has 
a major bug you can just toggle off the flag of that feature, limiting the reach of the incident until a proper fix can
be deployed. Since you don't need a separate build and deploy, an immediate action can be taken.

# Technical dept

Technical dept need to be carefully managed when using feature flags. Maintenance and review of the flags
have to happen periodically and old unused flags removed. Otherwise, there is a risk that the application will become a tangled mess of nested `if-else` which is difficult to debug. So once a feature is fully released and the flag is no longer needed, it should be removed to clean up the code.

# Tools

[OpenFeature](https://openfeature.dev/) is an open-source specification providing standard set of properties and APIs to implement feature flags.

Here is a list of third-party management systems for feature flags. They provide various tooling to implement and use the flags as well as tracking and observability. If your software uses a lot of flags, I recommend using one of those systems:
- [LaunchDarkly](https://launchdarkly.com/)
- [Split](https://www.split.io/)
- [FlagSmith](https://flagsmith.com/)
