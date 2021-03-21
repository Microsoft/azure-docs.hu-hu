---
title: Adatátalakítás az U-SQL-parancsfájllal – Azure
description: Megtudhatja, hogyan dolgozhat fel és alakíthat át az adatok a U-SQL-parancsfájlok futtatásával Azure Data Lake Analytics számítási szolgáltatás – 1. verzió.
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 5931cb28721e8658a771ceea1cd94624a0c09f7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392918"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Adatok átalakítása az Azure Data Lake Analyticsben található U-SQL-szkriptek futtatásával 
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-usql-activity.md)
> * [2-es verzió (aktuális verzió)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, lásd: [U-SQL-tevékenység a v2-ben](../transform-data-using-data-lake-analytics.md).

Egy Azure-beli adatfeldolgozó folyamata összekapcsolt számítási szolgáltatások használatával dolgozza fel a társított tárolási szolgáltatásokban tárolt adatokkal. Olyan tevékenységek sorát tartalmazza, amelyekben minden tevékenység egy adott feldolgozási műveletet hajt végre. Ez a cikk azt a **Data Lake Analytics u-SQL-tevékenységet** ismerteti, amely egy **u-SQL-** parancsfájlt futtat egy **Azure Data Lake Analytics** számítási társított szolgáltatáson. 

Hozzon létre egy Azure Data Lake Analytics fiókot, mielőtt létre szeretne hozni egy Data Lake Analytics U-SQL-tevékenységgel rendelkező folyamatot. További információ a Azure Data Lake Analyticsről: a [Azure Data Lake Analytics első lépései](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Az adat-előállító, a társított szolgáltatások, az adatkészletek és a folyamat létrehozásának részletes lépéseiért tekintse át az [első folyamat összeállítása oktatóanyagot](data-factory-build-your-first-pipeline.md) . Data Factory entitások létrehozásához használjon JSON-kódrészleteket Data Factory Editor vagy Visual Studio vagy Azure PowerShell használatával.

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok
Az U-SQL-tevékenység az alábbi hitelesítési típusok használatát támogatja Data Lake Analytics:
* Egyszerű szolgáltatásnév hitelesítése
* Felhasználói hitelesítő adatok (OAuth) hitelesítése 

Javasoljuk, hogy a szolgáltatás egyszerű hitelesítését használja, különösen egy ütemezett U-SQL-végrehajtáshoz. A jogkivonat lejárati viselkedése a felhasználói hitelesítő adatok hitelesítésével történhet. A konfiguráció részleteiért tekintse meg a [társított szolgáltatás tulajdonságai](#azure-data-lake-analytics-linked-service) szakaszt.

## <a name="azure-data-lake-analytics-linked-service"></a>Társított szolgáltatás Azure Data Lake Analytics
Hozzon létre egy **Azure Data Lake Analytics** társított szolgáltatást egy Azure Data Lake Analytics számítási szolgáltatás Azure-beli adatgyárhoz való összekapcsolásához. A folyamat Data Lake Analytics U-SQL tevékenysége erre a társított szolgáltatásra hivatkozik. 

A következő táblázat a JSON-definícióban használt általános tulajdonságok leírásait tartalmazza. Az egyszerű szolgáltatásnév és a felhasználói hitelesítő adatok hitelesítése lehetőség közül választhat.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **típusa** |A Type tulajdonságot a következőre kell beállítani: **AzureDataLakeAnalytics**. |Yes |
| **accountName** |Azure Data Lake Analytics fiók neve. |Yes |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI. |No |
| **subscriptionId** |Azure-előfizetés azonosítója |Nem (ha nincs megadva, a rendszer az adatfeldolgozó előfizetését használja). |
| **resourceGroupName** |Azure-erőforráscsoport neve |Nem (ha nincs megadva, a rendszer az adatfeldolgozó erőforráscsoportot használja). |

### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)
Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazás-entitást Azure Active Directory (Azure AD), és adja meg a Data Lake Store elérését. A részletes lépésekért lásd: [szolgáltatások közötti hitelesítés](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:
* Alkalmazásazonosító
* Alkalmazás kulcsa 
* Bérlőazonosító

Az egyszerű szolgáltatás hitelesítését a következő tulajdonságok megadásával használhatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **servicePrincipalId** | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Yes |
| **servicePrincipalKey** | Az alkalmazás kulcsának meghatározása. | Yes |
| **bérlő** | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Yes |

**Példa: egyszerű szolgáltatásnév hitelesítése**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Felhasználói hitelesítő adatok hitelesítése
Azt is megteheti, hogy a következő tulajdonságok megadásával felhasználói hitelesítő adatokat használ a Data Lake Analyticshoz:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **engedély** | Kattintson az **Engedélyezés** gombra a Data Factory szerkesztőben, és adja meg a hitelesítő adatait, amely hozzárendeli az automatikusan létrehozott engedélyezési URL-címet ehhez a tulajdonsághoz. | Yes |
| **sessionId** | OAuth munkamenet-azonosító a OAuth-engedélyezési munkamenetből. Az egyes munkamenet-AZONOSÍTÓk egyediek, és csak egyszer használhatók fel. Ez a beállítás automatikusan létrejön a Data Factory-szerkesztő használatakor. | Yes |

**Példa: felhasználói hitelesítő adatok hitelesítése**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Jogkivonat lejárata
Az **Engedélyezés** gomb használatával létrehozott engedélyezési kód egy kis idő elteltével lejár. Az alábbi táblázatban megtekintheti a különböző típusú felhasználói fiókok lejárati idejét. A hitelesítési **jogkivonat érvényességének lejárta** után a következő hibaüzenet jelenhet meg: hitelesítőadat-műveleti hiba: INVALID_GRANT-AADSTS70002: hiba történt a hitelesítő adatok érvényesítése során. AADSTS70008: a megadott hozzáférési engedély lejárt vagy visszavont. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelációs azonosítója: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 timestamp: 2015-12-15 21:09:31Z

| Felhasználó típusa | Lejárat után lejár |
|:--- |:--- |
| NEM Azure Active Directory által felügyelt felhasználói fiókok ( @hotmail.com , @live.com stb.) |12 óra |
| A Azure Active Directory által kezelt felhasználói fiókok (HRE) |14 nappal az utolsó szelet futtatása után. <br/><br/>90 nap, ha egy OAuth-alapú társított szolgáltatáson alapuló szelet 14 naponta legalább egyszer fut. |

A hiba elkerüléséhez vagy megoldásához **engedélyezze újra az Engedélyezés gombot,** ha a **jogkivonat lejár** , majd újból üzembe helyezi a társított szolgáltatást. A **munkamenet** -azonosító és az **engedélyezési** tulajdonságok értékét programozott módon is létrehozhatja a következő módon:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

A kódban használt Data Factory osztályok részleteiért tekintse meg a [AzureDataLakeStoreLinkedService Class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), a [AzureDataLakeAnalyticsLinkedService Class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)és a [AuthorizationSessionGetResponse osztály](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) témakört. Adjon hozzá egy hivatkozást a következőhöz: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll a WindowsFormsWebAuthenticationDialog osztályhoz. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő JSON-kódrészlet egy Data Lake Analytics U-SQL-tevékenységgel rendelkező folyamatot határoz meg. A tevékenység definíciója a korábban létrehozott Azure Data Lake Analytics társított szolgáltatásra mutató hivatkozást tartalmaz.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

A következő táblázat ismerteti a tevékenységre jellemző tulajdonságok nevét és leírását. 

| Tulajdonság            | Leírás                              | Kötelező                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| típus                | A Type tulajdonságot **DataLakeAnalyticsU-SQL** értékre kell beállítani. | Yes                                      |
| linkedServiceName   | Hivatkozás a társított szolgáltatásként regisztrált Azure Data Lake Analytics Data Factory | Yes                                      |
| scriptPath          | A U-SQL-parancsfájlt tartalmazó mappa elérési útja. A fájl neve megkülönbözteti a kis-és nagybetűket. | Nem (ha parancsfájlt használ)                   |
| Scriptlinkedservice szolgáltatás | Társított szolgáltatás, amely összekapcsolja a parancsfájlt tartalmazó tárolót az adatgyárba | Nem (ha parancsfájlt használ)                   |
| parancsfájl              | A scriptPath és a Scriptlinkedservice szolgáltatás meghatározása helyett beágyazott parancsfájlt adjon meg. Példa: `"script": "CREATE DATABASE test"`. | Nem (scriptPath és Scriptlinkedservice szolgáltatás használata esetén) |
| Analyticsunits | A feladatok futtatásához egyidejűleg használt csomópontok maximális száma. | No                                       |
| prioritású            | Meghatározza, hogy az összes várólistán lévő feladatra kiválassza az első futtatást. Minél kisebb a szám, annál magasabb a prioritás. | No                                       |
| parameters          | A U-SQL-parancsfájl paramétereinek          | No                                       |
| runtimeVersion      | A használni kívánt U-SQL-motor futtatókörnyezet-verziója | No                                       |
| compilationMode     | <p>U-SQL fordítási módja. A következő értékek egyikének kell lennie:</p> <ul><li>**Szemantika:** Csak szemantikai ellenőrzéseket és a szükséges józan ész-ellenőrzéseket kell végrehajtania.</li><li>**Teljes:** Végezze el a teljes fordítást, beleértve a szintaxis-ellenőrzést, az optimalizálást, a kód generálását stb.</li><li>**SingleBox:** Hajtsa végre a teljes fordítást a TargetType beállítással a SingleBox.</li></ul><p>Ha nem ad meg értéket ehhez a tulajdonsághoz, a kiszolgáló meghatározza az optimális fordítási módot. </p> | No                                       |

Lásd [SearchLogProcessing.txt parancsfájl](#sample-u-sql-script) definícióját. 

## <a name="sample-input-and-output-datasets"></a>Minta bemeneti és kimeneti adatkészletek
### <a name="input-dataset"></a>Bemeneti adatkészlet
Ebben a példában a bemeneti adatok egy Azure Data Lake Store (SearchLog. TSV fájlban találhatók a datalake/input mappában). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Kimeneti adatkészlet
Ebben a példában a U-SQL-szkript által létrehozott kimeneti adatokat egy Azure Data Lake Store (datalake/output mappában) tárolja a rendszer. 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Példa Data Lake Store társított szolgáltatásra
Itt látható a bemeneti/kimeneti adatkészletek által használt példa Azure Data Lake Store társított szolgáltatás definíciója. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

A JSON-tulajdonságok leírását az [adatok áthelyezése a és a Azure Data Lake Storere](data-factory-azure-datalake-connector.md) című cikk tartalmazza. 

## <a name="sample-u-sql-script"></a>Minta U-SQL-parancsfájl

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Az U-SQL-parancsfájlban **\@ lévő** és a **\@ kimenő** paraméterek értékeit az ADF dinamikusan továbbítja a "parameters" szakasz használatával. Tekintse meg a folyamat definíciójának paraméterek szakaszát.

Egyéb tulajdonságokat is megadhat, például a Analyticsunits és a prioritást, valamint a folyamat definícióját a Azure Data Lake Analytics szolgáltatásban futó feladatok esetében.

## <a name="dynamic-parameters"></a>Dinamikus paraméterek
A mintavételi folyamat definíciójában a és a kimenő paraméterek rögzített értékekkel vannak társítva. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Ehelyett dinamikus paramétereket lehet használni. Például: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Ebben az esetben a bemeneti fájlok továbbra is a/datalake/input mappából lesznek kiválasztva, és a kimeneti fájlok a/datalake/output mappában jönnek létre. A fájlnevek dinamikusak a szelet indítási ideje alapján.