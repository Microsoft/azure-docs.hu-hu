---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3859b73e61a20f5950531d1680a3015fcb53d3a2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613751"
---
|Név |Leírás |Házirendek |Verzió |
|---|---|---|---|
|[\[Előzetes verzió \] : üzembe helyezés – előfeltételek konfigurálása a Azure monitor és az Azure biztonsági ügynökök virtuális gépeken való engedélyezéséhez](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Konfigurálja a gépeket a Azure Monitor és az Azure biztonsági ügynökök automatikus telepítéséhez. A Security Center az ügynököktől származó eseményeket gyűjti, és azokat a biztonsági riasztások és a testre szabott megerősítő feladatok (javaslatok) biztosítására használja fel. Hozzon létre egy erőforráscsoportot és Log Analytics munkaterületet abban a régióban, ahol a számítógép tárolja a naplózási rekordokat. Ez a szabályzat csak néhány régióban lévő virtuális gépekre vonatkozik. |5 |1.0.0 – előzetes verzió |
|[Virtual Machine Scale Sets Azure Monitor engedélyezése](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |A megadott hatókörben lévő Virtual Machine Scale Sets Azure Monitor engedélyezése (felügyeleti csoport, előfizetés vagy erőforráscsoport). Log Analytics munkaterületet paraméterként veszi fel. Megjegyzés: Ha a méretezési csoport upgradePolicy manuális értékre van állítva, a bővítményt a készletben lévő összes virtuális gépre alkalmaznia kell a frissítés meghívásával. A CLI-ben ez az az vmss Update-instances lenne. |6 |1.0.1 |
|[Azure Monitor for VMs engedélyezése](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Engedélyezze Azure Monitor a virtuális gépek (VM) számára a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). Log Analytics munkaterületet paraméterként veszi fel. |10 |2.0.0 |
