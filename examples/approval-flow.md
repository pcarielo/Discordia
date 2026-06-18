# Approval Flow Example

This example uses placeholders. Replace them with your own channel names, roles, and runtime behavior.

## Risk classes

| Class | Examples | Default action |
| --- | --- | --- |
| Safe response | Summaries, explanations, non-sensitive drafts | Agent may answer autonomously |
| Draft only | Emails, pull request descriptions, incident updates | Agent drafts, human sends or publishes |
| Reversible workspace change | Local file edits, sandbox changes | Agent may act if scoped by policy |
| External side effect | Posting publicly, sending email, creating issues, deploying | Requires approval |
| Privileged operation | Production change, credential rotation, destructive command | Requires explicit approval and evidence |

## Basic flow

```text
1. Human requests work in <#PROJECT_CHANNEL>.
2. Agent identifies that the next step is above its autonomous risk threshold.
3. Agent posts an approval request in <#APPROVAL_CHANNEL>.
4. Human with <REVIEWER_ROLE> or <OPERATOR_ROLE> approves, rejects, or changes scope.
5. Agent executes only the approved scope.
6. Agent posts result and evidence back to the originating thread.
```

## Approval request template

```markdown
## Approval requested: <ACTION_TITLE>

- Requested by: <REQUESTER>
- Agent: <AGENT_DISPLAY_NAME>
- Origin thread: <THREAD_LINK>
- Proposed action: <ACTION>
- Why approval is needed: <RISK_REASON>
- Scope limit: <SCOPE>
- Rollback plan: <ROLLBACK_PLAN>
- Expected evidence: <EVIDENCE_TYPE>

Approve with: `<APPROVAL_PHRASE>`
Reject with: `<REJECTION_PHRASE>`
```

## Completion template

```markdown
## Completed: <ACTION_TITLE>

- Approved by: <APPROVER>
- Executed scope: <SCOPE>
- Result: <RESULT>
- Evidence: <LINKS_OR_ARTIFACTS>
- Follow-up needed: <NEXT_STEP_OR_NONE>
```
