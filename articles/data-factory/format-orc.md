---
title: Az ork formátuma Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető az ork formátum a Azure Data Factoryban.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 8973692b90cc9d6caa852616bf2962371d25abfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386458"
---
# <a name="orc-format-in-azure-data-factory"></a>Az ork formátuma Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kövesse ezt a cikket, ha szeretné **elemezni az ork-fájlokat, vagy az adatfájlt az ork formátumba írja**. 

Az ork formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az ork-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát az **ork** értékre kell beállítani. | Yes      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Yes      |
| compressionCodec         | Az ork-fájlokra való íráskor használandó tömörítési kodek. Az ork-fájlokból való olvasáskor az adat-előállítók automatikusan meghatározzák a tömörítési kodeket a fájl metaadatai alapján.<br>A támogatott típusok a következők: **none**, **zlib**, **Snappy** (alapértelmezett) és **LZO**. Megjegyzés: a másolási tevékenység jelenleg nem támogatja a LZO az ork-fájlok olvasásakor/írásakor. | No      |

Alább látható egy példa az Azure Blob Storage-beli ork-adatkészletre:

```json
{
    "name": "OrcDataset",
    "properties": {
        "type": "Orc",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            }
        }
    }
}
```

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok (például a Térkép, a lista, a STRUCT) jelenleg csak adatforgalomban támogatottak, nem a másolási tevékenységekben. Ha összetett típusokat szeretne használni az adatfolyamatokban, ne importálja az adatkészletben a sémát, így a séma üres marad az adatkészletben. Ezután a forrás-átalakításban importálja a leképezést.
* Az oszlopnév nem támogatja az üres helyet.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az ork forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="orc-as-source"></a>ORK forrásként

A másolási tevékenység ***\* forrása \**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **OrcSource** értékre kell állítani. | Yes      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | No       |

### <a name="orc-as-sink"></a>ORK mint fogadó

A másolási ***\* \* tevékenység*** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység fogadójának Type tulajdonságát **OrcSink** értékre kell állítani. | Yes      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, az **ork írási beállítások** táblázatát. |    No      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | No       |

Támogatott **ork írási beállítások** a alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| típus          | A formatSettings típusát **OrcWriteSettings** értékre kell beállítani. | Yes                                                   |
| maxRowsPerFile | Az Adatmappában való íráskor több fájl írására is választhatja, és a fájlok maximális számát is megadhatja.  | No |
| fileNamePrefix | `maxRowsPerFile`A konfigurálásakor alkalmazható.<br> Adja meg a fájlnév előtagját, ha több fájlra ír be adatírást, ami a következő mintának eredményezte: `<fileNamePrefix>_00000.<fileExtension>` . Ha nincs megadva, a rendszer automatikusan létrehozza a fájlnév-előtagot. Ez a tulajdonság nem érvényes, ha a forrás fájl-vagy [partíciós](copy-activity-performance-features.md)tárolásra képes adattár.  | No |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatfolyamatok leképezése során a következő adattárakban olvashatja és írhatja az ork formátumot: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)és [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Az ork-fájlokra az ork-adathalmazt vagy egy [beágyazott adatkészletet](data-flow-source.md#inline-datasets)is mutathat.

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázat felsorolja az ork-forrás által támogatott tulajdonságokat. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

A beágyazott adatkészletek használatakor további beállításokat fog látni, amelyek megegyeznek az [adatkészlet tulajdonságai](#dataset-properties) szakaszban leírt tulajdonságokkal.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni `orc` | igen | `orc` | formátumban |
| Wild kártya elérési útjai | A rendszer feldolgozza a helyettesítő karakteres elérési úttal egyező összes fájlt. Felülbírálja az adatkészletben beállított mappát és a fájl elérési útját. | nem | Karakterlánc [] | wildcardPaths |
| Partíció gyökerének elérési útja | A particionált fájlok esetében megadhatja a partíció gyökerének elérési útját, hogy a particionált mappák oszlopként legyenek olvashatók. | nem | Sztring | partitionRootPath |
| Fájlok listája | Azt jelzi, hogy a forrás egy szövegfájlra mutat-e, amely a feldolgozandó fájlokat listázza | nem | `true` vagy `false` | fileList |
| A fájl nevét tároló oszlop | Új oszlop létrehozása a forrásfájl nevével és elérési útjával | nem | Sztring | rowUrlColumn |
| Befejezés után | A fájlok törlése vagy áthelyezése a feldolgozás után. A fájl elérési útja a tároló gyökeréből indul el | nem | Törlés: `true` vagy `false` <br> Áthelyezése `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Időbélyeg | modifiedAfter <br> modifiedBefore |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Forrás példa

Az ork-forrás konfigurációjának társított adatfolyam-parancsfájlja:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'orc') ~> OrcSource
```

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázat felsorolja az ork-fogadó által támogatott tulajdonságokat. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja.

A beágyazott adatkészletek használatakor további beállításokat fog látni, amelyek megegyeznek az [adatkészlet tulajdonságai](#dataset-properties) szakaszban leírt tulajdonságokkal.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni `orc` | igen | `orc` | formátumban |
| Mappa törlése | Ha a célmappa az írás előtt törlődik | nem | `true` vagy `false` | truncate |
| Fájlnév beállítás | Az írt adatnév formátuma. Alapértelmezés szerint egy fájl/partíció formátumban `part-#####-tid-<guid>` | nem | Minta: karakterlánc <br> /Partíció: karakterlánc [] <br> Oszlopbeli adatként: karakterlánc <br> Kimenet egyetlen fájlba: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Fogadó példa

Az ork fogadó konfigurációjának társított adatfolyam-parancsfájlja:

```
OrcSource sink(
    format: 'orc',
    filePattern:'output[n].orc',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> OrcSink
```

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime használata

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárak közötti másoláshoz, ha nem **az ork-** fájlokat másolja, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** és a **Microsoft Visual C++ 2010 terjeszthető csomagot** az IR-gépen. További részletekért olvassa el a következő bekezdést.

A saját üzemeltetésű IR-ben az ork-fájl szerializálásával/deszerializálásával futó másoláshoz az ADF megkeresi a Java-futtatókörnyezetet úgy, hogy először ellenőrzi a JRE beállításjegyzékét *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* , ha nem található, másodsorban a OpenJDK rendszer-változó ellenőrzése *`JAVA_HOME`* .

- A **JRE használatához**: a 64 bites IR használatához 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja ki a jvm.dllt a OpenJDK összes többi szükséges szerelvényével a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.
- **Visual c++ 2010 Újraterjeszthető csomag telepítése: a** visual c++ 2010 Újraterjeszthető csomag nincs telepítve a saját üzemeltetésű IR-telepítésekkel. [Itt](https://www.microsoft.com/download/details.aspx?id=14632)találhatja meg.

> [!TIP]
> Ha az Adatmásolást az ork formátumba vagy a saját üzemeltetésű Integration Runtime használatával másolja, és a "hiba történt a Java meghívásakor, üzenet: **Java. lang. működése OutOfMemoryError: Java heap Space**" hibaüzenetet, akkor hozzáadhat egy környezeti változót a saját üzemeltetésű integrációs `_JAVA_OPTIONS` modult futtató gépen, amely a JVM minimális/maximális méretének módosítását teszi lehetővé az adott példány számára

![JVM-halom méretének beállítása a saját üzemeltetésű IR-ben](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa be `_JAVA_OPTIONS` a változót értékkel `-Xms256m -Xmx16g` . A jelző `Xms` meghatározza a Java virtuális gép (JVM) kezdeti memória-kiosztási készletét, míg `Xmx` a maximális memória-kiosztási készletet adja meg. Ez azt jelenti, hogy a JVM a memóriával fog elindulni, `Xms` és a memória maximális mennyiségét fogja tudni használni `Xmx` . Alapértelmezés szerint az ADF minimális 64 MB és Max 1G értéket használ.

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
