---
title: Azure CLI-példaszkript – Egyéni témakör létrehozása | Microsoft Docs
description: Ez a cikk egy minta Azure CLI-parancsfájlt tartalmaz, amely bemutatja, hogyan hozhat létre Azure Event Grid egyéni témakört.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d61dac0e76fc7b7006fa36f1cb96965ccee3d863
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87494128"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Egyéni Event Grid-témakör létrehozása az Azure CLI-vel

Ez a szkript létrehoz egy egyéni Event Grid-témakört.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az egyéni témakör létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az eventgrid topic create](/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Egyéni Event Grid-témakör létrehozása. |


## <a name="next-steps"></a>Következő lépések

* Az előfizetések lekérdezéséről lásd: [Event Grid-előfizetések lekérdezése](../query-event-subscriptions.md).
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
