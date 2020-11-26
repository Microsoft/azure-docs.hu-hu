---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fb5f6dbb410a15bf88fccc1ed3f9c3ca0f830c70
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "96296087"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Azure szinapszis-munkaterületek az ügyfél által felügyelt kulcsokat használják a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |Az Azure szinapszis-munkaterületeken tárolt adatok titkosításának szabályozásához használja az ügyfél által felügyelt kulcsokat. Az ügyfél által felügyelt kulcsok dupla titkosítást biztosítanak egy második titkosítási réteg hozzáadásával az alapértelmezett titkosításhoz a szolgáltatás által felügyelt kulcsokkal. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Az Azure szinapszis-munkaterületeken található IP-tűzfalszabályok eltávolítását el kell távolítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |Az összes IP-tűzfalszabály eltávolítása növeli a biztonságot azáltal, hogy az Azure szinapszis-munkaterület csak privát végpontról érhető el. Ez a konfiguráció naplózza a nyilvános hálózati hozzáférést engedélyező tűzfalszabályok létrehozását a munkaterületen. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[A felügyelt munkaterület virtuális hálózatát engedélyezni kell az Azure szinapszis-munkaterületeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |A felügyelt munkaterület virtuális hálózatának engedélyezése biztosítja, hogy a munkaterület más munkaterületekről izolált hálózat legyen. Az ebben a virtuális hálózaton üzembe helyezett adatintegrációs és Spark-erőforrások felhasználói szintű elkülönítést is biztosítanak a Spark-tevékenységekhez. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[Az Azure szinapszis-munkaterületeken lévő magánhálózati végponti kapcsolatokat engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |A magánhálózati végpontok úgy konfigurálhatók, hogy az Azure-beli szinapszis-munkaterülethez kapcsolódjanak. Ezzel biztonságos kommunikációs csatornát lehet kikényszeríteni az Azure szinapszis munkaterületre. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[A szinapszis felügyelt magánhálózati végpontok csak jóváhagyott Azure Active Directory bérlők erőforrásaihoz csatlakozhatnak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |A szinapszis munkaterületét csak a jóváhagyott Azure Active Directory (Azure AD) bérlők erőforrásaihoz való kapcsolódás engedélyezésével védi. A jóváhagyott Azure AD-bérlők a szabályzat-hozzárendelés során meghatározhatók. |Naplózás, letiltva, megtagadás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
