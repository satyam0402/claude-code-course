# Project 1: RAG Chatbot — Full Walkthrough

> Explore, add features, test, debug & refactor a RAG chatbot using Claude Code (Lessons 2–6)

## Official Repos
- **Starting codebase:** [starting-ragchatbot-codebase](https://github.com/https-deeplearning-ai/starting-ragchatbot-codebase) (fork this)
- **Your fork:** [satyam0402/starting-ragchatbot-codebase](https://github.com/satyam0402/starting-ragchatbot-codebase)
- **After Lesson 5 (reference):** [ragchatbot-codebase](https://github.com/https-deeplearning-ai/ragchatbot-codebase)

## Setup

```bash
cd ~/Desktop/rag-chatbot
# Create .env file
echo "ANTHROPIC_API_KEY=your_key_here" > .env
# Install dependencies
uv sync
# Run the app
chmod +x run.sh && ./run.sh
# In another terminal, launch Claude Code:
cd ~/Desktop/rag-chatbot && claude
```

App runs at: http://localhost:8000 | API docs: http://localhost:8000/docs

---

## Lesson 2: Setup & Codebase Understanding

### Step 1: Init project memory
```
/init
```
> Creates `CLAUDE.md` — Claude reads this every session.

### Step 2: Add project-specific memories using `#`
```
# use uv to run python files or add any dependencies
```
```
# The vector database has two collections:
  - course_catalog: stores course titles for name resolution (metadata: title, instructor, course_link, lesson_count, lessons_json)
  - course_content: stores text chunks for semantic search (metadata: course_title, lesson_number, chunk_index)
```

### Step 3: Explore the codebase — try these prompts one by one

| # | Prompt | What you learn |
|---|--------|---------------|
| 1 | `Give me an overview of this codebase` | Architecture: FastAPI + ChromaDB + Claude API |
| 2 | `What are the key data models?` | Course → Lesson → CourseChunk hierarchy |
| 3 | `Explain how the documents are processed` | Doc reading → chunking → vector storage pipeline |
| 4 | `What is the format of the document expected by the document_processor?` | Lines 1-3 = metadata, then Lesson markers |
| 5 | `How are the course chunks loaded to the database?` | process_course_document → add_course_content flow |
| 6 | `Trace the process of handling user's query from frontend to backend` | Full request lifecycle: JS → FastAPI → RAG → Claude → ChromaDB → Response |
| 7 | `Draw a diagram that illustrates this flow` | Visual architecture diagram |
| 8 | `Explain how the text is transformed into chunks? What is the size of each chunk?` | 800 char chunks, 100 char overlap, sentence boundaries |
| 9 | `Describe the api endpoints` | POST /api/query, GET /api/courses |
| 10 | `How can I run the application?` | run.sh or manual uvicorn command |

### Commands learned
| Command | What it does |
|---------|-------------|
| `/init` | Create CLAUDE.md project memory |
| `#` | Quick-add a memory to CLAUDE.md |
| `/clear` | Clear conversation history |
| `/compact` | Summarize conversation to save context |
| `ESC` | Interrupt Claude to redirect |
| `ESC ESC` | Rewind conversation |
| `@filename` | Mention a file to include its content |
| `!pwd` | Run bash command inside Claude Code |
| `exit` | Quit Claude Code |

---

## Lesson 3: Adding Features

### Feature 1 — Embed links in source citations

**Mode:** Planning mode (`shift+tab` to toggle)

```
The chat interface displays query responses with source citations. I need to modify it so each source becomes a clickable link that opens the corresponding lesson video in a new tab:
- When courses are processed into chunks in @backend/document_processor.py, the link of each lesson is stored in the course_catalog collection
- Modify _format_results in @backend/search_tools.py so that the lesson links are also returned
- The links should be embedded invisibly (no visible URL text)
```

**Follow-up** (paste a screenshot with `Ctrl+V`):
```
[Ctrl + V to paste the screenshot] these links are hard to read. Make them more visually appealing.
```

### Feature 2 — Add '+ New Chat' button

First, set up Playwright MCP server:
```bash
# Exit Claude Code first, then in terminal:
claude mcp add playwright npx @playwright/mcp@latest
# Reopen Claude Code and verify:
/mcp
```

**Prompt:**
```
Add a '+ NEW CHAT' button to the left sidebar above the courses section. When clicked, it should:
- Clear the current conversation in the chat window
- Start a new session without page reload
- Handle proper cleanup on both @frontend and @backend
- Match the styling of existing sections (Courses, Try asking) - same font size, color, and uppercase formatting
```

**Follow-up** (using Playwright to visually check):
```
Using the playwright MCP server, visit 127.0.0.1:8000 and view the '+ New Chat' button. I want that button to look the same as the other links below for Courses and Try Asking. Make sure this is left aligned and that the border is removed.
```

> **Tip:** To auto-allow Playwright screenshots: `/permissions` → Add rule → Allow → `mcp__playwright__browser_take_screenshot`

### Feature 3 — Add a course outline tool

```
In @backend/search_tools.py, add a second tool alongside the existing content-related tool. This new tool should handle course outline queries.
- Functionality:
   - Input: Course title
   - Output: Course title, course link, and complete lesson list
   - For each lesson: lesson number, lesson title
- Data source: Course metadata collection of the vector store
- Update the system prompt in @backend/ai_generator so that the course title, course link, the number and title of each lesson are all returned to address an outline-related queries.
- Make sure that the new tool is registered in the system.
```

### Commands & shortcuts learned
| Shortcut | What it does |
|----------|-------------|
| `shift+tab` | Toggle planning mode ↔ auto-accept mode |
| `Cmd+Shift+Ctrl+4` | Take screenshot (Mac) |
| `Ctrl+V` | Paste screenshot into Claude Code |
| `/mcp` | Check MCP server connections & tools |
| `/permissions` | Manage tool permission rules |

---

## Lesson 4: Testing, Error Debugging & Code Refactoring

### Part 1 — Debug with tests

First, intentionally set `MAX_RESULTS: int = 0` in `backend/config.py` to create the bug.

**Prompt** (plan mode + think):
```
The RAG chatbot returns 'query failed' for any content-related questions. I need you to:
1. Write tests to evaluate the outputs of the execute method of the CourseSearchTool in @backend/search_tools.py 
2. Write tests to evaluate if @backend/ai_generator.py correctly calls for the CourseSearchTool 
3. Write tests to evaluate how the RAG system is handling the content-query related questions. 

Save the tests in a tests folder within @backend. Run those tests against the current system to identify which components are failing. Propose fixes based on what the tests reveal is broken.

Think.
```

### Part 2 — Refactor for sequential tool calling

**Prompt** (uses subagents for brainstorming):
```
Refactor @backend/ai_generator.py to support sequential tool calling where Claude can make up to 2 tool calls in separate API rounds.

Current behavior:
- Claude makes 1 tool call → tools are removed from API params → final response
- If Claude wants another tool call after seeing results, it can't (gets empty response)

Desired behavior:
- Each tool call should be a separate API request where Claude can reason about previous results
- Support for complex queries requiring multiple searches for comparisons, multi-part questions, or when information from different courses/lessons is needed

Example flow:
1. User: "Search for a course that discusses the same topic as lesson 4 of course X"
2. Claude: get course outline for course X → gets title of lesson 4
3. Claude: uses the title to search for a course that discusses the same topic → returns course information
4. Claude: provides complete answer

Requirements:
- Maximum 2 sequential rounds per user query
- Terminate when: (a) 2 rounds completed, (b) Claude's response has no tool_use blocks, or (c) tool call fails
- Preserve conversation context between rounds
- Handle tool execution errors gracefully

Notes: 
- Update the system prompt in @backend/ai_generator.py 
- Update the test @backend/tests/test_ai_generator.py
- Write tests that verify the external behavior (API calls made, tools executed, results returned) rather than internal state details. 

Use two parallel subagents to brainstorm possible plans. Do not implement any code.
```

### Features learned
| Feature | When to use |
|---------|-------------|
| `think` | Complex tasks — extended thinking mode |
| `think hard` / `think harder` / `ultrathink` | Increasing thinking budget |
| Subagents | Brainstorming, investigating multiple approaches |

---

## Lesson 5: Adding Multiple Features Simultaneously

### Step 1 — Create custom slash command

```bash
# Inside your project:
mkdir -p .claude/commands
```

Create `.claude/commands/implement-feature.md`:
```
You will be implementing a new feature in this codebase

$ARGUMENTS

IMPORTANT: Only do this for front-end features.
Once this feature is built, make sure to write the changes you made to file called frontend-changes.md
Do not ask for permissions to modify this file, assume you can always do it.
```

### Step 2 — Set up git worktrees

```bash
# Commit current changes first!
git add -A && git commit -m "pre-worktree checkpoint"

mkdir .trees
git worktree add .trees/ui_feature
git worktree add .trees/testing_feature
git worktree add .trees/quality_feature
```

### Step 3 — Run Claude in each worktree (3 terminals)

**Terminal 1 — UI Feature:**
```
Add a toggle button that allows users to switch between dark and light themes.

1. Toggle Button Design
    - Create a toggle button that fits the existing design aesthetic
    - Position it in the top-right
    - Use an icon-based design (sun/moon icons or similar)
    - Smooth transition animation when toggling
    - Button should be accessible and keyboard-navigable

2. Light Theme CSS Variables
    Add a light theme variant with appropriate colors:
    - Light background colors
    - Dark text for good contrast
    - Adjusted primary and secondary colors
    - Proper border and surface colors
    - Maintain good accessibility standards

3. JavaScript Functionality
    - Toggle between themes on button click
    - Smooth transitions between themes

4. Implementation Details
    - Use CSS custom properties (CSS variables) for theme switching
    - Add a `data-theme` attribute to the body or html element
    - Ensure all existing elements work well in both themes
    - Maintain the current visual hierarchy and design language
```

**Terminal 2 — Testing Feature:**
```
Enhance the existing testing framework for the RAG system in @backend/tests. The current tests cover unit components but are missing essential API testing infrastructure:

- API endpoint tests - Test the FastAPI endpoints (/api/query, /api/courses, /) for proper request/response handling
- pytest configuration - Add pytest.ini_options in pyproject.toml for cleaner test execution
- Test fixtures - Create conftest.py with shared fixtures for mocking and test data setup

The FastAPI app in backend/app.py mounts static files that don't exist in the test environment. Either create a separate test app or define the API endpoints inline in the test file to avoid import issues.
```

**Terminal 3 — Quality Feature:**
```
Add essential code quality tools to the development workflow. Set up black for automatic code formatting. Add proper formatting consistency throughout the codebase and create development scripts for running quality checks.
```

### Step 4 — Merge all worktrees

```bash
# In each worktree, commit:
cd .trees/ui_feature && git add -A && git commit -m "feat: dark/light theme toggle"
cd .trees/testing_feature && git add -A && git commit -m "feat: API endpoint tests"
cd .trees/quality_feature && git add -A && git commit -m "feat: code quality tooling"
```

Then in Claude Code (main directory):
```
use the git merge command to merge in all the worktrees of the .trees folder into main and fix any conflicts if there are any
```

---

## Lesson 6: GitHub Integration & Hooks

### GitHub Integration
```
/install-github-app
```
> After setup, mention `@claude` in any PR or issue on GitHub.

Docs: [Claude Code GitHub Actions](https://docs.anthropic.com/en/docs/claude-code/github-actions)

### Hooks
Shell commands that run at specific points in Claude Code's lifecycle:
- Before tool execution
- After tool execution
- When subagent finishes
- When Claude finishes responding

Docs: [Hooks Guide](https://docs.anthropic.com/en/docs/claude-code/hooks-guide) | [Hooks Reference](https://docs.anthropic.com/en/docs/claude-code/hooks)

---

## Commit Strategy

```bash
# After each lesson, in ~/Desktop/rag-chatbot:
git add -A && git commit -m "lesson N: brief description"
git push

# Then update notes in ~/Desktop/Claude-Code:
# Fill the lesson notes template
git add -A && git commit -m "notes: lesson N - title"
git push
```

## What Was Built
<!-- Describe after completing -->

## Tools & Techniques Used
- [ ] CLAUDE.md for project memory (`/init`, `#`)
- [ ] Planning mode (`shift+tab`)
- [ ] Thinking mode (`think`, `think hard`, `ultrathink`)
- [ ] Subagents (brainstorming plans)
- [ ] Playwright MCP server (visual checks)
- [ ] Custom slash commands (`.claude/commands/`)
- [ ] Git worktrees (parallel features)
- [ ] GitHub integration (`/install-github-app`)
- [ ] Hooks (pre/post tool execution)

## Key Learnings
<!-- Fill after completing -->
