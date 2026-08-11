# AI Context Memory Skill

## Skill Name

AI Context Memory

## Purpose

This Skill gives an AI agent persistent, structured, cross-conversation memory.

The goal is not simply to save previous messages.

The goal is to allow the agent to:

- Remember important information across conversations.
- Understand long-term user preferences.
- Continue projects without requiring the user to explain them again.
- Remember previous decisions and the reasons behind them.
- Compare current information with historical information.
- Detect changes between previous and current states.
- Maintain continuity across different sessions.
- Work independently from the underlying AI model.
- Use memory for general conversations as well as specialized domains such as trading and portfolio analysis.

The memory layer should work whether the underlying AI provider is OpenAI, Gemini, Claude, or another compatible model.

---

# Core Principle

The agent should behave as if conversations are connected over time.

Instead of:

Conversation 1
→ forgotten

Conversation 2
→ starts from zero

The Skill enables:

Conversation 1
→ Memory
→ Conversation 2
→ Retrieve Memory
→ Understand Context
→ Continue

The objective is:

Remember
→ Retrieve
→ Understand
→ Compare
→ Reason
→ Update
→ Continue

---

# Scope

This Skill is general-purpose.

It is NOT limited to:

- Trading
- Cryptocurrency
- Finance
- Portfolio management

It can be used for:

- General conversations
- Personal preferences
- Projects
- Coding projects
- YouTube projects
- Business projects
- Research
- Education
- Trading
- Portfolio management
- Technical work
- Long-running tasks

Trading is an important use case because historical context can significantly improve an AI trading agent, but the memory system itself must remain domain-independent.

---

# Memory Categories

The Skill should classify memories before storing them.

## 1. User Preference

Long-term preferences that affect future responses.

Examples:

- Preferred response style
- Preferred language
- Preferred market
- Preferred indicators
- Preferred tools
- Preferred workflow

Example:

{
  "type": "user_preference",
  "subject": "response_style",
  "content": {
    "style": "concise"
  },
  "importance": "medium",
  "confidence": 1.0,
  "status": "active"
}

---

# 2. Project Context

Information belonging to an ongoing project.

Examples:

- Project name
- Project objective
- Current status
- Technical decisions
- Constraints
- Architecture
- Strategy
- Rejected approaches
- Next steps

Example:

{
  "type": "project_context",
  "subject": "coinw_ai_trading_skill",
  "content": {
    "market": "spot",
    "status": "in_development"
  },
  "importance": "high",
  "confidence": 0.95,
  "status": "active"
}

---

# 3. Project Decision

A decision made during a project.

A decision should preferably include its reason.

Example:

{
  "type": "project_decision",
  "subject": "strategy_design",
  "content": {
    "decision": "spot_execution",
    "reason": "avoid_leverage_and_liquidation_risk",
    "alternatives_rejected": [
      "futures_execution"
    ]
  },
  "importance": "high",
  "confidence": 0.95,
  "status": "active"
}

Remembering the reason is important because it allows the agent to understand previous decisions rather than merely remembering keywords.

---

# 4. Asset Context

Historical information about a specific asset or entity.

Examples:

- Previous BTC analysis
- Previous ETH analysis
- Historical signal
- Previous project status
- Previous research result

Example:

{
  "type": "asset_context",
  "subject": "BTC",
  "content": {
    "consensus": 72,
    "decision": "WAIT",
    "rsi": 48,
    "macd": "neutral",
    "volume": "normal"
  },
  "importance": "medium",
  "confidence": 0.90,
  "status": "active"
}

Asset context is historical unless explicitly identified as current.

---

# 5. Portfolio Context

User-provided portfolio information.

Examples:

- Current holdings
- Watchlist
- Portfolio structure
- Previously held assets

Example:

{
  "type": "portfolio_context",
  "subject": "portfolio",
  "content": {
    "assets": [
      "BTC",
      "ETH"
    ]
  },
  "importance": "high",
  "confidence": 1.0,
  "status": "active"
}

The agent must never invent:

- Quantity
- Entry price
- Allocation
- Profit/loss
- Additional holdings

---

# 6. Temporary Context

Short-lived information.

Examples:

- Temporary task
- Current monitoring request
- Temporary analysis instruction
- Short-term plan

Example:

{
  "type": "temporary_context",
  "subject": "BTC",
  "content": {
    "task": "monitor_for_24_hours"
  },
  "importance": "medium",
  "expires_at": "2026-08-12T12:00:00Z",
  "status": "active"
}

Temporary memories should expire automatically when appropriate.

---

# 7. Historical Context

Previous states that may be useful for comparison.

Examples:

- Previous strategy
- Previous analysis
- Previous preference
- Previous project architecture

Historical information should not automatically be treated as active information.

---

# 8. Conversation Context

Information relevant to continuing a conversation or multi-step task.

Examples:

- Current task
- Current question
- Current unfinished operation
- Current document being edited

Conversation context may be short-lived and should not automatically become permanent memory.

---

# Memory Object

A memory should use a structured format.

Recommended structure:

{
  "id": "unique-memory-id",
  "type": "memory_type",
  "subject": "memory_subject",
  "content": {},
  "importance": "low",
  "confidence": 0.0,
  "created_at": "timestamp",
  "updated_at": "timestamp",
  "expires_at": null,
  "status": "active",
  "source": "conversation"
}

---

# Required Fields

## id

Unique identifier for the memory.

## type

The category of memory.

Examples:

- user_preference
- project_context
- project_decision
- asset_context
- portfolio_context
- temporary_context
- conversation_context
- historical_context

## subject

The entity or topic associated with the memory.

Examples:

- BTC
- ETH
- CoinW project
- YouTube project
- response style
- trading strategy

## content

The actual structured information.

## importance

Recommended values:

- low
- medium
- high

## confidence

A value between 0 and 1.

Example:

1.0 = explicitly confirmed

0.95 = strongly confirmed

0.70 = reasonably inferred

0.40 = uncertain

## status

Recommended values:

- active
- historical
- superseded
- expired
- deleted

---

# Memory Creation Rules

Create persistent memory when information is:

1. Explicitly requested to be remembered.
2. A stable user preference.
3. A meaningful project decision.
4. Important for an ongoing project.
5. A meaningful long-term state.
6. Useful for future conversations.
7. Necessary for maintaining project continuity.

Do not store every message.

---

# What Should NOT Be Stored

Do not create unnecessary memories for:

- Casual conversation
- Greetings
- Temporary irrelevant statements
- Duplicate information
- Unconfirmed assumptions
- Random comments
- Information that has no future value

Never store sensitive credentials such as:

- Passwords
- API keys
- Private keys
- Seed phrases
- Recovery phrases
- Authentication tokens
- Secret credentials

If sensitive credentials are detected, reject the memory operation.

---

# Explicit Remember Requests

When the user explicitly says:

- Remember this
- Save this
- Keep this in mind
- From now on
- Don't forget this

The agent should treat the information as a strong candidate for persistent memory.

Example:

User:

"Remember that I prefer Spot trading."

Store:

{
  "type": "user_preference",
  "subject": "trading_market",
  "content": {
    "preferred_market": "spot"
  },
  "importance": "high",
  "confidence": 1.0,
  "status": "active"
}

---

# Memory Retrieval

The agent must NOT retrieve the entire memory database for every request.

Instead:

1. Understand the current request.
2. Identify important entities.
3. Identify important subjects.
4. Search memory.
5. Rank candidate memories.
6. Check relevance.
7. Check importance.
8. Check confidence.
9. Check freshness.
10. Detect conflicts.
11. Retrieve only useful memories.

---

# Relevance Ranking

A useful ranking order is:

1. Directly related active memory
2. Current project context
3. Current user preference
4. Recent relevant historical context
5. High-confidence historical context
6. Older or weakly related information

Irrelevant memories should not be injected into the AI context.

---

# Memory Freshness

Memory freshness is important.

For example:

A BTC price from yesterday should not be treated as today's price.

A previous news event should not automatically be considered current.

A previous strategy decision may remain valid until changed.

The Skill should distinguish between:

Historical Information

and

Current Information.

---

# Historical Memory Rule

Historical memory provides context.

It does not replace current external data.

Example:

Memory:

BTC price = 62,000

User:

"What is BTC's current price?"

Correct behavior:

Retrieve live/current market data.

Do NOT answer:

BTC = 62,000

unless current data confirms it.

---

# Current Instructions Have Priority

The priority order should generally be:

1. Current explicit user instruction
2. Active project requirements
3. Active user preferences
4. Relevant historical context
5. Inferred information

Example:

Stored preference:

4H timeframe

Current user:

"For this analysis, use 15m."

The agent should use 15m for the current request.

The 4H preference should remain unchanged unless the user explicitly changes it permanently.

---

# Preference Changes

The agent should distinguish between:

Temporary instruction

and

Permanent preference change.

Example:

User:

"For this analysis, use Futures."

This does NOT necessarily mean:

"From now on I prefer Futures."

But:

"From now on, use Futures."

is a persistent preference change.

---

# Conflict Resolution

When memories conflict:

1. Prefer current explicit instructions.
2. Prefer newer confirmed information.
3. Prefer active memories over historical memories.
4. Prefer higher-confidence memories.
5. Prefer project-specific information for project questions.
6. Ask for clarification when ambiguity remains.

---

# Updating Memory

When new information changes an existing memory:

Do not automatically create a duplicate.

Instead:

1. Locate the existing memory.
2. Determine whether the new information actually changes it.
3. Update the memory if appropriate.
4. Preserve useful historical state.
5. Mark the old state as superseded when necessary.

---

# Example

Previous:

Preferred Market = Spot

New explicit instruction:

Preferred Market = Futures

Correct state:

Old:

{
  "preferred_market": "spot",
  "status": "superseded"
}

New:

{
  "preferred_market": "futures",
  "status": "active"
}

---

# Memory Lifecycle

Memory lifecycle:

NEW
↓
ACTIVE
↓
UPDATED
↓
SUPERSEDED
↓
EXPIRED / DELETED

Historical memories may remain available for comparison.

---

# Explicit Forget Requests

If the user says:

- Forget this
- Delete this
- Remove this from memory
- Don't remember this anymore

The Skill should remove or deactivate the relevant memory.

Example:

User:

"Forget my old BTC watchlist."

Correct:

{
  "id": "watchlist-old-001",
  "status": "deleted"
}

The deleted memory must not be used as active context.

---

# Memory Compression

The system should avoid unnecessary memory duplication.

If many memories describe the same evolving subject, the system may maintain:

Current State

plus

Historical Timeline.

Example:

Latest BTC consensus:

89

Historical consensus:

72 → 76 → 68 → 81 → 89

This is better than treating every old state as equally active.

---

# Decision Memory

For project decisions, remember both:

What was decided

and

Why it was decided.

Example:

Decision:

Spot execution

Reason:

Avoid leverage and liquidation risk

This allows future reasoning.

---

# Rejected Ideas

The agent may remember rejected approaches when they are important.

Example:

{
  "type": "project_decision",
  "subject": "strategy_design",
  "content": {
    "decision": "reject_rsi_only",
    "reason": "too_many_false_signals"
  },
  "status": "active"
}

If the user later asks for a new strategy, the agent should avoid repeatedly suggesting the same rejected approach unless there is a reason to reconsider it.

---

# Project Continuity

A major purpose of this Skill is continuing projects across sessions.

Example:

Session 1:

"We are building a CoinW AI Trading Skill."

Memory:

Project = CoinW AI Trading Skill
Status = in development

Session 2:

"We decided to focus on exchange inflows and outflows."

Memory update:

Strategy Focus =
Exchange Inflows
Exchange Outflows

Session 3:

"We also want to use funding rate as an additional signal, but execution remains Spot."

Memory update:

Execution =
Spot

Additional Signal =
Funding Rate

Session 4:

"Continue the project."

The agent retrieves all relevant project context and continues without asking the user to explain the project again.

---

# Model-Agnostic Architecture

The memory system must be independent from the underlying AI provider.

Architecture:

AI Provider
↓
Memory Interface
↓
Memory Store
↓
Application / Skill

Possible AI providers:

- OpenAI
- Gemini
- Claude
- Other compatible providers

The memory belongs to the application/agent memory layer, not to the individual model.

---

# Model Switching

Example:

Model A:

User:

"Remember that this project is Spot-only."

Memory Layer:

Project Market = Spot

Later:

Model B:

User:

"Continue the project."

Model B retrieves:

Project Market = Spot

The user should not lose memory simply because the underlying AI model changed.

---

# Memory Interface

The Skill should conceptually expose the following operations:

remember()

retrieve()

search()

update()

compare()

supersede()

expire()

delete()

summarize()

These operations should remain independent from the AI provider.

---

# Remember Operation

Purpose:

Create a new persistent memory.

Conceptual input:

remember(
  type,
  subject,
  content,
  importance,
  confidence
)

The operation should first check for an existing matching memory.

If one exists, prefer update() over duplicate creation.

---

# Retrieve Operation

Purpose:

Retrieve relevant memories.

Conceptual input:

retrieve(
  query,
  subject,
  project,
  limit
)

The result should be ranked by relevance.

---

# Update Operation

Purpose:

Modify existing memory.

Use when the user changes:

- Preference
- Project requirement
- Portfolio state
- Watchlist
- Strategy
- Decision

---

# Compare Operation

Purpose:

Compare historical and current states.

Example:

Previous:

Consensus = 72

Current:

Consensus = 89

Output:

Consensus increased by 17 points.

The comparison should identify meaningful changes rather than merely displaying two datasets.

---

# Supersede Operation

Use when new information replaces old information.

Example:

Old Strategy:

RSI-only

New Strategy:

Volume + Momentum

Old status:

superseded

New status:

active

---

# Expire Operation

Use for memories with a defined lifetime.

Examples:

- Temporary monitoring task
- Short-term plan
- Temporary instruction

---

# Delete Operation

Use for explicit forget requests or invalid memory.

Deleted memories must not influence normal retrieval.

---

# Summarize Operation

When a project contains many historical memories, the agent can create a compact current summary.

Example:

PROJECT:

CoinW AI Trading Skill

EXECUTION:

Spot

CORE SIGNALS:

Exchange Inflows
Exchange Outflows

ADDITIONAL SIGNAL:

Funding Rate

STATUS:

Active

This summary reduces context size while preserving continuity.

---

# Trading Use Case

The memory system can significantly improve a trading agent.

Memory can contain:

- Trading preferences
- Preferred market
- Preferred timeframe
- Previous asset analyses
- Portfolio context
- Watchlists
- Strategy decisions
- Rejected strategies
- Risk preferences
- Project requirements

But memory must not replace live market data.

Correct architecture:

Memory
+
Trading Strategy
+
Current Market Data
↓
Current Analysis

---

# Trading Example

Memory:

Project = CoinW AI Trading Skill

Execution = Spot

Preferred Timeframe = 4H

Strategy Focus =
Exchange Inflows
Exchange Outflows

Additional Signal =
Funding Rate

Current Data:

Price = LIVE

Volume = LIVE

Funding Rate = LIVE

Exchange Flows = LIVE

Agent:

1. Retrieves project context.
2. Retrieves relevant preferences.
3. Retrieves previous analysis if useful.
4. Obtains current data.
5. Applies the strategy.
6. Compares with historical state.
7. Generates current analysis.
8. Stores meaningful new information.

---

# Funding Rate Example

Funding rate may be stored as historical context.

Example:

Previous Funding Rate:

-0.01%

Current Funding Rate:

-0.03%

The agent can identify the change.

However, the historical funding rate must not be treated as the current funding rate.

Current trading decisions require current market data.

---

# Portfolio Example

Memory:

Current Holdings:

BTC
ETH

User:

"I sold ETH."

Correct update:

Current Holdings:

BTC

Historical:

ETH previously held

The agent must not continue treating ETH as a current holding.

---

# Watchlist Example

Memory:

BTC
ETH
SOL

User:

"Remove SOL and add SUI."

Updated:

BTC
ETH
SUI

The Skill should update the existing watchlist.

---

# Non-Trading Example

User:

"Remember that my YouTube channel focuses on technology and crypto."

Memory:

{
  "type": "project_context",
  "subject": "youtube_channel",
  "content": {
    "topics": [
      "technology",
      "crypto"
    ]
  },
  "importance": "high",
  "status": "active"
}

Later:

"Give me five video ideas."

The agent retrieves the channel context automatically.

---

# Another Non-Trading Example

User:

"We are building a Linux project for weak devices."

Memory:

Project = Linux Optimization

Later:

"Continue the project."

The agent retrieves the previous project context and continues.

The same memory architecture works regardless of domain.

---

# No Relevant Memory

If no relevant memory exists:

Do not guess.

Correct:

"I don't have a stored memory about that."

Incorrect:

"You previously said..."

unless the statement actually exists in memory.

---

# Memory Confidence

Confidence should reflect how certain the information is.

Example:

Explicit statement:

confidence = 1.0

Strongly confirmed:

confidence = 0.95

Reasonable inference:

confidence = 0.70

Weak inference:

confidence = 0.40

Low-confidence memories should not be presented as confirmed facts.

---

# Memory Source

Memory should ideally record its source.

Examples:

- conversation
- explicit_user_request
- project_update
- system
- imported_data

Example:

{
  "source": "explicit_user_request"
}

Explicit user statements should generally receive higher confidence than inferred information.

---

# Memory Security

The memory layer should protect user information.

Never intentionally persist:

- Passwords
- API keys
- Private keys
- Seed phrases
- Recovery phrases
- Authentication tokens
- Secret credentials

If such information appears in a message:

1. Do not store it.
2. Do not reproduce it unnecessarily.
3. Continue the conversation without persisting the secret.

---

# Memory Boundaries

The Skill must not assume that every piece of information should become permanent.

Use these boundaries:

Persistent:

Long-term preferences and project decisions.

Historical:

Previous states useful for comparison.

Temporary:

Short-lived tasks.

Conversation:

Current interaction context.

Deleted:

Explicitly forgotten information.

---

# Context Injection

Retrieved memory should be provided to the model in a structured way.

Example:

RELEVANT MEMORY

User Preferences:
- Concise responses

Project:
- CoinW AI Trading Skill
- Spot execution

Project Decisions:
- Avoid excessive leverage
- Use exchange flow data

Historical:
- Previous BTC consensus = 72

Current Request:
- Analyze BTC

This structure helps the model distinguish memory categories.

---

# Avoid Memory Pollution

Do not inject unrelated memories.

Bad:

User asks about BTC.

Agent context includes:

- BTC
- YouTube
- Linux
- Old unrelated projects
- Random conversations

Good:

BTC-related memories
+
Relevant trading preferences
+
Relevant project context

Only relevant context should be injected.

---

# Avoid Memory Loops

Do not store a memory simply because the agent retrieved it.

Bad:

Retrieve memory
→ store the same memory again
→ duplicate
→ retrieve duplicate
→ duplicate again

Correct:

Retrieve
→ use
→ update only if new meaningful information exists

---

# Avoid Duplicate Memories

Before creating a memory:

1. Search for similar memories.
2. Determine whether the information already exists.
3. Update existing memory if appropriate.
4. Create a new memory only when it represents a distinct fact.

---

# Change Detection

The Skill should detect meaningful changes.

Example:

Previous:

Strategy =
Volume + Momentum

Current:

Strategy =
Volume + Momentum + Funding Rate

Detected change:

Added:
Funding Rate

This is useful for maintaining project history.

---

# Historical Comparison

Historical information can improve reasoning.

Example:

Previous project decision:

Use Spot only

Current request:

Add Futures

The agent should recognize that the current request conflicts with the previous project requirement.

It can then follow the current explicit instruction or ask for clarification if the user has not clearly changed the project requirement.

---

# Reason-Aware Memory

Remembering reasons is often more valuable than remembering isolated facts.

Weak memory:

"Spot"

Better memory:

"Spot execution because the project avoids leverage and liquidation risk."

Reason-aware memory allows better future reasoning.

---

# Memory and Long Conversations

Long conversations should not require the model to process the entire conversation every time.

Instead:

Conversation
↓
Important Information
↓
Structured Memory
↓
Relevant Retrieval
↓
Current Context

This reduces unnecessary context and improves continuity.

---

# Memory and Token Efficiency

The Skill should prefer compact structured memories over raw conversation transcripts.

Instead of storing:

"On August 10 the user said..."

Store:

{
  "type": "project_decision",
  "subject": "execution_market",
  "content": {
    "market": "spot",
    "reason": "avoid_leverage"
  }
}

Structured memory is easier to retrieve and reason over.

---

# Full Agent Workflow

For every request:

1. Receive user request.
2. Parse the intent.
3. Identify relevant subjects.
4. Search persistent memory.
5. Rank memories.
6. Remove irrelevant memories.
7. Check freshness.
8. Check confidence.
9. Detect contradictions.
10. Apply priority rules.
11. Combine memory with current conversation.
12. Obtain live external data when required.
13. Perform reasoning.
14. Generate response.
15. Detect new persistent information.
16. Update memory if necessary.
17. Mark replaced information as superseded.
18. Expire temporary information.
19. Continue future conversations from the updated state.

---

# Final Rules

The Skill must follow these rules:

1. Remember important information.
2. Do not remember everything.
3. Retrieve only relevant memory.
4. Never invent memories.
5. Never confuse historical information with current information.
6. Current explicit instructions have priority.
7. Update existing memory instead of creating duplicates.
8. Preserve useful historical states.
9. Remember important project decisions.
10. Remember the reasons behind important decisions.
11. Expire temporary context.
12. Respect explicit forget requests.
13. Protect sensitive credentials.
14. Keep the memory system model-agnostic.
15. Work across conversations and sessions.
16. Work across different domains.
17. Use trading and portfolio information as specialized applications, not as the limitation of the Skill.
18. Prefer structured memory over raw conversation transcripts.
19. Use confidence and freshness when deciding whether memory should influence an answer.
20. The purpose of memory is continuity, not merely storage.

---

# Core Concept

The AI should not behave like a completely new agent every time a conversation starts.

It should behave like:

Same Agent
+
New Conversation
+
Relevant History
+
Current Information
=
Continuous Intelligence

The ultimate objective is:

Remember
↓
Understand
↓
Retrieve
↓
Compare
↓
Reason
↓
Update
↓
Continue
