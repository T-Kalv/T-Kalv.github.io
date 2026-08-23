---
title: 'The Viability of Bidirectional Neural Interfaces: Reading and Writing to the Human Brain'
date: 23/08/2026
permalink: /posts/2026/08/2026-08-23-BidirectionalNeuralInterfaces/
tags:
  - BCI
  - Brain-Computer Interface
  - Neuroscience
  - Neuroengineering
  - Artificial Intelligence
  - Human-Computer Interaction
  - Closed-Loop Systems
---

In my previous blogs on the "The Rise of Physical AI" and "Agentic AI", we explored how artificial intelligence is currently in a transition from static algorithms to continuous **Perception-Action Loops**. We observed this first in the physical space with robotics and then in digital execution environments with multi-agent systems.

This proposal blog outlines as investigation into a third new frontier (which I've been thinking about for the past couple of years) which is a biological workspace. If we perceive Physical AI giving algorithms a body and Agentic AI gives them digital agency, then this proposal examines how AI interfaces directly with the Human Central Nervous System. Specifically, we will evaluate here the viability of **Bidirectional Brain-Computer Interfaces (BCIs)**, where they not only decode (read) neural activity but successfully encode (write) structured data back into the brain.

## Background
Traditionally, most commercial neurotechnology has been read-only. Standard Brain-Computer Interfaces (BCI) rely on the decoding of neuronal electrical fields to move a cursor or a robotic arm around to different positions.

A bidirectional BCI closes the loop. It translates external data from outside the brain into biological perception or memory. However, writing data to the human brain induces significant challenges such as:
* **File Format:** Unlike a typical solid-state drive (SSD) with fixed block addresses, the brain's memory relies on instead synaptic plasticity which is the ability of the brain to change and adapt to new information that occurs at synapses, the junctions between the neurons that allow them to communicate. The memories here are distributed patters across millions of neurons.
* **Individual Variability:** Each brain encodes concepts differently between one and another. In order to write data, a system must stimulate at the synaptic scale and induce long-term biochemical changes. For example, a pattern that means "Apple" to you may not mean an "Apple" to myself. This is orders of magnitude much more complex than reading data from a coarse motor cortex spike.

## Core Ideas
This project hypothesizes that the most viable pathway to writing data lies in memory prostheses targeting the hippocampus located in the human brain. Rather than injecting synthetic concepts, researchers would use a **MIMO (Multi-Input Multi-Output) non-linear dynamical model** to bypass damaged neural tissue and restore signal transmission.

In terms mathematically, the research will evaluate models that predict the spatiotemporal firing pattern of a destination layer based on the input from a source layer. If $X_t$ represents the continuous neural spike train from the source layer at time $t$, the predicted output $Y_t$ in the destination layer can be modelled using a Volterra series expansion:

$$
Y_t = k_0 + \sum_{n=1}^{N} \sum_{\tau_1, \dots, \tau_n = 0}^{M} k_n(\tau_1, \dots, \tau_n) \prod_{i=1}^{n} X_{t-\tau_i}
$$

Once trained, the BCI reads $X_t$, processes it through MIMO model to predict $Y_t$ and uses mirco-electrodes to stimulate the destination layer with the exact pattern, effectively "writing" the memory sequence back into the brain.

## Hardware Constrains & Current Viability
Moving from the theoretical maths to physical embedded system introduces massive engineering bottlenecks. Here the investigation will benchmark the current state of bidirectional interfaces against recent hardware and AI breakthroughs such as:
* **Modular PCB Architectures:** Due to limited skull real-estate surrounding the human brain, where the typical implant footprint is ~20mm in diameter, a single monolithic board is inefficient. This proposal will evaluate the viability of **vertically stacked PCBS** linked via spring pogo-pins. This architecture allows specific layers (such as the multiplexer arrays used to compress 32+ electrode inputs for microcontrollers) to be hot-swapped or even upgraded in the future without the need for full surgical removal.
* **Connector Safety & Data Transfer:** Standard connectors (like 3.5mm jacks) are a massive risk if snapped inside an implant. We will evaluate here alternative architectures, such as flat, circular magnetic connectors. These designs expose all pad contacts at the surface, allowing for safe 360-degree rotation, natural waterproofing and intelligent negotiation circuits to prevent short circuits when connecting external processing units.
* **Biocompatible Enclosures:** A device must not trigger an auto-immune response. The research will compare the viability of 3D-printed Titanium (highly ductile and easy to manufacture but acts as a Faraday cage blocking wireless signals) versus Zirconium Oxide Ceramics (non-conductive and RF-transparent but prone to shattering).
* **AI Integration:** Evaluating how generative AI models (transformers, GANs and diffusion models) have recently accelerated signal acquisition and data augmentation in BCI development.

### Neural Acquisition & Electronic Architecture
In order to implement this architecture within a sub-25mm circular skull-mounted cavity, the electronic signal chain must address both analog frontend conditioning and I/O pin limits:
* **Analogue Frontend Conditioning:** Action potentials generated by neuronal firing produces micro-volt level potential differences. Precision instrumentation amplifiers and low-noise operational amplifiers step these potentials up to standard logic levels while filtering baseline wander, movement artifacts and 50/60 Hz electromagnetic noise.
* **Addressing with Cascaded Multiplexers:** Interfacing a 32-electrode matrix directly with an embedded microcontroller exhausts quickly the physical GPIO pins. By utilising a binary counter IC paired with cascaded 8:1 multiplexers, the microcontroller only requires three lines which are an analog signal input line, a clock increment pulse line to cycle addresses and a synchronous counter reset line.
* **Firmware & Low-Level Communication:** High-throughput data serialization requires lightweight firmware written natively in C/C++ to stream uncompressed spike trains with minimal jitter and latency.

### Edge AI & Thermal Constraints
Running inference for closed-loop neuromodulation on-device introduces severe physiological constraints:
* **Biological Thermal Budgets:** Brain tissue is highly sensitive to localised heat dissipation. This means that the neural implants must maintain temperature increases below 1°C to prevent cellular damage and inflammation.
* **Quantised Time-Series Transformers:** In place of heavy cloud-hosted models, the system must deploy heavily quantised (INT8/INT4) models and lightweight recurrent or 1D-convolutional networks to perform real-time spike sorting, state estimation and electrical stimulation trigger calculations within strict milliwatt power budgets.

## My Takeaways:
After analyzing the architectural requirements of bidirectional BCIs, a few points stand out to me:
* **The Biological Perception-Action Loop:** The ReAct (framework used by AI agents perfectly mirrors closed-loop BCIs. The model *observes* neural spike trains, *reasons* about the required state transformation and *acts* by delivering targeted electrical micro-stimulation.
* **Hardware-Wetware Bottleneck:** In physical robotics, we are constrained by motor torque and battery density; in BCIs, we are bottlenecked by analog front-end noise, biological thermal dissipation budgets and synaptic spatial resolution.
* **Three Main Intelligent Systems:** We are witnessing the convergence of three foundational frontiers: Physical AI (embodied actuation in real space), Agentic AI (autonomous reasoning in digital space) and Biological Interfaces (direct coupling with human neural circuits).

## Further Reading & References
- **AstroSam BCI Garage Build:** [Making a Brain Computer Interface in My Garage (YouTube)](https://www.youtube.com/watch?v=5YxrpcqkTYA)
- **Synaptic Plasticity Overview:** [What is synaptic plasticity? (University of Queensland)](https://qbi.uq.edu.au/brain-basics/brain/brain-physiology/what-synaptic-plasticity)
- **Hippocampal Prostheses Paper:** [Developing a hippocampal neural prosthetic to facilitate human memory encoding and recall (PubMed/Journal of Neural Engineering)](https://pubmed.ncbi.nlm.nih.gov/29589592/)
- **Closed-Loop Neuromodulation:** [On closed-loop brain stimulation systems for improving the quality of life of patients with neurological disorders (PubMed Central)](https://pmc.ncbi.nlm.nih.gov/articles/PMC10076878/)