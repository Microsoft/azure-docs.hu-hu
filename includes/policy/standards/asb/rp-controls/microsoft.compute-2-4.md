---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7ecff61cb1e72c8c24c9be1588819219a5792f6c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284845"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azokat a Windows-gépeket naplózza, amelyeken a Log Analytics ügynök nem a várt módon van csatlakoztatva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha az ügynök nincs telepítve, vagy ha telepítve van, de a COM-objektum AgentConfigManager. MgmtSvcCfg azt adja vissza, hogy az a házirend-paraméterben megadott AZONOSÍTÓtól eltérő munkaterületre van regisztrálva. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |
|[A Log Analytics ügynöknek telepítve kell lennie a Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Ez a házirend minden Windows-vagy Linux-Virtual Machine Scale Sets naplóz, ha az Log Analytics ügynök nincs telepítve. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[A Log Analytics ügynöknek telepítve kell lennie a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Ez a házirend a Windows/Linux rendszerű virtuális gépeket naplózza, ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |
