# AI Prompt Library

A curated set of reusable, production-ready prompts for Laravel, Vue, and automation work. Use these as drop-in building blocks across your projects.

## What you get

- Clear roles, context, and output requirements per prompt
- Implementation-ready checklists and code snippets
- Opinionated defaults that follow Laravel 11 + Vue 3 best practices
- Lightweight Docsify UI for quick browsing and copy-paste

## How to use these prompts

1. Open a prompt from the sidebar.
2. Copy the **TASK**, **ROLE**, and **CONTEXT** sections into your AI tool.
3. Run the implementation steps exactly as listed.
4. Keep the prompt source updated as you learn—this library is your single source of truth.

## Add a new prompt

Use this template to keep everything consistent. Save each prompt as its own `.md` file and add it to the sidebar under **Prompts**.

```markdown
# TASK: <clear outcome>

# ROLE: <who the AI should act as>

## CONTEXT

- Bullet points that pin down environment, constraints, and success criteria

## IMPLEMENTATION STEPS

1. Step-by-step instructions the AI can execute
2. Include code blocks with exact paths when possible

## OUTPUT REQUIREMENTS

- What the AI must return (files, commands, summaries)
```

## Library

- [Vue Sonner Toast](toast.md)

## Quality checklist

- Outcome is unambiguous and testable
- Steps are ordered, minimal, and environment-aware
- Code snippets include paths and versions where relevant
- Tone is direct; avoid fluff
