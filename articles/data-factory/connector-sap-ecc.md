---
title: Adatok másolása az SAP ECC-ból
description: Megtudhatja, hogyan másolhat adatokat az SAP ECC-ból egy Azure Data Factory-folyamat másolási tevékenységének használatával támogatott fogadó adattárakba.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: a3e701f3d433b5b52d8992035ac4ad75b78cb795
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386696"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Adatok másolása az SAP ECC-ból Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory az SAP Enterprise Central Component (ECC) adatainak másolásához. További információ: [másolási tevékenység áttekintése](copy-activity-overview.md).

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) című témakört, amely részletesen ismerteti az egyes SAP-összekötőket, a comparsion és

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP ECC-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az SAP ECC-adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez az SAP ECC-összekötő a következőket támogatja:

- Adatok másolása az SAP ECC-on az SAP NetWeaver 7,0-es és újabb verzióiban.
- Adatok másolása az SAP ECC OData Services által elérhető bármely objektumból, például:

  - SAP-táblák vagy-nézetek.
  - Üzleti alkalmazások programozási felülete [BAPI] objektumok.
  - Adatkivonatok.
  - Az SAP Process Integration (PI) szolgáltatásba küldött adatvagy köztes dokumentumok (IDOCs-EK), amelyek relatív adaptereken keresztül OData fogadhatók.

- Adatok másolása egyszerű hitelesítés használatával.

Az 7,0-es vagy újabb verzió az SAP NetWeaver verziójára hivatkozik, az SAP ECC verziója helyett. Például az SAP ECC 6,0 EHP Hotels 7 általánosan a NetWeaver verziója >= 7,4. Ha nem biztos a környezetében, a következő lépésekkel ellenőrizheti a verziót az SAP-rendszerből:

1. Az SAP-rendszerhez való kapcsolódáshoz használja az SAP GUI-t. 
2. Nyissa **meg a**  ->  **rendszerállapotot**. 
3. Ellenőrizze a SAP_BASIS kiadását, és győződjön meg arról, hogy a mérete egyenlő vagy nagyobb, mint 701.  
      ![SAP_BASIS keresése](./media/connector-sap-table/sap-basis.png)

>[!TIP]
>Az SAP ECC-adatok SAP-táblán vagy nézeten keresztüli másolásához használja az [SAP Table](connector-sap-table.md) Connectort, amely gyorsabb és skálázható.

## <a name="prerequisites"></a>Előfeltételek

Az SAP ECC-összekötő használatához az SAP ECC-entitásokat a OData Services szolgáltatáson keresztül kell kitenni az SAP Gateway használatával. Pontosabban:

- **Állítsa be az SAP Gatewayt**. Az SAP NetWeaver 7,4-nál újabb verzióval rendelkező kiszolgálók esetében az SAP Gateway már telepítve van. A korábbi verziók esetében telepítenie kell a beágyazott SAP-átjárót vagy az SAP Gateway hub-rendszerét, mielőtt az SAP ECC-adatok OData-szolgáltatásokon keresztül elérhetővé tehetők. Az SAP Gateway beállításához tekintse meg a [telepítési útmutatót](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktiválja és konfigurálja az SAP OData szolgáltatást**. A OData szolgáltatást másodpercek alatt aktiválhatja a TCODE SICF használatával. Azt is beállíthatja, hogy mely objektumokat kell elérhetővé tenni. További információ: [lépésenkénti útmutató](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az SAP ECC-összekötőre jellemző Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP ECC társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A tulajdonságot a következőre kell `type` beállítani: `SapEcc` . | Yes |
| `url` | Az SAP ECC OData szolgáltatás URL-címe. | Yes |
| `username` | Az SAP ECC-hoz való kapcsolódáshoz használt Felhasználónév. | No |
| `password` | Az SAP ECC-hoz való kapcsolódáshoz használt egyszerű szöveges jelszó. | No |
| `connectVia` | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nem ad meg futtatókörnyezetet, a rendszer az alapértelmezett Azure Integration Runtime-t használja. | No |

### <a name="example"></a>Példa

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md). A következő szakasz az SAP ECC-adatkészlet által támogatott tulajdonságokat tartalmazza.

Az adatok SAP ECC-ból történő másolásához állítsa az adatkészlet tulajdonságát a következőre: `type` `SapEccResource` .

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `path` | Az SAP ECC OData entitás elérési útja. | Yes |

### <a name="example"></a>Példa

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). A következő szakasz az SAP ECC-forrás által támogatott tulajdonságokat tartalmazza.

### <a name="sap-ecc-as-a-source"></a>SAP ECC forrásként

Az SAP ECC-ból származó adatok másolásához a `type` `source` másolási tevékenység szakaszának tulajdonságát állítsa a következőre: `SapEccSource` .

A másolási tevékenység szakasza a következő tulajdonságokat támogatja `source` :

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A `type` másolási tevékenység `source` szakaszának tulajdonságát be kell állítani `SapEccSource` . | Yes |
| `query` | Az OData lekérdezési beállításai az adatszűréshez. Például:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Az SAP ECC-összekötő az összesített URL-címről másolja az adatait:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>További információ: [OData URL-összetevők](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | No |
| `sapDataColumnDelimiter` | Az a karakter, amelyet a rendszer elválasztóként használ az SAP RFC számára a kimeneti adatokat felosztva. | No |
| `httpRequestTimeout` | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. Ha nincs megadva, az alapértelmezett érték **00:30:00** (30 perc). | No |

### <a name="example"></a>Példa

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Az SAP ECC adattípus-hozzárendelései

Az SAP ECC-ból történő adatmásoláskor a következő leképezések használatosak az SAP ECC-adatok OData adattípusaiból Azure Data Factory ideiglenes adattípusokhoz. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md)

| OData adattípusa | Data Factory időközi adattípus |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Az összetett adattípusok jelenleg nem támogatottak.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések

A Azure Data Factoryban a másolási tevékenység által a forrásként és a nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
