---
title: A nemmegfelelőség okainak meghatározása
description: Ha egy erőforrás nem megfelelő, számos lehetséges oka lehet. Ismerje meg, hogy mi okozta a meg nem felelés okait.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: a8168bf22aceaf5cbdec4b1346801aa62b7aa4ee
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439833"
---
# <a name="determine-causes-of-non-compliance"></a>A nemmegfelelőség okainak meghatározása

Ha egy Azure-erőforrás úgy van meghatározva, hogy nem felel meg egy házirend-szabálynak, érdemes megismerni, hogy az erőforrás melyik része nem felel meg a szabálynak. Azt is érdemes megismerni, hogy milyen változás lett módosítva egy korábban megfelelő erőforrásnak, hogy az nem megfelelő legyen. Ezt az információt kétféleképpen lehet megkeresni:

- [Megfelelőség részletei](#compliance-details)
- [Változási előzmények (előzetes verzió)](#change-history)

## <a name="compliance-details"></a>Megfelelőség részletei

Ha egy erőforrás nem megfelelő, az adott erőforrás megfelelőségi adatai a **szabályzat megfelelőségi** lapján érhetők el. A megfelelőség részleteit tartalmazó ablaktábla a következő információkat tartalmazza:

- Erőforrás részletei, például név, típus, hely és erőforrás-azonosító
- Az aktuális szabályzat-hozzárendelés utolsó kiértékelésének megfelelőségi állapota és időbélyege
- Az erőforrás nem megfelelőségi _okainak_ listája

> [!IMPORTANT]
> Mivel a _nem megfelelő_ erőforrások megfelelőségi adatai az adott erőforrás tulajdonságainak aktuális értékét jelenítik meg, a felhasználónak **olvasási** művelettel kell rendelkeznie az erőforrás **típusához** . Ha például a _nem megfelelő_ erőforrás a **Microsoft. számítás/virtualMachines** , akkor a felhasználónak rendelkeznie kell a **Microsoft. számítási/virtualMachines/olvasási** művelettel. Ha a felhasználó nem rendelkezik a szükséges művelettel, a rendszer hozzáférési hibaüzenetet jelenít meg.

A megfelelőségi adatok megtekintéséhez kövesse az alábbi lépéseket:

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal a **minden szolgáltatás** lehetőség kiválasztásával, majd a **szabályzat** keresésével és kiválasztásával.

1. Az **Áttekintés** vagy **megfelelőség** lapon válassza ki a szabályzatot olyan **megfelelőségi állapotban** , amely _nem megfelelő_.

1. A **szabályzat megfelelősége** lap **erőforrás-megfelelőség** lapján kattintson a jobb gombbal, vagy válassza ki az erőforrás három pontját egy olyan **megfelelőségi állapotban** , amely _nem megfelelő_. Ezután válassza a **megfelelőségi adatok megtekintése** lehetőséget.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Képernyőkép a &quot;megfelelőségi adatok megtekintése&quot; hivatkozásra az erőforrás-megfelelőség lapon." border="false":::

1. A **megfelelőség részletei** ablaktábla az erőforrás legutóbbi kiértékelésével kapcsolatos információkat jeleníti meg az aktuális szabályzat-hozzárendeléshez. Ebben a példában a **Microsoft. SQL/Servers/Version** mező értéke _12,0_ , míg a rendszer a _14,0_-es szabályzat-definíciót várta. Ha az erőforrás több okból nem megfelelő, az egyes elemek a panelen jelennek meg.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Képernyőkép a megfelelőségi részletek panelről és a nem megfelelőségi okokról, amelyek a jelenlegi érték tizenkét, a célként megadott érték pedig tizennégy." border="false":::

   **AuditIfNotExists** vagy **deployIfNotExists** házirend-definíció esetén a részletek között szerepel a **részletek. Type** tulajdonság és a nem kötelező tulajdonságok. A listában tekintse meg a [auditIfNotExists tulajdonságai](../concepts/effects.md#auditifnotexists-properties) és a [deployIfNotExists tulajdonságai](../concepts/effects.md#deployifnotexists-properties)című témakört. Az **utolsó kiértékelt erőforrás** a definíció **részletek** szakaszában található kapcsolódó erőforrás.

   Példa részleges **deployIfNotExists** -definícióra:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Képernyőkép a megfelelőségi részletek panelről ifNotExists, beleértve az értékelt erőforrások számát." border="false":::

> [!NOTE]
> Az adatvédelemhez, ha egy tulajdonság értéke _titkos_ , a jelenlegi érték csillagokat jelenít meg.

Ezek a részletek ismertetik, hogy egy adott erőforrás miért nem megfelelő, de ne jelenjen meg, ha az erőforrás változása miatt nem megfelelővé vált. Ebben az esetben tekintse meg az alábbi [változások előzményeit (előzetes verzió)](#change-history) .

### <a name="compliance-reasons"></a>Megfelelőségi okok

A következő mátrix minden lehetséges _okot_ leképez a szabályzat-definícióban szereplő felelős [feltételre](../concepts/definition-structure.md#conditions) :

|Ok | Feltétel |
|-|-|
|A jelenlegi értéknek a célként megadott értéket kell tartalmaznia kulcsként. |containsKey vagy **nem** notContainsKey |
|A jelenlegi értéknek tartalmaznia kell a célérték értékét. |notContains tartalmaz vagy **nem** |
|A jelenlegi értéknek meg kell egyeznie a célként megadott értékkel. |egyenlő vagy **nem** notEquals |
|A jelenlegi értéknek a célként megadott értéknél kisebbnek kell lennie. |kevesebb vagy **nem** greaterOrEquals |
|A jelenlegi értéknek nagyobbnak vagy egyenlőnek kell lennie a célként megadott értékkel. |greaterOrEquals vagy **nem** kevesebb |
|A jelenlegi értéknek nagyobbnak kell lennie a célként megadott értéknél. |nagyobb vagy **nem** lessOrEquals |
|A jelenlegi érték nem lehet kisebb a célként megadott értéknél. |lessOrEquals vagy **nem** nagyobb |
|A jelenlegi értéknek léteznie kell. |létezik |
|A jelenlegi értéknek a célként megadott értéknek kell lennie. |vagy **nem** notIn |
|A jelenlegi értéknek a célként megadott értéknek kell lennie. |hasonló vagy **nem** notLike |
|A jelenlegi értéknek a kis-és nagybetűk megkülönböztetésével egyezőnek kell lennie. |egyezés vagy **nem** notMatch |
|A jelenlegi értéknek a kis-és nagybetűk megkülönböztetésével egyeznie kell a célérték értékével. |matchInsensitively vagy **nem** notMatchInsensitively |
|A jelenlegi érték nem tartalmazhatja a célként megadott értéket kulcsként. |notContainsKey vagy **nem** containsKey|
|A jelenlegi érték nem tartalmazhatja a célérték értékét. |notContains vagy **nem** tartalmaz |
|A jelenlegi érték nem lehet egyenlő a célként megadott értékkel. |notEquals vagy **nem** egyenlő |
|A jelenlegi érték nem lehet létező. |**nem** létezik  |
|A jelenlegi érték nem lehet a célként megadott értékben. |notIn vagy **nem** |
|A jelenlegi érték nem lehet a célként megadott értékhez hasonló. |notLike vagy **nem** hasonló |
|A jelenlegi érték nem lehet kis-és nagybetűk megkülönböztetése a célként megadott értékkel. |notMatch vagy **nem** egyezik |
|A jelenlegi érték nem lehet kis-és nagybetűk megkülönböztetése a célként megadott értékkel. |notMatchInsensitively vagy **nem** matchInsensitively |
|Egyetlen kapcsolódó erőforrás sem felel meg a szabályzat-definícióban szereplő hatás részleteinek. |A **. details. Type** és a Policy szabály **IF** részében definiált erőforráshoz kapcsolódó erőforrás nem létezik. |

## <a name="component-details-for-resource-provider-modes"></a>Az erőforrás-szolgáltatói módok összetevő részletei

[Erőforrás-szolgáltatói móddal](../concepts/definition-structure.md#resource-manager-modes)rendelkező hozzárendelések esetén válassza a _nem megfelelő_ erőforrást a mélyebb nézet megnyitásához. Az **összetevő megfelelősége** lapon további információk szerepelnek az erőforrás-szolgáltatói üzemmódra vonatkozóan a hozzárendelt szabályzatban, amely a _nem megfelelő_ **összetevőt** és **összetevő-azonosítót** mutatja.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Képernyőkép az összetevő-megfelelőség lapról, valamint az erőforrás-szolgáltatói mód hozzárendelésének megfelelőségi részleteiről." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Vendégkonfiguráció megfelelőségi adatai

A _auditIfNotExists_ házirendek a _vendég konfiguráció_ kategóriában több beállítást is kiértékeltek a virtuális gépen belül, és meg kell tekintenie egy beállítás részleteit. Ha például a rendszer naplózza a jelszóházirend listáját, és csak az egyikük állapota _nem megfelelő_, akkor tudnia kell, hogy mely konkrét jelszóházirend-szabályzatok nem felelnek meg az előírásoknak, és miért.

Előfordulhat, hogy nem fér hozzá közvetlenül a virtuális géphez, de jelentenie kell, hogy a virtuális gép miért _nem megfelelő_.

### <a name="azure-portal"></a>Azure Portal

A szabályzat megfelelőségi részleteinek megtekintéséhez kövesse a fenti szakasz lépéseit.

A megfelelőség részletei ablaktáblán válassza az **utoljára kiértékelt erőforrás** hivatkozását.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Képernyőkép a auditIfNotExists-definíció megfelelőségi részleteinek megtekintéséhez." border="false":::

A **vendég-hozzárendelés** lap megjeleníti az összes elérhető megfelelőségi részletet. A nézet minden sora a gépen belül végrehajtott értékelést jelképezi. Az **OK** oszlopban megjelenik egy kifejezés, amely leírja, hogy a vendég-hozzárendelés miért _nem megfelelő_. Ha például a jelszóházirend naplózása, az **OK** oszlopban az egyes beállítások aktuális értéke is megjelenik.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Képernyőkép a vendég-hozzárendelés megfelelőségi részleteiről." border="false":::

## <a name="change-history-preview"></a><a name="change-history"></a>Változási előzmények (előzetes verzió)

Egy új **nyilvános előzetes** verzió részeként az utolsó 14 nap változási előzményei minden olyan Azure-erőforráshoz elérhetők, amely támogatja a [teljes módú törlést](../../../azure-resource-manager/templates/complete-mode-deletion.md). A változási előzmények részletesen ismertetik a változás észlelését és az egyes változtatások _vizuális_ eltéréseit. A változás észlelése akkor aktiválódik, ha a Azure Resource Manager tulajdonságok hozzáadása, eltávolítása vagy módosítása történik.

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal a **minden szolgáltatás** lehetőség kiválasztásával, majd a **szabályzat** keresésével és kiválasztásával.

1. Az **Áttekintés** vagy **megfelelőség** lapon válassza ki a szabályzatot bármilyen **megfelelőségi állapotban**.

1. A **szabályzat megfelelősége** lap **erőforrás-megfelelőség** lapján válasszon ki egy erőforrást.

1. Válassza az **erőforrás-megfelelőség** lapon az **Előzmények módosítása (előzetes verzió)** lapot. Megjelenik az észlelt módosítások listája, ha vannak ilyenek.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Képernyőkép a változások előzményei lapról, valamint az erőforrás-megfelelőségi lapon észlelt változási időkről." border="false":::

1. Válassza ki az észlelt módosítások egyikét. Az erőforráshoz tartozó _vizualizációs diff_ a **változási előzmények** lapon jelenik meg.

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Képernyőfelvétel a korábbi és a korábbi tulajdonságok állapotának változási előzményeiről a korábbi változások lapon." border="false":::

A _vizualizációs diff_ segédek egy erőforrás változásainak azonosításához. Előfordulhat, hogy az észlelt változások nem kapcsolódnak az erőforrás aktuális megfelelőségi állapotához.

Az előzmények módosításait az [Azure Resource Graph](../../resource-graph/overview.md)biztosíthatja. A Azure Portalon kívüli adatok lekérdezéséhez lásd: [erőforrás-módosítások beolvasása](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
