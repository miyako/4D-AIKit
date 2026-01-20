# AIKit

## Overview

[4D AIKit](https://github.com/4d/4D-AIKit) is a built-in 4D component that enables interaction with third-party AI APIs.

This repo was forked for experimentation and testing purposes.

Normally you should use the offical releases.

## Install

Add `https://github.com/miyako/AIKit/` (without the official `4D-` prefix) to `dependencies.json`

## Abstract: Function Calls and Structured Output

4D 21 added [tool calling](https://blog.4d.com/4d-21-and-ai-kit-redefining-how-applications-think-and-act/) to AI Kit. Tool calling allows the AI to agentically make queries to your database. The feature is especially useful with thinking models or reasoning models that can plan a sequence of function calls to complete a multi-step task.

Another important innovation is [structured outputs](https://blog.4d.com/4d-aikit-structured-outputs/) where the AI knows how to exchange infomation in a format that other computer programs can understand. Without structured outputs the AI can only interface with humans, not machines. 

Function calls and structured outputs are features not just of the models but of the platform on which the inference is performed. To give an example, The *Kimi K2 Thinking* model performs well on its native platform but poorly on generic platforms. Same for Phi, Qwen, DeepSeek, or Gemma on Azure OpenAI. You need the perfect combination of prompt, model, and platform for the tools to work efficiently.

## Instruct vs. vs. Reasoning vs. Thinking

An **instruct** model is trained, or fine-tuned, to follow instructions than simply engage in a casual conversaion. It uses it LLM training to dtermine what the users wants and responds accrodingly. It will only use tools when specifcally asked to. It is the right kind of AI to use as an interface for simple automation tasks.

A **reasoning** model is specifically trained in logic. Unlike earlier models that are trained to give the most likely response, the model reflects on its generated output and tries to make sense of it. while it may sound more intelligent, it has the potential to disobey instructions that seem, well, unreasonable. For simple automation tasks, the reasoning may actually backfire and result in hallucination or a chain of thought stuck in a rut.

A **thinking** model is trained to think which course of action to take in order to satisfy the user's expectations. The thinking process consumes massive amount of tokens and increases the time the model takes to make the first move. It also introduces a layer of unpredictability as to what the model will actually do to reach its goal. 

## Evaluation

> [!TIP]
> Top icon indicates structured output capability. Botton icon indicates tool calling capability.

|Model|FireWorks AI|Azure OpenAI|Moonshot AI|DeepInfra
|-|:-:|:-:|:-:|:-:|
|Llama 3.3 70B Instruct| |❌<br/>❌||
|Llama 3.1 405B| |❌<br/>❌||
|Llama 4 Scout 17B Instruct| |❌<br/>❌||
|Llama 4 Maverick 17B Instruct| |❌<br/>❌||
|Kimi K2 Thinking|✅<br/>❌|❌<br/>❌|✅<br/>✅|✅<br/>❌
|DeepSeek R1 0528|❌<br/>❌|❌<br/>❌||✅<br/>❌
|Cogito 671B v2|❌<br/>❌|

> [!WARNING]
> These models are technically open weight but too large to run on any consumer equipment. You need a cloud provider.

### Phi 

**Phi** is an open weight model developed by Microsoft. Several variants of the model are hosted on [Microsoft Foundry](https://azure.microsoft.com/en-us). 

[**Phi 4 Mini Instruct**](https://huggingface.co/microsoft/Phi-4-mini-instruct) and [Phi 4 Multimodal Instruct](https://huggingface.co/microsoft/Phi-4-multimodal-instruct) supports function calls but not [Phi 4 Mini Reasoning](https://huggingface.co/microsoft/Phi-4-mini-reasoning) or the original [Phi 4](https://huggingface.co/microsoft/phi-4). Because the reasoning models do not support function calls, you need to give clear instructions on how to use the tools and what to do with the results; you can't expect the model to plan a sequence of function calls. If you give a reasoning task to the instruct model, it will keep making the same function calls and eventually run out of tokens:

> the request exceeds the available context size, try increasing it

Phi 4 supports structured output on llama.cpp but not on Microsoft Foundry. The backend server framework used by Microsoft Azure OpenAI evidently implements the older "JSON Mode" standard not the newer "Structured Outputs" standard. 

|Model|llama.cpp|Azure OpenAI
|-|:-:|:-:|
|Phi 4 Mini Flash Reasoning|&nbsp;<br>&nbsp;||
|Phi 4 Mini Reasoning Plus|✅<br/>❌|
|Phi 4 Mini Reasoning|✅<br/>❌|❌<br/>❌|
|Phi 4 Mini Instruct|✅<br/>⚠️|❌<br/>❌
|Phi 4 Reasoning Plus|✅<br/>❌
|Phi 4 Reasoning|✅<br/>❌|❌<br/>❌||
|Phi 4 |✅<br/>❌|❌<br/>❌
|Phi 4 Multimodal Instruct||❌<br/>❌

### Gemma

**Gemma** is an open weight model developed by Google. There are pre-trained (Pt) and instruction tuned (It) models. The "N" variants are specifically designed to run better on mobile devices. 

Function calls are not enabled on Google Cloud Platform. 

> Function calling is not enabled for {model}

Gemma 3 does not have native [function call](https://ai.google.dev/gemma/docs/capabilities/function-calling#function-calling-setup) support. Gemma 3 expects a standard two-role conversation between a user and an assistant, with no "tool" role in between. You can't have an effective agent without prompt engineering where the user translates the function result to a natural statement. There are community models fine tuned for better tool support. 

Google released [FunctionGemma](https://blog.google/innovation-and-ai/technology/developers-tools/functiongemma/) to address this issue. But it refuses to process any requests that suggest business:

> I cannot assist with drafting or estimating project proposals. My current capabilities are focused on managing job postings and tool data analysis. I cannot generate strategic business proposals or generate detailed financial projections for projects requiring project management or cost estimation.

Gemma 3 supports structured output on llama.cpp but not on Microsoft Foundry for the same reason as Phi 4. 

|Model|llama.cpp|Google Cloud Platform |
|-|:-:|:-:|
|Gemma 3 270M It|❌<br/>❌||
|Gemma 3 1B It|✅<br/>❌|❌<br/>❌|
|Gemma 3 4B It|✅<br/>❌|❌<br/>❌|
|Gemma 3 12B It||❌<br/>❌|
|Gemma 3 27B It||❌<br/>❌|
|Gemma 3N E4B It||❌<br/>❌|
|Gemma 3N E2B It||❌<br/>❌|
|FunctionGemma|❌<br/>❌|

### Qwen 

**Qwen** is an open weight model developed by Alibaba. Their flagship `Qwen3-235B-A22B` is hosted by Alibaba Cloud and multiple providers including [Fireworks AI](https://fireworks.ai) and [DeepInfra](https://deepinfra.com).  Qwen 2.5 and 3 both have native function call support.

|Model|llama.cpp|Azure OpenAI|DeepInfra |
|-|:-:|:-:|:-:|
|Qwen 3 0.6B|✅<br/>❌||
|Qwen 3 1.7B|❌<br/>❌||
|Qwen 3 4B Thinking 2507|✅<br/>✅||
|Qwen 3 4B Instruct 2507|✅<br/>✅||
|Qwen 3 14B |||❌<br/>❌
|Qwen 3 32B ||❌<br/>❌|✅<br/>❌
|Qwen 3 235B Thinking|||✅<br/>❌

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

> Use 2.5. Gemini 3 (preview) on OpenAI compatibility seems to have a regression.

#### Claude Compatibility with AIKit function calling

|Model&nbsp;Family|Version|Function&nbsp;Calling|Remarks
|-|-|:-:|-|
|Haiku|4.5|✅|
|Opus|4.5|✅|
|Sonnet|4.5|✅|Request might exceed the rate limit of 10,000 input tokens per minute.

> Use Haiku or Opus if you have a low quota.
