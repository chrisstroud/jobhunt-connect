# Install JobHunt for Cline

JobHunt is a remote MCP server. There is nothing to download, build or run: you only add its address.
No API key or token is needed for the normal setup.

## Steps

1. If the `cline` command is available, run this and go to step 2; it writes the entry below into the right file for you:

   ```
   cline mcp add jobhunt https://jobhunt.app/mcp --transport http --yes
   ```

   Otherwise open Cline's MCP settings file and add the entry below, keeping any servers already there. The file is
   `cline_mcp_settings.json`. For the Cline command line it is `~/.cline/data/settings/cline_mcp_settings.json` (a file of the same
   name anywhere else, for example directly in `~/.cline/`, is not read); in the VS Code extension, open MCP Servers, then Configure
   MCP Servers, and Cline shows the path. If you are unsure, `cline mcp` then "List servers" prints the path it reads.

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

2. Save the file, then the person signs in. In the Cline command line, starting a task does not begin sign-in: ask the person to
   run `cline mcp` in a terminal, choose **Authorize OAuth**, then **jobhunt**. Their browser opens JobHunt's approval screen,
   which lists what Cline may do, and they approve it there. **Do not ask the person for a password, key or token, and do not
   write one into the file.**

3. Check the connection by asking Cline: "Use JobHunt to show where my job search stands." This calls the read-only
   `get_overview` tool. A new account returns empty lists, which is a correct answer.

## If sign-in does not start

Tested on 20 November 2026 with the Cline command line (3.0.74) against a test copy of JobHunt: the `cline mcp add` command above, then `cline mcp`, **Authorize OAuth**, jobhunt, signed in; the tools listed and read, save and delete calls worked. On 22 November 2026 a fresh Cline command line given only this file and the README wrote the settings file at the path above, and sign-in and the same calls worked. The Cline extension for VS Code was not tried. If Cline has no **Authorize OAuth** option for the server, tell the person that this Cline version cannot sign in to JobHunt yet; do not work around it by asking them to paste credentials.

## What JobHunt can do

40 tools: reads (`list_jobs`, `list_tasks`, `get_overview`, ...) change nothing; saving or deleting is a separate tool, used
only when the person asks. Nothing contacts an employer or submits an application. The full list is in [README.md](README.md).

Problems or security reports: see [SECURITY.md](SECURITY.md).
