# AI Trading Context Memory — Examples

This document demonstrates how an AI agent can use persistent memory across conversations and sessions.

---

## 1. Store a User Preference

### User

> From now on, I prefer Spot trading instead of Futures.

### Memory

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

### Later

> Find me a trading opportunity.

The agent retrieves the stored preference and prioritizes Spot opportunities.

---

## 2. Store a Watchlist

### User

> Add BTC, ETH and SUI to my watchlist.

### Memory

{
  "id": "watchlist-001",
  "type": "watchlist",
  "content": {
    "assets": ["BTC", "ETH", "SUI"]
  },
  "importance": "medium",
  "status": "active"
}

### Later

> What should I monitor today?

The agent retrieves the stored watchlist and returns BTC, ETH and SUI without asking the user to repeat them.

---

## 3. Update Existing Memory

### Previous Memory

{
  "id": "watchlist-001",
  "type": "watchlist",
  "content": {
    "assets": ["BTC", "ETH", "SUI"]
  },
  "status": "active"
}

### User

> Remove SUI and add SOL.

### Updated Memory

{
  "id": "watchlist-001",
  "type": "watchlist",
  "content": {
    "assets": ["BTC", "ETH", "SOL"]
  },
  "status": "active"
}

The agent should update the existing memory instead of creating a duplicate watchlist.

---

## 4. Store Previous Trading Analysis

### User

> Analyze BTC.

### Agent Result

BTC
Consensus: 72
Decision: WAIT
RSI: 48
MACD: Neutral
Volume: Normal

### Memory

{
  "id": "btc-analysis-001",
  "type": "asset_context",
  "subject": "BTC",
  "content": {
    "market": "spot",
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

---

## 5. Retrieve Previous Analysis

### Later User

> Analyze BTC again.

The agent searches persistent memory and finds the previous BTC analysis.

### Retrieved Context

Previous BTC Consensus: 72
Previous Decision: WAIT
Previous RSI: 48
Previous MACD: Neutral
Previous Volume: Normal

The agent must combine this historical context with current market data.

Historical memory must never be treated as current market data.

---

## 6. Compare Historical and Current State

### Previous

Consensus: 72
RSI: 48
MACD: Neutral
Volume: Normal
Decision: WAIT

### Current

Consensus: 89
RSI: 61
MACD: Bullish
Volume: Strong
Decision: BUY

### Change Detection

Consensus: 72 → 89
RSI: 48 → 61
MACD: Neutral → Bullish
Volume: Normal → Strong
Decision: WAIT → BUY

### Agent Response

> BTC is currently stronger than during the previous analysis. Consensus increased from 72 to 89, MACD changed from Neutral to Bullish, and volume strengthened.

The agent must not claim that the change guarantees a profitable trade.

---

## 7. Detect Expired Memory

### Memory

{
  "id": "btc-price-001",
  "type": "asset_context",
  "subject": "BTC",
  "content": {
    "price": 62000
  },
  "created_at": "2026-08-01T12:00:00Z",
  "expires_at": "2026-08-02T12:00:00Z",
  "status": "expired"
}

### User

> What is BTC's current price?

### Correct Behavior

Do not use the stored price.

The agent must retrieve current market data.

Rule:

Historical Memory ≠ Current Market Data

---

## 8. Persistent Preference Change

### Previous Memory

Preferred Market: Spot

### User

> I want to start using Futures now.

This is a clear permanent preference change.

### Updated Memory

{
  "id": "pref-market-001",
  "type": "user_preference",
  "subject": "trading_market",
  "content": {
    "preferred_market": "futures"
  },
  "importance": "high",
  "status": "active"
}

The previous Spot preference may remain in historical memory.

---

## 9. Do Not Assume a Preference Change

### Existing Memory

Preferred Market: Spot

### User

> Futures look interesting.

This does not necessarily mean that the user permanently changed their preference.

The agent should continue using Spot as the persistent preference unless the user explicitly changes it.

If necessary, ask:

> Do you want to permanently switch your preferred market to Futures?

---

## 10. Project Context

### User

> Remember that this project is Spot only. We don't use leverage.

### Memory

{
  "id": "project-market-001",
  "type": "project_context",
  "subject": "current_trading_project",
  "content": {
    "market": "spot",
    "leverage": false
  },
  "importance": "high",
  "status": "active"
}

### Later

> Should we add Futures to the strategy?

The agent retrieves the project constraint and recognizes that Futures conflicts with the current project requirements.

---

## 11. Project Decision Memory

### User

> We decided to use 30m, 4H and 1D timeframes for the signal.

### Memory

{
  "id": "project-timeframes-001",
  "type": "project_context",
  "subject": "signal_timeframes",
  "content": {
    "timeframes": ["30m", "4H", "1D"]
  },
  "importance": "high",
  "status": "active"
}

### Later

> What timeframes are we using?

### Agent

> The project currently uses 30m, 4H and 1D.

The user does not need to repeat the information.

---

## 12. Store Analysis Preferences

### User

> When analyzing signals, I want Volume, RSI, MACD and EMA included.

### Memory

{
  "id": "pref-indicators-001",
  "type": "user_preference",
  "subject": "analysis_indicators",
  "content": {
    "indicators": [
      "volume",
      "rsi",
      "macd",
      "ema"
    ]
  },
  "importance": "medium",
  "status": "active"
}

The agent should automatically consider these indicators in future relevant analyses.

---

## 13. Do Not Invent Memory

### User

> What did I say about SOL last month?

### Memory Search

No relevant memory found.

### Correct Response

> I don't have a stored memory about what you said regarding SOL last month.

### Incorrect Response

> You said SOL was your strongest investment.

The agent must never fabricate previous conversations.

---

## 14. Relevant Memory Retrieval

Suppose the memory store contains:

BTC Analysis
ETH Analysis
SUI Watchlist
CoinW Project
YouTube Project
Linux Project
Trading Preferences

### User

> Analyze BTC again.

### Retrieve

BTC Analysis
Trading Preferences
Relevant CoinW Project Context

### Do Not Retrieve

Unrelated YouTube information
Unrelated Linux information
Unrelated memories

Memory retrieval should prioritize relevance.

---

## 15. Memory Compression

Suppose the agent has five BTC analyses:

BTC Analysis 1
BTC Analysis 2
BTC Analysis 3
BTC Analysis 4
BTC Analysis 5

Instead of treating all five as equally important, the agent can maintain:

Current BTC Context

Latest Consensus: 89
Latest Decision: BUY

Historical Consensus:
72 → 76 → 68 → 81 → 89

Historical states remain available for comparison.

---

## 16. Temporary Context

### User

> Watch BTC for the next 24 hours and remind me to reconsider the position tomorrow.

### Memory

{
  "id": "btc-plan-001",
  "type": "temporary_context",
  "subject": "BTC",
  "content": {
    "task": "reconsider_position"
  },
  "importance": "medium",
  "expires_at": "2026-08-11T12:00:00Z",
  "status": "active"
}

After expiration, this context should not normally influence future decisions.

---

## 17. Portfolio Context

### User

> Remember that I currently hold BTC and ETH.

### Memory

{
  "id": "portfolio-001",
  "type": "portfolio_context",
  "content": {
    "assets": ["BTC", "ETH"]
  },
  "importance": "high",
  "status": "active"
}

### Later

The agent may consider BTC and ETH as current holdings.

The agent must not invent:

- Quantities
- Entry prices
- Allocation percentages
- Profit or loss
- Additional holdings

---

## 18. Portfolio Update

### Previous

Portfolio:
BTC
ETH

### User

> I sold my ETH.

### Updated

Portfolio:
BTC

ETH should no longer be treated as a current holding.

The historical ETH holding may remain in historical context.

---

## 19. Memory + Current Data

### Historical Memory

BTC Previous Consensus: 72
BTC Previous Decision: WAIT

### Current Data

Current Price: LIVE
Current Volume: LIVE
Current RSI: LIVE
Current MACD: LIVE

### Agent Process

Historical Memory
+
Current Data
↓
Current Analysis
↓
Comparison
↓
Memory Update

This prevents stale information from being mistaken for current information.

---

## 20. Memory Beyond Trading

The system should work for general conversations.

### User

> Remember that my YouTube channel focuses on technology and crypto.

### Memory

{
  "id": "youtube-project-001",
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

### Later

> Give me five video ideas.

The agent can use the stored channel context without asking again.

---

## 21. Cross-Conversation Project Continuity

### Conversation 1

User:

> We are building a trading Skill for the CoinW challenge.

### Stored

{
  "id": "coinw-project-001",
  "type": "project_context",
  "subject": "coinw_ai_trading_skill",
  "content": {
    "project": "CoinW AI Trading Skill",
    "status": "in_development"
  },
  "importance": "high",
  "status": "active"
}

### Conversation 2

User:

> Continue the project.

The agent retrieves the project context and continues from the previous state.

The user does not need to explain the project again.

---

## 22. Sensitive Data Protection

### User

> Remember my Binance API key.

### Correct Behavior

Do not store the credential.

Sensitive credentials include:

- API keys
- Private keys
- Seed phrases
- Recovery phrases
- Passwords
- Authentication tokens
- Secret credentials

The memory operation should be rejected.

---

## 23. Current Request Overrides Persistent Preference

### Memory

Preferred Timeframe: 4H

### User

> For this analysis, use the 15m timeframe.

### Correct Behavior

Use 15m for this request.

Do not permanently replace the 4H preference unless the user explicitly asks for a permanent change.

Priority:

Current Explicit Instruction
>
Persistent Preference
>
Historical Context

---

## 24. Complete Trading Workflow

### User

> Analyze BTC again using the strategy we created.

### Agent Workflow

1. Understand the current request.
2. Identify relevant memories.
3. Retrieve BTC historical analysis.
4. Retrieve relevant project context.
5. Retrieve relevant strategy preferences.
6. Retrieve timeframe preferences.
7. Check memory freshness.
8. Detect conflicts.
9. Obtain current market data.
10. Perform the current analysis.
11. Compare current state with historical state.
12. Identify meaningful changes.
13. Store useful new information.
14. Return the response.

---

## 25. CoinW Project Continuity

### Session 1

User:

> Remember that our CoinW project is Spot-only and we are building an AI Trading Skill.

### Memory

{
  "id": "coinw-project-001",
  "type": "project_context",
  "subject": "coinw_ai_trading_skill",
  "content": {
    "market": "spot",
    "project_status": "active"
  },
  "importance": "high",
  "status": "active"
}

### Session 2

User:

> We decided to focus on exchange inflows and outflows.

### Updated

{
  "id": "coinw-project-001",
  "type": "project_context",
  "subject": "coinw_ai_trading_skill",
  "content": {
    "market": "spot",
    "project_status": "active",
    "strategy_focus": [
      "exchange_inflows",
      "exchange_outflows"
    ]
  },
  "importance": "high",
  "status": "active"
}

### Session 3

User:

> We also want to use funding rate as an additional signal, but actual trades remain Spot.

### Updated

{
  "id": "coinw-project-001",
  "type": "project_context",
  "subject": "coinw_ai_trading_skill",
  "content": {
    "market": "spot",
    "execution_market": "spot",
    "project_status": "active",
    "strategy_focus": [
      "exchange_inflows",
      "exchange_outflows",
      "funding_rate"
    ]
  },
  "importance": "high",
  "status": "active"
}

### Session 4

User:

> Continue the project and suggest the next improvement.

### Retrieved Context

Project:
CoinW AI Trading Skill

Execution Market:
Spot

Strategy Focus:
Exchange Inflows
Exchange Outflows
Funding Rate

Status:
Active

The agent can continue development without requiring the user to repeat the project details.

---

## 26. Project Requirement Change

### Previous

Market:
Spot only

Leverage:
Not used

### User

> We changed the project. Futures are now allowed, but we still don't want excessive leverage.

### Updated

{
  "id": "coinw-project-001",
  "type": "project_context",
  "subject": "coinw_ai_trading_skill",
  "content": {
    "markets": [
      "spot",
      "futures"
    ],
    "leverage_policy": "limited"
  },
  "importance": "high",
  "status": "active"
}

The previous requirement becomes historical context.

---

## 27. Detect Project Changes

### Previous

Markets:
Spot

Strategy:
Volume + Momentum

### Current

Markets:
Spot + Futures

Strategy:
Volume + Momentum + Funding Rate

### Change Summary

Market:
Spot → Spot + Futures

Strategy:
Volume + Momentum
→
Volume + Momentum + Funding Rate

The agent can use this change summary to maintain continuity.

---

## 28. Replace an Old Strategy

### User

> I don't want to use the old strategy anymore. Forget that strategy and use the new one.

### Old Memory

{
  "id": "strategy-old-001",
  "type": "project_context",
  "status": "superseded"
}

### New Memory

{
  "id": "strategy-new-001",
  "type": "project_context",
  "status": "active"
}

The old strategy remains historical but must not influence normal future decisions.

---

## 29. Explicit Forget Request

### User

> Forget my old BTC watchlist.

### Memory

{
  "id": "watchlist-old-001",
  "type": "watchlist",
  "status": "deleted"
}

The deleted watchlist must not be used as active context.

---

## 30. Memory Confidence

Suppose the memory system contains:

BTC Strategy Preference
Confidence: 0.98

BTC Analysis
Confidence: 0.91

Old BTC Analysis
Confidence: 0.60

### User

> What was our previous BTC strategy?

The agent prioritizes the high-confidence strategy memory.

Low-confidence information should be presented as uncertain rather than as confirmed fact.

---

## 31. No Relevant Memory

### User

> What did we decide about XRP?

### Memory Search

No relevant XRP decision found.

### Correct Response

> I don't have a stored decision about XRP.

The agent must not guess.

---

## 32. Historical News vs Current News

### Stored Memory

Previous ETF Status:
Positive

### User

> What is the current ETF situation?

The agent may use the previous status as historical context.

However, it must retrieve current information before answering.

Historical News
+
Current News
↓
Current Assessment

---

## 33. Response Style Memory

### User

> I prefer short answers with the important numbers first.

### Memory

{
  "id": "response-style-001",
  "type": "user_preference",
  "subject": "response_style",
  "content": {
    "style": "concise",
    "important_numbers_first": true
  },
  "importance": "medium",
  "status": "active"
}

### Later

> Analyze ETH.

The agent should adapt its response when this preference is relevant.

---

## 34. Multiple Projects

### Memory Store

Project A:
CoinW Trading Skill

Project B:
YouTube Channel

Project C:
Linux Setup

### User

> Continue the CoinW project.

### Correct Retrieval

CoinW Trading Skill

The agent should not unnecessarily load unrelated YouTube or Linux memories.

---

## 35. Memory Lifecycle

A memory follows a lifecycle:

NEW
↓
ACTIVE
↓
UPDATED
↓
SUPERSEDED
↓
EXPIRED / DELETED

Example:

User Preference:
Spot Trading

↓

User changes preference

↓

Spot = SUPERSEDED
Futures = ACTIVE

↓

User changes preference again

↓

Futures = SUPERSEDED
New Preference = ACTIVE

Historical states remain available only when useful.

---

## 36. Complete ETH Example

### Previous Conversation

User:

> Analyze ETH.

Agent:

ETH
Consensus: 64
RSI: 44
MACD: Neutral
Volume: Weak
Decision: WAIT

### New Conversation

User:

> Check ETH again.

### Memory

Previous ETH Analysis:

Consensus: 64
RSI: 44
MACD: Neutral
Volume: Weak
Decision: WAIT

### Current Data

Consensus: 81
RSI: 58
MACD: Bullish
Volume: Strong

### Comparison

Consensus:
64 → 81

RSI:
44 → 58

MACD:
Neutral → Bullish

Volume:
Weak → Strong

Decision:
WAIT → BUY

The new analysis becomes the latest active ETH context.

The old analysis remains historical.

---

## 37. General Project Example

### Previous Conversation

User:

> I'm building a YouTube channel about technology and crypto. Remember that.

### Memory

{
  "id": "youtube-project-001",
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

### Later

User:

> Give me five video ideas.

The agent retrieves the project context and generates ideas aligned with technology and crypto.

---

## 38. Memory Is Context, Not Authority

### Memory

User previously preferred:
RSI + MACD

### Current User Request

> For this analysis, ignore RSI and focus on exchange flows.

### Correct Behavior

Follow the current request.

Persistent memory provides context but does not override explicit current instructions.

---

## 39. Complete CoinW End-to-End Example

### Session 1

User:

> Remember that our CoinW project is Spot-only and that we are building an AI trading Skill.

Stored:

Project:
CoinW AI Trading Skill

Market:
Spot

Status:
Active

### Session 2

User:

> We decided to focus on exchange inflows and outflows.

Updated:

Project Focus:
Exchange Inflows
Exchange Outflows

### Session 3

User:

> We also want to use funding rate as an additional signal, but actual trades remain Spot.

Updated:

Execution Market:
Spot

Core Data:
Exchange Inflows
Exchange Outflows

Additional Signal:
Funding Rate

### Session 4

User:

> Continue the project and suggest the next improvement.

Retrieved:

Project:
CoinW AI Trading Skill

Execution:
Spot

Core Signals:
Exchange Inflows
Exchange Outflows

Additional Signal:
Funding Rate

Status:
Active

The agent can continue from the existing project state.

---

## 40. Final Memory Architecture

The memory system should distinguish several kinds of information:

### User Preferences

Long-term preferences that affect future responses.

Examples:

- Preferred market
- Preferred response style
- Preferred indicators
- Preferred analysis format

### Project Context

Information belonging to a specific ongoing project.

Examples:

- CoinW Skill
- Strategy rules
- Project constraints
- Current project status
- Technical decisions

### Asset Context

Historical information about a specific asset.

Examples:

- Previous BTC analysis
- Previous ETH analysis
- Previous signal
- Historical consensus

### Portfolio Context

User-provided portfolio information.

Examples:

- Current holdings
- Watchlist
- Portfolio structure

### Temporary Context

Short-lived information.

Examples:

- Today's monitoring task
- Temporary analysis instruction
- Short-term plan

### Historical Context

Previous states retained for comparison.

The agent should distinguish historical information from active information.

---

## 41. Memory Retrieval Strategy

The agent should not retrieve all memories for every request.

Instead:

1. Understand the user's current request.
2. Identify entities and subjects.
3. Search for related memories.
4. Rank memories by relevance.
5. Rank by importance.
6. Check freshness.
7. Check confidence.
8. Detect contradictions.
9. Retrieve only useful context.
10. Combine with the current conversation.

Example:

User asks:

> Continue our BTC strategy.

Relevant memories:

- BTC strategy
- CoinW project
- Trading preferences
- Previous BTC analysis

Irrelevant memories:

- Linux project
- YouTube project
- Old unrelated watchlists

---

## 42. Memory Update Strategy

After completing a request, the agent should determine whether new information is worth remembering.

Store information when it is:

- Explicitly requested to be remembered.
- Important for an ongoing project.
- A stable user preference.
- A meaningful project decision.
- A useful long-term context.
- A meaningful state change.

Do not store every sentence.

Avoid creating memories for:

- Casual conversation
- Temporary irrelevant statements
- Duplicate information
- Unconfirmed assumptions
- Sensitive credentials

---

## 43. Memory Conflict Resolution

When memories conflict:

1. Prefer explicit current instructions.
2. Prefer newer confirmed information.
3. Prefer higher-confidence information.
4. Prefer active memories over historical memories.
5. Prefer project-specific context when answering project questions.
6. If ambiguity remains, ask the user.

Example:

Old:

Preferred Market = Spot

New:

Preferred Market = Futures

If the new statement is explicit and confirmed:

Futures becomes active.

Spot becomes historical.

---

## 44. Memory and AI Provider Independence

The memory system should not depend on one specific AI provider.

The architecture should separate:

AI Provider

from

Memory Layer

from

Application / Skill

Conceptually:

AI Model
↓
Memory Interface
↓
Persistent Memory Store
↓
Application

The model may be:

- OpenAI
- Gemini
- Claude
- Another compatible model

The memory system should provide the same context interface regardless of the underlying model.

This means the agent can switch models without losing the user's stored context.

---

## 45. Model-Agnostic Memory Example

### Conversation with Model A

User:

> Remember that our project is Spot-only.

Memory Layer stores:

Project Market = Spot

### Later Conversation with Model B

User:

> Continue the project.

Model B retrieves:

Project Market = Spot

The memory belongs to the agent/application, not to the individual model.

---

## 46. Memory + Trading Strategy

Memory should not replace the trading strategy.

Instead:

Memory
+
Strategy
+
Current Market Data
↓
AI Decision

Example:

Memory:

Project = Spot
Risk = Conservative
Preferred Timeframe = 4H

Strategy:

Volume anomaly
Exchange flows
Momentum

Current Data:

Price
Volume
Funding Rate
Order Book
Exchange Flows

Result:

Current Analysis

This allows the agent to understand the user's/project's history before analyzing the current market.

---

## 47. Memory-Driven Strategy Continuity

### Session 1

User:

> We rejected RSI-only strategies because they generated too many false signals.

Memory:

{
  "type": "project_decision",
  "subject": "strategy_design",
  "decision": "avoid_rsi_only",
  "reason": "too_many_false_signals",
  "status": "active"
}

### Session 2

User:

> Suggest another strategy.

The agent should remember the previous decision and avoid proposing the exact rejected approach unless there is a clear reason to revisit it.

---

## 48. Learning From Previous Decisions

Memory can store not only what the user decided, but why.

Example:

Decision:

> Do not use Futures.

Reason:

> Project is designed for Spot execution.

Later:

User:

> Can we use funding rate?

The agent can understand that funding rate may be used as an external sentiment signal while execution remains Spot.

This is more powerful than storing only:

Spot = true

because the agent also remembers the reasoning behind the decision.

---

## 49. Decision Memory

A useful project memory structure is:

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

This allows the agent to understand previous reasoning rather than simply remembering a keyword.

---

## 50. Final Principle

The goal of this Skill is not simply to store text.

The goal is to give the AI:

- Continuity
- Context
- Historical awareness
- Preference awareness
- Project awareness
- Decision awareness
- Change detection
- Cross-session consistency

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

The agent should behave as if every conversation is part of one continuous relationship, while still respecting memory boundaries, freshness, confidence, privacy, and the user's current instructions.
