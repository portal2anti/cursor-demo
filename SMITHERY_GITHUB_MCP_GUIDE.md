# Smithery + GitHub MCP: Setup & Usage Guide

## Successful configuration path (what we did)

### 1. Install and log in to Smithery CLI

```bash
# One-time: log in (opens browser; use full permissions so the API key can be saved)
npx @smithery/cli@latest login
```

- Complete the browser auth at the URL shown.
- Run with **full permissions** so the CLI can write the API key (e.g. to `~/.smithery` or similar). Otherwise you may see "Failed to save API key".

### 2. Add the GitHub MCP connection

```bash
npx @smithery/cli@latest mcp add github
```

- Smithery creates a connection (e.g. `github-ownk`) to `https://server.smithery.ai/github`.
- If it says **auth_required**, open the URL it prints and authorize the GitHub app in your browser.
- After auth, the connection status becomes **connected**.

### 3. Verify the connection

```bash
npx @smithery/cli@latest mcp list
```

You should see your GitHub connection (e.g. `github-ownk`) with status `connected`.

### 4. Using it from Cursor

- **In this setup:** The GitHub MCP was **not** visible as a server inside Cursor’s MCP list (only `cursor-ide-browser` was). So we used the **Smithery CLI** from the terminal to call GitHub tools.
- **To have GitHub MCP inside Cursor:** Configure Cursor’s MCP (e.g. project `.cursor/mcp.json` or Cursor Settings → MCP) with either:
  - The official GitHub MCP server + your `GITHUB_PERSONAL_ACCESS_TOKEN`, or
  - A Smithery gateway config if your Cursor version supports it and you have the right URL/API key.

So the “successful path” was: **Smithery CLI login → add GitHub → authorize in browser → use `smithery tool call github-ownk <tool> '<args>'`** for repo/file operations.

---

## How to use it again: prompt patterns

When you want to create repos, upload files, commit, etc., you can ask in natural language and the assistant will use the Smithery CLI (or, once configured, Cursor’s GitHub MCP) to run the right tools.

### 1. Create a new repo

**Example prompts:**

- “Using Smithery / GitHub MCP, create a new repo named **my-app** with description **My cool app** and add a README.”
- “Create a GitHub repo **project-x** (private) via the GitHub MCP.”

**What gets run (conceptually):**  
`create_repository` with `name`, optional `description`, `private`, and optionally `autoInit: true` for an initial README.

---

### 2. Upload / create or update a file

**Example prompts:**

- “Using the GitHub MCP, add a file **docs/setup.md** to the repo **username/cursor-demo** with content explaining how to install the app.”
- “Upload the local file **src/config.ts** to my repo **cursor-demo** as **src/config.ts** on branch **main**.”
- “Create **README.md** in **portal2anti/cursor-demo** with the content from **cursor-demo-README.md**.”

**What gets run:**  
`create_or_update_file` with `owner`, `repo`, `path`, `content`, `message`, `branch` (and optionally `sha` when updating).

---

### 3. Commit (implicit in create/update/delete)

Commits are created by the MCP when you create/update/delete files (you provide the commit `message`). There is no separate “commit” tool.

**Example prompts:**

- “Commit a change to **README.md** in **cursor-demo** with message **Update installation steps** and this content: …”
- “Add a new file **LICENSE** to **cursor-demo** with commit message **Add MIT license**.”

---

### 4. Other useful operations

- **Create a branch:**  
  “Create a branch **feature/docs** in **owner/repo** from **main**.”  
  → `create_branch` with `owner`, `repo`, `branch`, optional `from_branch`.

- **Create a pull request:**  
  “Open a PR in **owner/repo** from **feature/docs** into **main** with title **Add docs**.”  
  → `create_pull_request` with `owner`, `repo`, `title`, `head`, `base`, optional `body`.

- **Add an issue/PR comment:**  
  “Add a comment to issue #3 in **owner/repo**: ‘Fixed in latest release.’”  
  → `add_issue_comment` with `owner`, `repo`, `issue_number`, `body`.

- **Delete a file:**  
  “Delete **old-file.txt** from **owner/repo** on branch **main** with message **Remove obsolete file**.”  
  → `delete_file` with `owner`, `repo`, `path`, `message`, `branch`.

---

## Prompt template you can reuse

Copy and adapt:

```
Using the Smithery GitHub MCP (connection github-ownk), please:

1. [Create repo / add or update file / create branch / open PR / add comment / delete file]
2. Repo: [owner/repo-name]  (e.g. portal2anti/cursor-demo)
3. [For files: path, content or “use content from file X”, branch, commit message]
4. [Any other details]

Do it via the Smithery CLI: smithery tool call github-ownk <tool> '<args>'.
```

**Short version when the context is clear:**

- “Using the GitHub MCP, add **README.md** to **cursor-demo** with [content/path] and commit message **Add README**.”
- “Via Smithery GitHub MCP: create repo **my-new-repo**, then add **README.md** with the content from **my-readme.md**.”

---

## Quick reference: Smithery CLI

| Task              | Command |
|-------------------|--------|
| List connections  | `npx @smithery/cli@latest mcp list` |
| List tools        | `npx @smithery/cli@latest tool list github-ownk` |
| Call a tool       | `npx @smithery/cli@latest tool call github-ownk <tool> '<JSON args>'` |

Your GitHub connection id from our setup: **github-ownk**. Use that in `tool list` and `tool call` unless you add another connection with a different id.
