# Contributing to SimpleContext-Agents

Thanks for wanting to contribute! Here's how to add a new agent.

## Agent Requirements

Every agent YAML must have:
- `name` — unique, lowercase, use underscores
- `description` — one clear sentence
- `triggers.keywords` — at least 5 specific keywords
- `personality.default` — clear, focused system prompt

## Submission Checklist

- [ ] Agent YAML is valid (test with `python -c "import yaml; yaml.safe_load(open('agents/your_agent.yaml'))"`)
- [ ] Name is unique and descriptive
- [ ] At least 5 trigger keywords
- [ ] Has `personality.default`
- [ ] Skills have descriptions
- [ ] Tested with SimpleContext v4.1+

## Format

Follow the existing agent files as templates. See `coding.yaml` for a full example.

## Questions?

Open an issue at https://github.com/zacxyonly/SimpleContext-Agents/issues
