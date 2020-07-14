---
title: Adatmásolás és átalakítás Azure SQL Database
description: Megtudhatja, hogyan másolhat adatok Azure SQL Databaseba és onnan, és hogyan alakíthatja át a Azure SQL Database adatait Azure Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/28/2020
ms.openlocfilehash: d3fe5257b3db2057e805d2f2cd0c6e2a2973e211
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223057"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Azure SQL Database adatmásolása és átalakítása a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
>
> - [1-es verzió](v1/data-factory-azure-sql-connector.md)
> - [Aktuális verzió](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához és a Azure SQL Databaseához, valamint az adatfolyamatok használata az adatok Azure SQL Databaseban való átalakításához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure SQL Database-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Másolási tevékenység esetén ez az Azure SQL Database-összekötő támogatja ezeket a funkciókat:

- Az adatok másolása SQL-hitelesítéssel és Azure Active Directory (Azure AD) alkalmazás-jogkivonat-hitelesítéssel az Azure-erőforrások egyszerű szolgáltatásával vagy felügyelt identitásával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával beolvashatja az adatokból.
- Fogadóként automatikusan hozza létre a céltábla, ha nem létezik a forrásoldali séma alapján; az adattábla hozzáfűzésével vagy egy tárolt eljárás meghívásával egyéni logikával a másolás során.

>[!NOTE]
> Ez az összekötő jelenleg nem támogatja az Azure SQL Database [Always Encryptedt](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) . A megoldáshoz használhat egy [általános ODBC-összekötőt](connector-odbc.md) és egy SQL Server ODBC-illesztőt a saját üzemeltetésű integrációs modulon keresztül. További információ: [Always encrypted szakasz használata](#using-always-encrypted) . 

> [!IMPORTANT]
> Ha az Azure Integration Runtime használatával másol Adatmásolást, állítson be egy [kiszolgálói szintű tűzfalszabály-szabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) , hogy az Azure-szolgáltatások hozzáférhessenek a kiszolgálóhoz.
> Ha saját üzemeltetésű integrációs modul használatával másol Adatmásolást, konfigurálja a tűzfalat a megfelelő IP-címtartomány engedélyezéséhez. Ez a tartomány magában foglalja az Azure SQL Databasehoz való kapcsolódáshoz használt számítógép IP-címét.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Azure Data Factory Azure SQL Database-összekötőhöz tartozó entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Ezek a tulajdonságok Azure SQL Database társított szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **AzureSqlDatabase**értékre kell beállítani. | Igen |
| connectionString | A **ConnectionString** tulajdonsághoz Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatok megadása. <br/>A jelszó vagy a szolgáltatás egyszerű kulcsa is elhelyezhető Azure Key Vaultban. Ha SQL-hitelesítéssel rendelkezik, húzza `password` ki a konfigurációt a kapcsolatok sztringből. További információ: a táblázatot követő JSON-példa és a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md). | Igen |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. A mező megjelölése **SecureString** -ként a biztonságos tárolás Azure Data Factory vagy [Azure Key Vault tárolt titkos kód hivatkozása](store-credentials-in-key-vault.md)alapján. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| Bérlő | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlő AZONOSÍTÓját, amely alatt az alkalmazás található. Lekéri a Azure Portal jobb felső sarkában lévő egér fölé. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| Connectvia tulajdonsággal | Ez az [integrációs](concepts-integration-runtime.md) modul az adattárhoz való kapcsolódásra szolgál. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. | Nem |

Különböző hitelesítési típusok esetén tekintse át az előfeltételek és JSON-minták következő, az előfeltételeket és a JSON-mintákat ismertető szakaszt:

- [SQL-hitelesítés](#sql-authentication)
- [Azure AD-alkalmazás-jogkivonat hitelesítése: szolgáltatásnév](#service-principal-authentication)
- [Azure AD-alkalmazás-jogkivonat hitelesítése: felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha hibát észlelt a "UserErrorFailedToConnectToSqlServer" hibakódmal, és egy olyan üzenet, mint például "az adatbázis munkamenet-korlátja XXX és elérte," adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatási példa

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

**Jelszó Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
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

A szolgáltatás egyszerű Azure AD-alapú hitelesítésének használatához kövesse az alábbi lépéseket:

1. [Hozzon létre egy Azure Active Directory alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) a Azure Portal. Jegyezze fel az alkalmazás nevét és a társított szolgáltatást meghatározó következő értékeket:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. Ha még nem tette meg, hozzon [létre egy Azure Active Directory rendszergazdát](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) a Azure Portal-kiszolgálóhoz. Az Azure AD-rendszergazdának Azure AD-felhasználónak vagy Azure AD-csoportnak kell lennie, de nem lehet egyszerű szolgáltatásnév. Ez a lépés úgy történik, hogy a következő lépésben egy Azure AD-identitással hozzon létre egy tárolt adatbázis-felhasználót az egyszerű szolgáltatásnév számára.

3. [Hozzon létre tárolt adatbázis-felhasználókat](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) az egyszerű szolgáltatásnév számára. Kapcsolódjon ahhoz az adatbázishoz, amelyről vagy rendszerre szeretne másolni egy olyan eszközt, amely a SQL Server Management Studiohoz hasonló eszközöket használ, és egy Azure AD-identitással rendelkezik, amely legalább bármilyen felhasználói engedély megváltoztatásával rendelkezik. Futtassa a következő T-SQL-T:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Adja meg az egyszerű szolgáltatáshoz szükséges engedélyeket az SQL-felhasználók vagy mások számára általában. Futtassa a következő kódot. További lehetőségekért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Azure SQL Database társított szolgáltatás konfigurálása Azure Data Factoryban.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>A szolgáltatás egyszerű hitelesítését használó társított szolgáltatási példa

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adatok előállítója [felügyelt identitással](data-factory-service-identity.md) társítható az Azure-erőforrások számára, amelyek az adott adatelőállítót jelölik. Ezt a felügyelt identitást Azure SQL Database hitelesítéshez használhatja. A kijelölt gyár ezen identitás használatával férhet hozzá az adatbázishoz, és másolhatja azokat az adatbázisból.

A felügyelt identitásos hitelesítés használatához kövesse az alábbi lépéseket.

1. Ha még nem tette meg, hozzon [létre egy Azure Active Directory rendszergazdát](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) a Azure Portal-kiszolgálóhoz. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazda teljes hozzáféréssel rendelkezik az adatbázishoz.

2. [Hozzon létre tárolt adatbázis-felhasználókat](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) a Azure Data Factory felügyelt identitáshoz. Kapcsolódjon ahhoz az adatbázishoz, amelyről vagy rendszerre szeretne másolni egy olyan eszközt, amely a SQL Server Management Studiohoz hasonló eszközöket használ, és egy Azure AD-identitással rendelkezik, amely legalább bármilyen felhasználói engedély megváltoztatásával rendelkezik. Futtassa a következő T-SQL-T:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket, ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot. További lehetőségekért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Azure SQL Database társított szolgáltatás konfigurálása Azure Data Factoryban.

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

Az adatkészletek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services).

Azure SQL Database adatkészlet esetében a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet **Type** tulajdonságát **tulajdonsága azuresqltable**értékre kell állítani. | Igen |
| séma | A séma neve. |Nem, forrás, igen, fogadó  |
| table | A tábla vagy nézet neve. |Nem, forrás, igen, fogadó  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a és a elemet `schema` `table` . | Nem, forrás, igen, fogadó |

### <a name="dataset-properties-example"></a>Adatkészlet tulajdonságai – példa

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

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). Ez a szakasz a Azure SQL Database forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database forrásként

Az adatok Azure SQL Databaseból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának **Type** tulajdonságát **AzureSqlSource**értékre kell állítani. A "SqlSource" típus továbbra is támogatott a visszamenőleges kompatibilitás érdekében. | Igen |
| sqlReaderQuery | Ez a tulajdonság az egyéni SQL-lekérdezést használja az adatolvasáshoz. Például: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | Annak a tárolt eljárásnak a neve, amely beolvassa az adatokat a forrás táblából. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. | Nem |
| storedProcedureParameters | A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név vagy érték párok. A paraméterek neveinek és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. | Nem |
| isolationLevel | Meghatározza az SQL-forrás tranzakció-zárolási viselkedését. Az engedélyezett értékek a következők: **ReadCommitted** (alapértelmezett) **, ReadUncommitted**, **RepeatableRead**, **szerializálható**, **Pillanatkép**. További részletekért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | Nem |

**Megjegyzés:**

- Ha a **AzureSqlSource** **sqlReaderQuery** van megadva, a másolási tevékenység lefuttatja ezt a lekérdezést a Azure SQL Database forráson az adatkéréshez. Megadhat egy tárolt eljárást is a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával, ha a tárolt eljárás paraméterekkel rendelkezik.
- Ha nem ad meg **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az adatkészlet JSON-fájljának "struktúra" szakaszában meghatározott oszlopok a lekérdezések létrehozásához használatosak. A lekérdezés `select column1, column2 from mytable` Azure SQL Databaseon fut. Ha az adatkészlet definíciója nem rendelkezik "Structure" értékkel, az összes oszlop ki van választva a táblából.

#### <a name="sql-query-example"></a>Példa SQL-lekérdezésre

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database fogadóként

> [!TIP]
> További információ a támogatott írási viselkedésekről, konfigurációkról és ajánlott eljárásokról az [adatok Azure SQL Databaseba való betöltéséhez ajánlott](#best-practice-for-loading-data-into-azure-sql-database)eljárások közül.

Az adatAzure SQL Databaseba való másoláshoz a másolási tevékenység fogadója szakasz a **sink** következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának **Type** tulajdonságát **AzureSqlSink**értékre kell állítani. A "SqlSink" típus továbbra is támogatott a visszamenőleges kompatibilitás érdekében. | Igen |
| preCopyScript | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést az Azure SQL Databaseba való írás előtt. A rendszer csak egyszer hívja meg a példányt. Ezzel a tulajdonsággal törölheti az előre feltöltött adatkészleteket. | Nem |
| tableOption | Meghatározza, hogy a rendszer automatikusan létrehozza-e a fogadó táblát, ha az nem létezik a forrásoldali séma alapján. <br>Az automatikus tábla létrehozása nem támogatott, ha a fogadó a tárolt eljárást vagy a szakaszos másolást a másolási tevékenységben konfigurálja. <br>Az engedélyezett értékek a következők: `none` (alapértelmezett), `autoCreate` . | Nem |
| sqlWriterStoredProcedureName | Annak a tárolt eljárásnak a neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába. <br/>Ezt a tárolt eljárást batch-ként kell *meghívni*. Olyan műveletekhez, amelyek csak egyszer futnak, és nem kell megtenniük a forrásadatokat, például törlés vagy csonkítás, használja a `preCopyScript` tulajdonságot.<br>Lásd: példa [egy tárolt eljárás meghívására egy SQL-](#invoke-a-stored-procedure-from-a-sql-sink)fogadóból. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott tábla típusú paraméter neve.  |Nem |
| sqlWriterTableType |A tárolt eljárásban használandó táblanév neve. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a folyamatban lévő adattal másolt adatfájlokat. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név és érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. | Nem |
| writeBatchSize | A *kötegekben*az SQL-táblába beszúrandó sorok száma.<br/> Az engedélyezett érték **egész szám** (sorok száma). Alapértelmezés szerint a Azure Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. | Nem |
| writeBatchTimeout | Az a várakozási idő, ameddig a kötegelt beszúrási művelet befejezi az időtúllépést.<br/> Az engedélyezett érték a **TimeSpan**. Ilyen például a "00:30:00" (30 perc). | Nem |
| disableMetricsCollection | A Data Factory olyan mérőszámokat gyűjt, mint például a Azure SQL Database DTU a másolási teljesítmény optimalizálása és a javaslatok tekintetében. Ha ezt a viselkedést érinti, a `true` kikapcsolásához válassza a következőt:. | Nem (alapértelmezett érték `false` ) |

**1. példa: az adathozzáfűzés**

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

**2. példa: tárolt eljárás meghívása másolás közben**

További információ az SQL-fogadó [tárolt eljárásának meghívásáról](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Ajánlott eljárás az adatAzure SQL Databaseba való betöltéshez

Amikor az Adatmásolás Azure SQL Databaseba történik, különböző írási viselkedésre lehet szükség:

- [Hozzáfűzés](#append-data): a forrásadatok csak új rekordokkal rendelkeznek.
- [Upsert](#upsert-data): a saját forrásadatok lapkákat és frissítéseket is tartalmaz.
- [Felülírás](#overwrite-the-entire-table): minden alkalommal újra szeretném tölteni a teljes dimenzió táblázatát.
- [Írás Egyéni logikával](#write-data-with-custom-logic): további feldolgozásra van szükség a céltábla utolsó beszúrása előtt.

Tekintse át a megfelelő szakaszt a Azure Data Factory és az ajánlott eljárásokban való konfigurálásáról.

### <a name="append-data"></a>Adathozzáfűzés

Az adathozzáfűzés a Azure SQL Database fogadó összekötő alapértelmezett viselkedése. A Azure Data Factory egy tömeges beszúrást végez, hogy hatékonyan írjon a táblába. A forrást és a fogadót ennek megfelelően állíthatja be a másolási tevékenységbe.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adattal szeretne másolni, a másolási tevékenység használatával tömegesen betöltheti az összes rekordot egy előkészítési táblába, majd futtathatja a tárolt eljárási tevékenységet, hogy [egyesítse](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy INSERT/Update utasítást alkalmazzon egy adott felvételen. 

A másolási tevékenység jelenleg nem támogatja natív módon az betöltést egy adatbázis ideiglenes táblájába. A több tevékenység kombinációjának beállításával speciális módon állíthatja be az [Azure SQL Database tömeges Upsert forgatókönyvek optimalizálását](https://github.com/scoriani/azuresqlbulkupsert). Az alábbi példa egy állandó tábla használatát mutatja be átmenetiként.

Azure Data Factory például létrehozhat egy **másolási tevékenységgel** rendelkező **folyamatot egy tárolt eljárási tevékenységgel**. A korábbi adatokat másol a forrás-áruházból egy Azure SQL Database előkészítési táblába, például **UpsertStagingTable**, mint a tábla neve az adatkészletben. Ezután az utóbbi egy tárolt eljárást hív meg, hogy egyesítse a forrás adatait az előkészítési táblából a cél táblába, és törölje az előkészítési táblát.

![Beszúrás és frissítés](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Az adatbázisában Definiáljon egy EGYESÍTÉSi logikával ellátott tárolt eljárást, például az alábbi példát, amely az előző tárolt eljárási tevékenységből mutat. Tegyük fel, hogy a cél a **marketing** tábla három oszloppal: **ProfileID**, **State**és **category**. A upsert a **ProfileID** oszlop alapján végezze el.

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

**2. lehetőség:** Megadhat [egy tárolt eljárást a másolási tevékenységen belül](#invoke-a-stored-procedure-from-a-sql-sink). Ez a módszer a forrás táblában lévő összes köteget (a tulajdonság által szabályozott módon) futtatja `writeBatchSize` ahelyett, hogy a tömeges beszúrást használja a másolási tevékenység alapértelmezett módszere.

**3. lehetőség:** Használhatja a [leképezési](#sink-transformation) adatfolyamot, amely beépített INSERT/upsert/Update metódusokat kínál.

### <a name="overwrite-the-entire-table"></a>A teljes táblázat felülírása

A másolási tevékenység fogadójában a **preCopyScript** tulajdonságot is konfigurálhatja. Ebben az esetben minden futó másolási tevékenységnél először Azure Data Factory futtatja a parancsfájlt. Ezután futtatja a másolatot az adatbeszúráshoz. Ha például felül szeretné írni a teljes táblázatot a legfrissebb adatokkal, adjon meg egy parancsfájlt, hogy először törölje az összes rekordot, mielőtt tömegesen betölti az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Az adatírás egyéni logikával

Az egyéni logikával történő adatírás lépései hasonlóak az [Upsert](#upsert-data) -adatszakaszban leírt lépésekhez. Ha további feldolgozást kell alkalmaznia a forrásadatok végső beszúrása előtt a céltáblaba, betöltheti az előkészítési táblát, majd meghívja a tárolt eljárási tevékenységet, vagy meghívhat egy tárolt eljárást a másolási tevékenység fogadójában az adatalkalmazáshoz, vagy használhatja a leképezési adatfolyamot.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Tárolt eljárás meghívása egy SQL-gyűjtőből

Amikor az adatok Azure SQL Databaseba másolhatók, egy felhasználó által megadott tárolt eljárást is konfigurálhat és meghívhat a forrástábla minden egyes kötegén további paraméterekkel. A tárolt eljárás funkció kihasználja a [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx)előnyeit.

Tárolt eljárás használható, ha a beépített másolási mechanizmusok nem szolgálják ki a célt. Ilyen eset például, ha további feldolgozást szeretne alkalmazni a forrásadatok végső beszúrása előtt a céltáblaba. Néhány további feldolgozási példa: Ha egyesíteni szeretné az oszlopokat, további értékeket kereshet, és több táblába szúrhat be.

Az alábbi minta azt mutatja be, hogyan használható egy tárolt eljárás egy upsert egy táblázatba Azure SQL Database. Tegyük fel, hogy a bemeneti adatok és a fogadó **marketing** tábla mindhárom oszlopot tartalmaz: **ProfileID**, **State**és **category**. A upsert a **ProfileID** oszlop alapján végezze el, és csak a "producta" nevű adott kategóriára alkalmazza.

1. Az adatbázisában adja meg a tábla típusát a **sqlWriterTableType**megegyező névvel. A tábla típusának sémája megegyezik a bemeneti adatok által visszaadott sémával.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Az adatbázisban adja meg a tárolt eljárást ugyanazzal a névvel, mint a **sqlWriterStoredProcedureName**. Kezeli a megadott forrásból származó bemeneti adatokat, és egyesíti a kimeneti táblába. A tárolt eljárásban szereplő tábla típusának neve megegyezik az adatkészletben definiált **Táblanév** .

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

3. A Azure Data Factory a másolási tevékenységben adja meg az **SQL** -fogadó szakaszt a következőképpen:

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

Az adatoknak a leképezési folyamatba való átalakításakor a táblázatok Azure SQL Databaseból való olvasását és írását is elvégezheti. További információ: a forrás- [átalakítás](data-flow-source.md) és a fogadó [transzformáció](data-flow-sink.md) a leképezési adatfolyamatokban.

### <a name="source-transformation"></a>Forrás-átalakítás

A Azure SQL Database vonatkozó beállítások a forrás-átalakítás **forrás beállításai** lapján érhetők el.

**Bemenet:** Válassza ki, hogy a forrást egy táblán, ```Select * from <table-name>``` vagy egy egyéni SQL-lekérdezést adjon meg.

**Lekérdezés**: Ha a beviteli mezőben a lekérdezés lehetőséget választotta, adjon meg egy SQL-lekérdezést a forráshoz. Ez a beállítás felülbírálja az adatkészletben kiválasztott összes táblát. Az **Order by** záradékok itt nem támogatottak, de a teljes select from utasítással is megadható. A felhasználó által definiált Table functions is használható. a **select * from udfGetData ()** egy olyan UDF az SQL-ben, amely egy táblázatot ad vissza. Ez a lekérdezés létrehoz egy forrástábla, amelyet az adatfolyamatában használhat. A lekérdezések használata nagyszerű lehetőséget nyújt a sorok tesztelésre vagy keresésekre való csökkentésére is.

- SQL-példa:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Köteg mérete**: adjon meg egy batch-méretet, amely nagy mennyiségű adatokat olvas be.

**Elkülönítési szint**: a leképezési adatfolyamban található SQL-források alapértelmezett értéke nem véglegesítve. A következő értékek egyikére módosíthatja az elkülönítési szintet:

- Olvasás véglegesítve
- Nem véglegesített olvasás
- Ismételhető olvasás
- Szerializálható
- Nincs (elkülönítési szint figyelmen kívül hagyása)

![Elkülönítési szint](media/data-flow/isolationlevel.png "Elkülönítési szint")

### <a name="sink-transformation"></a>Fogadó transzformáció

A Azure SQL Database vonatkozó beállítások a fogadó átalakítás **Beállítások** lapján érhetők el.

**Frissítési módszer:** Meghatározza, hogy mely műveletek engedélyezettek az adatbázis célhelyén. Az alapértelmezett érték a beszúrások engedélyezése. Sorok frissítéséhez, upsert vagy törléséhez módosítani kell az Alter-Row transzformációt a műveletek sorainak címkézéséhez. A frissítések, upsert és törlések esetében meg kell adni a kulcs oszlopát vagy oszlopait annak meghatározásához, hogy melyik sort kell megváltoztatni.

![Kulcsok oszlopai](media/data-flow/keycolumn.png "Kulcsok oszlopai")

Az ADF a következő frissítés, upsert, törlés részeként használt oszlop nevét fogja használni. Ezért ki kell választania egy oszlopot, amely szerepel a fogadó leképezésében. Ha nem szeretné írni az értéket a kulcs oszlopba, kattintson a "kulcs oszlopainak kihagyása" elemre.

**Tábla művelete:** Meghatározza, hogy a rendszer az összes sort újra létrehozza vagy eltávolítja a célhelyről az írás előtt.

- Nincs: a rendszer nem hajt végre műveletet a táblán.
- Újból létrehozva: a tábla eldobása és újbóli létrehozása megtörténik. Új tábla dinamikus létrehozásakor szükséges.
- Csonkítás: a céltábla összes sora el lesz távolítva.

**Köteg mérete**: azt határozza meg, hogy hány sort kell megírni az egyes gyűjtők. A nagyobb méretű kötegek növelik a tömörítési és a memória-optimalizálást, de a gyorsítótárban tárolt adatmennyiség miatt kifogytak a memória

**SQL-parancsfájlok előtti és utáni**műveletek: Itt adhatja meg azokat a TÖBBsoros SQL-parancsfájlokat, amelyek a (z) előtti (előfeldolgozási) és a (feldolgozás utáni) adatainak a fogadó adatbázisba való beírásakor lesznek végrehajtva

![SQL-feldolgozási parancsfájlok előzetes és utáni feldolgozása](media/data-flow/prepost1.png "SQL-feldolgozási parancsfájlok")

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database adattípusának leképezése

Az adatoknak a vagy a Azure SQL Databaseba való másolásakor a rendszer a következő leképezéseket használja Azure SQL Database adattípusokból Azure Data Factory köztes adattípusokra. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md)

| Azure SQL Database adattípus | Azure Data Factory időközi adattípus |
|:--- |:--- |
| bigint |Int64 |
| binary |Bájt [] |
| bit |Logikai |
| char |Karakterlánc, char [] |
| dátum |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribútum (varbinary (max)) |Bájt [] |
| Float |Dupla |
| image |Bájt [] |
| int |Int32 |
| pénzt |Decimal |
| NCHAR |Karakterlánc, char [] |
| ntext |Karakterlánc, char [] |
| numerikus |Decimal |
| nvarchar |Karakterlánc, char [] |
| valós szám |Egyszeres |
| ROWVERSION |Bájt [] |
| idő adattípusúra |DateTime |
| smallint |Int16 |
| túlcsordulási |Decimal |
| sql_variant |Objektum |
| szöveg |Karakterlánc, char [] |
| time |időtartam |
| időbélyeg |Bájt [] |
| tinyint |Bájt |
| uniqueidentifier |Guid |
| varbinary |Bájt [] |
| varchar |Karakterlánc, char [] |
| xml |Xml |

>[!NOTE]
> A decimális átmeneti típusra leképezett adattípusok esetén a másolási tevékenység jelenleg legfeljebb 28-ig támogatja a pontosságot. Ha 28-nál nagyobb pontosságú adatmennyiséget tartalmaz, érdemes lehet karakterlánccá konvertálni az SQL-lekérdezésben.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>Always Encrypted használata

Ha [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)használatával másol át vagy Azure SQL Database az adatokból, az [általános ODBC-összekötőt](connector-odbc.md) és az SQL Server ODBC-illesztőt használja saját üzemeltetésű Integration Runtimeon keresztül. Ez az Azure SQL Database-összekötő jelenleg nem támogatja Always Encrypted. 

Pontosabban:

1. Hozzon létre egy saját üzemeltetésű Integration Runtime, ha még nem rendelkezik ilyennel. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.

2. Töltse le [innen](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=azuresqldb-current)SQL Server a 64 bites ODBC-illesztőt, és telepítse a Integration Runtime gépre. További információ arról, hogy az illesztőprogram hogyan működik a [Always encrypted és a SQL Server ODBC-illesztő használatával](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current#using-the-azure-key-vault-provider).

3. Hozzon létre az SQL-adatbázishoz való kapcsolódáshoz szükséges ODBC-típusú társított szolgáltatást a következő példákban:

    - SQL- **hitelesítés**használata: az alábbi módon adja meg az ODBC-kapcsolódási karakterláncot, majd válassza az **alapszintű** hitelesítés lehetőséget a Felhasználónév és a jelszó megadásához.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - **Data Factory felügyelt identitás hitelesítésének**használata: 

        1. Kövesse ugyanezeket az [előfeltételeket](#managed-identity) , hogy adatbázis-felhasználót hozzon létre a felügyelt identitáshoz, és adja meg a megfelelő szerepkört az adatbázisban.
        2. A társított szolgáltatás mezőben adja meg az ODBC-kapcsolati karakterláncot az alábbi módon, és válassza a **Névtelen** hitelesítés lehetőséget a kapcsolati sztringként `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Ennek megfelelően hozzon létre adatkészletet és másolási tevékenységet az ODBC-típussal. További tudnivalók az [ODBC-összekötőről](connector-odbc.md) című cikkben olvashatók.

## <a name="next-steps"></a>További lépések

A Azure Data Factoryban a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című részben tekintheti meg.
