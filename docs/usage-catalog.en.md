[日本語](usage-catalog.md) | English

# Reverse Lookup: What You Can Do (How-to-Ask Catalog)

You can operate Fugaku **just by talking to Claude Code in plain English**. Below are example phrases by task, along with the tools used behind the scenes and additional notes. Anything inside `「」` is something you can say verbatim.

## Status / Your Own Information
| What you want to do | Example phrase | Tool / Notes |
|---|---|---|
| Whether Fugaku is up | "Show me Fugaku's status" | `cluster_status` |
| Check your account | "Show me my account info on Fugaku" | `account_info` (prevents mix-ups) |
| Check your disk usage | "Check my home directory usage on Fugaku" | `run_command` (`du`/`quota`) |

## Jobs
| What you want to do | Example phrase | Tool / Notes |
|---|---|---|
| Run + collect results (recommended) | "Run `hostname; lscpu` on Fugaku and show me the results" | `run_job` (submit → wait for completion → collect, all in one) |
| Specify node count / time | "Run XXX on 2 nodes for 10 minutes" | Can be specified within the resource-limit policy |
| Specify a group | "Submit it under the XXX group" | Specifies the billing group |
| List running jobs | "Are any jobs running right now?" | `list_jobs` |
| Finished jobs | "Show me my recently finished jobs" | `list_jobs(completed=true)` (last 24h) |
| Status of a specific job | "What's the status of job 12345?" | `job_status` |
| Retrieve results later | "Show me the results of that XXX from earlier" | `fetch_result` (`<name>.result`) |
| Cancel a job | "Cancel job 12345" | `cancel_job` |
| Long-running jobs | "Submit a 3-hour job and let me know when it's done" | If the wait exceeds the limit, a jobid is returned → check status later |

## Files
| What you want to do | Example phrase | Tool / Notes |
|---|---|---|
| Local → Fugaku | "Put this script in `~/work/` on Fugaku" | `stage_in` (overwrites existing files) |
| Create content directly | "Create a file on Fugaku called `run.sh` with this content" | `stage_in` (with content) |
| Fugaku → local / display | "Show me `~/work/out.log` on Fugaku" | `stage_out` |
| List | "Show me what's in `~/work/` on Fugaku" | `run_command` (`ls`) |

## Commands (Lightweight Work on the Login Node)
| What you want to do | Example phrase | Tool / Notes |
|---|---|---|
| Checks, builds, etc. | "Run `module avail` on Fugaku" / "Run `make`" | `run_command` (within the safety policy) |

> Note: `run_command` is for lightweight commands **on the login node**. Always run heavy computation as a job (`run_job`).
> Destructive or dangerous commands are rejected by the safety measures ([security.en.md](security.en.md)).

## Combined Examples (the Agent Handles Multiple Steps Automatically)
- "Send this code to Fugaku, build it, run it on 1 node, and show me the results"
  → Runs `stage_in` → (build via `run_command`) → `run_job` → collect results, all as one sequence.
- "Look at the log of the failed job, fix the cause, and resubmit it"
  → Check with `fetch_result`/`stage_out` → fix → resubmit with `run_job`.

## Good to Know
- **Where results live**: Output from `run_job` is consolidated under `~/mcp-jobs/<name>.result` on Fugaku.
- **How completion appears**: On Fugaku, completed jobs disappear from the run queue, and the final state appears in the history (sacct, last 24h) (the tool detects this automatically).
- **Submission takes a little time** (a few tens of seconds, since execution is synchronous on the Fugaku side). `run_job` waits for it.
