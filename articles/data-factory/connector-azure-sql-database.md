---
title: Adatok másolása és átalakítása a Azure SQL Database
description: Megtudhatja, hogyan másolhatja az adatokat a Azure SQL Database és onnan más adatokat, és hogyan alakíthatja át Azure SQL Database adatokat a Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 75615b4bb8773d0c0b8f72278e5598462c779ceb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365229"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Adatok másolása és átalakítása Azure SQL Database a Azure Data Factory

> [!div class="op_single_selector" title1="Válassza ki a Azure Data Factory használt verzióját:"]
>
> - [1-es verzió](v1/data-factory-azure-sql-connector.md)
> - [Aktuális verzió](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet a Azure Data Factory-ban az adatok másolására a Azure SQL Database és a Adatfolyam használatával a Azure SQL Database. A további Azure Data Factory a bevezető [cikket.](introduction.md)

## <a name="supported-capabilities"></a>Támogatott képességek

Ez Azure SQL Database összekötő a következő tevékenységekhez támogatott:

- [Copy tevékenység](copy-activity-overview.md) [forrás/fogadó mátrix táblázatával](copy-activity-overview.md)
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

A Copy tevékenység összekötő a következő Azure SQL Database támogatja:

- Adatok másolása SQL-hitelesítéssel és Azure Active Directory (Azure AD) alkalmazás jogkivonat-hitelesítéssel szolgáltatásnévvel vagy az Azure-erőforrások felügyelt identitásával.
- Forrásként sql-lekérdezéssel vagy tárolt eljárással kell lekérdezni az adatokat. Másik lehetőségként párhuzamos másolást is választhat egy Azure SQL Database forrásból. További részletekért tekintse meg a Párhuzamos másolás [AZ SQL-adatbázisból](#parallel-copy-from-sql-database) című szakaszt.
- Fogadóként automatikusan létrehozza a céltáblát, ha nem létezik a forrásséma alapján; adatok hozzáfűzése egy táblához vagy tárolt eljárás egyéni logikával való meghozása a másolás során.

Ha kiszolgáló [nélküli](../azure-sql/database/serverless-tier-overview.md)Azure SQL Database használ, vegye figyelembe, hogy ha a kiszolgáló szüneteltetve van, a tevékenység futtatása meghiúsul ahelyett, hogy megvárja, amíg az automatikus folytatás készen áll. Hozzáadhat tevékenység-újrapróbálkozásokat, vagy láncolhat további tevékenységeket, hogy a kiszolgáló a tényleges végrehajtásnak megfelelő legyen.

>[!NOTE]
> Azure SQL Database [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) összekötő jelenleg nem támogatja. A munka során használhat általános [ODBC-összekötőt](connector-odbc.md) és egy SQL Server ODBC-illesztőt egy saját üzemeltetett integrációs környezeten keresztül. További információt a [Using Always Encrypted (Az Always Encrypted használata) című](#using-always-encrypted) szakaszban olvashat. 

> [!IMPORTANT]
> Ha az Azure integration Runtime használatával másol adatokat, konfigurálnia kell egy kiszolgálószintű tűzfalszabályt, hogy az [Azure-szolgáltatások](../azure-sql/database/firewall-configure.md) hozzáférnek a kiszolgálóhoz.
> Ha helyi integrációs környezetben másol adatokat, konfigurálja a tűzfalat úgy, hogy engedélyezze a megfelelő IP-címtartományt. Ebbe a tartományba tartozik a gép IP-címe, amely a virtuális géphez való Azure SQL Database.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen ismertetik az összekötőhöz Azure Data Factory entitások meghatározásához Azure SQL Database tulajdonságokat.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Ezek a tulajdonságok támogatottak egy Azure SQL Database szolgáltatáshoz:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **type (típus)** tulajdonságot **AzureSqlDatabase (AzureSqlDatabase) típusúra kell állítani.** | Igen |
| connectionString (kapcsolati karakterlánc) | Adja meg a **connectionString** Azure SQL Database-példányhoz való csatlakozáshoz szükséges adatokat. <br/>Jelszót vagy egyszerű szolgáltatáskulcsot is Azure Key Vault. Sql-hitelesítés esetén húzza ki `password` a konfigurációt a kapcsolati sztringből. További információért tekintse meg a táblázatot követő JSON-példát, és tárolja a hitelesítő adatokat a [Azure Key Vault.](store-credentials-in-key-vault.md) | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítóját. | Igen, ha Azure AD-hitelesítést használ szolgáltatásnévvel |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt a **mezőt SecureString** -ként, hogy biztonságosan tárolja a Azure Data Factory vagy hivatkozhat [egy, a](store-credentials-in-key-vault.md)következőben Azure Key Vault. | Igen, ha Azure AD-hitelesítést használ szolgáltatásnévvel |
| Bérlő | Adja meg a bérlő adatait, például azt a tartománynevet vagy bérlőazonosítót, amelyben az alkalmazás található. A lekéréshez vigye az egérmutatót a jobb felső sarokban Azure Portal. | Igen, ha Azure AD-hitelesítést használ szolgáltatásnévvel |
| azureCloudType | Egyszerű szolgáltatás hitelesítéséhez adja meg az Azure-felhőkörnyezet típusát, amelyben az Azure AD-alkalmazás regisztrálva van. <br/> Megengedett értékek: **AzurePublic,** **AzureChina,** **AzureUsGovernment és** **AzureGermany.** Alapértelmezés szerint a rendszer az adat-előállító felhőkörnyezetét használja. | Nem |
| connectVia | Ez [az integrációskörnyezet](concepts-integration-runtime.md) az adattárhoz való csatlakozásra használható. Használhatja az Azure-beli integrációs és helyi integrációskörnyezetet, ha az adattár egy magánhálózatban található. Ha nincs megadva, a rendszer az alapértelmezett Azure integration Runtime-t használja. | Nem |

A különböző hitelesítési típusok esetében tekintse meg az előfeltételekről és JSON-mintákról a következő szakaszokat:

- [SQL-hitelesítés](#sql-authentication)
- [Azure AD-alkalmazás jogkivonatának hitelesítése: Szolgáltatásnév](#service-principal-authentication)
- [Azure AD-alkalmazás jogkivonatának hitelesítése: Az Azure-erőforrások felügyelt identitása](#managed-identity)

>[!TIP]
>Ha a "UserErrorFailedToConnectToSqlServer" hibakóddal és "Az adatbázis munkamenetkorlátja XXX, és el lett érve" hibaüzenet jelenik meg, adja hozzá a sztringet a kapcsolati sztringhez, és próbálkozzon `Pooling=false` újra. `Pooling=false` az **SHIR (saját üzemeltetett Integration Runtime) típusú összekapcsolt** szolgáltatás beállításához is ajánlott. A készletezés és más kapcsolati paraméterek új paraméternevekként és -értékekként hozzáadhatók a csatolt szolgáltatás létrehozási űrlap **további** kapcsolati tulajdonságainak szakaszában.

### <a name="sql-authentication"></a>SQL-hitelesítés

**Példa: SQL-hitelesítés használata**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: jelszó a Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Szolgáltatásnév-alapú Azure AD-alkalmazás jogkivonat-hitelesítéséhez kövesse az alábbi lépéseket:

1. [Hozzon létre egy Azure Active Directory alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) a Azure Portal. Jegyezze fel az alkalmazás nevét és a csatolt szolgáltatást meghatározó alábbi értékeket:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. [Ha még Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) még nem tette meg, Azure Portal a kiszolgálón egy rendszergazdai rendszergazdát. Az Azure AD-rendszergazdának Azure AD-felhasználónak vagy Azure AD-csoportnak kell lennie, de nem lehet egyszerű szolgáltatás. Ez a lépés úgy történik, hogy a következő lépésben egy Azure AD-identitással létrehoz egy tartalmazottadatbázis-felhasználót a szolgáltatásnévhez.

3. [Hozzon létre tartalmazottadatbázis-felhasználókat](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) a szolgáltatásnévhez. Olyan Azure AD-identitással csatlakozzon az adatbázishoz, amelyről vagy amelyről adatokat szeretne másolni az SQL Server Management Studio használatával, legalább ALTER ANY USER engedéllyel rendelkező Azure AD-identitással. Futtassa a következő T-SQL-t:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Adjon a szolgáltatásnévnek a szokásos módon szükséges engedélyeket az SQL-felhasználók vagy mások számára. Futtassa a következő kódot. További beállításokért tekintse meg ezt [a dokumentumot.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Konfigurálja a Azure SQL Database szolgáltatást a Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Szolgáltatásnév-hitelesítést használó csatolt szolgáltatás példája

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure-erőforrások felügyelt identitásának hitelesítése

Az adat-előállítók társíthatóak az adott adat-előállítót képviselő [Azure-erőforrások](data-factory-service-identity.md) felügyelt identitásával. Ezt a felügyelt identitást használhatja a Azure SQL Database hitelesítéshez. A kijelölt gyár ezzel az identitással férhet hozzá az adatbázishoz, és adatokat másolhat az adatbázisba.

A felügyelt identitások hitelesítésének használatával kapcsolatban kövesse az alábbi lépéseket.

1. [Ha még Azure Active Directory,](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) Azure Portal ki a kiszolgálón egy rendszergazdai rendszergazdát. Az Azure AD-rendszergazda lehet Azure AD-felhasználó vagy Azure AD-csoport. Ha rendszergazdai szerepkört ad a csoportnak a felügyelt identitással, hagyja ki a 3. és a 4. lépést. A rendszergazda teljes hozzáféréssel rendelkezik az adatbázishoz.

2. [Hozzon létre tartalmazottadatbázis-felhasználókat](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) a Azure Data Factory identitáshoz. Csatlakozzon ahhoz az adatbázishoz, amelyről vagy amelybe adatokat szeretne másolni az SQL Server Management Studio és hasonló eszközökkel, olyan Azure AD-identitással, amely legalább ALTER ANY USER engedéllyel rendelkezik. Futtassa a következő T-SQL-t:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Adjon a Data Factory felügyelt identitásnak a szokásos módon szükséges engedélyeket az SQL-felhasználók és mások számára. Futtassa az alábbi kódot. További beállításokért tekintse meg ezt [a dokumentumot.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Konfigurálja a Azure SQL Database szolgáltatást a Azure Data Factory.

**Példa**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek meghatározásához rendelkezésre álló szakaszok és tulajdonságok teljes listáját lásd: [Adatkészletek.](./concepts-datasets-linked-services.md)

A következő tulajdonságok támogatottak Azure SQL Database adatkészlethez:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az **adatkészlet type** (típus) tulajdonságát **AzureSqlTable (AzureSqlTable) beállításra kell állítani.** | Igen |
| schema | A séma neve. |Nem a forráshoz, az Igen a fogadóhoz  |
| tábla | A tábla/nézet neve. |Nem a forráshoz, az Igen a fogadóhoz  |
| tableName | A tábla/nézet neve sémával. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Új számítási feladatokhoz használja a és `schema` a `table` et. | Nem a forráshoz, igen a fogadóhoz |

### <a name="dataset-properties-example"></a>Példa adatkészlet-tulajdonságokra

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek meghatározásához elérhető szakaszok és tulajdonságok teljes listájáért lásd: [Folyamatok.](concepts-pipelines-activities.md) Ez a szakasz a forrás és a fogadó által Azure SQL Database tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database mint forrás

>[!TIP]
>Ha hatékonyan szeretne adatokat Azure SQL Database adatok particionálásával, olvassa el az SQL Database [párhuzamos másolásával kapcsolatos további információkat.](#parallel-copy-from-sql-database)

Az adatok másolása Azure SQL Database következő tulajdonságokat támogatja a másolási tevékenység **forrása szakaszban:**

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **másolási** tevékenység forrásának type tulajdonságát **AzureSqlSource-ba kell állítani.** Az "SqlSource" típus továbbra is támogatott a visszamenőleges kompatibilitás érdekében. | Igen |
| sqlReaderQuery | Ez a tulajdonság az egyéni SQL-lekérdezés használatával olvassa be az adatokat. Például: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | A forrástáblából adatokat beolvasó tárolt eljárás neve. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. | Nem |
| storedProcedureParameters | A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név- vagy értékpárok. A paraméterek nevének és kis-nevének egyeznie kell a tárolt eljárás paramétereinek nevével és kis- és kisajátítási értékével. | Nem |
| isolationLevel (elkülönítési szint) | Megadja az SQL-forrás tranzakciózárolási viselkedését. Az engedélyezett értékek aek: **ReadCommitted**, **ReadUncommitted,** **RepeatableRead,** **Serializable**, **Snapshot**. Ha nincs megadva, a rendszer az adatbázis alapértelmezett elkülönítési szintjét használja. További [részletekért tekintse meg](/dotnet/api/system.data.isolationlevel) ezt a dokumentumot. | Nem |
| partitionOptions (partíciólehetőségek) | Az adatoknak a rendszerből való betöltéséhez használt adatpartíciós Azure SQL Database. <br>Megengedett értékek: **Nincs** (alapértelmezett), **PhysicalPartitionsOfTable**, és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem ), a másolási tevékenység beállítása vezérli, hogy a párhuzamosság milyen mértékben legyen párhuzamos az Azure SQL Database adatok párhuzamos `None` [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) betöltéséhez. | Nem |
| partitionSettings | Adja meg az adatpartíció beállításainak csoportját. <br>Akkor alkalmazza, ha a partíciós beállítás nem `None` . | Nem |
| ***A `partitionSettings` alatt:*** | | |
| partitionColumnName | Adja meg a forrásoszlop nevét egész számban vagy **dátum/dátum/idő** típusban ( , , , , , , vagy ), amelyet a tartomány particionálása fog használni a `int` `smallint` párhuzamos `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` másoláshoz. Ha nincs megadva, a rendszer automatikusan a tábla indexét vagy elsődleges kulcsát használja partícióoszlopként.<br>Akkor alkalmazza, ha a partíciós beállítás `DynamicRange` . Ha lekérdezést használ a forrásadatok lekéréséhez, hookot használjon a  `?AdfDynamicRangePartitionCondition ` WHERE záradékban. Példaként tekintse meg az [SQL-adatbázis párhuzamos másolása című](#parallel-copy-from-sql-database) szakaszt. | Nem |
| partitionUpperBound | A partíciótartomány-felosztás partícióoszlopának maximális értéke. Ezzel az értékkel határozható meg a partíciók hossza, nem pedig a tábla sorai szűréséhez. A rendszer a tábla vagy a lekérdezés eredményének összes sorát particionálta és átmásolta. Ha nincs megadva, a másolási tevékenység automatikusan észleli az értéket.  <br>Akkor alkalmazza, ha a partíciós beállítás `DynamicRange` . Példaként tekintse meg az [SQL-adatbázis párhuzamos másolása című szakaszt.](#parallel-copy-from-sql-database) | Nem |
| partitionLowerBound | A partíciótartomány-felosztás partícióoszlopának minimális értéke. Ez az érték határozza meg a partíciós hosszt, nem pedig a tábla sorait szűri. A rendszer a tábla vagy a lekérdezés eredményének összes sorát particionálta és átmásolta. Ha nincs megadva, a másolási tevékenység automatikusan észleli az értéket.<br>Akkor alkalmazza, ha a partíciós beállítás `DynamicRange` . Példaként tekintse meg az [SQL-adatbázis párhuzamos másolása című szakaszt.](#parallel-copy-from-sql-database) | Nem |

**Vegye figyelembe a következő szempontokat:**

- Ha **az sqlReaderQuery** meg van adva az **AzureSqlSource-hoz,** a másolási tevékenység ezt a lekérdezést Azure SQL Database forráson futtatja az adatok lekérdezése érdekében. Tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával is megadhat, ha a tárolt eljárás paramétereket használ.
- Ha tárolt eljárást használ a forrásban az adatok lekéréséhez, vegye figyelembe, hogy ha a tárolt eljárás más séma visszaadásaként lett kialakítva, amikor más paraméterértéket ad át, hibát vagy váratlan eredményt láthat a séma felhasználói felületről való importálásakor vagy az adatok SQL-adatbázisba történő automatikus táblakészítéssel történő másolása során.

#### <a name="sql-query-example"></a>SQL-lekérdezési példa

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Példa tárolt eljárásra

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Tárolt eljárás definíciója

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database mint fogadó

> [!TIP]
> További információ a támogatott írási viselkedésről, konfigurációkról és ajánlott eljárásokról: Az adatoknak a [Azure SQL Database.](#best-practice-for-loading-data-into-azure-sql-database)

Az adatok másolása Azure SQL Database következő tulajdonságokat támogatja a másolási tevékenység **fogadója szakaszban:**

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **másolási** tevékenység fogadó type (típus) tulajdonságát **AzureSqlSink (AzureSqlSink)** beállításra kell állítani. Az "SqlSink" típus továbbra is támogatott a visszamenőleges kompatibilitás érdekében. | Igen |
| preCopyScript | Adjon meg egy SQL-lekérdezést a másolási tevékenység számára, mielőtt adatokat ír a Azure SQL Database. Másolási futtatásonként csak egyszer lesz meghívva. Ezzel a tulajdonságkal tisztítja meg az előre betöltött adatokat. | Nem |
| tableOption (táblalehetőség) | Megadja, hogy a rendszer automatikusan hozza-e létre a [fogadó táblát,](copy-activity-overview.md#auto-create-sink-tables) ha nem létezik a forrásséma alapján. <br>Az automatikus táblakészítés nem támogatott, ha a fogadó a tárolt eljárást határozza meg. <br>Megengedett értékek: `none` (alapértelmezett), `autoCreate` . | Nem |
| sqlWriterStoredProcedureName | A tárolt eljárás neve, amely meghatározza, hogyan kell a forrásadatokat a céltáblára alkalmazni. <br/>A tárolt eljárás *kötegenként lesz meghívva.* Az olyan műveletek esetében, amelyek csak egyszer futnak, és nincs köze a forrásadatokhoz, például törlés vagy csonkálás, használja a `preCopyScript` tulajdonságot.<br>Tekintse meg a Tárolt [eljárás meghívása SQL-fogadóból című példáját.](#invoke-a-stored-procedure-from-a-sql-sink) | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott táblatípus paraméterneve.  |Nem |
| sqlWriterTableType |A tárolt eljárásban használt táblatípus neve. A másolási tevékenység elérhetővé teszi az áthelyezni szükséges adatokat egy ideiglenes táblában ezzel a táblatípussal. A tárolt eljáráskód ezután egyesítheti a másolt adatokat a meglévő adatokkal. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név- és értékpárok. A paraméterek nevének és kis-nevének egyeznie kell a tárolt eljárás paramétereinek nevével és kis- és kisajátítási értékével. | Nem |
| writeBatchSize | Az SQL-táblába kötegenként beszúrt *sorok száma.*<br/> Az engedélyezett érték **egész szám** (sorok száma). Alapértelmezés szerint a Azure Data Factory a sorméret alapján dinamikusan határozza meg a megfelelő kötegméretet. | Nem |
| writeBatchTimeout | A kötegelt beszúrási művelet befejeződés előtti várakozási ideje.<br/> Az engedélyezett érték az **időkorrekta.** Ilyen például a "00:30:00" (30 perc). | Nem |
| disableMetricsCollection (MetricsCollection letiltása) | Data Factory gyűjti a másolási teljesítményoptimalizáláshoz Azure SQL Database DKU-khoz hasonló metrikákat és javaslatokat, amelyek további fő adatbázis-hozzáférést vezetnek be. Ha ezzel a viselkedéssel kell aggódnia, kapcsolja `true` ki a következőt: . | Nem (az alapértelmezett érték `false` ) |
| maxConcurrentConnections |Az adattárhoz a tevékenység futtatása során létrehozott egyidejű kapcsolatok felső korlátja. Csak akkor adjon meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| Nem |

**1. példa: Adatok hozzáfűzése**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**2. példa: Tárolt eljárás meghívása másolás közben**

További információ: Tárolt eljárás [meghívása SQL-fogadóból.](#invoke-a-stored-procedure-from-a-sql-sink)

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Párhuzamos másolás SQL-adatbázisból

A Azure SQL Database összekötő a másolási tevékenységben beépített adatpartíciót biztosít az adatok párhuzamos másolására. Az adatpartíciós beállításokat a **másolási** tevékenység Forrás lapján találja.

![Képernyőkép a partíció beállításairól](./media/connector-sql-server/connector-sql-partition-options.png)

Ha engedélyezi a particionált másolást, a másolási tevékenység párhuzamos lekérdezéseket futtat a Azure SQL Database a forráson az adatok partíciók szerint való betöltéséhez. A párhuzamos fokot a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállítása vezérli. Ha például négyre van állítva, a Data Factory egyidejűleg generál és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és minden lekérdezés lekéri az adatok egy részét `parallelCopies` a Azure SQL Database.

Ajánlott engedélyezni a párhuzamos másolást adatpartícióval, különösen akkor, ha nagy mennyiségű adatot tölt be a Azure SQL Database. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációkat íme. Amikor fájlalapú adattárba másol adatokat, ajánlott több fájlként írni egy mappába (csak mappanév megadása), ebben az esetben a teljesítmény jobb, mint egyetlen fájlba írni.

| Eset                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés nagy táblából, fizikai partícióval.        | **Partíciós** beállítás: A tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory észleli a fizikai partíciókat, és partíciók szerint másolja az adatokat. <br><br/>Annak ellenőrzéséhez, hogy a tábla rendelkezik-e fizikai partícióval vagy sem, tekintse meg ezt [a lekérdezést.](#sample-query-to-check-physical-partition) |
| Teljes betöltés nagy táblából, fizikai partíciók nélkül, egész számmal vagy dátum/idő oszloppal az adatpartícióhoz. | **Partíciós beállítások:** Dinamikus tartomány partíciója.<br>**Partícióoszlop** (nem kötelező): Adja meg az adatok particionálhoz használt oszlopát. Ha nincs megadva, a rendszer az index vagy az elsődleges kulcs oszlopot használja.<br/>**Particionálás felső határa** és a partíció alsó **határa** (nem kötelező): Adja meg, hogy meg szeretné-e határozni a partíciók hosszúságát. Ez nem a tábla sorai szűréséhez szükséges, a tábla összes sora particionálva és másolható. Ha nincs megadva, a másolási tevékenység automatikusan észleli az értékeket.<br><br>Ha például az "ID" partícióoszlop értéke 1 és 100 között van, és az alsó korlátot 20-ra, a felső korlátot pedig 80-ra, a párhuzamos másolással 4-et használva, a Data Factory 4 partícióval olvassa be az adatokat – az <=20, [21, 50], [51, 80] és >=81 tartományba tartozó azonosítókat. |
| Nagy mennyiségű adat betöltése egyéni lekérdezéssel, fizikai partíciók nélkül, valamint egész számmal, dátum/dátum/idő oszloppal az adat particionáláshoz. | **Partíciós beállítások:** Dinamikus tartomány partíciója.<br>**Lekérdezés:** `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partícióoszlop:** Adja meg az adatok particionálhoz használt oszlopát.<br>**Particionálás felső határa** és a partíció alsó **határa** (nem kötelező): Adja meg, hogy meg szeretné-e határozni a partíciók hosszúságát. Ez nem a tábla sorait szűri, a lekérdezés eredményének minden sora particionálva lesz és másolható. Ha nincs megadva, a másolási tevékenység automatikusan észleli az értéket.<br><br>A végrehajtás során a Data Factory lecseréli a helyére az egyes partíciók tényleges oszlopnevét és értéktartományát, és elküldi őket a `?AdfRangePartitionColumnName` Azure SQL Database. <br>Ha például az "ID" partícióoszlop értéke 1 és 100 között van, az alsó korlátot pedig 20-ra, a felső korlátot pedig 80-ra, a párhuzamos másolással 4-et, a Data Factory 4 partícióval – az <=20, [21, 50], [51, 80] és >=81 tartományba tartozó azonosítókat. <br><br>Az alábbi példalekérdezések különböző forgatókönyvekhez használhatók:<br> 1. A teljes tábla lekérdezése: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Lekérdezés oszlopkijelölést és további where-clause szűrőket tartalmazó táblából: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Lekérdezés segédlekérdezésekkel: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Lekérdezés a partícióval a segédlekérdezésben: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Ajánlott eljárások az adatok partíciós beállítással való betöltéséhez:

1. Válassza az oszlopot partícióoszlopként (például elsődleges kulcsként vagy egyedi kulcsként), hogy elkerülje az adatdedukálást. 
2. Ha a tábla beépített partícióval rendelkezik, használja a "Tábla fizikai partíciói" partíciós lehetőséget a jobb teljesítmény érdekében.    
3. Ha adatmásoló Azure Integration Runtime használ, nagyobb " Adatintegrációs egységek[(DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) beállítással több számítási erőforrást használhat. Ellenőrizze a vonatkozó forgatókönyveket.
4. "[A](copy-activity-performance-features.md#parallel-copy)másolás párhuzamosságának foka" a partíciószámokat szabályozhatja, és ezt a számot túl nagyra állítva a teljesítményt okozva, javasoljuk, hogy ezt a számot a következőre kell beállítva: (DIU vagy saját üzemelő integrációspont-csomópontok száma) * (2–4).

**Példa: teljes terhelés nagy táblából fizikai partíciók esetén**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Példa: lekérdezés dinamikustartomány-partícióval**

```json
"source": {
    "type": "AzureSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Mintalekérdezés a fizikai partíció ellenőrzéséhez

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Ha a tábla fizikai partícióval rendelkezik, a "HasPartition" az alábbihoz hasonló "igen" lesz.

![Sql-lekérdezés eredménye](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Ajánlott eljárás az adatok betöltéséhez a Azure SQL Database

Amikor adatokat másol a Azure SQL Database, más írási viselkedésre lehet szüksége:

- [Hozzáfűzés:](#append-data)A forrásadatok csak új rekordokat tartalmaznak.
- [Upsert :A](#upsert-data)forrásadatok beszúrásokat és frissítéseket is tartalmaznak.
- [Felülírás:](#overwrite-the-entire-table)Minden alkalommal egy teljes dimenziótáblát szeretnék újratöltésre.
- [Írás egyéni logikával:](#write-data-with-custom-logic)További feldolgozásra van szükségem a céltáblába való végleges beszúrás előtt.

Tekintse meg a megfelelő szakaszokat a konfigurálásról a Azure Data Factory ajánlott eljárásokban.

### <a name="append-data"></a>Adatok hozzáfűzése

Az adatok hozzáfűzése a fogadó összekötő alapértelmezett Azure SQL Database viselkedése. Azure Data Factory tömeges beszúrásokkal hatékonyan ír a táblába. A forrást és a fogadót ennek megfelelően konfigurálhatja a másolási tevékenységben.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adatot kell másolnia, a másolási tevékenységgel az összes rekordot tömegesen betöltheti egy előkészítési táblába, majd futtathat egy tárolt eljárási tevékenységet egy [MERGE](/sql/t-sql/statements/merge-transact-sql) vagy INSERT/UPDATE utasítás alkalmazásához. 

Copy tevékenység jelenleg nem támogatja natív módon az adatok ideiglenes adatbázistáblába való betöltését. Ennek több tevékenység kombinációjával való beállításának speciális módját lásd: Optimize Azure SQL Database Bulk Upsert scenarios (Az Azure SQL Database [upsert forgatókönyvek optimalizálása).](https://github.com/scoriani/azuresqlbulkupsert) Az alábbiakban egy állandó tábla átmenetiként való használatának mintáját mutatjuk be.

Például a Azure Data Factory létrehozhat egy olyan folyamatot, amely  Copy tevékenység tárolt eljárási tevékenységgel **van összefűve.** Az előbbi adatokat másol a forrástárból egy Azure SQL Database előkészítési táblába, például **UpsertStagingTable** táblába az adatkészlet táblaneveként. Ezután az utóbbi egy tárolt eljárást hív meg, amely egyesíti az előkészítési táblából származó forrásadatokat a céltáblával, és megtisztítja az előkészítési táblát.

![Beszúrás és frissítés](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Az adatbázisban definiálhat egy EGYESÍTÉS logikával tárolt eljárást az alábbi példához hasonlóan, amelyre az előző tárolt eljárási tevékenység mutat. Tegyük fel, hogy a cél a **Marketing** tábla három oszloppal: **ProfileID,** **State**, és **Category**. Az upsert (upsert) et a **ProfileID oszlop alapján** kell megtenni.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
   MERGE TargetTable AS target
   USING UpsertStagingTable AS source
   ON (target.[ProfileID] = source.[ProfileID])
   WHEN MATCHED THEN
      UPDATE SET State = source.State
    WHEN NOT matched THEN
       INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    TRUNCATE TABLE UpsertStagingTable
END
```

**2. lehetőség:** A másolási [tevékenységben meghívhat](#invoke-a-stored-procedure-from-a-sql-sink)egy tárolt eljárást. Ez a módszer (a tulajdonság által szabályozott) kötegeket futtat a forrástáblában ahelyett, hogy tömeges beszúrást használ a másolási `writeBatchSize` tevékenység alapértelmezett megközelítéseként.

**3. lehetőség:** A Mapping [Adatfolyam](#sink-transformation) beépített beszúrási/upsert/update metódusokat kínál.

### <a name="overwrite-the-entire-table"></a>A teljes tábla felülírása

A **preCopyScript** tulajdonságot a másolási tevékenység fogadóban konfigurálhatja. Ebben az esetben minden futó másolási tevékenység esetében a Azure Data Factory futtatja először a szkriptet. Ezután futtatja a másolatot az adatok beszúrása érdekében. Ha például felül szeretné írni a teljes táblát a legújabb adatokkal, adjon meg egy szkriptet, amely először törli az összes rekordot, mielőtt tömegesen betölti az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Adatok írása egyéni logikával

Az egyéni logikával való adatírás lépései hasonlóak az [Upsert-adatok](#upsert-data) szakaszban leírtakhoz. Ha további feldolgozást kell alkalmaznia a forrásadatok céltáblába való végleges beillesztése előtt, betölthet egy előkészítési táblába, meghívhat tárolt eljárási tevékenységet, vagy meghívhat egy tárolt eljárást a másolási tevékenység fogadójában az adatok alkalmazásához, vagy használhatja a Leképezés Adatfolyam.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Tárolt eljárás meghívása SQL-fogadóból

Amikor adatokat másol a Azure SQL Database, konfigurálhat és meghívhat egy felhasználó által megadott tárolt eljárást további paraméterekkel a forrástábla minden kötegében. A tárolt eljárás funkció tábla értékű paramétereket [használ.](/dotnet/framework/data/adonet/sql/table-valued-parameters)

Akkor használhat tárolt eljárást, ha a beépített másolási mechanizmusok nem szolgálják ezt a célt. Ilyen például, ha további feldolgozást szeretne alkalmazni a forrásadatok céltáblába való végleges beillesztése előtt. További feldolgozási példák például akkor, ha oszlopokat szeretne egyesíteni, további értékeket szeretne keresni, és egynél több táblába szeretné beszúrni őket.

Az alábbi minta bemutatja, hogyan lehet egy tárolt eljárás használatával upsert műveletet egy táblában a Azure SQL Database. Tegyük fel, hogy a bemeneti adatok és a fogadó **Marketing** tábla mind három oszlopot tartalmaz: **ProfileID,** **State**, és **Category.** Az upsert (upsert) et a **ProfileID** oszlop alapján kell megtenni, és csak a "ProductA" (TermékA) nevű adott kategóriára alkalmazza.

1. Az adatbázisban definiálja a tábla típusát az **sqlWriterTableType típussal azonos névvel.** A táblatípus sémája megegyezik a bemeneti adatok által visszaadott sémával.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Az adatbázisban adja meg a tárolt eljárást az **sqlWriterStoredProcedureName névvel azonos néven.** Kezeli a megadott forrásból származó bemeneti adatokat, és egyesíti őket a kimeneti táblával. A tárolt eljárásban a táblatípus paraméterneve megegyezik az adatkészletben definiált **tableName** paraméter nevével.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. A Azure Data Factory adja meg az **SQL-fogadó** szakaszt a másolási tevékenységben a következőképpen:

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A leképezési adatfolyamban lévő adatok átalakításakor a táblákból olvashat és írhat Azure SQL Database. További információért tekintse meg a [forrás-átalakítást](data-flow-source.md) és a [fogadóátalakítást a](data-flow-sink.md) leképezési adatfolyamok között.

### <a name="source-transformation"></a>Forrásátalakítás

A Azure SQL Database beállítások a forrásátalakítás  Forrásbeállítások lapján érhetők el.

**Bemenet:** Válassza ki, hogy a forrást egy táblára (a megfelelőjére) mutasson, vagy ```Select * from <table-name>``` adjon meg egy egyéni SQL-lekérdezést.

**Lekérdezés:** Ha a beviteli mezőben a Lekérdezés lehetőséget választja, adjon meg egy SQL-lekérdezést a forráshoz. Ez a beállítás felülírja az adatkészletben kiválasztott összes táblát. **Az Order By** záradékok itt nem támogatottak, de teljes SELECT FROM utasítást állíthat be. Felhasználó által definiált táblafunkciókat is használhat. **A select * from udfGetData()** egy UDF az SQL-ben, amely egy táblát ad vissza. Ez a lekérdezés egy forrástáblát hoz létre, amely használható az adatfolyamban. A lekérdezések használatával emellett a tesztelési és keresési sorok is csökkenthetők.

**Tárolt eljárás:** Akkor válassza ezt a lehetőséget, ha a forrásadatbázisból végrehajtott tárolt eljárásból szeretne leképezés- és forrásadatokat létrehozni. Begépelheti a sémát, az eljárás nevét és a paramétereket, vagy a Frissítés gombra kattintva megkérheti az ADF-et, hogy derítse fel a sémákat és az eljárásneveket. Ezután az Importálás gombra kattintva importálhatja az összes eljárásparamétert a űrlap ``@paraName`` használatával.

![Tárolt eljárás](media/data-flow/stored-procedure-2.png "Tárolt eljárás")

- SQL-példa: ```Select * from MyTable where customerId > 1000 and customerId < 2000```
- Paraméteres SQL-példa: ``"select * from {$tablename} where orderyear > {$year}"``

**Köteg mérete:** Adja meg a köteg méretét, hogy a nagy adatokat olvasásra darabolja.

**Elkülönítési szint:** A leképezési adatfolyamban az SQL-források alapértelmezett beolvasatlan olvasása. Az elkülönítési szintet itt az alábbi értékek egyikére módosíthatja:

- Read Committed
- Olvasás Nincs megadva
- Ismételhető olvasás
- Szerializálható
- Nincs (hagyja figyelmen kívül az elkülönítési szintet)

![Elkülönítési szint](media/data-flow/isolationlevel.png "Elkülönítési szint")

### <a name="sink-transformation"></a>Fogadó-átalakítás

A Azure SQL Database beállítások a fogadóátalakítás **Beállítások** lapján érhetők el.

**Update metódus:** Meghatározza, hogy milyen műveletek engedélyezettek az adatbázis célhelyen. Az alapértelmezett beállítás az, hogy csak a beszúrásokat engedélyezi. A sorok frissítéséhez, upsert műveletéhez vagy törléséhez alter-row átalakításra van szükség az ilyen műveletek sorai címkézéséhez. A frissítésekhez, upsert-hez és törléshez kulcsoszlopot vagy oszlopokat kell beállítani annak meghatározásához, hogy melyik sort kell módosítani.

![Kulcsoszlopok](media/data-flow/keycolumn.png "Kulcsoszlopok")

Az itt kulcsként használt oszlopnevet az ADF a következő frissítés, upsert és delete részeként fogja használni. Ezért olyan oszlopot kell választania, amely létezik a Fogadó leképezésben. Ha nem szeretné ebbe a kulcsoszlopba írni az értéket, kattintson a "Kulcsoszlopok írásának kihagyása" lehetőségre.

A céltábla frissítéséhez használt kulcsoszlopot paraméteresre Azure SQL Database. Ha egy összetett kulcshoz több oszlop is tartozik, kattintson a "Custom Expression" (Egyéni kifejezés) elemre, és dinamikus tartalmat adhat hozzá az ADF adatfolyam-kifejezési nyelvvel, amely sztringek tömbjeként egy összetett kulcs oszlopnevét is tartalmazhatja.

**Tábla művelet:** Meghatározza, hogy az összes sort újra létre kell-e hozni vagy el kell-e távolítani a céltáblából az írás előtt.

- Nincs: A táblán nem történik művelet.
- Újbóli: A tábla el lesz dobva, majd újra létre lesz hozva. Kötelező, ha dinamikusan hoz létre új táblát.
- Csonkálás: A céltábla összes sorát eltávolítja a rendszer.

**Köteg mérete:** Azt szabályozza, hogy hány sor van megírva az egyes gyűjtőkben. A nagyobb kötegméretek javítják a tömörítést és a memóriaoptimalizálást, de fennáll a memória-kivételek kockázata az adatok gyorsítótárazása során.

**A TempDB használata:** Alapértelmezés szerint a Data Factory egy globális ideiglenes táblát fog használni az adatok tárolására a betöltési folyamat részeként. Másik lehetőségként törölje a "TempDB használata" jelölőnégyzet jelölését, és megkérheti a Data Factory-t, hogy tárolja az ideiglenes holding táblát egy felhasználói adatbázisban, amely a fogadóhoz használt adatbázisban található.

![Ideiglenes adatbázis használata](media/data-flow/tempdb.png "Ideiglenes adatbázis használata")

**ELŐZETES és utólagos SQL-szkriptek:** Adja meg a többsoros SQL-szkripteket, amelyek végrehajtása az (előfeldolgozás) előtt és után (utófeldolgozás) az adatok a fogadó adatbázisba való írása előtt és után lesz végrehajtva

![SQL-feldolgozási szkriptek elő- és utótagja](media/data-flow/prepost1.png "SQL-feldolgozási szkriptek")

### <a name="error-row-handling"></a>Hibát tartalmazó sorok kezelése

Az adatbázisba Azure SQL egyes adatsorok meghiúsulhatnak a célhely által beállított korlátozások miatt. Néhány gyakori hiba:

*    Sztring- vagy bináris adatok csonkolva lesznek a táblában
*    A NULL érték nem szúrható be az oszlopba
*    Az INSERT utasítás ütközött a CHECK megkötéssel

Alapértelmezés szerint az adatfolyam-futtatás az első hibán sikertelen lesz. Választhatja a Folytatás hiba **esetén lehetőséget,** amely lehetővé teszi az adatfolyam befejezését, még akkor is, ha az egyes sorok hibásak. Azure Data Factory különböző lehetőségeket kínál a hibasorok kezeléshez.

**Tranzakció véglegesítése:** Válassza ki, hogy az adatok egyetlen tranzakcióban vagy kötegben vannak-e megírva. Egyetlen tranzakció rosszabb teljesítményt nyújt, de a tranzakció befejezéséig mások nem fogják látni az adatokat.  

**Elutasított adatok kimenete:** Ha engedélyezve van, a hibasorokat kiírhatja egy csv-fájlba egy Azure Blob Storage egy Azure Data Lake Storage Gen2 választott fiókban. Ez három további oszloppal írja meg a hibasorokat: az SQL-művelet, például az INSERT vagy az UPDATE, az adatfolyam hibakódja és a sorban lévő hibaüzenet.

**Sikeres jelentés hiba esetén:** Ha engedélyezve van, az adatfolyam akkor is sikeresként lesz megjelölve, ha hibasorokat talál. 

![Hibát tartalmazó sorok kezelése](media/data-flow/sql-error-row-handling.png "Hibát tartalmazó sorok kezelése")


## <a name="data-type-mapping-for-azure-sql-database"></a>Adattípus-leképezés Azure SQL Database

Amikor a rendszer adatokat másol vagy Azure SQL Database, a rendszer a következő leképezéseket használja Azure SQL Database és Azure Data Factory köztes adattípusok között. A másolási tevékenység a forrássémát és az adattípust a fogadóhoz való leképezésével kapcsolatos további információkért lásd: Séma- és [adattípus-leképezések.](copy-activity-schema-and-type-mapping.md)

| Azure SQL Database adattípus | Azure Data Factory köztes adattípus |
|:--- |:--- |
| bigint |Int64 |
| binary |Bájt[] |
| bit |Logikai |
| Char |Sztring, Karakter[] |
| dátum |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset (Dátum és idő lefolyása) |DateTimeOffset (Dátum és idő lefolyása) |
| Tizedesjegy |Tizedesjegy |
| FILESTREAM attribútum (varbinary(max)) |Bájt[] |
| Float |Dupla |
| image |Bájt[] |
| int |Int32 |
| Pénzt |Tizedesjegy |
| nchar |Sztring, Karakter[] |
| ntext (szöveg) |Sztring, Karakter[] |
| numerikus |Tizedesjegy |
| nvarchar |Sztring, Karakter[] |
| valós szám |Egyirányú |
| rowversion (sorverzió) |Bájt[] |
| smalldatetime (smalldatetime) |DateTime |
| smallint |Int16 |
| smallmoney |Tizedesjegy |
| sql_variant |Objektum |
| szöveg |Sztring, Karakter[] |
| time |időtartam |
| időbélyeg |Bájt[] |
| tinyint |Bájt |
| uniqueidentifier |Guid |
| varbinary (varbinary) |Bájt[] |
| varchar |Sztring, Karakter[] |
| xml |Sztring |

>[!NOTE]
> A tizedes tört köztes típusra leképező adattípusok esetében a Copy tevékenység legfeljebb 28 pontosságot támogat. Ha 28-asnál nagyobb pontosságú adatokat tartalmaz, érdemes lehet sztringgé alakítani az SQL-lekérdezésben.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságokkal kapcsolatos részletekért tekintse meg a [keresési tevékenységet.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>A GetMetadata tevékenység tulajdonságai

A tulajdonságokkal kapcsolatos részletekért tekintse meg a [GetMetadata tevékenységet](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>A Always Encrypted

Amikor adatokat másol a Azure SQL Database és [Always Encrypted,](/sql/relational-databases/security/encryption/always-encrypted-database-engine)használjon általános [ODBC-összekötőt,](connector-odbc.md) és SQL Server ODBC-illesztőt egy helyi Integration Runtime. Ez Azure SQL Database összekötő jelenleg nem támogatja Always Encrypted-összekötőt. 

Pontosabban:

1. Ha még nem Integration Runtime saját maga által üzemeltetett kiszolgálót, állítson be. További [részleteket](create-self-hosted-integration-runtime.md) a saját Integration Runtime cikk tartalmaz.

2. Töltse le a 64 bites ODBC-SQL Server [innen,](/sql/connect/odbc/download-odbc-driver-for-sql-server)és telepítse a Integration Runtime gépre. Az illesztő működését a Using Always Encrypted with the ODBC Driver for SQL Server (Az Always Encrypted használata az [ODBC-illesztővel) SQL Server.](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider)

3. Hozzon létre ODBC típusú csatolt szolgáltatást az SQL-adatbázishoz való csatlakozáshoz, tekintse meg az alábbi mintákat:

    - **SQL-hitelesítés használata:** Adja meg az ODBC  kapcsolati sztringet az alábbiak szerint, majd válassza az Alapszintű hitelesítés lehetőséget a felhasználónév és a jelszó beállításához.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Ha saját maga által üzemeltetett Integration Runtime Azure-beli  virtuális gépen futtatja, használhatja a felügyelt identitás hitelesítését az Azure-beli virtuális gép identitásával:

        1. Kövesse ugyanezeket [az előfeltételeket,](#managed-identity) ha adatbázis-felhasználót hoz létre a felügyelt identitáshoz, és megadja a megfelelő szerepkört az adatbázisban.
        2. A csatolt szolgáltatásban adja meg az ODBC  kapcsolati sztringet az alábbiak szerint, majd válassza a Névtelen hitelesítés lehetőséget, ahogy maga a kapcsolati sztring `Authentication=ActiveDirectoryMsi` jelzi.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Ennek megfelelően hozzon létre egy adatkészletet és egy másolási tevékenységet ODBC-típussal. További információ az [ODBC-összekötőről.](connector-odbc.md)

## <a name="next-steps"></a>Következő lépések

A forrásként és fogadóként támogatott adattárakat a másolási tevékenység támogatja a Azure Data Factory támogatott adattárakat és [formátumokat.](copy-activity-overview.md#supported-data-stores-and-formats)
