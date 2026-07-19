---
name: scibowl-generator
description: Handles the end-to-end generation of a science bowl packet.
compatibility: Requires python3 with numpy and scipy
allowed-tools: [code_execution, web_search]
agents: ./AGENTS.md
---

# Skill: End-to-End Science Bowl Generation Engine

## Objective
Coordinate a structured, multi-agent assembly line to generate complex National Science Bowl questions that test deep conceptual understanding while guaranteeing LaTeX compilability and absolute mathematical precision.

## Shared File Environment Matrix
- **Reference Assets**: `references/FORMAT.md`, `references/EXCLUDED_TOPICS.md`
- **Historical Data**: `past_packets/`
- **Pipeline Artifacts**: `Master Outline` (Text) $\rightarrow$ `Problems` (LaTeX) $\rightarrow$ Final Compiled Output

---

## Execution Steps & Pipeline Protocol

### Execution Constraints
- DO NOT simulate multiple roles inside a single LLM context window.
- You MUST explicitly spawn subagents using the `spawn` or `sub_agents` command/tool to provision separated, isolated subagents.
- Await the asynchronous callback or Artifact generation from the subagent before proceeding.

### Step 1: Blueprint Configuration (Director & Brainstorm)
- **Director** validates that all reference files are present in the workspace.
- **Brainstorm** reads the format parameters and past test sets. It outputs the `Master Outline` mapping a science bowl question idea for each designated question slot.

### Step 2: Drafting & Graphic Rendering (Writer)
- **Writer** parses each sketch from the `Master Outline`.
- **Formatting Directives**:
  - Use `\ch{}` from the `chemformula` package for all reactions. Never use `mhchem`.
  - Every graph, phase diagram, or cell schematic must be coded natively using LaTeX `TikZ` syntax.
  - No bold characters (`\textbf` or `\mathbf`) are allowed inside math environments or regular text blocks.

### Step 3: Blind Verification & Distractor Assertion (Solver)
- **Solver** reads *only* the problem text block. It must programmatically evaluate the problem inside the sandbox to isolate the unique true value before looking at the choices.
- **Mathematical Integrity**: It must verify that numbers are mathematically consistent (e.g., cell potentials match Gibbs free energy changes, equilibrium concentrations remain positive).
- It writes out the detailed, non-truncated solution math directly to the `Solutions` document.
- It also reviews the question text and answer choices, ensuring quality.
- If a question contains a structural error (too easy, out of scope, etc), the **Solver** must flag it. The **Director** halts the chain, invalidates that specific question state, and routes it back to the **Brainstorm/Writer** step for a clean substitution run. If a question simply contains a syntax error, the **Director** should reroute the task to the **Writer** to fix the syntax.

### Step 4: Adversarial Quality Audit & Healing Loops (Reviewer & Director)
- **Reviewer** meticulously compares `Problems` against `Solutions` and checks strict compliance with the excluded topics list.
- **The Healing Loop Rule**: If a question contains a structural error (too easy, out of scope, etc), the **Reviewer** must flag it. The **Director** halts the chain, invalidates that specific question state, and routes it back to the **Brainstorm/Writer** step for a clean substitution run. If a question simply contains a syntax error, the **Director** should reroute the task to the **Writer** to fix the syntax.

### Step 5: Final Production Compilation (Compiler)
- **Compiler** collects the verified question structures.
- It formats according to the past test convention.
- It verifies that the entire document can compile via pdfLaTeX with zero environment bugs.
- Once everything has been completed, delete all intermediate files ("Problems", "Solutions", "Outlines" documents). Leave only the final packet (PDF file) in ~/Downloads.

---

## Output Standard Template

The **Compiler** must format the final payload using this structure:

```latex
[a full latex document, including preamble, the questions, and answers]
```