---
title: 'Beyond LLMs: The rise of Agentic AI'
date: 04/08/2026
permalink: /posts/2026/08/2026-08-04-AgenticAI/
tags:
  - Agentic AI
  - Agents
  - AI Agents
  - Artificial Intelligence
  - Multi-Agent Systems
  - LLMs
  - Automation
---

In my previous post, on "The Rise of Physical AI", we explored how the robotics industry is currently in a transition from static algorithms to continuous **Perception-Action Loops**, allowing autonomous systems to perceive, reason and act in real-world environments.

Interestingly enough, we are currently witnessing this same architectural shift unfold across the Software Engineering industry. Since Large Language Models became main stream in 2022, LLMs have become the dominant paradigm where the user provides a prompt and then the model outputs a response. If the output is incorrect, then the user must manually intervene, tweak the prompt and then try again. This process is fundamentally a reactive, open-loop with the user always intervening.

In 2026, we are in the middle of a shift from static text-based prediction to **Agentic AI**, where the neural models act autonomously, decompose complex objectives into multi-step execution plans, invoke external tools and even self-correct when encountering errors.

## Background
### What is Agentic AI?
If Physical AI is about giving algorithms a body to interact with physical space, then Agentic AI is about giving them access to digital execution environment-APIs, shell terminals, web browsers and databases.

AI Agents are often reffered to a LLM agents where the core language model acts as the "brain" behind the operation rather than just generating text. An AI Agent is a software system capable of autonomously reasoning, setting sub-goals and performing tasks on behalf of a user.

### How do AI Agents work?
AI agents operate using a continuous framework known as **ReAct (Reasoning and Acting)**. It was first introduced by researchers in order to bridge the gap between thinking and doing. A ReAct agent iteratively goes through a loop where:
1. **Thought:** The agent analyses what the current situation actually is and then identifies what the next action or information it needs next.
2. **Action:** The agent then executes a specific function. For example, querying a database or running a Python script.
3. **Observation:** The agent then ingests the result of that previous action.
4. **Iteration/Reflection:** The *Thought, Action, Observation* cycle repeats until the AI agent determines whether it has satisfied the original user request/prompt.

## Core Ideas
### The Mathematical Foundation of AI Agents
A traditional large language model computes a conditional probability over text tokens, predicting the next word $y_t$ given the sequence of previous words:
$$P(y_t) = \pi(y_{<t})$$

However, an Agentic AI doesn't solve the problem in a single forward pass. It instead models the task as a closed-loop **Markov Decision Process (MDP)** defined by the tuple $(\mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R}, \gamma)$, where:
* $\mathcal{S}$: The state of the environment (e.g., terminal output, current file contents, API JSON responses).
* $\mathcal{A}$: The action space (e.g., tools the agent can use, for example: `SearchWeb(query)` or `ExecuteCode(code)`).
* $\mathcal{P}$: The state transition probability resulting from executing action $a_t$.
* $\mathcal{R}$: The reward or goal verification function.

Rather than predicting a word, the agentic policy $\pi$ selects an optimal action $a_t \in \mathcal{A}$ based on a trajectory history $h_t$:
$$a_t \sim \pi(a_t \mid h_t, L)$$

where $L$ is the overarching objective and $h_t = (s_0, a_0, s_1, a_1, \dots, s_t)$ represents the continuous observation-action feedback loop. The agent maintains context across this trajectory where it learns from its failed actions and adjusts its subsequent thoughts.

### Implementing a Basic ReAct Loop in Python
```python
import openai

def executeTool(actionName, actionInput):
    if actionName == "calculate":
        return str(eval(actionInput))
    elif actionName == "search":
        return "The weather in London is 14°C."
    return "Tool not found!"

def runAgenticLoop(userPrompt, maxIterations=5):
    systemPrompt = """
    You are an AI Agent with access to the following tools: [calculate, search].
    You must follow this exact format:
    Thought: Consider what to do next.
    Action: The tools to use.
    Action Input: The input to the tool.
    Observation: (Provided by the system)
    ... (Repeat until you have the final answer)
    Final Answer: The answer to the user's request.
    """
    
    messages = [
        {"role": "system", "content": systemPrompt},
        {"role": "user", "content": userPrompt}
    ]
    
    for step in range(maxIterations):
        #Generate the LLM next Thought and Action
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=messages,
            stop=["Observation:"])

        agentReply = response.choices[0].message.content
        messages.append({"role": "assistant", "content": agentReply})
        
        #Check if the agent has reached a conclusion
        if "Final Answer:" in agentReply:
            print("Finished:", agentReply.split("Final Answer:")[-1].strip())
            return
            
        #Parse the action and execute it
        if "Action:" in agentReply and "Action Input:" in agentReply:
            lines = agentReply.split('\n')
            action = next(l.split("Action:")[1].strip() for l in lines if "Action:" in l)
            actionInput = next(l.split("Action Input:")[1].strip() for l in lines if "Action Input:" in l)
            #Execute the tool which is the Act phase
            observation = execute_tool(action, actionInput)
            #Feed the result back into the context which is the result phase
            messages.append({"role": "user", "content": f"Observation: {observation}"})
            print(f"Executed {action}({action_input}) -> Result: {observation}")

    print("Agent has failed to reach a final answer within the iteration limit!")

#Example trigger:
#run_agentic_loop("What is the square root of 144 multiplied by 3?")
```
## Findings & Results
The shift from the isolated conversational chat LLMs to autonomous agentic pipelines has resulted in significant operational insights over recent years:
- **Error Recovery via Reflection:** Benchmark evaluations have consistently shown that adding an iterative self-reflection loop, allowing an AI agent to execute code, inspect test failures or stack traces and revise its solution, can significantly improve coding performance compared with single-pass generation. This approach underpins many state-of-the-art coding agents evaluated on benchmarks such as SWE-bench.
- **Specialisation Beats Monotlithic Scale:** The industry has moved away from building one massive prompt to do everything. Instead, developers are now utilising Multi-Agent Systems (MAS) via frameworks like LangGraph and CrewAI. In these architectures, there are specialised micro-agents. For example, a "Coder Agent", a "Reviewer Agent" and a "Tester Agent" where they collaborate and debate, mimicking a real life software engineering team.
- **Standardised Tool Integration:** Historically, connecting LLMs to custom enterprise tools required fragmented API wrappers. The widespread adoption of the Model Context Protocol (MCP) has acted as a "USB-C port for AI," allowing agents to dynamically query and securely connect to external databases and tools without hard-coded code.

## My Takeaways:
After analysing the architectural shift towards Agentic AI a few points stood out to me:
- **Structural Parallelism with Physical AI:** The algorithmic loop that drives the digital software agent attempting to fix a bug is mathematically equivalent to a robotic arm adjusting its gripper force. They both rely heavily on closed-loop feedback, environment state observation and iterative error recovery.
- **Tool Access is a Force Multiplier:** Model intelligence is only as useful as its tool access. An agent equipped with access to a terminal, file system and external APIs will consistently outperform a slightly smarter foundational model that is restricted purely to a text-based interface.
- **The Evolution of the Developer:** The role of the software developer is shifting more and more thanks to AI. We are moving away from writing individual syntax blocks to orchestrating systems—designing the state machines, tool protocols and evaluation metrics that govern autonomous multi-agent networks.

## Further Reading & References
- **AI Agents Detailed:** [IBM: What are AI Agents?](https://www.ibm.com/think/topics/ai-agents)
- **ReAct Framework Paper:** [ReAct: Synergizing Reasoning and Acting in Language Models (arXiv)](https://arxiv.org/abs/2210.03629)
- **Self-Reflection Benchmarks:** [Reflexion: Language Agents with Verbal Reinforcement Learning (NeurIPS)](https://openreview.net/forum?id=vAElhFcKW6)
- **Enterprise Agent Adoption:** [Gartner Predicts 40% of Enterprise Apps Will Feature Task-Specific AI Agents by 2026](https://www.gartner.com/en/newsroom/press-releases/2025-08-26-gartner-predicts-40-percent-of-enterprise-apps-will-feature-task-specific-ai-agents-by-2026-up-from-less-than-5-percent-in-2025)
- **Agentic ROI Data:** [Enterprise AI Agent Stats 2026: 80% Embed, 31% Deploy](https://paul-okhrem.com/enterprise-ai-agents-statistics-2026/)

