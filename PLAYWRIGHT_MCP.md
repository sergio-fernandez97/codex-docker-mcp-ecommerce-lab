# Configure Playwright MCP (Browser Automation)

The Playwright MCP server gives Gemini the ability to control real browsers, 
Chrome, Firefox, and WebKit, just like a human would.

## In Docker Desktop:

1. Open Docker Desktop → MCP Toolkit → Catalog
2. Search for “Playwright”. Find Playwright (Browser Automation) in the results
3. Click + Add
4. The server will be added with default configuration (no additional setup needed)
6. Click Start Server
## What you get:

**21+ browser automation tools including:**
* `browser_navigate` – Navigate to URLs
* `browser_snapshot` – Capture page state for analysis
* `browser_take_screenshot` – Save visual evidence
* `browser_click`, `browser_type` – Interact with elements
* `browser_console_messages` – Get console errors
* `browser_network_requests` – Analyze HTTP requests

The Playwright MCP runs in a secure Docker container with browsers pre-installed. 
No manual ChromeDriver setup, no WebDriver conflicts, no OS-specific browser installations.