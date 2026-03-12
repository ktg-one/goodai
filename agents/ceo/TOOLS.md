# TOOLS.md — Popus CEO Tool Reference

These tools are available via MCP. Use them actively — they are what makes you effective.

---

## Paperclip API
**Purpose**: Issue management, agent hiring, approvals, status updates.
**How**: Use the `paperclip` and `paperclip-create-agent` skills.
**Use for**: Hiring writers, creating issues, reviewing completed work, escalating blockers.

---

## n8n (MCP: `n8n`)
**Instance**: https://ai-yah-old.blowfish-snares.ts.net
**Purpose**: Build and manage automation workflows directly.
**Use for**:
- Referencing real n8n capabilities when reviewing AI Automation docs
- Verifying that service descriptions in documents match what n8n can actually deliver
- Creating workflows as proof-of-concept for case study documents

---

## PAL (MCP: `pal`)
**Purpose**: Multi-model AI proxy. Gives access to Gemini and other models alongside Claude.
**Use for**:
- Getting a second opinion on legal document accuracy (run through a different model)
- Cross-checking document quality with `consensus` or `codereview` tools
- Deep strategic analysis with `thinkdeep`
- Verifying document logic before marking as done

Available PAL tools (non-disabled): `chat`, `thinkdeep`, `codereview`, `consensus`, `debug`, `planner`, `listmodels`

---

## Google Workspace (MCP: `google-workspace`)
**Purpose**: Google Docs, Sheets, Drive, Gmail, Calendar.
**Use for**:
- Saving final documents to Google Drive as formatted Google Docs (preferred output for client-facing docs)
- Reviewing drafts written to Docs by writer agents
- Sending board updates via Gmail
- Tracking document completion status in a Google Sheet

**Primary output path**: Documents should be saved to Google Drive in addition to `agents/goodai/docs/` locally.

---

## in-memoria (MCP: `in-memoria`)
**Purpose**: In-session memory persistence.
**Use for**:
- Tracking which agents have been hired this session
- Remembering document review decisions mid-heartbeat
- Avoiding duplicate actions within a long run

---

## Serena (MCP: `serena`)
**Purpose**: Symbolic code intelligence for this codebase.
**Use for**: Reading Paperclip source code if you need to understand API behaviour. Rarely needed in normal operation.

---

## Tool Selection Guide

| Task | Use |
|------|-----|
| Hire an agent | `paperclip-create-agent` skill |
| Create / update issues | `paperclip` skill |
| Verify document logic | PAL `codereview` or `consensus` |
| Save final client-facing doc | Google Workspace → Drive |
| Cross-check n8n capabilities | n8n MCP |
| Log decisions / status | in-memoria |
| Second-opinion on legal text | PAL `thinkdeep` with Gemini model |
