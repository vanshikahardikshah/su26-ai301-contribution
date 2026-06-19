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

## Phase II: Reproduce & Plan

### Reproduction Process

#### Environment Setup

I cloned my fork of the Sorbet repository and created a working branch for issue #7399.

Fork: https://github.com/vanshikahardikshah/sorbet
Working branch: https://github.com/vanshikahardikshah/sorbet/tree/fix-issue-7399

During setup, I noticed that Sorbet uses `master` as the default branch instead of `main`, so I switched to `master` before creating my working branch. I also had to authenticate GitHub CLI in the browser before I could successfully publish my branch.

I started by reviewing the repository structure and looking for documentation such as the README, contributing information, and relevant project docs. Since this issue is about improving the error message for misuse of `T.class_of(SomeModule)`, I searched for terms related to the issue, including `T.class_of`, `mixes_in_class_methods`, `ClassMethods`, `Method does not exist`, and error code `7003`.

#### Steps to Reproduce

1. Clone the Sorbet fork:

   `git clone https://github.com/vanshikahardikshah/sorbet.git`

2. Enter the repository:

   `cd sorbet`

3. Switch to the default branch:

   `git checkout master`

4. Create a working branch:

   `git checkout -b fix-issue-7399`

5. Publish the branch:

   `git push origin fix-issue-7399`

6. Create a reproduction Ruby file using the sample code from issue #7399. The file defines `SomeModule`, defines `SomeModule::ClassMethods`, uses `mixes_in_class_methods(ClassMethods)`, and then calls `klass.foo` where `klass` is typed as `T.class_of(SomeModule)`.

7. Run Sorbet on the reproduction file.

8. Expected behavior: Sorbet should give a clearer error message explaining that this may be a misuse of `T.class_of(SomeModule)` when trying to call methods defined through `mixes_in_class_methods`.

9. Actual behavior: Sorbet reports that `foo` does not exist on `T.class_of(SomeModule)`, but the message does not explain the likely reason or suggest a clearer type to use.

### Reproduction Evidence

Issue link: https://github.com/sorbet/sorbet/issues/7399
Working branch: https://github.com/vanshikahardikshah/sorbet/tree/fix-issue-7399

The issue’s observed output shows Sorbet currently reports:

`Method foo does not exist on T.class_of(SomeModule)`

This confirms the current behavior that needs a clearer diagnostic message.

### Solution Approach

#### Understand

The issue is not that Sorbet completely misses the error. Sorbet correctly reports that `foo` does not exist on `T.class_of(SomeModule)`. The problem is that the error message is not helpful enough for this specific situation. When a module uses `mixes_in_class_methods`, a developer may expect methods from `SomeModule::ClassMethods` to be available, but `T.class_of(SomeModule)` does not communicate that correctly.

#### Match

I will look for existing Sorbet diagnostics that add extra context or suggestions to method-missing errors. I will also look for tests related to error code `7003`, method lookup failures, `T.class_of`, and `mixes_in_class_methods` so that my change follows Sorbet’s existing style.

#### Plan

1. Find where Sorbet generates the “Method does not exist” diagnostic for error code `7003`.
2. Trace how Sorbet handles receiver types like `T.class_of(SomeModule)`.
3. Check whether the module has class methods mixed in through `mixes_in_class_methods`.
4. Add a clearer message for this specific case, explaining that the developer may need to use the module’s `ClassMethods` type or a more appropriate combined type.
5. Be careful with the exact suggested type because the issue discussion mentions that the original suggested wording may need adjustment.
6. Add or update a test case using the reproduction code from issue #7399.
7. Run the relevant Sorbet tests and confirm the clearer message appears without breaking existing behavior.

#### Review

Before opening a pull request, I will review Sorbet’s contribution guidelines and keep the change focused. I will make sure the new diagnostic message is clear, consistent with existing Sorbet errors, and supported by a test.

#### Evaluate

I will verify the fix by rerunning the reproduction case and confirming that Sorbet still catches the missing method error, but now gives more useful guidance. I will also run the relevant automated tests for the diagnostic area.
