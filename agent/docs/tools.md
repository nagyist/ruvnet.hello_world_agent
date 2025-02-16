# Tools Guide

## Overview
The Hello World Agent is designed to be extensible with custom tools. This guide will walk you through the process of creating, integrating, and using tools within the agent framework.

## Tool Structure
Tools are located in the `agent/tools/` directory. Each tool is a Python module that can be imported and used by the agent.

### Example Tool Structure
```
agent/
└── tools/
    ├── __init__.py
    ├── custom_tool.py
    └── user_prompt.py
```

## Creating a New Tool

### Step 1: Create a New Python File
Create a new Python file in the `agent/tools/` directory. For example, `my_tool.py`.

### Step 2: Define the Tool's Functionality
Implement the tool's functionality in the new file. Here's a basic template:

```python
# my_tool.py

def my_tool_function(input_data):
    """
    Process input_data and return the result.
    """
    # Implement your tool's logic here
    result = f"Processed: {input_data}"
    return result
```

### Step 3: Integrate the Tool with the Agent
To use the tool within the agent, import it in `agent/main.py` or any other relevant module:

```python
from .tools.my_tool import my_tool_function

def run():
    # Example usage of the tool
    input_data = "Sample data"
    result = my_tool_function(input_data)
    print(result)
```

## Tool Configuration
Tools can be configured using the agent's configuration files. Add any necessary configuration options to `agent/config/` files.

### Example Configuration
```yaml
# config/tools.yaml

my_tool:
  option1: "value1"
  option2: "value2"
```

## Advanced Tool Features

### Tool Dependencies
If your tool requires additional Python packages, add them to the `install_requires` section of `setup.py`:

```python
install_requires=[
    "crewai[local]>=0.1.0",
    "langchain>=0.1.0",
    "python-dotenv>=1.0.0",
    "pyyaml>=6.0.0",
    "httpx>=0.24.0",
    "new_dependency>=1.0.0"
]
```

### Tool Testing
Test your tool independently before integrating it with the agent. Create a separate test script or use a testing framework like `unittest` or `pytest`.

### Tool Documentation
Document your tool's functionality and usage within the tool's Python file. Use docstrings to describe the tool's purpose, parameters, and return values.

## Example: Custom Tool Implementation

### Step 1: Create the Tool
```python
# custom_tool.py

def analyze_data(data):
    """
    Analyze the provided data and return insights.
    """
    insights = f"Insights for {data}"
    return insights
```

### Step 2: Integrate the Tool
```python
# main.py

from .tools.custom_tool import analyze_data

def run():
    data = "Market trends"
    insights = analyze_data(data)
    print(insights)
```

### Step 3: Configure the Tool
```yaml
# config/tools.yaml

analyze_data:
  analysis_depth: "deep"
  report_format: "detailed"
```

## Best Practices

1. **Modularity**: Keep tools focused on a single responsibility.
2. **Reusability**: Design tools to be reusable across different parts of the agent.
3. **Documentation**: Clearly document tool functionality and usage.
4. **Testing**: Thoroughly test tools before integration.
5. **Version Control**: Track tool changes in version control.

## Conclusion
Tools are a powerful way to extend the Hello World Agent's capabilities. By following this guide, you can create custom tools that enhance the agent's functionality and provide additional value to users.