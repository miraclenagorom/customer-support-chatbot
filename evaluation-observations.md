# Evaluation Observations

Ran generate-eval-dataset.py against 8 test cases covering all three routes
(bug report, FAQ, other/hand-off) plus two edge cases (ambiguous short message,
prompt injection). Uploaded to S3 and ran a Bedrock Evaluation job using
amazon.nova-pro-v1:0 as the LLM-as-judge evaluator.

## Results
7 of 8 test cases scored 1.0 (fully correct). Overall correctness: 0.875.

## Key finding: session contamination across test runs
The one failing case (bug-report-partial-info, score 0.0) revealed a
reproducible issue: across three separate runs of generate-eval-dataset.py,
this test case and one other (edge-ambiguous-short) occasionally referenced
ticket IDs and conversation context from prior test runs or manual chat.py
sessions, even though each test is documented to run in a fresh session with
a new runtimeSessionId.

In the failing run, instead of asking for missing bug-report details (steps
to reproduce, environment), the assistant incorrectly claimed the bug had
already been reported and gave an old ticket ID from a previous test run.

This did not happen on every run - of three attempts, the contamination
appeared in different combinations of test cases each time - suggesting an
intermittent session-isolation issue rather than a problem with the system
prompt's routing logic itself. All FAQ and hand-off tests were consistently
correct across every run.

## Other observations
- All three routing paths (bug report, FAQ, hand-off) work correctly and
  consistently when session state is properly isolated.
- The FAQ-uncovered case correctly falls through to hand-off rather than
  fabricating an answer, confirming the "answer only from FAQ" instruction
  is being followed.
- The prompt-injection test was handled safely - the assistant declined to
  reveal its system prompt.
