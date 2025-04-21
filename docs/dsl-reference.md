## Abstract

This document provides formal definitions and detailed property references for all components described in the DClare Domain-Specific Language (DSL). It serves as a comprehensive technical guide for authors, developers, and system integrators working with DClare-based agentic architectures.

By exploring this reference, users will gain a clear and structured understanding of how to define, configure, and interconnect the various declarative components of DClare — including agents, kernels, kernel functions, processes, communication channels, memory providers, toolsets, and authentication blocks.

This document complements the DSL overview and is intended as a precise specification for implementation, validation, tooling support, and schema generation. It enables accurate modeling of agentic systems and fosters interoperability across runtimes and platforms that support the DClare ecosystem.

## Definitions

The following sections define the core elements of the DClare DSL and their associated properties. Each definition represents a building block of an agentic system, designed to be modular, declarative, and extensible.

Each component is presented with a summary of its purpose, a table of available properties, their expected types, descriptions, and whether they are required or optional. These definitions form the foundation of any valid DClare manifest and should be used as the authoritative source when authoring or validating component configurations.

Unless otherwise noted, all definitions are designed to be serialized in YAML or JSON and follow consistent naming and structural conventions throughout the DClare ecosystem.

### Manifest

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| metadata | [`manifestMetadata`](#manifest-metadata) | `no` | Provides descriptive information about a manifest. |
| components | [`componentCollection`](#component-collection) | `no` | Provides a collection of reusable components, if any, that can be referenced throughout the manifest. |
| interfaces | [`interfaceCollection`](#interface-collection) | `yes` | Provides a collection of interfaces through which the application's components are made accessible. |

#### Examples

```yaml
metadata:
  name: book-critic-app
  description: An AI-powered application that exposes a book review agent capable of analyzing literary works across genres and eras.
  version: '1.0.0'
  tags: [ book, critic, agent, openai, gpt4o ]
components:
  secrets:
    - openai-apikey
  kernels:
    openai-gpt4o:
      reasoning:
        provider: openai
        model: gpt-4o
        api:
          endpoint:
            authentication:
              apiKey:
                use: openai-apikey
          properties:
            organization: d-clare
  agents:
    book-critic:
      hosted:
        description: Offers insightful, articulate reviews and analyses of books across genres and literary traditions.
        instructions: >
          You are a literary critic specializing in narrative structure, thematic depth, character development, and writing style.
          Provide detailed yet accessible reviews that balance critical analysis with reader experience. Always justify your
          assessments with textual evidence or references, and avoid spoilers unless explicitly requested.
        skills:
          - name: Literary Analysis
            description: Analyzes narrative techniques, symbolism, tone, and stylistic elements within a text.
          - name: Thematic Evaluation
            description: Evaluates the depth and clarity of central themes, motifs, and philosophical undertones in a book.
        kernel:
          use: openai-gpt4o
interfaces:
  agents:
    book-critic:
      use: book-critic
      endpoints:
        a2a: true
        http:
          path: /api/agents/book-critic
```

### Manifest Metadata

Defines descriptive information about a manifest.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| name | `string` | `yes` | A unique, human-readable name used to identify the manifest in logs, dashboards, or registries. |
| description | `string` | `no` | A brief summary explaining the purpose or scope of the manifest. Useful for documentation or discovery. |
| version | `string` | `yes` |  The version of the manifest, following [semantic versioning 2.0.0](https://semver.org/#semantic-versioning-200https://semver.org/#semantic-versioning-200). Helps manage updates and compatibility. |
| tags | `string[]` | `no` | An optional list of keywords or labels used to categorize or filter manifests by theme, domain, or capability. |

### Component Collection

Defines a collection of reusable components.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| secrets | `string[]` | `no` | Defines the list of secrets, if any, used to securely configure components. |
| authentications | [`map{authenticationPolicy}`](#authentication-policy) | `no` | A map of identifiers to authentication policy definitions. Each key represents a unique authentication policy name, and each value specifies the configuration of that authentication policy. |
| memories | [`map{memory}`](#memory) | `no` | A map of identifiers to memory definitions. Each key represents a unique memory name, and each value specifies the configuration of that memory. |
| toolsets | [`map{toolset}`](#toolset) | `no` | A map of identifiers to toolset definitions. Each key represents a unique toolset name, and each value specifies the configuration of that toolset. |
| kernelFunctions | [`map{kernelFunction}`](#kernel-function) | `no` | A map of identifiers to kernel function definitions. Each key represents a unique kernel function name, and each value specifies the configuration of that kernel function. |
| kernels | [`map{kernel}`](#kernel) | `no` | A map of identifiers to kernel definitions. Each key represents a unique kernel name, and each value specifies the configuration of that kernel. |
| agents | [`map{agent}`](#agent) | `no` | A map of identifiers to agent definitions. Each key represents a unique agent name, and each value specifies the configuration of that agent. |
| processes | [`map{agenticProcess}`](#agentic-process) | `no` | A map of identifiers to agentic process definitions. Each key represents a unique agentic process name, and each value specifies the configuration of that agentic process. |

### Interface Collection

Defines the set of named interfaces that define how agents, processes, and other components are exposed by the application.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| agents | [map{agentInterface}](#agent-interface) | `no` | A map of identifiers to agent interfaces that are exposed by the application. Each key represents a unique agent interface name, and each value specifies the configuration of that agent interface. |
| processes | [map{agenticProcessInterface}](#agentic-process-interface) | `no` | A map of identifiers to agentic process interfaces that are exposed by the application. Each key represents a unique agentic process interface name, and each value specifies the configuration of that agentic process interface. |

### Kernel

Defines an abstract interface to AI capabilities such as reasoning and embedding.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| use | `string` | `no` | A reference to a predefined kernel by name. This allows reusing an existing kernel defined at top level instead of redefining it inline.<br>*If defined, no other property can be set.* |
| extends | `string` | `no` | A reference to the kernel definition to extend, if any. |
| reasoning | [`kernelCapability`](#kernel-capability) | `no` | Specifies the configuration of the underlying reasoning capability, if any, used by the kernel. |
| embedding | [`kernelCapability`](#kernel-capability) | `no` | Specifies the configuration of the underlying embedding capability, if any, used by the kernel. |
| toolsets | [`toolset`](#toolset) | `no` | A collection of toolsets available to the kernel. Each toolset provides a group of external or internal tools (functions, APIs, utilities) that the kernel can invoke to extend its capabilities. |

#### Examples

```yaml
openai-gpt4o:
  reasoning:
    provider: openai
    model: gpt-4o
    api:
      endpoint:
        authentication:
          apiKey:
            key: a87dd897cb2b4cb39c6840fcffccd9ae
      properties:
        organization: d-clare
```

### Kernel Capability

Defines and configures a kernel's capability such as reasoning or embedding.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| provider | `string` | `yes` | The name of the provider or platform offering the capability.<br>*Supported values are: `azure-openai`, `gemini`, `hugging-face`, `mistral`, `ollama`, `onnx` and `openai`.* |
| model | `string` | `yes` | The identifier or name of the model to use, such as 'gpt-4', 'claude-2', or a custom deployment name. |
| api | [`kernelCapabilityApi`](#kernel-capability-api) | `yes` | Specifies the configuration details for interacting with the provider's API. |
| settings | `map` | `no` | Optional provider- or model-specific settings that influence the behavior of the capability. These may include parameters such as temperature, top-p sampling, maximum tokens, stop sequences, or other advanced tuning options supported by the provider. |

### Kernel Capability API

Specifies the configuration details for interacting with the provider's API.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| endpoint | [`kernelCapabilityApiEndpoint`](#kernel-capability-api-endpoint) | `no` | The API endpoint used to communicate with the capability provider. |
| properties | `map` | `no` | A map of additional properties to include in API requests, such as headers, authentication tokens, query parameters, or other provider-specific options. |

### Kernel Capability API Endpoint

Defines the API endpoint of a Kernel Capability.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| uri | `uri` | `no` | The full URI used to access the target resource. |
| authentication | [`authenticationPolicy`](#authentication-policy) | `no` | Specifies the authentication policy, if any, used to access the endpoint. |

### Kernel Function Strategy

Defines a kernel function–based strategy

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| function | [`kernelFunction`](#kernel-function) | `yes` | The definition of the kernel function to use. |
| kernel | [`kernel`](#kernel) | `yes` | The kernel used to invoke the function. |

#### Examples

```yaml
metaphorizer:
  function:
    template:
      content: >
        Read the following input and generate a metaphor or analogy that captures its core message in a vivid, poetic way.
        
        Input:
        {{ input }}

        Output:
        Respond with a single, original metaphor that reflects the essence of the input.
      inputVariables:
        - name: input
          description: The raw input to transform into a metaphor
          required: true
          sample: "The internet is filled with conflicting information and distractions."
      outputVariable:
        description: A single sentence metaphor expressing the concept in a novel way
        schema:
          type: string
  kernel:
    use: hugging-face
```

### Kernel Function

Defines kernel-backed function that performs reasoning, transformation, or decision-making. Kernel functions encapsulate prompt templates and model-specific configuration, and are invoked by agents or processes to execute structured tasks

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| use | `string` | `no` | A reference to a predefined function by name. This allows reusing an existing function defined at top level instead of redefining it inline.<br>*If defined, no other property can be set.* |
| template | [`kernelFunctionTemplate`](#kernl-function-template) | `yes` | The definition of the prompt template used by the kernel function to perform reasoning or decision-making. |
| parameters | `string[]` | `no` | A list containing the names of the parameters, if any, to exclude from being encoded. |

### Kernel Function Template

Defines the prompt template used by a kernel function to perform reasoning or decision-making

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| content | `string` | `yes` | The template content to use for prompt generation, including placeholders for input variables. |
| format | `string` | `no` | The optional format of the prompt template. |
| inputVariables | [`kernelFunctionInputVariable[]`](#kernel-function-input-variable) | `no` | A list containing the input variables, if any, used within the template. |
| outputVariable | [`kernelFunctionOutputVariable`](#kernel-function-output-variable) | `no` | The definition of the expected output variable, if any, for the result produced by the prompt. |

### Kernel Function Input Variable

Defines a kernel function input variable.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| name | `string` | `yes` | The variable's name. |
| description | `string` | `no` | The variable's description, if any. |
| default | `any` | `no` | The variable's default value, if any. |
| sample | `any` | `no` | A sample value for the variable, if any. |
| required | `boolean` | `no` | A boolean indicating whether or not the variable is required. |
| allowDangerousContent | `boolean` | `no` | A boolean indicating whether or not to handle the variable value as potential dangerous content. |
| schema | `jsonSchema` | `no` | The JSON schema, if any, used to describe the variable. |

### Kernel Function Output Variable

Defines a kernel function output variable.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| description | `string` | `no` | The variable's description, if any. |
| schema | `jsonSchema` | `no` | The JSON schema, if any, used to describe the variable. |

### Agent

Defines an AI agent.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| use | `string` | `no` | A reference to a predefined agent by name. This allows reusing an existing agent defined at top level instead of redefining it inline.<br>*If defined, no other property can be set.* |
| hosted | [`hostedAgentConfiguration`](#hosted-agent-configuration) | `no` | An object used to configure an hosted agent.<br>*Required if `use` and `remote` have not been set.* |
| remote | [`remoteAgentConfiguration`](#remote-agent-configuration) | `no` | An object used to configure a remote agent.<br>*Required if `use` and `hosted` have not been set.* |

### Hosted Agent Configuration

Defines an hosted AI agent, which runs within the local environment or platform.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| extends | `string` | `no` | A reference to the agent definition to extend, if any. |
| description | `string` | `no` | A short human-readable description of the agent's role or purpose, which is used for documentation, UI display, and prompt composition. |
| instructions | `string` | `no` | The specific instructions that defines the agent's behavior. |
| skills | [`agentSkill[]`](#agent-skill) | `no` | A list containing the agent's skills, if any. |
| kernel | [`kernel`](#kernel) | `yes` | The definition of the kernel that powers the agent's capabilities. |
| memory | [`memory`](#memory) | `no` | The definition of the agent's memory, if any. |

#### Examples

```yaml
book-critic:
  hosted:
    description: Offers insightful, articulate reviews and analyses of books across genres and literary traditions.
    instructions: >
      You are a literary critic specializing in narrative structure, thematic depth, character development, and writing style.
      Provide detailed yet accessible reviews that balance critical analysis with reader experience. Always justify your
      assessments with textual evidence or references, and avoid spoilers unless explicitly requested.
    skills:
      - name: Literary Analysis
        description: Analyzes narrative techniques, symbolism, tone, and stylistic elements within a text.
      - name: Thematic Evaluation
        description: Evaluates the depth and clarity of central themes, motifs, and philosophical undertones in a book.
    kernel:
      use: gemini-2.0-flash-lite
    memory:
      use: qdrant
```

### Agent Skill

Defines an agent's skill.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| name | `string` | `yes` | The skill's name. |
| description | `string` | `no` | A short human-readable description of the agent's skill or capability. This information may be used to evaluate and select the most appropriate agent for a given task. |

### Remote Agent Configuration

Defines an hosted AI agent, which runs on an external or separate infrastructure.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| channel | [`communicationChannel`](#communication-channel) | `yes` | Defines the communication channel used to interact with the remote agent. |

#### Examples

```yaml
fact-checker:
  remote:
    channel:
      a2a:
        endpoint:
          uri: https://agents.host.ai
        name: fact-checker
```

### Communication Channel

Defines the communication channel used to interact with the remote agent

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| a2a | [`a2aCommunicationChannel`](#a2a-communication-channel) | `no` | The definition of a channel based on the A2A (Agent-to-Agent) communication protocol.<br>*Required if no other property has been set, otherwise ignored.* |

### A2A Communication Channel

Defines a channel based on the A2A (Agent-to-Agent) communication protocol.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| endpoint | [`endpoint`](#endpoint) | `yes` | The endpoint of the remote host used to perform A2A agent discovery. This is not the agent’s direct message endpoint, but rather the host through which the agent is resolved. |
| name | `string` | `no` | The name of the remote agent to select from the A2A discovery endpoint, in case multiple agents are available at the same host. |

### Memory

Represents the definition of an agent's memory capability.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| file | [`fileMemory`](#file-memory) | `no` | The definition of a file-backed memory that loads entries from structured files in the local or remote file system.<br>*Required if not other property has been set, otherwise ignored.* |
| keyvalue | [`keyValueMemory`](#key-value-memory) | `no` | The definition of a key-value store memory that retrieves entries based on keys or tags.<br>*Required if not other property has been set, otherwise ignored.* |
| static | [`staticMemory`](#static-memory) | `no` | The definition of a static memory that returns predefined values without kernel lookup.<br>*Required if not other property has been set, otherwise ignored.* |
| vector | [`vectorMemory`](#vector-memory) | `no` | The definition of a vector memory that retrieves entries using semantic similarity and vector search.<br>*Required if not other property has been set, otherwise ignored.* |

#### Examples

```yaml
static-memory:
  type: static
  static:
    entries:
      - content: >
          The Oxford comma is a stylistic choice in English punctuation where a comma is placed before the final item in a list.
        metadata:
          topic: Grammar
          category: Fact
          tags: [punctuation, style, oxford-comma]
          locale: en-US
      - content: >
          Science fiction often explores speculative futures, advanced technology, and philosophical questions about humanity's role in the universe.
        metadata:
          topic: Literary Genres
          category: Background
          tags: [sci-fi, fiction, narrative]
          locale: en-US
```

### File memory

Defines a a file-backed memory that loads entries from structured files in the local or remote file system.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| path | `string` | `yes` | The path to the directory used as the memory source. |
| pattern | `string` | `no` | The optional file glob pattern to match files in the directory. |

### Key-Value Memory

Defines a key-value store memory that retrieves entries based on keys or tags.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| provider | `string` | `yes` | The name of the key-value memory provider.<br>Supported values are: `badger-db`, `appcache`, `etcd`,  `redis` and `rocks-db`. | 
| configuration | `map` | `no` | A key/value mapping, if any, of the provider-specific properties used to configure the key-value memory. |

### Static Memory

Defines a static memory that returns predefined values without kernel lookup.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| entries | [`memoryEntry`](#memory-entry) | `yes` | <br>*Must contain at least one entry.* |

### Memory Entry

Defines a memory entry.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| content | `string` | `yes` | The actual content or message of the memory entry. |
| metadata | [`memoryEntryMetadata`](#memory-entry-metadata) | `no` | The metadata, if any, used to describe the memory entry. |

### Memory Entry Metadata

Defines the metadata used to describe a memory entry.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| topic | `string` | `no` | The primary topic or subject of the memory entry. |
| category | `string` | `no` | The category or type of content represented by this entry. |
| tags | `string[]` | `no` | A list of tags, if any, used to annotate or describe the memory entry. |
| locale | `string` | `no` | The locale or language tag associated with the entry, if applicable. |
| properties | `map` | `no` | Additional arbitrary key-value data, if any, associated to the memory entry. |

### Vector Memory

Defines a vector memory that retrieves entries using semantic similarity and vector search.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| provider | `string` | `yes` | The name of the vector memory provider.<br>Supported values are: `chroma`, `milvus`, `pinecone`, `qdrant`, `redis`, `weaviate` and `zep`. | 
| configuration | `map` | `no` | A key/value mapping, if any, of the provider-specific properties used to configure the vector memory. |

### Agentic Process

Defines a high-level orchestration process involving one or more agents.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| collaboration | [`collaborationProcess`](#collaboration-process) | `no` | The configuration of an agentic process in which multiple agents perform their tasks sequentially with configurable selection and termination strategies.<br>*Required if no other property has been set, otherwise ignored*. |
| convergence | [`convergenceProcess`](#convergence-process) | `no` | The definition of an agentic process in which multiple specialized agents are invoked using tailored sub-prompts, and a designated function synthesizes their responses into a single cohesive result.<br>*Required if no other property has been set, otherwise ignored*. |

### Collaboration Process

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| agents | [`map{agent}`](#agent) | `yes` | A collection of named agents that participate in this process. |
| strategy | [`collaborationProcessStrategy`](#collaboration-process-strategy) | `yes` | The definition of the collaboration process's strategy. |

#### Examples

```yaml
refine-text:
  type: collaboration
  collaboration:
    agents:
      GrammarBot:
        extends: openai-gpt4o
        description: Ensures proper grammar, punctuation, and sentence structure.
        instructions: >
          Carefully review the input for grammatical issues and suggest precise corrections. Maintain the original meaning.
      ToneTuner:
        extends: openai-gpt4o
        description: Adjusts tone for friendliness, professionalism, or enthusiasm as needed.
        instructions: >
          Analyze the emotional tone of the text. Make suggestions to align it with a friendly and engaging voice.
      ClarityCoach:
        extends: openai-gpt4o
        description: Improves clarity and removes ambiguity or fluff.
        instructions: >
          Rewrite unclear sentences to improve flow, clarity, and precision. Avoid jargon or overly complex wording.
    strategy:
      selection:
        kernel:
          use: ollama-llama3_2
        function:
          template:
            content: >
              Based on the chat history, decide which single agent should be invoked next
              to improve the text. Consider agent specialties and whether they have already contributed.

              Agents:
              {{ agents }}

              History:
              {{ history }}

              Output:
              Return only the name of the next agent to invoke (as a string).
            inputVariables:
              - name: agents
                description: A list of available agents with descriptions
                required: true
              - name: history
                description: The chat history so far
                required: true
          outputVariable:
            description: The name of the agent to invoke next
            schema:
              type: string
      termination:
        kernel:
          use: huggingface-ms-phi-4
        function:
          template: >
            Determine whether this collaboration should stop based on the agent conversation.

            Agent:
            {{ agent }}

            History:
            {{ history }}

            Output:
            Return "true" if the process should terminate, or "false" to continue.
          inputVariables:
            - name: agent
              description: The name of the agent being evaluated
              required: true
            - name: history
              description: The chat history so far
              required: true
          outputVariable:
            description: Whether to stop the collaboration
            schema:
              type: boolean
        maximumIterations: 5
```

### Collaboration Process Strategy

Defines the strategy of a collaboration process.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| selection | [`agentSelectionStrategy`](#agent-selection-strategy) | `yes` | The kernel function strategy used to select which agents participate and in what order or combination. |
| termination | [`chatTerminationStrategy`](#chat-termination-strategy) | `yes` | The kernel function strategy used to determine when the collaborative process should conclude. |

### Agent Selection Strategy

Defines a kernel function strategy used to select which agents participate and in what order or combination.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| agentsVariableName | `string` | `no` | The name of the variable used to store the list of agents to select.<br>*Defaults to `agents`. |
| historyVariableName | `string` | `no` | The name of the variable used to store the chat history.<br>*Defaults to `history`. |
| initialAgent | `string` | `no` | The name of the agent to invoke first, if any. If not set, the initial agent will be resolved using the selection strategy. |

> [!NOTE]  
> This definition **extends** the [`Kernel Function Strategy`](#kernel-function-strategy), and inherits all its properties.

### Chat Termination Strategy

Defines a kernel function strategy used to determine when the collaborative process should conclude.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| agentVariableName | `string` | `no` | The name of the variable used to store the name of the agent being evaluated.<br>*Defaults to `agent`. |
| historyVariableName | `string` | `no` | The name of the variable used to store the chat history.<br>*Defaults to `history`. |
| agents | `string[]` | `no` | A list containing the names of the agents for which this strategy is applicable.<br>*By default value, any agent is evaluated.* |
| maximumIterations | `integer` | `no` | The maximum number of agent interactions for a given chat invocation.<br>*Defaults to `99`.* |

> [!NOTE]  
> This definition **extends** the [`Kernel Function Strategy`](#kernel-function-strategy), and inherits all its properties.

### Convergence Process

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| agents | [`map{agent}`](#agent) | `yes` | A collection of named agents that participate in this process. |
| strategy | [`convergenceProcessStrategy`](#convergence-process-strategy) | `yes` | The definition of the convergence process's strategy. |

#### Examples

```yaml
review-question-quality:
  type: convergence
  convergence:
    agents:
      GrammarCritic:
        extends: openai-gpt4o
        description: Ensures clarity, correctness, and readability through proper grammar, spelling, and concise phrasing.
        instructions: >
          Your task is to refine the question for clarity and correctness. Focus on grammar, spelling, punctuation,
          and sentence structure. Do not alter the meaning unless it improves clarity.
      DidacticsExpert:
        extends: openai-gpt4o
        description: Evaluates if the question aligns with pedagogical goals and fair assessment practices.
        instructions: >
          Review the question to ensure it aligns with learning objectives, assesses the intended cognitive level,
          and avoids bias or ambiguity. Suggest improvements if needed.
      SubjectMatterSpecialist:
        extends: openai-gpt4o
        description: Validates the technical or factual accuracy of the question.
        instructions: >
          Examine the question for factual accuracy and technical clarity. Point out any misleading or incorrect information,
          and suggest revisions to ensure precision and correctness.
    strategy:
      decomposition:
        kernel:
          use: ollama-mistral
        function:
          template:
            content: >
              You are a task planner responsible for distributing a complex instruction to multiple specialized agents.

              Your task:
              1. Analyze the exam question provided in the prompt.
              2. For each of the following agents, generate a tailored subprompt suited to their area of expertise:
                 - GrammarCritic
                 - DidacticsExpert
                 - SubjectMatterSpecialist
              3. Each subprompt must be fully self-contained, actionable, and include the original question context.

              Prompt:
              {{ prompt }}

              Agents:
              {{ agents }}

              Output:
              Return a JSON object where each key is the agent name and the value is their assigned subprompt.
            inputVariables:
              - name: prompt
                description: The original exam question to review
                required: true
              - name: agents
                description: A list of the agents and their descriptions
                required: true
          outputVariable:
            description: Mapping of agent names to their subprompts
            schema:
              type: object
              additionalProperties:
                type: string
      synthesis:
        kernel:
          use: huggingface-ms-phi-4
        function:
          template:
            content: >
              You are an expert synthesizer responsible for merging the responses of multiple agents into a single cohesive improvement suggestion.

              Inputs:
              {{ inputs }}

              Output:
              Provide a unified recommendation to improve the original exam question. Your response should be concise,
              clearly structured, and reflect the contributions of all agents without repeating content.
            inputVariables:
              - name: inputs
                description: The individual agent responses
                required: true
          outputVariable:
            description: The final synthesized recommendation
            schema:
              type: string
```

### Convergence Process Strategy

Defines the strategy of a convergence process.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| decomposition | [`decompositionStrategy`](#decomposition-strategy) | `no` | The definition of the kernel function strategy, if any, used to decompose the initial prompt into specialized sub-prompts tailored to each contributing agent. |
| synthesis | [`synthesisStrategy`](#synthesis-strategy) | `no` | The kernel function strategy used to synthesize the individual agent responses into a single, unified output. |

### Decomposition Strategy

Defines the kernel function strategy used to decompose the initial prompt into specialized sub-prompts tailored to each contributing agent.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| promptVariableName | `string` | `no` | The name of the variable used to store the prompt to decompose.<br>*Defaults to `prompt`.* |
| agentsVariableName | `string` | `no` | The name of the variable used to store the agents to tailor sub-prompts for.<br>*Defaults to `agents`.* |

> [!NOTE]  
> This definition **extends** the [`Kernel Function Strategy`](#kernel-function-strategy), and inherits all its properties.

### Synthesis Strategy

Defines the kernel function strategy used to synthesize multiple inputs into a single, unified output.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| inputsVariableName | `string` | `no` | The variable name used to pass the collection of inputs to the synthesis function.<br>*Defaults to `inputs`. |

> [!NOTE]  
> This definition **extends** the [`Kernel Function Strategy`](#kernel-function-strategy), and inherits all its properties.

### Agent Interface

Defines an interface to an agent exposed by the application.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| endpoints | [`interfaceEndpointCollection`](#interface-endpoint-collection) | `yes` | Defines the access points through which the agent interface is made available. |

> [!NOTE]  
> This definition **extends** [`agent`](#agent), and inherits all its properties.

### Process Interface

Defines an interface to an agentic process exposed by the application.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| endpoints | [`interfaceEndpointCollection`](#interface-endpoint-collection) | `yes` | Defines the access points through which the agentic process interface is made available. |

> [!NOTE]  
> This definition **extends** [`agenticProcess`](#agentic-process), and inherits all its properties.

### Interface Endpoint Collection

Defines the access points through which an application interface is made available.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| a2a | `boolean` | `no` | Indicates whether the interface is publicly documented and accessible via the [Agent2Agent protocol](https://github.com/google/A2A). |
| http | [`httpInterface`](#http-interface) | `no` | Defines the HTTP-specific configuration used to expose the interface over standard web protocols. |

### Http Interface

Defines the HTTP interface through which an application component (e.g., an agent) is exposed.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| path | `string` | `yes` | Specifies the relative URL path at which the component is exposed. |
| authentication | [`securityScheme`](#security-scheme) | `no` | Specifies the authentication scheme, if any, used to protect access to the HTTP endpoint.<br>Follows [OpenAPI v3.0.3 `Security Scheme Object` structure](https://swagger.io/specification/v3/#security-scheme-object). |

### Security Scheme

Defines the authentication method used to secure access to an exposed interface.

> [!NOTE]  
> Follows [OpenAPI v3.0.3 `Security Scheme Object` structure](https://swagger.io/specification/v3/#security-scheme-object).

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| type | `string` | `yes` | The type of the security scheme.<br>*Supported values are `apiKey`, `http`, `mutualTLS`, `oauth2`, `openIdConnect`.* |
| description | `string` | `no` | A short description for security scheme. CommonMark syntax MAY be used for rich text representation. |
| name | `string` | `no` | The name of the header, query or cookie parameter to be used.<br>*Required if `type` is set to `apiKey`.* |
| in | `string` | `no` | The location of the API key.<br>*Supported values are `query`, `header` or `cookie`.<br>*Required if `type` is set to `apiKey`.* |
| scheme | `string` | `no` | The name of the HTTP Authorization scheme to be used in the [Authorization header as defined in RFC7235](https://datatracker.ietf.org/doc/html/rfc7235#section-5.1). The values used SHOULD be registered in the [IANA Authentication Scheme registry](https://www.iana.org/assignments/http-authschemes/http-authschemes.xhtml).<br>*Required if `type` is set to `http`.* |
| bearerFormat | `string` | `no` | A hint to the client to identify how the bearer token is formatted. Bearer tokens are usually generated by an authorization server, so this information is primarily for documentation purposes.<br>*Applies if `type` is set to `http`, otherwise ignored.* |
| flows | [`oauthFlowCollection`](#oauth-flow-collection) | `no` | An object containing configuration information for the flow types supported.<br>*Required if `type` is set to `oauth2`.* |
| openIdConnectUrl | `string` | `no` | OpenId Connect URL to discover OAuth2 configuration values. This MUST be in the form of a URL.<br>*Required if `type` is set to `openIdConnect`.* |

### OAuth Flow Collection

Defines the configuration of the supported OAuth Flows.

> [!NOTE]  
> Follows [OpenAPI v3.0.3 `OAuth Flows Object` structure](https://swagger.io/specification/v3/#oauth-flows-object).

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| implicit | [`oauthFlow`](#oauth-flow) | `no` | Configuration for the OAuth Implicit flow. |
| password | [`oauthFlow`](#oauth-flow) | `no` | Configuration for the OAuth Resource Owner Password flow. |
| clientCredentials | [`oauthFlow`](#oauth-flow) | `no` | Configuration for the OAuth Client Credentials flow. Previously called application in OpenAPI 2.0. |
| authorizationCode | [`oauthFlow`](#oauth-flow) | `no` | Configuration for the OAuth Authorization Code flow. Previously called accessCode in OpenAPI 2.0. |

### OAuth Flow

Defines the configuration of an OAuth Flow.

> [!NOTE]  
> Follows [OpenAPI v3.0.3 `OAuth Flow Object` structure](https://swagger.io/specification/v3/#oauth-flow-object).

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| authorizationUrl | `string` | `no` | The authorization URL to be used for this flow. This MUST be in the form of a URL.<br>*Required if applies to a `implicit` or `authorizationCode` flow, otherwise ignored.* |
| tokenUrl | `string` | `yes` | The token URL to be used for this flow. This MUST be in the form of a URL.<br>*Required if applies to a `password`, `clientCredentials` or `authorizationCode` flow, otherwise ignored.* |
| refreshUrl | `string` | `no` | The URL to be used for obtaining refresh tokens. This MUST be in the form of a URL.<br>*Applies if to `oauth2` authentication, otherwise ignored.* |
| scopes | `map{string}` | `no` | The available scopes for the OAuth2 security scheme. A map between the scope name and a short description for it. The map MAY be empty.<br>*Required if applies to an `oauth2` authentication.* |

### Toolset

Defines a toolset that exposes one or more callable tools or functions which kernels can invoke at runtime. Toolsets enable structured interactions with external APIs, internal functions, or logic providers.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| mcp | [`mcpToolset`](#mcp-toolset) | `no` | Configures a Model Context Protocol (MCP) based toolset, where tools are declared as modules accessible via standard capability interfaces.<br>*Required if not other property has been set, otherwise ignored.*  |
| openapi | [`openapiToolset`](#openapi-toolset) | `no` | Configures an OpenAPI based toolset, enabling kernels to invoke HTTP-based tools using structured OpenAPI operation definitions.<br>*Required if not other property has been set, otherwise ignored.* |

### MCP Toolset

Defines a toolset based on the Model Context Protocol (MCP), where tools are declared as modules accessible via standard capability interfaces.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| transport | [`mcpTransport`](#mcp-transport) | `yes` | Defines the transport mechanism used by the MCP toolset to communicate with its capabilities. |
| client | [`mcpClient`](#mcp-client) | `yes` | Defines the MCP client settings used to interface with the toolset. |

### MCP Transport

Defines the transport mechanism used by the MCP toolset to communicate with its capabilities.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| http | [`mcpHttpTransport`](#mcp-http-transport) | `no` | Defines an HTTP-based transport for communicating with MCP capabilities.<br>*Required if not other property has been set, otherwise ignored.* |
| stdio | [`mcpStdioTransport`](#mcp-stdio-transport) | `no` | <br>*Required if not other property has been set, otherwise ignored.* |

### MCP HTTP Transport

Defines an HTTP-based transport for communicating with MCP capabilities.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| endpoint | [`endpoint`](#endpoint) | `yes` | The remote HTTP endpoint exposing MCP-compatible modules. |
| headers | `map{string}` | `no` | A mapping of HTTP headers, if any, to include in transport requests. |

### MCP STDIO Transport

Defines a transport that uses standard input/output (stdio) to communicate with a local process implementing MCP capabilities.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| command | `string` | `yes` | Specifies the command and arguments used to launch the stdio-based MCP process. |
| arguments | `string[]` | `no` | An optional list of command-line arguments passed to the command. |

### MCP Client

Defines the MCP client settings used to interface with the toolset.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| implementation | [`mcpClientImplementation`](#mcp-client-implementation) | `yes` | Specifies the client implementation used to interact with the MCP toolset. |
| protocolVersion | `string` | `yes` | The version of the MCP protocol supported by the client when interacting with the toolset.<br>*Defaults to `2024-11-05`.* |
| timeout | [`duration`](#duration) | `no` | Optional timeout applied to toolset requests issued by the client. |

### MCP Client Implementation

Specifies the client implementation used to interact with the MCP toolset

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| name | `string` | `yes` | The name of the MCP client implementation. |
| version | `string` | `yes` | The version of the MCP client implementation. |

### OpenAPI Toolset

Defines a toolset based on an OpenAPI specification, enabling kernels to invoke HTTP-based tools using structured OpenAPI operation definitions.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| document | [`externalResource`](#external-resource) | `yes` | The OpenAPI document to use. |

### Authentication Policy

Defines the mechanism used to authenticate users and workflows attempting to access a service or a resource.

#### Properties

| Property | Type | Required | Description |
|----------|:----:|:--------:|-------------|
| use | `string` | `no` | The name of the top-level authentication definition to use. Cannot be used by authentication definitions defined at top level. |
| basic | [`basicAuthentication`](#basic-authentication) | `no` | The `basic` authentication scheme to use, if any.<br>Required if no other property has been set, otherwise ignored. |
| bearer | [`bearerAuthentication`](#bearer-authentication) | `no` | The `bearer` authentication scheme to use, if any.<br>Required if no other property has been set, otherwise ignored. |
| certificate | [`certificateAuthentication`](#certificate-authentication) | `no` | The `certificate` authentication scheme to use, if any.<br>Required if no other property has been set, otherwise ignored. |
| digest | [`digestAuthentication`](#digest-authentication) | `no` | The `digest` authentication scheme to use, if any.<br>Required if no other property has been set, otherwise ignored. |
| oauth2 | [`oauth2`](#oauth2-authentication) | `no` | The `oauth2` authentication scheme to use, if any.<br>Required if no other property has been set, otherwise ignored. |
| oidc | [`oidc`](#openidconnect-authentication) | `no` | The `oidc` authentication scheme to use, if any.<br>Required if no other property has been set, otherwise ignored. |

### Basic Authentication

Defines the fundamentals of a 'basic' authentication.

#### Properties

| Property | Type | Required | Description |
|----------|:----:|:--------:|-------------|
| username | `string` | `yes` | The username to use. |
| password | `string` | `yes` | The password to use. |

### Bearer Authentication

Defines the fundamentals of a 'bearer' authentication

#### Properties

| Property | Type | Required | Description |
|----------|:----:|:--------:|-------------|
| token | `string` | `yes` | The bearer token to use. |

### OAUTH2 Authentication

Defines the fundamentals of an 'oauth2' authentication.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| authority | `uri-template` | `yes` | The URI that references the authority to use when making OAuth2 calls. |
| endpoints.token | `uri-template` | `no` | The relative path to the endpoint for OAuth2 token requests.<br>Defaults to `/oauth2/token`. |
| endpoints.revocation | `uri-template` | `no` | The relative path to the endpoint used to invalidate tokens.<br>Defaults to `/oauth2/revoke`. |
| endpoints.introspection | `uri-template` | `no` | The relative path to the endpoint used to validate and obtain information about a token, typically to check its validity and associated metadata.<br>Defaults to `/oauth2/introspect`. | 
| grant | `string` | `yes` | The grant type to use.<br>Supported values are `authorization_code`, `client_credentials`, `password`, `refresh_token` and `urn:ietf:params:oauth:grant-type:token-exchange`. |
| client.id | `string` | `no` | The client id to use.<br>Required if the `client.authentication` method has **not** been set to `none`. |
| client.secret | `string` | `no` | The client secret to use, if any. |
| client.assertion | `string` | `no` | A JWT containing a signed assertion with your application credentials.<br>Required when `client.authentication` has been set to `private_key_jwt`. |
| client.authentication | `string` | `no` | The client authentication method to use.<br>Supported values are `client_secret_basic`, `client_secret_post`, `client_secret_jwt`, `private_key_jwt` or `none`.<br>Defaults to `client_secret_post`. |
| request.encoding | `string` | `no` | The encoding of the token request.<br>Supported values are `application/x-www-form-urlencoded` and `application/json`.<br>Defaults to application/x-www-form-urlencoded. |
| issuers | `uri-template[]` | `no` | A list that contains that contains valid issuers that will be used to check against the issuer of generated tokens. |
| scopes | `string[]` | `no` | The scopes, if any, to request the token for. |
| audiences | `string[]` | `no` | The audiences, if any, to request the token for. |
| username | `string` | `no` | The username to use. Used only if the grant type is `Password`. |
| password | `string` | `no` | The password to use. Used only if the grant type is `Password`. |
| subject | [`oauth2Token`](#oauth2-token) | `no` | The security token that represents the identity of the party on behalf of whom the request is being made. |
| actor | [`oauth2Token`](#oauth2-token) | `no` | The security token that represents the identity of the acting party. |

#### OAUTH2 Token

Represents the definition of an OAUTH2 token

##### Properties

| Property | Type | Required | Description |
|----------|:----:|:--------:|-------------|
| token | `string` | `yes` | The security token to use to use. |
| type | `string` | `yes` | The type of security token to use. |

### OpenIdConnect Authentication

Defines the fundamentals of an 'oidc' authentication.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| authority | `uri-template` | `yes` | The URI that references the authority to use when making OpenIdConnect calls. |
| grant | `string` | `yes` | The grant type to use.<br>Supported values are `authorization_code`, `client_credentials`, `password`, `refresh_token` and `urn:ietf:params:oauth:grant-type:token-exchange`. |
| client.id | `string` | `no` | The client id to use.<br>Required if the `client.authentication` method has **not** been set to `none`. |
| client.secret | `string` | `no` | The client secret to use, if any. |
| client.assertion | `string` | `no` | A JWT containing a signed assertion with your application credentials.<br>Required when `client.authentication` has been set to `private_key_jwt`. |
| client.authentication | `string` | `no` | The client authentication method to use.<br>Supported values are `client_secret_basic`, `client_secret_post`, `client_secret_jwt`, `private_key_jwt` or `none`.<br>Defaults to `client_secret_post`. |
| request.encoding | `string` | `no` | The encoding of the token request.<br>Supported values are `application/x-www-form-urlencoded` and `application/json`.<br>Defaults to application/x-www-form-urlencoded. |
| issuers | `uri-template[]` | `no` | A list that contains that contains valid issuers that will be used to check against the issuer of generated tokens. |
| scopes | `string[]` | `no` | The scopes, if any, to request the token for. |
| audiences | `string[]` | `no` | The audiences, if any, to request the token for. |
| username | `string` | `no` | The username to use. Used only if the grant type is `Password`. |
| password | `string` | `no` | The password to use. Used only if the grant type is `Password`. |
| subject | [`oauth2Token`](#oauth2-token) | `no` | The security token that represents the identity of the party on behalf of whom the request is being made. |
| actor | [`oauth2Token`](#oauth2-token) | `no` | The security token that represents the identity of the acting party. |

### Duration

Defines a duration.

#### Properties

| Property | Type | Required | Description |
|----------|:----:|:--------:|-------------|
| Days | `integer` | `no` | Number of days, if any. |
| Hours | `integer` | `no` | Number of hours, if any. |
| Minutes | `integer` | `no`| Number of minutes, if any. |
| Seconds | `integer` | `no`| Number of seconds, if any. |
| Milliseconds | `integer` | `no`| Number of milliseconds, if any. |

### External Resource

Defines an external resource.

#### Properties

| Property | Type | Required | Description |
|----------|:----:|:--------:|-------------|
| name | `string` | `no` | The name, if any, of the defined resource. |
| endpoint | [`endpoint`](#endpoint) | `yes` | The endpoint at which to get the defined resource. |

### Endpoint

Defines an endpoint.

#### Properties

| Name | Type | Required | Description |
|:-----|:----:|:--------:|:------------|
| uri | `uri` | `yes` | The full URI used to access the target resource. |
| authentication | [`authenticationPolicy`](#authentication-policy) | `no` | Specifies the authentication policy, if any, used to access the endpoint. |