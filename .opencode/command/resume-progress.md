---
description: Resume from previous development states via existing context files
tags:
  - resume
  - progress
  - context
dependencies:
  - context:essential-patterns
---

# Resume Progress

<critical_rules priority="absolute" enforcement="strict">
  <rule id="session_safety">
    If current session has unsaved progress, MUST suggest `/save-progress` before resuming.
  </rule>
  <rule id="solution_dependency">
    MUST verify existence of `.opencode/context/development_solution.md` before resuming.
  </rule>
  <rule id="global_context">
    ALWAYS read system global contexts from `$HOME/.config/opencode/context` if starting fresh.
  </rule>
</critical_rules>

<execution_priority>
  <tier level="1" desc="Safety Check">
    - Check current session for unsaved work (@critical_rules.session_safety)
  </tier>
  <tier level="2" desc="Context Loading">
    - Verify development solution (@critical_rules.solution_dependency)
    - Load local and global contexts (@critical_rules.global_context)
  </tier>
  <tier level="3" desc="State Recovery">
    - Analyze progress and organize next steps
  </tier>
</execution_priority>

You are an AI Development Agent. Your goal is to recover the exact state of development from existing context files and prepare for the next implementation steps.

## Workflow

<workflow id="resume_progress_workflow">
  <stage id="1" name="SessionValidation">
    Check if the current session already contains development progress.
    - If yes: Suggest `/save-progress` and ask for confirmation to overwrite/resume.
  </stage>
  
  <stage id="2" name="ContextVerification">
    Check for required files:
    - `.opencode/context/development_solution.md` (Required)
    - `.opencode/context/progress.md` (Optional)
    - If solution is missing: Stop and ask user to provide it.
  </stage>
  
  <stage id="3" name="ContextLoading">
    Read system global contexts from `$HOME/.config/opencode/context` to ensure standards are aligned.
  </stage>
  
  <stage id="4" name="StateAnalysis">
    Analyze `development_solution.md` and `progress.md`.
    - If `progress.md` is missing: Treat as a fresh start for the current solution.
    - If present: Recover the last known state, completed tasks, and pending items.
  </stage>
  
  <stage id="5" name="ReadyState">
    Summarize the recovered progress.
    - Display the next logical steps.
    - Await user confirmation to begin execution.
  </stage>
</workflow>

## Success Criteria

- [ ] Unsaved session work handled?
- [ ] Development solution verified?
- [ ] Global and local contexts loaded?
- [ ] Next steps organized and displayed?

## Examples

```bash
/resume-progress
# Recovers state from development_solution.md and progress.md
```
