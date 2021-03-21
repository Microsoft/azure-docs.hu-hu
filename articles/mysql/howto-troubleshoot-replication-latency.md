---
title: Replikációs késés – Azure Database for MySQL
description: Megtudhatja, hogyan oldhatja meg a replikációs késést Azure Database for MySQL olvasási replikák használatával.
keywords: MySQL, hibakeresés, replikálás késése másodpercben
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: 92513a8c24b5106e3a59c8cfa4d743e900b957bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249771"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>A replikáció késésének elhárítása az Azure Database for MySQL-ben

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

Az [olvasási replika](concepts-read-replicas.md) funkció lehetővé teszi az adatok replikálását egy Azure Database for MySQL-kiszolgálóról egy írásvédett replika kiszolgálóra. A számítási feladatok felskálázása az alkalmazásból a replika kiszolgálókra irányuló olvasási és jelentéskészítési lekérdezések útválasztásával végezhető el. Ez a beállítás csökkenti a forráskiszolgáló terhelését. Emellett az alkalmazás teljes teljesítményét és késését is javítja, ahogy méretezi.

A replikák aszinkron módon frissülnek a MySQL-motor natív bináris naplójának (BinLog) fájljának pozíció-alapú replikációs technológiájának használatával. További információ: [MySQL BinLog-fájl pozíció alapú replikációs konfiguráció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

A másodlagos olvasási replikák replikációs késése számos tényezőtől függ. Ezek a tényezők többek között az alábbiakra korlátozódnak:

- Hálózati késés.
- Tranzakciós kötet a forráskiszolgálón.
- A forráskiszolgáló és a másodlagos olvasási replika kiszolgáló számítási szintje.
- A forráskiszolgálón és a másodlagos kiszolgálón futó lekérdezések.

Ebből a cikkből megtudhatja, hogyan lehet elhárítani a Azure Database for MySQL replikációs késését. Azt is megismerheti, hogy a replika kiszolgálók nagyobb replikációs késésének gyakori okai.

> [!NOTE]
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.
>

## <a name="replication-concepts"></a>Replikációs fogalmak

Ha engedélyezve van egy bináris napló, a forráskiszolgáló véglegesített tranzakciókat ír a bináris naplóba. A rendszer a bináris naplót használja a replikáláshoz. Alapértelmezés szerint be van kapcsolva minden olyan újonnan kiosztott kiszolgáló számára, amely akár 16 TB tárterületet is támogat. A replika-kiszolgálókon két szál fut minden egyes másodpéldány-kiszolgálón. Az egyik szál az *IO-szál*, a másik pedig az *SQL-szál*:

- Az i/o-szál csatlakozik a forráskiszolgálón, és a frissített bináris naplókat kéri. Ez a szál a bináris napló frissítéseit kapja meg. Ezeket a frissítéseket egy replika-kiszolgálóra menti a rendszer a *Relay-napló* nevű helyi naplóban.
- Az SQL-szál beolvassa a Relay-naplót, majd alkalmazza az adatmódosításokat a replika-kiszolgálókon.

## <a name="monitoring-replication-latency"></a>Replikáció késésének figyelése

A Azure Database for MySQL a [Azure monitor](concepts-monitoring.md)a replikálás késésének mérőszámát adja meg másodpercben. Ez a metrika csak olvasási replika kiszolgálókon érhető el. Ezt a MySQL-ben elérhető seconds_behind_master metrika számítja ki. 

A replikációs késés okainak megismeréséhez a [MySQL Workbench](connect-workbench.md) vagy a [Azure Cloud Shell](https://shell.azure.com)használatával kapcsolódjon a másodpéldány-kiszolgálóhoz. Ezután futtassa a következő parancsot.

>[!NOTE]
> A kódban cserélje le a példában szereplő értékeket a replika-kiszolgáló nevére és a rendszergazdai felhasználónevére. A rendszergazdai felhasználónévhez a `@\<servername>` Azure Database for MySQL szükséges.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

A tapasztalatok a Cloud Shell terminálon láthatók:

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

Ugyanebben a Cloud Shell terminálban futtassa a következő parancsot:

```
mysql> SHOW SLAVE STATUS;
```

A következő egy tipikus kimenet:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Replikáció késésének figyelése":::

A kimenet számos információt tartalmaz. Általában csak az alábbi táblázatban leírt sorokra kell összpontosítania.

|Metric|Leírás|
|---|---|
|Slave_IO_State| Az i/o-szál aktuális állapotát jelöli. Az állapot általában "Várakozás a főkiszolgáló számára esemény küldésére", ha a forrás (főkiszolgáló) kiszolgáló szinkronizálást végez. Egy olyan állapot, mint a "Csatlakozás a főkiszolgálóhoz" érték azt jelzi, hogy a replika elvesztette a kapcsolatot a forráskiszolgálóról. Győződjön meg arról, hogy a forráskiszolgáló fut, vagy ellenőrizze, hogy a tűzfal blokkolja-e a kapcsolódást.|
|Master_Log_File| Azt a bináris naplófájlt jelöli, amelyre a forráskiszolgáló írása történik.|
|Read_Master_Log_Pos| Azt jelzi, hogy a forráskiszolgáló hol van írva a bináris naplófájlban.|
|Relay_Master_Log_File| Azt a bináris naplófájlt jelöli, amelyet a replika kiszolgáló a forráskiszolgálóról olvas.|
|Slave_IO_Running| Azt jelzi, hogy fut-e az IO-szál. Az értéknek a értéket kell tartalmaznia `Yes` . Ha az érték `NO` , akkor a replikálás valószínűleg megszakad.|
|Slave_SQL_Running| Azt jelzi, hogy fut-e az SQL-szál. Az értéknek a értéket kell tartalmaznia `Yes` . Ha az érték `NO` , akkor a replikálás valószínűleg megszakad.|
|Exec_Master_Log_Pos| A replika által alkalmazott Relay_Master_Log_File pozícióját jelzi. Ha késés van, akkor ennek a pozíciónak kisebbnek kell lennie, mint Read_Master_Log_Pos.|
|Relay_Log_Space|Megadja a Relay-napló méretének felső határát. A méretet a következő lekérdezéssel tekintheti meg: `SHOW GLOBAL VARIABLES` `relay_log_space_limit` .|
|Seconds_Behind_Master| A replikációs késést jeleníti meg másodpercben.|
|Last_IO_Errno|Az i/o-szál hibakódját jeleníti meg, ha van ilyen. További információ ezekről a kódokról: [MySQL-kiszolgáló hibaüzenetének referenciája](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_IO_Error| Az i/o-szál hibaüzenetét jeleníti meg, ha van ilyen.|
|Last_SQL_Errno|Megjeleníti az SQL-szál hibakódját, ha van ilyen. További információ ezekről a kódokról: [MySQL-kiszolgáló hibaüzenetének referenciája](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Az SQL-szál hibaüzenetét jeleníti meg, ha van ilyen.|
|Slave_SQL_Running_State| Az aktuális SQL-szál állapotát jelzi. Ebben az állapotban `System lock` normális. Emellett a állapota is normális `Waiting for dependent transaction to commit` . Ez az állapot azt jelzi, hogy a replika arra vár, hogy a forráskiszolgáló frissítse a véglegesített tranzakciókat.|

Ha Slave_IO_Running `Yes` , és Slave_SQL_Running `Yes` , akkor a replikáció rendben fut. 

Ezután vizsgálja meg Last_IO_Errno, Last_IO_Error, Last_SQL_Errno és Last_SQL_Error.  Ezek a mezők az SQL-szál leállítását okozó legújabb hiba számát és hibaüzenetét jelenítik meg. A hiba száma `0` és az üres üzenet azt jelenti, hogy nincs hiba. Vizsgálja meg a nem nulla hibaértéket a [MySQL-kiszolgáló hibaüzenet-referenciájában](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)található hibakód ellenőrzésével.

## <a name="common-scenarios-for-high-replication-latency"></a>Gyakori forgatókönyvek a nagy replikációs késéshez

A következő fejezetek olyan forgatókönyveket mutatnak be, amelyekben a nagy replikációs késés gyakori.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Hálózati késés vagy nagy CPU-felhasználás a forráskiszolgálón

Ha a következő értékeket látja, a replikálás késése valószínűleg nagy hálózati késés vagy a forráskiszolgáló magas CPU-kihasználtsága okozhatja.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

Ebben az esetben az IO-szál fut, és a forráskiszolgálón várakozik. A forráskiszolgáló már a 20. számú bináris naplófájlba lett írva. A replika csak a 10-es számú fájlt fogadta. Ebben a forgatókönyvben a nagy replikációs késés elsődleges tényezője a forráskiszolgáló hálózati sebessége vagy magas CPU-kihasználtsága.  

Az Azure-ban a régión belüli hálózati késés általában ezredmásodpercben mérhető. A régiók között a késés tartománya ezredmásodperctől másodpercig terjed.

A legtöbb esetben az IO-szálak és a forráskiszolgáló közötti kapcsolat késleltetését a forráskiszolgáló magas CPU-kihasználtsága okozza. Az IO-szálak feldolgozása lassan történik. A probléma észleléséhez Azure Monitor használatával ellenőrizze a processzor kihasználtságát és a forráskiszolgáló egyidejű kapcsolatainak számát.

Ha nem látja a magas CPU-kihasználtságot a forráskiszolgálón, lehetséges, hogy a probléma hálózati késéssel jár. Ha a hálózati késés hirtelen rendellenesen magas, tekintse meg az ismert problémákkal és kimaradásokkal kapcsolatos [Azure-állapot lapot](https://status.azure.com/status) . 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Nagy mennyiségű tranzakció a forráskiszolgálón

Ha a következő értékek láthatók, akkor a forráskiszolgáló nagy mennyiségű tranzakciója valószínűleg a replikálás késését okozza. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

A kimenet azt mutatja, hogy a replika lekérheti a bináris naplót a forráskiszolgáló mögött. A replika IO-szála azonban azt jelzi, hogy a továbbítási napló területe már megtelt.

A hálózati sebesség nem okozza a késést. A replika megpróbál felzárkózni. A frissített bináris napló mérete azonban meghaladja a továbbítási napló területének felső korlátját.

A probléma megoldásához engedélyezze a [lassú lekérdezési naplót](concepts-server-logs.md) a forráskiszolgálón. Lassú lekérdezési naplók használatával azonosíthatja a forráskiszolgáló hosszan futó tranzakcióit. Ezután hangolja az azonosított lekérdezéseket, hogy csökkentse a késést a kiszolgálón. 

A rendezés replikációs késését általában a forráskiszolgáló adatterhelése okozza. Ha a forráskiszolgáló hetente vagy havonta töltődik be, a replikációs késés sajnos elkerülhetetlen. A replika-kiszolgálók végül a forráskiszolgáló adatterhelésének befejeződése után fognak megjelenni.

### <a name="slowness-on-the-replica-server"></a>A replika-kiszolgáló lassúsága

Ha a következő értékeket veszi figyelembe, előfordulhat, hogy a probléma a másodpéldány-kiszolgálón található.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Ebben az esetben a kimenet azt mutatja, hogy az i/o-szál és az SQL-szál is jól működik. A replika beolvassa ugyanazt a bináris naplófájlt, amelyet a forráskiszolgáló ír. A replika-kiszolgáló néhány késése azonban ugyanazt a tranzakciót tükrözi a forráskiszolgálón.

Az alábbi szakaszok az ilyen típusú késés gyakori okait ismertetik.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>Nincs elsődleges kulcs vagy egyedi kulcs egy táblában

A Azure Database for MySQL sor alapú replikálást használ. A forráskiszolgáló eseményeket ír a bináris naplóba, és rögzíti a módosításokat az egyes táblázat soraiban. Az SQL-szál ezután replikálja ezeket a módosításokat a másodpéldány-kiszolgálón lévő megfelelő táblázat soraiba. Ha egy tábla nem rendelkezik elsődleges kulccsal vagy egyedi kulccsal, az SQL-szál megvizsgálja a cél tábla összes sorát a módosítások alkalmazásához. Ez a vizsgálat replikációs késést eredményezhet.

A MySQL-ben az elsődleges kulcs egy társított index, amely biztosítja a gyors lekérdezési teljesítményt, mert nem tartalmazhat NULL értékeket. Ha a InnoDB Storage motort használja, a tábla-adatfeldolgozás fizikailag úgy van rendszerezve, hogy az elsődleges kulcson alapuló, rendkívül gyors keresési és rendezési műveleteket hajtson végre.

Azt javasoljuk, hogy a másodpéldány létrehozása előtt adjon hozzá egy elsődleges kulcsot a forráskiszolgálón lévő táblákhoz. Adjon hozzá elsődleges kulcsokat a forráskiszolgálón, majd olvassa el újra az olvasási replikákat a replikálási késés javítása érdekében.

A következő lekérdezés segítségével megtudhatja, hogy mely táblák hiányoznak elsődleges kulcs a forráskiszolgálón:

```sql
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>Hosszan futó lekérdezések a másodpéldány-kiszolgálón

A replika-kiszolgáló munkaterhelése az IO-szál mögött is elvégezheti az SQL-szál késését. A replika-kiszolgálón a hosszan futó lekérdezések egyike a nagy replikációs késés leggyakoribb okai. A probléma elhárításához engedélyezze a [lassú lekérdezési naplót](concepts-server-logs.md) a másodpéldány-kiszolgálón.

A lassú lekérdezések növelhetik az erőforrások felhasználását, vagy lelassítják a kiszolgálót, így a replika nem tud felfogni a forráskiszolgálóról. Ebben az esetben hangolja le a lassú lekérdezéseket. A gyorsabb lekérdezések megakadályozzák az SQL-szál blokkolását, és jelentősen javítják a replikálás késését.

#### <a name="ddl-queries-on-the-source-server"></a>DDL-lekérdezések a forráskiszolgálón

A forráskiszolgálón egy adatdefiníciós nyelv (DDL) parancs, például [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) hosszú időt vehet igénybe. Amíg a DDL-parancs fut, több ezer más lekérdezés is futhat párhuzamosan a forráskiszolgálón. 

A DDL replikálása esetén az adatbázis konzisztenciájának biztosítása érdekében a MySQL motor egyetlen replikációs szálban futtatja a DDL-t. A feladat során minden más replikált lekérdezés le lesz tiltva, és várnia kell, amíg a DDL-művelet be nem fejeződik a másodpéldány-kiszolgálón. Ez a késleltetés még az online DDL-műveletek esetében is okozhat. A DDL-műveletek fokozzák a replikálás késését.

Ha engedélyezte a [lassú lekérdezési naplót](concepts-server-logs.md) a forráskiszolgálón, ezt a késési problémát észlelheti a forráskiszolgálón futtatott DDL-parancs ellenőrzésével. Az index eldobása, átnevezése és létrehozása révén használhatja az ALTER tábla inplace algoritmusát. Előfordulhat, hogy át kell másolnia a tábla adattábláját, és újra létre kell hoznia a táblát.

Az egyidejű DML-t általában a inplace algoritmus támogatja. A művelet előkészítésekor és futtatásakor azonban rövid időre kihasználhatja a metaadatok zárolását a táblán. A CREATE INDEX utasítás esetében a záradékok ALGORITMUS és a zárolás használatával befolyásolhatja a tábla másolásának módszerét, valamint az olvasásra és írásra vonatkozó párhuzamossági szintet. A DML-műveleteket továbbra is megakadályozhatja egy teljes SZÖVEGES index vagy térbeli index hozzáadásával.

Az alábbi példa egy indexet hoz létre az ALGORITMUS és a LOCK záradék használatával.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

A zárolást igénylő DDL-utasítások esetében sajnos nem kerülheti el a replikálás késését. A lehetséges hatások csökkentése érdekében hajtsa végre az ilyen típusú DDL-műveleteket a munkaidőn kívüli időszakban, például az éjszaka folyamán.

#### <a name="downgraded-replica-server"></a>Visszaminősített replika kiszolgáló

Azure Database for MySQLban az olvasási replikák ugyanazt a kiszolgálói konfigurációt használják, mint a forráskiszolgáló. A replika-kiszolgáló konfigurációját a létrehozása után módosíthatja.

Ha a replika-kiszolgálót leértékelik, a munkaterhelés több erőforrást is felhasználhat, ami pedig replikációs késéshez vezethet. A probléma észleléséhez használja a Azure Monitor a replika kiszolgáló CPU-és memória-felhasználásának ellenőrzéséhez.

Ebben a forgatókönyvben azt javasoljuk, hogy a másodpéldány-kiszolgáló konfigurációját a forráskiszolgáló értékeinek megfelelő vagy annál nagyobb értékekkel tartsa. Ez a konfiguráció lehetővé teszi, hogy a replika lépést tartson a forráskiszolgálóról.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>A replikációs késés javítása a forráskiszolgáló paramétereinek hangolásával

A Azure Database for MySQL alapértelmezés szerint a replikálás a replikák párhuzamos szálakkal való futtatására van optimalizálva. Ha a forráskiszolgálón a nagy párhuzamossági szintű munkaterhelések miatt a replika-kiszolgáló lemarad, a replikálás késését a forráskiszolgáló binlog_group_commit_sync_delay paraméterének konfigurálásával javíthatja.

A binlog_group_commit_sync_delay paraméter azt határozza meg, hogy hány másodpercenként a bináris napló véglegesítve várakozik a bináris naplófájl szinkronizálása előtt. Ennek a paraméternek az előnye, hogy az összes véglegesített tranzakció azonnali alkalmazása helyett a forráskiszolgáló tömegesen küldi el a bináris napló frissítéseit. Ez a késleltetés csökkenti a replika IO-értékét, és segít a teljesítmény javításában.

Hasznos lehet a binlog_group_commit_sync_delay paraméter beállítása 1000-re vagy így. Ezután figyelje a replikálás késését. Ezt a paramétert óvatosan állítsa be, és csak a nagy párhuzamosságú munkaterhelésekhez használja.

> [!IMPORTANT]
> A replika-kiszolgálón a binlog_group_commit_sync_delay paraméter értéke 0. Ez azért ajánlott, mert a forráskiszolgálótól eltérően a replika kiszolgáló nem rendelkezik magas párhuzamossággal, és a replika-kiszolgálón lévő binlog_group_commit_sync_delay értékének növelése véletlenül a replikálás késésének növelését okozhatja.

A sok önálló tranzakciót tartalmazó, alacsony párhuzamosságú számítási feladatokhoz a binlog_group_commit_sync_delay-beállítás növelheti a késést. A késés megnövelhető, mert az IO-szál a tömeges bináris naplók frissítésére vár, még akkor is, ha csak néhány tranzakció véglegesítve van.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [MySQL BinLog-replikáció áttekintését](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
