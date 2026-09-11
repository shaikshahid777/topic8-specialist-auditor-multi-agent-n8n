# Topic 8 – Specialist-Auditor Multi-Agent Quote Approval Workflow

## Assessment Overview

This project implements the **Topic 8 LMS Assessment: Single-Agent vs. Multi-Agent Systems – Build a Specialist-Auditor Multi-Agent Workflow** using n8n and Google Gemini.

The workflow demonstrates two specialized AI Agents with clearly separated responsibilities, structured context handoff, business-rule auditing, fail-safe validation, and dynamic approval routing.

## Workflow Architecture

```
Start
  ↓
Mock Quote Request
  ↓
Evaluator Agent
  ↓
Context Handoff
  ↓
Auditor Agent
  ↓
Auditor Decision
  ↓
Is Approved?
 ↙          ↘
Approved     Manual Review
Queue        Queue
```

## Agent Responsibilities

### 1. Evaluator Agent – Pricing Specialist

The Evaluator Agent analyzes the incoming quote and:

- Calculates the discounted total
- Calculates effective discount
- Identifies pricing and risk flags
- Provides a preliminary recommendation
- Returns structured output

The Evaluator **does not make the final approval decision**.

### 2. Auditor Agent – Business-Rule Auditor

The Auditor Agent receives the original quote together with the Evaluator's structured analysis and:

- Independently validates the quote
- Applies the configured business rules
- Identifies rule violations
- Produces the final decision
- Returns exactly `APPROVED` or `MANUAL_REVIEW`

## Context Handoff

The **Context Handoff** node combines the original quote information with Evaluator-generated fields, including:

- Evaluator recommendation
- Calculated total
- Effective discount
- Evaluation summary
- Risk flags

This creates a clear and structured communication layer between the two agents.

## Gemini Configuration

Both AI Agents use Google Gemini:

- **Model:** `models/gemini-3.1-flash-lite`
- **Temperature:** `0.0`
- **Max Iterations:** `5`
- **Max Output Tokens:** `512`
- **Structured Output Parser:** Enabled

No OpenAI API is required for this implementation.

## Business Rules

The implementation currently uses these documented assumptions:

1. Discount must be **≤ 20%**
2. Quote value must be **≤ 50,000**
3. Payment terms must be **Prepaid, Net 15, or Net 30**
4. Quantity must be **greater than 0**
5. Evaluator calculated total must match the stated quote value within **1%**

> If the official Requirement Document or Implementation Guide specifies different numeric thresholds, those official values should take precedence.

## Fail-Safe Validation

The **Auditor Decision** Code node provides deterministic validation after the AI Auditor.

- Malformed/invalid Auditor output → **MANUAL_REVIEW**
- Auditor returns APPROVED but a hard rule fails → **MANUAL_REVIEW**
- Valid APPROVED and all hard rules pass → **APPROVED**
- Valid MANUAL_REVIEW → **MANUAL_REVIEW**

This ensures that an unsafe or malformed AI response cannot directly reach the Approved Queue.

## Dynamic Routing

The **Is Approved?** IF node checks:

```
final_decision equals APPROVED
```

Routing:

- **TRUE → Approved Queue**
- **FALSE → Manual Review Queue**

## Live Validation

The workflow was tested using real Google Gemini executions.

### Execution 155 – APPROVED

- Evaluator Agent executed successfully
- Auditor Agent executed successfully
- Structured parsers succeeded
- Final decision: `APPROVED`
- Queue: `APPROVED_QUEUE`

### Execution 156 – MANUAL_REVIEW

- Test quote used a **35% discount**
- Evaluator identified the unusually high discount
- Auditor identified violation of the **≤20% discount rule**
- Final decision: `MANUAL_REVIEW`
- Queue: `MANUAL_REVIEW_QUEUE`

Both executions completed successfully without workflow errors.

## Repository Contents

```
topic8-specialist-auditor-multi-agent-n8n/
│
├── README.md
│
├── docs/
│   ├── assessment-checklist.md
│   ├── implementation-notes.md
│   ├── loom-script.md
│   └── workflow-documentation.md
│
└── workflow/
    └── topic8-workflow.json
```

## Demo

**Loom Demonstration:**  
https://www.loom.com/share/3bb805d68290477e84a972500d76e5b5

The Loom demonstrates the workflow architecture, agent configuration, Gemini models, context handoff, business-rule auditing, decision validation, IF routing, and live execution results.

## Deliverables

- Public GitHub Repository
- Exported n8n Workflow JSON
- Workflow Documentation
- Implementation Notes
- Assessment Checklist
- Loom Demonstration

Screenshots are not separately included because the workflow configuration and execution evidence are demonstrated in the Loom video.

## Security

The repository contains credential references only. **No Gemini API secret/key should be committed to GitHub.**

---

**Topic 8 LMS Assessment – Specialist-Auditor Multi-Agent Workflow**  
Built with **n8n + Google Gemini**.