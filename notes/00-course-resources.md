# Course Resources & Setup

> Quick reference for all course links, installation steps, and repo URLs.

---

## Claude Code Installation

```bash
# 1. Install Node.js (if not already installed)
# https://nodejs.org/

# 2. Install Claude Code globally
npm install -g @anthropic-ai/claude-code

# 3. Launch: navigate to your project folder, then:
claude
```

**VS Code:** Type `claude` in the integrated terminal — extension auto-installs.

> If `code` command not found: `Cmd+Shift+P` → "Shell Command: Install 'code' command in PATH"

**Docs:**
- [Installation Guide](https://docs.anthropic.com/en/docs/claude-code/overview)
- [Windows Setup](https://docs.anthropic.com/en/docs/claude-code/overview#windows-setup)
- [IDE Integrations](https://docs.anthropic.com/en/docs/claude-code/ide-integrations)

---

## Official Course Repositories

| Resource | Repo | Used In |
|----------|------|---------|
| RAG Chatbot (starting) | [starting-ragchatbot-codebase](https://github.com/https-deeplearning-ai/starting-ragchatbot-codebase) | Lessons 2–6 (fork this) |
| RAG Chatbot (after L5) | [ragchatbot-codebase](https://github.com/https-deeplearning-ai/ragchatbot-codebase) | Reference after Lesson 5 |
| E-Commerce Data (L7) | [sc-claude-code-files/lesson7_files](https://github.com/https-deeplearning-ai/sc-claude-code-files/tree/main/lesson7_files) | Lesson 8 (notebook + data) |
| Figma → FRED Dashboard | [FRED-dashboard](https://github.com/https-deeplearning-ai/FRED-dashboard) | Lesson 9 (final app) |
| Course Files & Prompts | [sc-claude-code-files](https://github.com/https-deeplearning-ai/sc-claude-code-files) | All lessons (notes, prompts, summaries) |
| Figma Mockup | In `sc-claude-code-files/additional_files/` | Lesson 9 (open with Figma Desktop) |

---

## Course Files Breakdown (`sc-claude-code-files`)

```
sc-claude-code-files/
├── reading_notes/           # Detailed lesson notes with prompts
├── updated_reading_notes/   # Lesson summaries & prompts doc
├── lesson7_files/           # E-commerce notebooks + data + dashboard
├── additional_files/        # L1 visualization + Figma mockup binary
└── links_to_course_repos.md # Links to all course repos
```

---

## Useful Documentation Links

- [Claude Code Overview](https://docs.anthropic.com/en/docs/claude-code/overview)
- [Common Workflows](https://docs.anthropic.com/en/docs/claude-code/common-workflows)
- [Best Practices (Anthropic Blog)](https://www.anthropic.com/engineering/claude-code-best-practices)
- [How Anthropic Teams Use Claude Code](https://www.anthropic.com/news/how-anthropic-teams-use-claude-code)
- [Claude Code in Action (Anthropic Academy)](https://anthropic.skilljar.com/claude-code-in-action)

---

## Cost

- **Pro/Max subscription** — Pro is sufficient for all lesson activities
- **API usage** — Check cost with `/cost` command in any session
