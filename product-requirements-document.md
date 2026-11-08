# Product Requirements Document (PRD)

**Title:** PR Comment Issue Analyzer Agent
**Date:** 2026-11-06
**Owner:** Development Team
**Solution Category:** AI Agent
Yoav test
---

## Product Purpose & Value Proposition

**Elevator Pitch:**
Developers waste time parsing noisy PR comment threads to find the actual problem being raised. This agent reads those comments and tells you what the real issue is — instantly.

**Business Need:**
Pull request reviews often contain scattered, indirect, or verbose feedback. Developers and tech leads must manually read through all comments to understand what the reviewer is actually asking. This agent eliminates that friction by reasoning over the full comment thread and surfacing the core issue.

**Expected Value:**
Instant identification of the root issue from any PR comment thread, replacing manual reading and interpretation with a single agent call.

**Product Objectives:**
1. Accurately identify the real underlying issue from PR comment threads.
2. Deliver the result instantly via a single agent invocation.
3. Handle noisy, indirect, or multi-threaded comment formats reliably.

---

## Business Metrics

| Metric | Baseline | Target | Timeline | Process / Capability | Source |
|--------|----------|--------|----------|----------------------|--------|
| Time to identify root issue from PR comments | — | Instant (single agent call) | — | Software Delivery / Code Review | user |

---

## Requirements

### Must-Have Requirements

**R1: Fetch PR Comments**

- **Problem to Solve**: The agent needs access to all comments on a given pull request to perform its analysis.
- **User Story**: As a developer, I need the agent to retrieve all comments from a PR so that it has the full context to identify the issue.
- **Acceptance Criteria**:
  - Given a valid PR reference (URL or ID + repo), when the agent is invoked, then it retrieves all comments from that PR.
  - Given an invalid or inaccessible PR reference, when invoked, then the agent returns a clear error message.
- **Maps to Objective**: Objective 1
- **Priority Rank**: 1

**R2: Analyze Comment Thread**

- **Problem to Solve**: Raw PR comments are unstructured and noisy; the agent must reason over them to extract meaning.
- **User Story**: As a developer, I need the agent to process the full comment thread so that it can understand the context and tone of the review.
- **Acceptance Criteria**:
  - Given a set of fetched comments, when analyzed, then the agent produces an intermediate understanding of the discussion topics.
  - The agent handles multi-threaded and inline comments.
- **Maps to Objective**: Objectives 1 and 3
- **Priority Rank**: 2

**R3: Identify Root Issue**

- **Problem to Solve**: Developers need to know the core problem being raised, not a summary of every comment.
- **User Story**: As a developer, I need the agent to identify the single most important underlying issue so that I know what to address first.
- **Acceptance Criteria**:
  - Given an analyzed comment thread, when reasoning is complete, then the agent returns a concise statement of the root issue.
  - If no clear issue can be identified, the agent communicates that explicitly.
- **Maps to Objective**: Objective 1
- **Priority Rank**: 3

**R4: Present Result Clearly**

- **Problem to Solve**: The output must be immediately understandable without further interpretation.
- **User Story**: As a developer, I need the result in plain language so that I can act on it without re-reading the PR comments.
- **Acceptance Criteria**:
  - The agent returns a short, plain-language summary (2–4 sentences) of the identified issue.
  - The response is returned directly in the conversation interface.
- **Maps to Objective**: Objectives 1 and 2
- **Priority Rank**: 4

---

## Solution Architecture

**Architecture Overview:**
A lightweight Python AI agent following the A2A protocol, deployed on SAP BTP. It accepts a PR reference as input, fetches comments from the source control platform (GitHub or GitLab), and uses an LLM via SAP AI Core to reason over the comments and identify the root issue.

**Key Components:**

- **AI Agent (Python / A2A)**: Core agent logic — orchestrates comment fetching, LLM reasoning, and result presentation.
- **SAP AI Core**: Provides LLM inference for comment analysis and issue identification.
- **Source Control Integration**: Connects to GitHub or GitLab API to retrieve PR comments given a PR reference.

**Integration Points:**

- GitHub / GitLab REST API: read-only access to fetch PR comments by PR reference.
- SAP AI Core / Generative AI Hub: LLM calls for reasoning over fetched comments.

---

### Agent Extensibility & Instrumentation

**Agent Extensibility:**
- The agent is designed as a single-responsibility component focused on PR comment analysis. The fetching and reasoning steps are separated to allow independent extension (e.g., adding support for additional source control platforms, or swapping the LLM model).
- Extension points: source control connector (pluggable per platform), LLM prompt template (adjustable per use case).

**Business Step Instrumentation:**
- All four milestones (see Milestones section) must emit structured log statements on achievement and on miss.
- Log pattern: `[MILESTONE_ID].[achieved|missed]: [description]`
- Enables production monitoring of where failures occur in the reasoning pipeline.

---

### Automation & Agent Behaviour

**Automation Level:** Autonomous agent

**Actions performed without human approval:**
- Fetching PR comments from the source control platform.
- Reasoning over comment content using the LLM.
- Returning the identified root issue to the user.

**Actions requiring human review:**
- None — the agent is read-only and advisory; it does not modify any system.

**Model or engine used:** LLM via SAP Generative AI Hub (SAP AI Core)

**Knowledge & data sources accessed:**
- Pull request comments from GitHub or GitLab (read-only, provided by the user at invocation time).

**Tools or connectors invoked:**
- GitHub / GitLab API connector: reads PR comments — read-only, no side effects.

**Guardrails & fail-safes:**
- The agent never writes to or modifies the source control system.
- If the PR reference is invalid or inaccessible, the agent returns an error rather than guessing.
- If no clear root issue is found, the agent states that explicitly rather than fabricating a conclusion.
- LLM responses are bounded to the provided comment context — no external knowledge injection.

---

## Milestones

### M1: PR Comments Fetched

- **Description**: The agent has successfully retrieved all comments from the specified pull request.
- **Achieved when**: Comments are returned from the source control API with a non-empty result.
- **Log on achievement**: `M1.achieved: PR comments fetched successfully`
- **Log on miss**: `M1.missed: Failed to fetch PR comments — invalid reference or access error`

### M2: Comments Analyzed

- **Description**: The agent has processed the full comment thread and built a contextual understanding of the discussion.
- **Achieved when**: LLM analysis of the comment thread completes without error.
- **Log on achievement**: `M2.achieved: Comment thread analyzed`
- **Log on miss**: `M2.missed: Comment analysis did not complete — LLM call failed or timed out`

### M3: Root Issue Identified

- **Description**: The agent has determined the real underlying issue from the PR discussion.
- **Achieved when**: The LLM returns a non-empty, coherent issue statement.
- **Log on achievement**: `M3.achieved: Root issue identified`
- **Log on miss**: `M3.missed: No clear root issue could be identified from the comment thread`

### M4: Result Presented

- **Description**: The identified issue has been returned to the user in plain language.
- **Achieved when**: The agent sends a final response to the user.
- **Log on achievement**: `M4.achieved: Result presented to user`
- **Log on miss**: `M4.missed: Result could not be delivered to the user`
