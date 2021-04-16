---
title: Importálás és exportálás – Azure Database for MySQL
description: Ez a cikk az adatbázisok importálásának és exportálásának gyakori Azure Database for MySQL a MySQL Workbench és hasonló eszközök használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 641dfa2439513138b5dd8f56843e81c31eb38609
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389774"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>A MySQL-adatbázis migrálása importálás és exportálás használatával

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Ez a cikk két gyakori megközelítést ismerteti az adatok importálására és exportálására egy Azure Database for MySQL a MySQL Workbench használatával.

A migrálással kapcsolatos részletes és átfogó útmutatásért tekintse meg a [migrálási útmutató erőforrásait.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) 

Egyéb áttelepítési forgatókönyvekért lásd az adatbázis-áttelepítési [útmutatót.](https://datamigration.microsoft.com/) 

## <a name="prerequisites"></a>Előfeltételek

A MySQL-adatbázis áttelepítésének megkezdése előtt el kell kezdenie a következőt:
- Hozzon [létre egy Azure Database for MySQL-kiszolgálót a Azure Portal.](quickstart-create-mysql-server-database-using-azure-portal.md)
- Töltse le és telepítse a [MySQL Workbench alkalmazást](https://dev.mysql.com/downloads/workbench/) vagy más külső MySQL-eszközt importáláshoz és exportáláshoz.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Adatbázis létrehozása a Azure Database for MySQL kiszolgálón
Hozzon létre egy üres adatbázist a Azure Database for MySQL MySQL Workbench, Toad vagy Navicat használatával. Az adatbázis neve megegyezik a kidobott adatokat tartalmazó adatbázis nevével, vagy létrehozhat egy másik nevű adatbázist.

A csatlakozáshoz tegye a következőket:

1. A Azure Portal keresse meg a kapcsolati adatokat a Azure Database for MySQL. 

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Képernyőkép a Azure Database for MySQL kapcsolati adatairól a Azure Portal.":::

1. Adja hozzá a kapcsolati adatokat a MySQL Workbenchhez.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Képernyőkép a MySQL Workbench kapcsolati sztringről.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Annak meghatározása, hogy mikor használjon importálási és exportálási technikákat

> [!TIP]
> Olyan forgatókönyvek esetén, ahol a teljes adatbázist ki szeretné állítani és ki szeretné állítani, használja inkább a [memóriakép és](concepts-migrate-dump-restore.md) visszaállítás módszert.

A következő forgatókönyvekben a MySQL-eszközökkel importálhat és exportálhat adatbázisokat a MySQL-adatbázisba. Egyéb eszközökért lásd a [MySQL–Azure Database](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf)migrálási útmutató "Migrálási módszerek" című szakaszát (22. oldal). 

- Ha csak néhány táblát kell választania egy meglévő MySQL-adatbázisból az Azure MySQL-adatbázisba való importáláshoz, ajánlott az importálási és exportálási technikát használni. Így időt és erőforrásokat takaríthat meg, ha kihagyja a felesleges táblákat a migrálásból. Használja például a vagy a `--include-tables` `--exclude-tables` kapcsolót a [mysqlpump kapcsolóval,](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)a kapcsolót pedig a `--tables` [mysqldump kapcsolóval.](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables)
- Ha táblákon kívül más adatbázis-objektumokat is mozgat, explicit módon hozza létre ezeket az objektumokat. Korlátozásokat (elsődleges kulcs, idegen kulcs és indexek), nézeteket, függvényeket, eljárásokat, eseményindítókat és az átemelni kívánt egyéb adatbázis-objektumokat foglalja magában.
- A MySQL-adatbázistól különböző külső adatforrásból származó adatok áttelepítésekor hozzon létre egymáshoz hasonló fájlokat, és importálja őket a [mysqlimport használatával.](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)

> [!Important]
> Az egykiszolgálós és a rugalmas kiszolgáló is csak az InnoDB tárolási motort támogatja. Győződjön meg arról, hogy az adatbázis összes táblája az InnoDB tárolási motort használja, amikor adatokat tölt be a MySQL-hez használt Azure-adatbázisba.
>
> Ha a forrásadatbázis egy másik tárolómotort használ, alakítsa át az InnoDB-motort az adatbázis mi áttelepítése előtt. Ha például van egy WordPress- vagy webalkalmazása, amely a MyISAM motort használja, először alakítsa át a táblákat az adatok InnoDB-táblákba való milikálással. A záradék használatával beállíthatja a motort egy tábla létrehozásához, majd a migrálás előtt átkísérte az adatokat a kompatibilis `ENGINE=INNODB` táblába.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Teljesítménnyel kapcsolatos javaslatok importáláshoz és exportáláshoz

Az optimális adatimport- és exportálási teljesítmény érdekében javasoljuk, hogy tegye a következőket:
-   Az adatok betöltése előtt hozzon létre fürtözött indexeket és elsődleges kulcsokat. Töltse be az adatokat az elsődleges kulcs sorrendjében.
-   Késleltetheti a másodlagos indexek létrehozását, amíg be nem töltődnek az adatok.
-   Az adatok betöltése előtt tiltsa le a idegen kulcsokra vonatkozó korlátozásokat. A idegenkulcs-ellenőrzések letiltása jelentős teljesítménynövekedést eredményez. Engedélyezze a korlátozásokat, és ellenőrizze az adatokat a betöltés után a hivatkozási integritás biztosítása érdekében.
-   Adatok párhuzamos betöltése. Kerülje a túl sok párhuzamosság, amely az erőforráskorlát túllépését okozná, és figyelje az erőforrásokat a Azure Portal.
-   Ha szükséges, használjon particionált táblákat.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Adatok importálása és exportálása a MySQL Workbench használatával
Kétféleképpen exportálhat és importálhat adatokat a MySQL Workbenchben: az objektumböngésző helyi menüjéből vagy a Kezelő panelről. Minden metódus más célt szolgál.

> [!NOTE]
> Ha az egykiszolgálós MySQL-kiszolgálóhoz vagy a rugalmas kiszolgálóhoz (előzetes verzió) a MySQL Workbenchben ad hozzá kapcsolatot, tegye a következőket:
> - Az egykiszolgálós MySQL-kiszolgálóhoz győződjön meg arról, hogy a felhasználónév formátuma *\<username@servername>* .
> - Rugalmas MySQL-kiszolgáló esetén csak használja *\<username>* a et. Ha a használatával *\<username@servername>* csatlakozik, a kapcsolat sikertelen lesz.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>A táblaadatok exportálása és importálása varázslók futtatása az objektumböngésző helyi menüjéből

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Képernyőkép a MySQL Workbench exportálási és importálási varázsló parancsairól az objektumböngésző helyi menüjében.":::

A táblaadatok varázslói támogatják az importálási és exportálási műveleteket CSV- és JSON-fájlok használatával. A varázslók számos konfigurációs beállítást tartalmaznak, például elválasztókat, oszlopválasztást és kódolási kijelölést. Minden varázslót futtathat helyi vagy távolról csatlakoztatott MySQL-kiszolgálókon. Az importálási művelet magában foglalja a tábla-, oszlop- és típusleképezést.

A varázslók az objektumböngésző helyi menüjéből való eléréséhez kattintson a jobb gombbal egy táblára, majd válassza a **Táblázatadatok** exportálása varázsló vagy a **Táblaadatok importálása varázsló lehetőséget.**

#### <a name="the-table-data-export-wizard"></a>A táblaadatok exportálása varázsló

Tábla exportálása CSV-fájlba:

1. Kattintson a jobb gombbal az exportálni következő adatbázis táblára.
1. Válassza a **Táblázatadatok exportálása varázsló lehetőséget.** Válassza ki az exportálni kívánt oszlopokat, a soreltolást (ha van) és a darabszám (ha van).
1. Az Adatok **kiválasztása exportáláshoz panelen** válassza a Tovább **lehetőséget.** Válassza ki a fájl elérési útját, CSV-fájlját vagy JSON-fájltípusát. Válassza ki a sorelválasztót, a sztringek bezárásának módszerét és a mezőelválasztót is.
1. A Kimeneti **fájl helyének kiválasztása panelen** válassza a Tovább **lehetőséget.**
1. Az Adatok **exportálása panelen** válassza a Tovább **lehetőséget.**

#### <a name="the-table-data-import-wizard"></a>A táblaadatok importálása varázsló

Tábla importálása CSV-fájlból:

1. Kattintson a jobb gombbal az importálni fog adatbázis táblára.
1. Keresse meg és válassza ki az importálni kívánt CSV-fájlt, majd válassza a Tovább **lehetőséget.**
1. Jelölje be a céltáblát (új vagy meglévő), jelölje be vagy törölje a **Truncate** tábla jelölését az importálás előtt, majd válassza a Tovább **lehetőséget.**
1. Válassza ki a kódolást és az importálni kívánt oszlopokat, majd válassza a **Tovább lehetőséget.**
1. Az Adatok **importálása panelen** válassza a Tovább **lehetőséget.** A varázsló importálja az adatokat.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Az SQL-adatok exportálása és importálása varázslók futtatása a Kezelő panelről
Varázslóval exportálhatja vagy importálhatja a MySQL Workbenchből vagy a mysqldump parancsból létrehozott SQL-adatokat. A varázslókat elérheti a  Kezelő panelen, vagy kiválaszthatja a **Főmenü Kiszolgáló** parancsát.

#### <a name="export-data"></a>Adatok exportálása

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Képernyőkép a Kezelő panelről az Adatexportum panel megjelenítéséhez a MySQL Workbenchben.":::

A **MySQL-adatok exportálására** használhatja az Adatok exportálása panelt.

1. A MySQL Workbench Kezelő panelén **válassza** az **Adatexportum lehetőséget.**

1. Az Adatok **exportálása panelen** válassza ki az exportálni kívánt sémákat.
 
   Az egyes sémákhoz kiválaszthatja az exportálni kívánt sémaobjektumokat vagy táblákat. A konfigurációs beállítások közé tartozik az exportálás projektmappába vagy önálló SQL-fájlba, tárolt rutinok és események kiírása, vagy a táblaadatok kihagyása.

   Másik lehetőségként az **Eredményhalmaz** exportálása lehetőség használatával exportálhatja egy adott eredményhalmazt az SQL-szerkesztőben egy másik formátumba, például CSV-fájlba, JSON-fájlba, HTML-be és XML-be.

1. Jelölje ki az exportálni kívánt adatbázis-objektumokat, és konfigurálja a kapcsolódó beállításokat.
1. Az **aktuális objektumok betöltéséhez** válassza a Frissítés lehetőséget.
1. Az exportálási **művelet finomításhoz** válassza a jobb felső sarokban található Speciális beállítások lehetőséget. Hozzáadhat például táblazárolásokat, használhat utasításokat helyett, és idézőjel-azonosítókat a `replace` `insert` visszacsatolási karakterekkel.
1. Az **exportálási folyamat megkezdéséhez** válassza az Exportálás megkezdése lehetőséget.


#### <a name="import-data"></a>Adatok importálása

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Képernyőkép a Kezelő panelről az Adatok importálása panel megjelenítéséhez a MySQL Workbenchben.":::

Az Adatok importálása **panelen** importálhatja vagy visszaállíthatja az exportált adatokat az adatexport műveletekből vagy a mysqldump parancsból.

1. A MySQL Workbench  Kezelő panelén válassza az Adatok **exportálása/visszaállítása lehetőséget.**
1. Válassza ki a projektmappát vagy egy önálló SQL-fájlt,  válassza ki az importálni kívánt sémát, vagy válassza az Új gombot egy új séma definiálásaként.
1. Az **importálási folyamat megkezdéséhez** válassza az Importálás megkezdése lehetőséget.

## <a name="next-steps"></a>Következő lépések
- Egy másik migrálási módszer: MySQL-adatbázis migrálása a MySQL-hez használt Azure-adatbázisba [memóriakép és visszaállítás használatával.](concepts-migrate-dump-restore.md)
- Az adatbázisok Azure Database for MySQL-be való migrálásával kapcsolatos további információkért lásd az adatbázis-migrálási [útmutatót.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)
