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

This is an interactive highway traffic simulator that compares European (keep-right) and American (random lane) driving behaviors.

<iframe src="{{ site.baseurl }}/assets/highway_simulator.html" width="100%" height="900px" style="border:none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1);"></iframe>

## About the Simulator
The simulator uses a simple physics model to calculate vehicle movements, lane changes, and congestion events. You can configure various parameters such as arrival rates, speed limits, and driver reaction times to see how they impact overall throughput and safety.

### Features:
- **Fair Comparison:** Both modes receive identical vehicle rosters.
- **Monte Carlo Simulations:** Run multiple batches to see statistical differences.
- **Video Export:** Save your simulations as WebM files.
