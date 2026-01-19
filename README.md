# AIKit

## Overview

[4D AIKit](https://github.com/4d/4D-AIKit) is a built-in 4D component that enables interaction with third-party AI APIs.

This repo was forked for experimentation and testing purposes.

Normally you should use the offical releases.

## Install

Add `https://github.com/miyako/AIKit/` (without the official `4D-` prefix) to `dependencies.json`

## Topic: Function Calls and Structured Output

> [!TIP]
> Top icon indicates JSON schema capability. Botton icon indicates tool call capability.

|Model|FireWorks AI|Azure OpenAI|Moonshot AI|DeepInfra
|-|:-:|:-:|:-:|:-:|
|Llama 3.1 405B| |❌<br/>❌||
|Llama 3.3 70B Instruct| |❌<br/>❌||
|Llama 4 Scout 17B Instruct| |❌<br/>❌||
|Llama 4 Maverick 17B Instruct| |❌<br/>❌||
|Kimi K2 Thinking|✅<br/>❌|❌<br/>❌|✅<br/>✅|✅<br/>❌
|Qwen 3 235B Thinking|✅<br/>❌|||✅<br/>❌
|DeepSeek R1 0528|❌<br/>❌|❌<br/>❌||✅<br/>❌
|Cogito 671B v2|❌<br/>❌|

> [!WARNING]
> These models are technically open weight but too large to run on any consumer equipment. You need a cloud provider.

Function calls and structured outputs are features not just of the models (although the model plays a huge part) but also the platform on which the inference is performed. The *Kimi K2 Thinking* model performs best on its native platform. Azure OpenAI evidently does a lousy job at prompt injection and fails to deliver the full potential of models like *Kimi*, *Qwen 3* or *DeepSeek*. The test also shows suggests that *DeepSeek* is a model that can hallucinate badly without proper tuning.  

## Thinking vs. Reasoning vs Instruct

Function calls and structured outputs do not necessarily require models to think or reason. For simple agentic automation tasks, an "instruct" model that understands natural language and follows instructions would be both safer and more efficient. A "thinking" model first strategies its course of action, which consumes massive amount of tokens and increases the time it takes to generate the first token. It also introduces an unnecessary layer of unpredictability to what should be a straightforward task. Likewise, "reasoning" models might disobey instructions that seem, well, unreasonable. 

In this test, the prompt left the model to its own devices whether to use the tools or not. The combinaation of a demanding prompt and and exact schema might have pushed the model to an imapass or hallicanation rut. Some of the failures are not necessarily damning verdicts but rather an indication that maybe a simpler model or a verbose prompt was needed.

### Phi 

**Phi** is an open weight model developed by Microsoft. The model is hosted by multiple providers including ~~[Fireworks AI](https://fireworks.ai)~~, ~~[Together AI](https://www.together.ai)~~, and [Microsoft Foundry](https://azure.microsoft.com/en-us). 

**Phi 4 Mini** is the first version that added native function call support and native structured output. Earlier versions including the original Phi 4 do not have native function call support. **llama.cpp** simulates function calls by system prompt injection and post processing when the `--jinja` and `--chat-template phi3` CLI flags are passed.

|Model|llama.cpp|Azure OpenAI|DeepInfra
|-|:-:|:-:|:-:|
|Phi 4 Mini Flash Reasoning|||
|Phi 4 Mini Reasoning Plus|✅<br/>❌|
|Phi 4 Mini Reasoning|✅<br/>❌|❌<br/>❌|
|Phi 4 Mini Instruct|✅<br/>❌|❌<br/>❌
|Phi 4 Reasoning Plus|
|Phi 4 Reasoning||❌<br/>❌||
|Phi 4 |||❌<br/>❌

The backend server framework used by Microsoft Azure OpenAI evidently implements the older "JSON Mode" standard not the newer "Structured Outputs" standard used by 4D AI Kit. That means the output is unreliable. Quick testing shows that the "instuct" models produce valid JSON but ignore the schema. "Reasoning" models don't event produce valid JSON. This is likely a feature of the backend server, not necesarily of the models themselves.

---

#### OpenAI Compatibility with AIKit function calling

|Model&nbsp;Family|Version|Function&nbsp;Calling|Remarks
|-|-|:-:|-|
|GPT|3.5||This is not a chat model and thus not supported in the v1/chat/completions endpoint. 
||3.5-turbo||This is not a chat model and thus not supported in the v1/chat/completions endpoint. 
||o1||This model is only supported in v1/responses and not in v1/chat/completions.
||o3||This is not a chat model and thus not supported in the v1/chat/completions endpoint. 
||o4||Unsupported value: 'temperature' does not support 0 with this model. Only the default (1) value is supported.
||4||
||4o|✅|
||4-turbo|✅|
||4.1|✅|
||5||This model is only supported in v1/responses and not in v1/chat/completions.
||5.1|✅|
||5.2|✅|

For function calling you would want to use a **reasoning** (thinking, chain of thought) model. The first reasoning model from OpenAI is **4o** which was released between 4 and 4.1. 4.1 is the last non-reasoning model. After 4.1 came o1, o3, and o4 which are all reasoning models. GPT 5 series are all reasoninig models. As of today, 3.5, 4o, 4.1, o1, o3, o4 are legacy models.

> Use 4o, 4-turbo, 4.1, 5.1, or 5.2.

#### Google Compatibility with AIKit function calling

|Model&nbsp;Family|Version|Function&nbsp;Calling|Remarks
|-|-|:-:|-|
|Gemini|2.0|
||2.5|✅|
| |3||

> Use Gemini 2.5. Gemini 3 (preview) on OpenAI compatibility seems to have a regression.

#### Google Compatibility with AIKit function calling

|Model&nbsp;Family|Version|Function&nbsp;Calling|Remarks
|-|-|:-:|-|
|Gemini|2.0|
||2.5|✅|
| |3||

> Use 2.5. Gemini 3 (preview) on OpenAI compatibility seems to have a regression.

#### Claude Compatibility with AIKit function calling

|Model&nbsp;Family|Version|Function&nbsp;Calling|Remarks
|-|-|:-:|-|
|Haiku|4.5|✅|
|Opus|4.5|✅|
|Sonnet|4.5|✅|Request might exceed the rate limit of 10,000 input tokens per minute.

> Use Haiku or Opus if you have a low quota.
