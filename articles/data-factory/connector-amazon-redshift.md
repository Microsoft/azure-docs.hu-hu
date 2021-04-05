---
title: Adatok másolása az Amazon Vöröseltolódásból
description: További információ az Amazon vöröseltolódásról származó adatok másolásáról a Azure Data Factory használatával történő támogatott fogadó adattárakra.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 9441885766dad97dfc237ab81a59710245bf13ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364256"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Adatok másolása az Amazon Vöröseltolódásból a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuális verzió](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Amazon Vöröseltolódásból való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Amazon vöröseltolódás-összekötő a következő tevékenységeknél támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Amazon vöröseltolódásról származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez az Amazon vöröseltolódás-összekötő támogatja a Vöröseltolódásból származó adatok lekérését a lekérdezés vagy a beépített vöröseltolódás támogatásával.

> [!TIP]
> Ha nagy mennyiségű adatnak a Vöröseltolódásból történő másolásakor a legjobb teljesítményt szeretné elérni, érdemes lehet a beépített vöröseltolódást használni az Amazon S3-on keresztül. További részletekért lásd: az [Eltávolítás használata az Amazon vöröseltolódása című szakaszban található adatok másolásához](#use-unload-to-copy-data-from-amazon-redshift) .

## <a name="prerequisites"></a>Előfeltételek

* Ha saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md)használatával másol egy helyszíni adattárba, adja meg Integration Runtime (a gép IP-címe) hozzáférést az Amazon vöröseltolódási fürthöz. További útmutatásért lásd: [hozzáférés engedélyezése a fürthöz](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) .
* Ha Adatmásolást végez egy Azure-adattárba, tekintse meg az Azure-adatközpontok által használt számítási IP-cím és SQL [-tartományok Azure-adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653) című témakört.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Amazon vöröseltolódás-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Amazon vöröseltolódás társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **AmazonRedshift** | Yes |
| kiszolgáló |Az Amazon Vöröseltolódási kiszolgáló IP-címe vagy állomásneve. |Yes |
| port |Az Amazon vöröseltolódás-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port száma. |Nem, az alapértelmezett érték 5439 |
| adatbázis |Az Amazon vöröseltolódás-adatbázis neve. |Yes |
| username |Az adatbázishoz hozzáférő felhasználó neve. |Yes |
| jelszó |A felhasználói fiók jelszava. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

**Példa**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az Amazon Vöröseltolódási adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Amazon Vöröseltolódásból való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AmazonRedshiftTable** | Yes |
| schema | A séma neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. `schema`A és `table` az új számítási feladatok használata. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha `RelationalTable` a beírt adatkészletet használta, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Amazon vöröseltolódás forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="amazon-redshift-as-source"></a>Amazon vöröseltolódás forrásként

Az adatok Amazon Vöröseltolódásból való másolásához állítsa a forrás típusát a másolási tevékenység **AmazonRedshiftSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **AmazonRedshiftSource** | Yes |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. Például: select * from Sajáttábla. |Nem (ha meg van adva a "táblanév" az adatkészletben) |
| redshiftUnloadSettings | Tulajdonság az Amazon vöröseltolódás eltávolításakor. | No |
| s3LinkedServiceName | Egy "AmazonS3" típusú társított szolgáltatás nevének megadásával egy olyan Amazon S3-ra hivatkozik, amelyet a rendszer ideiglenes tárolóként használ. | Igen, ha kitöltést használ |
| bucketName | Adja meg az S3 gyűjtőt az ideiglenes adatai tárolásához. Ha nincs megadva, Data Factory a szolgáltatás automatikusan létrehozza azt.  | Igen, ha kitöltést használ |

**Példa: az Amazon Vöröseltolódásának forrása a másolási tevékenységben a kitöltés használatával**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

További információ a kitöltés használatáról: az adatok Amazon Vöröseltolódásból való hatékony másolása a következő szakaszból.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Az Eltávolítás használata az adatok az Amazon Vöröseltolódásból való másolásához

Az [Eltávolítás](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) az Amazon vöröseltolódás által biztosított mechanizmus, amely képes a lekérdezés eredményeinek egy vagy több fájlba való kitöltésére az Amazon Simple Storage szolgáltatásban (Amazon S3). Az Amazon a nagyméretű adathalmazok Vöröseltolódásból való másolásának módját javasolja.

**Példa: adatok másolása az Amazon Vöröseltolódásáról az Azure szinapszis Analytics szolgáltatásba a kitöltés, a szakaszos másolás és a Kiindulás használatával**

Ebben a példában a másolási tevékenység kitölti az Amazon Vöröseltolódásból az Amazon S3-be a "redshiftUnloadSettings" beállításban konfigurált, majd az adatok az Amazon S3-ból az Azure Blobba való másolását az "stagingSettings" paraméterben megadott módon, végül a következőt használja: a Base használata az adatok Azure szinapszis Analyticsbe való betöltéséhez. Az összes ideiglenes formátumot a másolási tevékenység megfelelően kezeli.

![Az Azure szinapszis Analytics-másolási munkafolyamat vöröseltolódása](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Az Amazon vöröseltolódás adattípusának leképezése

Az Amazon Vöröseltolódásból történő adatmásoláskor a következő leképezések használatosak az Amazon vöröseltolódás adattípusaiból Azure Data Factory átmeneti adattípusokra. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| Amazon vöröseltolódás adattípusa | Az adatgyár átmeneti adattípusa |
|:--- |:--- |
| BIGINT |Int64 |
| LOGIKAI |Sztring |
| CHAR |Sztring |
| DATE |DateTime |
| DECIMÁLIS |Tizedesjegy |
| DUPLA PONTOSSÁG |Dupla |
| EGÉSZ SZÁM |Int32 |
| VALÓS SZÁM |Egyirányú |
| SMALLINT |Int16 |
| TEXT |Sztring |
| IDŐBÉLYEGZŐ |DateTime |
| VARCHAR |Sztring |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
