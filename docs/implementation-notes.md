# Implementation Notes

## Architecture
Two independent Gemini-powered AI Agents are used. The Evaluator is responsible for quote/pricing analysis. The Auditor is responsible for business-rule validation and the final APPROVED or MANUAL_REVIEW decision.

## Context handoff
The Context Handoff node combines original quote fields with Evaluator-generated fields before invoking the Auditor.

## Fail-safe behavior
The Auditor Decision Code node validates the AI decision and independently checks the hard rules. Invalid output or an APPROVED result that violates a hard rule is converted to MANUAL_REVIEW.

## Model configuration
Both agents use Google Gemini models/gemini-3.1-flash-lite, temperature 0.0, max iterations 5, and max output tokens 512.

## Business-rule assumption
The current thresholds are documented assumptions because the supplied Topic 8 assessment document does not provide a numeric rule table: discount <=20%, quote value <=50000, allowed terms Prepaid/Net 15/Net 30, quantity >0, and calculated total within 1% of quote value. Official Requirement/Implementation documents should take precedence if they specify different values.

## Security
The repository contains credential references only. Never commit an API secret or key.