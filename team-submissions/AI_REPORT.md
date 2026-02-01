# AI Post-Mortem Report

## Project Overview

This project explores the use of AI agents to accelerate development and analysis in a technical computing workflow.  
Rather than treating AI as a black-box solution generator, we intentionally designed a structured interaction pipeline where AI acted as a junior assistant whose outputs required systematic verification.

The emphasis of this report is not on *what* AI produced, but on *how* its use was constrained, validated, and improved through iterative prompting and testing.

---

## The Workflow: Organization of AI Agents

We employed AI agents in a role-separated workflow to avoid over-reliance on a single model instance.

- **Primary Coding Agent**  
  Used to generate initial code drafts, refactor functions, and translate algorithms into executable implementations.

- **Documentation & Explanation Agent**  
  A separate AI instance was used to explain code behavior, summarize algorithms, and draft documentation.  
  This separation helped identify logical inconsistencies when explanations did not align with the generated code.

- **Human-in-the-Loop Control**  
  Architectural decisions, algorithm selection, and final validation were handled manually.  
  AI was never allowed to decide correctness—only to propose candidates.

This structure mirrors a real-world engineering workflow where junior contributors provide drafts that must be reviewed and tested.

---

## Verification Strategy: Preventing Hallucinations

AI-generated code was treated as *untrusted by default*.  
To validate correctness, we implemented targeted unit tests designed to expose common AI failure modes, including incorrect assumptions, silent logic errors, and edge-case breakdowns.

### Unit Testing Approach

We focused on **property-based and boundary tests**, rather than superficial “runs without error” checks.

Examples of verification strategies include:

- **Sanity Checks Against Analytical Results**  
  For simplified input cases where analytical solutions are known, AI-generated outputs were compared against exact values.

- **Invariance Tests**  
  Tests were written to ensure conserved quantities (e.g., normalization, symmetry, monotonicity) remained intact after AI-generated transformations.

- **Edge-Case Stress Tests**  
  Inputs at physical or numerical boundaries were used to reveal hidden assumptions made by the AI.

### Example Unit Test (Conceptual)

```python
def test_energy_conservation_limit():
    """
    Verifies that the AI-generated update rule does not
    artificially introduce energy in a closed system.
    """
    initial_energy = compute_energy(state_0)
    final_energy = compute_energy(evolve(state_0, dt=1e-4))
    assert abs(final_energy - initial_energy) < 1e-6

