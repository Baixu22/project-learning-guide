# Stdio Protocol Reference

> Non-interactive machine protocol for programmatic skill invocation

## Overview

The stdio protocol enables programmatic, non-interactive execution of skills via JSON-line communication on stdin/stdout. Inspired by zread's `--stdio` mode.

## When to Use

- **Agent automation**: AI agents driving skill execution
- **Script integration**: Shell scripts or CI/CD pipelines
- **Batch processing**: Multiple skills in sequence
- **Headless environments**: Servers or containers without TUI

## Protocol Format

### Request (stdin)
```json
{"action": "execute", "skill": "quick_start", "params": {"language": "zh-CN", "detail_level": "beginner"}}
```

### Response (stdout)
```json
{"event": "skill_start", "skill": "quick_start", "timestamp": "2026-04-14T10:00:00Z"}
{"event": "progress", "phase": "discovery", "progress_percent": 20}
{"event": "progress", "phase": "skeleton", "progress_percent": 50}
{"event": "skill_complete", "skill": "quick_start", "output": {"status": "success", "docs_created": 2}}
```

## Events

### Lifecycle Events
```json
// Skill start
{"event": "skill_start", "skill": "<name>", "params": {...}}

// Progress update
{"event": "progress", "phase": "<phase_name>", "progress_percent": 0-100, "details": {...}}

// Skill complete
{"event": "skill_complete", "skill": "<name>", "output": {...}}

// Error
{"event": "error", "skill": "<name>", "error": "message", "recoverable": true}

// Warning
{"event": "warning", "skill": "<name>", "warning": "message"}
```

### Control Events
```json
// Pause (waiting for user input)
{"event": "waiting_for", "type": "user_confirmation", "message": "Confirm generation? This will consume LLM tokens."}

// Resume
{"action": "resume", "response": "yes"}

// Quit
{"action": "quit"}
{"event": "quit", "reason": "user_requested"}
```

## Common Actions

### 1. Quick Start
```json
{"action": "execute", "skill": "quick_start", "params": {"use_zread": true, "language": "zh-CN"}}
```

### 2. Deep Analysis
```json
{"action": "execute", "skill": "deep_analysis", "params": {"module": "auth", "detail_level": "advanced"}}
```

### 3. Continue Progress
```json
{"action": "execute", "skill": "doc_maintenance", "params": {"mode": "continue"}}
```

### 4. Recovery
```json
{"action": "execute", "skill": "hybrid_discover", "params": {"recovery_mode": "resume_from_draft"}}
```

## Error Handling

### Recoverable Errors
```json
{
  "event": "error",
  "error": "Zread generation interrupted",
  "recoverable": true,
  "suggestions": [
    "resume_from_last_checkpoint",
    "restart_from_beginning",
    "skip_failed_modules"
  ]
}
```

### Non-recoverable Errors
```json
{
  "event": "error",
  "error": "Status file corrupted",
  "recoverable": false,
  "suggestions": [
    "backup_and_create_new",
    "manual_verification_required"
  ]
}
```

## Integration Examples

### Shell Script
```bash
#!/bin/bash
echo '{"action": "execute", "skill": "quick_start", "params": {"language": "zh-CN"}}' | \
  python -m project_learning_guide --stdio > output.jsonl
```

### Python Agent
```python
import subprocess
import json

# Start skill in stdio mode
process = subprocess.Popen(
  ["python", "-m", "project_learning_guide", "--stdio"],
  stdin=subprocess.PIPE,
  stdout=subprocess.PIPE,
  text=True
)

# Send action
action = {"action": "execute", "skill": "quick_start", "params": {}}
process.stdin.write(json.dumps(action) + "\n")
process.stdin.flush()

# Read events
for line in process.stdout:
  event = json.loads(line)
  print(f"Event: {event['event']}")
  if event['event'] == 'skill_complete':
    break
```

### Node.js Agent
```javascript
const { spawn } = require('child_process');

const skill = spawn('python', ['-m', 'project_learning_guide', '--stdio']);

// Listen to events
skill.stdout.on('data', (data) => {
  const event = JSON.parse(data.toString());
  console.log(`Event: ${event.event}`);
  
  if (event.event === 'waiting_for') {
    // Send confirmation
    skill.stdin.write(JSON.stringify({action: 'resume', response: 'yes'}) + '\n');
  }
  
  if (event.event === 'skill_complete') {
    skill.kill();
  }
});

// Start skill
skill.stdin.write(JSON.stringify({
  action: 'execute',
  skill: 'quick_start',
  params: {language: 'zh-CN'}
}) + '\n');
```

## Best Practices

1. **Always check for `waiting_for` events**: Some skills require user confirmation (e.g., zread generation)
2. **Handle errors gracefully**: Check `recoverable` field and follow suggestions
3. **Log all events**: Write to `.ai/log/stdio.log` for debugging
4. **Set timeouts**: Skills may run for minutes on large projects
5. **Validate output**: Check `skill_complete` output for expected results

## Migration from TUI

If migrating from interactive TUI mode:

| TUI Feature | Stdio Equivalent |
|-------------|------------------|
| Progress bar | `progress` events with `progress_percent` |
| Confirmation dialog | `waiting_for` event + `resume` action |
| Error popup | `error` event with `recoverable` flag |
| Log viewer | Read `.ai/log/*.log` files directly |
| Cancel button | Send `{"action": "quit"}` |

## Troubleshooting

### Issue: No output on stdout
**Solution**: Ensure `--stdio` flag is passed. Without it, skill runs in TUI mode.

### Issue: JSON parse errors
**Solution**: Each line is a complete JSON object. Split by newlines first.

### Issue: Skills hang waiting for input
**Solution**: Check for `waiting_for` events and send appropriate `resume` action.

### Issue: Recovery from interruption
**Solution**: Read `.ai/status.yaml` to check `draft_state` and use `recovery_options`.

## Version History

- **v2.0** (2026-04-14): Initial stdio protocol specification, inspired by zread `--stdio` mode
- **v1.0**: TUI-only mode, no programmatic access
