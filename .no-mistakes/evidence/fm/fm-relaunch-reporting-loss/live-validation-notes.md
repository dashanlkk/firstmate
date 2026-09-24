# Test-step notes: fm/fm-relaunch-reporting-loss

Base: d1ce6b6c4f0440ff9e3c0bdd4969caffbf8f107f
Target: bc6221da4f38a091f684c1d33f3c4426ae1cc289

## Change scope (git diff --stat)
 docs/agent-control.md | 4 ++++
 1 file changed, 4 insertions(+)

## Diff
diff --git a/docs/agent-control.md b/docs/agent-control.md
index ee6f292..54ead36 100644
--- a/docs/agent-control.md
+++ b/docs/agent-control.md
@@ -83,6 +83,10 @@ It is not deterministic across the verified adapters: codex, grok, gemini, and d
 
 Switching harness is therefore one ordinary relaunch rather than a separate mechanism.
 
+Per-task activity reporting is bound by that launch rather than held in the task record: `bin/fm-spawn.sh` writes the busy generation together with that harness's wiring, and [`bin/fm-control-lib.sh`](../bin/fm-control-lib.sh) owns which paths each form leaves behind.
+Wiring the harness reads back for itself out of the worktree outlives a passive reopen, while wiring carried on the launch command - an extension flag, an injected settings file, a notifier - reaches only the process that command started, so an agent started without replaying it reports nothing at all while its work continues normally and current-state reads then have only the process and the terminal left to go on.
+There the repair is a `relaunch` and never a passive reopen, and a per-task extension file's mtime is the launch timestamp rather than evidence that reporting is alive.
+
 ### Reclaiming a task whose endpoint is gone
 
 A Herdr pane or workspace can be destroyed out from under a live task by churn or a session restart.

## Read-only cross-check that the new prose matches the code it cites
- bin/fm-spawn.sh and bin/fm-control-lib.sh exist; docs/herdr-backend.md exists.
- bin/fm-control-lib.sh owns per-harness wiring paths:
337:fm_control_harness_wiring_paths() {  # <harness> <worktree> <state-dir> <id>
341:    claude) printf '%s\n' "$wt/.claude/settings.local.json" ;;
343:    pi|pi-signed) printf '%s\n' "$state/$id.pi-ext.ts" ;;
375:# fm_control_harness_wiring_paths. Prints the registry path or nothing.

## Why no live scenario
Docs-only diff (4 inserted prose lines in docs/agent-control.md). No executable behavior changed, so there is no runtime product surface to drive. The invariant the prose records lives in unchanged bin/fm-spawn.sh relaunch wiring.
