# Go Coding Agent

A lightweight, interactive AI coding agent implemented in Go, inspired by Thorsten Ball's post [How to Build an Agent](https://ampcode.com/notes/how-to-build-an-agent).

This repository demonstrates how to build an autonomous agent using the Anthropic Go SDK. At its core, the agent consists of an execution loop, a language model, and tools that allow it to interact with local files.

## Features

* **Interactive CLI Loop**: Continuous chat session with Claude directly from your terminal.

* **Tool Use & Autonomous Loop**: When Claude requests tool execution (e.g., reading or editing a file), the agent runs the tool, feeds the output back into the conversation, and continues inference until the task is complete.

* **Extensible Architecture**: Cleanly separated package structure to prevent circular dependencies, making it simple to create and register custom tools.

## Repository Structure

```
.
├── main.go               # Agent loop, execution logic, and CLI interactions
└── tools/
    ├── read_files.go     # ToolDefinition struct, JSON schema generator, and `read_file` logic
    ├── edit_files.go     # `edit_file` implementation for modifying existing files
    └── list_files.go     # Additional tool definitions
```

## Getting Started

### Prerequisites

* [Go](https://golang.org/doc/install?utm_source=gemini) 1.21 or higher

* An Anthropic API Key (`ANTHROPIC_API_KEY`)

### Setup & Running

1. **Clone the repository:**

   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   cd your-repo-name
   ```

2. **Set your API Key:**

   ```bash
   export ANTHROPIC_API_KEY="your-anthropic-api-key"
   ```

3. **Run the agent:**

   ```bash
   go run main.go
   ```

## How It Works

As outlined in *How to Build an Agent*, an agent is essentially:

$$
\text{LLM} + \text{Tools} + \text{Execution Loop}
$$

1. **Definition**: Tools (like `read_file` and `edit_file`) are defined in the `tools` package alongside JSON schemas generated from Go struct types using `jsonschema`.

2. **Inference**: User input and tool schemas are sent to the Anthropic API.

3. **Tool Execution**: When the model response contains a `tool_use` block, the agent executes the corresponding local function and appends the result to the conversation history.

4. **Looping**: The agent recursively feeds tool outputs back to the model until a final text response is produced.

## Adding Custom Tools

To add a new tool:

1. Define the input struct and tool function in `tools/`:

   ```go
   type MyToolInput struct {
       Param string `json:"param" jsonschema_description:"Parameter description"`
   }
   
   func MyTool(input json.RawMessage) (string, error) {
       // Perform action here...
       return "result", nil
   }
   
   var MyToolDefinition = ToolDefinition{
       Name:        "my_tool",
       Description: "Description of what this tool does",
       InputSchema: GenerateSchema[MyToolInput](),
       Function:    MyTool,
   }
   ```

2. Register the tool in `main.go`:

   ```go
   alltools := []tools.ToolDefinition{
       tools.ReadFileDefinition,
       tools.EditFileDefinition,
       tools.MyToolDefinition,
   }
   ```

## References

* [How to Build an Agent](https://ampcode.com/notes/how-to-build-an-agent) by Thorsten Ball

* [Anthropic Go SDK](https://github.com/anthropics/anthropic-sdk-go)
