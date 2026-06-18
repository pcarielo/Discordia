# Server Layout Example

All names are placeholders. Adapt the layout to your own community and security model.

```text
<DISCORD_SERVER>
├── INFORMATION
│   ├── #announcements
│   ├── #rules-and-boundaries
│   └── #agent-directory
│
├── OPERATIONS
│   ├── #operator-console
│   ├── #approvals
│   ├── #reviews
│   ├── #handoffs
│   └── #evidence
│
├── PROJECTS
│   ├── #project-alpha
│   ├── #project-beta
│   └── #project-sandbox
│
├── AGENTS
│   ├── #agent-a-home
│   ├── #agent-b-home
│   └── #agent-lobby
│
└── INCIDENTS
    ├── #incident-room
    └── #postmortems
```

## Suggested roles

```text
@operator
@reviewer
@maintainer
@agent-runtime
@agent:<AGENT_ID>
@sandbox
```

## Suggested permission model

- `#announcements`: maintainers write, everyone reads.
- `#operator-console`: operators and trusted agents only.
- `#approvals`: reviewers and operators approve; agents request.
- `#reviews`: agents submit outputs; reviewers accept or request changes.
- `#evidence`: append-only if your platform supports it; otherwise restrict edits.
- `#project-sandbox`: lower-risk experimentation.
- `#incident-room`: restricted during active incidents.
