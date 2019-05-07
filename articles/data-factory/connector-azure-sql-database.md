---
title: Adatok másolása az vagy egy Azure SQL Database használatával a Data Factory |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure SQL Database támogatott forrás adattárakból származó, vagy az SQL-adatbázisoktól a támogatott fogadó adattárakba Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 231f44612b5e87afdf84f31d86c80be644fb4484
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154324"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure SQL Database-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [1-es verzió](v1/data-factory-azure-sql-connector.md)
> * [Aktuális verzió](connector-azure-sql-database.md)

Ez a cikk ismerteti, hogyan másolhat adatokat, és az Azure SQL Database-ből. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure SQL Database-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) a [támogatott forrás/fogadó mátrix](copy-activity-overview.md) tábla
- [Az adatfolyam-leképezés](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban az Azure SQL Database-összekötő támogatja ezeket a funkciókat:

- Adatok másolása az Azure-erőforrások egy egyszerű vagy felügyelt szolgáltatásidentitások SQL-hitelesítés és Azure Active Directory (Azure AD) alkalmazástoken-hitelesítésének használata által.
- Forrásként adatok lekérdezése egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként adatok hozzáfűzése a táblát, vagy a másolás során az egyéni logikát tárolt eljárás meghívása.

Az Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) jelenleg nem támogatja. 

> [!IMPORTANT]
> Az Azure Data Factory integrációs modul adatokat másol, konfiguráljon egy [Azure SQL server tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) úgy, hogy az Azure-szolgáltatásokhoz férhet hozzá a kiszolgálón.
> Ha adatokat másolja egy saját üzemeltetésű integrációs modul használatával, konfigurálja az Azure SQL-kiszolgáló tűzfalán, hogy a megfelelő IP-címtartomány. A tartományba beletartozik az Azure SQL adatbázishoz való csatlakozáshoz használja a gép IP-Címmel.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások egy Azure SQL Database-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Ezek a Tulajdonságok támogatottak egy Azure SQL Database-beli társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **AzureSqlDatabase**. | Igen |
| connectionString | Adja meg a az Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatokat a **connectionString** tulajdonság. <br/>Ez a mező jelölhetnek egy SecureString tárolja biztonságos helyen a Data Factoryban. Jelszó szolgáltatásnév kulcsát/is helyezheti az Azure Key Vaultban, és ha az SQL-hitelesítés lekéréses a `password` konfigurációs ki a kapcsolati karakterláncot. A táblázat alatti a JSON-példa és [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md) további részleteket a cikkben. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Azure integrációs modul és a egy saját üzemeltetésű integrációs modult is használhatja, ha az adattár egy magánhálózaton található. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem |

Különböző hitelesítési típus tekintse meg a következő szakaszok az Előfeltételek és a JSON-minták, illetve:

- [SQL-hitelesítés](#sql-authentication)
- [Az Azure AD alkalmazástoken-hitelesítésének: Egyszerű szolgáltatás](#service-principal-authentication)
- [Az Azure AD alkalmazástoken-hitelesítésének: Felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha nyomja le az "UserErrorFailedToConnectToSqlServer", hibakód: Hiba történt, és üzenet például a "a munkamenet korlátot, az adatbázis XXX elérte.", és adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatás példa

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Az Azure Key Vaultban jelszava:** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
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

A szolgáltatás egyszerűszolgáltatás-alapú Azure AD alkalmazástoken-hitelesítésének használatához kövesse az alábbi lépéseket:

1. **[Az Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  az Azure Portalról. Jegyezze fel az alkalmazás nevét és a következő értékeket, amelyek meghatározzák a társított szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. **[Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL Serverhez, az Azure Portalon, ha ezt még nem tette meg. Azure AD-rendszergazdaként kell lennie az Azure AD-felhasználó vagy az Azure AD-csoport, de nem lehet egy egyszerű szolgáltatást. Ebben a lépésben végezhető el, hogy a következő lépéssel, használhatja az Azure AD identity egy tartalmazottadatbázis-felhasználó, a szolgáltatás egyszerű létrehozásához.

3. **[Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  egyszerű szolgáltatás számára. Az adatbázis vagy amelyek adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg a szükséges engedélyekkel a szolgáltatásnév** szokásos módon SQL-felhasználók vagy mások számára. Futtassa a következő kódot, vagy a további beállítások [Itt](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Egy Azure SQL Database társított szolgáltatás konfigurálása** az Azure Data Factoryban.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Társított szolgáltatás példa, amely használja az egyszerű szolgáltatásnév hitelesítése

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

### <a name="managed-identity"></a> Felügyelt identitások Azure-erőforrások hitelesítéshez

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md) , amely az adott adat-előállító jelöli. A felügyelt identitást is használhatja az Azure SQL Database-hitelesítéshez. A kijelölt gyári férhet hozzá, és másolja ezt az identitást az adatok vagy az adatbázishoz.

Felügyelt identitás-hitelesítést használ, kövesse az alábbi lépéseket:

1. **[Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL Serverhez, az Azure Portalon, ha ezt még nem tette meg. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoporthoz. Ha a csoport felügyelt identitás rendszergazda szerepkörrel rendelkező biztosít, hagyja ki a 3. és 4. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

2. **[Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  a Data Factory által felügyelt identitás. Az adatbázis vagy amelyek adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Adja meg a szükséges engedélyekkel a Data Factory a felügyelt identitást** szokásos módon SQL-felhasználók és mások. Futtassa a következő kódot, vagy a további beállítások [Itt](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. **Egy Azure SQL Database társított szolgáltatás konfigurálása** az Azure Data Factoryban.

**Példa**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) cikk. Ez a szakasz az Azure SQL Database-adatkészletet által támogatott tulajdonságok listáját tartalmazza.

Másolja az adatokat, vagy az Azure SQL Database, az a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **AzureSqlTable**. | Igen |
| tableName | A tábla vagy nézet az Azure SQL Database-példány, amelyre a társított szolgáltatás hivatkozik a neve. | Nincs forrás, a fogadó Igen |

#### <a name="dataset-properties-example"></a>Adatkészlet tulajdonságai példa

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a forrás Azure SQL Database és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-as-the-source"></a>Az Azure SQL Database-t a forrás

Adatok másolása az Azure SQL Database, állítsa be a **típus** tulajdonság, a másolási tevékenység forrás **SqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **SqlSource**. | Igen |
| sqlReaderQuery | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Példa: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. | Nem |
| storedProcedureParameters | A tárolt eljárás paraméterei.<br/>Megengedett értékek: neve vagy értéke párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |

### <a name="points-to-note"></a>Tudnivalók

- Ha a **sqlReaderQuery** van megadva a **SqlSource**, a másolási tevékenység a lekérdezés fut az Azure SQL Database forrás, az adatok beolvasásához. Vagy megadhat egy tárolt eljárást. Adja meg **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha paramétereket fogadja tárolt eljárást.
- Ha nem adja meg vagy **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az oszlopok definiálva a **struktúra** az adatkészlet JSON szakaszában is használható a lekérdezés hozhatnak létre. `select column1, column2 from mytable` futtatja az Azure SQL-adatbázison. Ha az adatkészlet definíciója nem rendelkezik a **struktúra**, az összes oszlop ki van jelölve, a táblából.

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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Tárolt eljárás példa

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
                "type": "SqlSource",
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

### <a name="azure-sql-database-as-the-sink"></a>Az Azure SQL Database-t a fogadó

Adatok másolása az Azure SQL Database, állítsa be a **típus** tulajdonságot a másolási tevékenység fogadó való **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát **SqlSink**. | Igen |
| writeBatchSize | Az SQL-táblába beilleszti sorok száma **kötegenként**.<br/> Az engedélyezett érték **egész** (sorok száma). Alapértelmezés szerint a Data Factory a megfelelő kötegméret sor mérete alapján dinamikus meghatározásához. | Nem |
| writeBatchTimeout | A várakozási idő a köteg beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/> Az engedélyezett érték **timespan**. Példa: "00: 30:00" (30 perc). | Nem |
| preCopyScript | Adjon meg egy SQL-lekérdezést a másolási tevékenység futtatása előtt írja az adatokat az Azure SQL Database-be. Csak indítva egyszer futtatni példányonkénti. Ez a tulajdonság használatával az előre betöltött adatokat. | Nem |
| sqlWriterStoredProcedureName | A tárolt eljárást, amely meghatározza, hogyan alkalmazhatja a forrásadatok egy cél táblába neve. Például akkor upserts, vagy átalakíthatja a saját üzleti logikája használatával. <br/><br/>A tárolt eljárás **kötegenként meghívása**. Csak egyszer futnak le, és nincs köze forrásadatokkal rendelkező műveletek, használja a `preCopyScript` tulajdonság. Példa műveleti delete és csonkolja. | Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Megengedett értékek: név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |
| sqlWriterTableType | Adjon meg egy tábla típusú név a tárolt eljárás használható. A másolási tevékenység áthelyezett elérhetővé teszi a tábla típusú egy ideiglenes táblába. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. | Nem |

> [!TIP]
> Adatmásolás az Azure SQL Database, amikor a másolási tevékenység adatokat fűz hozzá a fogadó tábla alapértelmezés szerint. Ehhez az upsert vagy további üzleti logikát, használja a tárolt eljárás **SqlSink**. További részletek a [fogadó SQL tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Példa adatok hozzáfűzése

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
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Tárolt eljárás meghívása során upsert például másolása

További részletek a [fogadó SQL tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink).

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
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>A céladatbázis azonosító oszlop

Ez a szakasz bemutatja, hogyan másolhat adatokat egy forrás táblából egy identitásoszlop nélkül az identity oszlopot tartalmazó táblát.

#### <a name="source-table"></a>Forrástábla

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Céltábla

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> A céltábla tartalmaz azonosító oszlopot.

#### <a name="source-dataset-json-definition"></a>Forrás adatkészlet JSON-definíció

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

#### <a name="destination-dataset-json-definition"></a>Cél adatkészlet JSON-definíció

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

> [!NOTE]
> A forrás- és table eltérő sémával rendelkezik. 

A cél egy további oszlopot egy identitással rendelkezik. Ebben az esetben meg kell adnia a **struktúra** tulajdonság a célként megadott adatkészlet definíciójában, amely nem tartalmazza az identitásoszlop.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Az SQL-fogadó tárolt eljárás meghívása

Azure SQL Database-be másol adatokat, amikor is konfigurálásához és meghívása felhasználó által megadott tárolt eljárást a további paraméterek.

Tárolt eljárás segítségével használhatja, ha a beépített másolási mechanizmusokkal nem szolgálnak ki erre a célra. Általában használhatók, amikor egy upsert, insert és update, vagy további feldolgozás előtt a végső beszúrási a forrásadatok a céltáblázatba történő kell elvégezni. Extra feldolgozási példákat egyesítési oszlop, keresse meg a további értéket, és annak több tábla.

A következő minta bemutatja, hogyan egy tárolt eljárást az upsert ehhez az Azure SQL Database egyik táblájába. Tegyük fel, amelyek bemeneti és a fogadó **Marketing** tábla minden egyes háromoszloposak: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre az upsert alapján a **ProfileID** oszlopot, és csak egy adott kategória alkalmazhatja azt.

**Kimeneti adatkészlet:** a "tableName" kell lennie a ugyanazon tábla típusú paraméter neve a tárolt eljárás (lásd az alábbi tárolt eljárás szkriptet).

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
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Adja meg a **SQL-fogadó** a másolási tevékenység a következő szakaszban.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Az adatbázis határoz meg a tárolt eljárás, amelynek a neve megegyezik a **SqlWriterStoredProcedureName**. Kezeli a bemeneti adatokat az adott forrásból származnak, és azt a kimeneti tábla egyesít. A tárolt eljárást a táblázat típusú paraméter neve legyen ugyanaz, mint a **tableName** adatkészletben történő definiálása okozza.

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

Az adatbázis határoz meg a táblatípus, amelynek a neve megegyezik a **sqlWriterTableType**. A tábla típusú sémát a sémát, a bemeneti adatok által visszaadott megegyezőnek kell lennie.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

A tárolt eljárás szolgáltatás kihasználja [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok hozzárendelése

A részletek [átalakítási forrás](data-flow-source.md) és [átalakítási fogadó](data-flow-sink.md) az adatfolyam-leképezés.

## <a name="data-type-mapping-for-azure-sql-database"></a>Az Azure SQL Database-adattípus-leképezés

Másolt adatok vagy az Azure SQL Database, a következő hozzárendeléseket használják az Azure SQL Database adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megtudhatja, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Az Azure SQL Database-adattípus | Data Factory közbenső adattípus |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| A FILESTREAM attribútum (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numerikus |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| pénz |Decimal |
| sql_variant |Object |
| szöveg |String, Char[] |
| time |TimeSpan |
| időbélyeg |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| Varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> A típusok képezi le közbenső tizedes tört szám típus jelenleg ADF támogatja a pontosság 28 legfeljebb. Ha adatok 28-nál nagyobb pontossággal rendelkezik, érdemes lehet alakítandó karakterláncot az SQL-lekérdezésben.

## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).
