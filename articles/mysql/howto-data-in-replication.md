---
title: Az adatreplikálás konfigurálása – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL felhőbe irányuló replikálás beállítását ismerteti.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: d5a013fc4e4ef931579da4fa13f400d5f4fcff0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030749"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL konfigurálása felhőbe irányuló replikálás

Ez a cikk azt ismerteti, hogyan állíthatja be a Azure Database for MySQL [felhőbe irányuló replikálás](concepts-data-in-replication.md) a forrás-és a replika-kiszolgálók konfigurálásával. Ez a cikk azt feltételezi, hogy a MySQL-kiszolgálókkal és-adatbázisokkal kapcsolatos korábbi tapasztalatokkal rendelkezik.

> [!NOTE]
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.
>

Ahhoz, hogy replikát hozzon létre a Azure Database for MySQL szolgáltatásban, [felhőbe irányuló replikálás](concepts-data-in-replication.md)  szinkronizálja az adatokat egy helyszíni MySQL-kiszolgálóról, a virtuális gépekről vagy a Felhőbeli adatbázis-szolgáltatásokból. A beérkező adatokra épülő replikáció a MySQL natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört.

A jelen cikkben ismertetett lépések végrehajtása előtt tekintse át az adatok replikálásának [korlátozásait és követelményeit](concepts-data-in-replication.md#limitations-and-considerations) .

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Replikaként használandó MySQL-kiszolgáló létrehozása

1. Új Azure Database for MySQL-kiszolgáló létrehozása

   Hozzon létre egy új MySQL-kiszolgálót (pl. "replica.mysql.database.azure.com"). A kiszolgáló létrehozásához [a Azure Portal használatával Azure Database for MySQL kiszolgáló létrehozása](quickstart-create-mysql-server-database-using-azure-portal.md) című témakörben talál további információt. Ez a kiszolgáló a felhőbe irányuló replikálás replika-kiszolgálója.

   > [!IMPORTANT]
   > A Azure Database for MySQL-kiszolgálót a általános célú vagy a memória optimalizált díjszabási szintjein kell létrehozni.
   >

2. Azonos felhasználói fiókok és megfelelő jogosultságok létrehozása

   A felhasználói fiókok nem replikálódnak a forráskiszolgálóról a másodpéldány-kiszolgálóra. Ha azt tervezi, hogy hozzáférést biztosít a felhasználóknak a másodpéldány-kiszolgálóhoz, manuálisan kell létrehoznia az összes fiókot és a hozzá tartozó jogosultságokat az újonnan létrehozott Azure Database for MySQL kiszolgálón.

3. Adja hozzá a forráskiszolgáló IP-címét a replika tűzfalszabály-szabályaihoz.

   A tűzfalszabályokat az [Azure Portallal](howto-manage-firewall-using-portal.md) vagy az [Azure CLI-vel](howto-manage-firewall-using-cli.md) frissítheti.

## <a name="configure-the-source-server"></a>A forráskiszolgáló konfigurálása

Az alábbi lépéseket követve elkészítheti és konfigurálhatja a helyszínen üzemeltetett MySQL-kiszolgálót egy virtuális gépen vagy más felhőalapú szolgáltató által a felhőbe irányuló replikálás számára üzemeltetett adatbázis-szolgáltatásban. Ez a kiszolgáló a "forrás" az adatreplikációban.

1. A továbblépés előtt tekintse át a [forráskiszolgáló követelményeit](concepts-data-in-replication.md#requirements) .

2. Győződjön meg arról, hogy a forráskiszolgáló engedélyezi a bejövő és a kimenő forgalmat is a 3306-es porton, valamint hogy **nyilvános IP-címmel** rendelkezik, a DNS nyilvánosan elérhető, vagy teljes TARTOMÁNYNEVE (FQDN).

   Tesztelje a kapcsolatot a forráskiszolgálóról egy olyan eszközről való csatlakozásra tett kísérlettel, amely egy másik gépen vagy a Azure Portal elérhető [Azure Cloud Shell](../cloud-shell/overview.md) .

   Ha a szervezete szigorú biztonsági házirendekkel rendelkezik, és nem engedélyezi az összes IP-címet a forráskiszolgálón az Azure-ból a forráskiszolgálóról való kommunikáció engedélyezéséhez, akkor az alábbi parancs segítségével meghatározhatja a MySQL-kiszolgáló IP-címét.

   1. Jelentkezzen be a Azure Database for MySQL egy olyan eszköz használatával, mint a MySQL parancssor.

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
   4. Az IP-cím lekéréséhez futtassa a következő parancsot a ping segédprogramban.

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
  
3. Bináris naplózás bekapcsolása

   A következő parancs futtatásával ellenőrizze, hogy engedélyezve van-e a bináris naplózás a forráson: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Ha a változót [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) "on" értékre adja vissza, a bináris naplózás engedélyezve van a kiszolgálón.
   
    Ha a a `log_bin` "off" értékkel tér vissza, és a forráskiszolgáló helyszíni vagy virtuális gépeken fut, ahol elérheti a konfigurációs fájlt (saját. cnf), kövesse az alábbi lépéseket:
   1. Keresse meg a MySQL konfigurációs fájlját (My. cnf) a forráskiszolgálón. Például: cnf
   2. A fájl szerkesztéséhez nyissa meg a konfigurációs fájlt, és keresse meg a **mysqld** szakaszt.
   3.  A mysqld szakaszban adja hozzá a következő sort
   
       ```bash
       log-bin=mysql-bin.log
       ```
     
   4. A módosítások életbe léptetéséhez indítsa újra a MySQL forráskiszolgáló-kiszolgálót.
   5. A kiszolgáló újraindítása után ellenőrizze, hogy a bináris naplózás engedélyezve van-e a következővel megegyező lekérdezés futtatásával:
   
      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```
   
4. Forráskiszolgáló beállításai

   Felhőbe irányuló replikálás megköveteli `lower_case_table_names` , hogy a paraméter konzisztens legyen a forrás-és a replika-kiszolgálók között. Ez a paraméter a Azure Database for MySQL alapértelmezés szerint 1.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Új replikációs szerepkör létrehozása és az engedélyek beállítása

   Hozzon létre egy felhasználói fiókot a forráskiszolgálón, amely replikációs jogosultságokkal van konfigurálva. Ez az SQL-parancsokkal vagy egy olyan eszközzel végezhető el, mint például a MySQL Workbench. Gondolja át, hogy az SSL-sel való replikálást tervezi-e, mivel ezt a felhasználó létrehozásakor meg kell adni. Tekintse át a MySQL dokumentációját, hogy megtudja, hogyan [adhat hozzá felhasználói fiókokat](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) a forráskiszolgálón.

   A következő parancsokban a létrehozott új replikációs szerepkör bármely gépről elérheti a forrást, nem csak a forrást futtató gépet. Ezt a "syncuser@"% "beállítás megadásával teheti meg a felhasználó létrehozása parancsban. A [fiókok nevének megadásával](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)kapcsolatos további információkért tekintse meg a MySQL dokumentációját.

   **SQL-parancs**

   *Replikáció SSL használatával*

   Az SSL minden felhasználói kapcsolathoz való megköveteléséhez használja a következő parancsot egy felhasználó létrehozásához:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikáció SSL nélkül*

   Ha az SSL nem szükséges minden kapcsolathoz, használja a következő parancsot egy felhasználó létrehozásához:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   A MySQL Workbench replikációs szerepkörének létrehozásához nyissa meg a **felhasználók és jogosultságok** panelt a **felügyeleti** panelen, majd válassza a **fiók hozzáadása** lehetőséget.

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Felhasználók és jogosultságok":::

   Írja be a felhasználónevet a **bejelentkezési név** mezőbe.

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Felhasználó szinkronizálása":::

   Válassza a **felügyeleti szerepkörök** panelt, majd a **globális jogosultságok** listájából válassza ki a **replikálás Slave** elemet. Ezután válassza az **alkalmaz** elemet a replikációs szerepkör létrehozásához.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Replikálási Slave":::

6. A forráskiszolgáló beállítása írásvédett módra

   Az adatbázis kiírásának megkezdése előtt a kiszolgálót csak olvasható módban kell elhelyezni. Amíg csak olvasható módban van, a forrás nem tudja feldolgozni az írási tranzakciókat. Értékelje ki a vállalatra gyakorolt hatást, és szükség esetén ütemezze a csak olvasási időszakot.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Bináris naplófájl nevének és eltolásának beolvasása

   Futtassa a [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) parancsot az aktuális bináris naplófájl nevének és eltolásának meghatározásához.

   ```sql
    show master status;
   ```

   Az eredmények az alábbihoz hasonlóan jelennek meg. Ügyeljen arra, hogy a bináris fájl nevét jegyezze fel, mivel a későbbi lépésekben lesz használva.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Fő állapot eredményei":::

## <a name="dump-and-restore-source-server"></a>Forráskiszolgáló kiírása és visszaállítása

1. Határozza meg, hogy mely adatbázisokat és táblákat kívánja replikálni Azure Database for MySQLba, és végezze el a memóriaképet a forráskiszolgálóról.

    A mysqldump segítségével kitörölheti az adatbázisokat a főkiszolgálóról. Részletekért tekintse meg a következőt: [Dump & Restore](concepts-migrate-dump-restore.md). Szükségtelen a MySQL-függvénytár és a tesztelési könyvtár kiírása.

2. A forráskiszolgáló beállítása írási/olvasási módra.

   Az adatbázis kiírása után állítsa vissza a forrás MySQL-kiszolgálót olvasási/írási módba.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. A memóriakép-fájl visszaállítása az új kiszolgálóra.

   Állítsa vissza a memóriakép-fájlt a Azure Database for MySQL szolgáltatásban létrehozott kiszolgálóra. A dump-fájlok MySQL-kiszolgálóra való visszaállításával kapcsolatban tekintse meg a [dump & Restore](concepts-migrate-dump-restore.md) című témakört. Ha a Memóriakép fájlja nagyméretű, töltse fel az Azure-beli virtuális gépre a replika-kiszolgálóval megegyező régión belül. Állítsa vissza a Azure Database for MySQL-kiszolgálóra a virtuális gépről.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Forrás-és replika-kiszolgálók csatolása a felhőbe irányuló replikálás indításához

1. Forráskiszolgáló beállítása.

   Az összes felhőbe irányuló replikálás függvényt tárolt eljárások hajtják végre. Az összes eljárást [felhőbe irányuló replikálás tárolt eljárásokban](./reference-stored-procedures.md)találja. A tárolt eljárások a MySQL-rendszerhéjban vagy a MySQL Workbenchben is futtathatók.

   Két kiszolgáló összekapcsolásához és a replikáció megkezdéséhez jelentkezzen be a cél replikát futtató kiszolgálóra az Azure DB for MySQL szolgáltatásban, és állítsa be a külső példányt forráskiszolgálóként. Ezt a `mysql.az_replication_change_master` tárolt eljárással teheti meg a MySQL-kiszolgálóhoz készült Azure-adatbázison.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: a forráskiszolgáló állomásneve
   - master_user: a forráskiszolgáló felhasználóneve
   - master_password: a forráskiszolgáló jelszava
   - master_log_file: bináris naplófájl neve a futtatásból `show master status`
   - master_log_pos: a bináris napló pozíciója fut `show master status`
   - master_ssl_ca: HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány környezete. Ha nem használ SSL-t, adja át az üres karakterláncot.

     Azt javasoljuk, hogy ezt a paramétert változóként adja át. További információkért tekintse meg az alábbi példákat.

   > [!NOTE]
   > Ha a forráskiszolgáló egy Azure-beli virtuális gépen található, állítsa "be az Azure-szolgáltatásokhoz való hozzáférés engedélyezése" lehetőséget, hogy a forrás-és a replika-kiszolgálók kommunikálhassanak egymással. Ez a beállítás a **kapcsolatok biztonsági** beállításaiból módosítható. További információ: [Tűzfalszabályok kezelése a portál használatával](howto-manage-firewall-using-portal.md) .

   **Példák**

   *Replikáció SSL használatával*

   A változó `@cert` létrehozásához futtassa a következő MySQL-parancsokat:

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   Az SSL-sel történő replikáció beállítása a "companya.com" tartományban üzemeltetett forráskiszolgáló és a Azure Database for MySQLban üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Replikáció SSL nélkül*

   Az SSL nélküli replikáció beállítása a (z) "companya.com" tartományba tartozó forráskiszolgáló és a Azure Database for MySQL rendszerben üzemeltetett replika-kiszolgáló között történik. Ez a tárolt eljárás fut a replikán.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Szűrés.

   Ha ki szeretné hagyni egyes táblák replikálását a főkiszolgálóról, frissítse a `replicate_wild_ignore_table` kiszolgálói paramétert a másodpéldány-kiszolgálón. Vesszővel tagolt lista használatával több táblázatos mintát is megadhat.

   A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) .

   A paraméter frissítéséhez használhatja a [Azure Portal](howto-server-parameters.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)-t.

3. Replikáció elindítása.

   A `mysql.az_replication_start` replikálás elindításához hívja meg a tárolt eljárást.

   ```sql
   CALL mysql.az_replication_start;
   ```

4. Replikáció állapotának bejelölése.

   A [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) replikálási állapot megtekintéséhez hívja meg a parancsot a replika kiszolgálóján.

   ```sql
   show slave status;
   ```

   Ha az állapota `Slave_IO_Running` és `Slave_SQL_Running` a értéke "igen", és a "0" értékre van állítva `Seconds_Behind_Master` , a replikálás jól működik. `Seconds_Behind_Master` azt jelzi, hogy a replika milyen későn van. Ha az érték nem "0", az azt jelenti, hogy a replika frissítéseket dolgoz fel.

## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

### <a name="stop-replication"></a>Replikáció leállítása

A forrás-és a replika kiszolgáló közötti replikáció leállításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Replikációs kapcsolat eltávolítása

A forrás-és a replika kiszolgáló közötti kapcsolat eltávolításához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Replikálási hiba kihagyása

A replikálási hibák kihagyásához és a replikálás folytatásához használja a következő tárolt eljárást:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Következő lépések

- További információ a Azure Database for MySQL [felhőbe irányuló replikálásról](concepts-data-in-replication.md) .
