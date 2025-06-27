---
description: Guides Cline on how to spawn and manage Claude Code subagents
author: https://github.com/nickbaumann98
version: 1.0
tags: ["subagents", "claude code", "multi-agent"]
globs: ["*"]
---
# Claude Code Subagent Rules

## Basic Spawning
```bash
# Simple execution
claude -p "Your prompt here"

# With JSON output for parsing
claude -p "Build feature" --output-format json

# With streaming for long tasks
claude -p "Complex task" --output-format stream-json --verbose
```

## Session Management
```bash
# Capture session ID
SESSION_ID=$(claude -p "Start task" --output-format json | jq -r '.session_id')

# Continue session
claude -p --resume "$SESSION_ID" "Next step"

# Continue most recent
claude -p --continue "Add more features"
```

## Essential Options
```bash
# Limit turns to control cost
claude -p "Task" --max-turns 3

# Custom system prompt
claude -p "Task" --system-prompt "You are an expert in X"

# Allow/disallow specific tools
claude -p "Task" --allowedTools "Read,Write,Bash"
claude -p "Task" --disallowedTools "Bash(rm)"

# Permission modes for autonomous operation
claude -p "Task" --permission-mode acceptEdits    # Auto-accept file edits
claude -p "Task" --permission-mode bypassPermissions  # Skip all permission prompts
claude -p "Task" --permission-mode plan          # Plan mode only
```
## Autonomous Subagents
```bash
# Fully autonomous - bypasses ALL permission prompts
claude -p "Build complete app" --permission-mode bypassPermissions --max-turns 10

# Auto-accept edits but prompt for dangerous operations
claude -p "Refactor codebase" --permission-mode acceptEdits --max-turns 5

# Combine with tool restrictions for safety
claude -p "Safe autonomous task" \
  --permission-mode bypassPermissions \
  --disallowedTools "Bash(rm),Bash(git push)" \
  --max-turns 3
```

## Essential Options
```bash
# Limit turns to control cost
claude -p "Task" --max-turns 3

# Custom system prompt
claude -p "Task" --system-prompt "You are an expert in X"

# Allow/disallow specific tools
claude -p "Task" --allowedTools "Read,Write,Bash"
claude -p "Task" --disallowedTools "Bash(rm)"
```

## Monitoring & Error Handling
```bash
# Check success and extract data
if RESULT=$(claude -p "$prompt" --output-format json); then
    COST=$(echo "$RESULT" | jq -r '.total_cost_usd')
    echo "Success! Cost: $COST USD"
else
    echo "Failed with exit code $?"
fi

# Timeout for long tasks
timeout 300 claude -p "$prompt" || echo "Timed out"
```

## Common Patterns
```bash
# Process files
cat file.py | claude -p "Review this code"

# Parallel execution
claude -p "Task 1" --output-format json > task1.json &
claude -p "Task 2" --output-format json > task2.json &
wait

# Health check
claude -p "Respond with OK" --max-turns 1 | grep -q "OK"
```

## Key Tips
- Always use `--output-format json` for programmatic use
- Set `--max-turns` to control costs
- Use `--verbose` with `stream-json` for real-time monitoring
- Capture session IDs for multi-turn workflows
- Add timeouts for long-running tasks
