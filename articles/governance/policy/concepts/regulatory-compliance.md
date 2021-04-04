---
title: Szabályozási megfelelőség a kezdeményezési definíciókban
description: Ismerteti, hogyan használhatók a házirendek a szabályzatok szabályozási tartományon, például a Access Control, a konfiguráció felügyeletében és másokon történő csoportosításához.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 15b03b5a40cba1290859b02883df70e3df54c5cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89645520"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Jogszabályoknak való megfelelés Azure Policy

A Azure Policy szabályozási megfelelősége beépített kezdeményezési definíciókat tartalmaz az **ellenőrzések** és **megfelelőségi tartományok** (_ügyfél_, _Microsoft_, _megosztott_) alapján történő megtekintéséhez.
A Microsoft által felügyelt ellenőrzésekhez a harmadik féltől származó igazolások alapján további részleteket biztosítunk a naplózási eredményekről, valamint a megvalósítás részleteit a megfelelőség érdekében.
A Microsoft által felelős ellenőrzések `type` [statikusak](./definition-structure.md#type).

> [!NOTE]
> A szabályozás megfelelősége egy előzetes verziójú funkció. A frissített beépített modulok esetében a kezdeményezések a megfelelő megfelelőségi szabványhoz való leképezést vezérlik. A meglévő megfelelőségi standard kezdeményezések a szabályozásnak való megfelelést támogató folyamatban vannak.

## <a name="regulatory-compliance-defined"></a>Szabályozási megfelelőség definiálva

A szabályozás megfelelősége egy kezdeményezési definíció [csoportosítási](./initiative-definition-structure.md#policy-definition-groups) részére épül. A beépített modulokban a kezdeményezési definícióban szereplő egyes csoportok egy nevet (**vezérlőelemet**), egy kategóriát (**megfelelőségi tartományt**) határoznak meg, és olyan hivatkozást biztosítanak a [policyMetadata](./initiative-definition-structure.md#metadata-objects) objektumra, amely az adott **vezérlőre** vonatkozó információkat tartalmaz. A szabályzatok megfelelőségi kezdeményezésének definíciójában a `category` tulajdonságot **szabályozási megfelelőségre** kell beállítani. A standard szintű kezdeményezési definíciók szerint a szabályozási megfelelőségi kezdeményezések a dinamikus hozzárendelések létrehozásához is támogatják a [paramétereket](./initiative-definition-structure.md#parameters) .

Az ügyfelek létrehozhatják saját szabályozási megfelelőségi kezdeményezéseiket. Ezek a definíciók lehetnek eredetiek, vagy a meglévő beépített definíciók alapján másolhatók. Ha beépített szabályozási megfelelőségi kezdeményezési javaslattal rendelkezik referenciáként, javasoljuk, hogy figyelje a szabályozási megfelelőségi definíciók forrását a [Azure Policy GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)-tárházban.

Ha egyéni szabályozási megfelelőségi kezdeményezést szeretne összekapcsolni a Azure Security Center irányítópulttal, tekintse meg a [Azure Security Center egyéni biztonsági házirendek használatával](../../../security-center/custom-security-policies.md)című témakört.

## <a name="regulatory-compliance-in-portal"></a>Szabályozási megfelelőség a portálon

Ha egy kezdeményezési definíciót [csoportokkal](./initiative-definition-structure.md#policy-definition-groups)hoztak létre, akkor az adott kezdeményezéshez tartozó portál **megfelelőségi** adatok lapja további információkat tartalmaz. 

Új lapon a **vezérlők** hozzáadódnak a laphoz. A szűrést a **megfelelőségi tartomány** és a házirend-definíciók a `title` **policyMetadata** objektum mezői szerint csoportosítják. Minden sor egy olyan **vezérlőt** jelöl, amely megjeleníti a megfelelőségi állapotot, a **megfelelőségi tartományt** , a felelősségi adatokat, valamint azt, hogy hány nem megfelelő és megfelelő szabályzat-definíció alkotja ezt a **vezérlőt**.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="Képernyőkép a megfelelőségi és nem megfelelő vezérlőket megjelenítő, a NIST SP 800-53 R4 beépített definíciójának szabályozásáról.":::

A **vezérlő** kiválasztásával megnyílik egy oldal, amely a vezérlő részleteit ismerteti. Az **Áttekintés** a és a adatait `description` tartalmazza `requirements` . A **szabályzatok** lapon az összes olyan házirend-definíció szerepel a kezdeményezésben, amely hozzájárul ehhez a **vezérlőhöz**. Az **erőforrás-megfelelőség** lapon részletesen áttekintheti az összes olyan erőforrást, amelyet a jelenleg megtekintett **vezérlő** egyik tagja értékel ki.

> [!NOTE]
> A **Microsoft által felügyelt** kiértékelés típusa [statikus](./definition-structure.md#type) házirend-definíció `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="Képernyőfelvétel: a NIST SP 800-53 R4 beépített definíciójának határos védelmi szabályozásának szabályozási megfelelőségi részletei.":::

Ugyanabból a **vezérlőből** az **erőforrás-megfelelőség** lapra való váltáskor az összes olyan erőforrás látható, amely a **vezérlő** házirend-definíciói közé tartozik. A szűrők a név vagy az azonosító, a megfelelőségi állapot, az erőforrás típusa és a hely számára érhetők el.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="Képernyőkép az erőforrás-megfelelőségről a NIST SP 800-53 R4 beépített definíciójának határos védelmi vezérlőjével.":::

## <a name="regulatory-compliance-in-sdk"></a>Szabályozási megfelelőség az SDK-ban

Ha a szabályozás megfelelősége engedélyezve van egy kezdeményezési definícióban, a kiértékelési vizsgálati rekord, az események és a házirend állapota SDK minden további tulajdonságot ad vissza. Ezek a további tulajdonságok megfelelőségi állapot szerint vannak csoportosítva, és információt nyújtanak arról, hogy hány csoport van az egyes állapotokban.

A következő kód példa a hívás eredményeinek hozzáadására `summarize` :

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [kezdeményezési definíció szerkezetét](./initiative-definition-structure.md)
- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatok hatásainak ismertetése](./effects.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
