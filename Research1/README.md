# Energy-to-Token Production Revisited

### How Numerical Precision, Batch Size, and Context Length Jointly Determine LLM Inference Energy Efficiency

**An Empirical Study on Consumer/Mid-Tier GPU Hardware**

**Author:** Basmala Mohammed

---

## 📌 Overview

This research investigates how **numerical precision, batch size, and context length jointly affect the energy efficiency of Large Language Model (LLM) inference**.

The study focuses on a commonly assumed optimization strategy in LLM deployment: reducing numerical precision through quantization should reduce energy consumption.

Rather than assuming that lower precision automatically leads to lower energy usage, this study evaluates the relationship experimentally through **direct GPU power measurements** and reports energy consumption using **joules per output token (J/token)**.

The experiments were conducted using:

* **Model:** TinyLlama-1.1B-Chat-v1.0
* **GPU:** NVIDIA T4 (16 GB VRAM)
* **Precision:** FP16, INT8, INT4
* **Batch sizes:** 1, 4, 8, 16
* **Context lengths:** 128, 768, 1792 tokens
* **Repeated measurements:** 8 per completed configuration
* **Primary metric:** Energy per output token (J/token)
* **Power measurement:** NVIDIA NVML
* **Quality evaluation:** 100-question MMLU subset

The full factorial design contained **36 planned configurations**, of which **33 completed successfully**. The remaining three configurations failed with CUDA out-of-memory errors.

---

## 🎯 Research Question

> **How do numerical precision, batch size, and context length jointly affect the energy efficiency of LLM inference, measured in joules per output token, and does quantization reliably reduce energy consumption, or is its effect dependent on other operating conditions?**

---

## 🔬 Experimental Design

The study uses a controlled factorial design with three experimental factors.

| Factor              | Levels                |
| ------------------- | --------------------- |
| Numerical Precision | FP16, INT8, INT4      |
| Batch Size          | 1, 4, 8, 16           |
| Context Length      | 128, 768, 1792 tokens |

This results in:

**3 × 4 × 3 = 36 planned configurations**

Thirty-three configurations completed successfully.

The three failed configurations were:

* FP16 — Batch 16 — Context 1792
* INT8 — Batch 16 — Context 1792
* INT4 — Batch 16 — Context 1792

All three failures resulted from CUDA out-of-memory errors.

---

## ⚡ Energy Measurement

GPU power was continuously sampled using **NVIDIA Management Library (NVML)** during inference.

Energy was calculated by numerically integrating the power measurements over time using the **trapezoidal rule**.

The primary metric was:

```text
Energy per Output Token = Total Measured Energy / Number of Output Tokens
```

This end-to-end metric includes both the prefill and decode phases.

### Why End-to-End Energy?

An earlier attempt was made to isolate decode-phase energy by subtracting separately measured generation windows.

However, validation experiments produced:

```text
R² = 0.737
```

together with a physically implausible negative regression slope.

This indicated that the measurement noise of the short-duration windows was too large for reliable differential estimation at this workload scale.

Consequently, the study adopted **end-to-end energy per output token** as the primary metric.

---

## 📊 Main Findings

### 1. Quantization did not consistently reduce energy

Under the tested T4 configuration, **FP16 achieved lower energy per output token than the quantized configurations in most tested conditio**
