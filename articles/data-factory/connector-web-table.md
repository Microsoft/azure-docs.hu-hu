---
title: Adatok másolása webes táblából Azure Data Factory használatával
description: Ismerkedjen meg a Azure Data Factory webtábla-összekötővel, amely lehetővé teszi, hogy egy webtáblából másoljon adatokból a Data Factory által támogatott adattárakba.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 1ab45868afd8d1b7ba1f61f5eaacca283817e6d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367010"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Adatok másolása webes táblából Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-web-table-connector.md)
> * [Aktuális verzió](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok webes táblázatos adatbázisból való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

A webes tábla összekötője, a REST- [összekötő](connector-rest.md) és a [http-összekötő](connector-http.md) közötti különbség a következő:

- A **web Table Connector** kibontja a táblázat tartalmát egy HTML-weboldalról.
- A **Rest-összekötő** kifejezetten támogatja a REST API-k adatainak másolását.
- A **http-összekötő** általános az adatok bármely http-végpontból való lekéréséhez, például a fájl letöltéséhez. 

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a webes tábla összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a webes tábla adatbázisából bármilyen támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez a webtábla-összekötő támogatja a **táblázatok tartalmának kinyerését egy HTML-lapról**.

## <a name="prerequisites"></a>Előfeltételek

A webes tábla összekötő használatához létre kell hoznia egy saját üzemeltetésű Integration Runtime. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a web Table connectorra jellemző Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A webes tábla társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **web** |Yes |
| url | A webes forrás URL-címe |Yes |
| authenticationType | Az engedélyezett érték: **Anonymous**. |Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Yes |

**Példa**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a webes tábla adatkészlete által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a webtáblából, állítsa be az adatkészlet Type (típus) tulajdonságát a webes **táblázat** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **Webtable** | Yes |
| path |A táblázatot tartalmazó erőforrás relatív URL-címe. |Nem. Ha nincs megadva az elérési út, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. |
| index |Az erőforrásban található tábla indexe A HTML-lapokban található táblázat indexének beolvasásához szükséges lépésekért lásd: [index beolvasása egy html-oldalon](#get-index-of-a-table-in-an-html-page) . |Yes |

**Példa**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a webes tábla forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="web-table-as-source"></a>Webes tábla forrásként

Az adatok webtáblából való másolásához a másolási tevékenységben állítsa be a forrás típusát a **Websource** értékre, a további tulajdonságok nem támogatottak.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
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
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Táblázat indexének beolvasása egy HTML-oldalon

Egy olyan tábla indexének lekéréséhez, amelyet az [adatkészlet tulajdonságaiban](#dataset-properties)kell konfigurálni, az alábbi módon használhatja például az Excel 2016 eszközt:

1. Indítsa el az **Excel 2016 alkalmazást** , és váltson át az **adatlapra.**
2. Kattintson a **New Query (új lekérdezés** ) elemre az eszköztáron, mutasson a **más forrásokból** lehetőségre, és kattintson **a from web**

    ![Power Query menü](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. A **from web (webes** verzió) párbeszédpanelen adja meg az **URL-címet** , amelyet a társított szolgáltatás JSON-ban kíván használni (például: az https://en.wikipedia.org/wiki/) adatkészlethez megadott elérési ÚTTAL együtt (például: AFI% 27s_100_Years... 100_Movies), majd kattintson **az OK** gombra.

    ![Webes párbeszédpanelen](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Az ebben a példában használt URL-cím: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Ha a **webes tartalom elérése** párbeszédpanel jelenik meg, válassza ki a **megfelelő URL-címet**, a **hitelesítést**, majd kattintson a **Kapcsolódás** elemre.

   ![Hozzáférés a webes tartalomhoz párbeszédpanel](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kattintson a fanézetben **a táblázat elemre** a tartalom megjelenítéséhez, majd kattintson a lap alján található **Szerkesztés** gombra.  

   ![Kezelő párbeszédpanel](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. A **Lekérdezés-szerkesztő** ablakban kattintson **speciális szerkesztő** gombra az eszköztáron.

    ![Speciális szerkesztő gomb](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. A Speciális szerkesztő párbeszédpanelen a "forrás" mező melletti szám az index.

    ![Speciális szerkesztő – index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Ha az Excel 2013-et használja, az index beszerzéséhez használja a [Excelhez készült Microsoft Power Query](https://www.microsoft.com/download/details.aspx?id=39379) . További részletekért lásd [a kapcsolódás weblaphoz](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) című cikket. A lépések hasonlóak, ha a [Microsoft Power bi for Desktopot](https://powerbi.microsoft.com/desktop/)használja.


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
