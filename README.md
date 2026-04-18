# speedtest — Claude Code Plugin

A Claude Code skill that runs internet speed and connection diagnostics on the current network and logs each run to a history file for future comparison.

## What it does

Invoked by typing `/speedtest` in Claude Code, the skill:

1. Runs four tests in parallel:
   - **Ping / packet loss** — 20 samples to `8.8.8.8`
   - **Google page load** — DNS, connect, and total time to load `https://www.google.com`
   - **Download (1 MB)** — burst speed from Cloudflare
   - **Download (2 MB)** — sustained speed from Cloudflare
2. Prints a clean report table with a one-line verdict (Good / OK / Poor).
3. Asks for a short label (e.g. `"Home WiFi"`, `"Office 5G"`) and appends the run to `~/.claude/skills/speedtest/history.md` so you can compare runs over time.

## Installation

This plugin is distributed via a Claude Code **marketplace**. Add the marketplace repo, then install:

```
/plugin marketplace add ahsanyaqoob1167/speedtest-plugin
/plugin install speedtest
```

Restart Claude Code after the first install so the skills directory is picked up.

## Usage

```
/speedtest
```

That's it. The skill handles the rest — asks for a label, runs the tests, prints the report, appends to history.

## Example output

```
| Metric               | Value         |
|----------------------|---------------|
| Ping min/avg/max     | 64 / 717 / 2798 ms |
| Packet loss          | 0%            |
| Google page load     | 12.6 s        |
| Download (1 MB)      | 84 KB/s       |
| Download (2 MB)      | 285 KB/s      |

Verdict: OK — no packet loss but high latency, likely carrier-side.
```

## History file

Every run is appended to `~/.claude/skills/speedtest/history.md` with an ISO timestamp and the label you provide. Ask Claude "compare my speedtest runs" and it will read the file and diff them for you.

## Requirements

- `curl` and `ping` (preinstalled on macOS and most Linux)
- Claude Code with plugin support

## License

MIT
