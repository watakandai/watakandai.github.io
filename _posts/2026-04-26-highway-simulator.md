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

## Motivation

A few years ago I moved to the United States after spending most of my life driving in Europe. The adjustment was jarring — not because of the left-hand traffic or the unfamiliar road signs, but because of something subtler and more dangerous: nobody keeps right.

On a German Autobahn or a French motorway, the rule is simple and universally respected. You drive in the rightmost lane that fits your speed. You move left only to overtake, and the moment you have passed, you return. The fast lane is genuinely fast — it flows, it breathes, and slower drivers never linger there. The result is a kind of emergent order: lanes self-sort by speed, gaps are predictable, and merging is almost mechanical in its reliability.

My first highway experience in the US felt chaotic by comparison. I found trucks cruising at 55 mph in the left lane while sports cars threaded through gaps on the right. I watched a driver move into the passing lane, slow to match traffic, and stay there for forty miles without any apparent awareness that anyone was behind them. Lane changes were unpredictable — sometimes left, sometimes right, with no obvious logic. Merging felt like a negotiation rather than a procedure.

I started wondering: is this actually worse, or does it just feel worse? Could the American style — chaotic as it appears — somehow produce equivalent throughput? Or is the European discipline genuinely more efficient, and if so, by how much? I could not find a simple, interactive model that isolated the behavioral difference cleanly, so I decided to build one.

## About the Simulator

The simulator uses a physics-based model to track individual vehicles on a multi-lane highway over time. Both driving cultures are simulated on identical vehicle populations — same arrival rates, same speed distributions, same road geometry — so that the only variable is driver behavior.

### Physics model

Vehicle following distance scales with speed using the classic kinematic headway formula:

$$d_{\text{safe}} = v \cdot T_{\text{react}} + d_{\text{min}}$$

where $v$ is the vehicle's current speed, $T_{\text{react}}$ is the driver's reaction time, and $d_{\text{min}}$ is a fixed minimum bumper gap. A car travelling at 33 m/s with a 1.2 s reaction time needs roughly 45 m of clearance; a slow 15 m/s car needs only about 23 m. This means fast lanes naturally self-space more, which mirrors real highway behaviour.

To prevent vehicles from tunneling through each other at large time steps, each simulation step is subdivided into sub-steps chosen so that no vehicle can travel more than half a minimum gap in one sub-step. Within each sub-step, a backward sweep clamps any vehicle that has gotten too close to the one ahead.

### Spawn process

Vehicles arrive according to a Poisson process with rate λ. Speeds are drawn from a single shared Gaussian distribution $\mathcal{N}(\mu, \sigma^2)$, clamped to a sensible range. Critically, both EU and US simulations consume an identical pre-generated roster of vehicles — same arrival times, same speeds, same initial lane assignments — so differences in outcome are attributable purely to driving behaviour, not to lucky or unlucky vehicle populations.

### European driving behaviour

European drivers follow a strict keep-right discipline:

1. **Speed-matched lane assignment.** On entry, each vehicle is placed in the lane whose target speed $\mu_{\text{lane}}$ most closely matches its own speed. A slow vehicle starts in the right lane; a fast one starts in the left lane.
2. **Proactive keep-right.** Every time step, a driver checks whether their speed falls more than a configurable threshold below their current lane's target speed. If so, they move one lane right — even without prompting from a tailgater.
3. **Yield to faster traffic.** If a faster vehicle is approaching from behind within a configurable look-back distance, the slower driver proactively moves right to clear the lane.
4. **Overtake left, return immediately.** When blocked by a slower vehicle ahead, a driver moves left to pass. As soon as the obstacle is cleared, they return right. The fast lane is used exclusively as a transient overtaking space.

### American driving behaviour

American drivers are modelled with two key differences:

1. **Random lane assignment.** On entry, vehicles are placed in a uniformly random lane regardless of speed. A slow car is just as likely to spawn in the left lane as a fast one.
2. **Bidirectional passing.** When blocked, a driver will pass on either the left or the right — whichever adjacent lane has a sufficient gap. There is no preference for returning to a slower lane after passing.

### Metrics

| Metric | What it captures |
|---|---|
| Throughput | Vehicles completing the road — primary flow efficiency measure |
| Average effective speed | Mean speed across all vehicle-steps, including time spent slowing behind others |
| Lane changes per vehicle | Disruption and weaving frequency |
| Congestion events | Times a vehicle is blocked below its safe following distance |
| Lane utilization SD | Standard deviation of occupancy across lanes — lower means more balanced flow |

### Features

- **Fair comparison:** Both modes receive identical vehicle rosters — same arrivals, same speeds, same road.
- **Configurable lane speed profiles:** Each lane has a target speed μ used for EU lane assignment. Adjust these in the Lane speed profiles tab.
- **Velocity-dependent safe distances:** Following gap scales with speed so fast lanes self-space more.
- **Rigorous collision checking:** Sub-stepping ensures no vehicle can tunnel through another regardless of time step size.
- **Animation playback:** Watch the simulation unfold in real time with adjustable playback speed. Overtaking vehicles are highlighted in amber (EU) or red (US).
- **Monte Carlo simulations:** Run paired batches — each pair uses an identical roster — and see distributional results with Welch t-tests and p-values.
- **Video export:** Save any single run or full Monte Carlo batch as a WebM file.

## What the results show

Under typical highway conditions — moderate arrival rates, mixed speeds, three or more lanes — the European model consistently produces lower congestion event counts and more balanced lane utilization. The fast lane in EU mode genuinely flows faster because slow vehicles have left it. The US model tends to produce more lane changes per vehicle as drivers thread through gaps on both sides, and the left lane clogs more readily as slower drivers settle in it permanently.

Throughput differences are smaller than intuition might suggest, because the US model's bidirectional passing allows some recovery — a fast driver who cannot pass on the left can try the right. But average effective speed tends to favour the EU model, particularly at higher arrival rates where the left-lane congestion in US mode compounds.

The Monte Carlo results make these differences statistically rigorous. With 100 paired runs, the Welch t-test typically returns p < 0.01 on congestion events and lane utilization SD, meaning the behavioral differences reliably produce measurable outcomes across random vehicle populations.

## Limitations and future work

This is a microscopic simulation with simplified behaviour rules. Real drivers are more complex — they have reaction delays, make errors, exhibit aggression, and respond to road geometry. Some directions worth exploring:

- **Merging and on-ramps.** The current model assumes a closed road with no entry or exit points mid-stream.
- **Truck and mixed-vehicle traffic.** Different vehicle classes with different speed distributions and gap requirements would add realism.
- **Calibration against real data.** Loop detector data from US and European highways could be used to fit arrival rates and speed distributions to specific corridors.
- **Autonomous vehicle modes.** A fully cooperative AV fleet could serve as a third comparison mode — what happens when every vehicle follows the optimal policy?

Despite these simplifications, the simulator captures the essential behavioural difference cleanly enough to draw a clear conclusion: lane discipline matters. The European keep-right norm produces measurably smoother, faster, and less congested highways when all else is equal.

<iframe src="{{ site.baseurl }}/assets/highway_simulator.html" width="100%" height="900px" style="border:none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1);"></iframe>