---
title: Adatok másolása az Azure SQL felügyelt példányaiba és onnan
description: Megtudhatja, hogyan helyezheti át adatait az Azure SQL felügyelt példányaiba és onnan az Azure Data Factory használatával.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: b06a01bf280d6d12e2df122d411e15e3432e61c7
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847065"
---
# <a name="copy-data-to-and-from-azure-sql-managed-instance-by-using-azure-data-factory"></a>Adatok másolása az Azure SQL felügyelt példányára és onnan a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához az Azure SQL felügyelt példányain. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

A felügyelt SQL-példány összekötője a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Az SQL által felügyelt példányokból származó adatok bármely támogatott fogadó adattárba másolhatók. Az adatok másolása bármely támogatott forrás adattárból az SQL felügyelt példányára is elvégezhető. A másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez az SQL felügyelt példány-összekötő a következőket támogatja:

- Az adatok másolása SQL-hitelesítéssel és Azure Active Directory (Azure AD) alkalmazás-jogkivonat-hitelesítéssel az Azure-erőforrások egyszerű szolgáltatásával vagy felügyelt identitásával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával beolvashatja az adatokból. A részleteket az SQL mi forrásból származó párhuzamos másolással is választhatja. a részletekért tekintse meg a [párhuzamos másolás az SQL-ből](#parallel-copy-from-sql-mi) című szakaszt.
- Fogadóként automatikusan hozza létre a céltábla, ha nem létezik a forrásoldali séma alapján; az adattábla hozzáfűzése vagy egy tárolt eljárás meghívása egyéni logikával a másolás során.

>[!NOTE]
> Ez az összekötő jelenleg nem támogatja az SQL felügyelt példányának [Always Encryptedét](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) . A megoldáshoz használhat egy [általános ODBC-összekötőt](connector-odbc.md) és egy SQL Server ODBC-illesztőt a saját üzemeltetésű integrációs modulon keresztül. További információ: [Always encrypted szakasz használata](#using-always-encrypted) . 

## <a name="prerequisites"></a>Előfeltételek

Az SQL felügyelt példány [nyilvános végpontjának](../azure-sql/managed-instance/public-endpoint-overview.md)eléréséhez használhat egy Azure Data Factory felügyelt Azure Integration Runtime-t is. Ügyeljen arra, hogy engedélyezze a nyilvános végpontot, és engedélyezze a nyilvános végponti forgalmat a hálózati biztonsági csoporton, hogy Azure Data Factory csatlakozni tudjanak az adatbázishoz. További információkért tekintse meg [ezt az útmutatót](../azure-sql/managed-instance/public-endpoint-configure.md).

Az SQL felügyelt példány privát végpontjának eléréséhez állítson be egy saját üzemeltetésű [integrációs](create-self-hosted-integration-runtime.md) modult, amely hozzáfér az adatbázishoz. Ha a saját üzemeltetésű integrációs modult ugyanabban a virtuális hálózatban hozza létre, mint a felügyelt példányát, győződjön meg arról, hogy az integrációs modul számítógépe a felügyelt példánytól eltérő alhálózaton van. Ha a saját üzemeltetésű integrációs modult a felügyelt példánytól eltérő virtuális hálózaton helyezi üzembe, a virtuális hálózati kapcsolatok vagy virtuális hálózatok használatával virtuális hálózati kapcsolatot is használhat. További információ: [az alkalmazás összekötése az SQL felügyelt példányával](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az SQL felügyelt példány-összekötőhöz tartozó Azure Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SQL felügyelt példány társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot **AzureSqlMI**értékre kell beállítani. | Igen |
| connectionString |Ez a tulajdonság határozza meg az SQL-hitelesítés használatával a felügyelt példányhoz való kapcsolódáshoz szükséges **ConnectionString** -információkat. További információkért tekintse meg az alábbi példákat. <br/>Az alapértelmezett port a 1433-as számú. Ha nyilvános végponttal rendelkező SQL felügyelt példányt használ, explicit módon megadhatja a 3342-as portot.<br> A jelszó Azure Key Vaultban is elhelyezhető. Ha SQL-hitelesítéssel rendelkezik, húzza `password` ki a konfigurációt a kapcsolatok sztringből. További információ: a táblázatot követő JSON-példa és a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md). |Igen |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. A mező megjelölése **SecureString** -ként a biztonságos tárolás Azure Data Factory vagy [Azure Key Vault tárolt titkos kód hivatkozása](store-credentials-in-key-vault.md)alapján. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| Bérlő | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlő AZONOSÍTÓját, amely alatt az alkalmazás található. Lekéri a Azure Portal jobb felső sarkában lévő egér fölé. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| azureCloudType | Az egyszerű szolgáltatás hitelesítéséhez adja meg a HRE-alkalmazás regisztrálásához használt Azure-beli felhőalapú környezet típusát. <br/> Az engedélyezett értékek a következők: **AzurePublic**, **AzureChina**, **AzureUsGovernment**és **AzureGermany**. Alapértelmezés szerint a rendszer az adatfeldolgozó felhőalapú környezetét használja. | Nem |
| Connectvia tulajdonsággal | Ez az [integrációs](concepts-integration-runtime.md) modul az adattárhoz való kapcsolódásra szolgál. Használhatja a saját üzemeltetésű integrációs modult vagy egy Azure Integration Runtime-t, ha a felügyelt példány nyilvános végponttal rendelkezik, és lehetővé teszi Azure Data Factory számára a hozzáférését. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Igen |

Különböző hitelesítési típusok esetén tekintse át az előfeltételek és JSON-minták következő, az előfeltételeket és a JSON-mintákat ismertető szakaszt:

- [SQL-hitelesítés](#sql-authentication)
- [Azure AD-alkalmazás-jogkivonat hitelesítése: szolgáltatásnév](#service-principal-authentication)
- [Azure AD-alkalmazás-jogkivonat hitelesítése: felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

### <a name="sql-authentication"></a>SQL-hitelesítés

**1. példa: SQL-hitelesítés használata**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: SQL-hitelesítés használata jelszóval Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. A [felügyelt példányok Azure Active Directory rendszergazdájának](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)üzembe helyezéséhez kövesse az alábbi lépéseket.

2. [Hozzon létre egy Azure Active Directory alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) a Azure Portal. Jegyezze fel az alkalmazás nevét és a társított szolgáltatást meghatározó következő értékeket:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

3. [Hozzon létre bejelentkezési adatokat](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) a Azure Data Factory felügyelt identitáshoz. SQL Server Management Studio (SSMS) alkalmazásban kapcsolódjon a felügyelt példányhoz egy **rendszergazdaként**szolgáló SQL Server-fiókkal. A **Master** adatbázisban futtassa a következő T-SQL-T:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Hozzon létre tárolt adatbázis-felhasználókat](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) a Azure Data Factory felügyelt identitáshoz. Kapcsolódjon az adatbázishoz a vagy a rendszerből, amelyről másolni kívánja az adatait, futtassa a következő T-SQL-T: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket, ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot. További lehetőségekért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Az SQL felügyelt példány társított szolgáltatásának konfigurálása Azure Data Factoryban.

**Példa: egyszerű szolgáltatásnév hitelesítésének használata**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

Az adatok előállítója [felügyelt identitással](data-factory-service-identity.md) társítható az Azure-erőforrások számára, amelyek az adott adatelőállítót jelölik. Ezt a felügyelt identitást használhatja az SQL felügyelt példányának hitelesítéséhez. A kijelölt gyár ezen identitás használatával férhet hozzá az adatbázishoz, és másolhatja azokat az adatbázisból.

A felügyelt identitásos hitelesítés használatához kövesse az alábbi lépéseket.

1. A [felügyelt példányok Azure Active Directory rendszergazdájának](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)üzembe helyezéséhez kövesse az alábbi lépéseket.

2. [Hozzon létre bejelentkezési adatokat](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) a Azure Data Factory felügyelt identitáshoz. SQL Server Management Studio (SSMS) alkalmazásban kapcsolódjon a felügyelt példányhoz egy **rendszergazdaként**szolgáló SQL Server-fiókkal. A **Master** adatbázisban futtassa a következő T-SQL-T:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Hozzon létre tárolt adatbázis-felhasználókat](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) a Azure Data Factory felügyelt identitáshoz. Kapcsolódjon az adatbázishoz a vagy a rendszerből, amelyről másolni kívánja az adatait, futtassa a következő T-SQL-T: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket, ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot. További lehetőségekért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Az SQL felügyelt példány társított szolgáltatásának konfigurálása Azure Data Factoryban.

**Példa: felügyelt identitásos hitelesítés használata**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához használható csoportok és tulajdonságok teljes listáját az adatkészletek című cikkben találja. Ez a szakasz a felügyelt SQL-példányok adatkészlete által támogatott tulajdonságok listáját tartalmazza.

Az SQL felügyelt példányra irányuló és onnan érkező adatok másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát **AzureSqlMITable**értékre kell állítani. | Igen |
| schema | A séma neve. |Nem, forrás, igen, fogadó  |
| table | A tábla vagy nézet neve. |Nem, forrás, igen, fogadó  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a és a elemet `schema` `table` . | Nem, forrás, igen, fogadó |

**Példa**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

A tevékenységek definiálásához használható csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a felügyelt SQL-példány forrása és fogadója által támogatott tulajdonságok listáját tartalmazza.

### <a name="sql-managed-instance-as-a-source"></a>SQL felügyelt példány forrásaként

>[!TIP]
>Az SQL-ből az adatok particionálásával hatékonyan tölthetők be az adatok az [SQL mi használatával párhuzamos másolásból](#parallel-copy-from-sql-mi).

Az SQL felügyelt példányból származó adatok másolásához a másolási tevékenység forrása szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát **SqlMISource**értékre kell állítani. | Igen |
| sqlReaderQuery |Ez a tulajdonság az egyéni SQL-lekérdezést használja az adatolvasáshoz. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Ez a tulajdonság annak a tárolt eljárásnak a neve, amely beolvassa az adatokat a forrás táblából. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |Nem |
| storedProcedureParameters |Ezek a paraméterek a tárolt eljáráshoz tartoznak.<br/>Az engedélyezett értékek név vagy érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |Nem |
| isolationLevel | Meghatározza az SQL-forrás tranzakció-zárolási viselkedését. Az engedélyezett értékek a következők: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **szerializálható**, **Pillanatkép**. Ha nincs megadva, a rendszer az adatbázis alapértelmezett elkülönítési szintjét használja. További részletekért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | Nem |
| partitionOptions | Megadja az adatok SQL-ből való betöltéséhez használt adatparticionálási beállításokat. <br>Az engedélyezett értékek a következők: **none** (alapértelmezett), **PhysicalPartitionsOfTable** és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None` ), az SQL-ből származó adatok párhuzamos betöltésének foka a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. | Nem |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció lehetőség nem `None` . | Nem |
| ***Alatt `partitionSettings` :*** | | |
| partitionColumnName | Adja meg a forrás oszlop nevét **Integer vagy Date/datetime típusú** értékben, amelyet a rendszer a párhuzamos másolási tartomány particionálásakor fog használni. Ha nincs megadva, a rendszer automatikusan észleli a tábla indexét vagy elsődleges kulcsát, és a partíció oszlopként használja.<br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfDynamicRangePartitionCondition ` a WHERE záradékban lévő hookot. Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-sql-mi) szakaszát. | Nem |
| partitionUpperBound | A particionálási tartomány felosztásának partíciós oszlopának maximális értéke. Ezzel az értékkel lehet eldönteni, hogy a partíció Stride-e, nem pedig a táblázat sorainak szűrésére. A program a tábla vagy a lekérdezés eredményének összes sorát particionálja és másolja. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.  <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-sql-mi) szakaszát. | Nem |
| partitionLowerBound | A particionálási tartomány felosztásához szükséges partíciós oszlop minimális értéke. Ezzel az értékkel lehet eldönteni, hogy a partíció Stride-e, nem pedig a táblázat sorainak szűrésére. A program a tábla vagy a lekérdezés eredményének összes sorát particionálja és másolja. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.<br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-sql-mi) szakaszát. | Nem |

**Vegye figyelembe a következő szempontokat:**

- Ha a **SqlMISource** **sqlReaderQuery** van megadva, a másolási tevékenység futtatja ezt a lekérdezést az SQL felügyelt példányának forrásán az adatgyűjtéshez. Megadhat egy tárolt eljárást is a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával, ha a tárolt eljárás paraméterekkel rendelkezik.
- Ha nem ad meg **sqlReaderQuery** vagy **sqlReaderStoredProcedureName** tulajdonságot, a rendszer a JSON-adatkészlet "struktúra" szakaszában meghatározott oszlopokat használja a lekérdezések létrehozásához. A lekérdezés `select column1, column2 from mytable` a felügyelt SQL-példányon fut. Ha az adatkészlet definíciója nem rendelkezik "Structure" értékkel, az összes oszlop ki van választva a táblából.

**Példa: SQL-lekérdezés használata**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Példa: tárolt eljárás használata**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

**A tárolt eljárás definíciója**

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

### <a name="sql-managed-instance-as-a-sink"></a>SQL felügyelt példány fogadóként

> [!TIP]
> További információ a támogatott írási viselkedésekről, konfigurációkról és ajánlott eljárásokról az [adatok SQL felügyelt példányba való betöltéséhez ajánlott](#best-practice-for-loading-data-into-sql-managed-instance)eljárások közül.

Az SQL felügyelt példányba történő adatmásoláshoz a másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát **SqlMISink**értékre kell állítani. | Igen |
| preCopyScript |Ez a tulajdonság határozza meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az SQL felügyelt példányba írna. A rendszer csak egyszer hívja meg a példányt. Ezt a tulajdonságot használhatja az előre betöltött adatértékek törléséhez. |Nem |
| tableOption | Meghatározza, hogy a [rendszer automatikusan létrehozza](copy-activity-overview.md#auto-create-sink-tables) -e a fogadó táblát, ha az nem létezik a forrásoldali séma alapján. Az automatikus tábla létrehozása nem támogatott, ha a fogadó megadja a tárolt eljárást. Az engedélyezett értékek a következők: `none` (alapértelmezett), `autoCreate` . |Nem |
| sqlWriterStoredProcedureName | Annak a tárolt eljárásnak a neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába. <br/>Ezt a tárolt eljárást batch-ként kell *meghívni*. Olyan műveletekhez, amelyek csak egyszer futnak, és nem kell megtenniük a forrásadatokat, például törlés vagy csonkítás, használja a `preCopyScript` tulajdonságot.<br>Lásd: példa [egy tárolt eljárás meghívására egy SQL-](#invoke-a-stored-procedure-from-a-sql-sink)fogadóból. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott tábla típusú paraméter neve.  |Nem |
| sqlWriterTableType |A tárolt eljárásban használandó táblanév neve. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a folyamatban lévő adattal másolt adatfájlokat. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név és érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. | Nem |
| writeBatchSize |A *kötegekben*az SQL-táblába beszúrandó sorok száma.<br/>Az engedélyezett értékek a sorok számának egésze. Alapértelmezés szerint a Azure Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján.  |Nem |
| writeBatchTimeout |Ez a tulajdonság határozza meg, hogy a kötegelt beszúrási művelet várakozási ideje befejeződjön, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a TimeSpan vonatkoznak. Ilyen például a "00:30:00", amely 30 percet vesz igénybe. |Nem |

**1. példa: az adathozzáfűzés**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**2. példa: tárolt eljárás meghívása másolás közben**

További információ: [tárolt eljárás meghívása egy SQL mi](#invoke-a-stored-procedure-from-a-sql-sink)fogadóból.

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="parallel-copy-from-sql-mi"></a>Párhuzamos másolás SQL-ből MI

A másolási tevékenységben az Azure SQL felügyelt példányának összekötője beépített adatparticionálást biztosít az adatmásoláshoz párhuzamosan. Az adatparticionálási beállítások a másolási tevékenység **forrás** lapján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-sql-server/connector-sql-partition-options.png)

A particionált másolás engedélyezésekor a másolási tevékenység párhuzamos lekérdezéseket futtat az SQL-t futtató forráson az adatpartíciók szerinti betöltéshez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. Ha például négyre van állítva `parallelCopies` , Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés az SQL-ből származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be az SQL-ből. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása esetén ajánlott több fájlként írni egy mappába (csak a mappa nevét adja meg), ebben az esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Eset                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés nagyméretű táblából, fizikai partíciókkal.        | **Partíciós beállítás**: a tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a fizikai partíciókat, és az Adatmásolást partíciók szerint. |
| Teljes terhelés a nagyméretű táblából fizikai partíciók nélkül, egész számmal vagy datetime oszloppal az adatok particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Partíciós oszlop** (nem kötelező): az adatparticionáláshoz használt oszlop megadása. Ha nincs megadva, a rendszer az indexet vagy az elsődleges kulcs oszlopot használja.<br/>A **partíció felső határa** és a * * partíció alsó határa * * (nem kötelező): adja meg, hogy szeretné-e megállapítani a partíció lépéseit. Ez nem a tábla sorainak szűrésére szolgál, a tábla összes sorát particionálja és másolja a rendszer. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értékeket.<br><br>Ha például az "ID" partíciós oszlop értéke 1 és 100 között van, és az alsó határ 20-ra van állítva, a felső határ pedig 80, és a párhuzamos másolás 4 Data Factory, akkor az adatok 4 partíció-azonosítóval vannak lekérdezve a következő tartományban: <= 20, [21, 50], [51, 80] és >= 81. |
| Nagy mennyiségű adat betöltése egyéni lekérdezéssel fizikai partíciók nélkül, egész szám vagy dátum/datetime oszlop használatával az adatok particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál.<br>**Partíció felső határa** és a **partíció alsó határa** (nem kötelező): adja meg, hogy meg szeretné-e állapítani a partíciós lépéseket. Ez nem a tábla sorainak szűrésére szolgál, a lekérdezés eredményének összes sorát particionálja és másolja a rendszer. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.<br><br>A végrehajtás során Data Factory lecseréli `?AdfRangePartitionColumnName` az egyes partíciók tényleges oszlop-és értéktartomány-tartományára, és elküldi az SQL mi-nak. <br>Ha például az "ID" partíciós oszlop értéke 1 és 100 között van, és az alsó határ 20-ra van állítva, a felső határ pedig 80, és a párhuzamos másolás 4 Data Factory, akkor az adatok 4 partíció-azonosítóval vannak lekérdezve a következő tartományban: <= 20, [21, 50], [51, 80] és >= 81. |

Ajánlott eljárások az adattároláshoz a Partition kapcsolóval:

1. Válassza a megkülönböztető oszlop partíciós oszlopként (például az elsődleges kulcs vagy az egyedi kulcs) lehetőséget az adattorzítás elkerüléséhez. 
2. Ha a tábla beépített partícióval rendelkezik, a jobb teljesítmény érdekében használja a "fizikai partíciók tábla" partíciós beállítást.  
3. Ha Azure Integration Runtimet használ az adatmásoláshoz, több számítási erőforrás kihasználása érdekében megadhat nagyobb "[adatintegrációs egységeket (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4). Tekintse át a megfelelő forgatókönyveket.
4. "A[másolási párhuzamosság foka](copy-activity-performance-features.md#parallel-copy)" szabályozza a partíciók számát, így a szám túl nagy ideig nem árt a teljesítménynek, javasoljuk, hogy állítsa be ezt a számot (DIU vagy saját ÜZEMELTETÉSű IR-csomópontok száma) * (2 – 4).

**Példa: teljes terhelés a nagyméretű táblából fizikai partíciókkal**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

```json
"source": {
    "type": "SqlMISource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>Ajánlott eljárás az adatgyűjtés SQL felügyelt példányba való betöltéséhez

Ha SQL felügyelt példányba másol Adatmásolást, különböző írási viselkedésre lehet szükség:

- [Hozzáfűzés](#append-data): a forrásadatok csak új rekordokkal rendelkeznek.
- [Upsert](#upsert-data): a saját forrásadatok lapkákat és frissítéseket is tartalmaz.
- [Felülírás](#overwrite-the-entire-table): minden alkalommal újra szeretném tölteni a teljes dimenzió táblát.
- [Írás Egyéni logikával](#write-data-with-custom-logic): további feldolgozásra van szükség a céltábla utolsó beszúrása előtt. 

Tekintse meg a megfelelő részeit, hogy miként konfigurálható a Azure Data Factory és az ajánlott eljárásokban.

### <a name="append-data"></a>Adathozzáfűzés

Az adathozzáfűzés az SQL felügyelt példány-fogadó összekötő alapértelmezett viselkedése. A Azure Data Factory egy tömeges beszúrást végez, hogy hatékonyan írjon a táblába. A forrást és a fogadót ennek megfelelően állíthatja be a másolási tevékenységbe.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adattal szeretne másolni, a másolási tevékenység használatával tömegesen betöltheti az összes rekordot egy előkészítési táblába, majd futtathatja a tárolt eljárási tevékenységet, hogy [egyesítse](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-mi-current) vagy INSERT/Update utasítást alkalmazzon egy adott felvételen. 

A másolási tevékenység jelenleg nem támogatja natív módon az betöltést egy adatbázis ideiglenes táblájába. A több tevékenység kombinációjának beállításával speciális módon állíthatja be az [SQL Database tömeges Upsert forgatókönyvek optimalizálását](https://github.com/scoriani/azuresqlbulkupsert). Az alábbi példa egy állandó tábla használatát mutatja be átmenetiként.

Azure Data Factory például létrehozhat egy **másolási tevékenységgel** rendelkező **folyamatot egy tárolt eljárási tevékenységgel**. A korábbi adatok másolása a forrás tárolójából egy Azure SQL felügyelt példány-előkészítési táblába, például **UpsertStagingTable**, az adatkészlet táblázatának neveként. Ezután az utóbbi egy tárolt eljárást hív meg, hogy egyesítse a forrás adatait az előkészítési táblából a cél táblába, és törölje az előkészítési táblát.

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

### <a name="overwrite-the-entire-table"></a>A teljes táblázat felülírása

A **preCopyScript** tulajdonságot egy másolási tevékenység fogadójában is konfigurálhatja. Ebben az esetben minden futó másolási tevékenységnél először Azure Data Factory futtatja a parancsfájlt. Ezután futtatja a másolatot az adatbeszúráshoz. Ha például felül szeretné írni a teljes táblázatot a legfrissebb adatokkal, adjon meg egy parancsfájlt, hogy először törölje az összes rekordot, mielőtt tömegesen betölti az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Az adatírás egyéni logikával

Az egyéni logikával történő adatírás lépései hasonlóak az [Upsert](#upsert-data) -adatszakaszban leírt lépésekhez. Ha további feldolgozást kell alkalmaznia a forrásadatok végső beszúrása előtt, akkor betöltheti az előkészítési táblát, majd meghívja a tárolt eljárási tevékenységet, vagy meghívhat egy tárolt eljárást a másolási tevékenység fogadójában az adatalkalmazáshoz.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Tárolt eljárás meghívása egy SQL-gyűjtőből

Az adatok SQL felügyelt példányba való másolása során egy felhasználó által megadott tárolt eljárás is konfigurálható és meghívható további paraméterekkel a forrástábla minden kötegén. A tárolt eljárás funkció kihasználja a [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx)előnyeit.

Tárolt eljárás használható, ha a beépített másolási mechanizmusok nem szolgálják ki a célt. Ilyen eset például, ha további feldolgozást szeretne alkalmazni a forrásadatok végső beszúrása előtt a céltáblaba. Néhány további feldolgozási példa: Ha egyesíteni szeretné az oszlopokat, további értékeket kereshet, és több táblába szúrhat be.

Az alábbi minta azt mutatja be, hogyan használható egy tárolt eljárás egy upsert a SQL Server-adatbázis egyik táblájába. Tegyük fel, hogy a bemeneti adatok és a fogadó **marketing** tábla mindhárom oszlopot tartalmaz: **ProfileID**, **State**és **category**. A upsert a **ProfileID** oszlop alapján végezze el, és csak a "producta" nevű adott kategóriára alkalmazza.

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

3. A Azure Data Factory a másolási tevékenységben adja meg az **SQL mi mosogató** szakaszt a következőképpen:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-sql-managed-instance"></a>SQL felügyelt példány adattípusának leképezése

Amikor az SQL felügyelt példányra másolta az adatok másolását, a rendszer az SQL felügyelt példány adattípusait használja az ideiglenes adattípusok Azure Data Factoryához. Annak megismeréséhez, hogy a másolási tevékenység hogyan legyen leképezve a forrás sémából és az adattípusból a fogadóba, lásd: [séma és adattípus-leképezések](copy-activity-schema-and-type-mapping.md).

| SQL felügyelt példány adattípusa | Azure Data Factory időközi adattípus |
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
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Bájt [] |
| varchar |Karakterlánc, char [] |
| xml |Sztring |

>[!NOTE]
> A decimális átmeneti típusra leképezett adattípusok esetén a másolási tevékenység jelenleg legfeljebb 28-ig támogatja a pontosságot. Ha a 28-nál nagyobb pontosságot igénylő adatmennyiségre van szüksége, érdemes lehet egy SQL-lekérdezésben szereplő sztringre konvertálni.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Always Encrypted használata

Ha az Azure SQL felügyelt példányára vagy [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)-ra másol adatokból, az [általános ODBC-összekötőt](connector-odbc.md) és az SQL Server ODBC-illesztőt a saját üzemeltetésű Integration Runtime segítségével. Az Azure SQL felügyelt példány-összekötője jelenleg nem támogatja a Always Encrypted. 

Pontosabban:

1. Hozzon létre egy saját üzemeltetésű Integration Runtime, ha még nem rendelkezik ilyennel. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.

2. Töltse le [innen](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=azuresqldb-mi-current)SQL Server a 64 bites ODBC-illesztőt, és telepítse a Integration Runtime gépre. További információ arról, hogy az illesztőprogram hogyan működik a [Always encrypted és a SQL Server ODBC-illesztő használatával](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current#using-the-azure-key-vault-provider).

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

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
