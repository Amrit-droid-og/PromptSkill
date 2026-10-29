# PromptSkill

> **The battle-tested, model-agnostic prompt engineering skill file that turns any AI into a disciplined senior engineer.**  
> *Contract-first. Minimal diffs. Tests always. Zero hallucinated fluff.*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/Amrit-droid-og/PromptSkill/pulls)
[![Model Agnostic](https://img.shields.io/badge/AI-Model--Agnostic-orange.svg)](#supported-tools-and-environments)
[![Engineering First](https://img.shields.io/badge/Focus-Production%20Grade-purple.svg)](#why-promptskill)

---

## Navigation

- [The Problem With Standard AI Prompts](#the-problem-with-standard-ai-prompts)
- [The Core Philosophy](#the-core-philosophy)
- [The 6 Rs Framework](#the-6-rs-framework)
- [Interactive Template Library](#interactive-template-library)
- [Interactive Setup by Environment](#interactive-setup-by-environment)
- [Interactive Pre-Flight and PR Verification Checklist](#interactive-pre-flight-and-pr-verification-checklist)
- [Task Matrix](#task-matrix)
- [The 12 Traps and Verification Checks](#the-12-traps-and-verification-checks)
- [Before and After Case Study](#before-and-after-case-study)
- [Supported Tools and Environments](#supported-tools-and-environments)
- [Contributing](#contributing)
- [License](#license)

---

## The Problem With Standard AI Prompts

Most developers prompt AI models using open-ended questions:

```text
"Hey, build me an authentication flow with JWT in Node.js and Express."
```

The resulting failure mode is consistent across every foundation model:
- **Scope Explosion:** The model produces an unprompted 400-line rewrite that breaks existing imports and project structure.
- **Phantom Dependencies:** It introduces non-existent packages or outdated APIs.
- **Silent Failure Handling:** It masks runtime failures with empty `catch (e) {}` blocks.
- **Tautological Tests:** It writes tests that assert trivial truths rather than exercising actual failure paths.
- **Context Drift:** Multi-turn sessions quickly degrade into confident circular reasoning.

The root cause is not the model. The root cause is the absence of an engineering harness.

LLMs are probabilistic completion engines. When given ambiguous boundaries, they substitute missing constraints with plausible guesses.

**`PROMPTSKILL.md` establishes those boundaries.** It acts as an operational runtime configuration for LLMs, forcing strict contract definition, test-driven validation, and atomic diff generation.

---

## The Core Philosophy

```
Spec first. Design second. Gates third. Small diffs fourth. Tests always.
```

1. **Human Ownership:** The AI never owns architecture, system contracts, or git merges. The AI drafts; the human validates.
2. **Model Portability:** The harness is the constant; the model is interchangeable. Whether you run Claude 3.7 Sonnet, GPT-4o, Gemini 2.5/3.8, DeepSeek R1, or local models, the output standard remains identical.
3. **Atomic Unit of Work:** One prompt equals one isolated behavior, verified by one test, delivered in one unified diff.
4. **Minimal Sufficient Context:** Supply only the types, schemas, and signatures relevant to the task. Avoid full repository dumps.
5. **No Verification, No Merge:** Unverified code is technical debt generated at higher velocity.

---

## The 6 Rs Framework

Every prompt structured through `PromptSkill` incorporates the six core parameters. If any parameter is omitted, the model will invent assumptions to fill the void.

| Parameter | Definition | Objective |
|---|---|---|
| **Role** | Specific domain persona (*e.g., Staff Systems Architect, Senior QA Automation Engineer*) | Sets technical depth, baseline conventions, and communication tone. |
| **Request** | Exactly one behavior, acceptance criteria, and explicit non-goals | Prevents feature sprawl and collateral file modifications. |
| **Reality** | Target file paths, signatures, schemas, runtime versions, stack traces | Anchors the model to existing code instead of generic patterns. |
| **Rules** | Code style, error protocols, zero unauthorized dependencies rule | Constrains package selection and runtime conventions. |
| **References** | One good codebase example, one anti-pattern, official doc snippet | Directs the model toward idiomatic repo implementations. |
| **Results** | Test commands, verification expectations, unified diff requirement | Guarantees instant local reproducibility and automated testing. |

---

## Interactive Template Library

Click any template below to expand the copy-pasteable prompt structure:

<details>
<summary><b>Template 1: Contract Critique (Pre-Code Ambiguity Check)</b></summary>

```text
ROLE: Senior [domain] reviewer.

TASK: Critique this contract. Do not write code or artifacts.

CONTRACT:
[paste spec or acceptance criteria]

RETURN:
- Numbered list of ambiguities
- Missing edge cases and failure modes
- Unstated assumptions
- Testability gaps
- Questions that must be answered before implementation
```
</details>

<details>
<summary><b>Template 2: Design Options and Trade-Offs (ADR Generation)</b></summary>

```text
ROLE: Senior [language/stack] architect.

TASK: Propose 3 designs for [goal] under these constraints. No code.

CONSTRAINTS:
- Versions: [...]
- Performance/latency/memory: [...]
- Security/compliance: [...]
- No new dependencies unless justified

FOR EACH DESIGN RETURN:
- Data flow
- Failure modes
- Test strategy
- Complexity and maintenance cost
- Why I might reject it

END WITH: a recommendation and the single riskiest assumption.
```
</details>

<details>
<summary><b>Template 3: Context Pack (Precision Input Formulation)</b></summary>

```text
ROLE: Senior [language] engineer on [repo/service].

TASK: Implement exactly [one behavior].

ACCEPTANCE CRITERIA:
- [testable bullet]
- [testable bullet]

NON-GOALS:
- Do not change [X].
- Do not add dependencies unless approved.

FILES / CONTEXT:
- [exact path + relevant snippet]
- [call sites, types, interfaces, schema]

CONVENTIONS:
- Error handling: [pattern]
- Logging: [pattern]
- Naming/style: [pattern]
- Existing test style: [example]

ENVIRONMENT:
- Language/runtime: [version]
- Framework: [version]
- Commands: format=[...], lint=[...], typecheck=[...], test=[...]

REFERENCES:
- Good example: [snippet]
- Avoid: [anti-pattern]
- Official docs/version: [snippet]

CONSTRAINTS:
- Security / performance / memory / latency: [...]

OUTPUT:
- Return a unified diff for the named files only.
- Add or update tests.
- List assumptions at the top.
- Do not rewrite unrelated code.
- If ambiguous, ask before generating.
```
</details>

<details>
<summary><b>Template 4: Implementation With Test-First Constraints</b></summary>

```text
ROLE: Senior [language] engineer.

TASK: Implement exactly [one behavior]. Include tests.

ACCEPTANCE CRITERIA:
- [bullet]
- [bullet]

TESTS FIRST:
- Propose failing tests for the acceptance criteria and edge cases.
- Wait for my approval, then implement the smallest change to pass them.
- Do not modify tests after I approve them.

OUTPUT:
- Unified diff for named files only.
- Assumptions at top.
- No new dependencies without approval.
```
</details>

<details>
<summary><b>Template 5: Root-Cause Bug Remediation</b></summary>

```text
ROLE: Senior [language] engineer debugging [service].

SYMPTOM:
[what happens]

EXPECTED:
[what should happen]

EVIDENCE:
- Command: [exact command]
- Error/stack: [paste]
- Relevant logs: [paste]
- Recent diff: [paste]
- Failing test: [paste]

CONSTRAINTS:
- Fix only the cause. Do not refactor unrelated code.
- Do not weaken or delete tests.
- Explain root cause in one paragraph before the diff.

OUTPUT:
- Root cause (one paragraph)
- Unified diff for named files only
- New or updated test that fails before and passes after
```
</details>

<details>
<summary><b>Template 6: Behavior-Preserving Refactor</b></summary>

```text
ROLE: Senior [language] engineer.

TASK: Refactor [target] without changing behavior.

INVARIANTS:
- Public interfaces identical.
- Existing tests unchanged and passing.
- No new dependencies.

RETURN:
- Unified diff
- One-line justification per change
- Any behavior you could not preserve, called out explicitly
```
</details>

<details>
<summary><b>Template 7: Adversarial Code and Security Review</b></summary>

```text
ROLE: Staff engineer doing a hostile review. Do not praise.

REVIEW THIS DIFF:
[paste diff]

INTENT:
[one paragraph]

HUNT FOR:
- Correctness bugs
- Race conditions and concurrency issues
- Security issues (injection, authz, secrets, deserialization)
- Missing edge cases (null, empty, timeout, 429, 500, malformed input)
- Performance traps
- Hallucinated or misused APIs
- Tests that assert nothing meaningful
- Silent catches and swallowed errors
- Logging of sensitive data

FOR EACH FINDING RETURN:
- Severity (blocker / major / minor / nit)
- Evidence (line reference)
- Concrete fix

VERIFY: After your list, state which findings you are least confident about.
```
</details>

<details>
<summary><b>Template 8: Comprehensive Test Suite Generation</b></summary>

```text
ROLE: Senior test engineer.

TASK: Write tests for [function/module] in the existing style.

BEHAVIOR SPEC:
- Happy path: [...]
- Edge cases: [null, empty, max, min, unicode, timezone, money, rounding]
- Failure paths: [timeout, 429, 500, malformed input, auth failure]
- Invariants: [...]

STYLE:
- Framework: [...]
- Factories/fixtures to reuse: [...]
- Naming convention: [...]

RULES:
- Each test must fail if the behavior breaks. No tautologies.
- No network calls unless a fixture/mock is provided.
- Do not modify production code.
```
</details>

---

## Interactive Setup by Environment

Choose your tool below to view integration instructions:

<details>
<summary><b>Cursor and Windsurf</b></summary>

1. Copy [`PROMPTSKILL.md`](PROMPTSKILL.md) to your project root.
2. Add the following rule to your `.cursorrules` or `.windsurfrules`:
   ```markdown
   Always consult PROMPTSKILL.md when planning, drafting, or refactoring code.
   Adhere to the 6 Rs framework and default to unified diffs with accompanying tests.
   Never add new dependencies without explicit human sign-off.
   ```
3. When prompting in chat or composer, type:
   ```text
   @PROMPTSKILL.md Use Template 4.4 to implement [feature description].
   ```
</details>

<details>
<summary><b>Claude Code CLI</b></summary>

1. Keep [`PROMPTSKILL.md`](PROMPTSKILL.md) in your repository root.
2. Reference it in your `CLAUDE.md`:
   ```markdown
   ## AI Development Guidelines
   All generation must adhere to the rules in PROMPTSKILL.md.
   - Enforce small diffs and test-first generation.
   - Run linter and tests after each iteration.
   - Report the single riskiest assumption on completion.
   ```
</details>

<details>
<summary><b>ChatGPT and Claude Projects (Web UI)</b></summary>

1. Navigate to Project Settings or Custom Instructions.
2. Paste the full contents of [`PROMPTSKILL.md`](PROMPTSKILL.md) into the instructions box.
3. Every conversation inside that project will now default to contract critique, minimal diff output, and adversarial self-review.
</details>

<details>
<summary><b>Google Gemini and Antigravity</b></summary>

1. In system instructions or workspace agents configuration, mount `PROMPTSKILL.md` as an active skill.
2. Use standard directives:
   ```text
   Review this contract according to PROMPTSKILL.md section 4.1 before writing implementation code.
   ```
</details>

<details>
<summary><b>Aider and OpenCode</b></summary>

1. Add the file to your active context:
   ```bash
   /add PROMPTSKILL.md
   ```
2. Run your request against specific templates:
   ```text
   Apply PROMPTSKILL.md section 4.5 to diagnose and fix the failing test in tests/auth.test.ts.
   ```
</details>

---

## Interactive Pre-Flight and PR Verification Checklist

Copy this checklist into your pull request descriptions or issue templates:

```markdown
### PromptSkill Verification Gate

#### Contract and Scope
- [ ] Requirements specified before code generation
- [ ] Acceptance criteria explicitly validated
- [ ] No extraneous file changes outside the declared scope
- [ ] No unapproved third-party dependencies introduced

#### Robustness and Error Handling
- [ ] Edge cases tested (null, empty, boundary limits, timeouts)
- [ ] Error paths handled without empty catch blocks
- [ ] Invariants preserved across state transitions
- [ ] No sensitive credentials, keys, or PII exposed in code or logs

#### Test Integrity
- [ ] New unit or integration tests written
- [ ] Verified that tests fail when the feature logic is deliberately broken
- [ ] Zero existing tests weakened or disabled

#### The Final Operational Check
- [ ] Can you explain every modified line?
- [ ] What is the riskiest line in this diff, and how is it proven correct?
```

---

## Task Matrix

What exact context to feed the AI based on task category:

| Task Type | Minimum Sufficient Context to Supply |
|---|---|
| Product Specification (PRD) | Target user, problem statement, jobs-to-be-done, constraints, non-goals, success metrics |
| User Story | Persona, objective, measurable benefit, acceptance criteria, analytics events |
| UX / UI Component | State variations (empty, loading, error, success), accessibilty requirements, design tokens |
| Database Schema | Access patterns, expected cardinality, retention limits, index strategy, migration rollback |
| API Interface | Target consumers, authentication model, error taxonomy, pagination, rate limits |
| Feature Logic | Exact interfaces, schema signatures, existing project patterns, acceptance tests |
| Defect Resolution | Exact error message, complete stack trace, reproduction steps, recent diff, failing test |
| Refactoring | Public interface declarations, existing test suite, target architecture pattern |
| Security Review | Threat model, trust boundaries, sensitive data classification, third-party dependency list |
| Observability | Target SLOs, log schema, metric names, alert thresholds |

---

## The 12 Traps and Verification Checks

| Common LLM Failure Mode | Verification Mechanism |
|---|---|
| Hallucinated APIs | Grep symbol names across repository and official documentation |
| Deprecated Method Signatures | Verify signatures against active version documentation |
| Diff Scope Creep | Inspect git status for unexpected file modifications |
| Tautological Tests | Deliberately alter business logic and ensure the test fails |
| Unearned Prose Explanations | Focus evaluation solely on the diff, not the surrounding narrative |
| Sycophantic Agreement | Require critique and ambiguity extraction prior to code creation |
| Context Degradation | Hard-reset conversation thread after approximately 20 turns |
| Foreign Framework Idioms | Check for conventions borrowed from unrelated programming ecosystems |
| Premature Abstraction | Challenge implementations with "What is the simplest vertical slice?" |
| Fictitious Citations | Manually verify URLs and referenced source material |
| Swallowed Exceptions | Search for `except: pass`, empty `catch`, or unhandled promise rejections |
| Secret Leakage | Audit diffs and log outputs for tokens, passwords, and private data |

---

## Before and After Case Study

### Traditional Approach (Unconstrained Prompt)

Input:
```text
"Can you add rate limiting to our auth endpoint?"
```

Output Result:
- 150 lines of boilerplate rewriting the entire controller.
- Unrequested Redis package added to dependencies.
- In-memory counters that fail across multi-instance deployments.
- No unit tests or integration tests included.

### PromptSkill Approach (Harness Applied)

Input:
```text
ROLE: Senior backend engineer on auth-service.
TASK: Add IP-based rate limiting to POST /api/v1/auth/login.
ACCEPTANCE CRITERIA:
- 5 attempts per minute per IP before returning HTTP 429 Too Many Requests.
- Includes Retry-After header.
- Uses existing Redis client in src/lib/redis.ts.
NON-GOALS:
- Do not modify user session logic.
- Do not add new third-party dependencies.
ENVIRONMENT: Node.js v20, Express v4.19, ioredis v5.3.
OUTPUT: Unified diff for src/middleware/rateLimit.ts and unit tests in tests/rateLimit.test.ts only. State assumptions at top.
```

Output Result:
- An isolated, 25-line atomic unified diff.
- Zero unexpected dependency modifications.
- Correct handling for reverse-proxy `X-Forwarded-For` headers.
- Two test suites validating both nominal operation (200 OK) and throttle enforcement (429).

---

## Supported Tools and Environments

`PromptSkill` works identically across command-line runners, web applications, and IDE integrations:

| Category | Environments |
|---|---|
| Language Models | Claude (Anthropic), GPT Models (OpenAI), Gemini (Google), DeepSeek, Llama, Qwen |
| IDE Platforms | Cursor, Windsurf, VS Code, JetBrains Suite, Zed |
| Developer Agents | Aider, Claude Code, OpenCode, Antigravity, Cline, Roo Code, GitHub Copilot |

---

## Contributing

Pull requests are welcome. If you have developed a battle-tested verification gate, refined a prompt template, or cataloged a new LLM failure mode:

1. Fork the repository
2. Create a branch: `git checkout -b feature/refinement`
3. Commit your changes: `git commit -m 'Add verification rule for distributed lock safety'`
4. Push to origin: `git push origin feature/refinement`
5. Open a Pull Request

---

## License

Distributed under the MIT License. See [LICENSE](LICENSE) for terms.
