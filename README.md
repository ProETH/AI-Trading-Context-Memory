# AI Trading Context Memory

A persistent memory skill for AI agents that stores, retrieves, updates, and compares important context across conversations and sessions.

## Overview

AI agents often lose useful context when a conversation ends or when a new session begins.

AI Trading Context Memory provides a persistent context layer that allows an AI agent to remember relevant information and retrieve it when needed.

Instead of repeatedly asking the user for the same information or starting an analysis from zero, the agent can use previous context to understand the current request.

The skill is designed to work alongside different AI agents and trading strategies. It does not replace the agent's reasoning or market-data sources.

## Core Functions

### Store

The skill identifies and stores useful information from conversations.

Examples include:

- Previous asset analysis
- Previous trading decisions
- Watchlists
- Portfolio context
- Trading preferences
- Strategy preferences
- Important timestamps
- Relevant user instructions

### Retrieve

The skill retrieves memories relevant to the current request.

For example:

> Analyze BTC again.

The agent can retrieve the previous BTC context before performing a new analysis.

The goal is to retrieve relevant context rather than loading the entire memory history.

### Update

Memory should evolve when information changes.

Instead of creating multiple conflicting copies of the same information, the skill updates existing context when appropriate.

For example:

Previous:

- RSI: 48
- MACD: Neutral
- Decision: WAIT

Current:

- RSI: 61
- MACD: Bullish
- Decision: BUY

The current state becomes the active context while the previous state can remain available for historical comparison.

### Compare

One of the main capabilities of the skill is comparing previous and current context.

For example:

Previous Analysis:

- Consensus: 72
- RSI: 48
- MACD: Neutral
- Volume: Normal

Current Analysis:

- Consensus: 89
- RSI: 61
- MACD: Bullish
- Volume: Strong

The skill can identify:

- Consensus increased from 72 to 89
- RSI increased from 48 to 61
- MACD changed from Neutral to Bullish
- Volume changed from Normal to Strong

This allows the agent to explain what changed since the previous analysis instead of simply repeating the current analysis.

### Expire

Not all memories remain useful forever.

The skill should support expiration or freshness rules for temporary information such as:

- Old market conditions
- Outdated asset analysis
- Completed trading plans
- Temporary watchlist entries
- Short-term preferences

This prevents old information from being treated as current truth.

## Memory Categories

### Asset Context

Stores information related to individual assets.

Examples:

- Previous analysis
- Previous signals
- Important levels
- Analysis history
- Market type
- Analysis timestamp

### Portfolio Context

Stores relevant portfolio information.

Examples:

- Holdings
- Allocation
- Portfolio changes
- Previous portfolio analysis

### Watchlist Context

Stores assets the user is currently monitoring.

Examples:

- BTC
- ETH
- SOL
- SUI
- LINK

The watchlist can be updated when the user's interests change.

### User Preferences

Stores useful long-term preferences that affect how the agent should respond.

Examples:

- Preferred market type
- Risk preferences
- Preferred analysis style
- Preferred assets
- Preferred timeframe

### Decision History

Stores previous decisions and recommendations.

Examples:

- Previous recommendation
- User response
- Decision timestamp
- Reason for the decision

## Memory Structure

A memory should contain enough information for the agent to determine whether it is still useful.

A typical memory can contain:

- Unique ID
- Memory type
- Related asset
- Market
- Content
- Creation time
- Last update time
- Confidence
- Expiration time

Example:

```json
{
  "id": "btc-analysis-001",
  "type": "asset_analysis",
  "asset": "BTC",
  "market": "spot",
  "content": {
    "decision": "WAIT",
    "consensus_score": 72,
    "rsi": 48,
    "macd": "neutral",
    "volume": "normal"
  },
  "created_at": "2026-08-10T12:00:00Z",
  "updated_at": "2026-08-10T12:00:00Z",
  "confidence": 0.90,
  "expires_at": "2026-08-17T12:00:00Z"
}
