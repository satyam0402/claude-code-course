# Claude Code: A Highly Agentic Coding Assistant

> Course notes & projects from [DeepLearning.AI](https://www.deeplearning.ai/short-courses/claude-code-a-highly-agentic-coding-assistant/) taught by **Elie Schoppik** (Head of Technical Education, Anthropic)

---

## Course Overview

Learn best practices for agentic coding with **Claude Code** — an AI assistant that can plan, execute, and improve code with minimal human input. Covers exploring codebases, adding features, testing, debugging, refactoring, MCP servers, GitHub integration, and parallel workflows.

**Duration:** ~1hr 50min | **Level:** Intermediate | **Prerequisites:** Python, Git basics

---

## Lesson Notes

| # | Lesson | Duration | Notes |
|---|--------|----------|-------|
| 1 | Introduction | 4 min | [notes/01-introduction.md](notes/01-introduction.md) |
| 2 | What is Claude Code? | 8 min | [notes/02-what-is-claude-code.md](notes/02-what-is-claude-code.md) |
| 3 | Setup & Codebase Understanding | 14 min | [notes/03-setup-and-codebase-understanding.md](notes/03-setup-and-codebase-understanding.md) |
| 4 | Adding Features | 17 min | [notes/04-adding-features.md](notes/04-adding-features.md) |
| 5 | Testing, Debugging & Refactoring | 12 min | [notes/05-testing-debugging-refactoring.md](notes/05-testing-debugging-refactoring.md) |
| 6 | Adding Multiple Features Simultaneously | 11 min | [notes/06-multiple-features-simultaneously.md](notes/06-multiple-features-simultaneously.md) |
| 7 | GitHub Integration & Hooks | 10 min | [notes/07-github-integration-and-hooks.md](notes/07-github-integration-and-hooks.md) |
| 8 | Refactoring Notebook & Creating Dashboard | 12 min | [notes/08-refactoring-notebook-dashboard.md](notes/08-refactoring-notebook-dashboard.md) |
| 9 | Creating Web App from Figma Mockup | 9 min | [notes/09-figma-web-app.md](notes/09-figma-web-app.md) |

**Quick References:**
- [Course Resources & Setup](notes/00-course-resources.md) — Installation, all repo links, docs
- [Cheatsheet](notes/cheatsheet.md) — One-page "read once, remember forever"
- [Key Commands](notes/key-commands.md) — All Claude Code commands in one place

---

## Projects

| # | Project | Lessons | Description |
|---|---------|---------|-------------|
| 1 | [RAG Chatbot](projects/01-rag-chatbot/) | 3–7 | Explore, add features, test, debug & refactor a RAG chatbot |
| 2 | [E-Commerce Dashboard](projects/02-ecommerce-dashboard/) | 8 | Refactor Jupyter notebook → interactive dashboard |
| 3 | [Figma Web App](projects/03-figma-web-app/) | 9 | Build web app from Figma mockup using MCP servers |

---

## Key Concepts Covered

- Claude Code architecture & tool usage
- `CLAUDE.md` — persistent project memory
- Context management (files, images, escape/clear/compact)
- Planning mode & thinking mode for complex tasks
- Subagents for brainstorming
- Git worktrees for parallel Claude sessions
- GitHub Issues/PRs via Claude Code
- Hooks (pre/post tool execution)
- MCP servers (Figma, Playwright)

---

## How to Use This Repo

```bash
# Clone
git clone https://github.com/satyam0402/claude-code-course.git
cd claude-code-course

# Browse notes
ls notes/

# Explore a project
cd projects/01-rag-chatbot
```

---

## License

For educational purposes. Course content belongs to [DeepLearning.AI](https://www.deeplearning.ai/) and [Anthropic](https://www.anthropic.com/).
