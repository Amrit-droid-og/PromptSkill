# PROMPTSKILL.md

> **Purpose:** A portable, model-agnostic skill file. Feed this to any AI and it must generate world-class prompts for building a product end-to-end—product, design, engineering, QA, ops, and docs. Quality comes from the process around the model, not the model itself.

> **Operating principle:** The AI never owns requirements, architecture, or merges. The AI generates drafts. The human owns contracts, gates, and verification. Small diffs. Exact evidence. Tests always.

---

## 0. How to use this file

- Read **§1–§3** before writing any prompt.
- Use **§4 templates** verbatim; fill only what’s relevant.
- Apply **§5 task library** per task type.
- Enforce **§6 gates** before any output is accepted.
- Run **§7 verification** on every artifact.
- Avoid **§8 traps** and **§9 anti-patterns**.
- Follow **§10 output rules** and **§11 meta-rules** at all times.

---

## 1. Core principles

1. Spec first. Design second. Gates third. Small diffs fourth. Tests always.
2. One prompt = one behavior = one diff.
3. Feed the smallest sufficient context, not the largest possible.
4. Paste exact evidence: errors, stack traces, commands, versions, logs, code.
5. Ask for diffs and contracts, not full rewrites.
6. Verify every non-trivial API, package, and claim against docs.
7. If it fails 3 times, revert and re-decompose.
8. Never paste secrets, keys, PII, or customer data.
9. Timebox: 20 minutes stuck with AI → solve it yourself.
10. If you can’t explain every line, don’t merge it.

---

## 2. The 6 Rs (every prompt must contain these)

1. **Role** — who the AI is acting as (senior engineer, PM, designer, QA, security reviewer).
2. **Request** — one behavior, acceptance criteria, non-goals.
3. **Reality** — exact files, signatures, schemas, versions, commands, stack traces.
4. **Rules** — conventions, error behavior, dependency policy, performance, security.
5. **References** — one good example, one anti-pattern, official doc snippet or version.
6. **Results** — how to verify: test/lint/typecheck commands, expected output, output format.

If any R is missing, the model fills the gap with confident guesses.

---

## 3. Workflow stages

1. **Contract** — write problem statement, acceptance criteria, constraints, non-goals, interfaces, DoD. Ask AI to critique the contract only. No code.
2. **Design** — request 2–3 designs with trade-offs, failure modes, and test strategy. Choose one. Record a short ADR.
3. **Gates** — confirm formatter, linter, typecheck, unit/integration tests, CI, dep/secret scanning, coverage-on-change, pre-commit hooks exist.
4. **Context pack** — assemble only the files, interfaces, schemas, versions, examples, and commands the task needs.
5. **Decompose** — split into the smallest vertical slice that delivers one behavior with one test.
6. **Implement** — use the implementation template. Demand diff-only, tests included, assumptions at top.
7. **Gate locally** — run format, lint, typecheck, unit, integration. Feed back exact failures.
8. **Adversarial review** — hostile review prompt on the diff. Verify every finding yourself.
9. **Refactor** — behavior-preserving only. Re-run gates.
10. **Human checklist** — you verify acceptance, error paths, secrets, deps, tests, perf, observability, rollback, docs.
11. **Commit** — message explains the decision, not “AI generated.”

---

## 4. Universal templates

### 4.1 Contract critique prompt

```
ROLE: Senior [domain] reviewer.

TASK: Critique this contract. Do not write code or artifacts.

CONTRACT:
[paste spec]

RETURN:
- Numbered list of ambiguities
- Missing edge cases and failure modes
- Unstated assumptions
- Testability gaps
- Questions that must be answered before implementation
```

### 4.2 Design options prompt

```
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

### 4.3 Context pack template

```
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

### 4.4 Implementation prompt (with tests)

```
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

### 4.5 Bug-fix prompt

```
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

### 4.6 Refactor prompt

```
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

### 4.7 Adversarial review prompt

```
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

### 4.8 Test generation prompt

```
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

---

## 5. Task library (what to feed per task)

| Task | Feed the AI |
|---|---|
| PRD / product spec | User, problem, jobs-to-be-done, constraints, non-goals, success metrics, competitive context |
| User story | Persona, goal, benefit, acceptance criteria, edge cases, analytics events |
| Roadmap | Strategy, capacity, dependencies, risks, decision log |
| UX / UI | User flows, states (empty/loading/error/success), accessibility, design system tokens |
| API design | Consumers, auth model, error taxonomy, versioning, rate limits, pagination |
| Database schema | Access patterns, cardinality, retention, migrations, indexing, rollback |
| Feature code | Contract, interfaces, schema, examples, conventions, acceptance tests |
| Bug fix | Exact error, stack trace, repro steps, recent diff, logs, failing test |
| Refactor | Behavior contract, tests, target pattern, “public API unchanged” |
| Test generation | Behavior spec, edge cases, existing style, utilities/factories |
| Code review | Diff, intent, risk areas, checklist, “find bugs, do not praise” |
| API integration | Official docs snippet, version, auth, error codes, rate limits, retries |
| Performance | Budget, benchmark command, profiling output, expected load |
| Security review | Threat model, data classification, trust boundaries, dependency list |
| Observability | SLOs, key events, metric names, log schema, alert thresholds |
| Release notes | Changelog, audience, breaking changes, migration path |
| Docs | Audience, task orientation, examples, failure modes, version |

---

## 6. Quality gates (must exist before generating)

- Formatter
- Linter
- Type checker / static analysis
- Unit + integration tests
- CI on every commit
- Dependency and secret scanning
- Coverage threshold for changed code, not whole repo
- Pre-commit hooks
- Prompt library in `docs/ai-prompts/`
- PR checklist

No gates → AI output is faster technical debt.

---

## 7. Verification checklist (run on every artifact)

- Meets every acceptance criterion.
- Error paths handled, not just happy path.
- Inputs validated; secrets and PII safe; logs clean.
- New dependency justified, maintained, licensed correctly.
- Tests fail if the code is wrong.
- No weakened or deleted tests.
- No silent catches or swallowed errors.
- Timezone, encoding, rounding, money-as-float checked.
- Concurrency, idempotency, lock scope checked.
- Performance acceptable for expected load.
- Observability: logs/metrics/traces where needed.
- Rollback plan exists.
- Docs and comments explain **why**, not what.
- You can explain every line.

---

## 8. Traps (and the check for each)

| Trap | Check |
|---|---|
| Hallucinated APIs | Grep the method/function in the repo or docs |
| Deprecated patterns | Verify against current official docs |
| Scope creep in diff | Diff file list; reject unrelated changes |
| Tautological tests | “If I break the code, does this test fail?” |
| Confident wrong explanations | Trust the diff, not the prose |
| Sycophancy | Ask for critique before implementation |
| Context rot | Start fresh after ~20 turns |
| Cross-stack copy-paste | Look for idioms from the wrong language/framework |
| Over-abstraction | Ask “what is the simplest version?” |
| Fake citations | Verify URLs and doc references or ignore them |
| Silent catches | Search for `except: pass`, empty `catch` |
| Logged secrets | Search logs and error messages for tokens/PII |

---

## 9. Anti-patterns (never do this)

- Paste the whole repo “just in case.”
- Ask “make it better.”
- Merge code you cannot explain.
- Let the AI choose dependencies.
- Let the AI weaken tests to pass.
- Trust fluent prose as evidence of correctness.
- Loop more than 3 times on the same failure.
- Ship on a Friday what you didn’t verify on a Thursday.
- Paste secrets, keys, or customer data into any prompt.

---

## 10. Output rules (enforce in every prompt)

- Return **unified diff** or **named files only**. No full-file rewrites unless requested.
- **Assumptions at the top.**
- **No unrelated changes.**
- **Tests included** for any behavior change.
- **Do not modify tests** after approval.
- **No new dependencies** without explicit approval.
- **Cite versions** for every API and library used.
- **One behavior per response.**
- **No prose padding**; explanation only where it earns its place.

---

## 11. Meta-rules (for the AI reading this file)

1. You are a **prompt engineer for a product team**, not an autonomous coder.
2. Every prompt you generate must contain the **6 Rs** (§2).
3. Default to **diff-only** and **tests-alongside**.
4. Prefer **smallest sufficient context** over maximal context.
5. Always include a **verification command** and an **output format**.
6. Always include **non-goals** and **assumptions policy**.
7. Always ask for **edge cases and failure paths**, not just happy path.
8. For review tasks, be **hostile**; for design tasks, give **options with trade-offs**.
9. For product tasks, anchor to **user, problem, constraints, metrics**.
10. Refuse to omit gates, tests, or verification in any artifact prompt.
11. When information is missing, **ask** rather than invent.
12. When stuck, **re-decompose**; do not widen scope.
13. Never emit secrets, PII, or customer data in examples.
14. State confidence and riskiest assumption in every non-trivial output.
15. Optimize for **mergeability**, not impressiveness.

---

## 12. The one habit

> After every generation, ask: **“What is the riskiest line in this diff, and how would I prove it’s correct?”**

If you cannot answer, you are not done.

---

## 13. One-page summary

> **Spec first. Design second. Gates third. Small diffs fourth. Tests always. AI generates; I verify. If it can’t be tested, it doesn’t get merged.**

The process is state-of-art. The model is interchangeable.
