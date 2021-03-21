---
title: Az adatreplikálás konfigurálása – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan állíthatja be a felhőbe irányuló replikálást a Azure Database for MariaDBban.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: 67e4da13d6954342b9979eb57a35c812cb63bb3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665123"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>felhőbe irányuló replikálás konfigurálása Azure Database for MariaDB

Ez a cikk azt ismerteti, hogyan állíthatja be a Azure Database for MariaDB [felhőbe irányuló replikálás](concepts-data-in-replication.md) a forrás-és a replika-kiszolgálók konfigurálásával. Ez a cikk azt feltételezi, hogy a MariaDB-kiszolgálókkal és-adatbázisokkal kapcsolatban némi korábbi tapasztalat van.

Ahhoz, hogy replikát hozzon létre a Azure Database for MariaDB szolgáltatásban, [felhőbe irányuló replikálás](concepts-data-in-replication.md) szinkronizálja a helyszíni forrásból, a virtuális gépekből vagy a Cloud Database szolgáltatásokból származó MariaDB-kiszolgáló adatait. A beérkező adatokra épülő replikáció a MariaDB natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A BinLog replikálásával kapcsolatos további tudnivalókért tekintse meg a [BinLog-replikáció áttekintése](https://mariadb.com/kb/en/library/replication-overview/)című témakört.

A jelen cikkben ismertetett lépések végrehajtása előtt tekintse át az adatok replikálásának [korlátozásait és követelményeit](concepts-data-in-replication.md#limitations-and-considerations) .

> [!NOTE]
> Ha a forráskiszolgáló 10,2-es vagy újabb verziójú, javasoljuk, hogy a [globális tranzakció-azonosító](https://mariadb.com/kb/en/library/gtid/)használatával állítsa be felhőbe irányuló replikálás.

> [!NOTE]
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Replikaként használandó MariaDB-kiszolgáló létrehozása

1. Hozzon létre egy új Azure Database for MariaDB-kiszolgálót (például replica.mariadb.database.azure.com). A kiszolgáló a felhőbe irányuló replikálás található replika-kiszolgáló.

    A kiszolgálók létrehozásával kapcsolatos további tudnivalókért lásd: [Azure Database for MariaDB kiszolgáló létrehozása a Azure Portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > A Azure Database for MariaDB-kiszolgálót a általános célú vagy a memória optimalizált díjszabási szintjein kell létrehoznia.

2. Hozzon létre azonos felhasználói fiókokat és megfelelő jogosultságokat.

    A felhasználói fiókok nem replikálódnak a forráskiszolgálóról a másodpéldány-kiszolgálóra. A replika-kiszolgálóhoz való felhasználói hozzáférés biztosításához manuálisan kell létrehoznia az összes fiókot és a megfelelő jogosultságokat az újonnan létrehozott Azure Database for MariaDB-kiszolgálón.

3. Adja hozzá a forráskiszolgáló IP-címét a replika tűzfalszabály-szabályaihoz. 

   A tűzfalszabályokat az [Azure Portallal](howto-manage-firewall-portal.md) vagy az [Azure CLI-vel](howto-manage-firewall-cli.md) frissítheti.

## <a name="configure-the-source-server"></a>A forráskiszolgáló konfigurálása

A következő lépések előkészítik és konfigurálja a helyszínen üzemeltetett MariaDB-kiszolgálót egy virtuális gépen vagy egy felhőalapú adatbázis-szolgáltatásban felhőbe irányuló replikálás számára. A MariaDB-kiszolgáló a forrás a felhőbe irányuló replikálásban.

1. A továbblépés előtt tekintse át az [elsődleges kiszolgálóra vonatkozó követelményeket](concepts-data-in-replication.md#requirements) . 

2. Győződjön meg arról, hogy a forráskiszolgáló engedélyezi a bejövő és a kimenő forgalmat is a 3306-es porton, valamint arról, hogy a forráskiszolgáló **nyilvános IP-címmel** rendelkezik, a DNS nyilvánosan elérhető, vagy rendelkezik teljes tartománynévvel (FQDN). 

   Tesztelje a kapcsolatot a forráskiszolgálóról egy olyan eszközről való csatlakozásra tett kísérlettel, amely egy másik gépen vagy a Azure Portal elérhető [Azure Cloud Shell](../cloud-shell/overview.md) .

   Ha a szervezete szigorú biztonsági házirendekkel rendelkezik, és nem engedélyezi az összes IP-címet a forráskiszolgálón az Azure-ból a forráskiszolgálóról való kommunikáció engedélyezéséhez, az alábbi parancs segítségével meghatározhatja a Azure Database for MariaDB-kiszolgáló IP-címét.

   1. Jelentkezzen be a Azure Database for MariaDB egy olyan eszközzel, mint a MySQL parancssor.
   2. Hajtsa végre az alábbi lekérdezést.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Néhány példa a kimenetre:

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Kilépés a MySQL parancssorból.
   4. Futtassa az alábbi parancsot a ping segédprogramban az IP-cím lekéréséhez.

      ```bash
      ping <output of step 2b>
      ```

      Például:

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Konfigurálja úgy a forráskiszolgáló tűzfalszabályok beállításait, hogy az előző lépés a 3306-es porton lévő, kiszolgált IP-címet tartalmazza.

   > [!NOTE]
   > Ez az IP-cím karbantartási/üzembe helyezési műveletek miatt változhat. Ez a kapcsolódási módszer csak olyan ügyfelek számára érhető el, akik nem engedhetik meg a 3306-es porton az összes IP-cím engedélyezését.

3. A bináris naplózás bekapcsolása.

    Ha szeretné megtekinteni, hogy engedélyezve van-e a bináris naplózás az elsődleges számítógépen, írja be a következő parancsot:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a változó [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) visszaadja az értéket `ON` , a bináris naplózás engedélyezve van a kiszolgálón.

   Ha `log_bin` az értéket adja vissza `OFF` , szerkessze a **My. cnf** fájlt úgy, hogy az `log_bin=ON` bekapcsolja a bináris naplózást. Indítsa újra a kiszolgálót a módosítás érvénybe léptetéséhez.

4. A forráskiszolgáló beállításainak konfigurálása.

    Felhőbe irányuló replikálás megköveteli `lower_case_table_names` , hogy a paraméter konzisztens legyen a forrás-és a replika-kiszolgálók között. A `lower_case_table_names` paraméter `1` alapértelmezett értéke Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Hozzon létre egy új replikációs szerepkört, és állítson be engedélyeket.

   Hozzon létre egy olyan felhasználói fiókot a forráskiszolgálón, amely replikációs jogosultságokkal van konfigurálva. Az SQL-parancsok vagy a MySQL Workbench használatával hozhat létre fiókot. Ha az SSL-sel való replikálást tervezi, ezt a felhasználói fiók létrehozásakor kell megadnia.

   Ha szeretné megtudni, hogyan adhat hozzá felhasználói fiókokat a forráskiszolgálón, tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/library/create-user/).

   Az alábbi parancsokkal az új replikációs szerepkör bármely gépről elérheti a forrást, nem csak a forrást futtató gépet. Ehhez a hozzáféréshez a **\@ (z) "%" syncuser** kell megadnia a parancsban a felhasználó létrehozásához.

   A MariaDB-dokumentációval kapcsolatos további tudnivalókért tekintse [meg a fiókok nevének megadásával](https://mariadb.com/kb/en/library/create-user/#account-names)foglalkozó témakört.

   **SQL-parancs**

   - Replikáció SSL használatával

       Ha az SSL használatát szeretné megkövetelni az összes felhasználói kapcsolathoz, adja meg a következő parancsot egy felhasználó létrehozásához:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikáció SSL nélkül

       Ha az SSL nem szükséges minden kapcsolathoz, adja meg a következő parancsot egy felhasználó létrehozásához:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   A MySQL Workbench replikációs szerepkörének létrehozásához a **felügyelet** ablaktáblán válassza a **felhasználók és jogosultságok** lehetőséget. Ezután válassza a **fiók hozzáadása** lehetőséget.

   ![Felhasználók és jogosultságok](./media/howto-data-in-replication/users_privileges.png)

   Adjon meg egy felhasználónevet a **bejelentkezési név** mezőben.

   ![Felhasználó szinkronizálása](./media/howto-data-in-replication/syncuser.png)

   Válassza ki a **felügyeleti szerepkörök** panelt, majd a **globális jogosultságok** listájában válassza ki a **replikálási Slave** elemet. A replikációs szerepkör létrehozásához kattintson az **alkalmaz** gombra.

   ![Replikálási Slave](./media/howto-data-in-replication/replicationslave.png)

6. A forráskiszolgáló beállítása írásvédett módra.

   Az adatbázisok kiírása előtt a kiszolgálót csak olvasható módban kell elhelyezni. Amíg csak olvasható módban van, a forrás nem dolgozhat fel írási tranzakciókat. Az üzleti hatás elkerülése érdekében ütemezze a csak olvasási időszakot egy off-Peak idő alatt.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Az aktuális bináris naplófájl nevének és eltolásának beolvasása.

   Az aktuális bináris naplófájl nevének és eltolásának meghatározásához futtassa a parancsot [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .

   ```sql
   show master status;
   ```

   Az eredmények az alábbi táblázathoz hasonlóak:

   ![Fő állapot eredményei](./media/howto-data-in-replication/masterstatus.png)

   Jegyezze fel a bináris fájl nevét, mert a későbbi lépésekben lesz használva.

8. A GTID pozíciójának beolvasása (nem kötelező, a GTID való replikáláshoz szükséges).

   Futtassa a függvényt a [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) megfelelő BinLog-fájlnév és ELTOLÁS GTID pozíciójának lekéréséhez.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```

## <a name="dump-and-restore-the-source-server"></a>A forráskiszolgáló kiírása és visszaállítása

1. A forráskiszolgáló összes adatbázisának kiírása.

   A mysqldump használatával az összes adatbázist kiírja a forráskiszolgálóról. Nem szükséges a MySQL-függvénytár és a tesztelési könyvtár kiírása.

    További információ: [memóriakép és visszaállítás](howto-migrate-dump-restore.md).

2. A forráskiszolgáló beállítása írási/olvasási módra.

   Az adatbázis kiírása után állítsa vissza a forrás MariaDB-kiszolgálót olvasási/írási módra.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Állítsa vissza a memóriakép fájlját az új kiszolgálóra.

   Állítsa vissza a memóriakép-fájlt a Azure Database for MariaDB szolgáltatásban létrehozott kiszolgálóra. A dump-fájlok MariaDB-kiszolgálóra való visszaállításával kapcsolatban lásd: [dump & Restore](howto-migrate-dump-restore.md) .

   Ha a memóriakép nagy méretű, töltse fel azt egy Azure-beli virtuális gépre, amely a replika-kiszolgálóval azonos régióban található. Állítsa vissza a Azure Database for MariaDB-kiszolgálóra a virtuális gépről.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>A forrás-és a replika-kiszolgálók összekapcsolása a felhőbe irányuló replikálás elindításához

1. Állítsa be a forráskiszolgáló-kiszolgálót.

   Az összes felhőbe irányuló replikálás függvényt tárolt eljárások hajtják végre. Az összes eljárást [felhőbe irányuló replikálás tárolt eljárásokban](reference-stored-procedures.md)találja. A tárolt eljárások a MySQL-rendszerhéjban vagy a MySQL Workbenchben is futtathatók.

   Két kiszolgáló összekapcsolásához és a replikáció megkezdéséhez jelentkezzen be a cél másodpéldány-kiszolgálóra az Azure DB for MariaDB szolgáltatásban. Ezután állítsa a külső példányt forráskiszolgálóként a `mysql.az_replication_change_master` vagy `mysql.az_replication_change_master_with_gtid` tárolt eljárás használatával a MariaDB-kiszolgálóhoz készült Azure-adatbázison.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   vagy

   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: a forráskiszolgáló állomásneve
   - master_user: a forráskiszolgáló felhasználóneve
   - master_password: a forráskiszolgáló jelszava
   - master_log_file: bináris naplófájl neve a futtatásból `show master status`
   - master_log_pos: a bináris napló pozíciója fut `show master status`
   - master_gtid_pos: a GTID pozíciója fut `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány környezete. Ha nem használ SSL-t, adjon meg egy üres karakterláncot. *


    * Javasoljuk, hogy a master_ssl_ca paramétert változóként adja át. További információkért tekintse meg az alábbi példákat.

   **Példák**

   - Replikáció SSL használatával

       Hozza létre a változót a `@cert` következő parancsok futtatásával:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Az SSL-sel történő replikáció beállítása a tartomány companya.com üzemeltetett forráskiszolgáló és a Azure Database for MariaDBban üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```

   - Replikáció SSL nélkül

       Az SSL nélküli replikáció beállítása a tartomány companya.com üzemeltetett forráskiszolgáló és a Azure Database for MariaDBban üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Replikáció elindítása.

   A `mysql.az_replication_start` replikálás elindításához hívja meg a tárolt eljárást.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Replikáció állapotának bejelölése.

   A [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) replikálási állapot megtekintéséhez hívja meg a parancsot a replika kiszolgálóján.

   ```sql
   show slave status;
   ```

   Ha `Slave_IO_Running` a és az `Slave_SQL_Running` állapotban van `yes` , és a értéke `Seconds_Behind_Master` , a `0` replikálás működik. `Seconds_Behind_Master` azt jelzi, hogy a replika milyen későn van. Ha az érték nem `0` , akkor a replika frissíti a frissítéseket.

4. Frissítse a megfelelő kiszolgálói változókat az adatreplikálás biztonságosabbá tételéhez (csak a GTID nélküli replikáláshoz szükséges).

    A MariaDB-ben a natív replikálás korlátozása miatt  [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) a GTID-forgatókönyv nélkül kell beállítania és konfigurálnia [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) a replikálást.

    Ellenőrizze a replika kiszolgálójának `sync_master_info` és `sync_relay_log_info` változóinak ellenőrzését, és győződjön meg arról, hogy az adatreplikáció stabil, és állítsa be a változókat a következőre: `1` .

## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A forrás-és a replika kiszolgáló közötti replikáció leállításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Replikációs kapcsolat eltávolítása

A forrás-és a replika kiszolgáló közötti kapcsolat eltávolításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>A replikálási hiba kihagyása

A replikálási hibák kihagyásához és a replikáció engedélyezéséhez használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Következő lépések

További információ a Azure Database for MariaDB [felhőbe irányuló replikálásról](concepts-data-in-replication.md) .
