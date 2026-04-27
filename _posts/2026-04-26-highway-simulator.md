---
title: "Highway Traffic Simulator"
categories:
  - simulation
tags:
  - "traffic"
  - "autonomous vehicles"
  - "simulator"
published: true
---

After moving to the US from Europe, the driving culture shocked me. In Europe, the rule is simple: keep right, pass left, then return. In the US, drivers pick a lane and stay there — slow cars in the fast lane, passing on either side, no apparent logic. I wanted to know if this was actually worse, or just felt worse. So I built a simulator.

<iframe src="{{ site.baseurl }}/assets/highway_simulator.html" width="100%" height="900px" style="border:none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1);"></iframe>

## About the Simulator

The simulator places both driving styles on an identical vehicle roster — same arrival times, same speeds, same road — so the only variable is behavior. European drivers are speed-matched to lanes on entry, keep right proactively, yield to faster traffic, and return after overtaking. American drivers spawn in random lanes and pass on either side without returning.

Following distance scales with speed ($d = v \cdot T_{\text{react}} + d_{\text{min}}$), and sub-stepping prevents vehicles from tunneling through each other at large time steps.

### Features

- **Fair comparison:** Both modes receive identical vehicle rosters.
- **Monte Carlo simulations:** Run multiple batches to see statistical differences.
- **Video export:** Save your simulations as WebM files.

## Results

European-style driving consistently produces fewer congestion events and more balanced lane utilization. The left lane actually flows fast because slow drivers have left it. The throughput gap is smaller than intuition suggests — bidirectional passing in US mode offers some recovery — but average effective speed and congestion favour the EU model, especially at higher traffic densities. With 100 paired Monte Carlo runs, these differences are statistically significant (p < 0.01).

The takeaway is simple: lane discipline matters.