# Market Research Skill for Hermes Agent

A universal market research methodology for AI agents — powered by a **4-stream research pipeline**, **AI noise filtering**, and **7-category classification taxonomy**.

## What It Does

This skill provides a structured framework for conducting deep market research using AI agents. It combines multiple data collection sources with intelligent filtering to extract actionable signals from noisy social media and web data.

### The 4-Stream Pipeline

```
Stream 1: Voice of the Market    → What people REALLY say when they think no one is listening
Stream 2: Demand                  → What fears exist before entering a niche
Stream 3: Money                   → Real market size, not abstract projections
Stream 4: Supply                  → What competitors offer — and what they DON'T
```

Each stream runs through multiple data sources, then all results pass through a **4-stage AI filtering pipeline** that turns 200 noisy posts into 30 relevant signals.

### 7-Category Classification

Every data point is classified into one of:

| Category | Description |
|----------|-------------|
| **COMPLAINT** | Frustration, bugs, pricing issues |
| **QUESTION** | What users want to know |
| **FACT** | Concrete data and experiences |
| **RECOMMENDATION** | What users suggest to others |
| **COMPARISON** | Head-to-head evaluations |
| **DEMAND** | Feature requests and market gaps |
| **NOISE** | Spam, off-topic, link-only posts |

## Key Features

- **4-Stream Research Methodology** — structured approach to cover all market angles
- **4-Stage AI Filtering Pipeline** — rule-based → heuristic → LLM classification → quote extraction
- **7-Category Taxonomy** — precise classification of market signals
- **Minority Opinion Preservation** — aggressive filtering that doesn't kill contrarian views
- **Verbatim Quote Extraction** — exact user language, not paraphrased summaries
- **Parallel Sub-Agent Execution** — run multiple streams simultaneously
- **Tavily + web_search** — deep web search with free fallback
- **Turnkey Integration** — works as a Hermes Agent skill out of the box

## Installation

### Method 1: Via AI Agent (Recommended)

Ask your Hermes Agent:

```
Copy the market-research skill folder to ~/.hermes/skills/market-research/
```

### Method 2: Via Terminal

```bash
# Clone the repository
git clone https://github.com/Segeda89/market_reserch.git /tmp/market_reserch

# Create the skills directory if it doesn't exist
mkdir -p ~/.hermes/skills

# Copy the skill folder
cp -r /tmp/market_reserch/market-research ~/.hermes/skills/

# Clean up
rm -rf /tmp/market_reserch
```

Verify installation:

```bash
ls ~/.hermes/skills/market-research/SKILL.md
```

## Quick Start

Once installed, just ask your Hermes Agent:

> "Do market research on [your niche]"

The agent will automatically:
1. Define the niche and target audience
2. Run 4 parallel research streams
3. Apply AI noise filtering
4. Find "diamonds" — gaps between demand and supply
5. Deliver a structured report with verbatim quotes

### Example

```
User: "Research the market for AI-powered customer support tools"

Agent will:
- Stream 1: Collect complaints about existing tools (Zendesk, Intercom, etc.)
- Stream 2: Find what beginners search for before buying
- Stream 3: Analyze market size, pricing, funding
- Stream 4: Compare competitor features and gaps
- Filter: Remove noise, classify signals, extract quotes
- Synthesize: Identify 5-7 market opportunities with evidence
```

## Configuration

### Tavily API Key (Optional but Recommended)

[Tavily](https://tavily.com/) provides deep web search optimized for AI agents. It returns clean, structured content from web pages — ideal for research tasks.

**Setup:**

1. Sign up at [https://app.tavily.com](https://app.tavily.com)
2. Copy your API key from the dashboard
3. Add it to your Hermes environment:

```bash
echo 'TAVILY_API_KEY=tvly-YOUR_API_KEY_HERE' >> ~/.hermes/.env
```

**Docs:** [https://docs.tavily.com/](https://docs.tavily.com/)

**Without Tavily:** The skill works fine using `web_search` (DuckDuckGo) as a free fallback. Tavily just gives you deeper results.

## How It Works

```
┌─────────────────────────────────────────────────┐
│              Market Research Pipeline            │
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│  │ Stream 1 │  │ Stream 2 │  │ Stream 3 │  │ Stream 4 │
│  │  Voice   │  │  Demand  │  │  Money   │  │  Supply  │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘
│       │              │              │              │
│       ▼              ▼              ▼              ▼
│  ┌─────────────────────────────────────────────────┐
│  │         Data Sources: Tavily / last30days /      │
│  │              web_search (fallback)               │
│  └──────────────────────┬──────────────────────────┘
│                         │
│                         ▼
│  ┌─────────────────────────────────────────────────┐
│  │           4-Stage AI Filtering Pipeline          │
│  │                                                   │
│  │  Stage 1: Rule-based (dedup, language, length)    │
│  │  Stage 2: Heuristic (N-grams, spam, boilerplate)  │
│  │  Stage 3: LLM Classification (7 categories)       │
│  │  Stage 4: Quote Extraction + Thematic Grouping    │
│  └──────────────────────┬──────────────────────────┘
│                         │
│                         ▼
│  ┌─────────────────────────────────────────────────┐
│  │              Diamond Finder                       │
│  │  Gap = Complaint + No Competitor Solution          │
│  │  Gap = Question + No Clear Answer                  │
│  │  Gap = Willingness to Pay + No Product             │
│  └──────────────────────┬──────────────────────────┘
│                         │
│                         ▼
│  ┌─────────────────────────────────────────────────┐
│  │          Structured Research Report               │
│  │  • Verbatim quotes with sources                   │
│  │  • Categorized signals (7 types)                  │
│  │  • Top 5-7 market opportunities                   │
│  │  • TAM/SAM/SOM estimates                          │
│  │  • Competitive landscape                          │
│  └─────────────────────────────────────────────────┘
│                                                  │
└─────────────────────────────────────────────────┘
```

## Flags

| Flag | Description |
|------|-------------|
| `--no-filter` | Disable AI noise filtering (raw data) |
| `--min-relevance 4` | Minimum relevance score to include (default: 4) |
| `--keep-contrarian true` | Keep posts contradicting majority (default: true) |
| `--streams 1-4` | Run only specified streams |
| `--parallel` | Run streams in parallel via sub-agents |
| `--output markdown` | Report format (markdown / json / plain) |
| `--save-to <path>` | Save report to file |

## License

MIT License

## Credits

- **Methodology:** "Four Streams" market research framework
- **AI Filtering:** 4-stage pipeline with minority opinion preservation
- **Built for:** [Hermes Agent](https://github.com/NousResearch/hermes-agent) by Nous Research
- **Data Sources:** [Tavily](https://tavily.com/) (deep web search), [last30days](https://github.com/Segeda89/market_reserch) (social media research), DuckDuckGo (free fallback)
