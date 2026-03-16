<div align="center">

<h1>🤖 SimpleContext-Agents</h1>

<p><strong>Ready-to-use agent definitions for <a href="https://github.com/zacxyonly/SimpleContext">SimpleContext</a></strong><br/>
Drop a YAML file → get a fully-configured AI agent. No code needed.</p>

[![Agents](https://img.shields.io/badge/Agents-9%20ready-brightgreen?style=flat-square)](agents/)
[![Compatible](https://img.shields.io/badge/SimpleContext-v4.1%2B-blueviolet?style=flat-square)](https://github.com/zacxyonly/SimpleContext)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](LICENSE)

</div>

---

## 📦 Available Agents

| Agent | Description | Best For |
|---|---|---|
| 🖥️ [coding](agents/coding.yaml) | Expert programmer for all languages | Code, debug, review, refactor |
| 🚀 [devops](agents/devops.yaml) | Server, deployment & infrastructure | Docker, CI/CD, Linux, cloud |
| ✍️ [writer](agents/writer.yaml) | Professional content writer | Blog, copy, email, social media |
| 📊 [analyst](agents/analyst.yaml) | Data & business analyst | Reports, KPIs, insights |
| 🎧 [customer_service](agents/customer_service.yaml) | Empathetic CS representative | Support, complaints, returns |
| 🌐 [translator](agents/translator.yaml) | Multi-language translator | Any language pair, cultural context |
| 📚 [tutor](agents/tutor.yaml) | Patient adaptive teacher | Learning any subject |
| 🔍 [researcher](agents/researcher.yaml) | Thorough fact-checker & researcher | Research, fact-checking, analysis |
| 📝 [summarizer](agents/summarizer.yaml) | Expert content condensation | Articles, reports, meetings |

---

## 🚀 Quick Install

```bash
# Clone this repo
git clone https://github.com/zacxyonly/SimpleContext-Agents.git

# Copy agents to your SimpleContext project
cp SimpleContext-Agents/agents/*.yaml your-project/agents/
```

That's it. SimpleContext hot-reloads agents — **no restart needed**.

---

## 🤔 How Agents Work

Each `.yaml` file defines a complete agent:

```
agent.yaml
├── name          → unique identifier
├── description   → what this agent does
├── triggers      → keywords that auto-route to this agent
│   ├── keywords  → list of trigger words
│   └── priority  → higher = preferred when multiple agents match
├── personality   → system prompts per user level
│   ├── default   → for all users
│   ├── beginner  → for profile.level = "beginner"
│   └── expert    → for profile.level = "expert"
├── skills        → injected into system prompt
│   ├── name      → skill identifier
│   ├── content   → instructions for this skill
│   ├── priority  → order in prompt (higher = first)
│   ├── group     → for filtering skills
│   ├── tags      → for search and filtering
│   └── conditions → only activate if profile condition met
└── chain         → handoff rules to other agents
    ├── condition → keywords that trigger handoff
    └── to        → target agent name
```

---

## 📖 Guide: Create Your Own Agent

### 1. Minimal Agent (5 lines)

```yaml
name: my_agent
description: What this agent does

triggers:
  keywords: [trigger1, trigger2]
  priority: 5

personality:
  default: You are a helpful assistant specialized in...
```

### 2. Full-Featured Agent

```yaml
name: legal_advisor
description: Legal information and document helper

triggers:
  keywords:
    - contract
    - legal
    - law
    - agreement
    - clause
    - liability
    - jurisdiction
  priority: 8

personality:
  default: |
    You are a knowledgeable legal information assistant.
    Always clarify that you provide general information, not legal advice.
    Recommend consulting a licensed attorney for specific situations.
    Use clear, plain language to explain legal concepts.

  expert: |
    You are assisting a legal professional.
    Use proper legal terminology and cite relevant principles.
    Focus on nuance, jurisdiction specifics, and edge cases.

  indonesian: |
    Kamu adalah asisten informasi hukum yang berpengetahuan.
    Selalu jelaskan bahwa ini informasi umum, bukan nasihat hukum resmi.
    Sarankan konsultasi ke pengacara untuk kasus spesifik.

skills:
  - name: disclaimer
    description: Legal disclaimer
    content: |
      Always include when relevant:
      "Note: This is general legal information, not legal advice.
      For your specific situation, please consult a licensed attorney."
    priority: 20

  - name: document_review
    description: How to review legal documents
    content: |
      When reviewing contracts or legal documents:
      1. Identify the parties involved
      2. Highlight key obligations for each party
      3. Flag unusual or potentially unfavorable clauses
      4. Note missing standard protections
      5. Summarize termination and dispute resolution terms
    priority: 10
    tags: [contract, review]

chain:
  - condition: tax OR pajak OR accounting OR akuntansi
    to: analyst
    message: For tax and accounting questions, routing to our Analyst agent.
```

### 3. Personality Levels

Set `profile.level` in user profile to activate different personalities:

```python
sc.memory(user_id).remember("level", "expert")
# Now the agent uses personality.expert instead of personality.default
```

Available by convention: `default`, `beginner`, `intermediate`, `expert`, `indonesian`, `formal`

### 4. Skill Conditions

Skills can be conditionally activated based on user profile:

```yaml
skills:
  - name: advanced_tips
    content: Advanced tips for power users...
    conditions:
      profile.level: expert          # only for expert users

  - name: multi_level_condition
    content: Content for Python experts...
    conditions:
      profile.level: expert
      profile.lang: python           # AND user uses Python
```

### 5. Agent Chaining

Automatically handoff to another agent based on message content:

```yaml
chain:
  - condition: deploy OR server OR docker    # keyword(s) in user message
    to: devops                               # target agent name
    message: Routing to DevOps agent.        # optional message to show user
```

**Important:** Chain is triggered by the **user's message**, not the LLM response.

---

## 💡 Agent Design Tips

**1. Be specific with triggers**
```yaml
# ❌ Too generic
keywords: [help, question, ask]

# ✅ Domain-specific
keywords: [contract, legal, law, clause, liability]
```

**2. Layer your personalities**
```yaml
personality:
  default: General instructions for everyone
  beginner: Simpler language, more examples, more encouragement
  expert: Skip basics, be technical, challenge assumptions
```

**3. Use skill groups**
```yaml
skills:
  - name: output_format
    group: output           # structural skills
    priority: 20            # always at top

  - name: domain_knowledge
    group: knowledge        # domain-specific skills
    priority: 10

  - name: advanced_feature
    group: advanced
    conditions:
      profile.level: expert # only load for expert users
```

**4. Set priority thoughtfully**
- `priority: 20` → always appears first (format rules, disclaimers)
- `priority: 10` → middle (domain knowledge, methodology)
- `priority: 5` → supporting details
- `priority: 1` → rarely needed extras

**5. Chain defensively**
```yaml
chain:
  # Only chain on very specific signals
  # Avoid generic words that appear in unrelated contexts
  - condition: deploy server nginx   # specific multi-word signal
    to: devops
```

---

## 🗂️ Project Structure

```
SimpleContext-Agents/
├── agents/
│   ├── coding.yaml          ← programmer expert
│   ├── devops.yaml          ← server & infrastructure
│   ├── writer.yaml          ← content & copywriting
│   ├── analyst.yaml         ← data & business
│   ├── customer_service.yaml← CS & support
│   ├── translator.yaml      ← multi-language
│   ├── tutor.yaml           ← teacher & mentor
│   ├── researcher.yaml      ← research & fact-checking
│   └── summarizer.yaml      ← content condensation
├── CONTRIBUTING.md
└── README.md
```

---

## 🤝 Contributing

Have a useful agent? Contributions welcome!

1. Fork this repo
2. Create `agents/your_agent.yaml`
3. Follow the agent format above
4. Test with SimpleContext
5. Submit a Pull Request

Please include in your PR:
- What the agent does
- Example use cases
- Trigger keywords used

---

## 🔗 Related

- **[SimpleContext](https://github.com/zacxyonly/SimpleContext)** — The AI brain this runs on
- **SimpleContext-Plugins** *(coming soon)* — Ready-to-use plugins

---

<div align="center">

**[⭐ Star SimpleContext](https://github.com/zacxyonly/SimpleContext)** · **[Report Issues](https://github.com/zacxyonly/SimpleContext-Agents/issues)**

</div>
