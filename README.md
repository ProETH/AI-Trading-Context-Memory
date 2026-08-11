# AI Context Memory

A model-agnostic persistent memory Skill that gives AI agents continuity across conversations, sessions, projects, and domains.

## Purpose

AI Context Memory allows an AI agent to remember important information and use it in future conversations.

The Skill is designed to help an agent:

- Remember important user preferences
- Remember ongoing projects
- Remember previous decisions
- Remember the reasons behind decisions
- Retrieve relevant historical context
- Compare previous and current states
- Detect meaningful changes
- Maintain continuity across sessions
- Maintain context when the underlying AI model changes
- Work across different domains
- Keep trading and portfolio context when relevant
- Respect current user instructions over old memory
- Prevent stale information from being treated as current
- Protect sensitive information

The goal is not to store every message.

The goal is to remember what matters.

---

## Core Concept

Without persistent memory:

Conversation 1
→ User explains everything
→ AI understands
→ Conversation ends
→ Context is lost
→ Conversation 2 starts again

With persistent memory:

Conversation 1
→ Important information
→ Memory
→ Persistent Store
→ Conversation 2
→ Retrieve relevant memory
→ Understand context
→ Continue

The core workflow is:

Remember
→ Retrieve
→ Understand
→ Compare
→ Reason
→ Update
→ Continue

---

# General-Purpose Design

This Skill is not limited to trading.

It can be used for:

- General AI assistants
- Personal assistants
- Coding agents
- Research agents
- Education agents
- Business agents
- YouTube projects
- Software projects
- Trading agents
- Portfolio agents
- Long-running autonomous agents

Trading and portfolio management are important use cases, but they are only applications of the memory system.

The memory architecture itself is domain-independent.

---

# Model-Agnostic Architecture

The memory system must be independent from the underlying AI provider.

The architecture separates:

AI Provider
↓
Memory Interface
↓
Persistent Memory Store
↓
Application / Skill

Possible AI providers include:

- OpenAI
- Gemini
- Claude
- Other compatible models

The memory belongs to the application or agent memory layer, not to the individual model.

---

# Model Switching

Example:

### Conversation with Model A

User:

> Remember that our project is Spot-only.

Memory:

Project Market = Spot

### Later Conversation with Model B

User:

> Continue the project.

Model B retrieves:

Project Market = Spot

The user should not lose memory because the underlying model changed.

---

# Memory Categories

The Skill should classify information before storing it.

## 1. User Preference

Long-term preferences that affect future interactions.

Examples:

- Preferred response style
- Preferred language
- Preferred market
- Preferred timeframe
- Preferred indicators
- Preferred tools
- Preferred analysis format

Example:

{
  "id": "pref-market-001",
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

## 2. Project Context

Information belonging to an ongoing project.

Examples:

- Project name
- Project objective
- Project status
- Architecture
- Technical decisions
- Constraints
- Strategy
- Current focus
- Next steps

Example:

{
  "id": "project-001",
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

## 3. Project Decision

A decision made during a project.

A decision should preferably include the reason behind it.

Example:

{
  "id": "decision-001",
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

Remembering the reason is more useful than remembering only the decision.

---

## 4. Asset Context

Historical information about an asset or entity.

Examples:

- Previous BTC analysis
- Previous ETH analysis
- Previous signal
- Previous project state
- Historical research

Example:

{
  "id": "btc-analysis-001",
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

Asset context is historical unless explicitly confirmed as current.

---

## 5. Portfolio Context

User-provided portfolio information.

Examples:

- Current holdings
- Watchlist
- Portfolio structure
- Previously held assets

Example:

{
  "id": "portfolio-001",
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

The agent must not invent:

- Quantities
- Entry prices
- Allocation percentages
- Profit/loss
- Additional holdings

---

## 6. Temporary Context

Short-lived information.

Examples:

- Temporary monitoring task
- Temporary timeframe
- Temporary analysis request
- Short-term plan

Example:

{
  "id": "temporary-001",
  "type": "temporary_context",
  "subject": "BTC",
  "content": {
    "task": "monitor_for_24_hours"
  },
  "importance": "medium",
  "expires_at": "2026-08-12T12:00:00Z",
  "status": "active"
}

Temporary memories should expire when appropriate.

---

## 7. Historical Context

Previous states that may be useful for comparison.

Examples:

- Previous strategy
- Previous analysis
- Previous preference
- Previous project architecture
- Previous portfolio state

Historical information should not automatically be treated as active information.

---

## 8. Conversation Context

Information needed to continue the current task.

Examples:

- Current file
- Current problem
- Current unfinished operation
- Current conversation objective

Conversation context should not automatically become permanent memory.

---

# Memory Object

Recommended structure:

{
  "id": "unique-memory-id",
  "type": "memory_type",
  "subject": "memory_subject",
  "content": {},
  "importance": "medium",
  "confidence": 0.90,
  "created_at": "timestamp",
  "updated_at": "timestamp",
  "expires_at": null,
  "status": "active",
  "source": "conversation"
}

---

# Memory Fields

## id

Unique identifier for the memory.

## type

Memory category.

Recommended values:

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
- YouTube channel
- Linux project
- response style
- trading strategy

## content

Structured information stored by the memory.

## importance

Recommended values:

- low
- medium
- high

## confidence

A value between 0 and 1.

Suggested interpretation:

- 1.00 = explicitly confirmed
- 0.95 = strongly confirmed
- 0.70 = reasonable inference
- 0.40 = uncertain

## created_at

Timestamp when the memory was created.

## updated_at

Timestamp when the memory was last updated.

## expires_at

Optional expiration timestamp.

Useful for temporary memories.

## status

Recommended values:

- active
- historical
- superseded
- expired
- deleted

## source

Where the information came from.

Examples:

- conversation
- explicit_user_request
- project_update
- imported_data
- system

---

# Memory Creation Rules

Store information when it is:

1. Explicitly requested to be remembered.
2. A stable user preference.
3. A meaningful project decision.
4. Important for an ongoing project.
5. A meaningful long-term state.
6. Useful for future conversations.
7. Important for project continuity.

Do not store every sentence.

---

# Explicit Remember Requests

When the user says:

- Remember this
- Save this
- Keep this in mind
- Don't forget this
- From now on

The information should be treated as a strong candidate for persistent memory.

Example:

User:

> Remember that I prefer Spot trading.

Memory:

{
  "id": "pref-market-001",
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

# What Should Not Be Stored

Avoid creating memories for:

- Greetings
- Casual conversation
- Random comments
- Temporary irrelevant statements
- Duplicate information
- Unconfirmed assumptions
- Information with no future value

---

# Sensitive Information

Never intentionally store:

- Passwords
- API keys
- Private keys
- Seed phrases
- Recovery phrases
- Authentication tokens
- Secret credentials

If sensitive credentials appear:

1. Do not store them.
2. Do not reproduce them unnecessarily.
3. Do not persist them.
4. Continue without adding them to memory.

---

# Memory Retrieval

The agent should not load the entire memory database for every request.

Instead:

1. Understand the current request.
2. Identify intent.
3. Identify entities.
4. Identify relevant subjects.
5. Search memory.
6. Rank candidate memories.
7. Check relevance.
8. Check importance.
9. Check confidence.
10. Check freshness.
11. Detect conflicts.
12. Retrieve only useful memories.

---

# Relevance Ranking

Recommended retrieval priority:

1. Directly relevant active memory
2. Relevant project context
3. Relevant user preference
4. Recent relevant historical context
5. High-confidence historical information
6. Older weakly related information

Irrelevant memories should not be injected into the model context.

---

# Memory Freshness

A critical rule:

> Historical memory is not automatically current information.

Example:

Stored memory:

BTC price = 62,000

User:

> What is BTC's current price?

Correct behavior:

Retrieve current market data.

Incorrect behavior:

Use the stored 62,000 as the current price.

Historical memory can provide context, but it cannot replace live information when freshness matters.

---

# Memory vs Current Data

Correct architecture:

Historical Memory
+
Current Data
↓
Current Analysis

Incorrect architecture:

Historical Memory
↓
Current Answer

This distinction is especially important for:

- Prices
- News
- Funding rates
- Exchange flows
- Market conditions
- Portfolio balances
- Other rapidly changing information

---

# Priority Rules

When information conflicts, use:

Current Explicit Instruction
>
Active Project Requirement
>
Active User Preference
>
Relevant Historical Context
>
Inferred Information

---

# Current Request Overrides Memory

Example:

Stored preference:

Preferred timeframe = 4H

Current request:

> For this analysis, use 15m.

Correct behavior:

Use 15m for this request.

Do not permanently change the 4H preference unless the user explicitly requests a permanent change.

---

# Temporary vs Permanent Instructions

The agent must distinguish between temporary instructions and persistent preferences.

Example:

> For this analysis, use Futures.

This does not necessarily mean:

> I permanently prefer Futures.

But:

> From now on, use Futures.

is a persistent preference change.

---

# Memory Updates

When new information changes an existing memory:

1. Search for the existing memory.
2. Determine whether the information actually changes it.
3. Update the existing memory.
4. Preserve useful historical state.
5. Mark replaced information as superseded when necessary.

Avoid creating duplicates.

---

# Preference Change Example

Previous:

Preferred Market = Spot

User:

> From now on, I prefer Futures.

Correct state:

Old memory:

{
  "preferred_market": "spot",
  "status": "superseded"
}

New memory:

{
  "preferred_market": "futures",
  "status": "active"
}

The old preference may remain as historical context.

---

# Memory Lifecycle

Recommended lifecycle:

NEW
↓
ACTIVE
↓
UPDATED
↓
SUPERSEDED
↓
EXPIRED / DELETED

Historical states can remain available when useful.

---

# Explicit Forget Requests

If the user says:

- Forget this
- Delete this
- Remove this from memory
- Don't remember this anymore

The relevant memory should be deleted or deactivated.

Example:

User:

> Forget my old BTC watchlist.

Memory:

{
  "id": "watchlist-old-001",
  "type": "portfolio_context",
  "subject": "BTC",
  "status": "deleted"
}

Deleted memories must not be used as active context.

---

# Duplicate Prevention

Before creating a new memory:

1. Search for similar memories.
2. Determine whether the information already exists.
3. Update an existing memory if appropriate.
4. Create a new memory only if it represents a distinct fact.

Avoid:

Memory 1 = same fact
Memory 2 = same fact
Memory 3 = same fact

Prefer:

One active memory
+
Historical versions when useful.

---

# Memory Compression

Long-running projects can accumulate many historical memories.

Instead of injecting every record, maintain:

Current State
+
Relevant History

Example:

Latest BTC Consensus = 89

Historical Consensus:

72 → 76 → 68 → 81 → 89

This provides continuity without unnecessarily increasing context size.

---

# Decision Memory

Important decisions should store both:

What was decided

and

Why it was decided.

Example:

Decision:

Do not use Futures execution.

Reason:

Avoid excessive leverage and liquidation risk.

This allows the agent to understand the reasoning behind a previous decision.

---

# Rejected Ideas

Important rejected approaches may also be remembered.

Example:

{
  "id": "decision-rejected-001",
  "type": "project_decision",
  "subject": "strategy_design",
  "content": {
    "decision": "reject_rsi_only",
    "reason": "too_many_false_signals"
  },
  "importance": "high",
  "confidence": 0.95,
  "status": "active"
}

If the user later asks for a new strategy, the agent should avoid repeatedly suggesting the same rejected approach unless there is a clear reason to reconsider it.

---

# Project Continuity

A major purpose of the Skill is continuing projects across different conversations.

## Session 1

User:

> We are building an AI Trading Skill for the CoinW challenge.

Memory:

Project = CoinW AI Trading Skill

Status = in development

## Session 2

User:

> We decided to focus on exchange inflows and outflows.

Memory update:

Strategy Focus:

- Exchange Inflows
- Exchange Outflows

## Session 3

User:

> We also want to use funding rate as an additional signal, but actual execution remains Spot.

Memory update:

Execution Market = Spot

Additional Signal = Funding Rate

## Session 4

User:

> Continue the project.

The agent retrieves the project state and continues without requiring the user to explain everything again.

---

# Cross-Conversation Continuity

The Skill should work across:

Conversation A
↓
Memory
↓
Conversation B
↓
Memory
↓
Conversation C

The user should not have to repeatedly provide information that has already been stored as relevant persistent context.

---

# Model-Agnostic Architecture

High-level architecture:

AI Provider
↓
Memory Interface
↓
Persistent Memory Store
↓
Application / Skill

The AI provider can change without changing the memory architecture.

---

# Model Switching Example

Conversation with Model A:

User:

> Remember that our project is Spot-only.

Memory Layer:

Project Market = Spot

Later:

Model B:

User:

> Continue the project.

Model B retrieves:

Project Market = Spot

The memory belongs to the application rather than to the individual model.

---

# Memory Interface

Conceptually, the Skill should provide operations such as:

remember()
retrieve()
search()
update()
compare()
supersede()
expire()
delete()
summarize()

The exact implementation can vary.

The important requirement is a consistent memory abstraction between the application and the AI model.

---

# Remember Operation

Purpose:

Create a persistent memory.

Conceptual operation:

remember(
    type,
    subject,
    content,
    importance,
    confidence
)

Before creating the memory, check whether a matching memory already exists.

If one exists, prefer update() rather than creating a duplicate.

---

# Retrieve Operation

Purpose:

Retrieve relevant memories.

Conceptual operation:

retrieve(
    query,
    subject,
    project,
    limit
)

Results should be ranked according to relevance, importance, freshness, and confidence.

---

# Update Operation

Purpose:

Modify existing memory.

Useful for:

- Preferences
- Projects
- Strategies
- Watchlists
- Portfolio state
- Decisions
- Technical architecture

---

# Compare Operation

Purpose:

Compare historical and current states.

Example:

Previous Consensus = 72

Current Consensus = 89

Result:

Consensus increased by 17.

The comparison should identify meaningful changes.

---

# Supersede Operation

Use when new information replaces old information.

Example:

Old Strategy:

RSI-only

Status:

superseded

New Strategy:

Volume + Momentum

Status:

active

---

# Expire Operation

Use for memories with a defined lifetime.

Examples:

- Temporary monitoring task
- Short-term plan
- Temporary instruction
- Time-limited analysis

---

# Delete Operation

Use when:

- The user explicitly asks to forget something.
- The memory is invalid.
- The memory should no longer exist.
- The memory contains prohibited sensitive information.

---

# Summarize Operation

When a project contains many memories, the system can maintain a compact summary.

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

This reduces context size while maintaining project continuity.

---

# Context Injection

Retrieved memory should be presented to the model in a structured format.

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

This makes it easier for the model to distinguish:

- Memory
- Current request
- Historical information
- Current information

---

# Avoid Memory Pollution

Do not inject unrelated memories.

Example:

User asks:

> Analyze BTC.

Relevant memories:

- BTC analysis
- Trading preferences
- CoinW trading project
- Relevant strategy
- Relevant risk preferences

Irrelevant memories:

- Linux project
- YouTube project
- Unrelated research
- Old unrelated conversations

Only relevant information should be provided to the model.

---

# Avoid Memory Loops

Do not store a memory simply because it was retrieved.

Bad:

Retrieve memory
↓
Store same memory
↓
Duplicate
↓
Retrieve duplicate
↓
Store again

Correct:

Retrieve
↓
Use
↓
Update only if meaningful new information exists

---

# No Relevant Memory

If no relevant memory exists:

Do not guess.

Correct:

> I don't have a stored memory about that.

Incorrect:

> You previously told me...

unless the information actually exists in memory.

---

# Memory Confidence

Confidence helps determine how strongly the AI should rely on memory.

Recommended interpretation:

1.00 = explicitly confirmed

0.95 = strongly confirmed

0.70 = reasonable inference

0.40 = uncertain

Low-confidence memories should not be presented as confirmed facts.

---

# Source Tracking

The system should preserve the source of important memories.

Examples:

- explicit_user_request
- conversation
- project_update
- imported_data
- system

Example:

{
  "source": "explicit_user_request"
}

Explicit user statements should generally have higher confidence than inferred information.

---

# Trading Use Case

Trading demonstrates how persistent memory can improve an AI agent.

Memory can contain:

- Trading preferences
- Preferred timeframe
- Preferred market
- Previous asset analyses
- Watchlists
- Portfolio context
- Strategy decisions
- Rejected strategies
- Risk preferences
- Project requirements

However:

> Memory must never replace current market data.

Correct architecture:

Memory
+
Strategy
+
Current Market Data
↓
Current Analysis

---

# Trading Example

Stored memory:

Project:

CoinW AI Trading Skill

Execution:

Spot

Preferred Timeframe:

4H

Strategy Focus:

Exchange Inflows

Exchange Outflows

Additional Signal:

Funding Rate

Current data:

Price = LIVE

Volume = LIVE

Funding Rate = LIVE

Exchange Flows = LIVE

Agent workflow:

1. Retrieve project context.
2. Retrieve relevant preferences.
3. Retrieve previous relevant analysis.
4. Obtain current market data.
5. Apply the strategy.
6. Compare with historical state.
7. Generate current analysis.
8. Store meaningful new information.

---

# Funding Rate Example

Funding rate can be stored historically.

Previous Funding Rate:

-0.01%

Current Funding Rate:

-0.03%

The agent can identify:

Funding Rate changed from -0.01% to -0.03%.

However, the previous funding rate must not be treated as current.

Current trading decisions require current funding data.

---

# Portfolio Example

Stored:

Current Holdings:

BTC
ETH

User:

> I sold ETH.

Updated:

Current Holdings:

BTC

Historical:

ETH was previously held.

The agent must not continue treating ETH as a current holding.

---

# Watchlist Example

Previous:

BTC
ETH
SOL

User:

> Remove SOL and add SUI.

Updated:

BTC
ETH
SUI

The existing watchlist should be updated instead of creating a duplicate.

---

# Non-Trading Example

User:

> Remember that my YouTube channel focuses on technology and crypto.

Memory:

Project:

YouTube Channel

Topics:

Technology
Crypto

Later:

> Give me five video ideas.

The agent can use the stored project context without asking the user again.

---

# Coding Project Example

Session 1:

> We are building a Python application that analyzes financial data.

Memory:

Project = Financial Analysis App

Language = Python

Status = Active

Session 2:

> Use PostgreSQL instead of SQLite.

Memory update:

Database = PostgreSQL

SQLite = superseded

Session 3:

> Continue the application.

The agent can continue using Python + PostgreSQL without asking again.

---

# Research Example

User:

> Remember that this research project focuses on Bitcoin liquidity.

Memory:

Research Topic = Bitcoin Liquidity

Later:

> Continue the research.

The agent retrieves the research context and continues from the previous state.

---

# Memory + Reasoning

Memory should improve reasoning rather than blindly control the AI.

Example:

Memory:

User previously rejected RSI-only strategies.

Current request:

> Suggest a new strategy.

The agent should consider the historical decision and avoid repeatedly suggesting the rejected approach.

But if the user explicitly says:

> Reconsider RSI-only strategies.

the current instruction overrides the historical decision.

---

# Memory Is Context, Not Authority

Persistent memory provides context.

It does not become an unquestionable source of truth.

The AI should reason using:

Current Request
+
Current Evidence
+
Relevant Memory

Not:

Memory
↓
Automatic Decision

---

# Change Detection

The Skill should detect meaningful changes between states.

Example:

Previous:

Strategy =
Volume + Momentum

Current:

Strategy =
Volume + Momentum + Funding Rate

Detected change:

Added Funding Rate

This allows the agent to maintain a meaningful history of project evolution.

---

# Historical Comparison

Historical information can improve reasoning.

Example:

Previous project requirement:

Spot only

Current user request:

Add Futures

The agent should recognize the conflict.

If the user explicitly changes the project requirement, the new requirement becomes active.

If the intent is ambiguous, the agent can ask for clarification.

---

# Memory Boundaries

The Skill should distinguish between:

Persistent:

Long-term preferences and important project decisions.

Historical:

Previous states useful for comparison.

Temporary:

Short-lived tasks and instructions.

Conversation:

Current interaction context.

Deleted:

Explicitly forgotten information.

---

# Long Conversation Management

Long conversations should not require the model to process the entire history every time.

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

This improves context efficiency and continuity.

---

# Token Efficiency

Prefer compact structured memories over raw conversation transcripts.

Instead of storing:

"On August 10 the user said that they preferred Spot trading because..."

Store:

{
  "type": "user_preference",
  "subject": "trading_market",
  "content": {
    "preferred_market": "spot"
  },
  "reason": "avoid_leverage",
  "importance": "high"
}

Structured memory is easier to retrieve, rank, compare, and reason over.

---

# Memory Quality

A good memory should be:

- Relevant
- Structured
- Specific
- Accurate
- Traceable
- Confidence-aware
- Fresh when freshness matters
- Easy to update
- Easy to supersede

A bad memory is:

- Vague
- Duplicated
- Unconfirmed
- Outdated but treated as current
- Irrelevant
- Sensitive
- Impossible to update cleanly

---

# Full Agent Workflow

For every request:

1. Receive the request.
2. Parse intent.
3. Identify entities and subjects.
4. Search persistent memory.
5. Rank candidate memories.
6. Remove irrelevant memories.
7. Check freshness.
8. Check confidence.
9. Detect conflicts.
10. Apply priority rules.
11. Combine memory with the current conversation.
12. Obtain current external data when required.
13. Perform reasoning.
14. Generate the response.
15. Detect new persistent information.
16. Update memory when necessary.
17. Mark replaced information as superseded.
18. Expire temporary memories.
19. Continue future conversations from the updated state.

---

# Final Rules

The Skill must follow these principles:

1. Remember important information.
2. Do not remember everything.
3. Retrieve only relevant memory.
4. Never invent memories.
5. Never confuse historical information with current information.
6. Current explicit instructions have priority.
7. Update existing memories instead of creating unnecessary duplicates.
8. Preserve useful historical states.
9. Remember important project decisions.
10. Remember the reasons behind important decisions.
11. Expire temporary context.
12. Respect explicit forget requests.
13. Protect sensitive credentials.
14. Keep the memory system model-agnostic.
15. Work across conversations and sessions.
16. Work across different domains.
17. Use trading and portfolio information as specialized use cases, not as the limitation of the Skill.
18. Prefer structured memory over raw conversation transcripts.
19. Use confidence and freshness when deciding whether memory should influence an answer.
20. Keep memory relevant to the current task.
21. Detect meaningful changes between old and new states.
22. Preserve project continuity.
23. Allow different AI models to use the same memory layer.
24. Treat memory as context rather than unquestionable authority.
25. Give the user control over what is remembered and forgotten.

---

# Core Objective

The objective is to transform an AI agent from a session-based assistant into a persistent, context-aware agent.

Without memory:

AI
=
Session-based answer generator

With memory:

AI
=
Continuous context-aware agent

The complete concept is:

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

The AI should not simply remember text.

It should remember what matters.

It should know what is current.

It should know what is historical.

It should understand previous decisions.

It should understand why decisions were made.

It should detect what changed.

It should respect current instructions.

It should avoid irrelevant context.

It should protect sensitive information.

And most importantly:

> Every new conversation should be able to continue from where the previous one ended.
