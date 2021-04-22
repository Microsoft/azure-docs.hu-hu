---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b4fa21e37d8a0794898cee827c73559162eed76d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867807"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Naplózhatja a Windows rendszerű gépeket, amelyeken a Log Analytics-ügynök nem a várt módon csatlakozik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Előfeltételként a szabályzat-hozzárendelési hatókörben kell telepíteni az előfeltételeket. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha az ügynök nincs telepítve, vagy ha telepítve van, de a AgentConfigManager.MgmtSvcCfg COM-objektum azt adja vissza, hogy a szabályzatparaméterben megadott azonosítótól különböző munkaterületen van regisztrálva. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |
|[A Log Analytics-ügynököt telepíteni kell a Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Ez a szabályzat naplóz minden Windows-/Linux-Virtual Machine Scale Sets ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[A Log Analytics-ügynököt telepíteni kell a virtuális gépekre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Ez a szabályzat naplóz minden Windows/Linux rendszerű virtuális gépet, ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |
