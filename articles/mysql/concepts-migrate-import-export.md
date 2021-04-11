---
title: Importálás és exportálás – Azure Database for MySQL
description: Ez a cikk az adatbázisok Azure Database for MySQL-ben történő importálásának és exportálásának általános módszereit ismerteti a MySQL Workbench eszközzel.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 049a0ad45ea82210d8fac28db0fb3d067841bba4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625143"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>A MySQL-adatbázis migrálása importálás és exportálás használatával

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Ez a cikk két gyakori megközelítést ismertet az adatAzure Database for MySQL-kiszolgálóra való importálásához és exportálásához a MySQL Workbench használatával.

Részletes és átfogó áttelepítési útmutatást az [áttelepítési útmutató erőforrásai](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)című témakörben talál. 

Más áttelepítési forgatókönyvek esetén tekintse meg az [adatbázis-áttelepítési útmutatót](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Előfeltételek

A MySQL-adatbázis áttelepítésének megkezdése előtt a következőket kell tennie:
- Hozzon létre egy [Azure Database for MySQL-kiszolgálót a Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md).
- Töltse le és telepítse a [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) vagy más, harmadik féltől származó MySQL-eszközt az importáláshoz és exportáláshoz.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Adatbázis létrehozása a Azure Database for MySQL-kiszolgálón
Hozzon létre egy üres adatbázist a Azure Database for MySQL-kiszolgálón a MySQL Workbench, a Varangy vagy a Navicat használatával. Az adatbázis neve megegyezik a dömpingelt adattartalommal, vagy létrehozhat egy másik nevet tartalmazó adatbázist is.

A csatlakozáshoz tegye a következőket:

1. A Azure Portal keresse meg a kapcsolatok adatait a MySQL-hez készült Azure-adatbázis **Áttekintés** paneljén.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Képernyőkép a Azure Portal Azure Database for MySQL kiszolgáló csatlakoztatási adatairól.":::

1. Adja hozzá a kapcsolódási adatokat a MySQL Workbenchhez.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL Workbench-beli kapcsolatok karakterlánca":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Az importálási és exportálási technikák használatának megállapítása

> [!TIP]
> A teljes adatbázis kiírására és visszaállítására szolgáló forgatókönyvek esetében Ehelyett használja a [memóriakép és visszaállítás](concepts-migrate-dump-restore.md) módszert.

A következő helyzetekben a MySQL Tools használatával importálhat és exportálhat adatbázisokat a MySQL-adatbázisba. Más eszközök esetében lépjen a [MySQL – Azure Database áttelepítési útmutató](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf)"áttelepítési módszerek" szakaszára (22. oldal). 

- Ha szelektíven kell választania néhány táblázatot egy meglévő MySQL-adatbázisból az Azure MySQL-adatbázisba való importáláshoz, ajánlott az importálási és exportálási módszer használata.  Ezzel kihagyhatja az áttelepítés felesleges tábláit, így időt és erőforrásokat takaríthat meg. Használja például a vagy a `--include-tables` `--exclude-tables` kapcsolót a [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) és a `--tables` kapcsolót a [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Ha a táblázatokon kívül más adatbázis-objektumokat helyez át, explicit módon hozza létre ezeket az objektumokat. Belefoglalja a korlátozásokat (elsődleges kulcs, külső kulcs és indexek), a nézeteket, a függvényeket, az eljárásokat, az eseményindítókat és az összes olyan adatbázis-objektumot, amelyet át szeretne telepíteni.
- Ha egy MySQL-adatbázistól eltérő külső adatforrásokból végez áttelepítést, hozzon létre lapos fájlokat, és importálja őket a [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)használatával.

> [!Important]
> Az egyetlen kiszolgáló és a rugalmas kiszolgáló is *csak a InnoDB-tárolót* támogatja. Győződjön meg arról, hogy az adatbázisban lévő összes tábla a InnoDB Storage motort használja, amikor az Azure Database for MySQL-be tölti be az adatait.
>
> Ha a forrásadatbázis egy másik tárolóeszközt használ, az adatbázis migrálása előtt váltson át a InnoDB motorra. Ha például van egy WordPress vagy egy webalkalmazás, amely a MyISAM motort használja, először alakítsa át a táblákat az InnoDB-táblákba való áttelepítéssel. A záradék használatával `ENGINE=INNODB` állítsa be a motort egy tábla létrehozásához, majd az adatok átvitelét a kompatibilis táblába az áttelepítés előtt.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Teljesítményre vonatkozó javaslatok importáláshoz és exportáláshoz

Az optimális adatimportálás és-exportálás érdekében javasoljuk, hogy tegye a következőket:
-   Fürtözött indexeket és elsődleges kulcsokat hozhat létre az adatbetöltése előtt. Az elsődleges kulcs sorrendjében töltse be az adathalmazt.
-   A másodlagos indexek létrehozásának késleltetése, amíg az adatgyűjtés be nem fejeződik.
-   Az adatbetöltése előtt tiltsa le a külső kulcsra vonatkozó korlátozásokat. A külső kulcsok ellenőrzésének letiltása jelentős teljesítményt nyújt. Engedélyezze a korlátozásokat, és ellenőrizze az adatok betöltését a hivatkozási integritás biztosítása érdekében.
-   Párhuzamosan tölthetők be az adathalmazok. Kerülje a túl sok párhuzamosságot, amelynek hatására elérheti az erőforrás-korlátot, és figyelheti az erőforrásokat a Azure Portal elérhető metrikák használatával.
-   Szükség esetén particionált táblákat használjon.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Az adatimportálás és-exportálás a MySQL Workbench használatával
Az adatok a MySQL Workbenchben való exportálásának és importálásának két módja van: az objektum böngésző helyi menüjében vagy a navigátor ablaktáblán. Minden metódus más célt szolgál.

> [!NOTE]
> Ha a MySQL Workbenchben a MySQL-kiszolgáló vagy a rugalmas kiszolgáló (előzetes verzió) használatával csatlakozik a kiszolgálóhoz, tegye a következőket:
> - MySQL-kiszolgáló esetén győződjön meg arról, hogy a Felhasználónév formátuma *\<username@servername>* .
> - A MySQL-hez rugalmas kiszolgáló csak a következőt használja: *\<username>* . Ha a használatával *\<username@servername>* csatlakozik, a kapcsolat sikertelen lesz.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>A tábla adatexportálási és-importálási varázslóinak futtatása az objektum böngésző helyi menüjéből

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Képernyőkép a MySQL Workbench exportálási és importálási varázsló parancsairól az objektum böngésző helyi menüjében.":::

A tábla adatai varázsló CSV-és JSON-fájlok használatával támogatja az importálási és exportálási műveleteket. A varázslók több konfigurációs lehetőséget is tartalmaznak, például elválasztókat, oszlopok kijelölését és a kódolás kijelölését. Az egyes varázslókat helyi vagy távolról csatlakoztatott MySQL-kiszolgálókon is futtathatja. Az importálási művelet táblázat-, oszlop-és típus-hozzárendelést tartalmaz.

Ha ezeket a varázslókat az objektum böngészője helyi menüjéből szeretné elérni, kattintson a jobb gombbal a táblázatra, majd válassza a **tábla adatexportálás varázsló** vagy a **tábla adatimportálása varázslót**.

#### <a name="the-table-data-export-wizard"></a>A tábla adatexportálási varázslója

Táblázat exportálása CSV-fájlba:

1. Kattintson a jobb gombbal az exportálni kívánt adatbázis táblára.
1. Válassza a **tábla adatexportálás varázslót**. Válassza ki az exportálandó oszlopokat, a sorok eltolását (ha van), és a darabszámot (ha van ilyen).
1. Az **Exportálás** ablaktáblán válassza a **tovább** lehetőséget. Válassza ki a fájl elérési útját, CSV-fájlját vagy JSON-fájltípusát. Válassza ki a vonal elválasztóját, a karakterláncok befoglalásának metódusát és a mező elválasztóját is.
1. A **kimeneti fájl helye** ablaktáblán válassza a **tovább** lehetőséget.
1. Az **adatexportálás** ablaktáblán válassza a **tovább** lehetőséget.

#### <a name="the-table-data-import-wizard"></a>A tábla adatimportálási varázslója

Táblázat importálása CSV-fájlból:

1. Kattintson a jobb gombbal az importálandó adatbázis táblára.
1. Keresse meg és válassza ki az importálni kívánt CSV-fájlt, majd kattintson a **tovább** gombra.
1. Válassza ki a céltábla (új vagy meglévő) elemet, jelölje be a **tábla csonkítása az importálás előtt** jelölőnégyzetet, vagy törölje a jelölését, majd válassza a **tovább** lehetőséget.
1. Válassza ki az importálni kívánt kódolást és oszlopokat, majd kattintson a **tovább** gombra.
1. Az **adatimportálás** ablaktáblán válassza a **tovább** lehetőséget. A varázsló importálja az adatmennyiséget.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Futtassa az SQL-adatok exportálása és importálása varázslót a navigátor ablaktáblán.
A MySQL Workbenchből vagy a mysqldump parancsból generált SQL-adatok exportálására és importálására használhatja a varázslót. A varázslókat a **navigátor** ablaktáblán érheti el, vagy a főmenü **kiszolgáló** elemét választva is kiválaszthatja.

#### <a name="export-data"></a>Adatok exportálása

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Képernyőkép a navigátor ablaktábla használatával a MySQL Workbench adatexportálás paneljének megjelenítéséhez.":::

Az **adatexportálás** ablaktáblán a MySQL-adatait is exportálhatja.

1. A MySQL Workbenchben kattintson a **navigátor** ablaktáblán az **adatexportálás** elemre.

1. Az **adatexportálás** ablaktáblán válassza ki az exportálni kívánt sémákat.
 
   Minden séma esetében kiválaszthatja az exportálandó sémákat vagy táblákat. A konfigurációs beállítások közé tartozik az Exportálás projekt mappájába vagy egy önálló SQL-fájlba, a tárolt rutinok és események kiírása vagy a táblázat adatainak kihagyása.

   Azt is megteheti, hogy egy **eredményhalmaz exportálásával** exportál egy adott EREDMÉNYHALMAZT az SQL-szerkesztőben más formátumba, például CSV, JSON, HTML és XML formátumban.

1. Válassza ki az exportálandó adatbázis-objektumokat, majd konfigurálja a kapcsolódó beállításokat.
1. Az aktuális objektumok betöltéséhez válassza a **frissítés** lehetőséget.
1. Ha szeretné, a jobb felső sarokban válassza a **Speciális beállítások** lehetőséget az exportálási művelet pontosításához. Tegyük fel például, hogy a tábla zárolások hozzáadása, az INSERT utasítás helyett a Replace, és a kezdő karaktereket tartalmazó idézőjelek szerepelnek.
1. Válassza az **Exportálás indítása** lehetőséget az exportálási folyamat megkezdéséhez.


#### <a name="import-data"></a>Adatok importálása

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Képernyőfelvétel a navigátor ablaktábla használatával a MySQL Workbench adatimportálás paneljének megjelenítéséhez.":::

Az **adatok importálása** ablaktáblán az exportált adatok importálhatók vagy visszaállíthatók az adatexportálási műveletből vagy a mysqldump parancsból.

1. A MySQL Workbenchben kattintson a **navigátor** ablaktáblán az **adatexportálás/visszaállítás** elemre.
1. Válassza ki a Project mappát vagy az önálló SQL-fájlt, válassza ki a importálni kívánt sémát, vagy az új séma definiálásához kattintson az **új** gombra.
1. Az importálási folyamat megkezdéséhez válassza az **Importálás megkezdése** lehetőséget.

## <a name="next-steps"></a>Következő lépések
- Egy másik áttelepítési módszer esetén lásd: [a MySQL-adatbázis áttelepítése egy Azure Database for MySQL-re a dump és a Restore paranccsal](concepts-migrate-dump-restore.md).
- Az adatbázisok MySQL-hez készült Azure Database-be történő áttelepítésével kapcsolatos további információkért tekintse meg az [adatbázis áttelepítési Útmutatóját](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
