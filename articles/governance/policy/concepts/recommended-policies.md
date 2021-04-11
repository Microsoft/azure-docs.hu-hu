---
title: Ajánlott szabályzatok az Azure-szolgáltatásokhoz
description: Ismerteti, hogyan lehet megkeresni és alkalmazni az Azure-szolgáltatások, például az Azure Virtual Machines ajánlott szabályzatait.
ms.date: 03/31/2021
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: ef028dc61b10117509fdd27e815230ff27dbadfa
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090001"
---
# <a name="recommended-policies-for-azure-services"></a>Ajánlott szabályzatok az Azure-szolgáltatásokhoz

Azok az ügyfelek, akik újak, Azure Policy gyakran tekintik meg az erőforrások kezeléséhez és irányításához szükséges általános szabályzat-definíciókat. A Azure Policy **ajánlott házirendjei** a közös házirend-definíciók célzott listáját ismertetik, amelyekkel elkezdheti a használatot. A támogatott erőforrások **ajánlott házirendjeinek** felülete az adott erőforrás portáljának felhasználói felületén belül van beágyazva.

További Azure Policy beépített [definíciók: Azure Policy beépített definíciók](../samples/built-in-policies.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Az [Azure Virtual Machines](../../../virtual-machines/index.yml) **ajánlott házirendjei** a virtuális gépek **Áttekintés** lapján, a **képességek** lapon találhatók. A _Azure Policy_ kártyán válassza a "nincs konfigurálva" vagy a "# hozzárendelt" szöveget egy oldalsó ablaktábla megnyitásához az ajánlott házirendekkel. Minden olyan házirend-definíció, amely már hozzá van rendelve egy hatókörhöz, a virtuális gép már szürkén jelenik meg. Válassza ki az erre a virtuális gépre alkalmazandó ajánlott házirendeket, majd válassza a **szabályzatok hozzárendelése** lehetőséget a hozzárendelés létrehozásához.

Mivel a szervezet az [erőforrások és az erőforrás-hierarchia megszervezésével](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)éri el a lejáratot, javasoljuk, hogy a szabályzat-hozzárendeléseket egy erőforrásról az előfizetés vagy a [felügyeleti csoport](../../management-groups/index.yml) szintjére váltson át.

### <a name="azure-virtual-machines-recommended-policies"></a>Azure Virtual Machines ajánlott szabályzatok

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Olyan virtuális gépek naplózása, amelyek nem rendelkeznek a vész-helyreállítási beállításokkal. A vész-helyreállítással kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/asr-doc](../../../site-recovery/index.yml) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Felügyelt lemezeket nem használó virtuális gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Ez a házirend a felügyelt lemezeket nem használó virtuális gépeket naplózza |ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[A Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Az Azure Backup engedélyezésével gondoskodjon az Azure-Virtual Machines védelméről. A Azure Backup biztonságos és költséghatékony adatvédelmi megoldás az Azure-hoz. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatok hatásainak ismertetése](./effects.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).