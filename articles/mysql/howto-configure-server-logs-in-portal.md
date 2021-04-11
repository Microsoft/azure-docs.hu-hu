---
title: Lassú lekérdezési naplók elérése – Azure Portal-Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan konfigurálhatja és érheti el a Azure Database for MySQL lassú naplóit a Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 91569780aa71861e07c7e96bec5eac879642760d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496218"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Lassú lekérdezési naplók konfigurálása és elérése a Azure Portal

Beállíthatja, listázhatja és letöltheti az [Azure Database for MySQL lassú lekérdezési naplókat](concepts-server-logs.md) a Azure Portalból.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések végrehajtásához Azure Database for MySQL- [kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)szükséges.

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a MySQL lassú lekérdezési napló elérését. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a Azure Database for MySQL-kiszolgálót.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók** lehetőséget. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="A kiszolgálói naplók beállításainak képernyőképe":::

4. A kiszolgáló paramétereinek megtekintéséhez **kattintson ide a naplók engedélyezéséhez és a naplózási paraméterek konfigurálásához**.

5. **Slow_query_log** bekapcsolása **a** következőre:.

6. Válassza ki, hová szeretné kiadni a naplókat a **log_output** használatához. Ha a naplókat a helyi tárolóba és Azure Monitor diagnosztikai naplókba kívánja küldeni, válassza a **fájl** elemet.

7. Érdemes megfontolni a "long_query_time" beállítást, amely a lassú lekérdezési naplófájlban összegyűjtött lekérdezések lekérdezési idő küszöbértékét jelöli, a long_query_time minimális és alapértelmezett értéke pedig 0 és 10.

8. Módosítsa a más paramétereket, például a log_slow_admin_statements a felügyeleti utasítások naplózására. Alapértelmezés szerint a felügyeleti utasítások nincsenek naplózva, és nem olyan lekérdezések, amelyek nem használnak indexeket a keresésekhez. 

9. Kattintson a **Mentés** gombra. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Képernyőkép a lassú lekérdezési napló paramétereinek és mentéséhez.":::

A **kiszolgáló paraméterei** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="view-list-and-download-logs"></a>A lista és a letöltési naplók megtekintése
A naplózás megkezdése után megtekintheti az elérhető lassú lekérdezési naplók listáját, és letöltheti az egyes naplófájlokat.

1. Nyissa meg az Azure Portalt.

2. Válassza ki a Azure Database for MySQL-kiszolgálót.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók** lehetőséget. A lap megjeleníti a naplófájlok listáját.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Képernyőkép a kiszolgálói naplók lapról, a Kiemelt naplók listájával":::

   > [!TIP]
   > A napló elnevezési konvenciója **MySQL-Slow-< a kiszolgáló nevét>-yyyymmddhh. log**. A fájlnévben használt dátum és idő a napló kiadásának időpontja. A naplófájlokat 24 óránként vagy 7,5 GB-ban forgatják el, attól függően, hogy melyik következik be először. 

4. Ha szükséges, a keresőmező használatával gyorsan leszűkítheti egy adott naplóra a dátum és idő alapján. A keresés a napló nevében található.

5. Az egyes naplófájlok letöltéséhez kattintson az egyes naplófájlok melletti lefelé mutató nyíl ikonra a táblázat sorában.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="Képernyőkép a kiszolgálói naplók lapról, a lefelé mutató nyíl ikon kiemelve":::

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **figyelés** szakaszában válassza a **diagnosztikai beállítások**  >  **Hozzáadás diagnosztikai beállítások** elemet.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="A diagnosztikai beállítások beállításainak képernyőképe":::

2. Adja meg a diagnosztikai beállítások nevét.

3. Itt adhatja meg, hogy mely adatnyelők küldje el a lassú lekérdezési naplókat (Storage-fiók, Event hub vagy Log Analytics munkaterület).

4. Válassza a **MySqlSlowLogs** lehetőséget a napló típusaként.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="A diagnosztikai beállítások konfigurációs beállításainak képernyőképe":::

5. Miután konfigurálta az adatnyelőket a lassú lekérdezések naplóinak a csatornába való konfigurálásához, válassza a **Mentés** lehetőséget.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="A diagnosztikai beállítások konfigurációs beállításainak képernyőképe a kijelölt mentéssel":::

6. A lassú lekérdezési naplók eléréséhez vizsgálja meg őket a konfigurált adattárolók között. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
- A lassú lekérdezési naplók programozott módon történő letöltésének megismeréséhez lásd: [a lassú lekérdezési naplók elérése a CLI-ben](howto-configure-server-logs-in-cli.md) .
- További információ a Azure Database for MySQL [lassú lekérdezési naplóiról](concepts-server-logs.md) .
- A paraméter-definíciókkal és a MySQL-naplózással kapcsolatos további információkért tekintse meg a MySQL dokumentációját a [naplókon](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).