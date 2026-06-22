# antigravity-n8n-skills

**Expert Google Antigravity skills for building flawless n8n workflows.**

This repository is a port of the excellent [n8n-skills](https://github.com/czlonkowski/n8n-skills) originally designed for Claude Code, now adapted for **Google Antigravity**.

---

## 🎯 What is this?

This repository contains **15 complementary skills** (14 workflow/dev skills + 1 always-on router skill) that teach Google Antigravity agents how to build production-ready n8n workflows and deploy self-hosted instances. By adding these skills to your Antigravity environment, your agent becomes an expert in:

- ✅ **Correct n8n expression syntax** (`{{ $json.body }}` patterns)
- ✅ **Using n8n-mcp tools effectively**
- ✅ **Proven workflow patterns** (Webhooks, AI Agents, Database syncs)
- ✅ **Validation error interpretation & fixing**
- ✅ **Operation-aware node configuration**
- ✅ **Writing JavaScript & Python Code in n8n Code nodes & Code tools**
- ✅ **Handling file & binary data correctly**
- ✅ **Building composable sub-workflows**
- ✅ **Managing multi-instance environments**
- ✅ **Deploying self-hosted n8n instances**

## 📚 The 15 Skills

### 1. **n8n Expression Syntax**
Teaches correct n8n expression syntax and common patterns.
*   **Activates when:** Writing expressions, using `{{}}` syntax, accessing `$json`/`$node` variables, troubleshooting expression errors.
*   **Key Insight:** Webhook data lives under `$json.body`, and expressions are NOT for Code nodes.

### 2. **n8n MCP Tools Expert**
Expert guide for using n8n-mcp MCP tools effectively.
*   **Activates when:** Searching for nodes, validating configurations, accessing templates, managing workflows.
*   **Key Insight:** Guides tool selection, nodeType format rules, validation profiles, and auto-sanitization.

### 3. **n8n Workflow Patterns**
Build workflows using proven architectural patterns.
*   **Activates when:** Creating workflows, connecting nodes, designing automation.
*   **Key Insight:** Implements 5 proven patterns (webhook, HTTP API, database, AI, scheduled).

### 4. **n8n Validation Expert**
Interpret validation errors and guide fixing.
*   **Activates when:** Validation fails, debugging workflow errors, handling false positives.
*   **Key Insight:** Validation loop checklist, error catalog, and profile selection.

### 5. **n8n Node Configuration**
Operation-aware node configuration guidance.
*   **Activates when:** Configuring nodes, understanding property dependencies, setting up AI workflows.
*   **Key Insight:** Property dependency rules and operation-specific requirements.

### 6. **n8n Code JavaScript**
Write effective JavaScript code in n8n Code nodes.
*   **Activates when:** Writing JS in Code nodes, troubleshooting Code node errors, using `$helpers`.
*   **Key Insight:** Correct data access patterns (`$input.all()`, `$input.item`) and required `[{json: {...}}]` return format.

### 7. **n8n Code Python**
Write Python code in n8n Code nodes with proper limitations awareness.
*   **Activates when:** Writing Python in Code nodes, working with the standard library.
*   **Key Insight:** Correct data access (`_input`, `_json`) and awareness that external libraries (like `pandas`) are not available.

### 8. **n8n Code Tool**
Write code for the AI-agent-callable Custom Code Tool (`@n8n/n8n-nodes-langchain.toolCode`).
*   **Activates when:** Building a Code Tool attached to an AI Agent, troubleshooting return type errors.
*   **Key Insight:** Must return a **string** (not `[{json:{}}]`), and input binds to `query`/`_query` (no `$fromAI()`).

### 9. **n8n Error Handling**
Make failures loud, structured, and recoverable.
*   **Activates when:** Building unattended workflows, wiring error outputs, setting retries, designing 4xx/5xx responses.
*   **Key Insight:** Node-level error outputs, `retryOnFail`, response-shape mapping, and Error Trigger nodes.

### 10. **n8n Binary & Data**
Handle files, images, and binary data correctly.
*   **Activates when:** Working with files, images, PDFs, attachments, or passing files to/from AI agent tools.
*   **Key Insight:** `$binary` vs `$json` differences, keeping binary alive, and CDN/URL requirements for chat surfaces.

### 11. **n8n Sub-workflows**
Build reusable, composable sub-workflows.
*   **Activates when:** Extracting shared logic, building multi-step workflows, or workflows over 10 nodes.
*   **Key Insight:** Execute Workflow Trigger, typed inputs, `mode: all` vs `each`, and naming prefixes.

### 12. **n8n AI Agents**
Design n8n AI agents the right way.
*   **Activates when:** Building any LangChain AI node (Agent, Chain, Classifier) or working with tool calling, memory, or RAG.
*   **Key Insight:** Agent vs Chain vs Classifier choice, sub-node slots, prompt formatting, and structured output.

### 13. **n8n Multi-Instance**
Target the right n8n instance when an account has more than one.
*   **Activates when:** Using `n8n_instances` tool, managing multiple environments (prod vs staging).
*   **Key Insight:** `list`/`switch` command shapes, session persistence, and credential verification.

### 14. **n8n Self-Hosting**
Deploy a production self-hosted n8n end-to-end to a fresh Linux VM.
*   **Activates when:** Self-hosting, installing, or deploying n8n on VPS/bare-metal.
*   **Key Insight:** Docker Compose behind Caddy, SQLite vs Postgres/Redis queues, secure defaults.

### 15. **Using n8n MCP Skills**
Always-on router skill for session initialization.
*   **Activates when:** Loaded by session start hook, routing to correct skills, and providing cross-cutting rules.

---

## 🚀 Installation

For detailed information on how Skills work in Google Antigravity, check the official documentation:
[https://antigravity.google/docs/skills](https://antigravity.google/docs/skills)

### Option 1: Global Installation (Recommended)
This makes the skills available to **all** your future Antigravity conversations and workspaces.

1.  Clone this repository into your global skills directory:
    ```bash
    cd ~/.gemini/antigravity/skills
    git clone https://github.com/WilkoMarketing/antigravity-n8n-skills.git .
    ```
    *(Note: If the `skills` folder doesn't exist, create it first).*

### Option 2: Workspace Installation
This enables the skills only for a specific project.

1.  Navigate to your project root.
2.  Create the `.agent/skills` directory:
    ```bash
    mkdir -p .agent/skills
    ```
3.  Clone the repository into that folder:
    ```bash
    git clone https://github.com/WilkoMarketing/antigravity-n8n-skills.git .agent/skills
    ```

---

## 💡 Usage

Once installed, you don't need to do anything special! The skills activate **automatically** when you ask relevant questions.

**Try asking your agent:**
*   "How do I write an n8n expression to get the user email?"
*   "Configure a Slack node to post a message."
*   "Why is my webhook workflow failing validation?"
*   "Write a JavaScript code node to filter these items."

---

## 🙏 Credits

**Original Skills Conceived by:** Romuald Członkowski ([n8n-skills](https://github.com/czlonkowski/n8n-skills))
**Ported to Antigravity by:** WilkoMarketing

These skills are designed to work perfectly with the [n8n-mcp](https://github.com/czlonkowski/n8n-mcp) server.

---

## 📝 License

MIT License - see [LICENSE](LICENSE) file for details.

---

## 👨‍💻 Maintainer

This Antigravity port is actively maintained by Wilko Marketing, the [best SEO consultant in Uruguay](https://www.wilko.marketing/consultor-seo-uruguay/), helping businesses automate their workflows and scale their digital growth. If you need to automate your SEO and digital marketing, reach out!
