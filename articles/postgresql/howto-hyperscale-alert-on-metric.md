---
title: Riasztások konfigurálása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ez a cikk bemutatja, hogyan konfigurálhat és érhet el metrikus riasztásokat Azure Database for PostgreSQL-nagy kapacitású (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: f5557140d77865a6d4c44316cecd512f877736e0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577087"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>A Azure Database for PostgreSQL-nagy kapacitású (Citus) metrikáinak beállítása a Azure Portal használatával

Ez a cikk bemutatja, hogyan állíthat be Azure Database for PostgreSQL riasztásokat a Azure Portal használatával. Riasztást az Azure-szolgáltatások [metrikáinak monitorozása](concepts-hyperscale-monitoring.md) alapján kaphat.

A rendszer riasztást állít be, amely akkor aktiválódik, ha egy adott metrika értéke átlép egy küszöbértéket. A riasztás akkor aktiválódik, ha a feltétel először teljesül, és ezt követően folytatódik.

A következő műveletek elvégzéséhez beállíthatja a riasztást:
* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a rendszergazdáknak.
* Küldjön e-mailt a megadott további e-mailekre.
* Hívja meg a webhookot.

A riasztási szabályokkal kapcsolatos információkat a használatával konfigurálhatja és kérheti le:
* [Azure Portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Metrikához tartozó riasztási szabály létrehozása az Azure Portalon
1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt Azure Database for PostgreSQL-kiszolgálót.

2. Az oldalsáv **figyelés** szakaszában válassza a **riasztások** lehetőséget az alábbiak szerint:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="Riasztási szabályok kiválasztása":::

3. Válassza az **új riasztási szabály** (+ ikon) lehetőséget.

4. Megnyílik a **szabály létrehozása** lap az alább látható módon. Adja meg a kötelező adatokat:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Metrikus riasztási űrlap hozzáadása":::

5. A **feltétel** szakaszban válassza a **Hozzáadás** lehetőséget.

6. Válasszon ki egy mérőszámot azon jelek listájáról, amelyekről riasztást szeretne kapni. Ebben a példában válassza a "tárolási százalék" lehetőséget.
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Képernyőfelvétel: a jel logikai beállítása oldal, ahol több jel is megtekinthető.":::

7. A riasztás logikájának konfigurálása:

    * **Operátor** (pl. "Nagyobb, mint")
    * **Küszöbérték** (pl. 85 százalék)
    * **Összesítési részletességi** időtartam, ameddig a metrikai szabálynak teljesülnie kell a riasztási eseményindítók előtt (pl. "Az elmúlt 30 percben")
    * és **értékelés gyakorisága** (pl. "1 perc")
   
   A Befejezés gombra kattintva válassza a **kész** lehetőséget.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Képernyőfelvétel: a panel, amelyen beállíthatja a riasztási logikát.":::

8. A **műveleti csoportok** szakaszban válassza az **új létrehozása** lehetőséget egy új csoport létrehozásához, hogy értesítést kapjon a riasztásról.

9. Töltse ki a "műveleti csoport hozzáadása" űrlapot névvel, rövid névvel, előfizetéssel és erőforráscsoporthoz.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="A képernyőképen a műveleti csoport hozzáadása űrlap látható, ahol megadhatja a leírt értékeket.":::

10. **E-mail/SMS/leküldéses/** hangműveletek típusának konfigurálása.
    
    Válassza az "e-mail-Azure Resource Manager szerepkör" lehetőséget, ha értesítéseket szeretne küldeni az előfizetés-tulajdonosoknak, közreműködőknek és olvasóknak.
   
    Ha elkészült, kattintson **az OK gombra** .

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Képernyőfelvétel: az e-mailek/mp-EK/leküldés/hang panel.":::

11. Adja meg a riasztási szabály nevét, leírását és súlyosságát.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Képernyőfelvétel: a riasztás részletei ablaktábla."::: 

12. A riasztás létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.

    Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

### <a name="managing-alerts"></a>Riasztások kezelése

Miután létrehozott egy riasztást, kiválaszthatja, és elvégezheti a következő műveleteket:

* Megtekintheti a metrika küszöbértékét és a riasztásra vonatkozó előző naptól számított tényleges értékeket bemutató diagramot.
* A riasztási szabály **szerkesztése** vagy **törlése** .
* **Tiltsa le** vagy **engedélyezze** a riasztást, ha átmenetileg le kívánja állítani vagy folytatni szeretné az értesítések fogadását.

## <a name="suggested-alerts"></a>Javasolt riasztások

### <a name="disk-space"></a>Lemezterület

A figyelés és a riasztás fontos az összes éles nagy kapacitású (Citus) kiszolgáló csoport számára. Az alapul szolgáló PostgreSQL-adatbázis megfelelő működéséhez szabad lemezterület szükséges. Ha a lemez megtelik, az adatbázis-kiszolgáló csomópont offline állapotba kerül, és elutasítja az indítást, amíg a szabad terület elérhetővé válik. Ezen a ponton a probléma megoldásához Microsoft támogatási kérelem szükséges.

Javasoljuk, hogy a lemezterület-riasztásokat az összes kiszolgálócsoport minden csomópontján állítsa be, még a nem éles használatra is. A lemezterület-használattal kapcsolatos riasztások gondoskodnak arról, hogy milyen előre figyelmeztetés szükséges a csomópontok Kifogástalan állapotba lépéséhez. A legjobb eredmények érdekében próbálkozzon a riasztások sorozatával 75%, 85% és 95% használatban. A kiválasztható százalékértékek az adatfeldolgozási sebességtől függenek, mivel a gyors adatfeldolgozás gyorsabban kitölti a lemezt.

Mivel a lemez megközelíti a lemezterület korlátját, próbálja ki a következő technikákat, hogy több szabad terület álljon rendelkezésre:

* Tekintse át az adatmegőrzési szabályzatot. Ha lehetséges, helyezze át a régi adattárolást a hűtőházi tárolóba.
* Érdemes lehet [csomópontokat hozzáadni](howto-hyperscale-scale-grow.md#add-worker-nodes) a kiszolgálócsoport és a szegmensek újrakiegyensúlyozásához. Az újrakiegyensúlyozás több számítógép között osztja el az adatmegosztást.
* Vegye fontolóra a munkavégző csomópontok [kapacitásának növekedését](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) . Minden dolgozó legfeljebb 2 TiB tárterületet tartalmazhat. A csomópontok átméretezése előtt azonban meg kell adni a csomópontok hozzáadását, mivel a csomópontok hozzáadása gyorsabban megtörtént.

### <a name="cpu-usage"></a>Processzorhasználat

A CPU-használat figyelése hasznos a teljesítmény alapkonfigurációjának létrehozásához. Észreveheti például, hogy a CPU-használat általában 40-60% körül van. Ha a CPU-használat hirtelen megkezdődik a 95% körül, akkor felismerheti az anomáliát. A CPU-használat a szerves növekedést is tükrözheti, de felfedi a kóbor lekérdezést is. A CPU-riasztások létrehozásakor állítsa be a hosszú összesítés részletességét a hosszan tartó növekedéshez, és hagyja figyelmen kívül a pillanatnyi tüskéket.

## <a name="next-steps"></a>Következő lépések
* További információ a [webhookok riasztásokban való konfigurálásáról](../azure-monitor/alerts/alerts-webhooks.md).
* [Tekintse át a metrikák gyűjteményét](../azure-monitor/data-platform.md) , és győződjön meg arról, hogy a szolgáltatás elérhető és rugalmas.
