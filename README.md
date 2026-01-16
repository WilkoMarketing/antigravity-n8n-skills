# antigravity-n8n-skills

**Expert Google Antigravity skills for building flawless n8n workflows.**

This repository is a port of the excellent [n8n-skills](https://github.com/czlonkowski/n8n-skills) originally designed for Claude Code, now adapted for **Google Antigravity**.

---

## 🎯 What is this?

This repository contains **7 complementary skills** that teach Google Antigravity agents how to build production-ready n8n workflows. By adding these skills to your Antigravity environment, your agent becomes an expert in:

- ✅ **Correct n8n expression syntax** (`{{ $json.body }}` patterns)
- ✅ **Using n8n-mcp tools effectively**
- ✅ **Proven workflow patterns** (Webhooks, AI Agents, Database syncs)
- ✅ **Validation error interpretation**
- ✅ **Operation-aware node configuration**

## 📚 The 7 Skills

### 1. **n8n Expression Syntax**
Teaches correct n8n expression syntax and common patterns.
*   **Activates when:** Writing expressions, accessing `$json`/`$node` variables.
*   **Key Insight:** Teaches critical gotchas like webhook data living under `$json.body`.

### 2. **n8n MCP Tools Expert**
Expert guide for using n8n-mcp tools effectively.
*   **Activates when:** Searching for nodes, validating configurations, template searching.
*   **Key Insight:** Guides the agent to use `get_node_essentials` before dumping full schemas.

### 3. **n8n Workflow Patterns**
Build workflows using proven architectural patterns.
*   **Activates when:** Creating workflows, connecting nodes.
*   **Key Insight:** Implements 5 proven patterns (Webhook, HTTP API, Database, AI, Scheduled).

### 4. **n8n Validation Expert**
Interpret validation errors and guide fixing.
*   **Activates when:** Validation fails, debugging workflow errors.
*   **Key Insight:** Explains auto-sanitization behavior and false positives.

### 5. **n8n Node Configuration**
Operation-aware node configuration guidance.
*   **Activates when:** Configuring nodes, understanding property dependencies.
*   **Key Insight:** Understands that different operations (e.g., Slack `post` vs `update`) require different fields.

### 6. **n8n Code JavaScript**
Write effective JavaScript code in n8n Code nodes.
*   **Activates when:** Writing JS in Code nodes.
*   **Key Insight:** Correct data access patterns (`$input.all()`, `$input.item`).

### 7. **n8n Code Python**
Write Python code in n8n Code nodes with proper limitations awareness.
*   **Activates when:** Writing Python in Code nodes.
*   **Key Insight:** Awareness of library limitations (no `pandas`/`numpy`).

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
