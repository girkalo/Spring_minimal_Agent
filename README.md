

# Resonance Frequency Agent

### LLM-Driven Autonomous Experimental Discovery

This project implements an **Agentic AI system** that autonomously discovers the **resonance frequency of a damped spring–mass system** through **experimentation** rather than analytic calculation.

The agent behaves like a **scientist performing experiments**:

1. Propose a driving frequency
2. Perform an experiment (simulate the system)
3. Measure the steady-state oscillation amplitude
4. Store results in memory
5. Design the next experiment

The resonance frequency is discovered as the **frequency where the steady-state amplitude is maximal**.

---

#  Agentic System Architecture

The project implements a full **Agent + Environment architecture**.

```
User
 ↓
LLM Agent (Brain / Reasoning)
 ↓
Agent Tools
 ↓
Environment Simulation
 ↓
Experimental Observation
 ↓
Memory Update
 ↓
Next Experiment Decision
```

The LLM does **not know the physics equations directly**.
Instead it interacts with the system **only through experimental measurements**, mimicking real scientific experimentation.

---

#  Agent Components

# Brain (LLM)

The **Large Language Model acts as the cognitive layer of the agent**.

Responsibilities:

* interpret user requests
* decide which experiments to perform
* propose new driving frequencies
* analyze experimental results
* detect the resonance peak
* generate the final explanation

The LLM performs **iterative reasoning** based on experimental observations.

Example reasoning loop:

```
Observed high amplitude near 0.11 Hz
→ resonance likely nearby
→ test slightly above and below
→ refine around the peak
→ determine final resonance frequency
```

This mirrors the **scientific method**.

---

#  Memory

Agent memory stores all performed experiments.

```
samples = [(f1, A1), (f2, A2), ...]
```

Where:

* `f` = tested driving frequency
* `A` = measured steady-state amplitude

Memory enables the agent to:

* understand the shape of the response curve
* identify regions with large amplitudes
* refine the search around the peak

The agent therefore builds an **empirical model of the system response**.

---

#  Environment

The environment represents the **physical system being studied**.

Class:

```
OscillatorEnvironment
```

The environment contains the hidden system parameters:

```
m – mass
k – spring stiffness
b – damping coefficient
```

These parameters are **not used by the agent for analytic computation**.

Instead, the environment exposes only an **experimental interface**:

```
measure(f) → amplitude
```

This simulates performing a real physical experiment.

---

#  Agent Tools

The agent interacts with the environment using tools.

### Measurement Tool

```
measure_amplitude(frequency)
```

Input:

```
driving frequency f
```

Output:

```
steady-state amplitude A(f)
```

Internally this tool:

1. Simulates the oscillator dynamics
2. Waits until transient motion disappears
3. Measures steady-state amplitude

This acts as the **agent’s experimental instrument**.

---

#  Physics Simulation

The environment simulates a **forced damped harmonic oscillator**:

[
m x'' + b x' + k x = F_0 \sin(2\pi f t)
]

Where:

```
m – mass
b – damping
k – stiffness
F0 – forcing amplitude
f – driving frequency
```

The system is converted into a first-order ODE:

```
x' = v
v' = (F(t) − b v − k x) / m
```

and solved numerically using **SciPy's ODE solver**.

---

#  Measuring Steady-State Amplitude

Two measurement methods are implemented.

### Peak-to-Peak Method

```
A = (max(x) − min(x)) / 2
```

Simple but sensitive to noise.

---

### Lock-In Detection (recommended)

The signal is projected onto

```
sin(2π f t)
cos(2π f t)
```

to isolate the response at the **driving frequency**.

Advantages:

* ignores transient oscillations
* robust to noise
* provides accurate steady-state amplitude

---

#  Experimental Search Strategy

The agent performs a **two-phase experimental search**.

---

## Phase 1 — Coarse Scan

The agent first samples a wide frequency range:

```
f_min → f_max
```

Example:

```
[0.01, 0.03, 0.05, ..., 0.33]
```

Goal:

```
identify approximate resonance region
```

---

## Phase 2 — Local Refinement

Once a promising region is found, the agent performs **local experiments near the peak**.

Strategies include:

* midpoint testing
* quadratic peak estimation
* sampling around the current maximum

Goal:

```
precisely locate the peak of A(f)
```

---

#  Agent Reasoning Behaviour

The agent performs **experimental optimization of a 1-D function**:

```
A(f)
```

The reasoning process follows:

```
measure → observe → update memory → choose next experiment
```

Example reasoning:

```
Amplitude increases toward 0.11 Hz
→ resonance likely nearby
→ test 0.108 and 0.115
→ detect amplitude drop
→ peak near 0.112 Hz
```

This resembles **adaptive experimental design**.

---

#  Output

The agent returns:

```
resonance frequency
maximum steady-state amplitude
```

and generates a visualization of:

```
A(f) vs frequency
```

showing the resonance peak.

---

#  Example Interaction

```
User:
Hi, we have a spring with m=10, k=5, b=0.4.
Find the resonance frequency experimentally.

Agent:
Running experimental search...

The resonance frequency was found experimentally to be approximately 0.115 Hz.
This corresponds to the peak of the steady-state amplitude curve A(f).
```

A plot of the resonance curve is also generated.

---

#  Example Result

System parameters:

```
m = 10
k = 5
b = 0.4
```

Experimental result:

```
f_res ≈ 0.115 Hz
```

Theoretical resonance:

```
≈ 0.112 Hz
```

The small difference arises because the agent performs **discrete experimental sampling**.  The relative error is |0.112 - 0.115| / 0.112 = 2.6%, what is fair for an experimental model.



