---
title: A szabályzat-hozzárendelési struktúra részletei
description: A szabályzatdefiníciók és -paraméterek kiértékelési erőforrásokhoz Azure Policy által használt szabályzat-hozzárendelési definíciót ismerteti.
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 9de210b17264330e79ab5978a449e7a494054be2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535871"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-hozzárendelés struktúrája

A szabályzat-hozzárendeléseket a Azure Policy határozza meg, mely erőforrásokhoz mely szabályzatok vagy kezdeményezések vannak hozzárendelve. A szabályzat-hozzárendelés képes meghatározni az adott erőforráscsoport paramétereinek értékeit a hozzárendeléskor, így újból felhasználhatók azok a szabályzatdefiníciók, amelyek ugyanazokra az erőforrás-tulajdonságokra vonatkoznak különböző megfelelőségi igényekkel.

A JSON használatával hozhat létre szabályzat-hozzárendelést. A szabályzat-hozzárendelés a következő elemeket tartalmazza:

- megjelenített név
- leírás
- metaadatok
- kényszerítési mód
- kizárt hatókörök
- szabályzatdefiníció
- meg nem felelés esetén kapott üzenetek
- parameters

Az alábbi JSON-ban például egy _DoNotEnforce_ módban, dinamikus paraméterekkel megadott szabályzat-hozzárendelés látható:

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

Minden Azure Policy minta a [Azure Policy van.](../samples/index.md)

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **displayName és** **a description paraméter használatával** azonosíthatja a szabályzat-hozzárendelést, és kontextust nyújthat az adott erőforráskészlethez való használathoz. **A displayName** legfeljebb _128_ karakterből állhat, és **legfeljebb** _512_ karakterből állhat.

## <a name="metadata"></a>Metaadatok

A választható `metadata` tulajdonság a szabályzat-hozzárendelés adatait tárolja. Az ügyfelek bármilyen, a szervezet számára hasznos tulajdonságot és értéket meghatároznak a `metadata` -ban. Van azonban néhány gyakori _tulajdonság,_ amelyet a Azure Policy. Minden `metadata` tulajdonság legfeljebb 1024 karakterből állhat.

### <a name="common-metadata-properties"></a>Gyakori metaadat-tulajdonságok

- `assignedBy` (sztring): A hozzárendelést létrehozó rendszerbiztonsági tag rövid neve.
- `createdBy` (sztring): A hozzárendelést létrehozó rendszerbiztonsági tag GUID-ja.
- `createdOn` (sztring): A hozzárendelés létrehozási ideje univerzális ISO 8601 DateTime formátuma.
- `parameterScopes`(object): Kulcs-érték párok gyűjteménye, ahol a kulcs megegyezik egy strongType konfigurált paraméter nevével, és az érték határozza meg a Portalon az elérhető erőforrások listájának a [strongType](./definition-structure.md#strongtype) értékkel való egyeztetéséhez használt erőforrás-hatókört.  A portál akkor állítja be ezt az értéket, ha a hatókör eltér a hozzárendelés hatókörtől. Ha be van állítva, a portálon a szabályzat-hozzárendelés szerkesztése automatikusan erre az értékre állítja a paraméter hatókörét. A hatókör azonban nincs az értékhez zárolva, és módosítható egy másik hatókörre.

  A következő példája `parameterScopes` egy **backupPolicyId** nevű _strongType_ paraméterre mutat, amely beállítja az erőforrás-kiválasztási hatókört, amikor a hozzárendelést szerkesztik a portálon.

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (sztring): Annak a rendszerbiztonsági tagnak a rövid neve, amely frissítette a hozzárendelést, ha van ilyen.
- `updatedOn` (sztring): A hozzárendelés frissítési ideje univerzális ISO 8601 DateTime formátuma, ha van ilyen.

## <a name="enforcement-mode"></a>Kényszerítési mód

A **enforcementMode** tulajdonság lehetővé teszi az ügyfelek számára, hogy teszteljék a szabályzat eredményét a meglévő erőforrásokon anélkül, hogy elindítanák a szabályzat hatását, vagy bejegyzéseket aktiválnak az [Azure-tevékenységnaplóban.](../../../azure-monitor/essentials/platform-logs-overview.md) Ezt a forgatókönyvet gyakran "What If" néven is nevezik, és a biztonságos üzembe helyezési eljárásokhoz igazodik. **A enforcementMode** különbözik a [Disabled](./effects.md#disabled) hatástól, mivel ez a hatás megakadályozza, hogy az erőforrások kiértékelése egyáltalán megtörténjen.

Ez a tulajdonság a következő értékekkel rendelkezik:

|Mód |JSON-érték |Típus |Manuális szervizek |Tevékenységnapló bejegyzése |Leírás |
|-|-|-|-|-|-|
|Engedélyezve |Alapértelmezett |sztring |Igen |Igen |A szabályzat hatása az erőforrás létrehozása vagy frissítése során lép érvénybe. |
|Disabled (Letiltva) |DoNotEnforce |sztring |Igen |Nem | A szabályzat hatása nem lesz kényszerítve az erőforrás létrehozása vagy frissítése során. |

Ha **a enforcementMode** nincs megadva egy szabályzat- vagy kezdeményezési definícióban, a rendszer az _Alapértelmezett_ értéket használja. [A szervizelési feladatok](../how-to/remediate-resources.md) akkor is elindíthatóak a [deployIfNotExists](./effects.md#deployifnotexists) szabályzatok esetén, ha a **enforcementMode** _beállítása DoNotEnforce._

## <a name="excluded-scopes"></a>Kizárt hatókörök

A **hozzárendelés** hatóköre magában foglalja az összes gyermekerőforrás-tárolót és gyermekerőforrást. Ha egy gyermekerőforrás-tároló vagy gyermekerőforrás definíciója  nem alkalmazható, mindegyik kizárható a kiértékelésből a **notScopes beállításával.** Ez a tulajdonság egy tömb, amely lehetővé teszi egy vagy több erőforrástároló vagy erőforrás kizárását a kiértékelésből. **A notScopes** a kezdeti hozzárendelés létrehozása után is hozzáadható vagy frissíthető.

> [!NOTE]
> A _kizárt_ erőforrások eltérnek a _kivétel alá képező erőforrásoktól._ További információ: A hatókör [Azure Policy.](./scope.md)

## <a name="policy-definition-id"></a>Szabályzatdefiníció azonosítója

Ennek a mezőnek egy szabályzatdefiníció vagy egy kezdeményezési definíció teljes elérési útjának kell lennie.
`policyDefinitionId` A nem tömb, csak sztring. Javasoljuk, hogy ha gyakran több szabályzatot rendelnek egymáshoz, akkor inkább egy [kezdeményezést](./initiative-definition-structure.md) használjon.

## <a name="non-compliance-messages"></a>Meg nem felelésről küldött üzenetek

Egyéni üzenet beállításához, amely leírja, hogy egy erőforrás miért nem felel meg a szabályzat- vagy kezdeményezési definíciónak, állítsa be a hozzárendelés `nonComplianceMessages` definíciójában. Ez a csomópont bejegyzések `message` tömbje. Ez az egyéni üzenet a meg nem felelés esetén az alapértelmezett hibaüzeneten kívül jelenik meg, és nem kötelező.

> [!IMPORTANT]
> A meg nem felelés esetén az egyéni üzenetek csak az olyan definíciók vagy kezdeményezések esetében támogatottak, amelyek Resource Manager [móddefiníciókat.](./definition-structure.md#resource-manager-modes)

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Ha a hozzárendelés egy kezdeményezéshez van hozzárendelve, különböző üzenetek konfigurálhatóak a kezdeményezés egyes szabályzatdefinícióihoz. Az üzenetek a `policyDefinitionReferenceId` kezdeményezési definícióban konfigurált értéket használják. Részletekért lásd [a szabályzatdefiníciók tulajdonságait.](./initiative-definition-structure.md#policy-definition-properties)

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

A szabályzat-hozzárendelés ezen szegmense a szabályzatdefinícióban vagy kezdeményezési definícióban meghatározott paraméterek [értékeit biztosítja.](./definition-structure.md#parameters) Ez a kialakítás lehetővé teszi a szabályzatok vagy kezdeményezési definíciók újbóli felhasználását különböző erőforrásokkal, de különböző üzleti értékeket vagy eredményeket keresnek.

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

Ebben a példában a szabályzatdefinícióban korábban definiált paraméterek és `prefix` `suffix` . Ez az adott szabályzat-hozzárendelés `prefix` **DeptA és** `suffix` **-LC lesz.** Ugyanaz a szabályzatdefiníció újrahasználható egy másik részleg különböző paramétereivel, ami csökkenti a szabályzatdefiníciók duplikálását és összetettségét, ugyanakkor rugalmasságot biztosít.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [szabályzatdefiníciók struktúrájáról.](./definition-structure.md)
- A szabályzatok [programozott módon történő létrehozása.](../how-to/programmatically-create.md)
- Megtudhatja, hogyan [olvashatja be a megfelelőségi adatokat.](../how-to/get-compliance-data.md)
- Ismerje meg, [hogyan szervizelődnek a nem megfelelő erőforrások.](../how-to/remediate-resources.md)
- Tekintse át a felügyeleti csoportokat az [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal való használatával.](../../management-groups/overview.md)
