# 🤝 Contributing to DCOS

Welcome. The fact that you're reading this means you're curious about distributed cognitive architecture — and that's exactly the kind of curiosity this project needs.

## Current Phase: Research & Design

DCOS is in its foundational phase. **There is no code to contribute to yet** — and that's by design. We're building the theoretical architecture before writing a single line of implementation. This is where you can have the most impact.

## How to Contribute Right Now

### 💡 Propose a Cognitive Profile
Think of a domain where specialized reasoning would help an LLM perform better. Write a brief proposal:

- **Profile name**: What would you call it?
- **Domain**: What kind of problems does it address?
- **Key phases**: What cognitive steps should the profile follow?
- **Common errors**: What mistakes does an LLM typically make in this domain? (This informs the introspective few-shots)
- **Sub-routes**: When should this profile invoke another profile?

Open an issue with the tag `profile-proposal`.

### 🔬 Challenge the Architecture
Read the [Architecture document](docs/ARCHITECTURE.md) and find the weak points. We specifically welcome:

- Scalability concerns (what happens at 100 profiles? 1000?)
- Edge cases in routing logic
- Token budget analysis (when does the overhead exceed the benefit?)
- Comparison with existing frameworks we may have missed

Open an issue with the tag `architecture-review`.

### 📚 Literature Contributions
Found a paper, framework, or technique that's relevant? Share it with context:

- What is it?
- How does it relate to DCOS?
- What component would it improve?

Open an issue with the tag `literature`.

### 🧪 Benchmark Proposals
Suggest specific benchmarks or evaluation tasks where DCOS should be tested against monolithic prompting approaches. We need concrete, reproducible comparisons.

Open an issue with the tag `evaluation`.

---

## Principles for Contributors

**Think in systems, not in features.** Every contribution should consider how it affects the whole — not just the component you're touching.

**Show the error.** When proposing few-shot examples, always include the initial mistake and the moment of correction. Teaching doubt is more valuable than teaching answers.

**Respect the fractal.** New profiles must follow the MANIFEST.json standard. This isn't bureaucracy — it's what enables composability. A profile that breaks the standard breaks the ecosystem.

**Be constructive in criticism.** This project is built on adversarial self-testing as a design principle. We welcome rigorous challenges. We don't welcome dismissiveness.

---

## Future Contribution Paths

As the project matures, we'll open contribution paths for:

- Implementation of the Cortex router
- MCP server development
- Cognitive profile test suites
- Cross-profile integration testing
- Documentation and tutorials
- Translations

---

<p align="center">
  <em>"In a lichen, every symbiont contributes something the others cannot produce alone."</em>
</p>
