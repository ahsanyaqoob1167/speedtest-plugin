# speedtest — User Guide

A short, practical guide to using the `speedtest` Claude Code plugin.

## 1. Install

Open Claude Code and run:

```
/plugin marketplace add ahsanyaqoob1167/speedtest-plugin
/plugin install speedtest
```

**Restart Claude Code** after the first install so the skills directory is picked up. You only need to restart once.

To restart, exit your current session and relaunch:

```
/exit
```

Then from your terminal:

```
claude
```

(Or just close and reopen the Claude Code window / tab.)

To confirm it's installed:

```
/plugin list
```

You should see `speedtest` in the output.

## 2. Run a test

In any Claude Code session, type:

```
/speedtest
```

You'll be asked for a short label — something like `Home WiFi`, `Office 5G`, `Jazz device`, or `Airport lounge`. The label is only used to tag the run in your history file so you can tell runs apart later.

Claude will then:

1. Run four diagnostics in parallel (ping, Google page load, two Cloudflare downloads).
2. Print a report table with a one-line verdict.
3. Append the run to `~/.claude/skills/speedtest/history.md`.

Total run time is typically 20–45 seconds depending on your connection.

## 3. Reading the report

The verdict line classifies the connection:

| Verdict | Criteria                                              |
|---------|-------------------------------------------------------|
| Good    | Ping avg < 100 ms, 0% loss, download > 500 KB/s       |
| OK      | Ping avg < 300 ms, ≤ 1% loss, download > 100 KB/s     |
| Poor    | Anything worse                                        |

What to look at first:

- **Packet loss > 0%** → usually a hardware or signal issue (faulty device, weak signal, interference).
- **High ping (>300 ms) with 0% loss** → carrier-side congestion or a distant backhaul; not a device problem.
- **Low download (<100 KB/s)** → could be a depleted data bundle, carrier throttling, or weak signal.

## 4. Comparing runs over time

Every run is appended to `~/.claude/skills/speedtest/history.md`. To compare, just ask:

> "Compare my last three speedtest runs"
> "Show me how Home WiFi has been performing this week"

Claude will read the history file and diff the runs for you. No separate command is needed.

You can also open the file directly:

```
open ~/.claude/skills/speedtest/history.md
```

## 5. Useful patterns

**Before calling IT / your ISP:** run `/speedtest`, label the run clearly, and share the report. It gives support a concrete starting point instead of "my internet is slow."

**Comparing two networks quickly:** run `/speedtest` on network A with label `A`, switch, run again with label `B`. Then ask Claude to compare the two runs.

**Diagnosing a flaky device:** run the test 3–4 times over a few hours with the same label. High variance between runs points to intermittent issues (overheating, power, signal drops).

## 6. Troubleshooting

**`Unknown command: /speedtest`**
Restart Claude Code — exit with `/exit`, then relaunch with `claude` from the terminal. This happens when the skills directory didn't exist before the session started.

**One of the downloads shows "timeout"**
Not a bug — on a slow link, the sustained 2 MB test may exceed the 45 s cap. The burst 1 MB result is usually still valid. Re-run if you want a cleaner number.

**Numbers vary a lot between runs**
Normal on cellular / shared WiFi. Signal and carrier load fluctuate. Run multiple times and look at the range, not a single number.

**I want to reset the history**
Delete the file:

```
rm ~/.claude/skills/speedtest/history.md
```

It will be recreated on the next run.

## 7. Uninstall

```
/plugin uninstall speedtest
```

The history file at `~/.claude/skills/speedtest/history.md` is left behind intentionally — delete it manually if you want a clean slate.
