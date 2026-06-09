AI301 Open Source Capstone Contribution README

Student Information

Name: Vanshika Shah
Course: AI301 Open Source Capstone Summer 2026
Section: 1C
Personal Member ID: 154827

⸻

Phase I: Issue Selection

Status

Phase I Complete

Selected Issue

Issue link: https://github.com/sorbet/sorbet/issues/7399

Project Fork

Fork link: https://github.com/vanshikahardikshah/sorbet

Problem Summary

This issue focuses on improving the error message shown when T.class_of(SomeModule) is used incorrectly in Sorbet. Right now, Sorbet reports that a method does not exist on T.class_of(SomeModule), but the message does not give enough context about why this happens when methods are defined through mixes_in_class_methods. A successful fix would make the error message more helpful by explaining the likely misuse and guiding developers toward the correct type, such as using the module’s ClassMethods type.

Why I Chose This Issue

I chose this issue because it is specific, beginner-friendly, and marked as a good first issue. It also feels realistic for the AI301 timeline because the goal is not to redesign a major system, but to improve how Sorbet communicates a confusing error to developers. I’m interested in this issue because it will help me practice reading a real open-source codebase, understanding how error messages are generated, and making a small but meaningful improvement to developer experience.

Issue Selection Checklist

* I understand the problem: Sorbet’s current error message is technically correct but not helpful enough for this case.
* The scope seems reasonable for 3–4 weeks because it focuses on improving a specific diagnostic message.
* The issue is active and claimable because it is open, unassigned, and has no linked pull request.
* The issue has helpful context through the example input, observed output, expected behavior, and discussion in the comments.
* The project has clear open-source structure and contributor documentation that I can use during Phase II.

Issue Comment

I commented on the GitHub issue to express interest in working on it and asked for pointers about relevant files, tests, or examples related to T.class_of(SomeModule) and mixes_in_class_methods.

Notes for Phase II

In Phase II, I will set up the Sorbet project locally, reproduce the current error message behavior, identify where this diagnostic is generated, and draft a solution plan before making code changes.
