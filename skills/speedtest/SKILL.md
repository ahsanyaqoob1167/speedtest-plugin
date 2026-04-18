---
name: speedtest
description: Run internet speed and connection diagnostics (ping, packet loss, page load, download speed) on the current network, print a report, and append it to a history file for future comparison. Use when the user asks to test internet speed, run a speed test, check connection quality, or diagnose a WiFi/network issue.
---

# Internet Speed Test

Runs the standard diagnostic suite Ahsan used for the Jazz WiFi device: ping, packet loss, Google page load, and Cloudflare download speeds. Prints a clean report and appends it to a history log so future runs can be compared.

## Steps

### 1. Run the diagnostics

Run these four commands in parallel (single message, four Bash tool calls):

```bash
# Ping / packet loss (20 samples, 0.5s interval)
ping -c 20 -i 0.5 8.8.8.8 2>&1 | tail -3
```

```bash
# Google page load timing
curl -o /dev/null -s -w "DNS: %{time_namelookup}s | Connect: %{time_connect}s | Total: %{time_total}s | Size: %{size_download} bytes\n" https://www.google.com
```

```bash
# 1 MB download test (burst speed)
curl -o /dev/null -s -w "Download: %{speed_download} bytes/sec | Time: %{time_total}s\n" "https://speed.cloudflare.com/__down?bytes=1000000" --max-time 30
```

```bash
# 2 MB download test (sustained speed)
curl -o /dev/null -s -w "Download: %{speed_download} bytes/sec | Time: %{time_total}s\n" "https://speed.cloudflare.com/__down?bytes=2000000" --max-time 45
```

### 2. Parse and present the report

Extract these fields and print a table:

| Metric | Value |
|---|---|
| Ping min / avg / max | from ping output `round-trip min/avg/max/stddev` |
| Packet loss | from ping output `N.N% packet loss` |
| Google page load | `Total` from Google curl |
| Download 1MB | `speed_download` converted to KB/s |
| Download 2MB | `speed_download` converted to KB/s |

Convert `bytes/sec` to `KB/s` by dividing by 1024.

After the table, give a one-line verdict on the connection quality:
- **Good**: ping avg < 100ms, 0% loss, download > 500 KB/s
- **OK**: ping avg < 300ms, ≤1% loss, download > 100 KB/s
- **Poor**: anything worse

### 3. Append to history

Append the run to `~/.claude/skills/speedtest/history.md`. Create the file with a header on first run. Each entry should look like:

```markdown
## {ISO timestamp} — {short label, ask user for it: e.g. "Jazz WiFi, home", "Office WiFi"}

| Metric | Value |
|---|---|
| Ping min/avg/max | ... |
| Packet loss | ... |
| Google page load | ... |
| Download 1MB | ... KB/s |
| Download 2MB | ... KB/s |

Verdict: {Good/OK/Poor — one-line reasoning}

---
```

Before appending, ask the user for a short label identifying this run (network name, location, or device). Keep the prompt brief — one line.

### 4. Confirm

Tell the user the report was saved to `~/.claude/skills/speedtest/history.md` and note how many total runs are now logged.

## Notes

- If any command times out or fails, record "timeout" or "failed" in the table for that metric rather than aborting. A partial report is still useful.
- Don't try to be clever — just run the four tests, print the numbers, append to history, done. No auto-comparison against prior runs unless the user asks.
- Use `date -u +"%Y-%m-%dT%H:%M:%SZ"` to get the ISO timestamp for the history entry.
