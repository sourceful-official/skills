---
name: reliability
description: Prompting strategies to increase repeatability and reduce retries
---

# Reliability and repeatability

Use these tactics to reduce retries and improve consistency:

## Make the first sentence unambiguous

- Start with the subject and goal.
- Avoid multiple competing subjects.

## Separate goals and constraints

- First sentence: what to generate or change.
- Second sentence: layout, lighting, materials, and camera constraints.
- Third sentence: typography or brand rules.

## Avoid hidden contradictions

- Do not mix "minimal" with "busy" in the same prompt.
- Do not request conflicting lighting setups.

## Iterate with small deltas

- Change only one or two details at a time.
- Keep the idempotency key when retrying the same request.
