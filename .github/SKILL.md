---
name: tdd-cycle
description: |
  Adversarial TDD workflow for any project.
  Enforces RED → GREEN → REFACTOR cycle with contract validation.
  Example usage: /tdd-cycle for Scrabble solver, API endpoints, etc.
---

# TDD Cycle Skill

## Invocation

When the user says "run TDD" or "/tdd-cycle", execute this workflow:

## Workflow Steps

# Step 1: RED phase (loop until tests fail correctly)
1. Invoke test-writer agent (from AGENTS.md)
2. test-writer creates tests for next Scrabble feature (e.g., "validate word against TWL06")
3. Run tests: pytest tests/scrabble/
4. **If tests pass** (theater test detected), repeat step 2 with stricter requirements
5. **If tests fail with clear error messages**, proceed to GREEN phase

# Step 2: GREEN phase (loop until tests pass)
6. Invoke coder agent (from AGENTS.md)
7. coder implements ONLY what error messages specify (e.g., "load TWL06 dictionary")
8. Run tests: pytest tests/scrabble/
9. **If tests fail**, analyze error messages:
   - **If** error message unclear → return to RED phase (refactor test)
   - **Else** → return to step 7 (coder iterates)
10. **If all tests pass**, proceed to REFACTOR

# Step 3: REFACTOR (after GREEN achieved)
11. Run contract validator: pytest -m contract
12. **If contract violations detected**, fix and re-run tests
13. Commit with message: "WHY: [requirement satisfied] EXPECTED: [new behavior enabled]"

## Scrabble-Specific Gates

# Domain validation (conditional logic)
- **Before committing**, verify:
  - All word lookups use TWL06 (not SOWPODS or other dictionaries)
  - Blank tile logic preserves score=0 invariant
  - Board state immutability maintained (functional updates only)

# Why this workflow? (rationale annotation)
This enforces contract-driven development **because** Scrabble has complex
domain rules (dictionary validity, scoring, board constraints). Adversarial
separation **ensures** tests validate behavior, not implementation details.

## Exit Conditions

- **Success:** All tests pass, contracts validated, commit created
- **Escalation:** Theater test detected after 3 iterations → manual review