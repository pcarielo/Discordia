# Discordia

**Discordia is a reference architecture for Discord-native communities of persistent AI agents.**

It treats a Discord server as more than a chat surface for a single bot. Discordia describes how humans and long-lived agents can share an operational space with identities, channels, roles, handoffs, reviews, approval gates, and audit trails.

This project is self-contained and implementation-agnostic. It can be used with any agent runtime that can receive Discord events, maintain agent identity, call tools safely, and post responses back to Discord.

> All names, channels, IDs, tokens, and organizations shown in this repository are placeholders or examples. Replace them with values from your own deployment. Do not commit real secrets, user IDs, channel IDs, or private server details.

## Why Discordia?

Most Discord AI integrations are shaped like this:

```text
human -> bot -> answer
```

Discordia is shaped like this:

```text
humans + agents + projects + reviews + approvals + evidence -> shared operating space
```

The goal is to make agents feel present and accountable without pretending they are humans. They should have stable identity, known scope, clear permissions, explicit escalation rules, and observable work products.

## Core ideas

### 1. The server is the operating space

A Discord server provides:

- channels for different work modes;
- roles for authorization and visibility;
- threads as work units;
- message history as shared context;
- reactions and mentions as lightweight coordination signals;
- voice and media as optional richer interaction layers.

Discordia uses these primitives deliberately instead of forcing every interaction through one generic bot channel.

### 2. Agents have persistent identities

An agent is not just a prompt attached to a webhook. A Discordia agent should have a stable profile:

```yaml
agent:
  id: <AGENT_ID>
  display_name: <AGENT_DISPLAY_NAME>
  purpose: <AGENT_PURPOSE>
  home_channel: <AGENT_HOME_CHANNEL>
  memory_scope: <MEMORY_SCOPE>
  tools: <ALLOWED_TOOLSETS>
  approval_policy: <APPROVAL_POLICY>
  escalation_channel: <ESCALATION_CHANNEL>
  handoff_format: <HANDOFF_FORMAT>
```

Identity is operational, not theatrical. The agent should not constantly recite its role. It should behave consistently, expose boundaries when relevant, and leave useful traces of its work.

### 3. Humans remain the authority boundary

Autonomy is useful only when the boundary is clear.

Discordia separates:

- low-risk autonomous replies;
- draft generation;
- reversible workspace changes;
- external side effects;
- privileged operations;
- production changes.

Actions above the configured risk threshold require explicit human approval in a review or approval channel.

### 4. Threads are work units

A thread can represent:

- a task;
- an incident;
- a research question;
- a review request;
- a handoff;
- a decision record.

Agents should keep the thread coherent, summarize state when needed, and link evidence or artifacts before declaring work complete.

### 5. Auditability is a product feature

A useful agent network should make it easy to answer:

- What was requested?
- Who or what acted?
- What changed?
- What evidence proves it?
- What still needs human review?
- Which decision unblocked the next step?

Discordia encourages evidence-first reporting and lightweight decision logs.

## Reference server layout

This is a starting blueprint, not a mandate.

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

### Channel roles

- `#operator-console`: primary human control room.
- `#approvals`: explicit approvals for side effects and privileged work.
- `#reviews`: human review queue for outputs, plans, drafts, and proposed changes.
- `#handoffs`: structured transfer of context between agents or humans.
- `#evidence`: links to artifacts, test output, logs, commits, reports, or validation records.
- `#agent-directory`: human-readable index of available agents and their boundaries.
- `#agent-lobby`: low-risk shared coordination space.
- `#project-*`: project-specific work rooms.
- `#incident-room`: time-sensitive response area with stricter audit requirements.

## Role model

Example roles:

```text
@operator          full human authority for the deployment
@reviewer          can approve reviews and accept deliverables
@maintainer        can manage channels, roles, and agent configuration
@agent-runtime     runtime identity used by connected agent processes
@agent:<AGENT_ID>  role scoped to one agent identity
@sandbox           limited experimentation access
```

Recommended principle: roles should describe authority and scope, not personality.

## Agent directory template

Each agent should have a public or semi-public card.

```yaml
agent_card:
  display_name: <AGENT_DISPLAY_NAME>
  short_description: <ONE_SENTENCE_PURPOSE>
  home_channel: <#agent-home-channel>
  can_do:
    - <CAPABILITY_1>
    - <CAPABILITY_2>
  cannot_do:
    - <BOUNDARY_1>
    - <BOUNDARY_2>
  requires_approval_for:
    - <SIDE_EFFECT_CLASS>
    - <PRIVILEGED_OPERATION>
  typical_outputs:
    - <REPORT>
    - <PLAN>
    - <PULL_REQUEST>
    - <SUMMARY>
  escalation:
    channel: <#approvals>
    mention: <@operator-or-reviewer-role>
```

## Interaction patterns

### Direct request

```text
Human -> mentions <AGENT_DISPLAY_NAME> in #project-alpha
Agent -> asks clarifying question only if blocked
Agent -> performs safe work
Agent -> posts result + evidence + next step
```

### Approval gate

```text
Agent -> proposes side-effect action in #approvals
Human -> approves, rejects, or modifies scope
Agent -> executes only the approved scope
Agent -> posts evidence in the originating thread
```

### Review flow

```text
Agent -> posts draft in a review thread
Reviewer -> comments or approves
Agent -> revises or finalizes
Agent -> records final artifact and decision
```

### Agent-to-agent handoff

```text
Agent A -> writes a structured handoff in #handoffs
Agent B -> acknowledges scope and assumptions
Agent B -> continues in a new or existing work thread
```

Suggested handoff format:

```markdown
## Handoff: <TASK_TITLE>

- Requested outcome: <OUTCOME>
- Current state: <STATE>
- Evidence: <LINKS_OR_ARTIFACTS>
- Decisions made: <DECISIONS>
- Open blockers: <BLOCKERS>
- Recommended next action: <NEXT_ACTION>
```

### Incident room

```text
Human or monitor -> opens incident thread
Agent -> gathers facts and proposes safe checks
Human -> approves privileged operations
Agent -> records timeline, actions, evidence, and postmortem notes
```

## Implementation architecture

Discordia does not require a specific runtime. A conforming implementation usually needs these components:

```text
Discord Gateway
  receives messages, attachments, reactions, thread events

Agent Router
  maps Discord context to an agent identity and session

Agent Runtime
  runs the selected model, memory, tools, and policies

Policy Layer
  enforces allowed users, roles, channels, tools, and approvals

Evidence Store
  stores durable artifacts, logs, validation output, and links

Delivery Layer
  posts replies, files, reactions, summaries, and status updates
```

### Runtime requirements

A practical runtime should support:

- multiple independent agent identities;
- per-channel and per-thread sessions;
- role-based authorization;
- tool isolation;
- approval gates for risky operations;
- durable memory or documented stateless behavior;
- transcript search or audit export;
- file and media handling if needed;
- graceful silence when an agent is not addressed.

## Optional runtime adapters

Discordia can be implemented on top of many systems:

- a custom Discord bot;
- an MCP-aware agent gateway;
- a workflow engine connected to Discord events;
- an existing open-source agent framework;
- a hosted agent platform with Discord support.

### Example: Hermes Agent as one possible runtime

If using Hermes Agent, start with the official Discord gateway documentation:

- <https://hermes-agent.nousresearch.com/docs/user-guide/messaging/discord/>
- <https://hermes-agent.nousresearch.com/docs/user-guide/messaging/>

Map Discordia concepts to Hermes concepts like this:

```text
Discord bot/application        -> gateway platform configuration
agent identity                 -> profile, prompt/personality, skills, memory policy
server/channel/thread context  -> gateway session routing
approval gates                 -> gateway approvals and operational policy
safe tool access               -> enabled toolsets and runtime permissions
evidence links                 -> files, commits, reports, logs, or external evidence store
```

Keep implementation details in `examples/` and never commit production tokens or private IDs.

## Security model

Minimum recommendations:

- use least-privilege Discord permissions;
- allow only trusted users or roles to interact with operational agents;
- separate sandbox channels from production channels;
- require explicit approval for irreversible or external side effects;
- avoid placing secrets in prompts, channel topics, screenshots, or repo files;
- keep runtime credentials outside git;
- rotate tokens after accidental exposure;
- log enough to audit decisions without leaking secrets;
- document what each agent can and cannot do.

## MVP blueprint

A minimal Discordia deployment can start with:

- one private Discord server;
- one operator role;
- one reviewer role;
- two example agents;
- one sandbox channel;
- one project channel;
- one approvals channel;
- one reviews channel;
- one evidence channel;
- one documented handoff format;
- one runtime adapter.

Success criteria:

- an allowed human can ask an agent for safe work;
- an agent can produce a useful artifact;
- a risky action is blocked until approved;
- the final result includes evidence;
- the thread remains understandable to a new reviewer.

## Roadmap

Potential future contributions:

- server layout templates;
- agent profile schema;
- approval policy examples;
- gateway adapter examples;
- review and evidence templates;
- safety checklists;
- sample incident workflows;
- multi-agent coordination patterns;
- evaluation criteria for Discord-native agents.

## Repository structure

```text
.
├── README.md
├── CONTRIBUTING.md
├── LICENSE
└── examples
    ├── agent-profile.yaml
    ├── approval-flow.md
    ├── hermes.discord.config.example.yaml
    ├── hermes.env.example
    └── server-layout.md
```

## Contributing

Contributions are welcome when they improve the reference architecture without assuming a single private deployment.

Good contributions include:

- generic server blueprints;
- reusable agent profile templates;
- security and approval patterns;
- runtime adapter examples;
- evidence and review workflows;
- documentation improvements.

Please avoid:

- real tokens, IDs, internal names, or private server details;
- vendor lock-in as a core assumption;
- examples that require administrator permissions by default;
- anthropomorphic roleplay as a substitute for operational identity.

## License

MIT.
