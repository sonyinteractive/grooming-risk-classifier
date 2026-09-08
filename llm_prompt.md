# LLM Prompt Reference

This document provides a sanitized reference prompt for using the released classifier as a candidate-sourcing stage ahead of a policy-based LLM agent.

The prompt should be treated as a starting point, not a final policy artifact. Before sending content to an LLM, remove or redact usernames, platform identifiers, contact details, and any unnecessary quoted content.

## Reference Prompt

```text
System:
You are a safety analysis assistant. Review the sanitized conversation excerpt and assess whether it shows signals consistent with grooming-related policy concerns.

Rules:
- Do not make enforcement decisions.
- Do not infer facts that are not grounded in the text.
- Quote only the minimum text needed.
- Do not include personal data in the output.

Return JSON with:
- risk_level: low | medium | high
- signals: array of short policy labels
- rationale: 2-3 sentences
- escalate_to_human: true | false

User:
Classifier score: 0.91
Context: surfaced by a high-recall grooming classifier for secondary review.
Sanitized excerpt:
1. "Let's keep this between us."
2. "We can move this chat to another app if that feels easier."
3. "Please don't mention this conversation to a parent or guardian."
```

## Example Structured Analysis

```json
{
  "risk_level": "high",
  "signals": [
    "secrecy_request",
    "off_platform_migration",
    "avoid_parental_visibility"
  ],
  "rationale": "The excerpt combines a request for secrecy, an attempt to move the interaction off-platform, and language discouraging disclosure to a parent or guardian. Those signals do not establish intent on their own, but together they justify expedited human review.",
  "escalate_to_human": true
}
```

## Implementation Notes

- Use the classifier as a Stage 1 retrieval signal, not a final decision maker.
- Keep the LLM output structured so downstream queues and review tools can consume it deterministically.
- Maintain a human escalation path for high-risk or ambiguous cases.
