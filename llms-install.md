# Install JobHunt for Cline

JobHunt is a remote MCP server. There is nothing to download, build or run: you only add its address.
No API key or token is needed for the normal setup.

## Steps

1. Open Cline's MCP settings file (`cline_mcp_settings.json`; Cline shows the path under MCP Servers, Configure) and add this
   entry. Keep any servers already there.

   ```json
   {
     "mcpServers": {
       "jobhunt": {
         "type": "streamableHttp",
         "url": "https://jobhunt.app/mcp",
         "disabled": false,
         "autoApprove": []
       }
     }
   }
   ```

   `type` must be `streamableHttp`; leaving it out makes Cline use the older SSE transport, which JobHunt does not serve.
   Leave `autoApprove` empty so you approve each tool call that changes something.

2. Save the file. The first time Cline uses JobHunt, sign-in happens in the person's browser on JobHunt's approval screen,
   which lists what Cline may do. The person approves it there. **Do not ask the person for a password, key or token, and do
   not write one into the file.**

3. Check the connection by asking Cline: "Use JobHunt to show where my job search stands." This calls the read-only
   `get_overview` tool. A new account returns empty lists, which is a correct answer.

## If sign-in does not start

Tested on 20 November 2026 with the Cline command line (3.0.74) against a test copy of JobHunt: `cline mcp add jobhunt https://jobhunt.app/mcp --transport http --yes`, then `cline mcp`, **Authorize OAuth**, jobhunt, signed in; the tools listed and read, save and delete calls worked. Starting a task does not begin the sign-in, so run the authorize step first. The settings-file entry above was checked only for valid JSON and for the server's 401 and OAuth metadata answers, not run in Cline, and the Cline extension for VS Code was not tried. If Cline does not open a
sign-in page for remote servers, use the CLI wizard's **Authorize OAuth** option for the server, if your Cline version has it.
Otherwise tell the person that this Cline version cannot sign in to JobHunt yet; do not work around it by asking them to paste
credentials.

## What JobHunt can do

40 tools: reads (`list_jobs`, `list_tasks`, `get_overview`, ...) change nothing; saving or deleting is a separate tool, used
only when the person asks. Nothing contacts an employer or submits an application. The full list is in [README.md](README.md).

Problems or security reports: see [SECURITY.md](SECURITY.md).
