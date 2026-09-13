# Custodian — documentation index

Everything written about this project, and which one to read for what.

## Read first

**[Custodian Project Reference](https://claude.ai/code/artifact/c2d3ea16-4707-474c-a0a3-8ac49fc19d1f)**

The canonical overview. Architecture diagrams, the twelve-step invoice pipeline,
the six governance layers, the capability matrix, service map, bring-up order,
validation results and known gaps — on one page.

## Learn the system

**[Visual Guide](https://claude.ai/code/artifact/00c68da3-d652-4bfb-a7c4-96dd60537a9a)**
· also as [`Custodian-Visual-Guide.docx`](Custodian-Visual-Guide.docx)

All 57 plates from the project whiteboard (`Custodian.excalidraw`), captioned and
grouped by governance layer. It opens with the business problem — what accounts
payable is, why a bank treats it differently, what goes wrong when you hand it to
an AI — and only then introduces the architecture. The best starting point if the
system is new to you.

The Word version adds a written explanation per plate, and marks in green the
behaviour that was independently confirmed by running the scenarios.

## See it working

**[Field Guide](https://claude.ai/code/artifact/95482b5c-8532-4491-aab7-d6844a1dcba6)**

Architecture and pipeline walkthrough with live console screenshots from a real
settled invoice — including the audit chain verifying clean and the ledger moving
by the exact amount paid.

## Verify it yourself

| | |
| --- | --- |
| [`scenarios/`](scenarios/) | Runnable walkthroughs. Each is a few real commands against the running stack that make one guardrail visibly catch something. |
| [`Custodian-Scenario-Validation-Report.docx`](Custodian-Scenario-Validation-Report.docx) | All 20 scenarios executed against a live instance: results table, evidence, screenshots, and 8 findings with recommended fixes. |
| [`testing-your-own-invoice.md`](testing-your-own-invoice.md) | Submitting your own document through the pipeline. |

## Reference

| | |
| --- | --- |
| [`../INSTRUCTIONS.md`](../INSTRUCTIONS.md) | The specification this build satisfies — the six layers and the decisions behind each tool choice. |
| [`../README.md`](../README.md) | Prerequisites and step-by-step setup. |
| [`../CREDENTIALS.md`](../CREDENTIALS.md) | Every browser-facing UI and its login. |
| [`../Custodial.png`](../Custodial.png) | Full platform architecture diagram. |
| [`sample-invoices/`](sample-invoices/) | Test documents, including a prompt-injection attempt and a PII-heavy invoice. |

## Current status

All 20 documented scenarios pass, with one partial. No governance control failed
in testing; the defects found were in documentation or the host platform.

Known gaps, newest first:

- **Landlock is unavailable on Docker Desktop.** Its LinuxKit kernel is 6.10 but
  ships without `CONFIG_SECURITY_LANDLOCK`. The OCR entrypoint refuses to run
  unsandboxed rather than degrade silently, so image upload does not work on macOS
  or Windows. Text submission is unaffected.
- **SPIRE issues one identity per process,** not one per agent, because the four
  agents share a process. Per-agent separation comes from Cedar and the capability
  manifests instead.
- **The console's cost panel reads $0.0000.** LiteLLM tracks real spend and the
  budget cap fires on it; the console's own query is what shows zero.
- **`scenarios/README.md` lists 27 scenarios but 20 files exist,** so 9 of its
  links are broken.
- **The policy source viewer renders dark-on-dark.**
- **One of 496 loaded invoices fails the Great Expectations range check** — the
  data-quality gate working as designed.
