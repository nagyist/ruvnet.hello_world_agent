# Meta Agent Generator for Deno
**Created by rUv**

## Overview

The Meta Agent Generator is an advanced TypeScript script designed for Deno that dynamically creates a complete, single-file ReACT agent based on user-defined configuration. The generated agent interleaves chain-of-thought reasoning with actionable steps to solve tasks step by step. It leverages the OpenRouter API (default model: `openai/o3-mini-high`) and utilizes Deno's secure sandboxing, fast cold start times, and built-in support for both CLI and HTTP deployments.

## Concept

A **Meta Agent** is an agent that creates agents. This generator automates the creation of fully self-contained AI agents which can:
- Interpret and execute complex user queries.
- Use a variety of custom tools (e.g., arithmetic calculator, date/time, algebra solver, code executor).
- Reflect on its chain-of-thought to self–optimize its final answer.
- Communicate with other agents using multi–agent communication controls based on robots.txt (optional).

## Features

- **Dynamic Agent Creation:**  
  Automatically assembles a complete agent from your configuration, including tool implementations, a robust ReACT loop, and self–reflection.

- **Built-in Tools:**
  - **Calculator:** Performs arithmetic calculations (e.g., "2 + 2" → "4")
  - **DateTime:** Returns current time in ISO format
  - **AlgebraSolver:** Solves linear equations (e.g., "3*x = 15" → "x = 5")
  - **CodeExecutor:** Runs JavaScript/TypeScript code securely (e.g., factorial calculations)

- **User-Defined Customization:**  
  Configure:
  - **Tools:** Add or modify tool implementations with custom functions
  - **System Prompts:** Customize role instructions and formatting guidelines
  - **Model Selection:** Choose any OpenRouter-compatible model (default: `openai/o3-mini-high`)
  - **Deployment Settings:** Generate agents for local CLI use or as an HTTP server

- **Optional NPM Package Integration:**  
  Import and integrate npm packages via Deno's npm support by providing a comma-separated list of packages.

- **Advanced Command-Line Arguments:**  
  Override defaults using Deno CLI arguments:
  ```sh
  # Example with all options
  deno run --allow-net --allow-env --allow-run --allow-write agent.ts \
    --agentName="CalculatorAgent" \
    --model="openai/gpt-4" \
    --deployment=local \
    --outputFile="./calculator_agent.ts" \
    --enableReflection=true \
    --enableMultiAgentComm=false \
    --npmPackages="mathjs,moment" \
    --advancedArgs='{"logLevel":"debug","memoryLimit":256}'
  ```

- **Multi-Agent Communication Controls:**  
  Optionally check a target agent's robots.txt to control inter-agent communication.

- **Neuro–Symbolic Reasoning Enhancements:**  
  The system prompt instructs the agent to use specialized tools for arithmetic, time, and symbolic tasks, enabling exact computations and improved reasoning.

- **Self–Reflection:**  
  An optional reflection step reviews the chain-of-thought and final answer, allowing the agent to catch and correct errors.

## Example Usage

### 1. Basic Arithmetic
```sh
# Generate an agent
deno run --allow-net --allow-env --allow-run --allow-write agent.ts --agentName="MathAgent"

# Use the generated agent
deno run --allow-net --allow-env --allow-run math_agent.ts "What is 2 + 2?"
# Output:
# Thought: I will use the Calculator tool to calculate 2 + 2.
# Answer: The answer is 4.
```

### 2. Multi-Tool Tasks
```sh
# Complex query using multiple tools
deno run --allow-net --allow-env --allow-run math_agent.ts "What is the current time, and what is x in the equation: 3*x = 15?"
# Output:
# Thought: I need to obtain the current time using the DateTime tool and then solve the equation 3*x = 15 using the AlgebraSolver tool.
# Answer: The current time is 2025-02-21T04:23:27.000Z and the value of x in the equation 3*x = 15 is 5.
```

### 3. Code Execution
```sh
# Execute JavaScript code
deno run --allow-net --allow-env --allow-run math_agent.ts "Execute this code: function factorial(n) { return n <= 1 ? 1 : n * factorial(n-1); }; console.log(factorial(5));"
# Output:
# Thought: We need to execute the given JavaScript factorial function code and check its output.
# Answer: The code outputs 120.
```

### 4. HTTP Server Mode
```sh
# Generate HTTP server agent
deno run --allow-net --allow-env --allow-run --allow-write agent.ts \
  --agentName="APIAgent" \
  --deployment=http \
  --outputFile="./api_agent.ts"

# Run the server
deno run --allow-net --allow-env --allow-run api_agent.ts

# Test with curl
curl "http://localhost:8000?input=What+is+2+%2B+2%3F"
# Output: {"answer": "The answer is 4."}
```

## Tool Specifications

### Calculator Tool
- Supports basic arithmetic operations: +, -, *, /
- Handles parentheses and floating-point numbers
- Input validation prevents code injection
- Example: `Calculator|2 + 2 * (3 - 1)`

### DateTime Tool
- Returns current system time in ISO format
- No input required
- Example: `DateTime|`

### AlgebraSolver Tool
- Solves linear equations in the form:
  - x + a = b
  - x - a = b
  - a * x = b
  - x / a = b
- Returns solution in the form "x = value"
- Example: `AlgebraSolver|3*x = 15`

### CodeExecutor Tool
- Executes JavaScript/TypeScript code in a sandboxed environment
- Supports async/await
- Captures stdout and stderr
- Example: `CodeExecutor|console.log("Hello, World!")`

## Security Considerations

1. **Sandboxed Execution:**
   - CodeExecutor tool runs in a restricted Deno environment
   - No file system access by default
   - Network access can be controlled via permissions

2. **API Key Management:**
   - OpenRouter API key should be set via environment variable
   - Never hardcode API keys in the agent code

3. **Input Validation:**
   - All tool inputs are validated before execution
   - Calculator prevents arbitrary code execution
   - AlgebraSolver enforces strict equation format

## Deployment

The meta-agent and generated agents support multiple deployment scenarios:

### Meta-Agent Server Mode

Run the meta-agent as an HTTP server to create agents via REST API:

```sh
# Start meta-agent server with custom port (default: 8000)
deno run --allow-net --allow-env --allow-run --allow-write agent.ts \
  --server=true \
  --port=3000

# Create agent via POST request
curl -X POST http://localhost:3000 \
  -H "Content-Type: application/json" \
  -d '{
    "agentName": "TestAgent",
    "model": "openai/gpt-4",
    "deployment": "http",
    "enableReflection": true,
    "tools": [...],
    "systemPrompt": "...",
    "npmPackages": ["mathjs"],
    "advancedArgs": {
      "logLevel": "debug",
      "memoryLimit": 256
    }
  }'

# Response includes:
{
  "message": "Agent generated successfully",
  "outputPath": "./generated_agent.ts",
  "code": "... generated TypeScript code ..."
}
```

### Generated Agent Deployment

Generated agents can be deployed in various scenarios:

- **Local CLI Execution:**  
  Quickly test and iterate on your agent using Deno's CLI.
  ```sh
  deno run --allow-net --allow-env --allow-run generated_agent.ts "What is 2+2?"
  ```

- **HTTP Server Deployment:**  
  Deploy as an HTTP server with customizable options:
  ```sh
  # Start with custom host/port
  deno run --allow-net --allow-env --allow-run generated_agent.ts \
    --deployment=http \
    --hostname=0.0.0.0 \
    --port=3000 \
    --cert=./cert.pem \
    --key=./key.pem

  # Available Deno server arguments:
  # --port=<number>       Port to listen on (default: 8000)
  # --hostname=<string>   Hostname to listen on (default: 0.0.0.0)
  # --cert=<path>        Path to TLS certificate file
  # --key=<path>         Path to TLS private key file
  # --cors               Enable CORS headers
  # --rateLimit=<number> Max requests per IP per minute
  # --timeout=<number>   Request timeout in milliseconds
  ```

- **Edge & Serverless:**  
  Leverage Deno's low cold start times and secure sandboxing to run the agent in global serverless environments (compatible with Deno Deploy, Supabase Edge, Fly.io, etc.).

- **Optional Multi-Agent Communication:**  
  Enable communication between agents by checking robots.txt of target agents.

## Error Handling

The agent includes comprehensive error handling:
- Tool execution errors are caught and returned as readable messages
- API communication errors include detailed status codes
- Invalid input formats trigger helpful validation messages
- Network timeouts and retries are handled gracefully

## Performance Optimization

1. **Cold Start Time:**
   - Single file design minimizes initialization
   - No external dependencies required
   - Efficient tool implementation

2. **Memory Usage:**
   - Configurable memory limits via advancedArgs
   - Automatic cleanup of completed operations
   - Efficient string handling for large inputs

3. **Response Time:**
   - Streaming responses for real-time feedback
   - Parallel tool execution where possible
   - Optimized regex patterns for parsing

## Conclusion

The Meta Agent Generator empowers developers to create custom, flexible, and secure AI agents without extensive manual coding. By simply adjusting configuration options and using command-line arguments, you can generate an agent that meets your domain-specific requirements and deployment environment. Enjoy building and deploying your own autonomous agents with this next-generation tool – created by rUv.

---

*For further questions or contributions, please contact rUv or visit the project repository.*
