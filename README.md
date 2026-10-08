# agent-plugins

Reusable plugins and skills for research agents: plan experiments, run cluster
jobs, synchronize manuscripts, and archive web papers.

The workflows are written as Markdown instructions and use the tools available
in the host agent. They can be used with compatible plugin clients or adapted to
an agent that reads SKILL.md files.

## Plugins

| Plugin | What it does |
| --- | --- |
| [research-workflow](plugins/research-workflow/) | Initializes research records and updates the plan from verified evidence |
| [csc.fi-workflow](plugins/csc.fi-workflow/) | Configures CSC access, synchronizes code, manages SLURM jobs, and records results |
| [overleaf-workflow](plugins/overleaf-workflow/) | Connects an Overleaf project, synchronizes manuscript changes, and adds references |
| [web-paper-to-pdf](plugins/web-paper-to-pdf/) | Converts supported Distill-style web papers into unofficial archival PDFs |

Install the plugins you need. There is no requirement to enable the full set.

## Getting started

For a client that supports this marketplace format, add the repository
[xxtars/agent-plugins](https://github.com/xxtars/agent-plugins) as a plugin source,
then select a plugin. The marketplace identifier is xxtars-plugins.

For direct skill use, start from the selected plugin's skills/<name>/SKILL.md.
Keep the plugin's rules, templates, scripts, and assets alongside its skills;
some instructions refer to these files by relative path. A compatible agent
can then select a skill or accept a request in plain language.

Examples:

- “Initialize research records from this project discussion.”
- “Check the current jobs for this project.”
- “Synchronize this manuscript with Overleaf.”
- “Save this Distill-style paper as a PDF.”

The .claude-plugin manifests are a packaging adapter retained for compatible
clients. Installation, command syntax, credentials, and scheduling are supplied
by the host. The workflows do not assume one agent application or automatic
loading of every supporting file.

## How the workflows fit together

research-workflow defines PLAN.md, LOG.md, weekly records, and PITFALLS.md.
The cluster plugin uses these conventions to record jobs and verified outputs.
The Overleaf plugin manages a separate manuscript repository; it does not
synchronize the paper's claims with the research plan automatically.
The web-paper converter operates independently.

Project-specific settings belong in the project using the plugin. Keep access
tokens, account identifiers, local paths, research data, and personal writing
preferences out of this reusable repository.

## Requirements

Most skills need file access and Git. Cluster operations additionally need a
working SSH connection and access to the target SLURM environment. Overleaf
operations need authorized Git access to the manuscript. The PDF converter needs
Python dependencies, a browser, and a LaTeX toolchain. See each plugin's README
for its configuration and limits.

## License

Repository code and documentation are under the [MIT License](LICENSE).
The bundled NeurIPS style file retains its original third-party attribution and
applicable terms. Generated PDFs are unofficial archival copies, not publications
or renderings endorsed by the source authors. Respect the source material's
copyright and license.
