# JobHunt connect

Connect your AI assistant to [JobHunt](https://jobhunt.app), a job search workspace: saved jobs, tasks, interviews,
documents and your Hunt Page. This repository holds **only** connection docs, config snippets and registry metadata.
It contains no server code. JobHunt's server is not open source; the [`jobhunt` command line](https://github.com/chrisstroud/jobhunt-cli) is.

**Connection address:** `https://jobhunt.app/mcp` (streamable HTTP, sign-in in your browser through OAuth).

## How sign-in works

Add the address to your assistant. The first time it uses JobHunt, your browser opens JobHunt's approval screen, which lists what
the assistant may do. You approve it there; no password, API key or token is typed into the assistant or stored in a file. You can
see and disconnect every connected assistant in JobHunt under Settings. Assistants that cannot open a browser can use an API key
with `Authorization: Bearer <key>`; create one in JobHunt and keep it out of any file you share.

## Add it to your tools

| Product | How | Status |
| --- | --- | --- |
| Codex CLI | [`snippets/codex.config.toml`](snippets/codex.config.toml), then `codex mcp login jobhunt` | Verified 2026-11-19 (0.170.0): signed in, 39 tools listed, read, save and delete calls worked |
| Claude Code | `claude mcp add --scope project --transport http jobhunt https://jobhunt.app/mcp`, then `/mcp` and Authenticate ([`snippets/claude-code.mcp.json`](snippets/claude-code.mcp.json)) | Verified 2026-11-19 (2.1.328): signed in, 39 tools listed, read, save and delete calls worked |
| GitHub Copilot CLI | `copilot mcp add --transport http jobhunt https://jobhunt.app/mcp`, then `/mcp`, jobhunt, Authenticate inside the CLI ([`snippets/copilot-cli.mcp-config.json`](snippets/copilot-cli.mcp-config.json)) | Verified 2026-11-19 (1.0.114): signed in, 39 tools listed, read, save and delete calls worked |
| Cursor CLI | [`snippets/cursor.mcp.json`](snippets/cursor.mcp.json) as `.cursor/mcp.json`, then `cursor-agent mcp login jobhunt` | Verified to the level "connected, tools listed (39)" on 2026-11-19 (2026.11.19-f005e9d); tool calls not tested |
| OpenCode | `opencode mcp add jobhunt --url https://jobhunt.app/mcp`, then `opencode mcp auth jobhunt` ([`snippets/opencode.json`](snippets/opencode.json)) | Live; second sign-in fixed in the release that contains JobHunt#1327. Tested to "connected, tools listed (39)" on 2026-11-19 (2.3.2) against a test copy of JobHunt; tool calls not tested. Until that release is out, if a second sign-in fails with `invalid_request`, run `opencode mcp logout jobhunt` first |
| Hermes Agent | [`snippets/hermes.config.yaml`](snippets/hermes.config.yaml) after installing `hermes-agent[mcp]==0.19.0` (the plain package lacks the extra), then `hermes mcp login jobhunt` in a terminal | Tested 2026-11-19 (0.19.0) against a test copy of JobHunt: signed in, 39 tools listed, read and write calls worked. Hermes has no command for calling a tool, so those calls went through the MCP SDK with Hermes's saved sign-in |
| OpenClaw | `openclaw mcp set jobhunt '{"url":"https://jobhunt.app/mcp","transport":"streamable-http","auth":"oauth"}'` ([`snippets/openclaw.mcp-server.json`](snippets/openclaw.mcp-server.json)), then `openclaw mcp login jobhunt` and `openclaw mcp status` | Tested to "connected, tools listed (39)" on 2026-11-19 (2026.11.6) against a test copy of JobHunt; tool calls not tested |
| Cursor directory and other plugin catalogs | The root [`plugin.json`](plugin.json) and [`.mcp.json`](.mcp.json) (the Agent Plugins layout), and [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json) for Cursor's plugin template. Submit this repository's address to the catalog | Both manifests were checked against the published Agent Plugins schemas; not tested in Cursor's editor or in any catalog |
| Cursor (editor install link) | [Install in Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=jobhunt&config=eyJ1cmwiOiJodHRwczovL2pvYmh1bnQuYXBwL21jcCJ9) | Untested |
| VS Code | [Install in VS Code](vscode:mcp/install?%7B%22name%22%3A%22jobhunt%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A//jobhunt.app/mcp%22%7D) or the snippet below | Untested |
| Kiro | [Add to Kiro](https://kiro.dev/launch/mcp/add?name=jobhunt&config=%7B%22url%22%3A%22https%3A//jobhunt.app/mcp%22%7D) | Untested |
| Claude (web) | Settings, Connectors, add a custom connector, address `https://jobhunt.app/mcp` | Untested here |
| Gemini CLI | `gemini mcp add --transport http jobhunt https://jobhunt.app/mcp` | Untested |
| Zed | snippet below | Untested |
| LM Studio | `mcp.json` snippet below | Untested |

**What "verified" means here.** The verified rows (and OpenCode) were tried on 2026-11-19 against a local test copy of JobHunt, not
against jobhunt.app; the snippets use the production address `https://jobhunt.app/mcp`. Each row says how far the test went and
nothing more. Every other row is untested in its product. Install links decode to a remote-URL config only
(`{"url":"https://jobhunt.app/mcp"}`); check that before you accept one. See [`snippets/`](snippets) for copyable files.

## The tools

JobHunt offers 39 tools. Reads change nothing. Anything that changes your account is a separate tool that the assistant
calls only when you ask, and nothing here contacts an employer or submits an application for you.

| Tool | Does | What |
| --- | --- | --- |
| `add_note` | changes | Save a note about a job, a contact or a company, or record a conversation the person says they had with a contact (kind conversation, with occurred_on and channel). |
| `complete_task` | changes | Mark a task done, reopen it, or skip one date of a repeating task. |
| `configure_pipeline` | changes | Read or change how the pipeline is set up: add, rename, reorder or remove stages, and rename, reorder or remove a stage's checklist templates. |
| `create_resume` | changes | Create a new main resume from career facts the person stated or confirmed in this conversation. |
| `delete_record` | changes | Permanently delete one record; this cannot be undone from chat. |
| `edit_document` | changes | Change one saved document: replace one section, rename it, or reorder and hide sections. |
| `export_files` | changes | Prepare files to download and check on them: one document as PDF or Word, or one job's selected resume and cover letter as PDFs frozen exactly as they are now, so the person submits what they reviewed. |
| `get_activity` | reads | Read the dated history of the search, newest first: jobs saved, stage changes, notes, tasks, reported submissions and document changes, for the whole account or one job ("what did I do last week", "when did this job move"). |
| `get_document` | reads | Read one document in full, section by section. |
| `get_hunt_page` | reads | Read the person's public Hunt Page setup: its address, whether it is public or private right now, which resume it shows, and which resumes could be shown. |
| `get_job` | reads | Read one saved job: title, company, stage, link, posting text, the stage moves allowed (stage_type null: that stage no longer exists, or the stages could not be read), and when it entered the stage and last changed. |
| `get_operation_status` | reads | Check what happened to an earlier change when its response was lost, timed out, or said it was still running. |
| `get_overview` | reads | Read where the person's job search stands, in one call: today in their time zone, the current Hunt, every stage with its job count, tasks overdue or due in 7 days, interviews in the next 7 days, Applied and offer jobs with days in stage and any later event, links being read, and when free access ends. |
| `get_profile` | reads | Read the person's account name and email and their Hunt Profiles: the name, email, phone, location and links printed on resumes, cover letters and the Hunt Page. |
| `import_resume` | changes | Turn the person's existing resume into career facts for review, then save the facts they approve as a new resume. |
| `list_contacts` | reads | List the people saved in the person's network, or read one in full: details, company, linked jobs, recorded conversations and notes. |
| `list_documents` | reads | List saved resumes, cover letters and other documents: main resumes, the copies tailored to specific jobs, and which resume is the default. |
| `list_highlights` | reads | List entries in the person's career journal: dated accomplishments with their role, category and any linked company, newest first. |
| `list_hunts` | reads | List the person's Hunts: separate job searches, for example one per kind of role, each with its status and how many jobs it holds, and their saved Bookmarks. |
| `list_interviews` | reads | List interview rounds across all jobs or for one job, soonest first, with date, format, location, status and notes. |
| `list_jobs` | reads | List saved jobs, optionally only one stage or those matching some words ("what's waiting for me to apply to"). |
| `list_sharing` | reads | See what is shared now: every document link, job-documents link and the Hunt Page if it is public, with each link, who can open it and when it was shared. |
| `list_tasks` | reads | List follow-ups and other tasks: by default every open task, soonest first, with overdue ones marked. |
| `preview_document` | reads | Show the person the pages of one saved document as they will print: a resume, any letter, references or a statement. |
| `record_submission` | changes | Record that the person has already submitted an application themselves, and move the job to Applied. |
| `save_contact` | changes | Add a person to the network, or correct one. |
| `save_cover_letter` | changes | Save a cover letter you drafted for one saved job, alongside that job's copy of the resume. |
| `save_highlight` | changes | Add or edit a career journal entry from something the person tells you they did. |
| `save_hunt` | changes | Create a Hunt, or change one: rename it, pause or resume it, change its criteria, or make it the current Hunt that new jobs are saved into. |
| `save_interview` | changes | Add an interview round to a job, or change one: when it is, how long, format, location, notes, and whether it was completed or skipped. |
| `save_job` | changes | Save a job the person wants to track. |
| `save_task` | changes | Create a follow-up or reminder, or change an existing one's title, due date or notes. |
| `search` | reads | Find saved jobs, documents, contacts, companies, tasks, Hunts and highlights by name, company, title, a job's link or a highlight's text, when the person names something ("the Northwind job") and you do not hold its id. |
| `set_hunt_page_visibility` | changes | Publish the Hunt Page or take it down. |
| `share_document` | changes | Create or revoke the share link for one document or one job's documents. |
| `tailor_resume_for_job` | changes | Start tailoring a resume to one saved job. |
| `update_hunt_page` | changes | Change the Hunt Page draft: which resume it shows, its address, and whether search engines may list it. |
| `update_job` | changes | Change one saved job: move it to another stage by name, or correct its title, company, link or posting text. |
| `update_profile` | changes | Create a Hunt Profile or change one: contact details, its label, or which is the default. |

## Privacy, support, terms

- Privacy: https://jobhunt.app/privacy
- Terms: https://jobhunt.app/terms
- Support: https://jobhunt.app/support
- Developers: https://jobhunt.app/developers
- Security: see [SECURITY.md](SECURITY.md)

## Licence

MIT, Copyright (c) 2026 JobHunt App, Inc. The licence covers the documents and snippets in this repository, not the JobHunt service.

## Not listed yet

This repository is not a listing in any directory. A directory is named here as "Listed in" only after it has accepted JobHunt.
