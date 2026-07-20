## Week 7 — Issue selection

**Issue link:** [\[Issue #37 link\]](https://github.com/ascherj/pathreview/issues/37)

**Issue title:** Add snapshot tests for prompt templates to catch accidental changes

**Tier:** [*] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
Prompt changes can greatly affect review quality. This issue requests that we save a fingerprint of each prompt as a baseline, and add a test that compares the current prompt against that saved baseline. When the test passes, the prompt is unchanged; when it fails, it warns that the prompt has changed. If the change was intentional, the prompt requires a version bump. The test would be added in tests/unit/test_prompt_templates.py, which checks the prompts defined in rag/generator/prompt_templates.py.

**Branch name:** test/37-prompt-template-snapshots-tests

**Setup confirmation:** [Yes] App runs locally at localhost:5173

**Cohort ledger:** [Yes] Issue added to cohort ledger