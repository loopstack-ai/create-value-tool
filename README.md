# Loopstack Create Value Tool

A simple Loopstack workflow tool that logs and returns any value. This is useful for debugging workflows or initializing workflow context variables.

## Overview

The Create Value Tool is a custom Loopstack tool that accepts any type of input (string, number, object, array, boolean, or null) and returns it as the tool result. It also logs the value for debugging purposes.

This tool extends `ToolBase` and follows the standard Loopstack custom tool structure with required decorators (`@Injectable`, `@BlockConfig`, `@WithArguments`) and a Zod schema for argument validation.

## Installation

```bash
loopstack add @loopstack/create-value-tool
```

## Usage

### In a Workflow

The Create Value Tool can be used in your Loopstack workflows to create and log values. Here's how to use it:

#### 1. Register the Tool in Your Workflow

```typescript
import { Injectable } from '@nestjs/common';
import { WorkflowBase, Tool } from '@loopstack/core';
import { BlockConfig } from '@loopstack/common';
import { CreateValue } from '@loopstack/create-value-tool';

@Injectable()
@BlockConfig({
  configFile: __dirname + '/my-workflow.yaml',
})
export class MyWorkflow extends WorkflowBase {
  @Tool() private createValue: CreateValue;
}
```

#### 2. Register as Provider

Add the tool to your module's providers:

```typescript
import { Module } from '@nestjs/common';
import { MyWorkflow } from './my-workflow';
import { CreateValue } from '@loopstack/create-value-tool';

@Module({
  providers: [MyWorkflow, CreateValue],
})
export class MyModule {}
```

#### 3. Use in Workflow YAML

Reference the tool in your workflow transitions:

```yaml
title: 'My Workflow'
description: 'Example workflow using Create Value tool'

transitions:
  - id: createInitialValue
    from: start
    to: processValue
    call:
      - id: initValue
        tool: createValue
        args:
          input: ${ args.inputData }
        assign:
          initialValue: ${ result.data }

      - tool: createChatMessage
        args:
          role: 'assistant'
          content: |
            Created value: {{ initialValue }}
```

### Use Cases

- **Debugging**: Log values at different stages of your workflow
- **Variable Initialization**: Create initial context variables for your workflows
- **Testing**: Verify workflow data flow and transformations
- **Passthrough Operations**: Pass data through while logging it for inspection

## Tool Structure

This tool follows the [Loopstack custom tool structure](https://loopstack.ai/docs/building-with-loopstack/creating-custom-tools):

```typescript
@Injectable()
@BlockConfig({
  config: {
    description: 'Create a value from input.',
  },
})
@WithArguments(
  z
    .object({
      input: InputSchema,
    })
    .strict(),
)
export class CreateValue extends ToolBase<{ input: InputType }> {
  async execute(args: { input: InputType }): Promise<ToolResult> {
    return {
      data: args.input,
    };
  }
}
```
## Development

### Prerequisites

- Node.js (v18 or higher recommended)
- npm or yarn

### Setup

```bash
# Install dependencies
npm install

# Run tests
npm run test

# Build the project
npm run build

# Watch mode
npm run watch

# Lint code
npm run lint

# Format code
npm run format
```

### Testing

The tool includes comprehensive tests covering:

- Input validation for all supported types
- Execution with different value types
- Strict mode validation (rejects extra properties)

Run tests with:

```bash
npm run test
```