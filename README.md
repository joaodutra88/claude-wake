# claude-wake

**Optimize your Claude Code session usage — automatically wake your PC and start your timer.**

> Maximize Claude Code Max plan, optimize session windows, and never waste usage time again.

Schedule your [Claude Code](https://claude.ai/claude-code) usage window to begin at a specific time every day — even while your PC is asleep. Your computer wakes up, pings Claude to start the 5-hour session timer, and goes right back to sleep. All in under a minute, no interaction needed.

## Why?

Claude Code (Max plan) has a **5-hour rolling usage window** that resets after the timer expires. If you want your window to start before you sit down at your desk, `claude-wake` handles it automatically — wake from sleep, start the timer, sleep again.

Perfect for developers who want to **optimize Claude Code session usage** and have their session ready and waiting when they start working.

## Features

- **Wake from sleep** — Uses Windows Task Scheduler with `WakeToRun` to wake your PC at the scheduled time
- **Multi-profile support** — Auto-detects Claude profiles (claude, claude-max, claude-pro, etc.) and lets you choose which one to use
- **Go back to sleep** — After pinging Claude, the PC suspends itself back to sleep
- **Execution logs** — Every wake/ping/sleep cycle is logged with timestamps
- **Interactive CLI** — Schedule, view logs, change profile, test, or remove — all from one menu
- **Persistent config** — Saves your profile choice so you only set it up once
- **Zero dependencies** — Only Node.js built-in modules, no external packages

## Quick Start

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

1. You run `claude-wake` and pick a wake-up time + Claude profile
2. It creates a Windows Scheduled Task with **Wake to Run** enabled
3. It generates a PowerShell script that:
   - Sets `CLAUDE_CONFIG_DIR` to your chosen profile
   - Sends `"hi"` to Claude via `claude -p` (non-interactive mode)
   - Waits 30 seconds for the request to complete
   - Puts the PC back to sleep via `SetSuspendState`
4. At the scheduled time, Windows wakes from sleep, runs the script, and sleeps again

## Multi-profile support

If you have multiple Claude accounts (Max, Pro, etc.), the tool auto-detects all profiles in your home directory:

```
~/.claude        → claude (default)
~/.claude-max    → claude-max
~/.claude-pro    → claude-pro
```

Each profile uses a separate `CLAUDE_CONFIG_DIR`, so you can schedule the wake for whichever plan has the usage window you want to optimize.

## Example log

```
[2026-03-16 06:00:00] Waking up and starting Claude...
[2026-03-16 06:00:27] OK - Response: Hi! How can I help you today?
[2026-03-16 06:00:57] Going back to sleep...
```

Logs are saved to `~/scripts/claude-wake.log`.

## Requirements

- **Windows 10 or 11**
- **Claude Code CLI** installed ([installation guide](https://docs.anthropic.com/en/docs/claude-code/overview))
- **Node.js 18+**
- **Sleep mode enabled** (not Hibernate — the PC must be in S3 sleep for wake timers to work)

## Troubleshooting

**PC doesn't wake up?**
- Open Power Options → Change plan settings → Change advanced power settings → Sleep → Allow wake timers → Set to **Enable**
- Some BIOS/UEFI settings may block wake timers — check your BIOS for "Wake on RTC" or similar options

**Claude command not found?**
- Make sure Claude Code CLI is installed: `npm install -g @anthropic-ai/claude-code`
- The tool checks `~/.local/bin/claude.exe` and `PATH` automatically

**PC doesn't go back to sleep?**
- The scheduled task may need to run with elevated privileges
- Check that no other applications are preventing sleep (use `powercfg /requests` to diagnose)

## Use cases

- **Optimize Claude Code Max plan** — Start your 5-hour window before you wake up so it's ready when you sit down
- **Schedule Claude Code sessions** — Automate the session start for a consistent daily workflow
- **Maximize Claude Code usage** — Don't waste time waiting for the session to begin
- **Multi-account management** — Switch between Pro and Max profiles without reconfiguring

## License

MIT
