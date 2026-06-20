# 🤖 AI Agents — Autonomous AI Systems

> AI Agents are LLMs that can think, plan, and act — using tools and memory to complete complex, multi-step tasks autonomously.

---

## 📖 What is an AI Agent?

An **AI Agent** is an LLM-powered system that can:
1. **Perceive** its environment (through tools and inputs)
2. **Reason** about what to do next (using the LLM)
3. **Act** by calling tools, APIs, or other systems
4. **Observe** the result and continue the loop

**The key difference from a simple LLM call:** Agents can take **multiple steps**, use **tools**, maintain **memory**, and make **decisions** based on intermediate results.

---

## 🏗️ Agent Architecture

```
                    ┌──────────────────────────────┐
                    │           AGENT LOOP          │
                    │                              │
User Input ────────▶│  LLM (Brain / Planner)       │
                    │      ↓                        │
                    │  Should I use a tool?         │
                    │      ↓ Yes                    │
                    │  Tool Call (Action)           │
                    │      ↓                        │
                    │  Tool Result (Observation)    │
                    │      ↓                        │
                    │  Enough info? → No → Loop     │
                    │              → Yes → Respond  │
                    └──────────────────────────────┘
                             ↑           ↑
                        Tools        Memory
```

---

## 🔑 Core Components

### 1. Tools (The Agent's Abilities)

```python
from langchain.tools import tool

@tool
def search_web(query: str) -> str:
    """Search the web for current information."""
    # Implementation using SerpAPI, Tavily, etc.
    return f"Results for: {query}"

@tool
def calculator(expression: str) -> str:
    """Evaluate a mathematical expression."""
    try:
        return str(eval(expression))
    except Exception as e:
        return f"Error: {e}"

@tool
def get_crop_price(crop: str, market: str) -> str:
    """Get current mandi price for a crop at a specific market."""
    # Call real mandi API
    return f"₹2100/quintal for {crop} at {market}"
```

### 2. Basic Agent with Tools

```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain.chat_models import ChatOpenAI
from langchain import hub

llm = ChatOpenAI(model="gpt-4o", temperature=0)
tools = [search_web, calculator, get_crop_price]

# Use ReAct prompt (Reasoning + Acting)
prompt = hub.pull("hwchase17/react")

agent = create_react_agent(llm, tools, prompt)
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,  # Shows reasoning steps
    max_iterations=10,
    handle_parsing_errors=True
)

result = agent_executor.invoke({
    "input": "What is the current price of wheat in Hapur mandi? Also calculate how much 50 quintals would cost."
})
print(result['output'])
```

### 3. Memory Systems

```python
from langchain.memory import ConversationBufferMemory, ConversationSummaryMemory

# Short-term: Store full conversation history
buffer_memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

# For long conversations: Summarize old history
summary_memory = ConversationSummaryMemory(
    llm=llm,
    memory_key="chat_history",
    return_messages=True
)

# Agent with memory
from langchain.agents import create_openai_functions_agent

agent_with_memory = AgentExecutor(
    agent=agent,
    tools=tools,
    memory=buffer_memory,
    verbose=True
)

# First message
agent_with_memory.invoke({"input": "My name is Ramesh and I grow wheat in UP"})

# Agent remembers context
agent_with_memory.invoke({"input": "What crops should I grow alongside my main crop?"})
# Agent knows "main crop" = wheat, location = UP
```

---

## 🔑 Agent Types & Frameworks

### ReAct (Reasoning + Acting)
The most common agent pattern. The LLM alternates between:
- **Thought**: "I need to search for current prices"
- **Action**: Call the search tool
- **Observation**: Result from the tool
- **Thought**: "Now I can calculate..."
- **Final Answer**: Synthesized response

### Function Calling / Tool Use (OpenAI)
```python
from openai import OpenAI

client = OpenAI()

tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get weather for farming decisions",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string"},
                    "date": {"type": "string"}
                },
                "required": ["city"]
            }
        }
    }
]

# Agentic loop
messages = [{"role": "user", "content": "Should I irrigate my wheat field in Meerut tomorrow?"}]

while True:
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=messages,
        tools=tools,
        tool_choice="auto"
    )
    
    message = response.choices[0].message
    
    if message.tool_calls:
        # Execute the tool
        for tool_call in message.tool_calls:
            result = execute_tool(tool_call)
            messages.append({"role": "tool", "content": result, "tool_call_id": tool_call.id})
    else:
        # Agent is done
        print(message.content)
        break
```

### LangGraph (Multi-Agent Orchestration)
```python
from langgraph.graph import StateGraph, END
from typing import TypedDict, Annotated
import operator

class AgentState(TypedDict):
    messages: Annotated[list, operator.add]
    next_agent: str

# Define a multi-agent workflow
workflow = StateGraph(AgentState)

# Add agent nodes
workflow.add_node("researcher", researcher_agent)
workflow.add_node("analyst", analyst_agent)
workflow.add_node("writer", writer_agent)

# Define edges (flow between agents)
workflow.add_edge("researcher", "analyst")
workflow.add_edge("analyst", "writer")
workflow.add_edge("writer", END)

workflow.set_entry_point("researcher")
app = workflow.compile()
```

---

## 🔑 Popular Agent Frameworks

| Framework | Type | Best For |
|-----------|------|---------|
| **LangChain Agents** | Single agent | Simple tool use |
| **LangGraph** | Multi-agent | Complex workflows, state machines |
| **CrewAI** | Multi-agent | Role-based agents (crew/team) |
| **AutoGen** | Multi-agent | Microsoft, conversational agents |
| **OpenAI Assistants API** | Managed | Simple, hosted agent with threads |
| **Phidata** | Single/Multi | Structured data-aware agents |

---

## 🛠️ CrewAI Example (Role-Based Agents)

```python
from crewai import Agent, Task, Crew

# Define agents with roles
researcher = Agent(
    role="Research Analyst",
    goal="Research agricultural trends and government schemes",
    backstory="Expert in Indian agriculture policy and farming",
    tools=[search_web, government_api_tool],
    verbose=True
)

writer = Agent(
    role="Content Writer",
    goal="Create clear, farmer-friendly summaries",
    backstory="Expert at simplifying complex information for rural audiences",
    verbose=True
)

# Define tasks
research_task = Task(
    description="Research PM Kisan scheme eligibility criteria for 2026",
    agent=researcher,
    expected_output="Detailed eligibility criteria with latest updates"
)

writing_task = Task(
    description="Write a simple Hindi summary of the eligibility criteria",
    agent=writer,
    expected_output="Clear, simple Hindi explanation"
)

# Create and run the crew
crew = Crew(agents=[researcher, writer], tasks=[research_task, writing_task])
result = crew.kickoff()
```

---

## 🌍 Real-World Agent Applications

| Application | Agents Involved | Tools Used |
|-------------|----------------|-----------|
| Customer support | Single agent | CRM lookup, order status, refund API |
| Research assistant | Researcher + Writer | Web search, PDF reader, summarizer |
| Code review | Reviewer + Fixer | Code analyzer, test runner |
| Trip planner | Planner + Booker | Flight API, hotel API, calendar |
| Data pipeline | Extractor + Transformer + Loader | SQL, APIs, file system |

---

## ❓ Interview Questions

**Q: What is an AI Agent?**
> An AI Agent is an LLM-powered system that can perceive inputs, reason about what to do, call tools/APIs to take actions, and observe results — repeating this loop until the task is complete. Unlike a simple LLM call, agents can take multi-step actions autonomously.

**Q: What is the ReAct pattern?**
> ReAct (Reasoning + Acting) is the most common agent pattern where the LLM alternates between Thought (reasoning about what to do), Action (calling a tool), and Observation (seeing the tool result) until it has enough information to give a final answer.

**Q: What types of memory do agents use?**
> Short-term (in-context) memory stores the current conversation. Long-term memory can use a vector database to retrieve relevant past interactions. Buffer memory stores full history; summary memory compresses old history to save tokens.

**Q: What is the difference between LangChain and LangGraph?**
> LangChain provides tools for building single-agent pipelines (chains, tools, memory). LangGraph extends this for multi-agent systems with stateful, cyclic workflows — enabling complex patterns like parallel agents, human-in-the-loop, and conditional branching.

---

## ✅ Revision Checklist

- [ ] Can I explain what an AI agent is and how it differs from a simple LLM call?
- [ ] Can I describe the ReAct reasoning pattern?
- [ ] Can I build a simple agent with tools using LangChain?
- [ ] Do I understand the types of memory in AI agents?
- [ ] Do I know the difference between LangChain and LangGraph?
- [ ] Can I name 3 real-world AI agent applications?
