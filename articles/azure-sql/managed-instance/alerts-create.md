---
title: Riasztások és értesítések beállítása a felügyelt példányhoz (Azure Portal)
description: A Azure Portal használatával SQL felügyelt példányokra vonatkozó riasztásokat hozhat létre, amelyek az Ön által megadott feltételek teljesülése esetén aktiválják az értesítéseket vagy az automatizálást.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: d37465223000399aa42895626c48426ddadc0c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592714"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Riasztások létrehozása felügyelt Azure SQL-példány esetében az Azure Portalon
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ebből a cikkből megtudhatja, hogyan állíthatja be a riasztásokat az Azure SQL felügyelt példány-adatbázisában lévő adatbázisokhoz a Azure Portal használatával. A riasztások küldhetnek e-mailt, meghívhatnak webhookot, végrehajthatnak egy Azure-függvényt, -runbookot, meghívhatnak külső, ITSM-kompatibilis jegykezelési rendszert, felhívhatják telefonon vagy küldhetnek SMS-t, ha egy adott metrika (például a példány tárterületének mérete vagy a processzorhasználat) elér egy előre meghatározott küszöbértéket. Ez a cikk a riasztási időszakok beállításának ajánlott eljárásait is tartalmazza.


## <a name="overview"></a>Áttekintés

Riasztást kaphat az Azure-szolgáltatásokra vonatkozó metrikák vagy események figyelése alapján.

* **Metrikai értékek** – a riasztás akkor aktiválódik, ha egy adott metrika értéke az egyik irányban hozzárendelt küszöbértéket keresztezi. Ez azt is elindítja, hogy a feltétel első teljesülése esetén, majd később, amikor az adott feltétel már nem teljesül.

A riasztást úgy is beállíthatja, hogy a következő esetekben hajtsa végre az eseményindítókat:

* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a rendszergazdáknak
* Küldjön e-mailt a megadott további e-mailekre.
* Telefonszám meghívása hangüzenettel
* Szöveges üzenet küldése telefonszámra
* Webhook meghívása
* Azure-függvény meghívása
* Az Azure runbook meghívása
* Külső jegyű ITSM-kompatibilis rendszer hívása

A riasztási szabályokkal kapcsolatos információkat [a Azure Portal, a PowerShell vagy az Azure CLI vagy a](../../azure-monitor/alerts/alerts-classic-portal.md) [Azure monitor REST API](/rest/api/monitor/alertrules)használatával konfigurálhatja és kérheti le. 

## <a name="alerting-metrics-available-for-managed-instance"></a>Felügyelt példányok számára elérhető riasztási mérőszámok

> [!IMPORTANT]
> A riasztási metrikák csak felügyelt példányok esetén érhetők el. A felügyelt példányokban lévő egyes adatbázisok esetében nem érhetők el riasztási mérőszámok. Az adatbázis-diagnosztika telemetria a [diagnosztikai naplók](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)formájában érhető el. A diagnosztikai naplókban lévő riasztások az [SQL Analytics](../../azure-monitor/insights/azure-sql.md) terméken belül állíthatók be a felügyelt példányhoz tartozó [log riasztási parancsfájlok](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) használatával.

A következő felügyelt példány metrikái elérhetők riasztási konfigurációhoz:

| Metric | Leírás | Mértékegység \ lehetséges értékek |
| :--------- | --------------------- | ----------- |
| Átlagos CPU-százalék | A CPU-kihasználtság átlagos százaléka a kiválasztott időszakban. | 0-100 (százalék) |
| I/o-bájtok olvasása | Az i/o-bájtok beolvasása a kiválasztott időszakban. | Bájt |
| I/o-bájtok írása | A kiválasztott időszakban írt IO-bájtok. | Bájt |
| IO-kérelmek száma | Az i/o-kérések száma a kiválasztott időszakban. | Numerikus |
| Tárterület fenntartva | A felügyelt példány számára fenntartott jelenlegi maximális tárterület. Változások az erőforrás-skálázási művelettel. | MB (megabájt) |
| Felhasznált tárterület | A kiválasztott időszakban használt tárterület. Az adatbázisok és a példányok tárterület-felhasználásának változásai. | MB (megabájt) |
| Virtuális mag száma | a felügyelt példány virtuális mag kiépítve. Változások az erőforrás-skálázási művelettel. | 4-80 (virtuális mag) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabály létrehozása metrika esetén a Azure Portal

1. Keresse meg a figyelni kívánt felügyelt példányt az Azure [Portalon](https://portal.azure.com/), és jelölje ki.

2. Válassza a **metrikák** menüpontot a figyelés szakaszban.

   ![Figyelés](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. A legördülő menüben válassza ki az egyik mérőszámot, amelyhez be szeretné állítani a riasztást (a példában a felhasznált tárterület látható).

4. Válassza ki az összesítési időszakot – átlagos, minimális vagy maximális érték a megadott időszakban (AVG, min vagy max). 

5. **Új riasztási szabály** kiválasztása

6. A riasztási szabály létrehozása panelen kattintson a **feltétel nevére** (a példában a felhasznált tárolóhely látható).

   ![Feltétel meghatározása](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. A jel logikájának konfigurálása panelen adja meg az operátort, az Összesítés típusát és a küszöbértéket.

   * Az operátor típusa beállítás értéke nagyobb, mint, egyenlő és kisebb, mint (a küszöbérték)
   * Az összesítési típus beállításai a következők: min, Max vagy Average (az összesítési részletességi időszakban)
   * A küszöbérték a riasztási érték, amely az operátori és az összesítési feltételek alapján lesz kiértékelve.
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   A képernyőképen látható példában a 1840876 MB értékű érték a 1,8 TB küszöbértéket jelképezi. Mivel a példában szereplő operátor értéke nagyobb, mint a, a rendszer akkor hozza létre a riasztást, ha a felügyelt példányon a tárterület-felhasználás 1,8 TB fölé kerül. Vegye figyelembe, hogy a tárolóhelyek metrikáinak küszöbértékét MEGABÁJTban kell megadni.

8. Állítsa be a próbaidőszak összesítési részletességét percben és a kiértékelés gyakoriságát. A kiértékelés gyakorisága azt jelzi, hogy a riasztási rendszer rendszeres időközönként ellenőrzi, hogy teljesülnek-e a küszöbérték-feltétel.

9. Válasszon műveleti csoportot. A műveleti csoport ablaktábla megjeleníti a meglévőket, vagy létrehozhat egy új műveletet. Ez a művelet határozza meg, hogy a rendszer a riasztások kiváltásakor fog történni (például e-mailek küldése, telefon hívása, webhook, Azure-függvény vagy runbook).

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Új műveleti csoport létrehozásához válassza a **+ műveleti csoport létrehozása** lehetőséget.

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Adja meg, hogyan kívánja riasztani a riasztást: adja meg a műveleti csoport nevét, a rövid nevet, a művelet nevét, és válassza a művelet típusa lehetőséget. A Művelettípus határozza meg, hogy e-mailben, szöveges üzenetben, hanghívásban vagy esetleg webhookban, Azure-függvényben, runbook vagy ITSM-jegyben lesz-e létrehozva a kompatibilis rendszerben.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Adja meg a rekordok riasztási szabályának részleteit, és válassza ki a súlyosság típusát.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * A riasztási szabály létrehozásához kattintson a **riasztási szabály létrehozása** gombra.

Az új riasztási szabály néhány percen belül aktív lesz, és a beállítások alapján aktiválódik.

## <a name="verifying-alerts"></a>Riasztások ellenőrzése

> [!NOTE]
> A zajos riasztások elnyomásával kapcsolatban lásd: riasztások leállítása [műveleti szabályok használatával](../../azure-monitor/alerts/alerts-action-rules.md#suppression-of-alerts).

Riasztási szabály beállításakor ellenőrizze, hogy elégedett-e a riasztási triggerrel és annak gyakoriságával. Az ezen a lapon látható, a használatban lévő tárolóhelyre vonatkozó riasztás beállításához, ha a riasztási lehetőség e-mailben szerepel, a következőt láthatja: e-mail-cím.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

Az e-mail megjeleníti a riasztás nevét, a küszöbérték részleteit, valamint azt, hogy a riasztás miért lett aktiválva a riasztás ellenőrzéséhez és hibakereséséhez. A **Azure Portal** gombra kattintva megtekintheti a Azure Portal e-mailben fogadott riasztásokat. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Meglévő riasztási szabályok megtekintése, felfüggesztése, aktiválása, módosítása és törlése

> [!NOTE]
> A meglévő riasztásokat Azure Portal irányítópult riasztások menüjéből kell felügyelni. A meglévő riasztások nem módosíthatók a felügyelt példányok erőforrásának paneljéről.

Meglévő riasztások megtekintése, felfüggesztése, aktiválása, módosítása és törlése:

1. Riasztások keresése Azure Portal keresés használatával. Kattintson a riasztások elemre.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Azt is megteheti, hogy a riasztások elemre kattint az Azure navigációs sávján, ha konfigurálva van.

2. A riasztások panelen kattintson a riasztási szabályok kezelése lehetőségre.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   Megjelenik a meglévő riasztások listája. Válasszon ki egy egyéni meglévő riasztási szabályt a kezeléséhez. A meglévő aktív szabályok módosíthatók és beállíthatók az Ön igényei szerint. Az aktív szabályok felfüggesztés nélkül is felmenthetők. 

## <a name="next-steps"></a>Következő lépések

* Tudnivalók Azure Monitor riasztási rendszerről: [a riasztások áttekintése Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
* További információ a metrikus riasztásokról: [a metrikai riasztások működésének ismertetése Azure monitor](../../azure-monitor/alerts/alerts-metric-overview.md)
* További tudnivalók a webhookok riasztásokban való konfigurálásáról: [webhook meghívása klasszikus metrikus riasztással](../../azure-monitor/alerts/alerts-webhooks.md)
* Tudnivalók a riasztások PowerShell használatával történő konfigurálásáról és kezeléséről: [műveleti szabályok](/powershell/module/az.monitor/add-azmetricalertrulev2)
* További információ a riasztások API-val történő konfigurálásáról és kezeléséről: [Azure Monitor REST API referenciája](/rest/api/monitor/)