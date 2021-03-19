---
title: Azure PowerShell – előfizetés erőforráscsoporthoz
description: Ez a cikk egy minta Azure PowerShell parancsfájlt tartalmaz, amely bemutatja, hogyan fizethet elő az erőforráscsoport Event Grid eseményeire, és hogyan szűrheti az erőforrást.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: f3aeec208ba6b80643e50307b89f590977bfb446
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460762"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Feliratkozás egy erőforráscsoport eseményeire és szűrés erőforrásra a PowerShell-lel

Ez a szkript létrehoz egy Event Grid-előfizetést egy erőforráscsoport eseményeihez. Szűrőt használ az erőforráscsoport adott erőforrásához tartozó események lekéréséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>Minta parancsfájl – előzetes verziójú modul

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Az előnézeti minta parancsfájlhoz a Event Grid modul szükséges. A telepítéséhez futtassa a következőt `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](/powershell/azure/get-started-azureps).
