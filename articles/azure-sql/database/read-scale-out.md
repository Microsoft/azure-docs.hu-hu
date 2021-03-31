---
title: Replika-lekérdezések olvasása
description: Az Azure SQL lehetővé teszi a csak olvasható replikák kapacitásának használatát az olvasási feladatokhoz, az úgynevezett olvasási felskálázáshoz.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/20/2021
ms.openlocfilehash: 5f9e7e1c96db2b60e41fe0ded69ea562cf8fcea6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663985"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Írásvédett replikák használata írásvédett lekérdezési feladatok kiszervezéséhez
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A [magas rendelkezésre állású architektúrák](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)részeként a prémium szintű és a üzletileg kritikus szolgáltatási rétegben található minden egyes adatbázis, rugalmas készlet-adatbázis és felügyelt példány automatikusan egy elsődleges írható-olvasható replikával és számos másodlagos írásvédett replikával van kiépítve. A másodlagos replikákat ugyanazzal a számítási mérettel kell kiépíteni, mint az elsődleges replikát. Az *olvasási Felskálázási* funkció lehetővé teszi a csak olvasási feladatok kiosztását az írásvédett replikák egyikének számítási kapacitása alapján, ahelyett, hogy az írható-olvasható replikán fusson. Így bizonyos írásvédett munkaterhelések elkülöníthetők az írási és olvasási munkaterheléstől, és nem befolyásolják a teljesítményüket. A szolgáltatás olyan alkalmazások számára készült, amelyek logikailag elkülönített, csak olvasható számítási feladatokat tartalmaznak, például az elemzést. A prémium és üzletileg kritikus szolgáltatási szinten az alkalmazások a további kapacitás nélkül vehetik igénybe a teljesítmény előnyeit.

Az *olvasási kibővítő* funkció a nagy kapacitású szolgáltatási rétegében is elérhető, ha legalább egy másodlagos replika létrejön. Több másodlagos replikát is használhat a csak olvasási feladatok terheléselosztásához, amelyek több erőforrást igényelnek, mint amennyi az egyik másodlagos replikán elérhető.

Az alapszintű, standard és általános célú szolgáltatási rétegek magas rendelkezésre állású architektúrája nem tartalmaz replikákat. Az *olvasási Felskálázási* funkció nem érhető el ezekben a szolgáltatási rétegekben.

A következő ábra szemlélteti a funkciót.

![Írásvédett replikák](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

Az *olvasási Felskálázási* funkció alapértelmezés szerint engedélyezve van az új Premium-, üzletileg kritikus-és nagy kapacitású-adatbázisokon. A nagy kapacitású esetében az új adatbázisokhoz alapértelmezés szerint egy másodlagos replika jön létre. 

> [!NOTE]
> Az olvasási felskálázás mindig engedélyezve van a felügyelt példány üzletileg kritikus szolgáltatási rétegében.

Ha az SQL-kapcsolódási karakterlánca konfigurálva van `ApplicationIntent=ReadOnly` , az alkalmazás az adott adatbázis vagy felügyelt példány írásvédett replikájának lesz átirányítva. További információ a tulajdonság használatáról `ApplicationIntent` : az [alkalmazás szándékának megadása](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Ha biztosítani szeretné, hogy az alkalmazás az SQL-kapcsolati sztring beállításától függetlenül az elsődleges replikához kapcsolódjon `ApplicationIntent` , explicit módon le kell tiltania az olvasási méretezést az adatbázis létrehozásakor vagy a konfigurációjának módosításakor. Ha például az adatbázist standard vagy általános célú szintről prémium, üzletileg kritikus vagy nagy kapacitású szintre frissíti, és biztosítani szeretné, hogy az összes kapcsolat továbbra is az elsődleges replikára lépjen, tiltsa le az olvasási felskálázást. A letiltásával kapcsolatos további információkért lásd: az [olvasási felskálázás engedélyezése és letiltása](#enable-and-disable-read-scale-out).

> [!NOTE]
> A lekérdezési tároló és az SQL Profiler-funkciók nem támogatottak csak olvasható replikák esetén. 

## <a name="data-consistency"></a>Adatkonzisztencia

A replikák egyik előnye, hogy a replikák mindig tranzakciós szempontból konzisztens állapotban vannak, de a különböző időpontokban előfordulhat, hogy a különböző replikák között kis késés van. Az olvasási felskálázás támogatja a munkamenet-szintű konzisztenciát. Ez azt jelenti, hogy ha a csak olvasási munkamenet újracsatlakozik a replika nem rendelkezésre állása okozta kapcsolódási hiba után, a rendszer átirányítja egy olyan másodpéldányra, amely nem 100%-kal naprakész az írható-olvasható replikával. Ugyanígy, ha egy alkalmazás olvasási-írási munkamenettel ír adatokat, és azonnal beolvassa őket írásvédett munkamenettel, előfordulhat, hogy a legújabb frissítések nem láthatók azonnal a replikán. A késést egy aszinkron tranzakciós napló ismétlési művelete okozza.

> [!NOTE]
> A régión belüli replikációs késések alacsonyak, és ez a helyzet ritka. A replikálás késésének figyeléséhez tekintse meg az [írásvédett replika figyelése és hibaelhárítása](#monitoring-and-troubleshooting-read-only-replicas)című témakört.

## <a name="connect-to-a-read-only-replica"></a>Kapcsolódás írásvédett replikához

Ha engedélyezi az olvasási felskálázást egy adatbázishoz, az `ApplicationIntent` ügyfél által megadott kapcsolódási karakterláncban szereplő beállítás azt határozza meg, hogy a rendszer átirányítja-e a kapcsolódást az írási replikára vagy egy írásvédett replikára. Pontosabban, ha az `ApplicationIntent` érték `ReadWrite` (az alapértelmezett érték), a rendszer átirányítja a kapcsolódást az írható-olvasható replikára. Ez megegyezik azzal a viselkedéssel, amikor `ApplicationIntent` a nem szerepel a kapcsolódási karakterláncban. Ha az `ApplicationIntent` érték `ReadOnly` , a rendszer a kapcsolódást egy írásvédett replikához irányítja.

A következő kapcsolati karakterlánc például összekapcsolja az ügyfelet egy írásvédett replikával (a szögletes zárójelben lévő elemeket az adott környezet megfelelő értékeivel és a szögletes zárójelek eldobásával):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

A következő kapcsolati karakterláncok valamelyike az-ügyfelet egy írható-olvasható replikához csatlakoztatja (a szögletes zárójelben lévő elemeket a környezet megfelelő értékeivel és a szögletes zárójelek eldobásával helyettesíti):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Annak ellenőrzése, hogy a kapcsolódás írásvédett replikához van-e

A következő lekérdezésnek az adatbázis környezetében való futtatásával ellenőrizheti, hogy csatlakozik-e egy írásvédett replikához. READ_ONLY akkor tér vissza, ha írásvédett replikához csatlakozik.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> A prémium és üzletileg kritikus szolgáltatási szinten csak az írásvédett replikák egyike érhető el a megadott időpontban. A nagy kapacitású több írásvédett replikát is támogat.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Csak olvasható replikák figyelése és hibaelhárítása

Ha írásvédett replikához csatlakozik, a dinamikus felügyeleti nézetek (DMV-EK) tükrözik a replika állapotát, és lekérdezhető figyelési és hibaelhárítási célból. Az adatbázismotor több nézetet biztosít a figyelési adatmennyiségek széles skálájának megjelenítéséhez. 

A gyakran használt nézetek a következők:

| Name | Cél |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Erőforrás-kihasználtsági metrikákat biztosít az elmúlt órában, beleértve a CPU-t, az adatio-t és a naplózási írási kihasználtságot a szolgáltatási célkitűzések korlátaihoz képest.|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Az adatbázismotor-példány összesített várakozási statisztikáját biztosítja. |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| A replika állapotának és szinkronizálási statisztikájának nyújtása. A várólista méretének megismétlése és az Ismétlési arány a csak olvasható replikán lévő Adatkésési mutatókként szolgál. |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Adatbázismotor-teljesítményszámlálókat biztosít.|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Lekérdezési végrehajtási statisztikákat biztosít, például a végrehajtások számát, a felhasznált CPU-időt stb.|
|[sys.dm_exec_query_plan ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Gyorsítótárazott lekérdezési terveket biztosít. |
|[sys.dm_exec_sql_text ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Lekérdezési szöveget biztosít a gyorsítótárazott lekérdezési tervhez.|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Valós idejű lekérdezési előrehaladást biztosít a lekérdezések végrehajtása közben.|
|[sys.dm_exec_query_plan_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| A legutóbbi ismert tényleges végrehajtási tervet tartalmazza, beleértve egy lekérdezés futásidejű statisztikáit.|
|[sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Az összes adatbázisfájlok tárolási IOPS, átviteli sebességét és késési statisztikáit biztosítja. |

> [!NOTE]
> Az `sys.resource_stats` és a `sys.elastic_pool_resource_stats` DMV a logikai főadatbázisban az elsődleges replika erőforrás-kihasználtsági adatai.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Írásvédett replikák figyelése kiterjesztett eseményekkel

Kiterjesztett esemény-munkamenet nem hozható létre, ha csak olvasható replikához csatlakozik. Azure SQL Database azonban az elsődleges replikán létrehozott és módosított adatbázis-hatókörű [bővített esemény](xevent-db-diff-from-svr.md) -munkamenetek definíciói replikálása írásvédett replikára, beleértve a földrajzi replikákat, valamint az események rögzítése a csak olvasható replikák esetében.

Az elsődleges replika munkamenet-definícióján alapuló, írásvédett replikán lévő kiterjesztett esemény-munkamenet elindítható és leállítható az elsődleges replikától függetlenül. Ha az elsődleges replikán egy kiterjesztett esemény-munkamenetet dobott el, az összes írásvédett replikán is el lesz dobva.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Tranzakció-elkülönítési szint írásvédett replikák esetén

A csak olvasási replikákat futtató lekérdezések mindig a [Pillanatkép](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) -tranzakciók elkülönítési szintjére vannak leképezve. A pillanatkép-elkülönítés a sorok verziószámozását használja a blokkoló forgatókönyvek blokkolására, ahol az olvasók blokkolja az írókat.

Ritka esetekben, ha egy pillanatkép-elkülönítési tranzakció hozzáfér egy másik párhuzamos tranzakcióban módosított objektum-metaadatokhoz, akkor az [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error)-es hiba miatt nem sikerült a pillanatkép-elkülönítési tranzakció végrehajtása a (z)%. * ls adatbázisban, mert az utasításhoz hozzáférő objektum egy másik párhuzamos tranzakcióban található DDL-utasítással lett módosítva a tranzakció kezdete óta. Ez nem engedélyezett, mert a metaadatok nincsenek verzióval ellátva. A metaadatok egyidejű frissítése inkonzisztenciát okozhat, ha a pillanatkép-elkülönítést keverik. "

### <a name="long-running-queries-on-read-only-replicas"></a>Hosszú ideig futó lekérdezések csak olvasható replikák esetén

A csak olvasási replikán futó lekérdezéseknek hozzá kell férniük a lekérdezésben hivatkozott objektumok metaadataihoz (táblák, indexek, statisztikák stb.). Ritka esetekben, ha egy metaadat-objektum módosul az elsődleges replikán, miközben egy lekérdezés zárolva van ugyanazon az objektumon az írásvédett replikán, a lekérdezés [blokkolhatja](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) azt a folyamatot, amely az elsődleges replikáról a írásvédett replikára alkalmazza a módosításokat. Ha egy ilyen lekérdezés hosszú ideig futott, a csak olvasási replikát jelentősen el lehet végezni az elsődleges replikával való szinkronizálás során.

Ha egy csak olvasható replikán egy hosszan futó lekérdezés okozza ezt a blokkolást, a rendszer automatikusan leállítja azt. A munkamenet a 1219-as hibát fogja kapni: "a munkamenete le lett választva egy magas prioritású DDL-művelet miatt", vagy a 3947-es hiba: "a tranzakció meg lett szakítva, mert a másodlagos számítás nem tudta felfogni a műveletet. Próbálja megismételni a tranzakciót. "

> [!NOTE]
> Ha 3961, 1219 vagy 3947 hibát kap egy írásvédett replikára irányuló lekérdezések futtatásakor, próbálja megismételni a lekérdezést.

> [!TIP]
> A prémium és üzletileg kritikus szolgáltatási szinten, ha egy írásvédett replikához csatlakozik, a (z `redo_queue_size` `redo_rate` ) [sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV-ban található és oszlopok az adatszinkronizálási folyamat figyelésére használhatók, amely a csak olvasható replikán lévő Adatkésési mutatókként szolgál.
> 

## <a name="enable-and-disable-read-scale-out"></a>Az olvasási felskálázás engedélyezése és letiltása

Az olvasási Felskálázási funkció alapértelmezés szerint engedélyezve van a Premium, a üzletileg kritikus és a nagy kapacitású szolgáltatás szintjein. Az olvasási felskálázás nem engedélyezhető alapszintű, standard vagy általános célú szolgáltatási szinteken. Az olvasási felskálázás automatikusan le van tiltva a nulla replikákkal konfigurált nagy kapacitású-adatbázisokban.

A következő módszerekkel letilthatja és újból engedélyezheti az olvasási felskálázást önálló adatbázisokban és rugalmas készletekben lévő adatbázisokban a prémium vagy üzletileg kritikus szolgáltatási szinteken.

> [!NOTE]
> Az önálló adatbázisok és a rugalmas készlet-adatbázisok esetében a visszamenőleges kompatibilitás érdekében letiltható az olvasási felskálázás. Az olvasási felskálázás nem tiltható le üzletileg kritikus felügyelt példányokon.

### <a name="azure-portal"></a>Azure Portal

Az olvasási Felskálázási beállítást az adatbázis **konfigurálása** panelen kezelheti.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. A Azure Resource Manager modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a Azure Resource Manager modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

Azure PowerShell az olvasási felskálázás kezelése a december 2016 Azure PowerShell vagy újabb verzióra van szükség. A legújabb PowerShell-kiadást itt tekintheti meg: [Azure PowerShell](/powershell/azure/install-az-ps).

A [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsmag meghívásával letilthatja vagy újból engedélyezheti az olvasási felskálázást Azure PowerShell a `Enabled` `Disabled` paraméterhez a kívánt érték (vagy) megadásával `-ReadScale` .

Ha le szeretné tiltani az olvasási felskálázást egy meglévő adatbázison (a szögletes zárójelben lévő elemek cseréje a környezet megfelelő értékeire, és a szögletes zárójelek eldobása):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Ha le szeretné tiltani az olvasási felskálázást egy új adatbázison (a szögletes zárójelben lévő elemek cseréje a környezet megfelelő értékeire, és a szögletes zárójelek eldobása):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Ha újra engedélyezni szeretné az olvasási felskálázást egy meglévő adatbázison (a szögletes zárójelben lévő elemek cseréje a környezet megfelelő értékeire, és a szögletes zárójelek eldobása):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

Ha olyan adatbázist szeretne létrehozni, amelynél le van tiltva az olvasási felskálázás, vagy egy meglévő adatbázis beállítását szeretné módosítani, használja a következő metódust a vagy értékre `readScale` beállított tulajdonsággal, `Enabled` `Disabled` ahogy az az alábbi példában is látható.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

További információ: [adatbázisok – létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate).

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>Az `tempdb` adatbázis használata írásvédett replikán

Az `tempdb` elsődleges replikán lévő adatbázis nem replikálódik a csak olvasható replikára. Minden replika saját `tempdb` adatbázissal rendelkezik, amely a replika létrehozásakor jön létre. Ez biztosítja, hogy `tempdb` frissíthető legyen, és módosítható legyen a lekérdezés végrehajtása során. Ha az írásvédett számítási feladatok az objektumok használatával függenek `tempdb` , ezeket az objektumokat a lekérdezési parancsfájl részeként kell létrehoznia.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Az olvasási méretezés földrajzilag replikált adatbázisokkal

A földrajzilag replikált másodlagos adatbázisok azonos magas rendelkezésre állású architektúrával rendelkeznek, mint az elsődleges adatbázisok. Ha a földrajzilag replikált másodlagos adatbázishoz csatlakozik, és az olvasási felskálázás engedélyezve van, a-munkamenetek a `ApplicationIntent=ReadOnly` magas rendelkezésre állású replikák egyikéhez lesznek irányítva, ugyanúgy, mint az elsődleges írható adatbázison. A munkamenetek nem `ApplicationIntent=ReadOnly` lesznek átirányítva a földrajzilag replikált másodlagos másodlagos replika elsődleges replikájának, amely szintén írásvédett. 

Ebben a formában a Geo-replika létrehozása két további írásvédett replikát biztosít egy írható és olvasható elsődleges adatbázishoz, összesen három írásvédett replikához. Minden további földrajzi replika egy másik pár írásvédett replikát biztosít. A Geo-replikák bármely Azure-régióban létrehozhatók, beleértve az elsődleges adatbázis régióját is.

> [!NOTE]
> Nincs automatikus ciklikus multiplexelés vagy más terheléselosztási útvonal a földrajzilag replikált másodlagos adatbázis replikái között.

## <a name="next-steps"></a>Következő lépések

- További információ a SQL Database nagy kapacitású-ajánlatról: [nagy kapacitású szolgáltatási szintje](service-tier-hyperscale.md).