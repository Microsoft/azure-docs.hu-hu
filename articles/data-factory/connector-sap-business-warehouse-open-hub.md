---
title: Adatok másolása az SAP Business Warehouse-ból az Open hub használatával
description: Megtudhatja, hogyan másolhat adatok az SAP Business Warehouse-ból (BW) az Open hub használatával a fogadó adattárakhoz a Azure Data Factory folyamat másolási tevékenységével.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 84098901d58e2087c7ece77049e445bb5c76f2a9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358491"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse-ból az Open hub használatával Azure Data Factory

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy SAP Business Warehouse-ból (BW) az Open hub-on keresztül történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetést, comparsion és útmutatást.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP Business Warehouse az Open hub-összekötőn keresztül támogatott a következő tevékenységeknél:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az SAP Business Warehouse-ból bármilyen támogatott fogadó adattárba másolhatja az Adatmásolást. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Az SAP Business Warehouse nyitott hub-összekötője a következőt támogatja:

- Az SAP Business Warehouse **7,01-es vagy újabb verziója (az SAP támogatási csomagjának a 2015-os év után kiadott legújabb verziójában)** .
- Adatok másolása az Open hub Destination helyi táblán keresztül, amely alatt a DSO, a InfoCube, a multibiztosító, a DataSource stb. lehet.
- Adatok másolása egyszerű hitelesítés használatával.
- Kapcsolódás az alkalmazáskiszolgáló eléréséhez.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub Integration 

[SAP BW Open hub szolgáltatás](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) hatékony módszer az adatok SAP BWból való kinyerésére. Az alábbi ábra az SAP-rendszer egyik jellemző folyamatát mutatja be. ebben az esetben az SAP ECC-> PSA-> DSO-> Cube adatforgalmai.

SAP BW nyitott központ célhelye (OHD) határozza meg azt a célt, amelyhez az SAP-adattovábbítási szolgáltatás továbbítva van. Az SAP Adatátvitel Process (DTP) által támogatott összes objektum használható nyílt hub-adatforrásként, például DSO, InfoCube, DataSource stb. Nyissa meg a hub rendeltetési típusát – a továbbított adattárolók tárolására szolgáló táblák (helyi vagy távoli) és egyszerű fájlok. Ez az SAP BW Open hub-összekötő támogatja az adatok másolását a BW-ben lévő OHD helyi táblából. Ha más típusokat használ, a más összekötők használatával közvetlenül kapcsolódhat az adatbázishoz vagy a fájlrendszerhez.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Különbözeti kinyerési folyamat

Az ADF SAP BW nyitott hub-összekötő két opcionális tulajdonságot kínál: `excludeLastRequest` és `baseRequestId`, amely az Open hub változási terhelésének kezelésére használható. 

- **excludeLastRequestId**: azt határozza meg, hogy ki kell-e zárni a legutóbbi kérelem rekordjait. Az alapértelmezett érték TRUE (igaz). 
- **baseRequestId**: a Delta betöltésére vonatkozó kérelem azonosítója. Ha be van állítva, csak a tulajdonság értékénél nagyobb kérelemazonosító rendelkező adatmennyiségeket kéri le a rendszer. 

Az SAP InfoProviders Azure Data Factory (ADF)-ből való kinyerése összességében 2 lépésből áll: 

1. **SAP BW adatátvitel folyamat (DTP)** Ez a lépés átmásolja az adatait egy SAP BW InfoProvider egy SAP BW Open hub-táblába. 

1. **Az ADF adatmásolása** Ebben a lépésben az ADF-összekötő beolvassa az Open hub táblát 

![Különbözeti kinyerési folyamat](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Az első lépésben a DTP végrehajtása történik. Mindegyik végrehajtás egy új SAP-kérelem AZONOSÍTÓját hozza létre. A rendszer az Open hub táblában tárolja a kérelem AZONOSÍTÓját, amelyet az ADF-összekötő használ a különbözet azonosítására. A két lépés aszinkron módon fut: a DTP aktiválva van az SAP-n keresztül, és az ADF-adatmásolat az ADF-en keresztül aktiválódik. 

Alapértelmezés szerint az ADF nem olvassa be a legutóbbi Delta értéket az Open hub táblából (a "legutóbbi kérelem kizárása" beállítás igaz). Alulírott, az ADF-ben tárolt adatok nem 100%-kal naprakészek az Open hub táblában lévő adatokkal (az utolsó Delta hiányzik). A cserében ez az eljárás biztosítja, hogy az aszinkron kinyerés miatt ne vesszenek el a sorok. Akkor is jól működik, ha az ADF az Open hub-táblázatot olvassa, miközben a DTP még mindig ugyanabba a táblába ír. 

A legutóbb másolt kérelmek AZONOSÍTÓját általában egy átmeneti adattárba (például az Azure Blobra a fenti ábrán) tárolja. Ezért a következő futtatáskor a rendszer nem olvassa be az ADF második időpontját. Eközben vegye figyelembe, hogy az adatok nem törlődnek automatikusan az Open hub táblából.

A megfelelő Delta-kezelést illetően nem megengedett, hogy a különböző DTPs származó kérelmek azonosítói ugyanabban az Open hub-táblában legyenek. Ezért nem kell egynél több DTP-t létrehoznia minden egyes nyitott hub-célhoz (OHD). Ha az azonos InfoProvider származó teljes és különbözeti kinyerésre van szüksége, hozzon létre két OHDs ugyanahhoz a InfoProvider. 

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse nyitott hub-összekötő használatához a következőket kell tennie:

- Hozzon létre egy saját üzemeltetésű Integration Runtime a 3,13-es vagy újabb verzióval. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.

- Töltse le az **64 bites [SAP .net-összekötőt 3,0](https://support.sap.com/en/product/connectors/msnet.html)**  az SAP webhelyéről, és telepítse azt a saját üzemeltetésű IR-gépre. A telepítésekor a telepítéshez választható lépések ablakban válassza a **szerelvények telepítése a GAC** -ra lehetőséget, ahogy az alábbi képen is látható. 

    ![Az SAP .NET-összekötő telepítése](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- A Data Factory BW-összekötőben használt SAP-felhasználónak a következő engedélyekkel kell rendelkeznie: 

    - Az RFC és a SAP BW engedélyezése. 
    - A (z) "S_SDSAUTH" engedélyezési objektum "végrehajtás" tevékenységének engedélyei.

- Hozzon létre egy SAP nyitott hub-célhelyet **adatbázis-táblázatként** a "technikai kulcs" beállítással.  Azt is javasoljuk, hogy az adatok törlését ne törölje a táblából, bár ez nem kötelező. Kihasználhatja a DTP-t (közvetlenül végrehajthatja vagy integrálhatja a meglévő feldolgozási láncot) az adatoknak a forrás objektumból (például adatkockából) való kiválasztásához, amelyet a nyitott hub-cél táblához választott.

## <a name="getting-started"></a>Bevezetés

> [!TIP]
>
> Az SAP BW Open hub-összekötő használatával kapcsolatos útmutatóért lásd: [adatok betöltése az SAP Business Warehouse-ból (BW) a Azure Data Factory használatával](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek az SAP Business Warehouse nyitott hub-összekötőhöz kapcsolódó entitások definiálásához használhatók Data Factory.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP Business Warehouse nyitott hub társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapOpenHub** | Igen |
| kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | Igen |
| systemNumber | A SAP BWrendszer rendszerszáma.<br/>Engedélyezett érték: két számjegyből álló decimális szám karakterláncként megadva. | Igen |
| clientId | A-ügyfél ügyfél-azonosítója az SAP W rendszeren.<br/>Engedélyezett érték: a háromjegyű decimális szám karakterláncként van megadva. | Igen |
| language | Az SAP-rendszer által használt nyelv. | Nem (az alapértelmezett érték az **en**)|
| userName | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve. | Igen |
| jelszó | A felhasználó jelszava. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| connectVia | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a SAP BW Open hub-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a és a rendszerből SAP BW nyitott hubhoz, állítsa az adatkészlet Type (típus) tulajdonságát **SapOpenHubTable**értékre. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **SapOpenHubTable**értékre kell beállítani.  | Igen |
| openHubDestinationName | Annak az Open hub-célhelynek a neve, amelyből az adatok másolása megtörténjen. | Igen |

Ha az adatkészletben `excludeLastRequest` és `baseRequestId`t állított be, akkor továbbra is támogatott, miközben az új modellt a tevékenység forrásaként fogja használni.

**Példa**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a SAP BW Open hub-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-bw-open-hub-as-source"></a>A hub megnyitása forrásként SAP BW

SAP BW Open hub adatainak másolásához a következő tulajdonságok támogatottak a másolási tevékenység **forrása** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **SapOpenHubSource**értékre kell állítani. | Igen |
| excludeLastRequest | Azt határozza meg, hogy ki kell-e zárni a legutóbbi kérelem rekordjait. | Nem (az alapértelmezett érték **igaz**) |
| baseRequestId | A különbözeti betöltésre vonatkozó kérelem azonosítója. Ha be van állítva, csak a tulajdonság értékénél **nagyobb** kérelemazonosító rendelkező adatmennyiségeket kéri le a rendszer.  | Nem |

>[!TIP]
>Ha a nyitott hub-tábla csak az egyszeres kérelem-azonosító által generált adatait tartalmazza, akkor a teljes terhelést és a táblázat meglévő adatait felülírja, vagy csak egyszer futtatja a DTP-tesztet, ne feledje, hogy törölje a "excludeLastRequest" lehetőséget a d ATA kimenő.

Az adatok betöltésének felgyorsításához beállíthatja, hogy [`parallelCopies`](copy-activity-performance.md#parallel-copy) a másolási tevékenységben SAP BW Open hub adatainak párhuzamos betöltéséhez. Ha például úgy állítja be a `parallelCopies`t négyre, Data Factory egyidejűleg négy RFC-hívást hajt végre, és mindegyik RFC-hívás az adatok egy részét kéri le az SAP BW Open hub-táblából, particionálva a DTP-kérelem azonosítója és a csomag azonosítója alapján. Ez akkor érvényes, ha az egyedi DTP-kérelmek AZONOSÍTÓjának és a csomag AZONOSÍTÓjának száma nagyobb, mint a `parallelCopies`értéke. Az adatok file-alapú adattárba másolásakor a rendszer úgy is Újrafuttatja, hogy több fájlként is ír egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájlba írás.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Adattípusok leképezése SAP BW nyitott hubhoz

SAP BW Open hub adatainak másolása során a rendszer a következő leképezéseket használja SAP BW adattípusokból Azure Data Factory köztes adattípusokra. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| SAP ABAP-típus | Data factory közbenső adattípus |
|:--- |:--- |
| C (String) | Sztring |
| I (integer) | Int32 |
| F (float) | Dupla |
| D (Date) | Sztring |
| T (Time) | Sztring |
| P (BCD Packed, Currency, Decimal, Qty) | tizedes tört |
| N (Numc) | Sztring |
| X (Binary and Raw) | Sztring |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
