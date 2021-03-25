---
title: Metrikus riasztások konfigurálása – Azure Portal-Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a cikk bemutatja, hogyan konfigurálhat és érhet el metrikus riasztásokat Azure Database for MySQL rugalmas kiszolgálóhoz a Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: be52bbe58f6c2ff6ee21703860e8d8e00ec30072
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110151"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL-rugalmas kiszolgáló metrikáinak beállítása a Azure Portal használatával 

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan állíthat be Azure Database for MySQL riasztásokat a Azure Portal használatával. Riasztást az Azure-szolgáltatások metrikáinak monitorozása alapján kaphat.

A riasztás akkor aktiválódik, ha egy megadott metrika értéke átlépi a hozzárendelt küszöbértéket. A riasztás akkor is aktiválódik, ha a feltétel először teljesül, majd ezt követően, ha a feltétel már nem teljesül. A metrikai riasztások állapota, azaz csak akkor küldenek értesítéseket, ha az állapot megváltozik.

A következő műveletek elvégzéséhez beállíthatja a riasztást:
* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a társ-rendszergazdáknak
* Küldjön e-mailt a megadott további e-mailekre.
* Webhook meghívása

A riasztási szabályokkal kapcsolatos információkat a használatával konfigurálhatja és kérheti le:
* [Azure Portal](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Metrikához tartozó riasztási szabály létrehozása az Azure Portalon
1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt Azure Database for MySQL rugalmas kiszolgálót.
2. Az oldalsáv **figyelés** szakaszában válassza a **riasztások** lehetőséget.
3. Válassza a **+ Új riasztási szabály** lehetőséget.
4. Megnyílik a **Szabály létrehozása** lap. Adja meg a kötelező adatokat:
5. A **feltétel** szakaszban válassza a **feltétel kiválasztása** lehetőséget.
6. Megjelenik a támogatott jelek listája, majd válassza ki azt a metrikát, amelyhez riasztást szeretne létrehozni. Válassza például a "tárolási százalék" lehetőséget.
7. Ekkor megjelenik egy diagram a metrikához az elmúlt hat órában. A **diagram időszakának** legördülő menüjéből kiválaszthatja, hogy a mérőszám hosszabb előzményeit jelenítse meg.
8. Válassza ki a **küszöbérték** típusát (pl. "Statikus" vagy "dinamikus"), **operátor** (pl. "Nagyobb, mint") és **aggregációs típus** (pl. átlag). Ez határozza meg azt a logikát, amelyet a metrika riasztási szabálya értékelni fog.
    - Ha **statikus** küszöbértéket használ, folytassa a **küszöbérték** meghatározásával (pl. 85 százalék). A metrikai diagram segítségével meghatározhatja, hogy mi lehet az ésszerű küszöbérték.
    - Ha **dinamikus** küszöbértéket használ, folytassa a **küszöbérték érzékenységének** meghatározásával. A metrika diagram a legutóbbi adatok alapján jeleníti meg a számított küszöbértékeket. [További információ a dinamikus küszöbértékek feltételének típusáról és az érzékenységi lehetőségekről](../../azure-monitor/alerts/alerts-dynamic-thresholds.md).
9. Pontosítsa a feltételt úgy, hogy módosítja az **összesítési részletességi (időszak)** intervallumot, amely fölé az adatpontok az aggregációs típus függvény használatával vannak csoportosítva (pl. "30 perc") és **gyakoriság** (ex "15 percenként").
10. Kattintson a **Kész** gombra.
11. Műveleti csoport hozzáadása. A műveleti csoport az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye. A **műveleti csoportok** szakaszban válassza a **műveleti csoport kiválasztása** lehetőséget egy már létező, a riasztási szabályhoz csatolni kívánt művelet kiválasztásához.
12. Létrehozhat egy új műveleti csoportot is, hogy értesítést kapjon a riasztásról. További információt a [műveleti csoport létrehozása és kezelése](../../azure-monitor/alerts/action-groups.md) című témakörben talál.
13. Új műveleti csoport létrehozásához válassza a **+ műveleti csoport létrehozása** lehetőséget. Töltse ki a "Create Action Group" (művelet létrehozása) űrlapot egy **előfizetéssel**, egy **erőforráscsoporthoz**, egy **műveleti csoport nevével** és a **megjelenített névvel**.
14. A műveleti csoport **értesítéseinek** konfigurálása.
    
    Az **értesítés típusa** területen válassza az "e-mail-Azure Resource Manager szerepkör" lehetőséget az előfizetés tulajdonosainak, közreműködőknek és olvasóknak az értesítések fogadásához. Válassza ki a **Azure Resource Manager szerepkört** az e-mail elküldéséhez.
    Az **e-mailek/SMS-üzenetek/leküldés/hang** lehetőséggel is választhatja az értesítések küldését adott címzetteknek.

    Adja meg az értesítési típus **nevét** , majd válassza a **felülvizsgálat + létrehozás** lehetőséget, ha elkészült.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Adja meg a **riasztási szabály részleteit** , például a **riasztási szabály nevét**, **leírását**, **a riasztási szabály mentését az erőforráscsoporthoz és a** **súlyosságra**.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. A riasztás létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.
    Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.
## <a name="manage-your-alerts"></a>A riasztások kezelése
Miután létrehozta a riasztást, kiválaszthatja, és elvégezheti a következő műveleteket:

* Megtekintheti a metrika küszöbértékét és a riasztásra vonatkozó előző naptól számított tényleges értékeket bemutató diagramot.
* A riasztási szabály **szerkesztése** vagy **törlése** .
* **Tiltsa le** vagy **engedélyezze** a riasztást, ha átmenetileg le kívánja állítani vagy folytatni szeretné az értesítések fogadását.


## <a name="next-steps"></a>Következő lépések
- További információ a [riasztások metrikák beállításáról](../../azure-monitor/alerts/alerts-metric.md).
- További információ a [Azure Database for MySQL rugalmas kiszolgáló rendelkezésre álló metrikáinak](./concepts-monitoring.md)használatáról.
- [A metrikariasztások működése az Azure Monitorban](../../azure-monitor/alerts/alerts-metric-overview.md)