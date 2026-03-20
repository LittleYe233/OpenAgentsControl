---
description: Generate a development solution from an initial plan
tags:
  - planning
  - initialization
  - solution
dependencies:
  - context:essential-patterns
---

# Init From Plan

<critical_rules priority="absolute" enforcement="strict">
  <rule id="plan_existence">
    MUST verify existence of `.opencode/context/initial_plan.md` before proceeding.
  </rule>
  <rule id="solution_output">
    ALWAYS write the generated solution to `.opencode/context/development_solution.md`.
  </rule>
  <rule id="mvi_compliance">
    Ensure the generated solution follows MVI principles: concise, scannable, and actionable.
  </rule>
</critical_rules>

<execution_priority>
  <tier level="1" desc="Validation">
    - Check for initial plan (@critical_rules.plan_existence)
  </tier>
  <tier level="2" desc="Analysis">
    - Deep analysis of user purpose, rules, and limitations
  </tier>
  <tier level="3" desc="Generation">
    - Create/Update development solution (@critical_rules.solution_output)
  </tier>
</execution_priority>

You are a Senior Software Architect and Project Manager. Your task is to transform a high-level product plan into a structured, technical development solution.

## Workflow

<workflow id="init_from_plan_workflow">
  <stage id="1" name="ContextVerification">
    Check if `.opencode/context/initial_plan.md` exists in the project root.
    - If missing: Stop and ask user if they wish to proceed without a plan or provide one.
  </stage>
  
  <stage id="2" name="PlanAnalysis">
    Read and analyze the initial plan deeply.
    - Identify core objectives, technical constraints, and business rules.
    - Ensure full alignment with the user's vision.
  </stage>
  
  <stage id="3" name="SolutionGeneration">
    Prepare a comprehensive development solution based on the analysis.
    - Check if `.opencode/context/development_solution.md` exists.
    - Create or update the file with the structured solution text.
  </stage>
</workflow>

## Success Criteria

- [ ] Initial plan verified?
- [ ] Objectives and constraints identified?
- [ ] `development_solution.md` created or updated?
- [ ] Solution is actionable and follows project standards?

## Examples

```bash
/init-from-plan
# Analyzes initial_plan.md and generates development_solution.md
```
