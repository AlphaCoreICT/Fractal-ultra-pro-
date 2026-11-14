# 🌌 Fractal PRO [Ultimate Institutional Edition]

[![TradingView](https://img.shields.io/badge/TradingView-Pine%20Script%20v6-blue?logo=tradingview&style=for-the-badge)](https://www.tradingview.com/)
[![License](https://img.shields.io/badge/License-Proprietary%20%2F%20MIT-brightgreen?style=for-the-badge)](#license)
[![Build Status](https://img.shields.io/badge/Status-Production%20Ready-success?style=for-the-badge)](#)
[![Performance](https://img.shields.io/badge/AST%20Tokens-34.8k%20%2F%2065k%20(46%25%20Headroom)-orange?style=for-the-badge)](#performance--architecture)

> **The definitive institutional multi-timeframe price action engine for TradingView.**  
> Combining ICT Quarterly Theory, 4-Candle Sweep Cycles, Change in State of Delivery (CISD), T-Spot Equilibrium Entry Zones, Multi-Asset SMT Divergence, and Real-Time Side-by-Side HTF Projections into an ultra-low latency, zero-lag institutional dashboard.

---

## 📑 Table of Contents

- [Executive Overview](#-executive-overview)
- [Key Features](#-key-features)
- [Institutional Core Mechanics](#-institutional-core-mechanics)
  - [1. The 4-Candle Institutional Model](#1-the-4-candle-institutional-model-c1--c2--c3--c4)
  - [2. CISD & IC-CISD (Protected Swings)](#2-cisd--ic-cisd-change-in-state-of-delivery)
  - [3. T-Spot Precision Entry Zones](#3-t-spot-precision-entry-zones)
  - [4. Universal Multi-Asset SMT Matrix](#4-universal-multi-asset-smt-matrix)
  - [5. ICT Quarterly Theory & Micro Cycles](#5-ict-quarterly-theory--micro-cycles)
  - [6. Side-by-Side HTF Candle Visualizer](#6-side-by-side-htf-candle-visualizer)
  - [7. Standard Deviation Projections & Liquidity Targets](#7-standard-deviation-projections--liquidity-targets)
- [Installation Guide](#-installation-guide)
- [Settings & Parameter Reference](#-settings--parameter-reference)
- [Alert System & Automation](#-alert-system--automation)
- [Performance & Architecture](#-performance--architecture)
- [Contributing & Support](#-contributing--support)
- [Disclaimer & License](#-disclaimer--license)

---

## ⚡ Executive Overview

**Fractal PRO [Ultimate Institutional Edition]** is engineered for professional traders, quantitative analysts, and institutional price action specialists. Unlike retail indicators that rely on lagged moving averages or generic oscillators, Fractal PRO maps pure order-flow delivery through fractal time and price.

It automates the complete delivery model taught across institutional concepts: from HTF liquidity sweeps, to LTF shift in market state (CISD), structural order-flow confirmation (IC-CISD), equilibrium re-pricing (T-Spots), and intermarket divergence confirmation (SMT).

---

## 💎 Key Features

- 🕒 **100% Timestamp Anchoring (`xloc.bar_time`)**: Drawings (labels, sweeps, zones, CISD, SMT lines) remain permanently locked to their exact historical candles when panning, zooming, or reloading older chart history.
- 📐 **Dynamic Viewport Auto-Repositioning**: HTF candle projections stay pinned directly within your active screen viewport using `chart.right_visible_bar_time`.
- 📊 **Side-by-Side HTF Visualizer**: View up to **4 distinct higher timeframes simultaneously** directly on your execution chart, complete with bodies, wicks, interval labels, session countdowns, and vertical session boundaries.
- 🎯 **Algorithmic T-Spot Zones**: Automatic calculation of C3 and C4 entry zones combining HTF Open, Full/Wick Equilibrium (EQ), and CISD 50% midpoint confluence.
- 🔍 **Institutional SMT Matrix**: Automated 3-symbol intermarket correlation engine with Institutional Leader vs. Swept Trap classification.
- ⏱️ **ICT Quarterly Theory Engine**: Full support for Asian Micro Cycles (22.5m), 90-minute Cycles, and 6-hour True Day Quarters based on New York session boundaries.
- 🛡️ **Buffer-Safe Memory Guard**: Safe historical offset tracing capped against Pine Script's 2,000-bar limit to prevent runtime crashes.
- 🚀 **High-Speed Object Pooling**: Custom `DrawingBin` pool recycles lines, boxes, and labels with zero garbage collection lag or chart flickering.

---

## 🧠 Institutional Core Mechanics

### 1. The 4-Candle Institutional Model (C1 ➔ C2 ➔ C3 ➔ C4)

```
       C1 (Swept)           C2 (Sweep & Reclaim)           C3 (CISD & Entry)          C4 (Continuation)
       ┌──┐                     ┌──┐                            ┌──┐                      ┌──┐
       │  │                     │  │ ◄── Extreme Wick           │  │                      │  │
       │  │                     │  │                            │  │ ◄── T-Spot Zone      │  │
       └──┘ ◄── Swept Level ─── └──┘ (Closes inside C1)         └──┘ (CISD Body Break)    └──┘ (Expands into Liq)
```

1. **Candle 1 (C1 - Swept Candle)**: Provides the reference liquidity pool (High or Low).
2. **Candle 2 (C2 - Sweep Candle)**: Wicks beyond C1's extreme, trapping breakout participants, and closes back within C1's range (Confirmed Sweep). Tracks running extreme and identifies initial CISD level.
3. **Candle 3 (C3 - Delivery & Trigger Candle)**: 
   - Evaluates LTF body-break of the CISD level.
   - Computes the **C3 T-Spot Entry Zone**.
   - If C3 breaches C2's extreme prior to CISD, it cascades into a fresh C2 or marks invalidation (`xC2`).
4. **Candle 4 (C4 - Continuation Expansion)**:
   - Evaluates delivery into untaken formation liquidity.
   - Computes the **C4 T-Spot Entry Zone**.
   - Invalidates to `xC4` if price breaches C3's extreme.

---

### 2. CISD & IC-CISD (Change in State of Delivery)

- **Main CISD**: Traces back consecutively opposing candles from the swing extreme to identify the institutional change in state of delivery level. A body close through this level confirms institutional order-flow sponsorship.
- **IC-CISD (Protected Swings)**: Tracks internal institutional continuations post-main CISD through C4. Marks protected swing points with dots (`●`), which turn gray once taken/mitigated.

---

### 3. T-Spot Precision Entry Zones

The **T-Spot** represents optimal institutional trade location within the HTF candle:
- **EQ Zone**: Range from HTF Open to the Equilibrium (EQ) of the preceding candle.
- **CISD Zone**: Range from the CISD Open to the CISD 50% midpoint.
- **Overlap Confluence**: When both zones overlap, the T-Spot bounds tighten to the highest-probability institutional order block.
- **State Progression**:
  $$\text{ACTIVE (Untouched)} \longrightarrow \text{TOUCHED (Price reaches midpoint)} \longrightarrow \text{CONFIRMED (New IC-CISD formed)}$$
- **SMT Confluence**: Displays `C3 (★ SMT)` in purple when an SMT divergence aligns with the T-Spot touch.

---

### 4. Universal Multi-Asset SMT Matrix

Institutional divergence (SMT) is evaluated across up to 3 correlated assets simultaneously, automatically identifying the **Institutional Leader** vs. **Swept Trap**:

| Sector | Current Chart | Peer 1 (Secondary) | Peer 2 (Tertiary) | Peer 3 (Third) |
|---|---|---|---|---|
| **US Tech** | `NQ` / `MNQ` / `US100` | `ES` / `MES` | `YM` / `MYM` | `RTY` / `M2K` |
| **US S&P** | `ES` / `MES` / `US500` | `NQ` / `MNQ` | `YM` / `MYM` | `RTY` / `M2K` |
| **US Dow** | `YM` / `MYM` / `US30` | `ES` / `MES` | `NQ` / `MNQ` | `RTY` / `M2K` |
| **Forex** | `EURUSD` / `6E` | `GBPUSD` | `USDX` *(Inverted)* | `AUDUSD` |
| **Precious Metals** | `XAUUSD` / `GC` / `GOLD` | `XAGUSD` (Silver) | `XAUEUR` | `USDX` *(Inverted)* |
| **Crypto** | `BTCUSD` / `BTC` | `ETHUSD` | `SOLUSD` | `TOTAL` |
| **Energy** | `WTI` / `CL` / `USOIL` | `QM` (e-mini) | `UKOIL` (Brent) | `USDCAD` *(Inverted)* |

*Supports Futures, CFDs, Cash Indices, Spot FX, and Crypto tickers universally.*

---

### 5. ICT Quarterly Theory & Micro Cycles

Built-in session cycle engine based on New York (18:00 NY) day-boundary mathematics:
- **Micro Cycle (`SESSION_MICRO`)**: 22.5-minute quarters for precision scalp execution (1m chart). Includes countdown to next session during Asian micro pauses.
- **90-Min Cycle (`SESSION_90`)**: 90-minute session quarters (3m/5m charts).
- **Daily Quarter (`SESSION_6H`)**: 6-hour true market cycles (Q1: 18:00–00:00, Q2: 00:00–06:00, Q3: 06:00–12:00, Q4: 12:00–18:00).
- **Offline & Replay Fallback**: Countdown timer gracefully defaults to last bar close (`time_close`) when markets are closed or in historical bar replay mode.

---

### 6. Side-by-Side HTF Candle Visualizer

Render up to 4 higher timeframes simultaneously without leaving your execution timeframe:
- **HTF 1 (Projection)**: Primary aligned HTF candle set with sweep lines, CISD, and T-Spots.
- **HTF 2, 3, 4 (Side-by-Side)**: Custom timeframes (e.g., 4H, Daily, Weekly) rendered side-by-side with independent gap spacing, vertical separators, and remaining time countdowns.

---

### 7. Standard Deviation Projections & Liquidity Targets

- **Standard Deviation Projections**: Measured from C2 extreme to CISD source candle (Body or Wick modes) across institutional deviation multiples: `0, 1, -1, -2, -2.5`.
- **Formation Liquidity**: Automatic detection and tracking of untaken liquidity pools across C1, C0, C-1, and C-2, drawing dynamic horizontal targets that stop upon mitigation.

---

## 💻 Installation Guide

1. Open **[TradingView](https://www.tradingview.com/)** and navigate to your desired chart.
2. At the bottom of the screen, open the **Pine Editor** tab.
3. Click **Open** ➔ **New Indicator**.
4. Delete any default code in the editor.
5. Copy the entire contents of [`fractal_pro.pine`](./fractal_pro.pine).
6. Paste into the Pine Editor and click **Save** (e.g., naming it `Fractal PRO [Institutional]`).
7. Click **Add to Chart**.

---

## ⚙️ Settings & Parameter Reference

### General & Alignment
- **Fractal Mode**: Select `Automatic`, `Quarterly`, `Custom`, or pre-set pairs (`1m-15m`, `5m-1H`, `15m-4H`, `90m-6H`, `15m-7H`, `1H-1D`, etc.).
- **Bias**: Filter setups by `Neutral` (both directions), `Bullish` only (low sweeps), or `Bearish` only (high sweeps).
- **History**: Control number of historical setups displayed on chart.

### SMT Divergence
- **SMT Mode**: `Automatic` (auto-detects asset class) or `Manual`.
- **Enable Second / Third SMT**: Activate 2-way or 3-way correlation checks.
- **SMT Line Style / Width / Color**: Customize divergence rendering.

### Projections & Liquidity
- **Enable Projections**: Toggle Fibonacci-style deviation levels.
- **Type**: Calculate from candle `Wick` or `Body`.
- **Deviations**: Comma-separated levels (default: `0,1,-1,-2,-2.5`).
- **Formation Liquidity**: Toggle and style C1/C0 untaken liquidity pools.

### Time Filter
- Restrict setup generation to specific institutional killzones (e.g., London Open, New York AM, New York PM) with custom timezone support.

---

### Standard Alert Payload Format
```
{LTF}-{HTF} {TICKER}: [{Potential}] {Bullish/Bearish} {Model Type}
```
*Examples:*
- `1m-15m NQ1!: Bearish Candle 2` (Confirmed C2 sweep + CISD)
- `5m-60m ES1!: Bullish Candle 4` (C4 continuation expansion)
- `1m-22.5m EURUSD: Bullish SMT` (Intermarket divergence confirmed)
- `1m-15m XAUUSD: Potential Bearish Candle 2` (Early live CISD before HTF close)
---

## 📄 Disclaimer & License

**Disclaimer**: This software is designed for educational and analytical purposes only. Trading financial markets involves substantial risk of loss and is not suitable for every investor. Past performance is not indicative of future results.
