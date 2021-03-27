---
title: Migrálás a dump és a Restore-Azure Database for MariaDB
description: Ez a cikk két gyakori módszert ismertet az adatbázisok MariaDB való biztonsági mentésére és visszaállítására az Azure Database-ben az olyan eszközök használatával, mint a mysqldump, a MySQL Workbench és a phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628220"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>A MariaDB-adatbázis migrálása Azure-adatbázisba MariaDB a dump és a Restore használatával

Ez a cikk két gyakori módszert ismertet az adatbázisok biztonsági mentésére és visszaállítására az Azure-adatbázisban a MariaDB:
- Memóriakép és visszaállítás parancssori eszköz használatával (mysqldump használatával) 
- Memóriakép és visszaállítás a phpMyAdmin használatával

## <a name="prerequisites"></a>Előfeltételek
Az adatbázis áttelepítésének megkezdése előtt tegye a következőket:
- Hozzon létre egy [Azure Database for MariaDB Server-Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).
- Telepítse a [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) parancssori segédprogramot.
- Töltse le és telepítse a [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) vagy más, harmadik féltől származó MySQL-eszközt a dump és a Restore parancsok futtatásához.

## <a name="use-common-tools"></a>Gyakori eszközök használata
A MySQL Workbench és a mysqldump gyakori segédprogramok és eszközök használatával távolról csatlakozhatnak az Azure-adatbázishoz, és visszaállíthatják azokat a MariaDB. Ezeket az eszközöket az ügyfélszámítógépen internetkapcsolattal csatlakoztathatja a MariaDB készült Azure-adatbázishoz. Az SSL-titkosítású kapcsolat használata ajánlott biztonsági eljárásként. További információ: [SSL-kapcsolat konfigurálása Azure Database for MariaDBban](concepts-ssl-connection-security.md). Az Azure Database for MariaDB-adatbázisba való áttelepítéskor semmilyen speciális Felhőbeli helyre nem kell áthelyeznie a memóriakép fájljait. 

## <a name="common-uses-for-dump-and-restore"></a>A dump és a Restore gyakori felhasználási módjai
A MySQL segédprogramok, például a mysqldump és a mysqlpump segítségével számos gyakori forgatókönyvben elvégezheti az adatbázisok kiírását és betöltését egy Azure Database for MariaDB-kiszolgálóra. 

- Adatbázis-memóriaképek használata a teljes adatbázis áttelepítésekor. Ezt a javaslatot akkor kell megtartani, ha nagy mennyiségű adatátvitelt végez, vagy ha az élő webhelyekhez vagy alkalmazásokhoz szeretné csökkenteni a szolgáltatás megszakítását. 
-  Győződjön meg arról, hogy az adatbázis összes táblája a InnoDB Storage motort használja, amikor az Azure Database-be MariaDB-ba tölti be az adatait. A Azure Database for MariaDB csak a InnoDB tároló motort támogatja, és más tárolóeszközöket sem. Ha a táblák más tárolóeszközökkel vannak konfigurálva, alakítsa át őket a InnoDB-motor formátumba, mielőtt áttelepíti azokat a MariaDB készült Azure-adatbázisba.
   
   Ha például egy WordPress-alkalmazással vagy egy MyISAM-táblázatokat használó webalkalmazással rendelkezik, először alakítsa át ezeket a táblákat úgy, hogy azokat InnoDB formátumba telepíti át, mielőtt visszaállítja őket az Azure Database for MariaDB szolgáltatásba. A záradék használatával `ENGINE=InnoDB` állítsa be a motort egy új tábla létrehozásához, majd a visszaállítás előtt vigye át az adatok a kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Ha el szeretné kerülni a kompatibilitási problémákat az adatbázisok közzétételekor, győződjön meg arról, hogy a MariaDB azonos verzióját használja a forrás-és a célszámítógépeken. Ha például a meglévő MariaDB-kiszolgáló a 10,2-es verzió, akkor a 10,2-es verzióra konfigurált MariaDB-re kell migrálni az Azure-adatbázisba. A `mysql_upgrade` parancs nem működik Azure Database for MariaDB-kiszolgálón, és nem támogatott. Ha frissítenie kell a MariaDB-verziók között, először a korábbi verziójú adatbázist kell kiadnia vagy exportálnia a MariaDB egy újabb verziójára a saját környezetében. Ezután futtathatja `mysql_upgrade` az MariaDB Azure-adatbázisba való áttelepítését.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
A nagyméretű adatbázisok kiírásakor a teljesítmény optimalizálása érdekében vegye figyelembe a következő szempontokat:
-   Használja a `exclude-triggers` mysqldump kapcsolót. Kizárhatja az eseményindítókat a memóriaképből, így elkerülhető, hogy az eseményindító parancsai tüzet az adatok visszaállítása során. 
-   Az `single-transaction` adatok kiírása előtt állítsa be a tranzakció-elkülönítési módot ismétlődő olvasási értékre, és küldje el a Start Transaction SQL-utasítást a kiszolgálónak. Egy tranzakción belül számos tábla kiírásakor a rendszer néhány további tárterületet használ a visszaállítás során. A `single-transaction` beállítás és a `lock-tables` beállítás kölcsönösen kizárható. Ennek az az oka, hogy a ZÁROLÁSi táblázatok implicit módon véglegesítik a függőben lévő tranzakciókat. A nagyméretű táblák kiírásához kombinálja a kapcsolót a `single-transaction` `quick` kapcsolóval. 
-   Használja a `extended-insert` több sorból álló szintaxist, amely több értéklista használatát is magában foglalja. Ez a módszer kisebb memóriaképet eredményez, és felgyorsítja a beszúrást a fájl újratöltése során.
-  Az `order-by-primary` adatbázisok kiírásakor használja a mysqldump kapcsolót, hogy az adattábla elsődleges kulcs szerinti sorrendben legyen megírt.
-   Használja az `disable-keys` mysqldump kapcsolót az adatfeldolgozás során, hogy letiltsa a külső kulcsokra vonatkozó korlátozásokat a betöltés előtt. A külső kulcsok ellenőrzésének letiltása segít a teljesítmény javításában. Engedélyezze a korlátozásokat, és ellenőrizze az adatok betöltését a hivatkozási integritás biztosítása érdekében.
-   Szükség esetén particionált táblákat használjon.
-   Párhuzamosan tölthetők be az adathalmazok. Kerülje a túl sok párhuzamosságot, ami azt eredményezheti, hogy elér egy erőforrás-korlátot, és figyeli az erőforrásokat a Azure Portal elérhető metrikák használatával. 
-   Az `defer-table-indexes` adatbázisok kiírásakor használja a mysqlpump kapcsolót, így az index létrehozása a tábla adatainak betöltése után történik.
-   Másolja a biztonságimásolat-fájlokat egy Azure Blob-tárolóba, és végezze el a visszaállítást onnan. Ennek a megközelítésnek sokkal gyorsabbnak kell lennie, mint a visszaállításnak az interneten keresztüli végrehajtása.

## <a name="create-a-backup-file"></a>Biztonságimásolat-fájl létrehozása

Ha egy meglévő MariaDB-adatbázisról szeretne biztonsági másolatot készíteni a helyi helyszíni kiszolgálón vagy egy virtuális gépen, futtassa a következő parancsot a mysqldump használatával: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

A megadható paraméterek a következők:
- *\<uname>*: Az adatbázis felhasználóneve 
- *\<pass>*: Az adatbázis jelszava (ne feledje, hogy a-p és a jelszó között nincs szóköz) 
- *\<dbname>*: Az adatbázis neve 
- *\<backupfile.sql>*: Az adatbázis biztonsági másolatának fájlneve 
- *\<--opt>*: A mysqldump beállítás 

Ha például egy *testdb* nevű adatbázist szeretne biztonsági másolatot készíteni a MariaDB-kiszolgálón a *tesztfelhasználó* felhasználónévvel, és a jelszó nélkül nem testdb_backup. SQL fájlt, használja a következő parancsot. A parancs biztonsági másolatot készít az `testdb` adatbázisról egy nevű fájlba `testdb_backup.sql` , amely tartalmazza az adatbázis újbóli létrehozásához szükséges összes SQL-utasítást. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Ha ki szeretne választani egy olyan táblát, amelyről biztonsági másolatot szeretne készíteni az adatbázisban, sorolja fel a táblák nevét, szóközzel elválasztva. Ha például csak a tábla1-és table2-táblákról szeretne biztonsági másolatot készíteni a *testdb*, kövesse az alábbi példát: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Ha egynél több adatbázisról szeretne biztonsági másolatot készíteni, használja az--Database kapcsolót, és sorolja fel az adatbázisok nevét szóközzel elválasztva. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Adatbázis létrehozása a célkiszolgálón
Hozzon létre egy üres adatbázist azon a célként Azure Database for MariaDB kiszolgálón, amelyen át szeretné telepíteni az adatátvitelt. Hozzon létre egy eszközt, például a MySQL Workbench alkalmazást az adatbázis létrehozásához. Az adatbázis neve megegyezik a dömpingelt adattartalommal, vagy létrehozhat egy másik nevet tartalmazó adatbázist is.

A csatlakozáshoz keresse meg a kapcsolati adatokat az Azure Database for MariaDB **Áttekintés** paneljén.

![Képernyőkép az Azure Database for MariaDB-kiszolgáló áttekintés paneljéről a Azure Portal.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

A MySQL Workbenchben adja meg a kapcsolatok adatait.

![Képernyőfelvétel a MySQL Workbench MySQL Connections paneljéről.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>A MariaDB-adatbázis visszaállítása
Miután létrehozta a céladatbázis-adatbázist, a MySQL-paranccsal vagy a MySQL Workbench-vel állíthatja vissza az adatait az újonnan létrehozott adatbázisba a memóriakép fájlból.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

Ebben a példában a cél Azure Database for MariaDB-kiszolgálón lévő, újonnan létrehozott adatbázisba állítja vissza az adatkészletet.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>A MariaDB-adatbázis exportálása a phpMyAdmin használatával

Az exportáláshoz használhatja az általános eszköz phpMyAdminot, amely már helyileg is telepíthető a környezetében. A MariaDB-adatbázis exportálásához tegye a következőket:
1. A phpMyAdmin megnyitása.
1. A bal oldali ablaktáblán válassza ki az adatbázist, majd válassza az **Exportálás** hivatkozást. Megjelenik egy új lap az adatbázis memóriaképének megtekintéséhez.
1. Az **Exportálás** területen válassza az **összes kijelölése** hivatkozást az adatbázis tábláinak kiválasztásához. 
1. Az **SQL-beállítások** területen válassza ki a megfelelő beállításokat. 
1. Válassza a **Mentés** fájlként lehetőséget és a megfelelő tömörítési lehetőséget, majd kattintson az **Indítás** gombra. A parancssorban mentse helyileg a fájlt.

## <a name="import-your-database-by-using-phpmyadmin"></a>Adatbázis importálása a phpMyAdmin használatával

Az importálási folyamat hasonló az exportálási folyamathoz. Tegye a következőket:
1. A phpMyAdmin megnyitása. 
1. A phpMyAdmin telepítése lapon válassza a **Hozzáadás** lehetőséget a Azure Database for MariaDB-kiszolgáló hozzáadásához. 
1. Adja meg a kapcsolat adatait és a bejelentkezési adatokat.
1. Hozzon létre egy megfelelő névvel ellátott adatbázist, majd válassza ki a bal oldali ablaktáblán. A meglévő adatbázis újraírásához jelölje ki az adatbázis nevét, jelölje be az összes jelölőnégyzetet a táblázat neve mellett, majd válassza a **drop (eldobás** ) lehetőséget a meglévő táblák törléséhez. 
1. Válassza ki az **SQL** -hivatkozást az oldal megjelenítéséhez, ahol megadhatja az SQL-parancsokat, vagy feltöltheti az SQL-fájlt. 
1. Az adatbázisfájl megkereséséhez kattintson a **Tallózás** gombra. 
1. Kattintson a **Go (ugrás** ) gombra a biztonsági mentés exportálásához, hajtsa végre az SQL-parancsokat, majd hozza létre újra az adatbázist.

## <a name="next-steps"></a>Következő lépések
- Az [alkalmazások összekapcsolhatók az Azure-adatbázissal a MariaDB](./howto-connection-string.md).
