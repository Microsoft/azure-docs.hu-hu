---
title: Gyakori problémák – Azure Database Migration Service
description: További információ a Azure Database Migration Service használatával kapcsolatos ismert problémák/hibák elhárításáról.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 785567685b77183df0f212897b923d911ac045f6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567307"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Gyakori Azure Database Migration Service problémák és hibák elhárítása

Ez a cikk néhány olyan gyakori problémát és hibát ismertet, amelyekkel Azure Database Migration Service felhasználók származhatnak. A cikk a problémák és hibák megoldásával kapcsolatos információkat is tartalmaz.

> [!NOTE]
> Elfogultság – ingyenes kommunikáció
>
> A Microsoft sokféle és befogadó környezetet támogat. Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz. Az [elfogultság nélküli kommunikációhoz használható Microsoft-stílus útmutatója](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) ezt a kizáró szót ismeri fel. A szó a jelen cikkben a konzisztencia miatt használatos, mert jelenleg a szoftverben megjelenő szó. Ha a szoftver frissítve lett a szó eltávolítására, a rendszer a cikket úgy frissíti, hogy az legyen az igazítás.
>

## <a name="migration-activity-in-queued-state"></a>Áttelepítési tevékenység várólistán lévő állapotban

Amikor új tevékenységeket hoz létre egy Azure Database Migration Service projektben, a tevékenységek várólistán maradnak.

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a probléma akkor fordul elő, ha a Azure Database Migration Service-példány elérte a maximális kapacitást az egyidejű futtatású folyamatban lévő feladatokhoz. Minden új tevékenység várólistára kerül, amíg a kapacitás elérhetővé nem válik. | Ellenőrizze, hogy az adatáttelepítési szolgáltatás példánya több projekten futó tevékenységeket futtat-e. Továbbra is létrehozhat olyan új tevékenységeket, amelyek automatikusan hozzáadódnak a várólistához a végrehajtáshoz. Amint bármelyik meglévő futó tevékenység befejeződik, a következő várólistán lévő tevékenység elindul, és az állapot automatikusan futó állapotra változik. A várólistán lévő tevékenységek áttelepítésének megkezdéséhez semmilyen további műveletet nem kell elvégeznie.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Az áttelepítéshez kiválasztott adatbázisok maximális száma

A következő hiba fordul elő, amikor egy adatbázis-áttelepítési projekthez hoz létre egy tevékenységet Azure SQL Databasere vagy egy Azure SQL felügyelt példányra való áthelyezéshez:

* **Hiba**: az áttelepítési beállítások érvényesítési hibája "," errorDetail ":" az "adatbázisok" több mint maximális száma "4" objektum lett kiválasztva áttelepítésre. "

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a hiba akkor jelenik meg, ha több mint négy adatbázist jelölt ki egyetlen áttelepítési tevékenységhez. Jelenleg minden áttelepítési tevékenység négy adatbázisra korlátozódik. | Egy áttelepítési tevékenységnél négy vagy kevesebb adatbázist válasszon ki. Ha négynél több adatbázist kell migrálni párhuzamosan, a Azure Database Migration Service egy másik példányát kell kiépíteni. Az egyes előfizetések jelenleg legfeljebb két Azure Database Migration Service példányt támogatnak.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Hibák a MySQL-re való áttéréskor az Azure MySQL-be helyreállítási hibákkal

Ha a MySQL-ből Azure Database for MySQL Azure Database Migration Service használatával végez áttelepítést, az áttelepítési tevékenység a következő hibával meghiúsul:

* **Hiba**: az adatbázis-áttelepítési hiba – a (z) "TaskID" feladat felfüggesztése a következő helyreállítási hibák miatt megszakadt: [n].

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a hiba akkor fordulhat elő, ha az áttelepítést végző felhasználó hiányzik a ReplicationAdmin szerepkör és/vagy a replikációs ügyfél, a replikációs REPLIKA és a SUPER (a MySQL 5.6.6 korábbi verziók).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Győződjön meg arról, hogy a felhasználói fiók [előfeltételként megadott jogosultságai](./tutorial-mysql-azure-mysql-online.md#prerequisites) pontosan vannak konfigurálva a Azure Database for MySQL példányon. Például az alábbi lépéseket követve létrehozhat egy "migrateuser" nevű felhasználót a szükséges jogosultságokkal:<br>1. hozza létre a (z) "%" felhasználói migrateuser@ "Secret" AZONOSÍTÓval. <br>2. adja meg az összes jogosultságot a db_name. * a "Secret" által azonosított "migrateuser" @ "%" értékre. Ismételje meg ezt a lépést, ha további adatbázisokhoz szeretne hozzáférést biztosítani <br>3. Adjon meg replikálási Slave-t *.* a "Secret" által azonosított "migrateuser" @ "%" értékre.<br>4. engedélyezze a replikációs ügyfelet a-ben *.* a "Secret" által azonosított "migrateuser" @ "%" értékre.<br>5. kiürítési jogosultságok; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Hiba történt a Azure Database Migration Service leállításakor

A Azure Database Migration Service példány leállításakor a következő hibaüzenetet kapja:

* **Hiba**: a szolgáltatás leállítása sikertelen. Hiba: {'error':{'code':'InvalidRequest','message':'Egy vagy több tevékenység jelenleg is fut. A szolgáltatás leállításához várjon, amíg a tevékenységek befejeződik, vagy állítsa le manuálisan a tevékenységeket, és próbálkozzon újra. "}}

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a hiba akkor jelenik meg, ha a leállítani próbált szolgáltatási példány olyan tevékenységeket tartalmaz, amelyek továbbra is futnak, vagy amelyek az áttelepítési projektekben szerepelnek. <br><br><br><br><br><br> | Győződjön meg arról, hogy nincsenek olyan tevékenységek, amelyek a leállítani kívánt Azure Database Migration Service példányán futnak. A szolgáltatás leállításának megkísérlése előtt törölheti a tevékenységeket vagy a projekteket is. Az alábbi lépések bemutatják, hogyan távolíthat el projekteket az áttelepítési szolgáltatás példányának tisztításához az összes futó feladat törlésével:<br>1. Install-Module neve: AzureRM. DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-SubscriptionName " \<subName> " <br> 4. Remove-AzureRmDataMigrationProject-név \<projectName> -ResourceGroupName \<rgName> -szolgáltatásnév \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Hiba történt a Azure Database Migration Service indításakor

A Azure Database Migration Service példány indításakor a következő hibaüzenetet kapja:

* **Hiba**: a szolgáltatás nem indul el. Hiba: {"errorDetail": "a szolgáltatás elindítása nem sikerült, forduljon a Microsoft támogatási szolgálatához"}

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a hiba akkor jelenik meg, ha az előző példány belső feladatátvétele meghiúsult. Ez a hiba ritkán fordul elő, és a mérnöki csapat tisztában van vele. <br> | Törölje a szolgáltatás azon példányát, amelyet nem tud elindulni, majd hozzon létre egy újat a helyére. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Hiba történt az adatbázis visszaállításakor az SQL Azure SQL DB felügyelt példányra történő áttelepítése közben

Ha SQL Serverról az Azure SQL felügyelt példányára végez online áttelepítést, a átváltás a következő hiba miatt meghiúsul:

* **Hiba**: a visszaállítási művelet nem sikerült a (z) "operationId" műveleti azonosítóhoz. "AuthorizationFailed", "a (z)" objectId "azonosítójú" ügyfél "clientId" üzenetnek nincs engedélye a (z) "Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/Read" művelet végrehajtására a (z) "/subscriptions/subscriptionId" hatókörben.

| Ok         | Feloldás    |
| ------------- | ------------- |
| Ez a hiba azt jelzi, hogy az SQL Serverról az SQL felügyelt példányára való online áttelepítéshez használt egyszerű alkalmazás nem járul hozzá az előfizetéshez. A felügyelt példányokkal rendelkező bizonyos API-hívások esetében ez az engedély szükséges a visszaállítási művelet előfizetéséhez. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | A `Get-AzureADServicePrincipal` hibaüzenetben elérhető PowerShell-parancsmag használatával `-ObjectId` jelenítse meg a használt alkalmazás-azonosító megjelenítendő nevét.<br><br> Érvényesítse az alkalmazás engedélyeit, és győződjön meg arról, hogy az előfizetés szintjén szerepel a [közreműködő szerepkör](../role-based-access-control/built-in-roles.md#contributor) . <br><br> A Azure Database Migration Service mérnöki csapat azon dolgozik, hogy korlátozza a jelenlegi hozzájárulási szerepkör szükséges hozzáférését az előfizetéshez. Ha olyan üzleti követelménye van, amely nem engedélyezi a hozzájárulási szerepkör használatát, további segítségért forduljon az Azure támogatási szolgálatához. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Hiba történt a Azure Database Migration Servicehoz társított NIC törlésekor

Ha Azure Database Migration Servicehoz társított hálózati adaptert próbál törölni, a törlési kísérlet a következő hibával meghiúsul:

* **Hiba**: a hálózati adaptert használó DMS-szolgáltatás miatt nem lehet törölni a Azure Database Migration Servicehoz társított hálózati adaptert

| Ok         | Feloldás    |
| ------------- | ------------- |
| Ez a probléma akkor fordul elő, ha a Azure Database Migration Service-példány továbbra is jelen van, és a hálózati adaptert használják. <br><br><br><br><br><br><br><br> | A hálózati adapter törléséhez törölje a DMS szolgáltatás azon példányát, amely automatikusan törli a szolgáltatás által használt hálózati adaptert.<br><br> **Fontos**: Győződjön meg arról, hogy a törlendő Azure Database Migration Service-példány nem rendelkezik futó tevékenységekkel.<br><br> Miután az Azure Database Migration Service-példányhoz társított összes projektet és tevékenységet törli, törölheti a szolgáltatási példányt. A szolgáltatási példány által használt hálózati adapter automatikusan törlődik a szolgáltatás törlésének részeként. |

## <a name="connection-error-when-using-expressroute"></a>Csatlakozási hibák az ExpressRoute használata esetében

Amikor az Azure Database Migration Service projektvarázslóban próbál csatlakozni a forráshoz, a csatlakozás időtúllépés miatt meghiúsul, ha a forrás az ExpressRoute-ot használja a csatlakozáshoz.

| Ok         | Feloldás    |
| ------------- | ------------- |
| A [ExpressRoute](https://azure.microsoft.com/services/expressroute/)használatakor Azure Database Migration Service a szolgáltatáshoz társított Virtual Network alhálózaton három szolgáltatási végpontot [kell](./tutorial-sql-server-to-azure-sql.md) kiépíteni:<br> – Service Bus végpont<br> – Tárolási végpont<br> – Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont)<br><br><br><br><br> | [Engedélyezze](./tutorial-sql-server-to-azure-sql.md) a szükséges szolgáltatási végpontokat a forrás-és a Azure Database Migration Service közötti ExpressRoute-kapcsolathoz. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Zárolási várakozási időtúllépési hiba történt a MySQL-adatbázis MySQL-adatbázisba való áttelepítésekor

Ha Azure Database Migration Service használatával telepít át egy MySQL-adatbázist egy Azure Database for MySQL-példányra, az áttelepítés a zárolási várakozási időkorlát következő hibája miatt meghiúsul:

* **Hiba**: adatbázis-áttelepítési hiba – nem sikerült betölteni a fájlt – nem sikerült elindítani a (z) "n" fájlhoz tartozó betöltési folyamatot RetCode: SQL_ERROR SQLSTATE: HY000 NativeError: 1205 üzenet: [MySQL] [ODBC-illesztő] [mysqld] zárolási várakozás időtúllépése túllépve. próbálja meg újraindítani a tranzakciót

| Ok         | Feloldás    |
| ------------- | ------------- |
| Ez a hiba akkor fordul elő, ha az áttelepítés sikertelen a zárolási várakozási időkorlát miatt. | Vegye fontolóra a (z) **"innodb_lock_wait_timeout"** kiszolgálói paraméter értékének növelését. A legmagasabb megengedett érték 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Hiba történt a forrás SQL Serverhoz való kapcsolódáskor, ha dinamikus portot vagy nevesített példányt használ

Ha egy vagy több elnevezett példányon vagy dinamikus porton futó SQL Server-forráshoz próbál Azure Database Migration Service csatlakozni, a kapcsolat a következő hibával meghiúsul:

* **Hiba**:-1 – az SQL-kapcsolatok sikertelenek. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Ellenőrizze, hogy a példány neve helyes-e, és hogy a SQL Server távoli kapcsolatok engedélyezésére van-e konfigurálva. (szolgáltató: SQL hálózati adapterek, hiba: 26 – hiba a megadott kiszolgáló/példány keresésekor)

| Ok         | Feloldás    |
| ------------- | ------------- |
| Ez a probléma akkor fordul elő, ha a forrás SQL Server-példány, amely Azure Database Migration Service megpróbál csatlakozni egy dinamikus porthoz, vagy nevesített példányt használ. Az SQL Server Browser szolgáltatás a 1434-es UDP-portot figyeli a bejövő kapcsolatok elnevezett példányhoz vagy dinamikus port használata esetén. A dinamikus port SQL Server szolgáltatás újraindításakor változhat. A példányokhoz rendelt dinamikus portot a SQL Server Konfigurációkezelő hálózati konfigurációja segítségével tekintheti meg.<br><br><br> |Ellenőrizze, hogy Azure Database Migration Service tud-e csatlakozni a forrás SQL Server Browser szolgáltatáshoz a 1434-es UDP-porton és a SQL Server példányon a dinamikusan hozzárendelt TCP-porton keresztül. |

## <a name="additional-known-issues"></a>További ismert problémák

* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel Azure SQL Database](./index.yml)
* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [Azure Database Migration Service PowerShellt](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)ismertető cikket.
* A Azure Portal használatával megtekintheti a [kiszolgáló paramétereinek konfigurálása Azure Database for MySQLben](../mysql/howto-server-parameters.md)című cikket.
* Tekintse meg a [Azure Database Migration Service használatának előfeltételeit ismertető](./pre-reqs.md)cikket.
* Tekintse meg a [Azure Database Migration Service használatának gyakori kérdéseit](./faq.md).