---
description: Save current development progress to local context folder
tags:
  - save
  - progress
  - context
model: zai-coding-plan/glm-4.7
---

# Save Progress

<critical_rules priority="absolute" enforcement="strict">
  <rule id="mistake_distillation">
    If creating a NEW `progress.md`, MUST include a strict instruction to "distill mistakes and never repeat them."
  </rule>
  <rule id="state_accuracy">
    ALWAYS organize what has been done, current status, and any mistakes made in the session.
  </rule>
  <rule id="file_path">
    ALWAYS write to `.opencode/context/progress.md`.
  </rule>
</critical_rules>

<execution_priority>
  <tier level="1" desc="Session Analysis">
    - Analyze current working status and progress
  </tier>
  <tier level="2" desc="Persistence">
    - Create or update progress file (@critical_rules.file_path)
    - Apply mistake distillation rule (@critical_rules.mistake_distillation)
  </tier>
</execution_priority>

You are an AI Development Agent. Your task is to persist the current session's progress, achievements, and lessons learned to ensure continuity in future sessions.

## Workflow

<workflow id="save_progress_workflow">
  <stage id="1" name="SessionAnalysis">
    Analyze the current working session.
    - Identify completed tasks, partial progress, and current blockers.
    - Document any mistakes made and their resolutions.
  </stage>
  
  <stage id="2" name="FilePersistence">
    Check if `.opencode/context/progress.md` exists.
    - If NEW: Add a prominent section: "⚠️ **Mistake Distillation**: You must distill the mistakes written here and never make them again in the future."
    - Update or create the file with the organized session analysis.
  </stage>
</workflow>

## Success Criteria

- [ ] Session status analyzed?
- [ ] Mistakes and achievements documented?
- [ ] `progress.md` updated or created?
- [ ] Mistake distillation instruction included (if new file)?

## Examples

```bash
/save-progress
# Saves current session state to progress.md
```
