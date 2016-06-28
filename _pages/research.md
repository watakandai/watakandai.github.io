---
permalink: /research/
title: Research interests
header:
  overlay_image: ../images/allen_brain.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
---

Computational neuroscience is unique among areas of theoretical and quantitative biology for its emphasis on *representation* and *information processing* -- more than any other organ, the brain *computes*. Representations are apparent in sensory systems, where neural responses can be interpretted as encoding information about features of a visual scene, for example. Notions of representation in the motor system are less clear, and it remains an open question whether primary motor cortex is best thought of as encoding information about limb kinetics, kinematics, or some other variables. Alternatively, perhaps the notion of such explicit encoding is not relevant, and a more *dynamical systems*-based approach is instead warranted. These issues of representation carry weight in the design of brain-computer interfaces (BCIs), where the successful implementation of sophisticated BCIs will likely rely on being able to properly *interpret the neural code*. I'm interested in understanding how the brain performs motor control and how that knowledge can be used to build better BCIs.

More generally, as a mathematician, I'm excited by the technologies and data driving neuroscience forward. Optical and electrical recording technologies now allow large populations of neurons to be recorded from simultaneously. For instance, optical imaging can now be performed for whole organisms, in conjunction with free behaviour, offering the chance for unprecedented insight into the functioning of (lower) organisms. I'm interested in statistical methods for learning dynamical structure in such high-dimensional datasets. 

## Ongoing projects

### Concurrent-use BCIs

<figure style="float: left; width: 400px;"><img src="../images/transferentropy_clean2.png" width="400"><figcaption>Caption</figcaption></figure>

### Neuron and behavior tracking in a model cnidarian

<figure style="float: left; width: 300px;"><img src="../images/hydra_wireframe_inverted.png" width="300"><figcaption>Tracking Hydra behaviour through video in order to aid calcium imaging neuron tracking</figcaption></figure>

## Previous projects

### Cholinergic retinal waves as traveling pulses in an excitable medium

<figure style="float: left; width: 300px;"><img src="../images/retinalwaves2.png" width="300"><figcaption>Frames from simulation of retinal waves model. Top frames show a spreading wave of depolarizing amacrine cells modulated by a slow refractory field (middle frames) and an extra-cellular acetylcholine concentration (bottom frames).</figcaption></figure>

In collaboration with Prof. Nathan Kutz and Dr. Kevin Ford, we developed a reaction-diffusion model of correlated spontaneous activity in the developing retina — activity known as retinal waves. Here, the developing retina was modeled as an excitable medium, allowing for the existence of retinal waves to be predicted above an excitability threshold that is determined from the model’s analysis. The approach is novel in its ability to determine such a criterion in a semi-analytic fashion. This was achieved by separating the dynamics into fast and slow timescales and using numerical continuation software to determine the existence of a traveling wave front for some combinations of the model’s parameters. Above you’ll find a numerical simulation of the model.

* Read more: [PLoS Comp Bio Dec 2014](../docs/retinalwaves.pdf)

### On bistability as a control mechanism of intrinsic apoptosis

<figure style="float: right; width: 280px;"><img src="../images/bcl2.png" width="280"><figcaption>Proposed control mechanism of Bcl-2 regulated apoptosis is through bistable dynamics shown here. Inert Bak is (in)activated in a discontinuous fashion, providing a robust switch.</figcaption></figure>

Intrinsic apoptosis (programmed cell death) is regulated by the Bcl-2 family of proteins, whose interactions on the mitochondrial membrane, on receiving sufficiently strong pro-apoptotic signals, can initiate caspase activation — a point of no return for the cell. Determining how this regulation is implemented, and how it can malfunction, is important for the development of anti-cancer therapies. With Prof.s Terry Speed, Kerry Landman and Ruth Kluck, we developed a kinetic mass-action model of Bcl-2 interactions relevant to a simplified mitochondrial assay containing only a subset of Bcl-2 members. A proposed theoretical control mechanism through which the Bcl-2 family may regulate apoptosis is a bistable switch. By careful calibration of our model with known and estimated kinetic reaction rates we show that bistability is unlikely to play a role in the reduced assay.

* Read more: [BMES poster Sept 2013](../docs/lansdell_BMES.pdf), [MPhil thesis](../docs/mphil.pdf)

### Integrating genomic tiling array data into gene prediction with generalized hidden Markov models

<figure style="float: center; width: 700px;"><img src="../images/tilegene-1024x438.png" width="700"><figcaption>Incorporating mRNA expression data (center) with genomic DNA signals to predict coding and non-coding eukaryotic gene structures (top).</figcaption></figure>

My first research project, with Tony Papenfuss, was in computational gene prediction, in which our goal was to build a generalised hidden Markov model to locate genes in eukaryotic DNA using both the sequence content and evidence from genomic tiling arrays. We showed that the incorporation of tiling array data was able to improve the performance of the gene predictor, to a degree that made the resulting predictor competitive with then state-of-the-art predictors.

* Read more: [my honours thesis](../docs/honours_thesis.pdf)
