---
title: A hatókör megismerése Azure Policy
description: Ismerteti Azure Resource Manager hatókörének fogalmát, valamint azt, hogy a Azure Policy hogyan vonatkozik a Azure Policy kiértékeléséhez szükséges erőforrások ellenőrzésére.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: c198d2d2961b6d9e10a3b78481183cba7f7197ca
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167736"
---
# <a name="understand-scope-in-azure-policy"></a>A hatókör megismerése Azure Policy

Számos olyan beállítás létezik, amely meghatározza, hogy mely erőforrások legyenek kiértékelve, és hogy a Azure Policy milyen erőforrásokat értékel ki. A vezérlőelemek elsődleges fogalma a _hatókör_. Az Azure Policy hatóköre a hatókörnek a Azure Resource Manager-ben való működésének a függvénye. A magas szintű áttekintést lásd: [hatókör a Azure Resource Managerban](../../../azure-resource-manager/management/overview.md#understand-scope).
Ez a cikk ismerteti a _hatókör_ fontosságát a Azure Policy és a kapcsolódó objektumok és tulajdonságok tekintetében.

## <a name="definition-location"></a>Definíció helye

Az Azure Policy által használt első példány hatóköre egy házirend-definíció létrehozásakor történik. A definíciót lehet menteni egy felügyeleti csoportba vagy előfizetésbe. A hely határozza meg azt a hatókört, amelyhez a kezdeményezést vagy házirendet hozzá lehet rendelni. Az erőforrásoknak meg kell egyezniük a hozzárendeléshez célként megadott definíciós hely erőforrás-hierarchiáján belül.

Ha a definíció helye:

- Az előfizetéshez tartozó csak **előfizetéshez** tartozó erőforrások rendelhetők hozzá a szabályzat-definícióhoz.
- **Felügyeleti csoport** – csak a gyermek-felügyeleti csoportokon belüli erőforrások és a gyermek előfizetések rendelhetők hozzá a szabályzat-definícióhoz. Ha azt tervezi, hogy a házirend-definíciót több előfizetésre is alkalmazza, akkor a helynek minden előfizetést tartalmazó felügyeleti csoportnak kell lennie.

A helynek az összes erőforrás által megosztott erőforrás-tárolónak kell lennie, amelyet a házirend-definíció használatára kíván használni. Ez az erőforrás-tároló általában a legfelső szintű felügyeleti csoport közelében lévő felügyeleti csoport.

## <a name="assignment-scopes"></a>Hozzárendelési hatókörök

A hozzárendelések több olyan tulajdonsággal rendelkeznek, amelyek hatókört állítanak be. Ezeknek a tulajdonságoknak a használata határozza meg, hogy Azure Policy milyen erőforrást kell kiértékelnie, és hogy mely erőforrások számítanak a megfelelőség felé. Ezek a tulajdonságok a következő fogalmakat képezik le:

- Felvétel – az erőforrás-hierarchiát vagy az egyes erőforrásokat ki kell értékelni a definíciónak való megfelelőség érdekében. A `properties.scope` hozzárendelési objektum tulajdonsága határozza meg, hogy mit tartalmazzon, és hogyan értékelje ki a megfelelőséget. További információ: hozzárendelés- [definíció](./assignment-structure.md).

- Kizárás – a definíció nem értékeli ki az erőforrás-hierarchiát vagy az egyes erőforrásokat. A `properties.notScopes` hozzárendelési objektum _Array_ tulajdonsága határozza meg, hogy mit szeretne kizárni. Az ezen hatókörökön belüli erőforrások nincsenek kiértékelve, vagy nem szerepelnek a megfelelőségi számban. További információ: [hozzárendelési definíció – kizárt hatókörök](./assignment-structure.md#excluded-scopes).

A szabályzat-hozzárendelés tulajdonságain kívül a [házirend kivételi](./exemption-structure.md) objektuma is. A kivételek növelik a hatóköri történetet azáltal, hogy a hozzárendelések egy részének azonosítására szolgáló módszert biztosítanak a kiértékeléshez.

- Kivétel (előzetes verzióban **ingyenes** ) – az erőforrás-hierarchiát vagy az egyes erőforrásokat ki kell értékelni a definíciónak való megfelelőség szempontjából, de nem lesz kiértékelve olyan okból, mint például a lemondás vagy más módszerrel való enyhítés. Az ebben az állapotban lévő erőforrások a megfelelőségi jelentésekben **kivételként** jelennek meg, hogy nyomon követhetők legyenek. A kivételt képező objektum az erőforrás-hierarchiában vagy az egyes erőforrásokban alárendelt objektumként jön létre, amely meghatározza a kivétel hatókörét. Egy erőforrás-hierarchia vagy egyedi erőforrás több hozzárendelésre is felhasználható. A kivételt úgy lehet konfigurálni, hogy a tulajdonság használatával lejárjon egy ütemterven `expiresOn` . További információ: [kivételek definíciója](./exemption-structure.md).

  > [!NOTE]
  > Az erőforrás-hierarchiák vagy az egyes erőforrások kivételének megadásának következményei miatt a kivételek további biztonsági intézkedésekkel bírnak. Amellett, hogy a `Microsoft.Authorization/policyExemptions/write` műveletet az erőforrás-hierarchián vagy az egyedi erőforráson szeretné megkövetelni, a kivételek létrehozójának rendelkeznie kell a célként megadott hozzárendelésben szereplő művelettel `exempt/Action` .

## <a name="scope-comparison"></a>Hatókör-összehasonlítás

A következő táblázat a hatókör-beállítások összehasonlítását tartalmazza:

| | Belefoglalási | Kizárás (notScopes) | Kivétel |
|---|:---:|:---:|:---:|
|**Az erőforrások kiértékelése** | &#10004; | - | - |
|**Resource Manager-objektum** | - | - | &#10004; |
|**Házirend-hozzárendelési objektum módosítását igényli** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Következő lépések

- A szabályzat- [definíciós struktúra](./definition-structure.md)megismerése.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).