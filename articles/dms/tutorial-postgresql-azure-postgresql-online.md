---
title: 'Oktatóanyag: a PostgreSQL migrálása Azure Database for PostgreSQL online állapotba az Azure CLI-n keresztül'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan végezheti el a helyszíni PostgreSQL-ről történő online áttelepítést, hogy a parancssori felületen keresztül Azure Database for PostgreSQL a Azure Database Migration Service használatával.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurecli
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: e5efa30343afcda736946164eb271ad0bf420cab
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096147"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-cli"></a>Oktatóanyag: a PostgreSQL migrálása az Azure DB for PostgreSQL online-ba a DMS használatával az Azure CLI-n keresztül

A Azure Database Migration Service segítségével telepítheti át az adatbázisokat egy helyszíni PostgreSQL-példányból, hogy [Azure Database for PostgreSQL](../postgresql/index.yml) minimális állásidővel. Más szóval a Migrálás az alkalmazás minimális állásidővel is elérhető. Ebben az oktatóanyagban áttelepíti a **DVD-kölcsönzési** minta adatbázisát a PostgreSQL 9,6 helyszíni példányáról, hogy Azure Database for PostgreSQL a Azure Database Migration Service Online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Telepítse át a minta sémát pg_dump segédprogram használatával.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

> [!NOTE]
> A Azure Database Migration Service használata az online áttelepítés végrehajtásához a prémium szintű díjszabás alapján kell létrehoznia egy példányt. A lemez titkosításával megakadályozható az adatok ellopása az áttelepítés során.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy Azure Database Migration Service-példányt hozzon létre ugyanabban az Azure-régióban, mint a célként megadott adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a [PostgreSQL Community edition](https://www.postgresql.org/download/) 9,5, 9,6 vagy 10 verziót. A forrás PostgreSQL-kiszolgáló verziójának 9.5.11, 9.6.7, 10 vagy újabbnak kell lennie. További információkért lásd a [PostgreSQL-adatbázisok támogatott verzióit](../postgresql/concepts-supported-versions.md)ismertető cikket.

    Azt is vegye figyelembe, hogy a cél Azure Database for PostgreSQL verziószámának vagy annál későbbinek kell lennie, mint a helyszíni PostgreSQL-verziónak. A PostgreSQL 9,6 például csak Azure Database for PostgreSQL 9,6, 10 vagy 11 rendszerre tud áttérni, de nem Azure Database for PostgreSQL 9,5-ra.

* [Hozzon létre egy példányt a Azure Database for PostgreSQLban](../postgresql/quickstart-create-server-database-portal.md) , vagy [hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálót](../postgresql/quickstart-create-hyperscale-portal.md).
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > * Tárolási végpont
    > * Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport (NSG) szabályai nem gátolják meg a ServiceTag 443 kimenő portját a ServiceBus, a Storage és a AzureMonitor számára. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás PostgreSQL-kiszolgálóhoz, amely alapértelmezés szerint a 5432-es TCP-port.
* Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
* Hozzon létre egy kiszolgálói szintű Azure Database for PostgreSQL [Tűzfalszabály](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) , amely lehetővé teszi, hogy a Azure Database Migration Service hozzáférjen a célként megadott adatbázisokhoz. Adja meg a Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* A CLI meghívásának két módja van:

  * A Azure Portal jobb felső sarkában válassza a Cloud Shell gombot:

       ![Cloud Shell gomb a Microsoft Azure Portal-on](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Telepítse és futtassa a CLI-t helyileg. A CLI 2.0 egy parancssori eszköz az Azure-erőforrások kezeléséhez.

       A CLI letöltéséhez kövesse [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest) című cikkben lévő utasításokat. A cikk a CLI 2.0 verzióját támogató platformokat is felsorolja.

       Windows Subsystem for Linux (WSL) beállításához kövesse a [Windows 10 telepítési útmutatójának](/windows/wsl/install-win10) utasításait

* Engedélyezze a logikai replikálást a postgresql.config fájlban, és állítsa be a következő paramétereket:

  * wal_level = **logical**
  * max_replication_slots = [bővítőhelyek száma], javasolt beállítás **öt bővítőhelyre**
  * max_wal_senders = [párhuzamos feladatok száma] – a max_wal_senders paraméter megadja a párhuzamosan futtatható feladatok számát, az ajánlott beállítás **10 feladat**

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra.

1. Pg_dump -s parancs segítségével hozzon létre egy séma-memóriaképfájlt az adatbázishoz. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Például: sémafájl kiírása a dvdrental adatbázishoz:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    A pg_dump segédprogram használatával kapcsolatos további információkért lásd a [pg-memóriakép](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) oktatóanyagban szereplő példákat.

2. Hozzon létre egy üres adatbázist a célkörnyezetben, amely az Azure Database for PostgreSQL.

    Az adatbázisok összekapcsolásával és létrehozásával kapcsolatos további információkért tekintse meg a [Azure Database for PostgreSQL kiszolgáló létrehozása a Azure Portal](../postgresql/quickstart-create-server-database-portal.md) vagy [a Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgáló létrehozása a Azure Portalben](../postgresql/quickstart-create-hyperscale-portal.md)című cikket.

3. Importálja a sémát a létrehozott céladatbázisba a séma-memóriaképfájl visszaállításával.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Például:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz. Hajtsa végre a következő szkriptet a PgAdmin alkalmazásban vagy a psql-ben a ’drop foreign key’ és az ’add foreign key’ szkript kibontásához a célhelyen (Azure Database for PostgreSQL).
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

    Futtassa a ’drop foreign key’-t (ez a második oszlop) a lekérdezési eredményben.

5. Az adatok között szereplő trigger (beszúrási vagy frissítési trigger) kikényszeríti az adatok integritását a célban az adatok forrásból való replikálása előtt. Javasoljuk, hogy az áttelepítés során tiltsa le az eseményindítókat **a célhelyen** lévő összes táblában, majd engedélyezze újra az eseményindítókat az áttelepítés befejeződése után.

    A céladatbázisban szereplő triggerek letiltásához használja az alábbi parancsot:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Ha a táblázatokban ENUMERÁLÁSi adattípus szerepel, azt javasoljuk, hogy ideiglenesen frissítse azt a céltábla "változó" típusára. Miután elkészült az adatreplikáció, állítsa vissza az adattípust ENUM-ra.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>A DMS egy példányának kiépítése a CLI használatával

1. A dms szinkronizálási bővítmény telepítéséhez:
   * Jelentkezzen be az Azure-ba az alábbi parancs futtatásával:
       ```azurecli
       az login
       ```

   * Amikor a rendszer kéri, nyisson meg egy webböngészőt, és adja meg a kódot az eszköz hitelesítéséhez. Kövesse az utasításokat a megjelenésük sorrendjében.
   * A dms bővítmény hozzáadásához:
       * Az elérhető bővítmények listájának megjelenítéséhez futtassa a következő parancsot:

           ```azurecli
           az extension list-available –otable
           ```

       * A bővítmény telepítéséhez futtassa a következő parancsot:

           ```azurecli
           az extension add –n dms-preview
           ```

   * A dms bővítmény helyes telepítésének ellenőrzéséhez futtassa a következő parancsot:

       ```azurecli
       az extension list -otable
       ```
       A következő kimenetnek kell megjelennie:

       ```output
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

      > [!IMPORTANT]
      > Győződjön meg arról, hogy a bővítmény verziója meghaladja a 0.11.0-t.

   * A következő futtatásával bármikor megjelenítheti a DMS-ben támogatott összes parancsot:

       ```azurecli
       az dms -h
       ```

   * Ha több Azure-előfizetéssel is rendelkezik, a DMS-szolgáltatás egy példányának kiépítéséhez használni kívánt előfizetés beállításához futtassa a következő parancsot.

        ```azurecli
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. A DMS egy példányának kiépítése a következő parancs futtatásával:

   ```azurecli
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Például a következő parancs szolgáltatást hoz létre az alábbi helyen:

   * Hely: az USA 2. keleti régiója
   * Előfizetés: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Erőforráscsoport neve: PostgresDemo
   * DMS-szolgáltatás neve: PostgresCLI

   ```azurecli
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   A DMS-szolgáltatás példányának létrehozása nagyjából 10–12 percet vesz igénybe.

3. A DMS-ügynök IP-címének azonosításához, így a Postgres pg_hba.conf fájlba való felvételéhez futtassa a következő parancsot:

    ```azurecli
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Például:

    ```azurecli
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    A következő címhez hasonló eredményt kell kapnia: 

    ```output
    [
      "172.16.136.18"
    ]
    ```

4. Vegye fel a DMS-ügynök IP-címét a Postgres pg_hba.conf fájlba.

    * A DMS kiépítése után jegyezze fel a DMS IP-címét.
    * Vegye fel az IP-címet a pg_hba.conf fájlba a forráson a következő bejegyzéshez hasonló módon:

        ```
        host     all            all        172.16.136.18/10    md5
        host     replication    postgres   172.16.136.18/10    md5
        ```

5. Ezután hozzon létre egy PostgreSQL-migrálási projektet a következő parancs futtatásával:
    
    ```azurecli
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Például a következő parancs létrehoz egy projektet az alábbi paraméterek használatával:

   * Hely: az USA nyugati középső régiója
   * Erőforráscsoport neve: PostgresDemo
   * Szolgáltatás neve: PostgresCLI
   * Projekt neve: PGMigration
   * Forrásplatform: PostgreSQL
   * Célplatform: AzureDbForPostgreSql

     ```azurecli
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Az alábbi lépéseket követve hozzon létre egy PostgreSQL-migrálási feladatot.

    Ez a lépés magában foglalja a forrás IP-címének, felhasználói azonosítójának és jelszavának, a cél IP-címének, felhasználói azonosítójának és jelszavának, és a feladattípusnak a megadását a kapcsolat létrehozásához.

   * A lehetőségek teljes listájának megtekintéséhez futtassa a parancsot:

       ```azurecli
       az dms project task create -h
       ```

       A forrás és cél kapcsolata esetében is a bemeneti paraméter egy az objektumlistát tartalmazó json-fájlra hivatkozik.

       A kapcsolat JSON-objektumának formátuma a PostgreSQL-kapcsolatokhoz.
        
       ```json
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Létezik egy adatbázis-beállítás JSON-fájl is, amely felsorolja a JSON-objektumokat. A PostgreSQL esetében az adatbázis-beállítási JSON-objektum formátuma az alábbi:

       ```json
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Hozzon létre egy json-fájlt a Jegyzettömbben, másolja ki a következő parancsokat, és illessze be őket a fájlba, majd mentse a fájlt a C:\DMS\source.json helyre.

        ```json
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Hozzon létre egy másik, target.json elnevezésű fájlt, és mentse másként a C:\DMS\target.json helyre. Foglalja bele az alábbi parancsokat:

       ```json
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Hozzon létre egy adatbázis-beállítási json-fájlt, amely migrálandó adatbázisként kilistázza a készletet:

       ```json
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Futtassa a következő parancsot, amely egybefoglalja a forrás, cél és a DB-beállítási json-fájlokat.

       ```azurecli
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     Ezen a ponton sikeresen beküldött egy migrálási feladatot.

7. A feladat előrehaladásának megjelenítéséhez futtassa a következő parancsot:

   ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   OR

    ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. A kibontott kimenetből futtathatja a migrationState lekérdezését is:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

A kimeneti fájlban számos, a migrálási folyamat előrehaladását jelző paraméter van. Például lásd az alábbi kimeneti fájlt:

  ```output
    "output": [                                 // Database Level
          {
            "appliedChanges": 0,         // Total incremental sync applied after full load
            "cdcDeleteCounter": 0        // Total delete operation  applied after full load
            "cdcInsertCounter": 0,       // Total insert operation applied after full load
            "cdcUpdateCounter": 0,       // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0,     //Number of tables that contain migration error
            "fullLoadLoadingTables": 0,     //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,      //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,           //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",    //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                        // Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",    //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                     //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,            //Volume in Bytes in full load
            "id": "or|inventory|public|actor",
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                       //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                    //Total sync changes that applied after full load
          },
          {                                            //Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                                      // DMS migration task state
        "state": "Running",    //Migration task state – Running means it is still listening to any changes that might come in
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
  ```

## <a name="cutover-migration-task"></a>Átállásos migrálási feladat

Amikor a teljes betöltés kész, az adatbázis készen áll az átállásra. Attól függően, mennyire foglalt a forráskiszolgáló a beérkező új tranzakciók miatt, lehet, hogy a teljes betöltés befejeződése után a DMS-feladat még módosításokat alkalmaz.

Annak érdekében, hogy az összes adat szerepeljen, ellenőrizze a forrás- és a céladatbázis sorainak számát. Ezt például a következő paranccsal teheti meg:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0, //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0, //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112, //full load for table 2
```

1. Hajtsa végre az átállásos adatbázis-migrálási feladatot a következő paranccsal:

    ```azurecli
    az dms project task cutover -h
    ```

    Például:

    ```azurecli
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --object-name Inventory
    ```

2. Az átállás előrehaladásának monitorozásához futtassa a következő parancsot:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```
3. Ha az adatbázis-áttelepítési **állapot megjelenik,** [hozza létre](https://wiki.postgresql.org/wiki/Fixing_Sequences) újra a sorozatot (ha van ilyen), és az alkalmazásait az Azure Database for PostgreSQL új célként megadott példányához kapcsolja.

## <a name="service-project-task-cleanup"></a>Szolgáltatás, projekt, feladat törlése

Ha valamelyik DMS-feladat, -projekt vagy -szolgáltatás megszakítására vagy törlésére van szükség, hajtsa végre a megszakítást a következő sorrendben:

* Az adott futó feladat megszakítása
* A feladat törlése
* A projekt törlése
* A DMS-szolgáltatás törlése

1. A futó feladat megszakításához használja a következő parancsot:

    ```azurecli
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. A futó feladat törléséhez használja a következő parancsot:
    ```azurecli
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. A futó projekt megszakításához használja a következő parancsot:
     ```azurecli
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. A futó projekt törléséhez használja a következő parancsot:
    ```azurecli
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. A DMS-szolgáltatás törléséhez használja a következő parancsot:

     ```azurecli
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Következő lépések

* Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](./dms-overview.md).
* További információ a Azure Database for PostgreSQLről: mi a [Azure Database for PostgreSQL?](../postgresql/overview.md).
