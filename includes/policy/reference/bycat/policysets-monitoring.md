---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f8095fc7c9be6ea23a573de8a5e871965d6c2a66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866033"
---
|Név |Leírás |Házirendek |Verzió |
|---|---|---|---|
|[\[Előzetes \] verzió: Üzembe helyezés – Előfeltételek konfigurálása a Azure Monitor és Azure Security-ügynökök engedélyezéséhez a virtuális gépeken](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Gépek konfigurálása a virtuális gépek és Azure Monitor Azure Security-ügynökök automatikus telepítéséhez. Security Center gyűjti az eseményeket az ügynököktől, és biztonsági riasztások és testre szabott biztonsági feladatok (javaslatok) végrehajtásához használja őket. Hozzon létre egy erőforráscsoportot és Egy Log Analytics-munkaterületet ugyanabban a régióban, ahol a gép tárolja a naplórekordokat. Ez a szabályzat csak néhány régióban található virtuális gépekre vonatkozik. |5 |1.0.0-preview |
|[A Azure Monitor engedélyezése Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Engedélyezze Azure Monitor a Virtual Machine Scale Sets hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). Paraméterként a Log Analytics-munkaterületet veszi fel. Megjegyzés: Ha a méretezési készlet upgradePolicy beállítása Manuális, akkor a bővítményt a készlet összes virtuális gépére alkalmaznia kell a frissítés hívásával. A CLI-ban ez az az vmss update-instances. |6 |1.0.1 |
|[A Azure Monitor for VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Engedélyezze Azure Monitor virtuális gépek (virtuális gépek) számára a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). Paraméterként a Log Analytics-munkaterületet veszi fel. |10 |2.0.0 |
