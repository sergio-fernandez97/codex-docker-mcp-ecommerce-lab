# Codex MCP Catalog Lab

Hands-on lab for **Module 2 — Codex Cloud & MCP Engineering Workflows**  
from the course **Coding Assistants & AI-Agents**.

This lab recreates a real-world workflow inspired by (the Docker MCP Toolkit demo)[https://www.docker.com/blog/how-to-set-up-gemini-cli-with-mcp-toolkit/], but using **Codex** instead of Gemini.

Students will learn how to integrate **Codex + MCP servers + Docker** to inspect, modify, and validate a real application.

---
## MCP Servers
> Give Codex access to third-party tools and context
> Model Context Protocol (MCP) connects models to tools and context. Use it to 
give Codex access to third-party documentation, or to let it interact with developer 
tools like your browser or Figma. — Open AI

For more information about how to configure MCP servers for Codex read 
the (documentation)[https://developers.openai.com/codex/mcp/].

### Docker MCP Catalog and Toolkit
> The challenge is that running MCP servers locally creates operational friction. Each server requires separate installation and configuration for every application you use. You run untrusted code directly on your machine, manage updates manually, and troubleshoot dependency conflicts yourself.

The MCP Toolkit and MCP Gateway solve these challenges through centralized management. You set things up once and connect all your clients to it. For further information read (Docker MCP Catalog and Toolkit)[https://docs.docker.com/ai/mcp-catalog-and-toolkit/]

Also you may watch the Youtube's video (How to Use Docker MCP Catalog and Toolkit (Docker Tutorial))[https://www.youtube.com/watch?v=6I2L4U7Xq6g].

## I. Learning Objectives

After completing this lab you should be able to:

• Use **Codex** to explore and understand an unfamiliar codebase  
• Connect a coding assistant to **MCP tools** using Docker MCP Toolkit  
• Use **skills** to guide AI-assisted development workflows  
• Implement a small improvement or automated test in a real application  
• Document AI-assisted engineering workflows  
• Use **GitHub Actions** as part of an automated evaluation pipeline  


## II. Live Session Walkthrough

During the live session we recreate a real development workflow using **Codex + MCP**.

Follow these steps to reproduce the session.


### Step 1: Set Up an e-Commerce Catalog application
For this demo, we’ll use a real e-commerce catalog application. This gives us realistic performance and accessibility issues to discover.

1. Clone the repository:
```bash
git clone https://github.com/sergio-fernandez97/codex-docker-mcp-ecommerce-lab.git
cd codex-docker-mcp-ecommerce-lab
```

2. Start all services:
```bash
# Start Docker services (database, S3, Kafka)
docker compose up -d
 
# Install dependencies
npm install --omit=optional
 
# Start the application
npm run dev
```

3. Verify it's running
* Frontend: http://localhost:5173
* API: http://localhost:3000

4. Enable Issues in Settings -> Features -> Issues for sergio-fernandez97/catalog-service-node.

5. Install Chromium: 
```bash
npx playwright install chromium
```

### Step 2 – Seed Test Data
1. Create sample products
```bash
cat > seed-data.sh << 'EOF'
#!/bin/bash
API_URL="http://localhost:3000/api"
 
echo "Seeding test products..."
 
curl -s -X POST "$API_URL/products" \
  -H "Content-Type: application/json" \
  -d '{"name":"Vintage Camera","description":"Classic 35mm film camera","price":299.99,"upc":"CAM001"}' \
  > /dev/null && echo "✅ Vintage Camera"
 
curl -s -X POST "$API_URL/products" \
  -H "Content-Type: application/json" \
  -d '{"name":"Rare Vinyl Record - LAST ONE!","description":"Limited edition. Only 1 left!","price":149.99,"upc":"VINYL001"}' \
  > /dev/null && echo "✅ Rare Vinyl Record"
 
curl -s -X POST "$API_URL/products" \
  -H "Content-Type: application/json" \
  -d '{"name":"Professional DSLR Camera","description":"50MP camera with 8K video","price":2499.99,"upc":"CAMPRO001"}' \
  > /dev/null && echo "✅ Professional DSLR"
 
# Add bulk test products
for i in {4..15}; do
  curl -s -X POST "$API_URL/products" \
    -H "Content-Type: application/json" \
    -d "{\"name\":\"Test Product $i\",\"description\":\"Bulk test product $i\",\"price\":$((50 + RANDOM % 450)).99,\"upc\":\"BULK$(printf '%03d' $i)\"}" \
    > /dev/null && echo "✅ Test Product $i"
done
 
echo ""
TOTAL=$(curl -s "$API_URL/products" | jq '. | length')
echo "Total products: $TOTAL"
echo "Ready! Visit http://localhost:5173"
EOF
 
chmod +x seed-data.sh
./seed-data.sh
```
2. Expected output
```bash
Seeding test products...
✅ Vintage Camera
✅ Rare Vinyl Record
✅ Professional DSLR
✅ Test Product 4
✅ Test Product 5
...
✅ Test Product 15
 
Total products: 15
Ready! Visit http://localhost:5173
```

### Step 2 – Connect Codex CLI to Docker MCP Toolkit
#### Option 1: One-Click Connection (Recommended)

1. Open Docker Desktop
2. Navigate to MCP Toolkit in the sidebar
3. Click the Clients tab
4. Find “Codex” in the list.
5. Click Connect

#### Option 2: Manual Command Line Setup
If you prefer a command-line setup or need to configure a specific project:

Navigate to your project folder in the terminal
Run this command:
```bash
docker mcp client connect codex --global
```
6. Increase the MCP startup timeout at `~/.config/codex/config.toml` 
```bash
[mcp_servers.MCP_DOCKER]
command = "docker"
args = ["mcp", "serve"]
startup_timeout_sec = 120
```

7. List the available mcp servers
```bash
docker mcp server ls
```

### Step 3 – Configure MCP Servers

For browser testing and performance analysis automation, you’ll orchestrate three MCP servers:

* Playwright MCP – Controls browsers, takes screenshots, captures console logs
* GitHub MCP – Creates issues automatically with full context
* Filesystem MCP – Saves screenshots and test artifacts

1. [Configure Playwright MCP](./PLAYWRIGHT_MCP.md)
2. [Configure Github MCP](./GITHUB_MCP.md)
3. [Configure Filesystem MCP](./FILESYSTEM_MCP.md)

#### Step 4 – Explore the repository
Inside the directory `catalog-service-node/`. Ask Codex to analyze the project structure
```bash
codex
```

**Prompt:**
```
Explore this repository and explain the architecture of the catalog application.
Identify:

- frontend components
- backend logic
- product catalog logic
- testing infrastructure

Create a file named `REPO_ANALYSIS.md` with you observations.
```

**Goal:** understand how the application works before modifying code.

### Step 5 – Run Automation
Execute the following prompt with codex:
```
Navigate to http://host.docker.internal:5173 and perform a detailed 
performance and accessibility analysis:
 
1. Take a screenshot of the full page.
2. Count how many products are displayed
3. Open browser DevTools and check:
   - Console for any JavaScript errors
   - Network tab: how many HTTP requests are made?
   - Performance: how long does the page take to load?
4. Identify performance issues:
   - Are all products loading at once with no pagination?
   - Are images optimized?
   - Any unnecessary network requests?
5. Check for accessibility issues:
   - Missing alt text on images
   - Color contrast problems
   - Vague button labels
 
Create a GitHub issue titled "Product catalog performance and 
accessibility issues" with:
- Screenshots attached
- List of specific problems found
- Severity: Medium
- Labels: performance, accessibility, enhancement

Create a file ./ACCESIBILITY_ANALYSIS.md, include your findings and the screenshot.
```

## III. SKILLS
This is brief overview of skills for OpenAI Codex.
> Use agent skills to extend Codex with task-specific capabilities. A skill packages 
instructions, resources, and optional scripts so Codex can follow a workflow reliably. 
You can share skills across teams or with the community. — Open AI

Read the (documentation)[https://developers.openai.com/codex/skills/] for further information.

* Other sources: [https://www.youtube.com/watch?v=en0It1zBjpw].

### QuickStart 
1. Execute `codex` and then press the command `\skills`.
2. List the available skills
```
Skill Creator -> $skill-creator
Skill Installer -> $skill-installer
```

For more information visit (OpenAI Agents Skills)[https://developers.openai.com/codex/skills/].

### Exercice
1. Install the skill ($agent-skill-evaluator)[https://github.com/JeredBlu/eval-marketplace/tree/main]
2. Evalute the skill ($playwright-skill)[https://github.com/lackeyjb/playwright-skill/tree/main] with $agent-skill-evaluator.

### Best Practices
* Source: [https://www.youtube.com/watch?v=d3Ydt6LyGeY&t=12s]. 

Avoid these common mistakes when designing skills or agent capabilities.

#### ✅ Keep skills small
**One responsibility per skill.**

Small, focused skills are easier to maintain, reuse, and trigger correctly.

---

#### ✅ Be crystal clear in descriptions
**The description controls auto-triggering.**

Write explicit and precise descriptions so the system knows exactly when the skill should be used.

---

#### ✅ Prefer instructions over scripts
**Use scripts only when determinism matters.**

- Use natural language instructions for flexible tasks.
- Use scripts only when strict, repeatable behavior is required.

---

#### ✅ Assume zero context
**Never rely on memory or previous messages.**

Each skill should work independently without assuming prior conversation context.

---

#### ✅ Match freedom to risk

Adjust how constrained the implementation is depending on the risk level:

| Risk Level | Approach |
|-------------|----------|
| Low freedom | Use **scripts** |
| Medium | Use **templates** |
| High | Use **text guidance** |

---

#### ✅ Test with real prompts
If a skill **does not trigger correctly**, fix the **description**, not the user prompt.

## IV. ASSIGNMENT: Recreate Step 5 with a Personalized Skill

### Assignment Statement
Recreate the main workflow from **Step 5 – Run Automation** (performance + accessibility analysis and GitHub issue creation), but this time by using a **personalized Codex skill** that you create and that **orchestrates MCP servers**.

Your skill must guide Codex to use MCP tools for:
- Browser analysis and evidence collection (screenshots + findings)
- GitHub issue posting with the required title, severity, labels, and findings

### Path A: Build the skill with `$skill-creator`
1. Open Codex and invoke `$skill-creator`.
2. Define a skill focused on recreating Step 5 end-to-end.
3. Write a clear `description` so the skill triggers when asked to analyze the catalog app and create a GitHub issue.
4. Add instructions in `SKILL.md` to orchestrate MCP servers in order:
   - open app
   - capture screenshot
   - inspect console/network/performance
   - check accessibility gaps
   - create GitHub issue
   - save local analysis file
5. Validate and refine the skill with at least one real run.

### Path B: Build the skill manually
1. Create a new skill folder.
2. Create `SKILL.md` with required frontmatter:
   - `name`
   - `description`
3. Add procedural instructions for the same Step 5 workflow and MCP orchestration.
4. Keep the instructions deterministic where needed (issue format, labels, severity, required artifacts).
5. Test the skill with a prompt that asks for Step 5 recreation and iterate until it executes reliably.

### Recommendations
- Keep the skill scoped to one responsibility: "catalog audit + issue creation".
- Be explicit in expected outputs: issue title, labels, severity, and analysis file.
- Include a checklist in the skill so no required step is skipped.
- Prefer concise instructions and only add scripts if repeatability requires them.
- Run a final safety review of your skill with `$agent-skill-evaluator`.

### Required Deliverables
Submit four items:
1. Fork URL of your `catalog-service-node` repository with the issue.
2. A Screenshot showing the posting of issue in the github repo from above.
2. A github repo with your skill. 
2. Skill safety evaluation report generated with `$agent-skill-evaluator` for your personalized skill.

Submissions missing either deliverable are incomplete.
