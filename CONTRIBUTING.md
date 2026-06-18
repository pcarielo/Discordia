# Contributing to Discordia

Discordia is a reference architecture for Discord-native communities of persistent AI agents.

Contributions should be generic, reusable, and safe to publish.

## Ground rules

Do not include:

- real names of private operators, teams, agents, companies, or servers;
- real Discord user IDs, channel IDs, guild IDs, role IDs, or bot tokens;
- production URLs, internal hostnames, screenshots, logs, or private transcripts;
- secrets, API keys, OAuth credentials, session cookies, or access tokens;
- assumptions that one specific runtime is required.

Use placeholders such as:

```text
<OPERATOR_ROLE>
<AGENT_ID>
<AGENT_DISPLAY_NAME>
<DISCORD_SERVER>
<PROJECT_CHANNEL>
<APPROVAL_CHANNEL>
<RUNTIME_ADAPTER>
<EVIDENCE_STORE>
```

If you need example names, label them clearly as fictional examples and keep them generic.

## Good contribution types

- server layout blueprints;
- agent profile schemas;
- approval gate patterns;
- review workflows;
- evidence templates;
- runtime adapter notes;
- security checklists;
- incident-room workflows;
- multi-agent coordination patterns.

## Documentation style

- Prefer concepts and reusable patterns over private deployment narratives.
- Make boundaries explicit: what is autonomous, what needs review, what needs approval.
- Keep runtime-specific instructions in `examples/`.
- Explain why a pattern exists, not only how to configure it.

## Pull request checklist

Before opening a pull request, verify:

- [ ] No real secrets, IDs, names, or private deployment details are included.
- [ ] Any runtime-specific content is optional and clearly scoped.
- [ ] Examples use placeholders or clearly fictional names.
- [ ] Security and approval implications are documented.
- [ ] The contribution improves the reference architecture for more than one deployment.
