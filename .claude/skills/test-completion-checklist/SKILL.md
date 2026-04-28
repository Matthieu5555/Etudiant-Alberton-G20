---
name: test-completion-checklist
description: End-of-task testing protocol. Use after implementing a change, before declaring a task done, and at the end of each plan step. Verifies that new behavior has tests, that the relevant tests actually ran and observed the change, and that nothing was silently skipped. Distinct from write-tests, which describes how a single test is shaped.
---

# Process

Run each check in order. If any check fails, stop and surface the failure to the user before moving on. Do not auto-fix without confirmation.

## 1. Identify what changed

Run `git status` and `git diff` (staged and unstaged). List the files touched and, for each, the public behavior that changed. If you cannot name the behavior change in one sentence, the diff is doing more than one thing; flag it.

## 2. Locate the tests covering the change

For each behavior change, name the test file and the specific test(s) that exercise it. If a behavior change has no corresponding test, this is a **blocking finding** — write the test before continuing. A test "I will write later" does not count.

Inherited tests (a function used by an already-tested caller) only count if the caller's test would actually fail when the new behavior breaks. Verify by mentally inverting the change; if the existing test still passes, the coverage is fake.

## 3. Run the tests

Run the actual test command for the project (`uv run pytest`, `pytest -x`, `npm test`, etc.). Do not claim a test passes because it "should pass" — observe the run.

- Run the full suite when changes touch shared code.
- Run a targeted subset only when the change is genuinely local and the suite is slow; record which tests you skipped and why.

## 4. Verify the tests actually exercised the change

A test passing does not prove it ran. Check:

- The test file appears in the test runner's collected list.
- The test was not skipped (`pytest` skip marks, `it.skip`, `xfail`, conditional skips on env vars).
- The test's assertions reference the new behavior, not just incidental state.

For numerical changes, verify the expected values were derived independently (see write-tests). A test that asserts `result == result_from_the_function_under_test` is circular and proves nothing.

## 5. Check for silent regressions

Confirm the full suite is green, not just the new tests. A change that adds a passing test while breaking three existing ones is a regression. If the suite was already red before your change, separate pre-existing failures from new ones explicitly.

## 6. Report

Output, in this order:
- Files changed and the behavior change in each.
- Tests that cover each change, with the test command used.
- Suite result: pass / fail / skipped count, with skipped tests named.
- Any blocking findings (missing tests, circular assertions, silently skipped tests).

# Anti-patterns

Do not declare done because "the code looks right." Do not run only the new test and ignore the rest of the suite. Do not accept `xfail` or `skip` markers added in the same change without an explicit reason in the test docstring. Do not paraphrase the test result ("looks good") — quote the runner's output.
