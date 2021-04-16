---
title: 'Rövid útmutató: Az első portállekérdezés'
description: Ebben a rövid útmutatóban az első lekérdezés futtatásához szükséges lépéseket fogja követni a Azure Portal explorer Azure Resource Graph használatával.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 07e336121c6338b27c018acb5b2332653663e1b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533084"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Rövid útmutató: Az első lekérdezés Resource Graph futtatása a Azure Resource Graph Explorerrel

A Azure Resource Graph közvetlenül a Azure Portal érhető el Azure Resource Graph Explorerben. Resource Graph Explorer a lekérdezhető Azure Resource Manager erőforrástípusokról és tulajdonságokról ad tájékoztatást. Resource Graph Explorer egy letisztult felületet is biztosít a több lekérdezéshez, az eredmények értékeléséhez, és akár egyes lekérdezések eredményeinek Azure-irányítópulton rögzítható diagramká konvertálásához.

A rövid útmutató végén az Azure Portal és az Resource Graph Explorer használatával futtatta az első Resource Graph-lekérdezését, és rögzítette az eredményeket egy irányítópulton.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Nyissa meg [Azure Portal,](https://portal.azure.com) és használja a Resource Graph Explorert az alábbi lépésekkel az első lekérdezés Resource Graph futtatásához:

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Resource Graph Explorer lehetőséget.**

1. Az ablak **1.** lekérdezés részében adja meg a lekérdezést, `Resources | project name, type | limit 5` és válassza a Lekérdezés futtatása **lehetőséget.**

   > [!NOTE]
   > Mivel ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint például, a lekérdezés többszöri futtatása valószínűleg kérésenként eltérő `order by` erőforráskészletet fog eredményezni.

1. Tekintse át a lekérdezési választ az **Eredmények lapon.** Válassza az **Üzenetek** lapot a lekérdezés részleteinek, köztük az eredményeknek és a lekérdezés időtartamának a számával együtt. A hibák (ha vannak) ezen a lapon jelennek meg.

1. Frissítse a lekérdezést a `order by` **Name (Név) tulajdonságra:** `Resources | project name, type | limit 5 | order by name asc` . Ezután válassza a **Lekérdezés futtatása lehetőséget.**

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancsrend először korlátozza a lekérdezési eredményeket, majd megrendeli azokat.

1. Frissítse a lekérdezést a `order by` Name (Név) **tulajdonságra,** majd az `limit` első öt találatra: `Resources | project name, type | order by name asc | limit 5` . Ezután válassza a **Lekérdezés futtatása lehetőséget.**

Ha a végső lekérdezés többször is lefut, feltéve, hogy a környezetben semmi sem változik, a visszaadott eredmények konzisztensek, és a **Name** (Név) tulajdonság szerint vannak rendezettek, de továbbra is az első öt eredményre korlátozódnak.

### <a name="schema-browser"></a>Sémaböngésző

A sémaböngésző az Explorer bal oldali Resource Graph található. Az erőforrások ezen listája  az összes olyan Azure-erőforrástípust megjeleníti, amelyet a Azure Resource Graph támogat, és amelyek egy olyan bérlőben léteznek, amelyhez Hozzáféréssel rendelkezik. Az erőforrástípus vagy altulajdonságok kibontása olyan gyermektulajdonságokat mutat, amelyek segítségével létrehozható egy Resource Graph lekérdezés.

Válassza ki az erőforrástípus `where type =="<resource type>"` helyeket a lekérdezési mezőben. A gyermektulajdonságok egyikének kiválasztása `where <propertyName> == "INSERT_VALUE_HERE"` hozzáadja a adatokat a lekérdezési mezőben.
A sémaböngésző nagyszerű módja a lekérdezésekben használható tulajdonságok felderítésének. Az INSERT _\_ VALUE \_ HERE_ értékét cserélje le a saját értékére, módosítsa a lekérdezést feltételekkel, operátorokkal és függvényekkel a kívánt eredmények eléréséhez.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Diagram létrehozása a Resource Graph lekérdezésből

Az előző lekérdezés futtatása után,  ha a Diagramok lapot választja, megjelenik egy üzenet, amely szerint "az eredményhalmaz nem kompatibilis a tortadiagram-vizualizációval". Az eredményeket felsoroló lekérdezéseket nem lehet diagramba tenni, az erőforrások számát megszabadítő lekérdezések viszont igen. A Minta [lekérdezés – Virtuális](./samples/starter.md#count-os)gépek száma operációs rendszertípus szerint használatával hozzunk létre egy vizualizációt a Resource Graph lekérdezésből.

1. Az ablak **1.** lekérdezés részében adja meg a következő lekérdezést, és válassza a **Lekérdezés futtatása lehetőséget.**

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Válassza az **Eredmények lapot,** és figyelje meg, hogy a lekérdezésre adott válasz a counts (darabszámok) lehetőséget adja meg.

1. Válassza a **Diagramok** lapot. A lekérdezés most vizualizációkat ad eredményként. Módosítsa a típust _a Diagram típusának kiválasztása..._ típusról a _Sávdiagram_ vagy a Fánkdiagram típusra, hogy kísérletezzen az elérhető vizualizációs lehetőségekkel. 

## <a name="pin-the-query-visualization-to-a-dashboard"></a>A lekérdezési vizualizáció irányítópulton való kitűzés

Ha egy vizualizációs lekérdezés eredményei vannak, az adatvizualizáció rögzíthető az egyik irányítópulton. Az előző lekérdezés futtatása után kövesse az alábbi lépéseket:

1. Válassza **a Mentés** lehetőséget, és adja meg a "Virtuális gépek operációs rendszer típusa szerint" nevet. Ezután válassza **a jobb** oldali panel alján található Mentés lehetőséget.

1. Válassza **a Lekérdezés futtatása** lehetőséget a lekérdezés mentés után való újrafuttassa.

1. A **Diagramok lapon** válasszon ki egy adatvizualizációt. Ezután válassza **a Rögzítés az irányítópulton lehetőséget.**

1. Válassza ki a megjelenő portálértesítést, vagy válassza az **Irányítópult** lehetőséget a bal oldali panelen.

A lekérdezés most már elérhető az irányítópulton, és a csempe címe megegyezik a lekérdezés nevével. Ha a lekérdezést a rögzítéskor nem mentik, akkor a neve 1. lekérdezés.

A lekérdezés és az eredményül kapott adatvizualizáció minden alkalommal fut és frissül, amikor az irányítópult betöltődik, így valós idejű és dinamikus elemzéseket biztosít az Azure-környezethez közvetlenül a munkafolyamatban.

> [!NOTE]
> A listát eredményező lekérdezések is rögzítheti őket az irányítópulton. A funkció nem korlátozódik a lekérdezések adatvizualizációira.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Példa importálása Resource Graph Explorer-irányítópultokra

Ha példákat Resource Graph a lekérdezésekre, Resource Graph Explorer hogyan használhatja a Azure Portal munkafolyamatának vábbfejlesztett használhatja, próbálja ki ezeket a példa-irányítópultokat.

- [Resource Graph Explorer – Minta irányítópult-#1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Példakép minta irányítópult-#1" lightbox="./media/arge-sample1-large.png":::

- [Resource Graph Explorer – Minta irányítópult-#2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Példakép minta irányítópult-#2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> A fenti irányítópult-képernyőképek száma és diagramjai az Azure-környezettől függően változnak.

1. Válassza ki és töltse le az értékelni kívánt minta-irányítópultot.

1. A Azure Portal bal **oldali panelen válassza** az Irányítópult lehetőséget.

1. Válassza **a Feltöltés** lehetőséget, majd keresse meg és válassza ki a letöltött irányítópult-mintafájlt. Ezután válassza a **Megnyitás lehetőséget.**

Az importált irányítópult automatikusan megjelenik. Mivel most már létezik a Azure Portal, szükség szerint felfedezheti és módosíthatja őket, vagy új irányítópultokat hozhat létre a példából a csapatokkal való megosztáshoz. További információ az irányítópultok használatával kapcsolatban: Irányítópultok létrehozása és megosztása [a Azure Portal.](../../azure-portal/azure-portal-dashboards.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a minta-Resource Graph irányítópultokat a Azure Portal környezetből, azt a következő lépésekkel használhatja:

1. A **bal oldali** panelen válassza az Irányítópult lehetőséget.

1. Az irányítópult legördülő menüben válassza ki a törölni Resource Graph törölni kívánt irányítópult mintáját.

1. Válassza **a Törlés** lehetőséget az irányítópult tetején található irányítópult menüjében, majd kattintson az **OK** gombra a megerősítéshez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az Azure Resource Graph Explorerrel futtatta az első lekérdezését, és a lekérdezések által működtetett irányítópult-példákat Resource Graph. Ha többet szeretne megtudni a Resource Graph nyelvről, folytassa a lekérdezési nyelv részleteit tartalmazó oldalon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
