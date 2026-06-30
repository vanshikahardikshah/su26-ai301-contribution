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

During setup, I noticed that Sorbet uses `master` as the default branch instead of `main`, so I switched to `master` before creating my working branch. I also had to authenticate GitHub CLI through the browser before I could successfully push my branch.

To build Sorbet locally, I first tried running:

`bundle exec srb tc issue_7399_repro.rb`

That did not work because the repository root did not have a `Gemfile` or `.bundle` directory. I then tried building Sorbet with Bazel, but Bazel was not installed. I installed Bazelisk using Homebrew, confirmed Bazel was available, and then successfully built Sorbet with:

`bazel build //main:sorbet --config=dbg`

After the build completed successfully, I ran the local Sorbet binary on my reproduction file.

#### Steps to Reproduce

1. Clone the Sorbet fork:

   `git clone https://github.com/vanshikahardikshah/sorbet.git`

2. Enter the repository:

   `cd sorbet`

3. Switch to the default branch:

   `git checkout master`

4. Create a working branch:

   `git checkout -b fix-issue-7399`

5. Create a reproduction file named `issue_7399_repro.rb`.

6. Add the sample code from issue #7399. The code defines `SomeModule`, defines `SomeModule::ClassMethods`, uses `mixes_in_class_methods(ClassMethods)`, and then calls `klass.foo` where `klass` is typed as `T.class_of(SomeModule)`.

7. Install Bazelisk if needed:

   `brew install bazelisk`

8. Build Sorbet locally:

   `bazel build //main:sorbet --config=dbg`

9. Run the local Sorbet binary on the reproduction file:

   `./bazel-bin/main/sorbet --silence-dev-message issue_7399_repro.rb`

10. Actual output:

`Method foo does not exist on T.class_of(SomeModule)`

11. Expected behavior:

Sorbet should still report the method-missing error, but it should include clearer context explaining that this may be a misuse of `T.class_of(SomeModule)` when trying to call methods defined through `mixes_in_class_methods`.

### Reproduction Evidence

Issue link: https://github.com/sorbet/sorbet/issues/7399
Working branch: https://github.com/vanshikahardikshah/sorbet/tree/fix-issue-7399
Reproduction file: https://github.com/vanshikahardikshah/sorbet/blob/fix-issue-7399/issue_7399_repro.rb

I successfully reproduced the current behavior locally. Running Sorbet on `issue_7399_repro.rb` produced this output:

`issue_7399_repro.rb:18: Method foo does not exist on T.class_of(SomeModule) https://srb.help/7003`

This matches the behavior described in issue #7399.

### Solution Approach

#### Understand

The issue is not that Sorbet completely misses the error. Sorbet correctly reports that `foo` does not exist on `T.class_of(SomeModule)`. The problem is that the message is not helpful enough for this specific situation. When a module uses `mixes_in_class_methods`, a developer may expect methods from `SomeModule::ClassMethods` to be available, but `T.class_of(SomeModule)` does not communicate that correctly.

#### Match

I will look for existing Sorbet diagnostics that add extra context or suggestions to method-missing errors. I will also search for tests related to error code `7003`, method lookup failures, `T.class_of`, and `mixes_in_class_methods` so that my change follows Sorbet’s existing testing and messaging style.

#### Plan

1. Find where Sorbet generates the “Method does not exist” diagnostic for error code `7003`.
2. Trace how Sorbet handles receiver types like `T.class_of(SomeModule)`.
3. Check whether the module has class methods mixed in through `mixes_in_class_methods`.
4. Add a clearer diagnostic message for this specific case.
5. Be careful with the exact suggestion because the issue discussion mentions that the original suggested type may need adjustment.
6. Add or update a test case using the reproduction code from issue #7399.
7. Run the relevant Sorbet tests and confirm the clearer message appears without breaking existing behavior.

#### Review

Before opening a pull request, I will review Sorbet’s contribution guidelines and keep the change focused. I will make sure the new diagnostic message is clear, consistent with existing Sorbet errors, and supported by a test.

#### Evaluate

I will verify the fix by rerunning the reproduction case and confirming that Sorbet still catches the missing method error, but now gives more useful guidance. I will also run the relevant automated tests for the diagnostic area.

## Phase III: Build

### Implementation Notes

Implemented a targeted diagnostic improvement for Sorbet issue #7399.

The change is in `core/types/calls.cc`, where Sorbet creates “Method does not exist” diagnostics. When Sorbet encounters a missing method on `T.class_of(SomeModule)`, it now checks whether `SomeModule` uses `mixes_in_class_methods` and whether the missing method exists in one of those class-method modules.

When that pattern is detected, Sorbet keeps the original error but adds a helpful note explaining where the method is defined and suggests the more accurate type form:

`T.all(T::Class[SomeModule], SomeModule::ClassMethods)`

### Code Changes

Active branch:
https://github.com/vanshikahardikshah/sorbet/tree/fix-issue-7399

Key commits:

* `089799e9f` — Add reproduction case for issue 7399
* `af1946b2d` — Improve class method mixin diagnostic
* `2f525c7e9` — Add test for class method mixin diagnostic

Files changed:

* `core/types/calls.cc`
* `test/testdata/infer/generics/class_of_mixes_in_class_methods.rb`
* `issue_7399_repro.rb`

### Testing Strategy

I built Sorbet locally with:

`bazel build //main:sorbet --config=dbg`

The build completed successfully.

I then ran Sorbet against the new regression test file:

`./bazel-bin/main/sorbet --silence-dev-message test/testdata/infer/generics/class_of_mixes_in_class_methods.rb`

The test produced the expected original error and the new contextual note:

`foo is defined in SomeModule::ClassMethods, which is mixed in through mixes_in_class_methods on SomeModule.`

It also suggested:

`T.all(T::Class[SomeModule], SomeModule::ClassMethods)`

### Challenges Faced

The main challenge was understanding how Sorbet tracks modules declared through `mixes_in_class_methods`. I traced the implementation through `resolver/resolver.cc` and `resolver/GlobalPass.cc`, which showed that Sorbet stores the class-method modules internally and mixes them into the singleton class of classes that include the interface.

I also initially needed to set up Bazelisk because Bazel was not installed locally. After that setup, Sorbet built successfully and the focused regression test confirmed the change.

**Phase IV: Submit & Iterate**

Pull Request Submission

I opened a pull request to the upstream Sorbet repository for issue #7399.

Pull request link: https://github.com/sorbet/sorbet/pull/10415

Pull request title:

Improve diagnostic for T.class_of with mixes_in_class_methods

The pull request updates Sorbet’s missing-method diagnostic for cases where T.class_of(SomeModule) is used with a module that provides class methods through mixes_in_class_methods.

Instead of only showing that the method does not exist on T.class_of(SomeModule), Sorbet now adds context explaining that the method is defined in the module’s ClassMethods module and was mixed in through mixes_in_class_methods.

The new diagnostic also suggests using the more accurate intersection type:

T.all(T::Class[SomeModule], SomeModule::ClassMethods)

Pull Request Summary

This contribution improves developer guidance without changing Sorbet’s underlying type-checking behavior.

Sorbet still correctly reports the original error:

Method foo does not exist on T.class_of(SomeModule)

However, it now gives additional context such as:

foo is defined in SomeModule::ClassMethods, which is mixed in through mixes_in_class_methods on SomeModule.

It also suggests the correct type representation for this pattern.

Files Included in the Pull Request

* core/types/calls.cc
* test/testdata/infer/generics/class_of_mixes_in_class_methods.rb
* issue_7399_repro.rb

Final Testing

Before submitting the pull request, I verified the implementation through both focused and full testing.

Focused regression test:

bazel test //test:test_PosTests/testdata/infer/generics/class_of_mixes_in_class_methods

Result:

Executed 1 out of 1 test: 1 test passes.

Full Sorbet test suite:

bazel test //test:test

Result:

Executed 2227 out of 2227 tests: 2227 tests pass.

I also manually ran the local Sorbet binary against the regression file and confirmed that the new contextual diagnostic appears with the suggested intersection type.

Maintainer Feedback

No maintainer feedback has been received yet.

Next Steps

I will monitor the pull request for feedback from Sorbet maintainers. If reviewers request changes, I will update the implementation, run the relevant tests again, push the follow-up commit, and respond professionally on the pull request.

Status

Awaiting review

