# PR Comment Issue Analyzer Agent

## Business Challenge

Developers spend too much time reading through pull request comment threads to understand the actual underlying issue being raised. Comments are often scattered, noisy, or indirect. An AI agent should read those comments and surface the real issue clearly.

## Business Goals & Success Criteria

| Metric | Baseline | Target | Timeline | Process / Capability | Source |
|--------|----------|--------|----------|----------------------|--------|
| Time to identify root issue from PR comments | — | Instant (single agent call) | — | Software Delivery / Code Review | user |

## Key Milestones

1. **PR Comments Fetched** — Agent retrieves all comments from a given pull request.
2. **Comments Analyzed** — Agent processes and reasons over comment content using an LLM.
3. **Root Issue Identified** — Agent determines the real underlying issue from the discussion.
4. **Result Presented** — Agent returns a concise summary of the identified issue to the user.

## Business Architecture (RBA)

### End-to-End Process

Governance (IT Management)

### Process Hierarchy

```
Governance (E2E)
└── IT Management
    └── Software Delivery
        └── Code Review & Quality Assurance
            └── Pull Request Analysis
            └── Issue Identification
```

### Summary

The challenge maps to the Software Delivery sub-process within IT Management. Specifically, it targets the code review activity where developers need to quickly understand the substance of PR feedback.

## Fit Gap Analysis

| Requirement (business) | Standard asset(s) found | API ORD ID | MCP Server ORD ID | MCP Server Version | Webhook API ORD ID | Data Product ORD ID | Gap? | Notes / assumptions |
| ---------------------- | ----------------------- | ---------- | ----------------- | ------------------ | ------------------ | ------------------- | ---- | ------------------- |
| Read PR comments from a source control system | None (no standard SAP asset) | — | — | — | — | — | Yes | Custom integration with GitHub/GitLab API required |
| Analyze and reason over comment content | SAP AI Core (LLM inference) | — | — | — | — | — | No | Use LLM via agent reasoning loop |
| Identify root issue from PR discussion | Custom agent logic | — | — | — | — | — | Yes | Requires prompt engineering and reasoning over unstructured text |

### Key Findings

- No standard SAP product covers PR comment reading or code review analysis.
- SAP AI Core provides the LLM reasoning capability needed for issue identification.
- A custom Python AI agent is the most direct and appropriate solution.
- Integration with a source control platform (GitHub/GitLab) is required to fetch PR comments.
- The solution is narrow in scope — read comments and identify the real issue, nothing more.

## Recommendations

### PR Comment Issue Analyzer — AI Agent

#### Executive Summary

Custom Python AI agent that reads PR comments and surfaces the root issue.

#### Recommended Solution

A lightweight Python AI agent (A2A protocol) that accepts a pull request reference, fetches comments from the source control platform, and uses LLM reasoning to identify and present the real underlying issue from the discussion. Built on SAP BTP using SAP AI Core for inference.

#### Recommended solution category

AI Agent

#### Intent fit
85%
