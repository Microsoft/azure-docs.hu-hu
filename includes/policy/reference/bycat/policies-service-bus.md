---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9305caa74728217c030b929e2ac0a01d5dca125b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429289"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani Service Bus névtérből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Service Bus-ügyfelek nem használhatnak névtér szintű hozzáférési szabályzatot, amely hozzáférést biztosít a névtérben lévő összes várólistához és témakörhöz. A legalacsonyabb jogosultsági szintű biztonsági modellel való összehangoláshoz hozzáférési házirendeket kell létrehoznia az entitások szintjén a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz lehessen hozzáférést biztosítani. |Naplózás, megtagadás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[A Service Bus erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Service Bus prémium szintű névtereknek ügyfél által felügyelt kulcsot kell használniuk a titkosításhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure Service Bus támogatja az inaktív adatok titkosítását a Microsoft által felügyelt kulcsokkal (alapértelmezett) vagy az ügyfél által felügyelt kulcsokkal. Ha az ügyfél által felügyelt kulcsokkal kívánja titkosítani az adattitkosítást, akkor a Service Bus által a névtérben tárolt adattitkosításhoz használt kulcsokhoz való hozzáférés hozzárendelését, elforgatását, letiltását és visszavonását választja. Vegye figyelembe, hogy Service Bus csak a prémium szintű névterek esetében támogatja a titkosítást az ügyfél által felügyelt kulcsokkal. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
