# Buddhi skill: memory guide for AI assistants

A skill that teaches an AI assistant how to work with [Buddhi](https://askbuddhi.com), your company's shared memory, through Buddhi's connectors.

Connectors are the only way an assistant reaches company memory. This skill tells the assistant:

- which connector it has in the current session, and what to say when it has none
- to read company memory before answering, and to say where an answer came from
- what is worth adding, and what never to submit
- how to write a memory so a colleague can use it months later
- how to submit it through each connector, and how to report what happened

The assistant proposes; a person approves in Buddhi before anything becomes company memory.

## What is in it

| File | Purpose |
|---|---|
| `buddhi-memory-guide/SKILL.md` | The guide the assistant follows |
| `buddhi-memory-guide/connectors.md` | Setup for each connector. The assistant reads it only when someone asks how to connect, or nothing is connected |

## Install

**Claude Code** — copy the folder into your skills directory:

```
# for you, in every project
cp -r buddhi-memory-guide ~/.claude/skills/

# or for one project, shared with the team through the repo
cp -r buddhi-memory-guide <project>/.claude/skills/
```

**Claude on the web or desktop** — zip the `buddhi-memory-guide` folder and add it under Settings → Capabilities → Skills.

Then ask Claude what the company knows about something, or to remember something for the team. You can also invoke it directly with `/buddhi-memory-guide`.

## The connectors

The skill is the guidance. It still needs a connector to act:

| Connector | For | Reads | Proposes |
|---|---|---|---|
| Buddhi tools (MCP connection) | Claude, Cursor, Windsurf, VS Code and other MCP apps | Yes | Yes |
| Claude Code plugin | Claude Code. Includes the connection, commands and reminders | Yes | Yes |
| `buddhi` command line (`@buddhi-ai/cli`) | Sets up the connection in local apps; pushes files | No | Yes |
| Product API (`/api/v1`) | Products and scripts | Yes | Yes |

Setup for each is in [`connectors.md`](buddhi-memory-guide/connectors.md).

## Using Claude Code?

The Buddhi plugin for Claude Code installs the connection, commands and reminders in one step. Use the plugin there. Add this skill as well if you want the full guide, and use it on its own for assistants that take skills but not plugins.

## License

MIT
