# HEARTBEAT.md — Popus CEO Execution Checklist

Run this every heartbeat, in order.

## Step 1 — Identity

```sh
GET /api/agents/me
```

Confirm your id, companyId, role, budget, chainOfCommand.
Check: `PAPERCLIP_TASK_ID`, `PAPERCLIP_WAKE_REASON`, `PAPERCLIP_WAKE_COMMENT_ID`, `PAPERCLIP_APPROVAL_ID`.

## Step 2 — Approval Follow-Up

If `PAPERCLIP_APPROVAL_ID` is set:
- `GET /api/approvals/{approvalId}` and `GET /api/approvals/{approvalId}/issues`
- If approved: close linked hire-request issues, then proceed to hire the agent immediately
- If rejected: comment on the issue with the board's feedback and propose an alternative

## Step 3 — Check Assigned Issues

```sh
GET /api/companies/{companyId}/issues?assigneeAgentId={your-id}&status=todo,in_progress,blocked
```

Work `in_progress` first, then `todo`. Skip `blocked` unless you have new context to act on.

## Step 4 — Audit Existing Agents for Waste

```sh
GET /api/companies/{companyId}/agents
```

For every agent that is NOT you:
- If `heartbeat.enabled: true` AND they have no assigned issues AND no instructions configured → disable their heartbeat immediately via `PATCH /api/agents/{id}` with `runtimeConfig.heartbeat.enabled: false` and post a comment on their last run explaining why
- If they have been running for multiple heartbeats with no progress on assigned work → investigate and either unblock or reassign

An agent burning budget with nothing to do is your failure as CEO, not theirs.

## Step 5 — Audit Agent Roster for Missing Hires

```sh
GET /api/companies/{companyId}/agents
```

Check for the four required writer roles:
- Legal & Compliance Writer
- Commercial Document Writer
- Marketing & Brand Writer
- Operations Writer

**If any are missing**: immediately use `paperclip-create-agent` skill to hire them.
Do not wait. Do not create a planning issue. Just hire.

## Step 7 — Audit P1 Document Issues

Search for existing P1 issues:
```sh
GET /api/companies/{companyId}/issues?q=P1
```

The seven P1 documents — each must have an issue, assigned to the correct writer:
1. Privacy Policy → Legal & Compliance Writer
2. Terms of Service → Legal & Compliance Writer
3. MSA template → Commercial Document Writer
4. SOW template → Commercial Document Writer
5. Rate Card → Commercial Document Writer
6. Invoice Payment Terms → Commercial Document Writer
7. One-Pager / Pitch Doc → Marketing & Brand Writer

If an issue is missing, create it:
```sh
POST /api/companies/{companyId}/issues
{
  "title": "...",
  "description": "...",
  "priority": "high",
  "assigneeAgentId": "...",
  "parentId": "...",   // P1 project issue if exists
  "goalId": "..."      // company goal if set
}
```

## Step 8 — Review Completed Work

For any issue in `in_review` assigned from a writer:
- Checkout the issue
- Read the submitted document
- Verify pricing and services against `agents/goodai/GOOD_AI_SERVICES_2026.md`
- Check for Australian English spelling
- Check for no placeholder text
- If confidence ≥ 85%: mark `done` with a brief summary of what passed review
- If confidence < 85%: reopen to `todo` with specific, numbered corrections — not vague feedback

## Step 9 — Check P1 Gate

If ALL P1 issues are `done`:
- Create P2 issues and assign them
- Post a board comment: "P1 complete. P2 work started."

Do not create P2 issues if any P1 issue is not `done`.

## Step 10 — Handle Blockers

For any `blocked` issue where you have new context:
- Checkout
- Resolve the blocker yourself if possible
- Otherwise escalate to board with specific ask

If no new context on a blocked issue: skip it entirely. Do not re-comment.

## Step 11 — Exit

- Comment on any in_progress work before exiting
- If nothing to do: exit cleanly with no comment
