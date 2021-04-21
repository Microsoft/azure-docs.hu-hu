---
title: Azure CLI-példaszkript – Feliratkozás Azure-előfizetésre | Microsoft Docs
description: Ez a cikk egy Azure CLI-példaszkprogramot mutat be, amely bemutatja, hogyan iratkhat fel Azure Event Grid eseményekre az Azure CLI használatával.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e523311c9df15e5801a65d69506d09a60b874d79
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766766"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>Feliratkozás egy Azure-előfizetés eseményeire az Azure CLI-vel

Ez a szkript létrehoz egy Event Grid-előfizetést egy Azure-előfizetés eseményeihez.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az előzetes verziójú példaszkripthez az Event Grid-bővítményre van szükség. A telepítéséhez futtassa az `az extension add --name eventgrid` parancsot.

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Példaszkript – előzetes verziójú bővítmény

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Event Grid-előfizetés létrehozása. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) – bővítmény verziója | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>Következő lépések

* Az előfizetések lekérdezéséről lásd: [Event Grid-előfizetések lekérdezése](../query-event-subscriptions.md).
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
