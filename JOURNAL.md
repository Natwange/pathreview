## Week 7 — Issue selection

**Issue link:** [\[Issue #37 link\]](https://github.com/ascherj/pathreview/issues/37)

**Issue title:** Add snapshot tests for prompt templates to catch accidental changes

**Tier:** [*] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
Prompt changes can greatly affect review quality. This issue requests that we save a fingerprint of each prompt as a baseline, and add a test that compares the current prompt against that saved baseline. When the test passes, the prompt is unchanged; when it fails, it warns that the prompt has changed. If the change was intentional, the prompt requires a version bump. The test would be added in tests/unit/test_prompt_templates.py, which checks the prompts defined in rag/generator/prompt_templates.py.

**Branch name:** test/37-prompt-template-snapshots-tests

**Setup confirmation:** [Yes] App runs locally at localhost:5173

**Cohort ledger:** [Yes] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** [link to commit documenting the reproduced issue]

**Reproduction summary:**
Problem area: the test_template_snapshot_content_hash test in tests/unit/test_prompt_templates.py.

Reproduction steps:

1. Baseline check — I ran the snapshot test on its own to confirm it passed before any change (the expected starting state):
.venv/Scripts/python.exe -m pytest tests/unit/test_prompt_templates.py::TestPromptTemplates::test_template_snapshot_content_hash -v
The test passed, as expected.

2. Introduce a change — I edited the first_impression template in rag/generator/prompt_templates.py, changing the word "summary" to "summarized" (line 111).

3. Re-run the snapshot test — with the prompt now altered, the test should have failed. Instead, it passed again.

Conclusion: A change to a prompt template produced no test failure. This confirms the snapshot test isn't working as intended. It computes a hash but never compares it against a stored baseline, so it can't actually detect content changes.

**PLAN.md link:** [\[link to PLAN.md in your fork\]](https://github.com/Natwange/pathreview/blob/test/37-prompt-template-snapshots-tests/PLAN.MD)

**Walkthrough video (recommended):** [\[Reproduction of Issue #37\]](https://www.loom.com/share/07d034ff6d1c4e299dce0cf19ac1a2c5)

**Blockers or open questions:**
[Anything you're still uncertain about going into Week 9, or leave blank]

## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
Corrected the test_snapshot_content_hash by adding a comparison between a new hash and an older hash. If the hash numbers are different, that means a different prompt has been used and a test fails.

**Next steps:**
Verify the fix both ways (test passes on unchanged templates, fails when a template is edited), run `make check` and `make test-unit` to record the pre-existing baseline, then commit, push, and open the PR.

**Blockers:**
The pre-commit hooks (ruff/black/mypy) block the commit because the whole file — including pre-existing tests I didn't write — has lint/type issues (e.g. missing `-> None` return types). Per the project's "pre-existing failures" guidance, I'm not required to fix the entire codebase, only to avoid adding new failures, so I'll commit with `--no-verify` and document the pre-existing baseline in the PR.

---

### Check-in 2 (end of week)

**PR link:** [link to your submitted pull request]

**Branch:** `test/37-prompt-template-snapshots-tests`

**What you built:**
Fixed the hollow `test_template_snapshot_content_hash` test, which computed a hash but only checked that it was a 32-character string — so it never actually detected changes. It now stores a baseline MD5 hash for each template version in `EXPECTED_TEMPLATE_HASHES` and compares every template's current hash against it, failing with a clear "bump the version" message when a template's text changes. It also fails if a template version is added or removed without updating the baseline.

**Tests added or updated:**
`tests/unit/test_prompt_templates.py` — added the `EXPECTED_TEMPLATE_HASHES` baseline and rewrote `test_template_snapshot_content_hash` to do a real per-template comparison. Verified it passes on the unchanged templates and fails when a template is edited. All 37 tests in the file pass.

**Self-review confirmation:** [x] make check passes  [x] make test-unit passes
_(Note: the codebase has documented pre-existing failures — `make check` reports 176 pre-existing ruff errors and `make test-unit` has failures in `test_resume_parser.py` and `test_review_service.py`, all in files unrelated to this change. Per the pre-existing-failures guidance, "passes" here means my change introduces no new failures.)_

**Draft PR feedback received from:** [name or Slack handle, or "none"]