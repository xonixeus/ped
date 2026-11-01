# PED Setup Guide

**For the operator.** How to run the Product & Engineering Department on [Multica](https://github.com/multica-ai/multica) and add a product. Each step says how to check it worked.

Placeholders: `<owner>/<product>` is a product repository on GitHub, `<home>` is the home directory of the runtime user, and `<agent-id>` is an agent's Multica ID. Commands are examples for Ubuntu or Debian and Claude Code; adapt them to your runtime.

## Part A — One-time setup

### A1. Runtime

Run the agents on a Multica runtime that supports the `multica` CLI inside tasks and per-agent environment variables (Claude Code works).

All agents run as one OS user on the runtime machine. That user must not have Docker access or passwordless root, because either would let any agent bypass the credential separation below.

**Check:** as the runtime user, `id` lists no `docker` group and `sudo -n true` fails.

### A2. Turn off claude.ai connectors

Otherwise every agent inherits the claude.ai connectors of the subscription's owner (for example, access to their documents, with create and delete). In Claude Code, add this to the runtime user's `~/.claude/settings.json`:

```json
{ "disableClaudeAiConnectors": true }
```

or set the environment variable `ENABLE_CLAUDEAI_MCP_SERVERS=false` for the runtime.

**Check:** an issue asking any agent to list its MCP tools returns none apart from those attached to it in Multica (A7).

### A3. Commit attribution (optional)

The runtime may add its own trailers to commit messages. To keep commits attributed only to the agents' identities (A5), turn them off. In Claude Code, use the `attribution` setting in the same `settings.json`.

**Check:** a test commit made in a task has no runtime trailer.

### A4. Agents

Create the six agents and paste the **entire** contents of each file into its Instructions field:

| Agent | Instructions file | Write credential | Browser |
| --- | --- | --- | --- |
| Woz | `agents/woz/AGENTS.md` | Yes (A6) | No |
| Uma | `agents/uma/AGENTS.md` | No | Yes |
| Beck | `agents/beck/AGENTS.md` | No | No |
| Faye | `agents/faye/AGENTS.md` | No | Yes |
| Sophie | `agents/sophie/AGENTS.md` | No | No |
| Paige | `agents/paige/AGENTS.md` | No | Yes |

Each file tells its agent to read `TEAM.md` from `ped` first, so the charter does not need to be pasted. When `TEAM.md` or an `AGENTS.md` changes, paste the new version into the matching Instructions fields.

**Check:** each agent's Instructions field starts with that file's frontmatter.

### A5. Commit identities

Give each agent these variables in its agent environment, so every commit shows who wrote it and, after `git am`, who integrated it:

```json
{
  "GIT_AUTHOR_NAME": "PED Beck",
  "GIT_AUTHOR_EMAIL": "<address>",
  "GIT_COMMITTER_NAME": "PED Beck",
  "GIT_COMMITTER_EMAIL": "<address>"
}
```

Use the agent's own name in each. Set them under **Agents → *agent* → Settings → Environment**, or pass them from a file (mode 0600) when you create the agent with `multica agent create … --custom-env-file <file>`.

**Check:** a commit made in a task by Beck shows `PED Beck` as author; after Woz applies it with `git am`, the author is still Beck and the committer is Woz.

### A6. Woz's write credential

Create a fine-grained GitHub token with:

- **Repositories:** `ped` and the product repositories only.
- **Permissions:** **Contents: Read and write**. No **Workflows** permission, so no agent can change CI or release workflows, and no **Administration** permission.

Store it only in Woz's agent environment, as `GH_TOKEN`. Then give the runtime user a credential helper that supplies it to git only when `GH_TOKEN` is set, so the same git configuration works for every agent and only Woz can authenticate:

```
git config --global credential.https://github.com.helper \
  '!f() { test "$1" = get && test -n "$GH_TOKEN" && printf "username=x-access-token\npassword=%s\n" "$GH_TOKEN"; }; f'
```

Do not store the token anywhere else: not in a file, the global environment or a credential store.

**Check:** in tasks on a test issue,

- Woz can push a test branch to a product repository (then ask it to report the branch so you can delete it; agents never delete branches);
- a push by Woz that changes a file in `.github/workflows/` is refused;
- a push by any specialist fails.

### A7. Browser for Faye, Uma and Paige

Faye, Uma and Paige check running interfaces in a browser. As the runtime user, install the Playwright MCP server at a pinned version and its Chromium; install Chromium's system libraries with sudo:

```
sudo apt install nodejs npm
mkdir -p ~/.local/share/playwright-mcp && cd ~/.local/share/playwright-mcp
npm init -y && npm install --save-exact @playwright/mcp@<version>
sudo node_modules/.bin/playwright install-deps chromium
node_modules/.bin/playwright install chromium
```

Run it headless and isolated (a fresh profile for every run, with no saved logins). Attach it from a JSON file (mode 0600):

```json
{"mcpServers":{"playwright":{"command":"<home>/.local/share/playwright-mcp/node_modules/.bin/playwright-mcp","args":["--headless","--isolated","--browser","chromium","--output-dir","<home>/.cache/playwright-mcp-output"]}}}
```

```
multica agent update <faye-id> --mcp-config-file browser-mcp.json
multica agent update <uma-id> --mcp-config-file browser-mcp.json
multica agent update <paige-id> --mcp-config-file browser-mcp.json
```

Do not attach it to Woz, which holds the write credential, or to Beck and Sophie, who do not need it.

**Check:** an issue asking Faye to list its `mcp__playwright` tools and open a public web page returns the page's title; the same question to Woz lists no browser tools.

## Part B — Add a product

### B1. Read access

Every agent reads the product repository over SSH with a read-only deploy key:

1. Create one key pair per product repository on the runtime machine, for example `<home>/.ssh/deploy_<product>`, and add its public key to the repository as a deploy key **without** write access.
2. GitHub accepts any registered deploy key at login and only then checks the repository, so with several deploy keys SSH must offer only the key that matches the repository being fetched. Use a small wrapper as git's SSH command that picks the key by repository name, for example `<home>/.local/bin/git-ssh-deploy`:

   ```sh
   #!/bin/sh
   # git calls: <wrapper> [options] git@github.com "git-upload-pack '<owner>/<product>.git'"
   for last; do :; done
   repo=$(printf '%s' "$last" | sed -n "s|.*'[^/]*/\([^/']*\)\.git'.*|\1|p")
   exec ssh -o IdentitiesOnly=yes -i "$HOME/.ssh/deploy_$repo" "$@"
   ```

   ```
   chmod 755 ~/.local/bin/git-ssh-deploy
   git config --global core.sshCommand ~/.local/bin/git-ssh-deploy
   ```

3. Send pushes for the repository over HTTPS, where only Woz has a credential (A6):

   ```
   git config --global url."https://github.com/<owner>/<product>.git".pushInsteadOf "git@github.com:<owner>/<product>.git"
   ```

`ped` is public and is read over HTTPS without a credential.

**Check:** a specialist's task can clone the product repository. Then confirm the deploy key cannot write: in a clone, as the runtime user, push over SSH while bypassing the global configuration (and so `pushInsteadOf`):

```
GIT_CONFIG_GLOBAL=/dev/null git -c core.sshCommand="$HOME/.local/bin/git-ssh-deploy" \
  push git@github.com:<owner>/<product>.git HEAD:refs/heads/deploy-key-check
```

GitHub refuses it because the key is read-only, so no branch is created.

### B2. Woz's token

Add the new product repository to the repositories of Woz's token (A6).

**Check:** Woz can push a test branch to the new repository.

### B3. Project

Create a Multica project for the product, with Woz as lead, and add two resources, both on the default branch:

- the product repository, by its SSH URL (`git@github.com:<owner>/<product>.git`);
- `ped`, by its HTTPS URL (`https://github.com/<owner>/ped.git`).

Every issue for the product must belong to this project, so its agents get the right repositories.

**Check:** a task in the project lists both repositories in `.multica/project/resources.json`.

### B4. `PED.md`

Create an issue in the project, assigned to Woz, asking it to propose `product:PED.md`: the owner, documentation destinations and their review requirements, required checks, environments, and the release procedure (tag format, who deploys and how). Approve it on the issue before other work starts.

The product must make these possible, and `PED.md` records how:

- **Required checks that run the way CI runs them.** For example, if CI installs and tests a sub-package on its own, the required check does the same, so a change that passes locally also passes in CI.
- **A throwaway test database per task.** Each task starts its own database and discards it; no task shares a database. For example, install the PostgreSQL server binaries with no default cluster (on Debian or Ubuntu, write `create_main_cluster = false` to `/etc/postgresql-common/createcluster.conf` before installing the server package, so no shared server is created and nothing listens on the default port), and have each task run `initdb` into its workspace and `pg_ctl` on a random local port.
- **Dev servers configurable per task.** Ports and the API address can be set per task, so concurrent browser checks do not reach each other's servers.

**Check:** `PED.md` is in the product repository's default branch, committed by Woz after your approval on the issue.

### B5. Deployment checks

Create one read-only issue per agent in the product's project:

**Every agent (same text for each):**

> Deployment check (read-only). Quote one line each from `ped:TEAM.md`, your own `AGENTS.md` in `ped:agents/`, `product:PED.md` and the product's `README`. State your role, what you must never do, and which agents you hand off to directly. Report whether `GH_TOKEN` is set in your environment (yes or no only; never print it), your git author and committer identity, the version of the test database the product's required checks use (or that there is none), and the MCP tools available to you. Make no changes.

**Faye, Uma and Paige (additionally):**

> Start the product's dev server on a port of your choice, open it with your browser tools, and report the page title and the port you used. Stop the server when you are done.

**Check:** every agent quotes real lines from the right files; only Woz reports `GH_TOKEN` set; each identity matches A5; only Faye, Uma and Paige list browser tools, and their browser checks reach their own server.
