---
title: 'How-to: Create and deploy an Azure OpenAI Service resource'
titleSuffix: Azure OpenAI
description: Learn how to get started with Azure OpenAI Service and create your first resource and deploy your first model in the Azure CLI or the Azure portal.
#services: cognitive-services
manager: nitinme
ms.service: azure-ai-openai
ms.custom: devx-track-azurecli, build-2023, build-2023-dataai, devx-track-azurepowershell
ms.topic: how-to
ms.date: 05/20/2024
zone_pivot_groups: openai-create-resource
author: mrbullwinkle
ms.author: mbullwin
recommendations: false
---

# Create and deploy an Azure OpenAI Service resource

* goal
  * how to 
    * create a resource
    * deploy a model

* ways to create resources | Azure
  * [Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_openai_tip#create/Microsoft.CognitiveServicesOpenAI)
  * REST APIs, the Azure CLI, PowerShell, or client libraries
  * Azure Resource Manager (ARM) templates

::: zone pivot="web-portal"

[!INCLUDE [Azure portal resource](../includes/create-resource-portal.md)]

::: zone-end

::: zone pivot="cli"

[!INCLUDE [Azure CLI resource](../includes/create-resource-cli.md)]

::: zone-end

::: zone pivot="ps"

[!INCLUDE [Azure PowerShell resource](../includes/create-resource-powershell.md)]

::: zone-end
