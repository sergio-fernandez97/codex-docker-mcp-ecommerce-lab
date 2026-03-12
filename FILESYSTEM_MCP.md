# Configure Filesystem MCP (Screenshot Storage)

The Filesystem MCP allows Gemini to save screenshots and test artifacts to your local machine.

## In Docker Desktop:

1. Go to MCP Toolkit → Catalog
2. Search for “Filesystem”
3. Find Filesystem (Reference) and click + Add
4. Go to the Configuration tab
5. Under filesystem.paths, add your project directory:
    * Example: /Users/yourname/catalog-service-node
    * Screenshots are saved at /tmp/
    * Or wherever you cloned the repository
6. You can add multiple paths by clicking the + button
7. Click Save
8. Click Start Server

**Important Security Note:** Only grant access to directories you’re comfortable 
with any Coding Assistant (e.g Codex, Claude, Gemini, etc.) reading and writing to. 
The Filesystem MCP is scoped to these specific paths for your protection.