---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1b7e8fab6aafb599ed49939333ac998564cfa27f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511439"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows rendszerű gépek naplózása, amelyeken hiányzik a Rendszergazdák csoportban megadott tagok bármelyike](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport nem tartalmaz a szabályzatparaméterben felsorolt egy vagy több tagot. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[A Rendszergazdák csoportban további fiókkal rendelkező Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport olyan tagokat tartalmaz, amelyek nem szerepelnek a szabályzatparaméterben. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[A Rendszergazdák csoportban megadott tagokkal megjelölt Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport tartalmaz egy vagy több tagot a szabályzatparaméterben felsoroltak közül. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
