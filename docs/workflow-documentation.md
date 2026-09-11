# Workflow Documentation

## Overview
The workflow implements a Specialist–Auditor multi-agent quote approval pattern in n8n.

## Flow
Start → Mock Quote Request → Evaluator Agent → Context Handoff → Auditor Agent → Auditor Decision → Is Approved? → Approved Queue / Manual Review Queue.

## Evaluator
Analyzes pricing, discounted total, effective discount, risk flags, and preliminary recommendation. It does not make the final approval decision.

## Auditor
Receives original quote data plus Evaluator context and enforces the configured business rules. Its structured decision is exactly APPROVED or MANUAL_REVIEW.

## Routing
The IF node checks final_decision == APPROVED. True routes to Approved Queue; false routes to Manual Review Queue.

## Validation
Execution 155 reached APPROVED_QUEUE. Execution 156 used a 35% discount, triggered the <=20% rule, returned MANUAL_REVIEW, and reached MANUAL_REVIEW_QUEUE.

See the exported workflow at workflow/topic8-workflow.json and the Loom demo for visual evidence.