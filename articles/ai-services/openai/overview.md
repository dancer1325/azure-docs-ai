---
title: What is Azure OpenAI Service?
titleSuffix: Azure AI services
description: Apply advanced language models to variety of use cases with Azure OpenAI
manager: nitinme
author: mrbullwinkle
ms.author: mbullwin
ms.service: azure-ai-openai
ms.topic: overview
ms.date: 08/14/2024
ms.custom: build-2023, build-2023-dataai
recommendations: false
---

# What is Azure OpenAI Service?

* Azure OpenAI Service
  * provides
    * 💡REST API -- access to -- OpenAI's powerful language models 💡
      * o1-preview,
      * o1-mini,
      * GPT-4o,
      * GPT-4o mini,
      * GPT-4 Turbo + Vision, 
      * GPT-4,
      * GPT-3.5-Turbo,
      * Embeddings model series
  * 👀ways to access👀 
    * REST APIs,
    * Python SDK,
    * Azure OpenAI Studio's web-based interface 

* 💡PREVIOUS models
  * enable to 💡
    * content generation,
    * summarization,
    * image understanding,
    * semantic search,
    * natural language
    * code translation.

## Features overview

| Feature | Azure OpenAI |
| --- | --- |
| Models available | **o1-preview** & **o1-mini** - (Limited Access - [Request Access](https://aka.ms/oai/modelaccess))<br>**GPT-4o & GPT-4o mini**<br> **GPT-4 series (including GPT-4 Turbo with Vision)** <br>**GPT-3.5-Turbo series**<br> Embeddings series|
| Fine-tuning | `GPT-4o-mini` (preview) <br> `GPT-4` (preview) <br>`GPT-3.5-Turbo` (0613) <br> `babbage-002` <br> `davinci-002`.|
| Price | [here](https://azure.microsoft.com/pricing/details/cognitive-services/openai-service/) <br> [GPT-4 Turbo + Vision](../openai/concepts/gpt-with-vision.md#special-pricing-information)|
| Virtual network support & private link support | Yes <br/> ❌EXCEPT, you use [Azure OpenAI \| your data](./concepts/use-your-data.md)❌ |
| Managed Identity| Yes -- via -- Microsoft Entra ID | 
| UI experience | \| account & resource management, **Azure portal**  <br>  \| model exploration and fine-tuning,  **Azure OpenAI Service Studio**|
| Model regional availability | [Model availability](./concepts/models.md) |
| Content filtering | Prompts & completions -- are evaluated against -- our content policy <br/> HIGH severity content -- will be -- filtered |

## Responsible AI

* Microsoft' recommendations,
  * AI driven / people first
  * take care of Generative models
  * follow [principles for responsible AI](../../machine-learning/concept-responsible-ai.md)
  * adopting a [Code of Conduct](/legal/cognitive-services/openai/code-of-conduct?context=/azure/ai-services/openai/context/context)
  * build [content filters](../content-safety/overview) -- to support -- customers
  * providing responsible AI [information and guidance](../../cognitive-services/openai/transparency-note?context=%2Fazure%2Fai-services%2Fopenai%2Fcontext%2Fcontext&tabs=image)

## How do I get access -- to -- Azure OpenAI?

* Limited Access registration form
  * ❌| MOST Azure OpenAI models, NOT required to access ❌  
* [Azure OpenAI Limited Access page](../content-safety/limited-access)

## Azure OpenAI vs OpenAI

* Azure OpenAI Service
  * enables customers
    * advanced language AI -- via -- OpenAI GPT-4, GPT-3, Codex, DALL-E, Whisper, and text to speech models / 💡Azure's security & enterprise promise💡
      * ⭐️== run OpenAI models + Azure's secure capabilities⭐️
  * provides
    * private networking,
    * regional availability,
    * responsible AI content filtering
* Azure OpenAI + OpenAI
  * 👀co-develops the APIs👀
    * -> compatibility & smooth transition 

## Key concepts

### Prompts & completions

* "/completions" endpoint
  * 💡API service's core component💡  
  * provides
    * access to the 
      * model's text-in interface
      * model's text-out interface
  * 's argument
    * input  **prompt** == English text command
  * 's response
    * generate a text **completion**
  * _Example:_
    >**Prompt**:
            ```
            """
            count to 5 in a for loop
            """
            ```
    >**Completion**:
            ```
            for i in range(1, 6):
                print(i)
            ```

### Tokens

#### Text tokens
* TODO:
Azure OpenAI processes text by breaking it down into tokens. Tokens can be words or just chunks of characters. For example, the word “hamburger” gets broken up into the tokens “ham”, “bur” and “ger”, while a short and common word like “pear” is a single token. Many tokens start with a whitespace, for example “ hello” and “ bye”.

The total number of tokens processed in a given request depends on the length of your input, output and request parameters. The quantity of tokens being processed will also affect your response latency and throughput for the models.

#### Image tokens (GPT-4 Turbo with Vision and GPT-4o)

The token cost of an input image depends on two main factors: the size of the image and the detail setting (low or high) used for each image. Here's a breakdown of how it works:

- **Detail: Low resolution mode**
    - Low detail allows the API to return faster responses and consume fewer input tokens for use cases that don’t require high detail.
    - These images cost 85 tokens each, regardless of the image size.
    - **Example: 4096 x 8192 image (low detail)**: The cost is a fixed 85 tokens, because it's a low detail image, and the size doesn't affect the cost in this mode.
      
- **Detail: High resolution mode**
    - High detail lets the API see the image in more detail by cropping it into smaller squares. Each square uses more tokens to generate text.
    - The token cost is calculated by a series of scaling steps:
        1. The image is first scaled to fit within a 2048 x 2048 square while maintaining its aspect ratio.
        1. The image is then scaled down so that the shortest side is 768 pixels long.
        1. The image is divided into 512-pixel square tiles, and the number of these tiles (rounding up for partial tiles) determines the final cost. Each tile costs 170 tokens.
        1. An additional 85 tokens are added to the total cost.
    - **Example: 2048 x 4096 image (high detail)**
        1. Initially resized to 1024 x 2048 to fit in the 2048 square.
        1. Further resized to 768 x 1536.
        1. Requires six 512px tiles to cover.
        1. Total cost is `170 × 6 + 85 = 1105` tokens.

### Resources

* goal
  * [create a resource or service instance](how-to/create-resource.md) | your Azure Subscription 
* see Azure's [resource management design](/azure/azure-resource-manager/management/overview)

### Deployments

* requirements
  * [create an Azure OpenAI Resource](#resources)
* == deploy a model
  * Reason: 🧠BEFORE making API calls & generating text🧠
  * way to do it
    * Deployment APIs

### Prompt engineering
* TODO:
The GPT-3, GPT-3.5 and GPT-4 models from OpenAI are prompt-based. With prompt-based models, the user interacts with the model by entering a text prompt, to which the model responds with a text completion. This completion is the model’s continuation of the input text.

While these models are extremely powerful, their behavior is also very sensitive to the prompt. This makes [prompt engineering](./concepts/prompt-engineering.md) an important skill to develop.

Prompt construction can be difficult. In practice, the prompt acts to configure the model weights to complete the desired task, but it's more of an art than a science, often requiring experience and intuition to craft a successful prompt.

### Models

* DIFFERENT capability and price point / EACH model 
  * DALL-E models (see [models](./concepts/models.md#dall-e))
    * from text prompts -> generate images 
  * Whisper models
    * speech -- is transcribed & translate to -- text
* see [models concept page](./concepts/models.md)

## Next steps

* [EXISTING models | Azure OpenAI](./concepts/models.md)