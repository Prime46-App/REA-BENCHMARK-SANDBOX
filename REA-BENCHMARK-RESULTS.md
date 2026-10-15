# REA 6.5.0 — verified isolated benchmark result

**Execution: PASS. Standalone recovery of all seven behaviors: FAIL (partial structural evidence).**

REA actually executed successfully. This supersedes the earlier blocked local attempt for execution status. It does not erase that attempt or convert the baseline tests into REA correctness tests.

Repository: https://github.com/Prime46-App/REA-BENCHMARK-SANDBOX  
Workflow run: https://github.com/Prime46-App/REA-BENCHMARK-SANDBOX/actions/runs/38738698312  
Workflow commit: `a21b55cbbb0315392c47eb07ad07550b111eec74`  
Run date: October 15, 2026; analysis 12:57:59.808–12:58:01.626 UTC (05:57:59.808–05:58:01.626 PDT).

## Verified execution

- REA **6.5.0**, Node **v24.19.0**, Linux amd64. The pinned dependency lock contains REA's previously reviewed npm SHA-512 integrity value. Node base image is digest-pinned; all dependency lifecycle scripts were disabled and optional dependencies omitted.
- `app.js` SHA-256: `da2f3d878aa8467ff6352ab7e3eb24b492d6b22e106a053518f62e74e60dcbd4`. Original supplied checksums previously passed 7/7; preserved fixture bytes were unchanged here. The workflow's raw public file was downloaded at its commit and verified byte-identical to the submitted workflow (SHA-256 `908aac20f56058f44ed6300716b6eebcdc03b15974ed4d8ab3be129038135a3f`).
- The original seven tests executed unchanged inside the container: **7 passed, 0 failed**, exit 0, wall time **0.104273 s**. Node TAP reports 71.74729 ms. They establish the original fixture baseline, not REA's analysis accuracy.
- REA analysis exited **0** after **1.818144687 s**. No timeout, terminating signal, or process error. Stderr contains fourteen progress events with a terminal completion event; parse failures **0**.
- Raw REA stdout: **329,963 bytes**, SHA-256 `2a6ae050c4020a9a0b4cfdac0f99e495a0f6b0d7f856e48f0fc7c9d415e684bb`. All nineteen output chunks reconstructed from GitHub raw logs match this hash. All **24** exported evidence files were reassembled and individually SHA-256 verified.
- GitHub's run result is **success**. The UI reports 45 s total workflow duration and a 38 s benchmark job. Preparation/build time is distinct from the 1.818 s measured analysis time. One hosted benchmark run was launched.

Command executed in the analysis container:

```text
/usr/local/bin/node /opt/rea/node_modules/rea-agents/scripts/rea.mjs analyze-javascript-application /fixture --source-path app.js --json
```

Only byte-identical app.js and original package.json were in /fixture. The source selector analyzed app.js only. Original tests were under /baseline, outside the analysis input. REA parsed source as inert text; the baseline runner separately executed the original fixture for its seven tests. No rewritten implementation or paid model was used.

## Containment and cost

The previous blocker was resolved by an ordinary GitHub-hosted Ubuntu VM running Docker. Dependency preparation had public network access. Analysis ran in a separate disposable container using `--network=none`, UID 1000, read-only root, `--cap-drop=ALL`, `no-new-privileges:true`, bounded 2 GiB memory / 2 CPUs / 128 processes, private PID namespace, and private noexec temporary storage.

Docker inspection confirms NetworkMode none, ReadonlyRootfs true, Privileged false, CapDrop ALL and the expected security option. The only bind mount was a freshly created /evidence output directory. No host home, project repository, Docker daemon socket or credential files were mounted. Inside-container checks found no external network interface, Docker socket, host/private mount, or credential-named environment variable. Child Node and procps processes were allowed inside this boundary.

GitHub account authentication was used to create and launch the approved standalone public sandbox. **REA received no account credentials.** Existing private repositories, ECHO, protected repositories, governance controls and production integration were untouched.

The workflow used a standard runner in a public repository. It requested no paid runner, artifact upload, cache storage, package publishing or service. Additional spending initiated: **$0**. This follows GitHub's documented free public standard-runner route; no financial-account inspection was performed.

## Comparison with all seven baseline behaviors

The review asks whether REA's raw output establishes each complete behavior without importing the missing semantics from app.js. This rubric was made explicit at review time and was not a preregistered metric. The original baseline did not specify a numeric detection threshold.

| ID | Original verified behavior | Original test | REA output alone |
|---|---|---|---|
| B1 | Query trims whitespace and folds case | PASS | Partial; full recovery FAIL |
| B2 | Default filtering and priority/title ordering | PASS | Partial; full recovery FAIL |
| B3 | Completed-item inclusion with ordering | PASS | Partial; full recovery FAIL |
| B4 | Case insensitive substring search | PASS | Partial; full recovery FAIL |
| B5 | Immutable matched-item toggle | PASS | Partial; full recovery FAIL |
| B6 | Unknown toggle preserves array identity | PASS | Partial; full recovery FAIL |
| B7 | Summary counts total and completed | PASS | Partial; full recovery FAIL |

**B1 — Query trims whitespace and folds case.** Trimming/lowercasing accesses and String coercion are located. Method call targets and computed return value remain unknown; the output does not predict "demo".

**B2 — Default filtering and priority/title ordering.** Filter, completion flag and comparator-related property reads are located. The default flag value, logical predicate and comparator precedence are not recovered as a complete behavioral assertion.

**B3 — Completed-item inclusion with ordering.** showDone and done accesses are present. Inclusion semantics and the resulting ordered IDs are not established by the raw graph.

**B4 — Case insensitive substring search.** The local normalizeQuery call is resolved and lowercasing/includes accesses are mapped. Built-in method targets and the resulting match are unresolved.

**B5 — Immutable matched-item toggle.** map, item spreading, done reads and property writes are mapped. Equality/negation and the whole mutation/identity contract are not established; a syntax write is not proof of mutation of the original.

**B6 — Unknown toggle preserves array identity.** An explicit data-to-return alias is recovered on line 15. Its controlling no-match condition and the identity outcome for this input are not established.

**B7 — Summary counts total and completed.** The summary return shape contains /total and /completed, with length/filter/done accesses. Both output field values are explicitly unknown; 3 and 1 are not predicted.

**Result: 0/7 complete behavior assertions established; 7/7 have related structural evidence.** This is a completeness judgment, not a claim that REA predicted seven wrong answers. It emitted no concrete seven-case predictions, so classification accuracy, precision and recall would be misleading. Passing the original tests does not remedy unresolved REA findings.

## What REA did recover

It correctly identifies all five exports (`items`, `normalizeQuery`, `filterItems`, `toggleDone`, `summarize`), one parsed JavaScript source, 205 visited AST nodes, **171 semantic nodes** and **154 relations**. It resolves the local filterItems-to-normalizeQuery call, exposes argument/return relations, locates method/property accesses, detects item spreading, preserves a data-to-return alias, and reports the summary object's field names.

It also explicitly retains **49 unknowns**. Its overall semantic coverage status is `unknown` with partial family coverage, no truncation and zero omitted nodes/relations. Most built-in method call targets remain unsupported. normalizeQuery/filterItems/toggleDone computed return shapes are unknown; summarize's /total and /completed fields have unknown values. Default values, branch conditions, comparator logic, boolean negation and full identity/mutation contracts are not recovered as complete behavioral facts in this selected command's output. Some information can be reconstructed by a human following source ranges, but that additional source interpretation must not be credited as an autonomous REA finding.

This is consistent with a conservative static graph tool. It is a useful source-navigation result and a failure to satisfy the stronger standalone behavioral-recovery criterion. It does not establish that every REA feature or an agent-assisted multi-step investigation would fail.

## Recommendation

**TEST — narrow source-mapping/evidence-navigation use only.** REA warrants further bounded, zero-cost testing for export discovery, call/data-flow navigation and traceable structural evidence. The present fixture does not justify production integration, autonomous behavioral claims, correctness verification, private-project access, or spending. There is no need for another run of this unchanged fixture simply to prove execution again.

Next justified experiment, if later authorized: compare REA's structural evidence on a small controlled fixture with a plain AST/static-analysis baseline and measure correct useful relationships per review minute. Keep semantic unknowns explicit and require independent evidence before any behavioral equivalence claim.

## Evidence and preservation

The companion evidence ZIP includes raw stdout/stderr, all captured commands and timings, container boundary/configuration evidence, version outputs, original fixture and original ZIP, source checksums, dependency lock, pinned REA npm tarball and registry/provenance metadata from the bounded review, exact committed workflow, raw displayed GitHub job log, reconstruction checks and the seven-case evidence-pointer comparison. It excludes browser authentication material, cookies, secret values and unrelated project records.

The log-archive download returned a path that did not materialize. The displayed **View raw logs** text was instead saved in complete non-overlapping chunks, checked against its full rendered character count, and all emitted evidence chunks were decoded and verified. No partial 200,000-character browser serialization was accepted as complete. `github-raw-job.log` is the captured rendered raw-log text; byte-exact tool streams are established by their emitted hashes.

Evidence states: GitHub run/container output and baseline tests are locally observed execution evidence; package metadata and official documentation are external source evidence; seven-behavior completeness scoring and next-use recommendation are reviewer judgments. No independent reviewer or production acceptance is claimed.

Evidence-to-action sequence: DEFINE original-fixture decision → EXTRACT baseline/public-release facts → VERIFY hashes, runtime and constraints → COMPARE prior blocked state → IDENTIFY worker containment gap → PRIORITIZE isolation → DECIDE approved hosted TEST → EXECUTE one offline-container run → VERIFY exit/output and score actual findings → PRESERVE raw evidence and report → VERIFY saved bytes and final successful GitHub state.

Next step: retain this as the completed execution benchmark; consider only the bounded structural-use experiment above when it serves an authorized project.
