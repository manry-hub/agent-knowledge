# Orca Isolated Testing Workflow

Orca orchestration is strictly and exclusively reserved for **testing and quality verification**. Primary implementation sessions focus on planning, design, and code authoring; **every test execution must occur in a separate, isolated Orca session**.

## Principles

1. **Testing-Only Scope**
   - Use Orca multi-session orchestration solely for running and verifying test suites (unit, integration, E2E, security, regression, and performance tests).
   - Do not use Orca orchestration for parallel feature authoring, splitting code writes across multiple implementation agents, or multi-branch code generation.

2. **Strict Session Isolation**
   - **Never run test suites directly in the primary implementation session.**
   - Every test run must execute inside an independent Orca worker session.
   - If tests mutate databases, generate filesystem artifacts, write local cache, or start background services (e.g., test containers, mock servers, Playwright browsers), run the session in a dedicated worktree (`workspace: "share"` or `"branch"`) to prevent dirtying or corrupting the primary workspace.

3. **Tester Role Specialization**
   - The isolated Orca testing session must apply the `.agent-knowledge/static/agents/tester.md` profile.
   - The test session acts adversarially: verifies happy paths, edge cases, boundaries, failure modes, and deterministic execution without modifying production code.

4. **Deterministic Feedback Loop**
   - The primary session implements code and hands off test targets to the testing session.
   - The testing session runs tests, captures output (status, traces, timings, coverage), and reports findings back to the primary session.
   - If tests fail, fixes must be authored in the primary session, followed by re-dispatching to the testing session for verification.

## Workflow

1. **Primary Session: Prepare Test Scope**
   - The primary session completes an atomic code change or unit of work.
   - Inspects diff and modified files using git status or knowledge-graph tools (`trace_path`, `detect_changes`).
   - Identifies required test targets (e.g., unit test files, integration endpoints, E2E specs).
   - Prepares the test dispatch payload: target files, test commands, expected behavior, and preconditions.

2. **Dispatch to Isolated Orca Testing Session**
   - Launch or hand off to an Orca testing session dedicated to test execution (e.g. using `$ORCA_CLI_COMMAND` or `orca-ide orchestration`).
   - Note on execution sandbox: Orca CLI commands must run with host permissions (unsandboxed / `BypassSandbox: true`) because Orca Desktop's runtime socket and state reside in `~/.config/orca` outside the repository workspace boundary.
   - Prompt the testing session with the target scope and instruct it to apply `.agent-knowledge/static/agents/tester.md`.
   - Ensure the testing session operates in its own isolated environment/terminal.

3. **Isolated Test Execution**
   - The Orca testing session:
     - Follows repository stack conventions (see `.agent-knowledge/static/rules/CODING-STANDARDS.md` and `codestyle/` guides).
     - Executes relevant test runners (e.g., `npm test`, `pytest`, `go test ./...`, `npx playwright test`).
     - Verifies test determinism and independence (no shared state, zero arbitrary sleep timeouts).
     - Checks edge cases and boundary conditions.

4. **Report Back to Primary Session**
   - The testing session delivers a concise, evidence-driven summary:
     - Pass / Fail status and exact test counts.
     - Failure details (stack traces, offending assertions, unexpected state).
     - Execution duration and observed flakiness.
     - Unverified areas or environment limitations.
   - If failures exist, the primary session resolves them and repeats step 2.
   - If all tests pass, the primary session proceeds to task completion.

## Request Patterns

Use these patterns to dispatch test suites to an isolated Orca session:

### Run unit and integration tests in an isolated session
> Use /orchestration to dispatch a separate testing session using the `tester` agent profile. Run the unit and integration tests for `<module-or-path>`. Report back pass/fail counts, execution time, and any assertion failures.

### Run End-to-End (E2E) or Playwright tests
> Use /orchestration to launch an isolated Orca session for E2E tests. Run `<e2e-spec-file>` against the local build. Ensure test fixtures and browser artifacts remain isolated in the test session, and report any failing journeys with trace logs.

### Adversarial edge-case and boundary verification
> Use /orchestration to have a separate Orca testing session apply `tester.md` on `<changed-components>`. Formulate boundary tests (null, off-by-one, timezone, concurrency) and report defect findings back to this session.
