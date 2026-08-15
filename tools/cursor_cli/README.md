# [Cursor CLI](https://cursor.com/cli)

## Commands

|Area|Command|Explanation & Remarks|
|:-|:-|:-|
|Current Chat|`/model <FILTER>`|select the model|
|Current Chat|`/run-everything`|run all commands without asking first|
|Current Chat|`/plan`|create a plan or show an existing plan|
|Current Chat|`/ask`|toggle ask mode (read-only Q&A)|
|Current Chat|`/debug`|toggle debug mode, or submit a prompt in debug mode|
|Current Chat|`/max-mode`|enable MAX mode|
|Current Chat|`/fast`|enable fast mode|
|Current Chat|`/fork`|fork the current chat into a new session|
|Current Chat|`/clear`|start a new chat session|
|Current Chat|`/rename-chat`|rename the current chat|
|Current Chat|`/summarize`|summarize chat to reduce context|
|Current Chat|`/rewind`|jump back to a previous message|
|Current Chat|`/copy`|copy a previous message to the clipboard|
|Current Chat|`/best-of-n`|use n models and compare results|
|Current Chat|`/goal`|start a durable goal that continues while idle|
|Current Chat|`/context`|show context information|
|Current Chat|`/jobs`|open active task list|
|Current Chat|`/sandbox`|toggle the sandbox|
|Current Chat|`/copy-request-id`|copy last request ID|
|Current Chat|`/copy-conversation-id`|copy current conversation ID|
|Miscellaneous|`/btw`|ask a side question without disrupting the main chat|
|Miscellaneous|`/shell <CMD>` / `!`|run a command in a shell|
|Miscellaneous|`/mcp <CMD>`|manage MCP servers (list, list-tools)|
|Miscellaneous|`/update`|update the CLI version|
|Miscellaneous|`/about`|show information about the CLI, system, account, etc.|
|Miscellaneous|`/logout`|sign out from Cursor|
|Miscellaneous|`/exit` / `/quit`|leave the CLI|
|Git|`/commit`|stage and commit changes|
|Git|`/changes`|review changes|
|Git|`/simplify`|simplify current changes|
|Git|`/review-bugbot`|review changes with bugbot subagent|
|Git|`/review-security`|review changes with security review subagent|
|Git|`/review`|review changes with bugbot or security|
|Git|`/babysit`|keep a PR merge-ready|
|Git|`/split-to-prs`|split the current work into small reviewable PRs|
|Visuals|`/line-numbers`|toggle line numbers in code blocks|
|Visuals|`/show-thinking`|toggle thinking block display|
|Visuals|`/status-indicators`|toggle terminal title status indicators|
|Visuals|`/zen-mode`|toggle zen mode, which dims non-diff context|
|Visuals|`/sync-theme`|re-detect terminal theme & refresh UI|
|Visuals|`/full-conversation`|redraw complete conversations|
|Visuals|`/statusline`|configure the status line above the prompt|
|Behavior|`/config`|configure CLI settings interactively|
|Behavior|`/update-cli-config`|change settings in `cli-config.json`|
|Behavior|`/vim`|toggle Vim keys|
|Behavior|`/rule`|manage rules|
|Behavior|`/skills`|open skills menu|
|Behavior|`/create-rule`|create a new rule|
|Behavior|`/create-skill`|create a new skill|
|Behavior|`/create-hook`|create a new hook|
|Behavior|`/create-subagent`|create a new subagent|
|Behavior|`/loop`|run a prompt or skill on a recurring interval|
|Behavior|`/sdk`|help with building on the Cursor SDK|

Commands such as `/review-bugbot`, `/create-rule` and `/loop` are shipped as built-in skills, so the exact set depends on the installed CLI version. `/skills` lists what the current version provides.

## Configuration

The CLI stores its settings in `~/.cursor/cli-config.json`. [`cli-config.json`](./cli-config.json) in this directory is an example to copy from; it is not read from here.

Edit it with `/config` or `/update-cli-config` rather than by hand where possible, because several slash commands write to it:

|Key|Command|
|:-|:-|
|`display.mode`|`/zen-mode`|
|`display.showLineNumbers`|`/line-numbers`|
|`display.showStatusIndicators`|`/status-indicators`|
|`display.showThinkingBlocks`|`/show-thinking`|
|`editor.vimMode`|`/vim`|
|`model`, `modelParameters`|`/model`, `/max-mode`|

## Miscellaneous

### Modes

Switch between modes with `Shift+Tab`, or jump straight to one with `/plan`, `/ask` or `/debug`.

### Continuation of Old Chats

You can use `agent ls`, `agent resume`, `agent --continue`, or `/resume` to resume an older session.
