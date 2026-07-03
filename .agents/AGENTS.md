# Token-Efficient Collaborative System Rules (Antigravity + Graph + OpenHands)

To optimize native performance and stay strictly within the 8% remaining Gemini model quota, all agents working in this workspace must adhere to the following rules:

## 1. Graph-First Codebase Analysis
- **Rule**: Never run directory recursive listings (`list_dir`) or generic searches (`grep_search`) to understand codebase architecture or dependencies.
- **Rule**: Always query the `code-review-graph` server first (e.g., using `query_graph_tool`, `get_impact_radius_tool`, or `list_flows_tool`).
- **Rule**: Only load specific files identified by the graph analysis. Never load files larger than 150 lines in their entirety; request specific line ranges using `view_file` to conserve context.

## 2. Strict OpenHands Sandbox Delegation
- **Rule**: Antigravity must act strictly as **The Brain** (Architect/Planner). It must **not** directly write code changes to the host file system, run local terminal debugging/build cycles, or run local unit tests.
- **Rule**: All file updates, terminal command executions, build/test cycles, and debugging actions must be executed inside the sandboxed OpenHands container via the `openhands.execute_bash` tool.
- **Rule**: Plan the exact commands and code edits (e.g. using `python`, `sed`, or other shell scripts) and run them directly in the sandbox.

## 3. Synchronous Sandbox Execution (No Polling)
- **Rule**: Commands run inside the sandbox via `openhands.execute_bash` are executed synchronously. Specify an appropriate timeout (default 300s) and handle the result (exit code, stdout, stderr) immediately in your next step.
- **Rule**: Do **not** run background `sleep` loops or poll task status.

## 4. Summarization and Verification
- **Rule**: When OpenHands finishes, only request the final diff and a brief test pass summary. Do not output raw build logs or stack traces to the main chat.
- **Rule**: Use the `code-review-graph` to review changes post-implementation to ensure architectural integrity.

## 5. Automatic Session State Persistence
- **Rule**: At the end of every execution phase, or whenever pausing/ending a task, the agent MUST automatically update the `task.md` file in the conversation artifacts directory.
- **Rule**: The update must detail completed tasks `[x]`, in-progress tasks `[/]`, and clearly state where the work was stopped so that a fresh conversation session can resume immediately without manual user explanation.

## 6. Docker Cleanup Shortcut
- **Rule**: If the user instructs to "run docker cleanup command", the agent must run the following PowerShell command in the workspace:
  `docker system prune --volumes -f`
- **Rule**: Print the execution stdout/stderr to show the user how much disk space was reclaimed.

## 7. GitHub Push Constraint
- **Rule**: Do NOT push code changes to the GitHub repository automatically.
- **Rule**: Only push changes to the remote GitHub repository when the user explicitly instructs you to do so.

