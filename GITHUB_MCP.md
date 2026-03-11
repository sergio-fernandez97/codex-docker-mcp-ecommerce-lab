# Configure GitHub MCP (Issue Creation)

The GitHub MCP enables Gemini to create issues, PRs, and manage repositories on your behalf.

## Option 1: OAuth Authentication (Recommended – Easiest)

1. In MCP Toolkit → Catalog, search “GitHub Official”
2. Click + Add
3. Go to the OAuth tab in Docker Desktop
4. Find the GitHub entry
5. Click “Authorize”
6. Your browser opens GitHub’s authorization page
7. Click “Authorize Docker” on GitHub
8. You’re redirected back to Docker Desktop
9. Return to Catalog tab, find GitHub Official
10. Click Start Server

**Advantage:** No manual token creation. Authorization happens through GitHub’s 
secure OAuth flow with automatic token refresh.

## Option 2: Personal Access Token (For Granular Control)

If you prefer manual control or need specific scopes:

### Step 1: Create GitHub Personal Access Token

1. Go to https://github.com  and sign in
2. Click your profile picture → Settings
3. Scroll to “Developer settings” in the left sidebar
4. Click “Personal access tokens” → “Tokens (classic)”
5. Click “Generate new token” → “Generate new token (classic)”
6. Name it: “Docker MCP Browser Testing”
7. Select scopes:
    * repo (Full control of repositories)
    * workflow (Update GitHub Actions workflows)
8. Click “Generate token”
9. Copy the token immediately (you won’t see it again!)

### Step 2: Configure in Docker Desktop

1. In MCP Toolkit → Catalog, find GitHub Official
2. Click + Add (if not already added)
3. Go to Configuration tab
4. Select “Personal Access Token” as the authentication method
5. Paste your token
6. Click Start Server

## Or via CLI:
```bash
docker mcp secret set GITHUB.PERSONAL_ACCESS_TOKEN=github_pat_YOUR_TOKEN_HERE
```
