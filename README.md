## A `PreToolUse` hook is not a gate

Grok Build 1.0.34, default permissions, allow rules for the Python interpreter only, one fixture hook in front of a shell command that writes a file (the operator's own fleet hook was also in the chain and allowed each time). Five runs, 2026-09-19:

| fixture | hook did | tool fired? | should have been | harness log line |
|---|---|---|---|---|
| deny (control) | well-formed deny, exit 2 | **blocked** | NO-GO | `gate hook blocked … NO-GO` |
| timeout | slept 8s against a 5s limit | **FIRED** | NO-GO | `gate hook failed; ignoring (fail-open) … timed out after 5000ms` |
| crash | raised, exit 1 | **FIRED** | NO-GO | `gate hook failed; ignoring (fail-open) … exit code 1` |
| malformed, exit 0 | truncated deny JSON, exit 0 | **FIRED** | NO-GO | `hook allowed` — documented: exit 0 allows |
| malformed, **exit 2** | truncated deny JSON, the deny code | **FIRED** | NO-GO | `gate hook failed; ignoring (fail-open) … exit code 1` |

Timeout and crash fail open as xAI documents — *"timeouts, crashes, malformed output — is fail-open."* The last row is the one the docs don't mention: a deny that exited 2, the documented deny code, with truncated stdout. The harness saw exit 1, had no JSON to parse, and let the tool fire. Windows, Grok Build 1.0.34; other platforms unconfirmed. (Claude Code's docs say the same about stalled hooks — *"don't count on a stalled hook to act as a gate"* — but nothing here was run against Claude Code.)

So the gate can't live in the hook. It lives in the mutator, and **no card is a NO-GO**.

Run the fixtures: [operator-gates/fixtures/grok-build](https://github.com/GFB2026/operator-gates/tree/main/fixtures/grok-build) — `python run.py`, one minute, redacted traces included. If the card is useful, license it. I am not applying.

---

# Hi, I'm Greg

I run companies with agents in production. Mail, desk, money — a human still owns send and spend. New York.

VP at [Insurance Licensing Institute](https://ili-li.com/). Also [GFB](https://gfbytes.com/) (studio), [Higher Hosting](https://higher-hosting.com/) (insurance brokerage), and [Frank Freda Medicare](https://frankfredamedicare.com/).

The hard part isn't the model. Agents already touch customers and charges. If HITL never pauses the loop, it's a notification.

## At a glance

- 4 companies operating in production
- 5 public products spanning licensing, compliance, and underwriting workflows
- 1 rule: a human approves send and spend before the loop continues
- Public repo = signal, not private operations or customer records

## What I actually run

**Companies:** [ILI](https://ili-li.com/) · [GFB](https://gfbytes.com/) · [Higher Hosting](https://higher-hosting.com/) · [Frank Freda Medicare](https://frankfredamedicare.com/)

**Products:** [BillWatch](https://billwatch.health/) · [OK To Open](https://oktoopen.com/) · [Part 500](https://part500filing.com/) · [ListingQC](https://listingqc.com/) · [Autark](https://tryautark.com/)

Named list: [gregfredabytes.com/running](https://gregfredabytes.com/running/)

## On GitHub

This account is a shop window, not an archive of the companies. Three public repos, pinned. Production internals stay private — student records, mail, money, client work, live ops. Public scars and patterns live here and on [gregfredabytes.com](https://gregfredabytes.com/).

- [operator-gates](https://github.com/GFB2026/operator-gates) — fail-closed GO/NO-GO cards before send, money, or outreach. Four replayable production scars and the fixture above. Reference code, not a product.
- [mcp-oauth-connect](https://github.com/GFB2026/mcp-oauth-connect) — MCP remotes that pass `curl` and then die in Claude, Cursor, Desktop, or Grok Connectors. Free checker: [check.gfbytes.com](https://check.gfbytes.com/). Official Registry probe: 21/25 remotes failed connector-critical checks.
- [mcp-gfbytes](https://github.com/GFB2026/mcp-gfbytes) — the web UI for that checker

I write the scars at [gregfredabytes.com](https://gregfredabytes.com/). I talk about this at [@gregfredabytes](https://x.com/gregfredabytes).
