---
title: Lassú lekérdezési naplók konfigurálása – Azure Portal-Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a lassú lekérdezési naplókat Azure Database for MySQL rugalmas kiszolgálóról a Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a61c8e3451d661dae2e5ad56a0d4a947252ec873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540063"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Lassú lekérdezési naplók konfigurálása és elérése Azure Database for MySQL rugalmas kiszolgálóhoz a Azure Portal használatával

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Beállíthatja, listázhatja és letöltheti a Azure Database for MySQL rugalmas kiszolgáló [lassú lekérdezési naplóit](concepts-slow-query-logs.md) a Azure Portalból.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések megkövetelik, hogy [rugalmas kiszolgálóval](quickstart-create-server-portal.md)rendelkezzen.

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a MySQL lassú lekérdezési napló elérését. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki a rugalmas kiszolgálót.

1. Az oldalsáv **Beállítások** szakaszában válassza ki a **kiszolgáló paramétereit**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="Kiszolgáló paramétereinek lapja":::

1. Frissítse a **slow_query_log** paramétert **a** következőre:.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="A lassú lekérdezési naplók bekapcsolása.":::

1. Módosítsa a többi szükséges paramétert (pl. `long_query_time`, `log_slow_admin_statements`). További paraméterekért tekintse meg a [lassú lekérdezési naplók](./concepts-slow-query-logs.md#configure-slow-query-logging) dokumentációját.  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="Lassú lekérdezési naplóval kapcsolatos paraméterek frissítése":::

1. Kattintson a **Mentés** gombra. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="Lassú lekérdezési napló paramétereinek mentése.":::

A **kiszolgáló paraméterei** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="set-up-diagnostics"></a>Diagnosztika beállítása

A lassú lekérdezési naplók integrálva vannak Azure Monitor diagnosztikai beállításokkal, amelyek lehetővé teszik a naplók Azure Monitor naplókba, Event Hubs vagy Azure Storage-ba történő átadását.

1. Az oldalsáv **figyelés** szakaszában válassza a **diagnosztikai beállítások**  >  **Hozzáadás diagnosztikai beállítások** elemet.

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="A diagnosztikai beállítások beállításainak képernyőképe":::

1. Adja meg a diagnosztikai beállítások nevét.

1. Itt adhatja meg, hogy mely célhelyek küldje el a lassú lekérdezési naplókat (Storage-fiók, Event hub vagy Log Analytics munkaterület).

1. Válassza a **MySqlSlowLogs** lehetőséget a napló típusaként.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="A diagnosztikai beállítások konfigurációs beállításainak képernyőképe":::

1. Miután konfigurálta az adatnyelőket a lassú lekérdezések naplóinak a csatornába való konfigurálásához, válassza a **Mentés** lehetőséget.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="A diagnosztikai beállítások konfigurációs beállításainak képernyőképe a kijelölt mentéssel":::

1. A lassú lekérdezési naplók eléréséhez vizsgálja meg őket a konfigurált adattárolók között. A naplók megjelenése akár 10 percet is igénybe vehet.

Ha a naplókat Azure Monitor naplókba (Log Analytics) használja, tekintse meg az elemzéshez használható [példákat](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) . 

## <a name="next-steps"></a>Következő lépések
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- További információ a [lassú lekérdezési naplókról](concepts-slow-query-logs.md)
- A paraméter-definíciókkal és a MySQL-naplózással kapcsolatos további információkért tekintse meg a MySQL dokumentációját a [naplókon](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- A [naplók](concepts-audit-logs.md) ismertetése
