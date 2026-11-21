# ICT Strategy — TradingView Indicator

A multi-timeframe **ICT (Inner Circle Trader) market-structure and liquidity analysis indicator** written in **Pine Script v6** for TradingView.

The project translates a documented ICT trading methodology into chart-based tools for identifying market structure, liquidity events, key price zones, session windows, and potential trade-confirmation patterns. The current implementation is **Phase 1: an indicator for analysis and visualization**. It does not place or manage live trades.

## Project Status

| Component | Status |
|---|---|
| Phase 1 — TradingView indicator (Pine Script v6) | Implemented |
| Multi-timeframe analysis and chart visualization | Implemented |
| TradingView alert conditions | Implemented |
| Phase 2 — MetaTrader 5 Expert Advisor (MQL5) | Future development; no EA source currently included |

## Features

### Multi-Timeframe Analysis

Separate analysis states are maintained for five configurable timeframes: Monthly, Weekly, Daily, 4-hour, and 1-hour. Each timeframe can be enabled or disabled independently.

### ICT Concepts

The current Pine Script implementation includes:

- **Swing Highs and Swing Lows**
- **Market Structure (MS High / MS Low)** and structure breaks
- **Liquidity raids** around relevant swing and market-structure levels
- **Order Blocks (OB)**, including validation using a same-direction Fair Value Gap
- **Rejection Blocks (RB)**
- **Fair Value Gaps (FVG)**, including higher-timeframe key levels
- **Stop Hunt + Break of Market Structure (shBms)**
- **Launch Pad (LP)**, Origin, and Breaker-related visual elements
- Relevant price-zone touches and key-level tracking

Higher-timeframe levels and lower-timeframe zones use different visual representations. The script also limits the number of nearby levels retained to help keep the chart readable.

### Trading Sessions and Time Filters

Session windows use a fixed New York UTC-5 time basis in the current implementation:

| Session | New York time | Entry window |
|---|---:|---|
| Asia | 20:00–00:00 | Not allowed |
| London Kill Zone | 01:00–05:00 | Allowed, subject to Monday filter |
| Lunch | 05:00–07:00 | Not allowed |
| New York Kill Zone | 07:00–10:00 | Allowed |
| London Close | 10:00–12:00 | Not an entry window |

Additional configurable filters include:

- Disable trading for all of Monday (default: enabled)
- Manually entered high-impact news timestamps
- Pre-news and post-news restricted windows
- Year-end and beginning-of-year blackout dates

**Time-zone limitation:** Session and manually entered news-time calculations use the fixed UTC-5 timezone identifier Etc/GMT+5. This does not automatically follow New York daylight-saving time. Check session alignment and configured timestamps when New York is observing daylight time.

### On-Chart Status Panel

The optional status table displays market-structure direction for each enabled timeframe, the current session and entry-window status, Monday filter status, news-window status, and annual blackout status.

### Alerts

TradingView alert conditions are available for:

1. New valid Order Block
2. Touch of a key level
3. Market-structure break
4. Liquidity raid
5. Completion of the Stop Hunt + BMS pattern
6. Formation of a Launch Pad
7. Kill Zone status change
8. Approaching a configured news window

To create an alert, add the indicator to a chart, open TradingView's **Create Alert** dialog, and select the required condition from the indicator's alert-condition list.

## Repository Structure

\`\`\`text
ict/
├── phase1/
│   ├── ICT_Strategy_Phase1.pine
│   └── ICT_Strategy_Phase1.md
├── claud/
│   └── ICT_Strategy_Technical_Spec (1).md
├── deepseek/
│   ├── deepseek_text_20260727_a66ec8.txt
│   ├── متن استراتژی.md
│   ├── پرامپت.md
│   └── reference images
├── grok/
│   ├── ICT_Strategy.md
│   ├── ICT_Strategy_Full.md
│   ├── ICT_Strategy_Technical_Spec.md
│   └── پرامپت.txt
├── استراتزی ict.docx
├── AGENTS.md
└── README.md
\`\`\`

The claud, deepseek, and grok directories contain research notes, prompts, and technical/specification documents created during strategy analysis. They are reference material and should not be assumed to be identical or equally authoritative. If specifications conflict, resolve the discrepancy before changing implementation logic.

## Installation

1. Open [TradingView](https://www.tradingview.com/).
2. Open a chart for the instrument you want to analyze.
3. Open **Pine Editor**.
4. Copy the contents of [phase1/ICT_Strategy_Phase1.pine](phase1/ICT_Strategy_Phase1.pine) into the editor.
5. Select **Save**, then **Add to chart**.
6. Open the indicator settings to configure timeframes, visual elements, sessions, history, and strategy parameters.

The script runs as a TradingView chart indicator. It is not a standalone application and does not require a local Python environment, package installation, or a broker API.

## Configuration

The script groups its inputs in the TradingView indicator settings:

| Group | Examples |
|---|---|
| Timeframes | Monthly, Weekly, Daily, 4H, 1H enable switches and timeframe selection |
| Display | Market-structure lines, OB, RB, FVG, LP, Origin, Breaker, sessions, labels, status table |
| Sessions | Visibility of Asia, London, Lunch, New York, and London Close windows |
| Calculations | Historical candle depth, nearby key-level count, shBms confirmation limit, OB confirmation limit |
| Strategy parameters | Entry-method selection, split percentage, TP mode, fixed risk/reward, maximum concurrent-trade setting |
| Time restrictions | Monday filter, manual news timestamps, pre/post-news minutes, annual blackout settings |

### Manual News Times

Enter news timestamps in this format:

\`\`\`text
YYYY-MM-DD HH:MM
\`\`\`

Use fixed New York UTC-5 time and separate multiple timestamps with commas.

Example:

\`\`\`text
2026-11-25 08:30, 2026-12-02 14:00
\`\`\`

Timestamps are supplied manually. The indicator does not fetch an economic calendar automatically.

### Scope of Settings

Some inputs are retained for the broader strategy specification or a planned automated-trading phase. Their presence in the settings does not mean that Phase 1 executes orders, manages positions, or implements every Phase 2 behavior. The current source is an indicator, not a TradingView strategy script or an Expert Advisor.

## Methodology Overview

The documented workflow is top-down:

1. Start with Monthly and Weekly structure to establish broader context.
2. Review Daily and 4-hour levels for relevant zones and liquidity.
3. Use the 1-hour timeframe for more granular structure and context.
4. Wait for a qualifying liquidity event and confirmation pattern rather than treating an isolated OB or FVG as an automatic trade signal.
5. Consider session and news restrictions before making a trading decision.

Detailed methodology and implementation notes are maintained in the repository's specification files. These documents contain research history and may differ in some details; consult the relevant source documents when investigating a specific rule.

## Validation and Limitations

- The Pine Script source targets **Pine Script version 6**.
- Compilation and runtime behavior must be checked in TradingView's Pine Editor and on representative charts.
- No automated test suite or independent performance validation is included in this repository at present.
- Historical chart markings are not evidence of future performance.
- Session calculations use fixed UTC-5 rather than an automatically daylight-saving-aware New York timezone.
- News filtering depends on timestamps entered manually by the user.
- Phase 1 visualizes market conditions; it does not send orders to a broker, calculate live position size, or manage open positions.

## Roadmap

- [x] Document the ICT methodology and technical requirements
- [x] Implement the Phase 1 TradingView indicator in Pine Script v6
- [ ] Validate detections across instruments, timeframes, and historical conditions
- [ ] Reconcile specification differences and finalize Phase 1 acceptance criteria
- [ ] Develop Phase 2 as a MetaTrader 5 Expert Advisor (MQL5)
- [ ] Backtest and forward-test any automated execution implementation before live use

## Contributing and Development Notes

Before modifying the indicator:

1. Read the relevant technical specification and current Pine Script source.
2. Check related documents in claud, deepseek, and grok for possible differences.
3. Preserve the distinction between implemented Phase 1 behavior and planned Phase 2 functionality.
4. Test Pine Script changes in TradingView and report what was actually tested.
5. Do not commit API keys, credentials, local tool configuration, or audio files. See [AGENTS.md](AGENTS.md) for repository-specific development instructions.

## Disclaimer

This project is provided for educational and research purposes only. It is not investment advice, a recommendation to buy or sell any financial instrument, or a guarantee of trading results. Trading financial markets involves substantial risk, including the possible loss of capital. Always perform your own testing and risk assessment before making trading decisions.
