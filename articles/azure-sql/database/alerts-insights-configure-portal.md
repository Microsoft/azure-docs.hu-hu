---
title: Riasztások és értesítések beállítása (a Azure Portal)
description: A Azure Portal használatával riasztásokat hozhat létre, amelyek a megadott feltételek teljesülése esetén aktiválják az értesítéseket vagy az automatizálást.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: 2b7457ad5870e31d682f8727901795c9d6d73f06
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442666"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>Riasztások létrehozása a Azure SQL Database és az Azure szinapszis Analytics számára a Azure Portal használatával
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan állíthatja be a Azure SQL Database és az Azure szinapszis Analytics (korábbi nevén SQL Data Warehouse) adatbázisaihoz tartozó riasztásokat a Azure Portal használatával. A riasztások e-mailt küldhetnek, vagy meghívhatnak egy webhookot, ha valamilyen metrika (például az adatbázis mérete vagy a CPU-használat) eléri a küszöbértéket.

> [!NOTE]
> Az Azure SQL felügyelt példányaira vonatkozó utasításokért lásd: [riasztások létrehozása az Azure SQL felügyelt példányaihoz](../managed-instance/alerts-create.md).

Riasztást kaphat az Azure-szolgáltatásokra vonatkozó metrikák vagy események figyelése alapján.

* **Metrikai értékek** – a riasztás akkor aktiválódik, ha egy adott metrika értéke az egyik irányban hozzárendelt küszöbértéket keresztezi. Ez azt is elindítja, hogy a feltétel első teljesülése esetén, majd később, amikor az adott feltétel már nem teljesül.
* **Műveletnapló eseményei** – a riasztás *minden* eseménynél aktiválható, vagy csak bizonyos számú esemény bekövetkeztekor.

A riasztást úgy is beállíthatja, hogy a következő esetekben hajtsa végre az eseményindítókat:

* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a társ-rendszergazdáknak
* Küldjön e-mailt a megadott további e-mailekre.
* Webhook meghívása

A riasztási szabályokkal kapcsolatos információkat a

* [A Azure Portal](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [Parancssori felület (CLI)](../../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabály létrehozása metrika esetén a Azure Portal

1. A [portálon](https://portal.azure.com/)keresse meg a figyelni kívánt erőforrást, és jelölje ki.
2. A figyelés szakaszban válassza a **riasztások** lehetőséget. A szöveg és az ikon némileg eltérő lehet a különböző erőforrások esetében.  

   ![Figyelés](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Kattintson az **új riasztási szabály** gombra a **szabály létrehozása** lap megnyitásához.
  ![Szabály létrehozása](./media/alerts-insights-configure-portal/create-rule.png)

4. A **feltétel** szakaszban kattintson a **Hozzáadás**gombra.
  ![Feltétel meghatározása](./media/alerts-insights-configure-portal/create-rule.png)
5. A **jel logikai beállítása** lapon válasszon ki egy jelet.
  ![Jel kiválasztása](./media/alerts-insights-configure-portal/select-signal.png)
6. Ha kiválasztott egy jelet, például a **CPU-százalékot**, megjelenik a **jel logikai beállítása** lap.
  ![Jellogika konfigurálása](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. Ezen a lapon konfigurálhatja a küszöbértéket, az operátort, az összesítési típust, a küszöbértéket, az Összesítés részletességét és a kiértékelés gyakoriságát. Ezután kattintson a **kész**gombra.
8. A **szabály létrehozása**lapon válasszon ki egy meglévő **műveleti csoportot** , vagy hozzon létre egy új csoportot. A műveleti csoport lehetővé teszi, hogy meghatározza a riasztási feltétel bekövetkezésekor végrehajtandó műveletet.
  ![Műveleti csoport definiálása](./media/alerts-insights-configure-portal/action-group.png)

9. Adja meg a szabály nevét, adjon meg egy opcionális leírást, válassza ki a szabály súlyossági szintjét, adja meg, hogy engedélyezi-e a szabályt a szabályok létrehozásakor, majd kattintson a **szabály riasztás létrehozása** elemre a metrikai szabály riasztásának létrehozásához.

10 percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

## <a name="next-steps"></a>Következő lépések

* További információ a [webhookok riasztásokban való konfigurálásáról](../../azure-monitor/platform/alerts-webhooks.md).
