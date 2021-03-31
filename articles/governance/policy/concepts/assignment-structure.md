---
title: A szabályzat-hozzárendelési struktúra részletei
description: Az Azure Policy által használt szabályzat-hozzárendelési definíciót ismerteti az erőforrásokhoz a kiértékeléshez.
ms.date: 03/17/2021
ms.topic: conceptual
ms.openlocfilehash: 909c1c361e092c512a73854a40e22a67efe5f2f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604865"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-hozzárendelés struktúrája

A szabályzat-hozzárendeléseket a Azure Policy használja annak meghatározásához, hogy mely erőforrások legyenek hozzárendelve házirendekhez vagy kezdeményezésekhez. A szabályzat-hozzárendelés meghatározhatja az adott erőforráscsoport paramétereinek értékeit a hozzárendelés időpontjában, így lehetőség van arra, hogy újrahasznosítsa azokat a házirend-definíciókat, amelyek a különböző megfelelőségi igényeknek megfelelő erőforrás-tulajdonságokat kezelik.

A JSON használatával szabályzat-hozzárendelést hozhat létre. A szabályzat-hozzárendelés a következő elemeket tartalmazza:

- megjelenítendő név
- leírás
- metaadatok
- kényszerítési mód
- kizárt hatókörök
- házirend-definíció
- nem megfelelőségi üzenetek
- parameters

Például a következő JSON egy szabályzat-hozzárendelést mutat be _DoNotEnforce_ módban dinamikus paraméterekkel:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Az összes Azure Policy minta [Azure Policy minta](../samples/index.md).

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **DisplayName** és a **Leírás** használatával azonosíthatja a szabályzat-hozzárendelést, és megadhatja a környezetét az adott erőforrásokkal való használathoz. a **DisplayName** maximális hossza _128_ karakter, és a **Leírás** legfeljebb _512_ karakter hosszúságú lehet.

## <a name="enforcement-mode"></a>Kényszerítési mód

A **enforcementMode** tulajdonság lehetővé teszi a felhasználók számára, hogy a házirend érvénybe léptetése vagy az [Azure-műveletnapló](../../../azure-monitor/essentials/platform-logs-overview.md)bejegyzéseinek elindítása nélkül teszteljék a meglévő erőforrásokra vonatkozó szabályzatok eredményét. Ezt a forgatókönyvet általában "What If"-ként emlegetik, és a biztonságos üzembe helyezési gyakorlatokhoz igazodik. a **enforcementMode** eltér a [letiltott](./effects.md#disabled) effektustól, mivel ennek hatására az erőforrás-kiértékelés egyáltalán nem történik meg.

Ennek a tulajdonságnak a következő értékei vannak:

|Mód |JSON-érték |Típus |Szervizelés manuálisan |Tevékenység naplójának bejegyzése |Leírás |
|-|-|-|-|-|-|
|Engedélyezve |Alapértelmezett |sztring |Igen |Yes |A házirend hatása az erőforrás létrehozásakor vagy frissítésekor lép érvénybe. |
|Disabled (Letiltva) |DoNotEnforce |sztring |Igen |Nem | A házirend-effektus nem kényszerített az erőforrás létrehozásakor vagy frissítésekor. |

Ha a **enforcementMode** nincs megadva házirend-vagy kezdeményezési definícióban, a rendszer az _alapértelmezett_ értéket használja. A [szervizelési feladatok](../how-to/remediate-resources.md) a [deployIfNotExists](./effects.md#deployifnotexists) házirendek esetében is elindíthatók, még akkor is, ha a **enforcementMode** beállítása _DoNotEnforce_.

## <a name="excluded-scopes"></a>Kizárt hatókörök

A hozzárendelés **hatóköre** tartalmazza az összes gyermek erőforrás-tárolót és gyermek erőforrást. Ha a gyermek erőforrás-tároló vagy a gyermek erőforrás nem rendelkezik a definíció alkalmazásával, akkor a **notScopes** beállításával mindegyik _kizárható_ a kiértékelésből. Ez a tulajdonság egy tömb, amely lehetővé teszi egy vagy több erőforrás-tároló vagy erőforrás kiszámítását a kiértékelésből. a **notScopes** a kezdeti hozzárendelés létrehozása után adhatók hozzá vagy frissíthetők.

> [!NOTE]
> A _kizárt_ erőforrások nem azonosak a _mentesített_ erőforrásokkal. További információ: [a hatókör megismerése Azure Policy](./scope.md).

## <a name="policy-definition-id"></a>Házirend-definíció azonosítója

Ebben a mezőben egy házirend-definíció vagy egy kezdeményezési definíció teljes elérési útjának kell szerepelnie.
`policyDefinitionId` karakterlánc, nem tömb. Javasoljuk, hogy ha több házirendet gyakran társítanak egymáshoz, inkább egy [kezdeményezést](./initiative-definition-structure.md) használjon.

## <a name="non-compliance-messages"></a>Nem megfelelőségi üzenetek

Ha olyan egyéni üzenetet szeretne beállítani, amely leírja, hogy egy erőforrás miért nem felel meg a szabályzatnak vagy a kezdeményezési definíciónak, állítsa be `nonComplianceMessages` a hozzárendelés definíciójában. Ez a csomópont a bejegyzések tömbje `message` . Ez az egyéni üzenet a nem megfelelőségre vonatkozó alapértelmezett hibaüzeneten felül nem kötelező.

> [!IMPORTANT]
> A meg nem felelés esetén az egyéni üzenetek csak a [Resource Manager-módok](./definition-structure.md#resource-manager-modes) definícióit tartalmazó definíciók vagy kezdeményezések esetén támogatottak.

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Ha a hozzárendelés egy kezdeményezésre van beállítva, különböző üzenetek konfigurálhatók a kezdeményezés minden egyes házirend-definíciójában. Az üzenetek a `policyDefinitionReferenceId` kezdeményezési definícióban konfigurált értéket használják. Részletekért lásd: [szabályzat-definíciók tulajdonságai](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Paraméterek

A szabályzat-hozzárendelés ezen szegmense megadja a szabályzat- [definícióban vagy a kezdeményezési definícióban](./definition-structure.md#parameters)definiált paraméterek értékeit. Ez a kialakítás lehetővé teszi egy házirend vagy kezdeményezési definíció más erőforrásokkal való felhasználását, de különböző üzleti értékeket vagy eredményeket kereshet.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

Ebben a példában a házirend-definícióban korábban definiált paraméterek a `prefix` és a `suffix` . Ez az adott szabályzat `prefix` -hozzárendelés a **depta** és `suffix` a **-LC** értékre van szabva. Ugyanez a házirend-definíció a különböző részlegekhez tartozó paraméterek eltérő készletével használható, így csökkentve a házirend-definíciók ismétlődését és összetettségét, miközben rugalmasságot biztosít.

## <a name="next-steps"></a>Következő lépések

- A szabályzat- [definíciós struktúra](./definition-structure.md)megismerése.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
