---
title: Metrikai riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával
description: Megtudhatja, hogyan hozhatja létre, tekintheti meg és kezelheti a metrikus riasztási szabályokat a Azure Portal vagy a parancssori felület használatával.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 0b2f70e2727832514f1ce92d1ce0da90f0a6a2e9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038033"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitorral

A metrikus riasztások Azure Monitor lehetővé teszik, hogy értesítést kapjon, ha az egyik mérőszám átlép egy küszöbértéket. A metrikaalapú riasztások többdimenziós platformmetrikák, egyéni metrikák és az Application Insights standard és egyéni metrikáinak széles skálájával működnek. Ebben a cikkben bemutatjuk, hogyan hozhat létre, tekinthet meg és kezelhet metrikus riasztási szabályokat a Azure Portal és az Azure CLI használatával. A metrikus riasztási szabályokat Azure Resource Manager sablonok használatával is létrehozhatja, amelyek [külön cikkben](./alerts-metric-create-templates.md)vannak leírva.

További információ a metrikus riasztások működéséről a [metrikus riasztások áttekintése című](./alerts-metric-overview.md)témakörben olvasható.

## <a name="create-with-azure-portal"></a>Létrehozás az Azure Portalon

Az alábbi eljárás azt ismerteti, hogyan hozhat létre metrikus riasztási szabályt a Azure Portalban:

1. [Azure Portal](https://portal.azure.com)kattintson a **figyelés** elemre. A figyelő panel egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.

2. Kattintson a **riasztások** , majd az **+ új riasztási szabály** elemre.

    > [!TIP]
    > A legtöbb erőforrás-panel a **figyelés** alatt is tartalmaz **riasztásokat** az erőforrás menüjében, és onnan is létrehozhat riasztásokat.

3. Kattintson a **cél kiválasztása** lehetőségre, a betöltött környezet ablaktáblán válassza ki azt a cél erőforrást, amelyre a riasztást szeretné használni. A figyelni kívánt erőforrás megtalálásához használja az **előfizetés** és az **Erőforrás típusa** legördülő listát. Az erőforrást a keresősáv használatával is megkeresheti.

4. Ha a kiválasztott erőforrás metrikákkal rendelkezik, akkor a jobb alsó sarokban található **elérhető jelek** tartalmazzák a metrikákat is. Ebben a [cikkben](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)a metrikus riasztásokhoz támogatott erőforrástípusok teljes listáját tekintheti meg.

5. A cél erőforrás kiválasztása után kattintson a **feltétel hozzáadása** elemre.

6. Megjelenik az erőforrás által támogatott jelek listája, majd válassza ki azt a metrikát, amelyhez riasztást kíván létrehozni.

7. Ekkor megjelenik egy diagram a metrikához az elmúlt hat órában. A **diagram időszakának** legördülő menüjéből kiválaszthatja, hogy a mérőszám hosszabb előzményeit jelenítse meg.

8. Ha a metrika dimenziókat tartalmaz, megjelenik egy megjelenített dimenziós tábla. Jelöljön ki egy vagy több értéket egy dimenzióban.
    - A megjelenített dimenzió értékek az elmúlt nap mérőszám-adatain alapulnak.
    - Ha a keresett dimenzióérték nem jelenik meg, kattintson az "egyéni érték hozzáadása" elemre egyéni dimenzió érték hozzáadásához.
    - A dimenziók **összes aktuális és jövőbeli értékét is kiválaszthatja** . Ez dinamikusan méretezi a kijelölést egy dimenzió összes aktuális és jövőbeli értékére.

    A metrika riasztási szabálya kiértékeli a kiválasztott értékek összes kombinációjának feltételeit. [További információ a többdimenziós metrikák működésével kapcsolatos riasztásokról](./alerts-metric-overview.md).

9. Válassza ki a **küszöbérték** típusát, az **operátort** és az **összesítési típust**. Ez határozza meg azt a logikát, amelyet a metrika riasztási szabálya értékelni fog.
    - Ha **statikus** küszöbértéket használ, folytassa a **küszöbérték** meghatározásával. A metrikai diagram segítségével meghatározhatja, hogy mi lehet az ésszerű küszöbérték.
    - Ha **dinamikus** küszöbértéket használ, folytassa a **küszöbérték érzékenységének** meghatározásával. A metrika diagram a legutóbbi adatok alapján jeleníti meg a számított küszöbértékeket. [További információ a dinamikus küszöbértékek feltételének típusáról és az érzékenységi lehetőségekről](../alerts/alerts-dynamic-thresholds.md).

10. Szükség esetén pontosíthatja a feltételt az **Összesítés részletességének** és **a kiértékelés gyakoriságának** beállításával. 

11. Kattintson a **Kész** gombra.

12. Ha szeretne egy összetett riasztási szabályt figyelni, vegyen fel további feltételeket. A felhasználók jelenleg egyetlen feltételként rendelkezhetnek dinamikus küszöbértékekkel rendelkező riasztási szabályokkal.

13. Adja meg a **riasztás részleteit** , például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.

14. Adjon hozzá egy műveleti csoportot a riasztáshoz egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.

15. Kattintson a **kész** gombra a metrika riasztási szabályának mentéséhez.

> [!NOTE]
> A portálon létrehozott metrikai riasztási szabályok ugyanabban az erőforráscsoporthoz jönnek létre, mint a célként megadott erőforrás.

## <a name="view-and-manage-with-azure-portal"></a>Megtekintés és kezelés a Azure Portal

A metrika riasztási szabályai a riasztások alatt a szabályok kezelése panelen tekinthetők meg és kezelhetők. Az alábbi eljárás bemutatja, hogyan tekintheti meg a metrikus riasztási szabályokat, és szerkesztheti az egyiket.

1. A Azure Portalban navigáljon a **figyeléshez**

2. Kattintson a **riasztások** elemre, és a **szabályok kezelése** lehetőségre

3. A **szabályok kezelése** panelen megtekintheti az összes riasztási szabályt az előfizetések között. A szabályokat az  **erőforráscsoport**, az **Erőforrás típusa** és az **erőforrás** alapján is szűrheti. Ha csak metrikai riasztásokat szeretne látni, válassza a **jel típusa** mérőszámként lehetőséget.

    > [!TIP]
    > A **szabályok kezelése** panelen több riasztási szabályt is kijelölhet, és engedélyezheti vagy tilthatja le őket. Ez akkor lehet hasznos, ha bizonyos cél erőforrásokat a karbantartás alá kell helyezni

4. Kattintson a szerkeszteni kívánt metrikai riasztási szabály nevére

5. A szabály szerkesztése területen kattintson a szerkeszteni kívánt **riasztási feltételekre** . A mérőszámot, a küszöbértéket és az egyéb mezőket szükség szerint módosíthatja

    > [!NOTE]
    > A metrikai riasztás létrehozása után nem szerkesztheti a **cél erőforrás** és a **riasztási szabály nevét** .

6. A módosítások mentéséhez kattintson a **kész** gombra.


## <a name="with-azure-cli"></a>Az Azure CLI-vel

Az előző szakasz ismerteti, hogyan hozhat létre, tekinthet meg és kezelhet metrikus riasztási szabályokat a Azure Portal használatával. Ez a szakasz azt írja le, hogyan végezheti el ugyanezt a platformfüggetlen [Azure CLI](/cli/azure/get-started-with-azure-cli)használatával. Az Azure CLI használatának leggyorsabb módja a [Azure Cloud Shellon](../../cloud-shell/overview.md)keresztül. Ebben a cikkben a Cloud Shell fogjuk használni.

1. Lépjen a Azure Portalra, majd a **Cloud Shell** elemre.

2. A parancssorban a ``--help`` parancsokkal további információkat kaphat a paranccsal és annak használatáról. A következő parancs például megjeleníti a metrikai riasztások létrehozásához, megtekintéséhez és kezeléséhez elérhető parancsok listáját.

    ```azurecli
    az monitor metrics alert --help
    ```

3. Létrehozhat egy egyszerű metrikai riasztási szabályt, amely figyeli, hogy egy virtuális gépen az átlagos CPU-érték nagyobb-e, mint 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Az erőforráscsoporthoz tartozó összes metrikai riasztást az alábbi paranccsal tekintheti meg

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Egy adott metrikai riasztási szabály részleteit a szabály nevével vagy erőforrás-azonosítójával tekintheti meg.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. A metrika riasztási szabályait a következő parancs használatával tilthatja le.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Az alábbi parancs használatával törölheti a metrika riasztási szabályait.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="with-powershell"></a>A PowerShell-lel

A metrikus riasztási szabályokhoz dedikált PowerShell-parancsmagok érhetők el:

- [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2): hozzon létre egy új metrikai riasztási szabályt, vagy frissítsen egy meglévőt.
- [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2): egy vagy több mérőszám riasztási szabályának beolvasása.
- [Remove-AzMetricAlertRuleV2](/powershell/module/az.monitor/remove-azmetricalertrulev2): metrikai riasztási szabály törlése.

## <a name="with-rest-api"></a>A REST API-val

- [Létrehozás vagy frissítés](/rest/api/monitor/metricalerts/createorupdate): hozzon létre egy új metrikai riasztási szabályt, vagy frissítsen egy meglévőt.
- [Get](/rest/api/monitor/metricalerts/get): adott metrikai riasztási szabály beolvasása.
- [Erőforráscsoport szerinti Listázás](/rest/api/monitor/metricalerts/listbyresourcegroup): egy adott erőforráscsoport metrika-riasztási szabályainak listája.
- [Listázás előfizetéssel](/rest/api/monitor/metricalerts/listbysubscription): egy adott előfizetéshez tartozó metrikai riasztási szabályok listájának beolvasása.
- [Frissítés](/rest/api/monitor/metricalerts/update): metrika riasztási szabályának frissítése.
- [Törlés](/rest/api/monitor/metricalerts/delete): metrikai riasztási szabály törlése.

## <a name="next-steps"></a>Következő lépések

- [Metrikus riasztások létrehozása Azure Resource Manager sablonok használatával](./alerts-metric-create-templates.md)
- [A metrikai riasztások működésének ismertetése](./alerts-metric-overview.md)
- [Megtudhatja, hogyan működik a metrikák riasztásai a dinamikus küszöbértékek feltételével](../alerts/alerts-dynamic-thresholds.md)
- [A webes Hook sémájának ismertetése metrikus riasztásokhoz](./alerts-metric-near-real-time.md#payload-schema)
- [A metrikus riasztásokkal kapcsolatos problémák elhárítása](./alerts-troubleshoot-metric.md)