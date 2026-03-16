# claude-wake

Wake your Windows PC from sleep to start a Claude Code session. Automatically. While you're still in bed.

Claude Code's Max plan has a 5-hour usage window. This tool sets an alarm on your PC so the window starts counting before you sit down to work. Your computer wakes up, sends a quick message to Claude, and goes back to sleep. Takes about 30 seconds, no interaction needed.

## Why I built this

I wanted my Claude session ready by the time I opened my laptop at 8am. The usage window takes 5 hours to reset, so if I started it at 8, it wouldn't reset until 1pm. Starting it at 6am means it resets at 11am, right when I might need a second session.

So I wrote a script that wakes my PC at 6am, pings Claude, and sleeps again. Then I turned it into a CLI so other people could use it too.

## What it does

- Wakes your PC from sleep at a time you choose (uses Windows Task Scheduler with `WakeToRun`)
- Detects your Claude profiles automatically (claude, claude-max, claude-pro, etc.)
- Pings Claude to start the usage timer
- Puts the PC back to sleep after 30 seconds
- Logs every cycle with timestamps
- Saves your config so you only set it up once
- No external dependencies, just Node.js built-ins

## Quick start

```bash
git clone https://github.com/joaodutra88/claude-wake.git
cd claude-wake
npm install
npm run build
node dist/index.js
```

## Usage

```
  ===================================
  CLAUDE WAKE - Schedule your sessions
  ===================================

  Active schedule: Wake at 06:00 (Ready | Profile: claude-max)

  What would you like to do?

  1. Schedule wake time
  2. View execution logs
  3. Remove schedule
  4. Test now (ping Claude without sleeping)
  5. Change profile
  6. Exit

  Option: 1
  What time should your PC wake up? (e.g. 6, 06:00, 5:30): 6

  Detected Claude profiles:

  1. claude-max (C:\Users\you\.claude-max)
  2. claude-pro (C:\Users\you\.claude-pro)
  3. claude (default) (C:\Users\you\.claude)

  Which profile to use? (1-3): 1
  Using profile: claude-max

  Done! Your PC will wake up every day at 06:00,
  ping Claude (claude-max) to start your usage window, then go back to sleep.
```

## How it works

1. You run `claude-wake` and pick a time and a Claude profile
2. It creates a Windows Scheduled Task with wake-to-run enabled
3. It generates a PowerShell script that sets `CLAUDE_CONFIG_DIR`, sends "hi" to `claude -p`, waits 30 seconds, then calls `SetSuspendState` to sleep again
4. Every day at that time, Windows wakes up, runs the script, and goes back to sleep

## Multiple profiles

If you have more than one Claude account, the tool finds all profiles in your home directory:

```
~/.claude        -> claude (default)
~/.claude-max    -> claude-max
~/.claude-pro    -> claude-pro
```

Pick which one to wake. The choice is saved in `~/scripts/claude-wake.json`.

## Example log

```
[2026-03-16 06:00:00] Waking up and starting Claude...
[2026-03-16 06:00:27] OK - Response: Hi! How can I help you today?
[2026-03-16 06:00:57] Going back to sleep...
```

Logs are saved to `~/scripts/claude-wake.log`.

## Requirements

- Windows 10 or 11
- Claude Code CLI installed ([how to install](https://docs.anthropic.com/en/docs/claude-code/overview))
- Node.js 18+
- Sleep mode enabled (not Hibernate, the PC needs to be in S3 sleep for wake timers)

## Troubleshooting

**PC doesn't wake up?**
- Go to Power Options > Advanced > Sleep > Allow wake timers > Enable
- Some BIOS settings block wake timers. Look for "Wake on RTC" or "RTC Alarm" in your BIOS

**Claude command not found?**
- Install it: `npm install -g @anthropic-ai/claude-code`
- The tool checks `~/.local/bin/claude.exe` and your PATH

**PC doesn't go back to sleep?**
- The task might need admin privileges
- Run `powercfg /requests` to see if something is blocking sleep

## License

MIT
