---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 02118ff7bf6568ba928ecea3658db1aa23e5015a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504861"
---
|Név |Leírás |Házirendek |Verzió |
|---|---|---|---|
|[\[Előzetes verzió: Üzembe helyezés – Előfeltételek konfigurálása az Azure Monitor \] és Azure Security-ügynökök engedélyezéséhez a virtuális gépeken](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Gépek konfigurálása a virtuális gépek és Azure Monitor Azure Security-ügynökök automatikus telepítéséhez. Security Center gyűjti az eseményeket az ügynököktől, és biztonsági riasztások és testre szabott biztonsági feladatok (javaslatok) végrehajtásához használja őket. Hozzon létre egy erőforráscsoportot és Egy Log Analytics-munkaterületet ugyanabban a régióban, mint a gép a naplórekordok tárolására. Ez a szabályzat csak néhány régióban található virtuális gépekre vonatkozik. |5 |1.0.0-preview |
|[A Azure Monitor engedélyezése Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Engedélyezze Azure Monitor a Virtual Machine Scale Sets hatókör (felügyeleti csoport, előfizetés vagy erőforráscsoport) hatókörében. A Log Analytics-munkaterületet használja paraméterként. Megjegyzés: Ha a méretezési készlet upgradePolicy beállítása Manuális, a bővítményt a készletben lévő összes virtuális gépre alkalmaznia kell a frissítés hívásával. A CLI-ban ez az az vmss update-instances. |6 |1.0.1 |
|[A Azure Monitor for VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Engedélyezze Azure Monitor virtuális gépek (VIRTUÁLIS gépek) számára a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics-munkaterületet használja paraméterként. |10 |2.0.0 |
