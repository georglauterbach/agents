## Reference

### General Instructions

- you MUST first report findings in chat before doing anything else
- you MUST NOT post, merge, implement, restart, approve, deny, comment or push anything unless the user explicitly asks.

### `git` Platform Adapters

|Platform|URI shape|Identity|Adapter File|MCP Name Matches|
|:-|:-|:-|:-|:-|
|GitLab|`https://<host>/<project_path>/-/merge_requests/<iid>`|`project_path`, `iid`|[`gitlab.md`](adapters/gitlab.md)|`*gitlab*`|
|GitHub|`https://<host>/<owner>/<repo>/pull/<number>`|`owner/repo`, `number`|[`github.md`](adapters/github.md)|`*github*`|
|Gitea|`https://<host>/<owner>/<repo>/pulls/<number>`|`owner/repo`, `number`|[`gitea.md`](adapters/gitea.md)|`*gitea*`|

When you encounter an unknown platform, you MUST tell the user and exit.

You MUST use the tools laid out in the platform adapter:

1. MCP Servers: if a ready server matches the platform, you MUST use it for all forge API operations; you MUST NOT use a CLI, `curl`, or a browser while that MCP is available.
2. Else, Platform CLI: if installed, you MUST use it.
3. Else: you MUST stop and ask whether to proceed with basic tools (WebFetch, `curl`); you MUST NOT scrape unprompted.

If the forge denies access, you MUST stop and tell the user. You MUST NOT work around allowlists.
