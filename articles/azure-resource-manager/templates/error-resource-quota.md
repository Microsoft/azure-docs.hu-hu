---
title: Kvóta hibái
description: Ismerteti, Hogyan oldhatók fel az erőforrás-kvótával kapcsolatos hibák az erőforrások Azure Resource Manager használatával történő telepítésekor.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 75e8abf31d035a1e3a106bc0c6561624762db5d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530420"
---
# <a name="resolve-errors-for-resource-quotas"></a>Erőforráskvótákkal kapcsolatos hibák elhárítása

Ez a cikk az erőforrások telepítése során felmerülő kvóta-hibákat ismerteti.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Ha olyan sablont helyez üzembe, amely túllépi az Azure-kvótákat, a következőhöz hasonló telepítési hiba jelenik meg:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Vagy a következőket láthatja:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Ok

A kvóták erőforráscsoportonként, előfizetésenként, fiókonként és egyéb hatókörönként érvényesek. Lehetséges például, hogy az előfizetése úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Ha olyan virtuális gépet próbál üzembe helyezni, amely a megengedettnél több magot tartalmaz, akkor hibaüzenetet kap a kvóta túllépéséről.
A kvóta részletes ismertetését az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben tekintheti meg.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI esetén használja a `az vm list-usage` parancsot a virtuálisgép-kvóták kereséséhez.

```azurecli
az vm list-usage --location "South Central US"
```

Amely a következőket adja vissza:

```output
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

A PowerShell esetében használja a **Get-AzVMUsage** parancsot a virtuálisgép-kvóták megkereséséhez.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Amely a következőket adja vissza:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Megoldás

A kvóta növeléséhez lépjen a portálra, és küldjön egy támogatási problémát. A támogatási probléma esetén a kvóta növelését kell megadnia ahhoz a régióhoz, amelybe telepíteni kívánja.

> [!NOTE]
> Ne feledje, hogy az erőforráscsoportok esetében a kvóta minden egyes régióra érvényes, nem a teljes előfizetéshez. Ha 30 magot kell üzembe helyeznie az USA nyugati régiójában, az USA nyugati régiójában 30 Resource Manager-magot kell kérnie. Ha minden olyan régióban 30 magot kell üzembe helyeznie, amelyhez hozzáférése van, akkor minden régióban 30 Resource Manager-magot kell kérnie.
>
>

1. Válassza az **Előfizetések** lehetőséget.

   ![A képernyőfelvételen az Azure Portal menüjében a kiválasztott előfizetések láthatók.](./media/error-resource-quota/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés kiválasztása](./media/error-resource-quota/select-subscription.png)

3. **Használat + kvóták** kiválasztása

   ![Használat és kvóták kiválasztása](./media/error-resource-quota/select-usage-quotas.png)

4. A jobb felső sarokban válassza a **kérelem növekedés** lehetőséget.

   ![Kérések növekedése](./media/error-resource-quota/request-increase.png)

5. Töltse ki az űrlapot ahhoz a kvótatípushoz, amelyet növelni szeretne.

   ![Űrlap kitöltése](./media/error-resource-quota/forms.png)