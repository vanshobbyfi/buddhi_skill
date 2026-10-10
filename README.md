# Buddhi skill: proposing memories

A skill that teaches an AI assistant how to get knowledge from a conversation into [Buddhi](https://askbuddhi.com), your company's shared memory: what is worth keeping, how to write it so a colleague can use it months later, and how to submit it for review.

It covers ingestion only. The assistant proposes; a person approves in Buddhi before anything becomes company memory.

## Install

**Claude Code** — copy the folder into your skills directory:

```
# for you, in every project
cp -r buddhi-remember ~/.claude/skills/

# or for one project, shared with the team through the repo
cp -r buddhi-remember <project>/.claude/skills/
```

**Claude on the web or desktop** — zip the `buddhi-remember` folder and add it under Settings → Capabilities → Skills.

Then ask Claude to remember something for the team, or invoke it directly with `/buddhi-remember`.

## What it needs

One of these, to actually submit:

- the Buddhi connection (its `submit_memory_candidate` tool), or
- the `buddhi` command line: `npm install -g @buddhi-ai/cli`, then `buddhi login`.

With neither, the skill tells the assistant to say so and show what it would have proposed.

## Using Claude Code?

The Buddhi plugin for Claude Code includes this guidance together with the connection, commands and reminders, in one install. Use the plugin there, and this standalone skill for assistants that take skills but not plugins.

## License

MIT
