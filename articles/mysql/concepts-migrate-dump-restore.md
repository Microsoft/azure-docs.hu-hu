---
title: Migrálás a dump és a Restore használatával – Azure Database for MySQL
description: Ez a cikk két gyakori módszert ismertet a Azure Database for MySQL adatbázisainak biztonsági mentésére és visszaállítására, például a mysqldump, a MySQL Workbench és a PHPMyAdmin eszközzel.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: f21587fe6a48d042ed98c126beb2a7dcaa39b7d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94537917"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>MySQL-adatbázis migrálása a MySQL-hez készült Azure Database-be memóriakép és visszaállítás használatával

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Ez a cikk két gyakori módszert ismertet a Azure Database for MySQL adatbázisainak biztonsági mentésére és visszaállítására
- Memóriakép és visszaállítás a parancssorból (mysqldump használatával)
- Memóriakép és visszaállítás a PHPMyAdmin használatával

Az [adatbázis-áttelepítési útmutatóban](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) részletes információkat találhat az adatbázisok Azure Database for MySQLba való áttelepítésével kapcsolatos információkról és használatáról. Ez az útmutató útmutatást nyújt az Azure-ba való MySQL-áttelepítés sikeres megtervezéséhez és végrehajtásához.

## <a name="before-you-begin"></a>Előkészületek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MySQL kiszolgáló létrehozása – Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- a [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) parancssori segédprogram telepítve van a gépen.
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) vagy más, harmadik féltől származó MySQL-eszköz a dump és a Restore parancsok végrehajtásához.

> [!TIP]
> Ha 1 TBs-nál több adatbázis-mérettel rendelkező nagyméretű adatbázisokat szeretne áttelepíteni, érdemes lehet olyan közösségi eszközöket használni, mint például a **mydumper/myloader** , amely támogatja a párhuzamos exportálást és importálást. Megtudhatja [, hogyan telepíthet át nagyméretű MySQL-adatbázisokat](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).


## <a name="common-use-cases-for-dump-and-restore"></a>Gyakori használati esetek a memóriaképhez és a visszaállításhoz

A leggyakoribb használati esetek a következők:

- **Más felügyelt** szolgáltatótól való áthelyezés – a legtöbb felügyelt szolgáltató nem biztosít hozzáférést a fizikai tárolási fájlhoz biztonsági okokból, így a logikai biztonsági mentés és visszaállítás az egyetlen lehetőség az áttelepítésre.
- **Migrálás a helyszíni környezetből vagy virtuális gépről** – Azure Database for MySQL nem támogatja a fizikai biztonsági másolatok visszaállítását, ami a logikai biztonsági mentést és visszaállítást teszi lehetővé az egyetlen módszerként.
- **A biztonsági mentési tár helyileg redundánsból a Geo-redundáns tárolóba való áthelyezése** – Azure Database for MySQL lehetővé teszi a helyileg redundáns vagy geo-redundáns tárolás konfigurálását a biztonsági mentéshez, csak a kiszolgáló létrehozásakor engedélyezett. A biztonsági mentési tároló redundanciára vonatkozó beállításait a kiszolgáló üzembe helyezése után már nem lehet módosítani. Ahhoz, hogy a biztonsági mentési tárolót helyileg redundáns tárterületről a Geo-redundáns tárolóra helyezze át, a dump és a Restore az egyetlen lehetőség. 
-  Az **alternatív tárolóeszközökről a InnoDB-Azure Database for MySQLra való Migrálás** csak a InnoDB-tárolókat támogatja, és így nem támogatja az alternatív tárolóeszközöket. Ha a táblák más tárolási motorokkal vannak konfigurálva, a Azure Database for MySQLba való áttelepítés előtt alakítsa át őket a InnoDB-motor formátumba.

    Ha például egy WordPress vagy WebApp a MyISAM táblázatokat használja, először alakítsa át ezeket a táblákat úgy, hogy a Azure Database for MySQLra való visszaállítás előtt áttelepíti őket a InnoDB formátumba. Használja a záradékot `ENGINE=InnoDB` az új tábla létrehozásakor használt motor beállításához, majd a visszaállítás előtt vigye át az adatok a kompatibilis táblába.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - A kompatibilitási problémák elkerülése érdekében az adatbázisok biztonsági mentésekor győződjön meg arról, hogy a forrás- és a célrendszerek ugyanazt a MySQL-verziót használják. Ha például a meglévő MySQL-kiszolgáló a 5,7-es verzió, akkor át kell térnie az 5,7-es verzió futtatására konfigurált Azure Database for MySQLra. A `mysql_upgrade` parancs nem működik Azure Database for MySQL-kiszolgálón, és nem támogatott. 
> - Ha frissítenie kell a MySQL-verziókat, először az alacsonyabb verziójú adatbázist a MySQL egy magasabb verziójára exportálhatja a saját környezetében. Ezután futtassa a parancsot `mysql_upgrade` , mielőtt áttelepíti az áttelepítést egy Azure Database for MySQLba.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
A teljesítmény optimalizálása érdekében vegye figyelembe a következő szempontokat a nagyméretű adatbázisok kiírásakor:
-   Az `exclude-triggers` adatbázisok kiírásakor használja a mysqldump kapcsolót. Kihagyhatja a memóriaképből származó eseményindítókat, hogy elkerülje az eseményindító parancsainak égetését az adatok visszaállítása során.
-   A `single-transaction` beállítással beállíthatja a tranzakció-elkülönítési módot ismétlődő olvasásra, és elküldheti a Start Transaction SQL-utasítást a kiszolgálónak az adatok kiírása előtt. Egy tranzakción belül számos tábla kiírásakor a rendszer néhány további tárterületet használ a visszaállítás során. A `single-transaction` beállítás és a `lock-tables` beállítás kölcsönösen kizárható, mert a zárolási táblázatok implicit módon elvégeznek minden függőben lévő tranzakciót. A nagyméretű táblák kiírásához kombinálja a kapcsolót a `single-transaction` `quick` kapcsolóval.
-   Használja a `extended-insert` több sorból álló szintaxist, amely több értéklista használatát is magában foglalja. Ez kisebb memóriaképet eredményez, és felgyorsítja a beszúrást a fájl újratöltése után.
-  A mysqldump használatakor használja az `order-by-primary` adatbázisokat, hogy az elsődleges kulcs sorrendjében legyenek megírtak.
-   A mysqldump használatakor használja az `disable-keys` adatmemóriaképet, hogy letiltsa a külső kulcsokra vonatkozó korlátozásokat a betöltés előtt. A külső kulcsok ellenőrzésének letiltása teljesítménybeli nyereséget biztosít. Engedélyezze a korlátozásokat, és ellenőrizze az adatok betöltését a hivatkozási integritás biztosítása érdekében.
-   Szükség esetén particionált táblákat használjon.
-   Párhuzamosan tölthetők be az adathalmazok. Kerülje a túl sok párhuzamosságot, amelynek hatására elérheti az erőforrás-korlátot, és figyelheti az erőforrásokat a Azure Portal elérhető metrikák használatával.
-   A mysqlpump használatakor használja az `defer-table-indexes` adatbázisokat, így az index létrehozása a táblázatok adatainak betöltése után történik.
-   A `skip-definer` mysqlpump lehetőséggel kihagyhatja a definomabb és az SQL biztonsági záradékokat a Create utasításban a nézetek és a tárolt eljárások számára.  A memóriakép újratöltése után az az alapértelmezett és az SQL biztonsági értékeket használó objektumokat hoz létre.
-   Másolja a biztonságimásolat-fájlokat egy Azure-blobba/-tárolóba, és végezze el a visszaállítást onnan, ami sokkal gyorsabb lehet, mint az interneten keresztüli visszaállítás végrehajtása.

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Adatbázis létrehozása a cél Azure Database for MySQL-kiszolgálón
Hozzon létre egy üres adatbázist azon a célként Azure Database for MySQL kiszolgálón, amelyen át szeretné telepíteni az adatátvitelt. Hozzon létre egy eszközt (például MySQL Workbench vagy mysql.exe) az adatbázis létrehozásához. Az adatbázis neve megegyezik a memóriaképet tartalmazó adatbázis nevével, vagy létrehozhat egy másik nevű adatbázist is.

A csatlakozáshoz keresse meg a kapcsolati adatokat a Azure Database for MySQL **áttekintésében** .

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="A Azure Portal található kapcsolatok adatainak megkeresése":::

Adja hozzá a kapcsolatok adatait a MySQL Workbench-hez.

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="MySQL Workbench-beli kapcsolatok karakterlánca":::

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>A cél Azure Database for MySQL-kiszolgáló előkészítése a gyors adatterhelések eléréséhez
A cél Azure Database for MySQL kiszolgáló gyorsabb adatterhelésre való előkészítéséhez a következő kiszolgálói paramétereket és konfigurációkat módosítani kell.
- max_allowed_packet – állítsa 1073741824-ra (azaz 1GB-ra), hogy megakadályozza a hosszú sorok miatti túlcsordulási problémákat.
- slow_query_log – az OFF érték kikapcsolásával kikapcsolhatja a lassú lekérdezési naplót. Ezzel a művelettel elkerülhető, hogy az adatterhelések során a lassú lekérdezési naplózás okozza a terhelést.
- query_store_capture_mode – állítsa a Nincs értékre a lekérdezési tároló kikapcsolásához. Ezzel a művelettel elkerülhető a lekérdezési tár mintavételi tevékenységei által okozott terhelés.
- innodb_buffer_pool_size – a-kiszolgáló vertikális felskálázása 32 virtuális mag memória optimalizált SKU-ra a portál díjszabási szintjéről az áttelepítés során a innodb_buffer_pool_size növeléséhez. Innodb_buffer_pool_size csak Azure Database for MySQL kiszolgáló számítási felskálázásával növelhető.
- innodb_io_capacity & innodb_io_capacity_max – váltson a Azure Portal kiszolgálói paramétereinek 9000 értékre, hogy javítsa az i/o-kihasználtságot az áttelepítési sebesség optimalizálása érdekében.
- innodb_write_io_threads & innodb_write_io_threads – az áttelepítés gyorsaságának javításához a Azure Portal kiszolgáló paraméterei közül a 4 értékre kell váltania.
- A tárolási rétegek vertikális felskálázása – a Azure Database for MySQL kiszolgáló IOPs fokozatosan növekszik a tárolási rétegek növekedésével. A gyorsabb terhelés érdekében érdemes lehet megnövelni a tárolási szintet, hogy növelje a IOPs kiépített mennyiségét. Ne feledje, hogy a tárterület csak vertikális felskálázásra használható, nem.

Az áttelepítés befejezése után visszaállíthatja a kiszolgálói paramétereket és a számítási rétegek konfigurációját a korábbi értékekre.

## <a name="dump-and-restore-using-mysqldump-utility"></a>Memóriakép és visszaállítás a mysqldump segédprogrammal

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Biztonságimásolat-fájl létrehozása a parancssorból a mysqldump használatával
Ha egy meglévő MySQL-adatbázist szeretne biztonsági másolatot készíteni a helyi helyszíni kiszolgálón vagy egy virtuális gépen, futtassa a következő parancsot:
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

A megadható paraméterek a következők:
- uname Az adatbázis felhasználóneve
- pass Az adatbázis jelszava (Megjegyzés: a-p és a jelszó között nincs szóköz)
- dbname Az adatbázis neve
- [biztonságimentésfájlja. SQL] az adatbázis biztonsági másolatának fájlneve
- [--opt] A mysqldump beállítás

Ha például egy "testdb" nevű adatbázist szeretne biztonsági másolatot készíteni a MySQL-kiszolgálón a (z) "tesztfelhasználó" felhasználónévvel, és nincs jelszó testdb_backup. SQL fájlhoz, használja a következő parancsot. A parancs biztonsági másolatot készít az `testdb` adatbázisról egy nevű fájlba `testdb_backup.sql` , amely tartalmazza az adatbázis újbóli létrehozásához szükséges összes SQL-utasítást. Győződjön meg arról, hogy a (z) "tesztfelhasználó" felhasználónévnek van legalább a SELECT jogosultsága a dömpingelt táblákhoz, a megjelenített nézetek megjelenítése, a dömpingelt eseményindítók aktiválása és a táblák ZÁROLÁSa, ha az--Single-Transaction beállítás nincs használatban.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
Most futtassa a mysqldump-t az adatbázis biztonsági másolatának létrehozásához `testdb`

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Ha a biztonsági mentéshez az adatbázisban megadott táblákat szeretne kiválasztani, sorolja fel a táblák nevét szóközzel elválasztva. Ha például csak a "testdb" tábla1 és table2-táblákról szeretne biztonsági másolatot készíteni, kövesse az alábbi példát:

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Ha egynél több adatbázisról szeretne biztonsági másolatot készíteni, használja az--Database kapcsolót, és sorolja fel az adatbázis nevét szóközzel elválasztva.
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>MySQL-adatbázis visszaállítása a parancssori vagy a MySQL Workbench használatával
Miután létrehozta a céladatbázis-adatbázist, a MySQL-paranccsal vagy a MySQL Workbench használatával visszaállíthatja az adatait az adott újonnan létrehozott adatbázisba a memóriakép fájlból.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Ebben a példában a cél Azure Database for MySQL-kiszolgálón az újonnan létrehozott adatbázisba állítja vissza az adathalmazt.

Íme egy példa arra, hogyan használhatja ezt a **MySQL** -t **egyetlen kiszolgálóhoz** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
Íme egy példa arra, hogyan használhatja ezt a **MySQL** -t a **rugalmas kiszolgálóhoz** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>Memóriakép és visszaállítás a PHPMyAdmin használatával
Kövesse az alábbi lépéseket az adatbázisok a PHPMyadmin használatával történő kiírásához és visszaállításához.

> [!NOTE]
> Egy-kiszolgáló esetén a felhasználónévnek ebben a formátumban kell lennie, username@servername de a rugalmas kiszolgálók esetében csak akkor használhatja a "username" karakterláncot, ha a username@servername rugalmas kiszolgáló esetében a ""-t használja, a kapcsolatok sikertelenek lesznek.

### <a name="export-with-phpmyadmin"></a>Exportálás PHPMyadmin-mel
Az exportáláshoz használhatja az általános eszközt (phpMyAdmin), amelyet esetleg már helyileg telepített a környezetében. MySQL-adatbázis exportálása a PHPMyAdmin használatával:
1. A phpMyAdmin megnyitása.
2. Válassza ki az adatbázist. Kattintson az adatbázis nevére a bal oldali listában.
3. Kattintson az **Exportálás** hivatkozásra. Megjelenik egy új lap az adatbázis memóriaképének megtekintéséhez.
4. Az Exportálás területen kattintson az **összes kijelölése** hivatkozásra az adatbázis tábláinak kiválasztásához.
5. Az SQL-beállítások területen kattintson a megfelelő beállításokra.
6. Kattintson a **Mentés fájlként** lehetőségre és a megfelelő tömörítési lehetőségre, majd kattintson a **Go (ugrás** ) gombra. Ekkor megjelenik egy párbeszédpanel, amely felszólítja a fájl helyi mentésére.

### <a name="import-using-phpmyadmin"></a>Importálás a PHPMyAdmin használatával
Az adatbázis importálása hasonló az exportáláshoz. Hajtsa végre a következő műveleteket:
1. A phpMyAdmin megnyitása.
2. A phpMyAdmin beállítása lapon kattintson a **Hozzáadás** gombra a Azure Database for MySQL-kiszolgáló hozzáadásához. Adja meg a kapcsolat adatait és a bejelentkezési adatokat.
3. Hozzon létre egy megfelelő névvel ellátott adatbázist, és válassza ki a képernyő bal oldalán. A meglévő adatbázis újraírásához kattintson az adatbázis nevére, jelölje be az összes jelölőnégyzetet a táblázat neve mellett, majd válassza a **drop (eldobás** ) lehetőséget a meglévő táblák törléséhez.
4. Az SQL **-hivatkozásra kattintva** megjelenítheti az SQL-parancsokat beírható oldalt, vagy feltöltheti az SQL-fájlt.
5. Az adatbázisfájl megkereséséhez használja a **Tallózás** gombot.
6. Kattintson a **Go (ugrás** ) gombra a biztonsági mentés exportálásához, hajtsa végre az SQL-parancsokat, majd hozza létre újra az adatbázist.

## <a name="known-issues"></a>Ismert problémák
Az ismert problémákkal, tippekkel és trükkökkel kapcsolatban javasoljuk, hogy tekintse meg a [techcommunity blogot](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912).

## <a name="next-steps"></a>Következő lépések
- [Alkalmazások Összekötése Azure Database for MySQLhoz](./howto-connection-string.md).
- Az adatbázisok Azure Database for MySQLre való áttelepítésével kapcsolatos további információkért tekintse meg az [adatbázis-áttelepítési útmutatót](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Ha 1 TBs-nál több adatbázis-mérettel rendelkező nagyméretű adatbázisokat szeretne áttelepíteni, érdemes lehet olyan közösségi eszközöket használni, mint például a **mydumper/myloader** , amely támogatja a párhuzamos exportálást és importálást. Megtudhatja [, hogyan telepíthet át nagyméretű MySQL-adatbázisokat](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
