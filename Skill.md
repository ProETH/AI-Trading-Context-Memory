# AI Trading Context Memory

## Purpose

AI Trading Context Memory is a persistent context-management skill for AI agents.

Its purpose is to help an agent remember relevant information across conversations and sessions, retrieve the right context when needed, update outdated information, detect conflicts, and compare previous and current states.

The skill is designed to work with different AI agents and is not dependent on a specific model provider.

---

## Core Principle

Do not treat memory as a transcript archive.

Store **structured, useful context** that can improve future interactions.

The agent should:

1. Identify useful information.
2. Decide whether it is worth remembering.
3. Store it in a structured form.
4. Retrieve only relevant memories.
5. Validate memory freshness.
6. Update or supersede outdated information.
7. Compare previous and current states when useful.
8. Avoid storing sensitive credentials.

---

## When to Store Memory

Store information when it is:

- Explicitly requested to be remembered.
- Likely to remain useful across sessions.
- Relevant to the user's ongoing work.
- A stable preference.
- A recurring project or workflow.
- Important historical context.
- A previous analysis that may be compared with future analysis.

Do not store every sentence from a conversation.

---

## Memory Priority

Classify memories by importance.

### High Priority

Examples:

- Explicit user instructions to remember something.
- Long-term preferences.
- Important project decisions.
- Portfolio context when explicitly provided.
- Repeated workflow requirements.

### Medium Priority

Examples:

- Watchlists.
- Previous asset analysis.
- Previous trading decisions.
- Strategy preferences.
- Recurring analysis settings.

### Low Priority

Examples:

- Temporary questions.
- Short-lived observations.
- Information unlikely to matter later.

Low-priority information should have a shorter retention period.

---

## Memory Types

Use structured memory types where possible.

### user_preference

Information about how the user wants the agent to operate.

Examples:

- Preferred market
- Preferred timeframe
- Preferred response format
- Risk preference

### asset_context

Information associated with a specific asset.

Examples:

- Previous analysis
- Previous signal
- Important levels
- Previous market state

### portfolio_context

Information about portfolio-related context.

Examples:

- Holdings
- Allocation
- Portfolio analysis
- Portfolio changes

Only store portfolio information when the user provides it or explicitly asks the agent to remember it.

### watchlist

Assets the user wants to monitor.

### decision_history

Previous decisions, recommendations, or conclusions.

### project_context

Information about an ongoing project.

Examples:

- Project requirements
- Previous decisions
- Current implementation state
- Pending tasks

### conversation_context

Short-term context that may be useful across nearby interactions but does not necessarily deserve permanent storage.

---

## Memory Record

Use a structured record similar to:

```json
{
  "id": "unique-memory-id",
  "type": "asset_context",
  "subject": "BTC",
  "content": {},
  "importance": "medium",
  "confidence": 0.9,
  "created_at": "timestamp",
  "updated_at": "timestamp",
  "expires_at": "timestamp",
  "status": "active"
}
