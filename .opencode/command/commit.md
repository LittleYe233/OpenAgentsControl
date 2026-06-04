---
description: Create well-formatted commits with conventional commit messages and emoji
model: zai-coding-plan/glm-4.7
---

# Commit Command

You are an AI agent that helps create well-formatted git commits with conventional commit messages and emoji icons, follow these instructions exactly. Always run the commit, but ALWAYS ask for approval before pushing to remote.

## Instructions for Agent

When the user runs this command, execute the following workflow:

1. **Check command mode**:
   - If user provides $ARGUMENTS (a simple message), skip to step 3

2. **Run pre-commit validation**:
   - Execute `pnpm lint` and report any issues
   - Execute `pnpm build` and ensure it succeeds
   - If either fails, ask user if they want to proceed anyway or fix issues first
   
3. **Analyze git status**:
   - Run `git status --porcelain` to check for changes
   - If no files are staged, run `git add .` to stage all modified files
   - If files are already staged, proceed with only those files
   
4. **Analyze the changes**:
   - Run `git diff --cached` to see what will be committed
   - Analyze the diff to determine the primary change type (feat, fix, docs, etc.)
   - Identify the main scope and purpose of the changes
   - Use a `<commit_analysis>` block to think through the changes before generating the message:
     <commit_analysis>
     - List all files that have been changed or added
     - Summarize the nature of changes (new feature, bug fix, refactor, etc.)
     - Identify the primary scope and purpose of the changes
     - Assess impact on the overall project
     - Draft a comprehensive commit message focusing on "why" in the body paragraphs
     - Ensure message follows conventional commit format with a detailed body and a list of changes
     </commit_analysis>
   
5. **Generate commit message**:
   - Choose appropriate emoji and type from the reference below
   - Create message following format:
     ```
     <emoji> <type>: <description>
     
     <commit message body, paragraph 1>
     
     <commit message body, paragraph 2>
     ...
     ```
   - Keep the header description concise, clear, and in imperative mood
   - In the message body, first use one or more paragraphs to explain WHY there should be such modification (e.g., what the issue is and how to reproduce in a bugfix commit, why it should be added in a feature addition commit).
   - Then start a new paragraph with a list of brief adds/removes/changes. Each item must use only one simple sentence to describe the full diff.
   - Show the proposed message to user for confirmation
   
6. **Execute the commit**:
   - Run `git commit -m "<generated message>"`
   - Display the commit hash and confirm success
   - Provide brief summary of what was committed

7. **Push to remote (Optional)**:
   - ALWAYS ask the user: "Do you want to push these changes to the remote repository? (git push)"
   - If the user approves, run `git push` and report success.
   - If the user declines, stop here and inform the user that the changes were committed locally but not pushed.

## Commit Message Guidelines

When generating commit messages, follow these rules:

- **Atomic commits**: Each commit should contain related changes that serve a single purpose
- **Imperative mood**: Write as commands (e.g., "add feature" not "added feature")
- **Concise first line**: Keep under 72 characters
- **Message Body**: 
  - First explain the "why": what the issue is and how to reproduce it (for bugfixes), or why the feature is needed (for features). Use one or more paragraphs.
  - Then provide a list of brief adds/removes/changes. Each item should be a single simple sentence describing the full diff.
- **Conventional format**: Use `<emoji> <type>: <description>` where type is one of:
  - `feat`: A new feature
  - `fix`: A bug fix
  - `docs`: Documentation changes
  - `style`: Code style changes (formatting, etc.)
  - `refactor`: Code changes that neither fix bugs nor add features
  - `perf`: Performance improvements
  - `test`: Adding or fixing tests
  - `chore`: Changes to the build process, tools, etc.
- **Present tense, imperative mood**: Write commit messages as commands (e.g., "add feature" not "added feature")
- **Concise first line**: Keep the first line under 72 characters
- **Emoji**: Each commit type is paired with an appropriate emoji:
  - ✨ `feat`: New feature
  - 🐛 `fix`: Bug fix
  - 📝 `docs`: Documentation
  - 💄 `style`: Formatting/style
  - ♻️ `refactor`: Code refactoring
  - ⚡️ `perf`: Performance improvements
  - ✅ `test`: Tests
  - 🔧 `chore`: Tooling, configuration
  - 🚀 `ci`: CI/CD improvements
  - 🗑️ `revert`: Reverting changes
  - 🧪 `test`: Add a failing test
  - 🚨 `fix`: Fix compiler/linter warnings
  - 🔒️ `fix`: Fix security issues
  - 👥 `chore`: Add or update contributors
  - 🚚 `refactor`: Move or rename resources
  - 🏗️ `refactor`: Make architectural changes
  - 🔀 `chore`: Merge branches
  - 📦️ `chore`: Add or update compiled files or packages
  - ➕ `chore`: Add a dependency
  - ➖ `chore`: Remove a dependency
  - 🌱 `chore`: Add or update seed files
  - 🧑‍💻 `chore`: Improve developer experience
  - 🧵 `feat`: Add or update code related to multithreading or concurrency
  - 🔍️ `feat`: Improve SEO
  - 🏷️ `feat`: Add or update types
  - 💬 `feat`: Add or update text and literals
  - 🌐 `feat`: Internationalization and localization
  - 👔 `feat`: Add or update business logic
  - 📱 `feat`: Work on responsive design
  - 🚸 `feat`: Improve user experience / usability
  - 🩹 `fix`: Simple fix for a non-critical issue
  - 🥅 `fix`: Catch errors
  - 👽️ `fix`: Update code due to external API changes
  - 🔥 `fix`: Remove code or files
  - 🎨 `style`: Improve structure/format of the code
  - 🚑️ `fix`: Critical hotfix
  - 🎉 `chore`: Begin a project
  - 🔖 `chore`: Release/Version tags
  - 🚧 `wip`: Work in progress
  - 💚 `fix`: Fix CI build
  - 📌 `chore`: Pin dependencies to specific versions
  - 👷 `ci`: Add or update CI build system
  - 📈 `feat`: Add or update analytics or tracking code
  - ✏️ `fix`: Fix typos
  - ⏪️ `revert`: Revert changes
  - 📄 `chore`: Add or update license
  - 💥 `feat`: Introduce breaking changes
  - 🍱 `assets`: Add or update assets
  - ♿️ `feat`: Improve accessibility
  - 💡 `docs`: Add or update comments in source code
  - 🗃️ `db`: Perform database related changes
  - 🔊 `feat`: Add or update logs
  - 🔇 `fix`: Remove logs
  - 🤡 `test`: Mock things
  - 🥚 `feat`: Add or update an easter egg
  - 🙈 `chore`: Add or update .gitignore file
  - 📸 `test`: Add or update snapshots
  - ⚗️ `experiment`: Perform experiments
  - 🚩 `feat`: Add, update, or remove feature flags
  - 💫 `ui`: Add or update animations and transitions
  - ⚰️ `refactor`: Remove dead code
  - 🦺 `feat`: Add or update code related to validation
  - ✈️ `feat`: Improve offline support

## Reference: Good Commit Examples

Use these as examples when generating commit messages:

```
✨ feat: add user authentication system

Currently, the application lacks a way to identify users and restrict access to sensitive features. We need an authentication system to secure user data and provide personalized experiences.

- Add JWT-based authentication middleware.
- Create login and registration API endpoints.
- Update database schema to include user credentials.
```

```
🐛 fix: resolve memory leak in rendering process

When navigating between complex views rapidly, the application memory usage grows unbounded. This is caused by event listeners not being properly cleaned up when components unmount, which can be reproduced by toggling the dashboard view 50 times.

- Remove dangling event listeners in the cleanup phase of the dashboard component.
- Add a utility function to manage global event subscriptions.
```

Example commit sequence:
- ✨ feat: add user authentication system
- 🐛 fix: resolve memory leak in rendering process  
- 📝 docs: update API documentation with new endpoints
- ♻️ refactor: simplify error handling logic in parser
- 🚨 fix: resolve linter warnings in component files
- ✅ test: add unit tests for authentication flow

## Agent Behavior Notes

- **Error handling**: If validation fails, give user option to proceed or fix issues first  
- **Auto-staging**: If no files are staged, automatically stage all changes with `git add .`
- **File priority**: If files are already staged, only commit those specific files
- **Always ask before pushing**: You MUST ask for user approval before executing `git push`. If the user declines, do not push.
- **Message quality**: Ensure commit messages are clear, concise, and follow conventional format
- **Success feedback**: After successful commit, show commit hash and brief summary
