# 🏗️ DCOS — Technical Architecture

> This document describes the intended architecture. Implementation has not yet begun.
> See [ROADMAP](../README.md#roadmap) for current status.

---

## System Overview

DCOS operates as a **cognitive middleware layer** between the user and the LLM. It intercepts queries, classifies them, loads the appropriate reasoning structures, and orchestrates multi-profile execution.

```
┌────────────┐     ┌──────────────────────────────────────────┐     ┌─────────┐
│            │     │              DCOS LAYER                   │     │         │
│   USER     │────▶│  Cortex → Profile(s) → Merge → Output   │────▶│   LLM   │
│            │     │                                           │     │         │
└────────────┘     └──────────────────────────────────────────┘     └─────────┘
```

Critically, DCOS does **not** modify the LLM. It composes prompts. The LLM receives a standard text input — it simply happens to be a highly structured, dynamically assembled cognitive scaffold.

---

## The MANIFEST.json Specification

Every cognitive profile is identified by its MANIFEST. This is the interface contract.

```json
{
  "schema_version": "1.0",
  "profile_id": "cognitive-systems-v1",
  "display_name": "Systems Thinking Profile",
  "description": "Specialized in analyzing complex systems with interconnected components, feedback loops, and emergent behavior.",
  "author": "lichen-collectives",
  "version": "0.1.0",

  "activation": {
    "task_types": ["systemic_analysis", "network_design", "complexity_mapping"],
    "trigger_keywords": ["system", "network", "feedback", "emergence", "topology"],
    "conditions": {
      "min_complexity": 0.5,
      "min_ambiguity": 0.3
    }
  },

  "cognitive_modules": [
    "systems_thinking",
    "causal_reasoning",
    "feedback_loop_detection",
    "stress_testing"
  ],

  "phases": [
    {
      "id": "component_mapping",
      "file": "phases/01_component_mapping.json",
      "description": "Identify all actors, components, and their relationships"
    },
    {
      "id": "feedback_analysis",
      "file": "phases/02_feedback_analysis.json",
      "description": "Trace reinforcing and balancing feedback loops"
    },
    {
      "id": "stress_test",
      "file": "phases/03_stress_test.json",
      "description": "Adversarial testing of system resilience"
    },
    {
      "id": "synthesis",
      "file": "phases/04_synthesis.json",
      "description": "Unified model with confidence calibration"
    }
  ],

  "few_shots": [
    "few_shots/systems_error_correction_01.json",
    "few_shots/systems_error_correction_02.json"
  ],

  "sub_routes": {
    "ethical_dimension_detected": {
      "target": "lichen-collectives/cognitive-ethical",
      "condition": "phase_output contains ethical_tension",
      "merge_strategy": "integrate_constraints"
    },
    "mathematical_formalization_needed": {
      "target": "lichen-collectives/cognitive-analytical",
      "condition": "phase_output requires quantitative_model",
      "merge_strategy": "append_phase"
    }
  },

  "constraints": {
    "max_recursion_depth": 3,
    "max_sub_routes": 2,
    "timeout_per_phase": "model_dependent"
  }
}
```

---

## Routing Logic

### Classification Dimensions

The Cortex classifies every incoming query along five dimensions:

| Dimension | Type | Description |
|-----------|------|-------------|
| `task_type` | enum | Primary cognitive mode required |
| `complexity` | float [0,1] | Number of interacting variables and steps |
| `ambiguity` | float [0,1] | Degree of interpretive openness |
| `domain` | string[] | Subject matter areas detected |
| `urgency` | enum | Whether depth or speed is prioritized |

### Routing Strategies

| Strategy | When Used | Description |
|----------|-----------|-------------|
| **Single** | Low complexity, clear domain | One profile handles the entire task |
| **Sequential** | Multi-step, ordered dependencies | Profile A completes, then Profile B begins |
| **Parallel** | Multi-domain, independent aspects | Profiles A and B execute simultaneously, merged after |
| **Nested** | Sub-problem detected mid-reasoning | Profile A invokes Profile B as a sub-routine, resumes after |

### Merge Strategies

When multiple profiles contribute to a response:

| Strategy | Mechanism |
|----------|-----------|
| `integrate_constraints` | Sub-profile output becomes a constraint for the parent |
| `append_phase` | Sub-profile's phases are appended to the parent's pipeline |
| `parallel_synthesis` | Both outputs are presented to a synthesis phase for fusion |
| `override` | Sub-profile's output replaces the parent's on the specific sub-topic |

---

## Fractal Recursion Control

To prevent infinite loops, DCOS enforces:

1. **Maximum recursion depth**: Default 3 levels (configurable per-profile)
2. **No circular routing**: A profile cannot route to an ancestor in its current call stack
3. **Budget awareness**: Each recursion level consumes context window tokens; the system tracks remaining budget and gracefully degrades to shallower reasoning when approaching limits

```
Level 0: Cortex routes to cognitive-systems/
  Level 1: cognitive-systems/ routes to cognitive-ethical/
    Level 2: cognitive-ethical/ routes to cognitive-cultural/
      Level 3: MAX DEPTH — cognitive-cultural/ executes standalone
```

---

## Few-Shot Format

Introspective few-shots follow a standardized JSON structure:

```json
{
  "id": "systems_error_01",
  "query": "How would you design a resilient supply chain?",
  "cognitive_trace": {
    "initial_response": {
      "content": "A resilient supply chain needs redundant suppliers...",
      "error_type": "surface_level_pattern_matching",
      "error_description": "Jumped to solution without mapping the system"
    },
    "introspection": {
      "diagnosis": "I skipped component mapping and went straight to solutions. I don't know what components need resilience.",
      "correction": "First map all actors and dependencies, then identify single points of failure, then design redundancy around those specific points."
    },
    "corrected_response": {
      "content": "Before designing solutions, we must map the system...",
      "phases_used": ["component_mapping", "feedback_analysis", "synthesis"]
    }
  }
}
```

The key innovation: the few-shot **includes the error and the moment of recognition**. This teaches the model the *metacognitive skill* of self-monitoring, not just the domain knowledge.

---

## Model Compatibility

DCOS generates standard text prompts. Any model that can follow structured instructions can execute a cognitive profile. However, effectiveness varies:

| Capability Required | Minimum Model Class | Optimal Model Class |
|---------------------|--------------------|--------------------|
| Single profile, light mode | 7B+ parameter models | Any modern model |
| Single profile, full pipeline | 13B+ or commercial API | Claude, GPT-4, Gemini |
| Multi-profile with routing | Commercial API tier | Claude Opus, GPT-4 |
| Fractal recursion (depth 3+) | 128K+ context window | Claude Opus, Gemini 1.5 |

---

## Integration Paths

### Path 1: Manual (Copy-Paste)
Load a profile's `system_prompt.md` and `few_shots/` into your chat interface. Simplest approach, zero infrastructure.

### Path 2: MCP Server
A Model Context Protocol server that exposes cognitive profiles as tools. The LLM can invoke `load_profile("cognitive-systems")` during reasoning.

### Path 3: API Middleware
A lightweight service that sits between your application and the LLM API. Intercepts requests, applies routing, assembles the cognitive scaffold, and forwards the enhanced prompt.

### Path 4: Agent Framework Integration
Plugins for existing frameworks (LangChain, CrewAI, AutoGen) that expose cognitive profiles as specialized agents in a multi-agent system.

---

<p align="center">
  <em>Architecture is not code. Architecture is the space in which code becomes thought.</em>
</p>
