---
layout:     post
title:      "Can First Crack Be Predicted? —— Challenging the Tipping Point of Roasting with Complex Systems Physics and AI"
date:       2024-03-20 12:00:00
summary:    "ARIGATO COFFEE: Mission Control's First Crack pre-detection feature. An account of predicting the tipping point of roasting using data, CSD (Critical Slowing Down) theory, and AI."
categories: engineering data-science coffee
lang: en
ref: arigato-coffee-csd-prediction
---

Over the past few years, having started with a hand roaster, I purchased a compact home roaster, [Aillio](https://aillio.com/), last year and have been roasting coffee beans.

While [Aillio](https://aillio.com/) has its own dedicated roasting log app, [RoasTime](https://roastime.aillio.com/), I switched to the more customizable [Artisan](https://artisan-scope.org/) and discovered that I could stream roasting logs in real-time via Websocket.
This seemed very interesting, so I decided to build a live roasting app that visualizes the logs sent via Websocket in real-time.

With AI being so prevalent, simply visualizing roasting logs in real-time isn't quite enough. So, I also implemented an experimental feature to see if AI could detect the underlying signs of the first crack.

## Chaos Theory Meets Roasting: CSD (Critical Slowing Down) Theory

In coffee roasting, the "first crack," where the cellular structure of the bean breaks down and moisture vaporizes, is a phenomenon as volatile as an untamed horse, accompanied by rapid thermal changes. As I began to understand the importance of temperature management around the first crack, I wondered if its timing could be predicted.

When I asked Gemini about it, it suggested the **CSD (Critical Slowing Down) theory**, a concept used in complex systems science such as meteorology and ecology. It sounded fascinating.

According to CSD theory, "just before a system undergoes a sudden change (phase transition), a **delay in recovery**, where the system struggles to return to its original state, occurs." We can overlay this concept onto the phase transition leading up to the first crack in the roasting process.

## Catching the Signs: Autocorrelation and Variance

To quantify this "delay in recovery," our system primarily monitors the following two indicators in real-time:

1.  **AR(1) Autocorrelation:** The slowing down of state recovery, marked by an increase in the degree to which the immediate past state is carried over.
2.  **Rolling Variance Calculation:** Detecting the phenomenon where the "fluctuations" of the entire system become larger.

The moment these indicators exceed a certain threshold, an alert is triggered, signaling that the roasting tipping point is near.

## Verification Data: Backtesting with Past Logs

As a result of backtesting using dozens of accumulated past roasting logs (`.alog`, etc.), we've seen that this CSD algorithm can detect a clear sign with decent accuracy, **30 to 120 seconds before the actual first crack occurs (lead time)**. Also, Claude analyzes the data instantly, which is incredibly convenient.

Although the lead time varies depending on the bean's moisture content and batch size, it provides a sufficient time buffer to prepare yourself that "it's going to crack soon."

<iframe src="/files/csd-backtest-result.html" width="100%" height="900" frameborder="0" style="border:1px solid #2a2a3a; border-radius: 8px; margin: 2rem 0; background: #0a0a0f;"></iframe>

## Current Status: Still Just the Beginning

While the backtesting showed promising results, we are still very much in the experimental stage. I want to continue roasting to find the perfect balance.

---

**You can view the live roasting app here:**
☕️ [ARIGATO COFFEE ROASTERY](https://roastery.arigato.coffee/)
