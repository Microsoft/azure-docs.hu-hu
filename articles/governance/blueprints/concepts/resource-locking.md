---
title: Az erőforrás-zárolás megismerése
description: Ismerje meg az Azure-tervrajzok zárolási lehetőségeit, amelyekkel biztosíthatja az erőforrások számára a tervrajzok kiosztását.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: b2004ad294ae0eec1b4f2fc6f49308efd32d652e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920190"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Az erőforrások zárolásának megismerése az Azure-tervekben

A konzisztens környezetek nagy méretekben történő létrehozása csak abban az esetben hasznos, ha van olyan mechanizmus, amely a konzisztencia fenntartására szolgál. Ez a cikk azt ismerteti, hogyan működik az erőforrás-zárolás az Azure-tervekben. Az erőforrás-zárolásra és a _megtagadási hozzárendelések_ alkalmazására vonatkozó példa az [új erőforrások védelme](../tutorials/protect-new-resources.md) oktatóanyagban található.

> [!NOTE]
> Az Azure-tervrajzok által üzembe helyezett erőforrás-zárolások csak a terv-hozzárendelés által üzembe helyezett erőforrásokra vonatkoznak. A meglévő erőforrások, például a már létező erőforráscsoportok, nincsenek hozzájuk adva zárolások.

## <a name="locking-modes-and-states"></a>Zárolási módok és állapotok

A zárolási mód a tervrajz-hozzárendelésre vonatkozik, és három lehetőség közül választhat: **ne zárolja**, **csak olvasható** vagy ne **törölje**. A zárolási módot a rendszer a terv hozzárendelése során konfigurálja az összetevő üzembe helyezése során. A terv hozzárendelésének frissítésével egy másik zárolási mód is beállítható.
A zárolási módokat azonban nem lehet az Azure-tervezeteken kívül módosítani.

A tervrajz-hozzárendelésekben az összetevők által létrehozott erőforrások négy állapottal rendelkeznek: **nincs zárolva**, **csak olvasható**, **nem szerkeszthető/** nem törölhető, vagy **nem törölhető**. Az egyes összetevők típusa **nem zárolt** állapotban lehet. Az alábbi táblázat egy erőforrás állapotának meghatározására használható:

|Mód|Összetevő típusú erőforrástípus|Állam|Leírás|
|-|-|-|-|
|Ne legyen zárolás|*|Nincs zárolva|Az Azure-tervrajzok nem védik az erőforrásokat. Ezt az állapotot az **írásvédett** erőforráshoz hozzáadott erőforrások, vagy az erőforráscsoport-összetevő **nem törölhető** a terv-hozzárendelésen kívül is használják.|
|Csak olvasási engedély|Erőforráscsoport|Nem lehet szerkeszteni/törölni|Az erőforráscsoport írásvédett, és az erőforráscsoport címkéi nem módosíthatók. A **nem zárolt** erőforrások hozzáadhatók, áthelyezhetők, módosíthatók vagy törölhetők ebből az erőforráscsoporthoz.|
|Csak olvasási engedély|Nem erőforráscsoport|Csak olvasási engedély|Az erőforrás semmilyen módon nem módosítható. Nincs változás, és nem törölhető.|
|Törlés mellőzése|*|Nem lehet törölni|Az erőforrások módosíthatók, de nem törölhetők. A **nem zárolt** erőforrások hozzáadhatók, áthelyezhetők, módosíthatók vagy törölhetők ebből az erőforráscsoporthoz.|

## <a name="overriding-locking-states"></a>Zárolási állapotok felülbírálása

Általában előfordulhat, hogy valaki a megfelelő [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../../../role-based-access-control/overview.md) rendelkezik az előfizetésben, például a "tulajdonos" szerepkört, amely lehetővé teszi bármely erőforrás módosítását vagy törlését. Ez a hozzáférés nem vonatkozik arra az esetre, amikor az Azure-tervrajzok egy üzembe helyezett hozzárendelés részeként zárolják a zárolást. Ha a hozzárendelés **csak olvasási** vagy nem **törlési** beállítással lett beállítva, akkor még az előfizetés tulajdonosa is elvégezheti a letiltott műveletet a védett erőforráson.

Ez a biztonsági mérték védi a definiált terv és az olyan környezet egységességét, amelyet véletlen vagy programozott törlés vagy módosítás alapján hoztak létre.

### <a name="assign-at-management-group"></a>Hozzárendelés felügyeleti csoportban

Az egyetlen lehetőség, amely megakadályozza, hogy az előfizetések tulajdonosai kivonják a terv hozzárendelését, hogy a tervet egy felügyeleti csoporthoz rendelje. Ebben az esetben csak a felügyeleti csoport **tulajdonosai** rendelkeznek a terv-hozzárendelés eltávolításához szükséges engedélyekkel.

Ha a tervet egy előfizetés helyett egy felügyeleti csoporthoz szeretné rendelni, a REST API a következőképpen fog megjelenni:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

A által definiált felügyeleti csoportnak `{assignmentMG}` a felügyeleti csoport hierarchiájában kell lennie, vagy ugyanabba a felügyeleti csoportba kell tartoznia, ahol a terv definícióját menti.

A terv-hozzárendelés kérelem törzse a következőképpen néz ki:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

A kérelem törzsében és egy előfizetéshez rendelt fő különbség a `properties.scope` tulajdonság. Ezt a kötelező tulajdonságot arra az előfizetésre kell beállítani, amelyre a terv-hozzárendelés vonatkozik. Az előfizetésnek a felügyeleti csoport hierarchiájának közvetlen gyermekének kell lennie, ahol a terv-hozzárendelést tárolja.

> [!NOTE]
> A felügyeleti csoport hatóköréhez hozzárendelt terv továbbra is előfizetési szintű terv-hozzárendelésként működik. Az egyetlen különbség, hogy a terv-hozzárendelést a rendszer tárolja, hogy megakadályozza az előfizetés-tulajdonosok számára a hozzárendelés és a társított zárolások eltávolítását.

## <a name="removing-locking-states"></a>Zárolási állapotok eltávolítása

Ha szükség lesz egy hozzárendelés által védett erőforrás módosítására vagy törlésére, kétféleképpen teheti meg.

- A terv-hozzárendelés frissítése a **nem zárolt** zárolási módba
- A terv hozzárendelésének törlése

A hozzárendelés eltávolításakor az Azure-tervrajzok által létrehozott zárolások törlődnek. Az erőforrás azonban marad, és a szokásos módon törölni kell.

## <a name="how-blueprint-locks-work"></a>A terv zárolásának működése

Egy olyan Azure-RBAC, amely [megtagadja a hozzárendelések](../../../role-based-access-control/deny-assignments.md) megtagadására vonatkozó műveletet a terv hozzárendelése során, ha a hozzárendelés a **csak olvasható** vagy a **nem törlés** lehetőséget választotta. A megtagadási műveletet a terv-hozzárendelés felügyelt identitása adja hozzá, és csak azonos felügyelt identitással lehet eltávolítani az összetevő-erőforrásokból. Ez a biztonsági mérték kikényszeríti a zárolási mechanizmust, és megakadályozza az Azure-tervezeteken kívüli terv zárolásának eltávolítását.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Képernyőfelvétel a hozzáférés-vezérlésről (I M) és egy erőforráscsoport megtagadási hozzárendeléseinek lapjáról." border="false":::

Az egyes üzemmódok [megtagadási hozzárendeléseinek tulajdonságai](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) a következők:

|Mód |Engedélyek. műveletek |Engedélyek. Tapintatok |Rendszerbiztonsági tag [i]. Típusa |ExcludePrincipals [i]. ID | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Csak olvasási engedély |**\** _ |_ *\* /READ **<br />** Microsoft. engedélyezés/zárolás/törlés **<br />** Microsoft. Network/virtualNetwork/alhálózatok/csatlakozás/művelet** |SystemDefined (mindenki) |tervezet-hozzárendelés és felhasználó által definiált **excludedPrincipals** |Erőforráscsoport – _igaz_; Erőforrás – _hamis_ |
|Törlés mellőzése |**\*/delete** | **Microsoft. Authorization/zárolások/törlés**<br />**Microsoft. Network/virtualNetwork/alhálózatok/csatlakozás/művelet** |SystemDefined (mindenki) |tervezet-hozzárendelés és felhasználó által definiált **excludedPrincipals** |Erőforráscsoport – _igaz_; Erőforrás – _hamis_ |

> [!IMPORTANT]
> A Azure Resource Manager legfeljebb 30 percig gyorsítótárazza a szerepkör-hozzárendelés részleteit. Ennek eredményeképpen a hozzárendelések megtagadására vonatkozó művelet megtagadása a terv erőforrásaiban nem lehet azonnal teljes mértékben érvényben. Ebben az időszakban lehetséges lehet egy olyan erőforrás törlése, amely a terv zárolásával védhető.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Tag kizárása egy megtagadási hozzárendelésből

Bizonyos tervezési vagy biztonsági helyzetekben szükség lehet egy rendszerbiztonsági tag kizárására a terv-hozzárendelés által létrehozott [megtagadási hozzárendelésből](../../../role-based-access-control/deny-assignments.md) . Ez a lépés REST API történik, ha a [hozzárendelés létrehozásakor](/rest/api/blueprints/assignments/createorupdate)a **zárolások** tulajdonságban legfeljebb öt értéket ad hozzá a **excludedPrincipals** tömbhöz. A következő hozzárendelési definíció egy példa a **excludedPrincipals** tartalmazó kérelem törzsére:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Művelet kizárása egy megtagadási hozzárendelésből

Hasonlóan a [megtagadási hozzárendelések](../../../role-based-access-control/deny-assignments.md) [kizárásához](#exclude-a-principal-from-a-deny-assignment) egy tervrajz-hozzárendelésben, kizárhatja az [Azure erőforrás-szolgáltató adott műveleteit](../../../role-based-access-control/resource-provider-operations.md). A **Properties. Locks** blokkon belül, ugyanazon a helyen, ahol a **ExcludedPrincipals** , egy **excludedActions** is hozzáadhatók:

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Míg a **excludedPrincipals** explicitnek kell lennie, a **excludedActions** -bejegyzések az `*` erőforrás-szolgáltatói műveletek helyettesítő karaktereit is használhatják.

## <a name="next-steps"></a>Következő lépések

- Kövesse az [új erőforrások védelemmel](../tutorials/protect-new-resources.md) foglalkozó oktatóanyagot.
- Tudnivalók a [tervek életciklusáról](./lifecycle.md).
- A [statikus és dinamikus paraméterek](./parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](./sequencing-order.md) testreszabásának elsajátítása.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).
