---
title: Adatok áthelyezése webes táblából Azure Data Factory használatával
description: További információ az adatok áthelyezéséről egy weblapon lévő táblázatból Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7663848bd06244de7efb169a576e11a5c78204c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392901"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Adatok áthelyezése webtábla-forrásokból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-web-table-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-web-table.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, lásd: a [webes tábla összekötője a v2-ben](../connector-web-table.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy weblapon lévő táblából egy támogatott fogadó adattárba való áthelyezéséhez. Ez a cikk az adattovábbítási [tevékenységekről](data-factory-data-movement-activities.md) szóló cikket ismerteti, amely általános áttekintést nyújt a másolási tevékenységgel végzett adatáthelyezésről és a forrásként/mosogatóként támogatott adattárakról.

A adatfeldolgozó jelenleg csak a webtáblákból származó adatok áthelyezését támogatja más adattárakba, de más adattárakból nem helyez át adatáthelyezést egy webes táblába.

> [!IMPORTANT]
> Ez a webes összekötő jelenleg csak a táblázat tartalmának kinyerését támogatja egy HTML-lapról. HTTP/s-végpont adatainak lekéréséhez használja helyette a [http-összekötőt](data-factory-http-connector.md) .

## <a name="prerequisites"></a>Előfeltételek

A webes tábla összekötő használatához létre kell hoznia egy helyi Integration Runtime (más néven adatkezelés átjárót), és konfigurálnia kell a `gatewayName` tulajdonságot a fogadó társított szolgáltatásban. Ha például a webtáblából az Azure Blob Storage-ba szeretne másolni, konfigurálja az Azure Storage-beli társított szolgáltatást a következő módon:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy helyszíni Cassandra-adattárból származó adatokkal. 

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával. 
- A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. 
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  Az adatok webtáblákból való másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a jelen cikk [JSON-példa: adatok másolása webtáblából az Azure blobba](#json-example-copy-data-from-web-table-to-azure-blob) című szakaszát. 

A következő szakaszokban részletesen ismertetjük a webtáblára jellemző Data Factory entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a webes társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **web** |Yes |
| URL-cím |A webes forrás URL-címe |Yes |
| authenticationType |Névtelen. |Yes |

### <a name="using-anonymous-authentication"></a>Névtelen hitelesítés használata

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **webtable** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |az adatkészlet típusa. a **webtable** értékre kell beállítani |Yes |
| path |A táblázatot tartalmazó erőforrás relatív URL-címe. |Nem. Ha nincs megadva az elérési út, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. |
| index |Az erőforrásban található tábla indexe A HTML-lapokban található táblázat indexének beolvasásához szükséges lépésekért lásd: [index beolvasása egy html-oldalon](#get-index-of-a-table-in-an-html-page) . |Yes |

**Példa**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Jelenleg, ha a másolási tevékenység forrása **webforrásként** van megadva, a további tulajdonságok nem támogatottak.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-példa: adatok másolása webes táblából az Azure Blobba
A következő minta a következőket mutatja be:

1. Egy [webes](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [Webtábla](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [Webforrást](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta óránként másolja át az adatait egy webtáblából egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Az alábbi példa bemutatja, hogyan másolhat adatok egy webtáblából egy Azure-blobba. Az adattovábbítási [tevékenységekről szóló cikkben megadott](data-factory-data-movement-activities.md) mosogatók adatai azonban közvetlenül átmásolhatók a Azure Data Factory másolási tevékenységének használatával.

**Webes társított szolgáltatás** Ez a példa a webes társított szolgáltatást használja névtelen hitelesítéssel. A használható hitelesítés különböző típusairól a [webes társított szolgáltatás](#linked-service-properties) című szakaszban olvashat.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Azure Storage társított szolgáltatás**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Webtábla bemeneti adatkészlete** A **külső** beállítása **igaz** érték esetén a Data Factory szolgáltatás, amely az adatkészletet az adat-előállítón kívülre helyezi, és nem az adat-előállító tevékenysége állítja elő.

> [!NOTE]
> A HTML-lapokban található táblázat indexének beolvasásához szükséges lépésekért lásd: [index beolvasása egy html-oldalon](#get-index-of-a-table-in-an-html-page) .  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure blobkimeneti adatkészlet**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa a **BlobSink** értékre van állítva **, és a** **fogadó típusa a** következő:.

A Webforrások által támogatott tulajdonságok listájáért lásd: Webforrások típusának tulajdonságai.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Táblázat indexének beolvasása egy HTML-oldalon
1. Indítsa el az **Excel 2016 alkalmazást** , és váltson át az **adatlapra.**  
2. Kattintson a **New Query (új lekérdezés** ) elemre az eszköztáron, mutasson a **más forrásokból** lehetőségre, és kattintson **a from web**

    ![Power Query menü](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. A **from web (webes** verzió) párbeszédpanelen adja meg az **URL-címet** , amelyet a társított szolgáltatás JSON-ban kíván használni (például: az https://en.wikipedia.org/wiki/) adatkészlethez megadott elérési ÚTTAL együtt (például: AFI% 27s_100_Years... 100_Movies), majd kattintson **az OK** gombra.

    ![Webes párbeszédpanelen](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Az ebben a példában használt URL-cím: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Ha a **webes tartalom elérése** párbeszédpanel jelenik meg, válassza ki a **megfelelő URL-címet**, a **hitelesítést**, majd kattintson a **Kapcsolódás** elemre.

   ![Hozzáférés a webes tartalomhoz párbeszédpanel](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kattintson a fanézetben **a táblázat elemre** a tartalom megjelenítéséhez, majd kattintson a lap alján található **Szerkesztés** gombra.  

   ![Kezelő párbeszédpanel](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. A **Lekérdezés-szerkesztő** ablakban kattintson **speciális szerkesztő** gombra az eszköztáron.

    ![Speciális szerkesztő gomb](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. A Speciális szerkesztő párbeszédpanelen a "forrás" mező melletti szám az index.

    ![Speciális szerkesztő – index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Ha az Excel 2013-et használja, az index beszerzéséhez használja a [Excelhez készült Microsoft Power Query](https://www.microsoft.com/download/details.aspx?id=39379) . További részletekért lásd [a kapcsolódás weblaphoz](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) című cikket. A lépések hasonlóak, ha a [Microsoft Power bi for Desktopot](https://powerbi.microsoft.com/desktop/)használja.

> [!NOTE]
> Ha az oszlopokat a forrás adatkészletből a fogadó adatkészletből származó oszlopokra kívánja leképezni, tekintse meg [Azure Data Factory az adatkészlet oszlopainak](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
