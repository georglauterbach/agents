# [Cursor CLI](https://cursor.com/cli)

## Commands

| Area          | Command                 | Explanation & Remarks                                 |
| :------------ | :---------------------- | :---------------------------------------------------- |
| Current Chat  | `/model <FILTER>`       | select the model                                      |
| Current Chat  | `/run-everything`       | run all commands without asking first                 |
| Current Chat  | `/plan`                 | create or show existing plan                          |
| Current Chat  | `/max-mode`             | enable MAX mode                                       |
| Current Chat  | `/fast`                 | enable fast mode                                      |
| Current Chat  | `/fork`                 | for the current chat                                  |
| Current Chat  | `/summarize`            | summarize chat to reduce context                      |
| Current Chat  | `/rewind`               | jump to a previous message                            |
| Current Chat  | `/best-of-n`            | use n models and compare results                      |
| Current Chat  | `/context`              | show context information                              |
| Current Chat  | `/jobs`                 | open active task list                                 |
| Current Chat  | `/sandbox`              | toggle the sandbox                                    |
| Current Chat  | `/copy-request-id`      | copy last request ID                                  |
| Current Chat  | `/copy-conversation-id` | copy current conversation ID                          |
| Miscellaneous | `/btw`                  | ask a side question without disrupting the main chat  |
| Miscellaneous | `/shell <CMD>` / `!`    | run a command in a shell                              |
| Miscellaneous | `/mcp <CMD>`            | manage MCPs                                           |
| Miscellaneous | `/update`               | update the CLI version                                |
| Miscellaneous | `/about`                | show information about the CLI, system, account, etc. |
| Git           | `/commit`               | stage and commit changes                              |
| Git           | `/changes`              | review changes                                        |
| Git           | `/simplify`             | simplify current changes                              |
| Git           | `/review-bugbot`        | review changes with bugbot subagent                   |
| Git           | `/review-security`      | review changes with security review subagent          |
| Git           | `/review`               | review changes with bugbot or security                |
| Git           | `/babysit`              | keep a PR merge-ready                                 |
| Visuals       | `/line-numbers`         | toggle line numbers in code blocks                    |
| Visuals       | `/show-thinking`        | toggle thinking block display                         |
| Visuals       | `/status-indicators`    | toggle terminal title status indicators               |
| Visuals       | `/sync-theme`           | re-detect terminal theme & refresh UI                 |
| Visuals       | `/full-conversation`    | redraw complete conversations                         |
| Behavior      | `/rule`                 | manage rules                                          |
| Behavior      | `/skills`               | open skills menu                                      |
| Behavior      | `/create-rule`          | create a new rule                                     |
| Behavior      | `/create-skill`         | create a new skill                                    |

## Miscellaneous

### Modes

Switch between modes with `Shift+Tab`.

### Continuation of Old Chats

You can use `agent ls`, `agent resume`, `agent --continue`, or `/resume` to resume an older session.
