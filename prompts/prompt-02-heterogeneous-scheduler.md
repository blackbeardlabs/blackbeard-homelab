Design an efficient strategy for scheduling jobs on a small compute cluster with heterogeneous machines.

Scenario:
- Some machines are fast but have limited memory.
- Some machines are slower but have much larger memory.
- Some jobs are short and latency-sensitive.
- Some jobs are long and require large context or large models.
- Only one model can run on a node at a time.

Task:
Propose an algorithm or decision framework for assigning jobs to nodes.

Requirements:
- Explain the strategy clearly.
- Include the factors used in scheduling decisions.
- Discuss tradeoffs and failure cases.
- Provide simple pseudocode for the scheduler.
- Be practical rather than theoretical.
