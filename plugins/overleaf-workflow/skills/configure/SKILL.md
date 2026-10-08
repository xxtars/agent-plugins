---
name: configure
description: Configure Overleaf project for local editing. Use when user says "set up overleaf", "connect overleaf", or starts working with an Overleaf project. Do NOT use for cluster config — that's /csc.fi-workflow:configure.
---

# Configure Overleaf Project

Set up an Overleaf project for local git-based editing.

## Steps

1. Reuse details already supplied. Ask only for missing information:
   - **Overleaf git URL**: `https://git.overleaf.com/<project_id>` (found in Overleaf → Menu → Git)
   - **Target venue** (optional): e.g., NeurIPS, ICML, CVPR — affects page limits and style guidance

2. **Validate the URL**: Must match the pattern `https://git.overleaf.com/<project_id>` where `<project_id>` is a 24-character hex string. If the URL doesn't match:
   - If it looks like a regular Overleaf URL (`https://www.overleaf.com/project/...`), extract the project ID and convert to git URL
   - Otherwise, warn the user and ask them to find the correct URL in Overleaf → Menu → Git

3. Clone the Overleaf repo using the host's supported Git and credential flow. If interactive authentication is required and unavailable to the agent, have the user complete that step:
   ```
   git clone https://git.overleaf.com/<project_id> overleaf/<project_id>
   ```

4. Add `overleaf/` to the main project's `.gitignore` if not already there — the overleaf repo is independent from the main project git.

5. Reuse the project's existing Overleaf section in `AGENTS.md` or `CLAUDE.md`. If none exists, record the configuration in `AGENTS.md`. Keep one configuration source:
   ```markdown
   ## Overleaf
   - Path: `overleaf/<project_id>/`
   - Venue: <venue>
   ```

6. Verify by listing the tex files:
   ```bash
   ls overleaf/<project_id>/*.tex overleaf/<project_id>/sections/*.tex
   ```

## Notes
- The overleaf directory is a **separate git repo** — never mix its git operations with the main project
- Overleaf git auth may require a token: https://www.overleaf.com/user/settings → Git Integration
- Do not save credentials in project instructions, command examples, or remote URLs. Use the supported credential flow.
