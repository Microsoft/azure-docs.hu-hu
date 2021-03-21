---
title: Az adatátalakítás U-SQL-szkript használatával
description: Megtudhatja, hogyan dolgozhat fel és alakíthat át az adatok a U-SQL-parancsfájlok futtatásával Azure Data Lake Analytics számítási szolgáltatáson.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 94b584f0bea01754a4e955d1418a7a9a2e9aeace
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373912"
---
# <a name="process-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Az adatfeldolgozást U-SQL-parancsfájlok futtatásával Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-usql-activity.md)
> * [Aktuális verzió](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Egy Azure-beli adatfeldolgozó folyamata összekapcsolt számítási szolgáltatások használatával dolgozza fel a társított tárolási szolgáltatásokban tárolt adatokkal. Olyan tevékenységek sorát tartalmazza, amelyekben minden tevékenység egy adott feldolgozási műveletet hajt végre. Ez a cikk azt a **Data Lake Analytics u-SQL-tevékenységet** ismerteti, amely egy **u-SQL-** parancsfájlt futtat egy **Azure Data Lake Analytics** számítási társított szolgáltatáson. 

Hozzon létre egy Azure Data Lake Analytics fiókot, mielőtt létre szeretne hozni egy Data Lake Analytics U-SQL-tevékenységgel rendelkező folyamatot. További információ a Azure Data Lake Analyticsről: a [Azure Data Lake Analytics első lépései](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Társított szolgáltatás Azure Data Lake Analytics
Hozzon létre egy **Azure Data Lake Analytics** társított szolgáltatást egy Azure Data Lake Analytics számítási szolgáltatás Azure-beli adatgyárhoz való összekapcsolásához. A folyamat Data Lake Analytics U-SQL tevékenysége erre a társított szolgáltatásra hivatkozik. 

A következő táblázat a JSON-definícióban használt általános tulajdonságok leírásait tartalmazza. 

| Tulajdonság                 | Leírás                              | Kötelező                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **típusa**                 | A Type tulajdonságot a következőre kell beállítani: **AzureDataLakeAnalytics**. | Yes                                      |
| **accountName**          | Azure Data Lake Analytics fiók neve.  | Yes                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI.           | No                                       |
| **subscriptionId**       | Azure-előfizetés azonosítója                    | No                                       |
| **resourceGroupName**    | Azure-erőforráscsoport neve                | No                                       |

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése
A Azure Data Lake Analytics társított szolgáltatáshoz egyszerű szolgáltatásnév-hitelesítés szükséges a Azure Data Lake Analytics szolgáltatáshoz való kapcsolódáshoz. Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazás entitást Azure Active Directory (Azure AD), és adja meg a Data Lake Analytics és az általa használt Data Lake Store elérését. A részletes lépésekért lásd: [szolgáltatások közötti hitelesítés](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

* Alkalmazásazonosító
* Alkalmazás kulcsa 
* Bérlőazonosító

Egyszerű szolgáltatásnév engedélyezése a Azure Data Lake Anatlyics a [felhasználó hozzáadása varázsló](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user)használatával.

Az egyszerű szolgáltatás hitelesítését a következő tulajdonságok megadásával használhatja:

| Tulajdonság                | Leírás                              | Kötelező |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját.     | Yes      |
| **servicePrincipalKey** | Az alkalmazás kulcsának meghatározása.           | Yes      |
| **bérlő**              | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Yes      |

**Példa: egyszerű szolgáltatásnév hitelesítése**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

További információ a társított szolgáltatásról: [számítási társított szolgáltatások](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő JSON-kódrészlet egy Data Lake Analytics U-SQL-tevékenységgel rendelkező folyamatot határoz meg. A tevékenység definíciója a korábban létrehozott Azure Data Lake Analytics társított szolgáltatásra mutató hivatkozást tartalmaz. Data Lake Analytics U-SQL-parancsfájl végrehajtásához Data Factory elküldi a Data Lake Analytics megadott parancsfájlt, és a szükséges bemenetek és kimenetek a parancsfájlban vannak definiálva a lekéréshez és a kimenethez Data Lake Analytics. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

A következő táblázat ismerteti a tevékenységre jellemző tulajdonságok nevét és leírását. 

| Tulajdonság            | Leírás                              | Kötelező |
| :------------------ | :--------------------------------------- | :------- |
| name                | A folyamatban szereplő tevékenység neve     | Yes      |
| leírás         | A tevékenység működését leíró szöveg  | No       |
| típus                | Data Lake Analytics U-SQL tevékenység esetén a tevékenység típusa  **DataLakeAnalyticsU-SQL**. | Yes      |
| linkedServiceName   | Társított szolgáltatás Azure Data Lake Analytics. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk.  |Yes       |
| scriptPath          | A U-SQL-parancsfájlt tartalmazó mappa elérési útja. A fájl neve megkülönbözteti a kis-és nagybetűket. | Yes      |
| Scriptlinkedservice szolgáltatás | Társított szolgáltatás, amely összekapcsolja a parancsfájlt tartalmazó **Azure Data Lake Store** vagy **Azure Storage-tárolót** . | Yes      |
| Analyticsunits | A feladatok futtatásához egyidejűleg használt csomópontok maximális száma. | No       |
| prioritású            | Meghatározza, hogy az összes várólistán lévő feladatra kiválassza az első futtatást. Minél kisebb a szám, annál magasabb a prioritás. | No       |
| parameters          | A U-SQL-parancsfájlba továbbítandó paraméterek.    | No       |
| runtimeVersion      | A használni kívánt U-SQL-motor futtatókörnyezet-verziója. | No       |
| compilationMode     | <p>U-SQL fordítási módja. A következő értékek egyikének kell lennie: **szemantika:** csak szemantikai ellenőrzéseket és a szükséges józan ész-ellenőrzéseket hajtja végre **:** teljes körű fordítás, beleértve a szintaxis-ellenőrzést, az optimalizálást, a kód generálását stb., a **SingleBox:** a teljes fordítást a TargetType beállítással SingleBox. Ha nem ad meg értéket ehhez a tulajdonsághoz, a kiszolgáló meghatározza az optimális fordítási módot. | No |

Lásd: [SearchLogProcessing.txt](#sample-u-sql-script) a parancsfájl definíciójában. 

## <a name="sample-u-sql-script"></a>Minta U-SQL-parancsfájl

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

A fenti szkriptben például a bemenet és kimenet a parancsfájlban a **\@ be** **\@ -és kimenő paraméterekben** van meghatározva. Az U-SQL-parancsfájlban **\@ lévő** és a **\@ kimenő** paraméterek értékeit a rendszer dinamikusan továbbítja Data Factory a "parameters" szakasz használatával. 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Ebben az esetben a bemeneti fájlok továbbra is a/datalake/input mappából lesznek kiválasztva, és a kimeneti fájlok a/datalake/output mappában jönnek létre. A fájlnevek dinamikusak a folyamat indításakor bekövetkező indítási idő alapján.  

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást: 

* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)