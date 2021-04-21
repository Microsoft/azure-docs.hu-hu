---
title: Azure CLI-példaszkript – Egyéni témakör létrehozása | Microsoft Docs
description: Ez a cikk egy Azure CLI-példaszkprogramot mutat be, amely bemutatja, hogyan hozhat létre Azure Event Grid témakört.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4e381310db5a21e532b262063ee33de3a23edeb2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766739"
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
| [az eventgrid topic create](/cli/azure/eventgrid/topic#az_eventgrid_topic_create) | Egyéni Event Grid-témakör létrehozása. |


## <a name="next-steps"></a>Következő lépések

* Az előfizetések lekérdezéséről lásd: [Event Grid-előfizetések lekérdezése](../query-event-subscriptions.md).
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
