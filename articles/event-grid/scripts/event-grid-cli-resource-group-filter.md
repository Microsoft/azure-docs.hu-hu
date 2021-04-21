---
title: Azure CLI – feliratkozás erőforráscsoportra & szűrés erőforrás alapján
description: Ez a cikk egy Azure CLI-példaszk szkriptet mutat be, amely bemutatja, hogyan iratkhat fel Event Grid egy erőforráshoz, és hogyan szűrhet erőforrásra.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a4343f4fb7791459cc3dc6e7db34433d8b7b60f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766640"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>Feliratkozás egy erőforráscsoport eseményeire és szűrés erőforrásra az Azure CLI-vel

Ez a szkript létrehoz egy Event Grid-előfizetést egy erőforráscsoport eseményeihez. Szűrőt használ az erőforráscsoport adott erőforrásához tartozó események lekéréséhez.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az előzetes verziójú példaszkripthez az Event Grid-bővítményre van szükség. A telepítéséhez futtassa az `az extension add --name eventgrid` parancsot.

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events/filter-events.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Példaszkript – előzetes verziójú bővítmény

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events-preview/filter-events-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Event Grid-előfizetés létrehozása. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) – bővítmény verziója | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>Következő lépések

* Az előfizetések lekérdezéséről lásd: [Event Grid-előfizetések lekérdezése](../query-event-subscriptions.md).
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
