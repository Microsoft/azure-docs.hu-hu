---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6cac9a3b166b3eb0fdb3b43064b327440ee318ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105032686"
---
|Név |Leírás |Házirendek |Verzió |
|---|---|---|---|
|[\[Előzetes verzió \] : üzembe helyezés – előfeltételek konfigurálása a Azure monitor és az Azure biztonsági ügynökök virtuális gépeken való engedélyezéséhez](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Konfigurálja a gépeket a Azure Monitor és az Azure biztonsági ügynökök automatikus telepítéséhez. A Security Center az ügynököktől származó eseményeket gyűjti, és azokat a biztonsági riasztások és a testre szabott megerősítő feladatok (javaslatok) biztosítására használja fel. Hozzon létre egy erőforráscsoportot és Log Analytics munkaterületet abban a régióban, ahol a számítógép tárolja a naplózási rekordokat. Ez a szabályzat csak néhány régióban lévő virtuális gépekre vonatkozik. |5 |1.0.0 – előzetes verzió |
|[Virtual Machine Scale Sets Azure Monitor engedélyezése](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |A megadott hatókörben lévő Virtual Machine Scale Sets Azure Monitor engedélyezése (felügyeleti csoport, előfizetés vagy erőforráscsoport). Log Analytics munkaterületet paraméterként veszi fel. Megjegyzés: Ha a méretezési csoport upgradePolicy manuális értékre van állítva, a bővítményt a készletben lévő összes virtuális gépre alkalmaznia kell a frissítés meghívásával. A CLI-ben ez az az vmss Update-instances lenne. |6 |1.0.1 |
|[Azure Monitor for VMs engedélyezése](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Engedélyezze Azure Monitor a virtuális gépek (VM) számára a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). Log Analytics munkaterületet paraméterként veszi fel. |10 |2.0.0 |
