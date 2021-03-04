---
title: Log Analytics munkaterület létrehozása – Azure PowerShell
description: 'Azure PowerShell szkript minta – Log Analytics munkaterület létrehozása a következőhöz:'
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: c04eb177eb8c52464be2ab7702a365d7a7b54e07
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040940"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Log Analytics munkaterület létrehozása a PowerShell-lel

Ez a szkript gyorsan üzembe helyezhető egy Azure Log Analytics-munkaterülettel, amely akkor szükséges, ha meg szeretné kezdeni az adatok gyűjtését, elemzését és beavatkozását.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy új Log Analytics munkaterület létrehozásához az előfizetésben. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Információk beolvasása egy meglévő munkaterületről. |
| [Új – AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Létrehoz egy munkaterületet a megadott erőforráscsoport és hely között. |


## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

