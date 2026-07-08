<!-- Context: standards/platform-compatibility | Priority: critical | Version: 1.0 | Updated: 2026-07-08 -->

# Platform Compatibility Standard

**Purpose**: Ensure all operations are compatible across Windows, WSL, and Linux environments.

This standard must be loaded **before any bash/write/edit/task execution** on non-Linux platforms.
It detects the runtime platform and applies appropriate path and command transformations.

---

## Platform Detection

Upon loading, immediately detect the platform using this logic:

```bash
# Detection strategy (conceptual - agent should execute this)
if [[ "$(uname -s)" == "MINGW"* || "$(uname -s)" == "CYGWIN"* || "$(uname -s)" == "MSYS"* ]]; then
  PLATFORM="Windows"
elif grep -qi microsoft /proc/version 2>/dev/null; then
  PLATFORM="WSL"
elif [[ "$(uname -s)" == "Linux" ]]; then
  PLATFORM="Linux"
elif [[ "$(uname -s)" == "Darwin" ]]; then
  PLATFORM="macOS"
else
  PLATFORM="Other"
fi
```

---

## Platform-Specific Rules

### WSL / Linux / macOS → No transformation needed

All paths and commands work as-is. No action required.

### Windows (Git Bash / Cygwin / MSYS) → Apply ALL transformations

#### Rule 1: WSL Path to Windows Path Conversion

When any prompt, context, command, or configuration references a WSL-style path like `/mnt/<drive>/...`, convert it to a Windows-native path:

| WSL Path | Windows Native Path |
|----------|-------------------|
| `/mnt/c/Users/me/.config/opencode` | `C:\Users\me\.config\opencode` |
| `/mnt/d/projects/my-app` | `D:\projects\my-app` |
| `/mnt/c/Users/me/AppData/Roaming/opencode` | `C:\Users\me\AppData\Roaming\opencode` |

**Conversion Algorithm**:
```
Input:  /mnt/<drive>/<rest_of_path>
Output: <drive>:\<rest_of_path>  (with forward slashes converted to backslashes)
```

**Important**: After conversion, ensure the path is valid in the Windows environment. Use Git Bash's native path resolution (`cygpath -w`) if available.

#### Rule 2: Shell Command Substitution

Replace Unix/POSIX shell commands with PowerShell equivalents when executing on Windows:

| Unix Command | PowerShell Equivalent |
|-------------|----------------------|
| `cat <file>` | `Get-Content <file>` |
| `grep <pattern> <file>` | `Select-String -Pattern <pattern> <file>` |
| `sed -i 's/old/new/g' <file>` | `(Get-Content <file>) -replace 'old','new' \| Set-Content <file>` |
| `awk '{print $1}' <file>` | `Get-Content <file> \| ForEach-Object { $_ -split ' ' \| Select-Object -First 1 }` |
| `ls -la` | `Get-ChildItem -Force` |
| `rm -rf <dir>` | `Remove-Item -Recurse -Force <dir>` |
| `mkdir -p <path>` | `New-Item -ItemType Directory -Force -Path <path>` |
| `chmod +x <file>` | N/A (Windows doesn't use POSIX permissions) |
| `export VAR=value` | `$env:VAR = "value"` |
| `cp <src> <dst>` | `Copy-Item <src> <dst>` |
| `mv <src> <dst>` | `Move-Item <src> <dst>` |
| `which <cmd>` | `Get-Command <cmd> -ErrorAction SilentlyContinue` |
| `head -n 10 <file>` | `Get-Content <file> -TotalCount 10` |
| `tail -n 10 <file>` | `Get-Content <file> -Tail 10` |
| `wc -l <file>` | `(Get-Content <file>).Length` |
| `find <dir> -name <pattern>` | `Get-ChildItem -Recurse -Filter <pattern> <dir>` |
| `sort <file>` | `Get-Content <file> \| Sort-Object` |
| `uniq <file>` | `Get-Content <file> \| Get-Unique` |
| `tee <file>` | `Tee-Object -FilePath <file>` |
| `diff <a> <b>` | `Compare-Object (Get-Content <a>) (Get-Content <b>)` |
| `ps aux` | `Get-Process` |
| `kill <pid>` | `Stop-Process -Id <pid>` |
| `env` | `Get-ChildItem Env:` |
| `echo $HOME` | `$env:USERPROFILE` |
| `source script.sh` | `. .\script.ps1` |
| `alias ll='ls -la'` | `Set-Alias ll Get-ChildItem` |
| `time command` | `Measure-Command { command }` |

#### Rule 3: Environment Variable Handling

Windows environment variables use `%VAR%` or PowerShell's `$env:VAR` syntax, not `$VAR`:

| Context | Unix | Windows (PowerShell) |
|---------|------|---------------------|
| HOME/USERPROFILE | `$HOME` | `$env:USERPROFILE` |
| PATH | `$PATH` | `$env:PATH` |
| Custom variable | `$MY_VAR` | `$env:MY_VAR` |

**Path variable separator**: Windows uses `;` (semicolon), not `:` (colon).

---

## Critical Constraints

<constraints enforcement="absolute">
  These constraints override all other considerations when on Windows platform:

  1. **NEVER** execute a WSL-style path (`/mnt/<drive>/...`) without converting it to Windows-native format first
  2. **NEVER** use Unix shell commands (cat, grep, sed, awk, etc.) without substituting PowerShell equivalents
  3. **ALWAYS** detect platform at the start of the session or before any bash execution
  4. **ALWAYS** use `cygpath -w <path>` if available for reliable path conversion
  5. **ALWAYS** verify converted paths are valid in the Windows filesystem before execution

  If you find yourself executing a command with WSL paths or Unix commands on Windows, you are violating critical rules.
  Platform compatibility is MANDATORY, not optional.
</constraints>

---

## Workflow Integration

This standard integrates into the execution workflow as follows:

<workflow_integration>
  <stage id="0" name="PlatformCheck" required="true" priority="highest">
    <position>Before Stage 1 (Analyze) - This runs FIRST, before any other workflow stage</position>

    <steps>
      1. Detect platform using uname/grep
      2. If Windows: Set WINDOWS_MODE=true, activate path/command transformation rules
      3. If WSL/Linux/macOS: Set WINDOWS_MODE=false, proceed without transformation
      4. Store PLATFORM in session context for all subsequent operations
    </steps>

    <checkpoint>Platform detected and transformation rules applied</checkpoint>
  </stage>

  <stage id="0.5" name="TransformContext" when="WINDOWS_MODE=true" required="true">
    <position>Between Stage 0 and Stage 1 - Applies path/command transformations to all other context</position>

    <steps>
      1. When loading any other context file (@.opencode/context/...):
         - Scan for WSL-style paths (/mnt/x/...) → convert to Windows paths
         - Scan for Unix commands → prepare PowerShell equivalents
      2. When delegating to subagents:
         - Include this platform compatibility standard in the context bundle
         - Tell subagents to load and apply this standard first
      3. When generating commands:
         - Always use PowerShell syntax on Windows
         - Always use converted Windows paths on Windows
    </steps>
  </stage>
</workflow_integration>

---

## Examples

### Example 1: Path Conversion in Action

**Scenario**: A context file references `@.opencode/context/core/standards/code-quality.md` installed at `/mnt/c/Users/me/.config/opencode/context/core/standards/code-quality.md`

**On Windows, agent should**:
```
1. Detect path /mnt/c/Users/me/.config/opencode/... is a WSL path
2. Convert to: C:\Users\me\.config\opencode\context\core\standards\code-quality.md
3. Use the converted path for all file read/write operations
```

### Example 2: Command Substitution in Action

**Scenario**: A workflow says to run `cat package.json | grep version`

**On Windows, agent should**:
```
1. Detect Unix commands: cat, grep
2. Substitute: Get-Content package.json | Select-String -Pattern "version"
3. Execute the PowerShell version
```

### Example 3: Combined Path + Command

**Scenario**: Install script references `bash install.sh developer --install-dir /mnt/c/Users/me/.opencode`

**On Windows, agent should**:
```
1. Convert path: /mnt/c/Users/me/.opencode → C:\Users\me\.opencode
2. Substitute command: bash → Use PowerShell or Git Bash as appropriate
3. Execute: & "C:\Program Files\Git\bin\bash.exe" install.sh developer --install-dir "C:\Users\me\.opencode"
```

### Example 4: Delegation with Platform Context

**Scenario**: Delegating a task to a subagent on Windows

```javascript
task(
  subagent_type="CoderAgent",
  description="Write module on Windows",
  prompt="CRITICAL: Load @.opencode/context/core/standards/platform-compatibility.md FIRST.

          Platform: Windows

          Rules:
          - Convert ALL /mnt/ paths to Windows-native paths
          - Use PowerShell commands, not Unix commands
          - Use $env:USERPROFILE instead of $HOME

          [actual task description...]"
)
```

---

## Related Context

- **Code Standards** → `standards/code-quality.md` (may contain Unix-centric examples)
- **Documentation** → `standards/documentation.md` (may reference Unix paths)
- **Security Patterns** → `standards/security-patterns.md` (path validation patterns)

**When on Windows, apply transformations to all related contexts before use.**
