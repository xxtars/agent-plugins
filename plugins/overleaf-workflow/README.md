# overleaf-workflow

Work on an Overleaf manuscript locally, synchronize it through Git, and maintain
its bibliography. The workflow uses the host agent's file and Git tools.

## Skills

| Skill | Use it to |
| --- | --- |
| [configure](skills/configure/SKILL.md) | Connect an Overleaf project for local editing |
| [sync](skills/sync/SKILL.md) | Push manuscript changes or pull collaborator edits |
| [add-ref](skills/add-ref/SKILL.md) | Add a checked BibTeX entry without overwriting an existing citation |

## Project layout

Keep the manuscript as a separate repository inside the working project:

    project/
    ├── AGENTS.md
    ├── .gitignore
    └── overleaf/
        └── <project_id>/
            ├── main.tex
            ├── sections/
            └── references.bib

Record the manuscript path and optional target venue in the project's instruction
file. Reuse an existing Overleaf section in AGENTS.md or CLAUDE.md; keep one
source of configuration. Ignore the nested overleaf/ directory in the parent
repository so manuscript files are not accidentally committed twice.

## Typical use

1. Provide the Overleaf project URL and ask the agent to configure local editing.
2. Authenticate through the supported Git credential flow if access is not already set up.
3. Edit the manuscript, then ask for a push or pull.
4. Provide a paper title, source URL, or BibTeX entry when adding a reference.

Git access depends on the project's Overleaf access and account features. See
[Overleaf Git integration](https://www.overleaf.com/learn/how-to/Git_integration)
for the service's current setup instructions. Do not place access tokens in
project files, repository URLs, or examples.

## Synchronization and references

All manuscript Git operations run inside the nested repository. Review local
changes, preserve collaborator work, and stage only the intended files. Conflicts
must be resolved before reporting a successful synchronization.

Reference insertion checks titles, author/year information, and DOI for
duplicates. Citation keys follow the existing bibliography and must not silently
replace another entry. Prefer verified publication metadata and check BibTeX
syntax before adding it.

## Writing guidance

Use the author's project instructions and the target venue's current requirements.
This plugin does not prescribe personal prose preferences, fixed section lengths,
or a universal page-limit table. Editing or synchronizing a manuscript does not
itself verify its scientific claims.

See the [repository README](../../README.md) for installation and
[LICENSE](LICENSE) for the MIT terms.
