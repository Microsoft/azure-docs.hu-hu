---
title: Másolási tevékenység Azure Data Factory
description: Ismerkedjen meg a másolási tevékenységgel Azure Data Factoryban. Használhatja az adatok egy támogatott forrás adattárból egy támogatott fogadó adattárba való másolásához.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: d52a0bba5fddaa865b8fad74b778ba7a3838b2a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387903"
---
# <a name="copy-activity-in-azure-data-factory"></a>Másolási tevékenység Azure Data Factory

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-data-movement-activities.md)
> * [Aktuális verzió](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory a másolási tevékenységgel a helyszíni és a felhőben található adattárak között másolhatók az Adatmásolás adatai. Az Adatmásolás után más tevékenységeket is használhat a további átalakításához és elemzéséhez. A másolási tevékenységgel az üzleti intelligencia (BI) és az alkalmazások felhasználásának átalakítási és elemzési eredményeit is közzéteheti.

![A másolási tevékenység szerepe](media/copy-activity-overview/copy-activity.png)

A másolási tevékenység végrehajtása egy [integrációs](concepts-integration-runtime.md)modulon történik. Különböző típusú integrációs modulokat használhat különféle adatmásolási forgatókönyvekhez:

* Ha az adatok másolása két olyan adattár között történik, amely nyilvánosan elérhető az interneten keresztül bármely IP-címről, használhatja az Azure Integration Runtime alkalmazást a másolási tevékenységhez. Ez az integrációs modul biztonságos, megbízható, méretezhető és [globálisan elérhető](concepts-integration-runtime.md#integration-runtime-location).
* Ha a helyszíni környezetben vagy a hozzáférés-vezérléssel (például egy Azure virtuális hálózattal) rendelkező hálózaton található adattárakba másolja az adatok másolását, akkor egy saját üzemeltetésű integrációs modult kell beállítania.

Integrációs modult kell társítani az egyes forrásokhoz és fogadó adattárakhoz. További információ arról, hogy a másolási tevékenység melyik integrációs futtatókörnyezetet határozza meg. a [használandó IR meghatározása](concepts-integration-runtime.md#determining-which-ir-to-use)című témakörben talál további információt.

Az adatok forrásról fogadóba való másolásához a másolási tevékenységet futtató szolgáltatás a következő lépéseket hajtja végre:

1. Adatokat olvas be egy forrás adattárból.
2. Szerializálási/deszerializálási, tömörítési/kibontási, oszlop-hozzárendelési és egyéb műveleteket hajt végre. Ezeket a műveleteket a bemeneti adatkészlet, a kimeneti adatkészlet és a másolási tevékenység konfigurációja alapján hajtja végre.
3. Az adatot a fogadó/cél adattárba írja.

![Másolási tevékenység áttekintése](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Támogatott adattárak és-formátumok

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Támogatott fájlformátumok

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A másolási tevékenységgel fájlok másolását végezheti el két fájl alapú adattár között, ebben az esetben az Adatmásolást a szerializálás vagy a deszerializálás nélkül is hatékonyan másolja a rendszer. Emellett egy adott formátumból is elemezheti vagy létrehozhatja a fájlokat, például az alábbiakat is elvégezheti:

* Adatok másolása SQL Server-adatbázisból és a Azure Data Lake Storage Gen2ba való írás parketta formátumban.
* Szövegfájlok (CSV) formátumának másolása helyszíni fájlrendszerből és az Azure Blob Storage-ba való írás Avro formátumban.
* Másolhatja a tömörített fájlokat egy helyszíni fájlrendszerből, kibonthatja azokat menet közben, és kibontott fájlokat írhat a Azure Data Lake Storage Gen2ba.
* Adatok másolása gzip tömörített szövegfájlba (CSV) az Azure Blob Storage-ból, és a Azure SQL Databasebe írás.
* Számos további tevékenység, amelyek szerializálást/deszerializálást vagy tömörítést/kibontást igényelnek.

## <a name="supported-regions"></a>Támogatott régiók

A másolási tevékenységet engedélyező szolgáltatás globálisan elérhető az [Azure Integration Runtime helyein](concepts-integration-runtime.md#integration-runtime-location)felsorolt régiókban és földrajzi területeken. A globálisan elérhető topológia biztosítja a hatékony adatáthelyezést, amely általában elkerüli a régiók közötti ugrásokat. A [termékek régiónként](https://azure.microsoft.com/regions/#services) való megtekintésével ellenőrizze, hogy rendelkezésre áll-e a Data Factory és az adatáthelyezés egy adott régióban.

## <a name="configuration"></a>Konfiguráció

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Általánosságban elmondható, hogy a másolási tevékenységet a Azure Data Factoryban kell használnia:

1. **Társított szolgáltatások létrehozása a forrás adattárhoz és a fogadó adattárhoz.** A támogatott összekötők listáját a jelen cikk [támogatott adattárak és formátumok](#supported-data-stores-and-formats) című szakaszában találja. A konfigurációs információk és a támogatott tulajdonságok esetében tekintse meg az összekötő cikk "társított szolgáltatás tulajdonságai" szakaszát. 
2. **Hozzon létre adatkészleteket a forráshoz és a fogadóhoz.** A konfigurációs információkat és a támogatott tulajdonságokat a forrás és a fogadó összekötő cikkek "adatkészlet tulajdonságai" szakaszában találja.
3. **Hozzon létre egy folyamatot a másolási tevékenységgel.** A következő szakasz egy példát mutat be.

### <a name="syntax"></a>Syntax

A másolási tevékenység következő sablonja a támogatott tulajdonságok teljes listáját tartalmazza. Adja meg a forgatókönyvnek megfelelőket.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Szintaxis részletei

| Tulajdonság | Leírás | Kötelező? |
|:--- |:--- |:--- |
| típus | Másolási tevékenység esetén állítsa a következőre: `Copy` | Yes |
| bemenetek | Itt adhatja meg a forrásadatok számára kimutatott adatkészletet. A másolási tevékenység csak egyetlen bemenetet támogat. | Yes |
| kimenetek | Itt adhatja meg a fogadó adatra mutató adatkészletet. A másolási tevékenység csak egyetlen kimenetet támogat. | Yes |
| typeProperties | A másolási tevékenység konfigurálásához adja meg a tulajdonságokat. | Yes |
| source | Adja meg a másolás forrásának típusát és a megfelelő tulajdonságokat az adatok beolvasásához.<br/>További információ: "másolási tevékenység tulajdonságai" szakasz, a [támogatott adattárakban és-formátumokban](#supported-data-stores-and-formats)felsorolt összekötők című cikke. | Yes |
| mosogató | Adja meg a másolási fogadó típusát és a hozzá tartozó tulajdonságokat az adatíráshoz.<br/>További információ: "másolási tevékenység tulajdonságai" szakasz, a [támogatott adattárakban és-formátumokban](#supported-data-stores-and-formats)felsorolt összekötők című cikke. | Yes |
| Translator | Explicit oszlop-hozzárendelések meghatározása a forrásból a fogadóba. Ez a tulajdonság akkor érvényes, ha az alapértelmezett másolási viselkedés nem felel meg az igényeinek.<br/>További információ: [séma-hozzárendelés a másolási tevékenységben](copy-activity-schema-and-type-mapping.md). | No |
| dataIntegrationUnits | Adja meg azt a mértéket, amely az [Azure Integration Runtime](concepts-integration-runtime.md) által az adatmásoláshoz használt teljesítmény mennyiségét jelöli. Ezeket az egységeket korábban Felhőbeli adatáthelyezési egységeknek (DMU) nevezik. <br/>További információ: [adatintegrációs egységek](copy-activity-performance-features.md#data-integration-units). | No |
| parallelCopies | Itt adhatja meg azt a párhuzamosságot, amelyet a másolási tevékenység az adatok forrásból való beolvasásakor és az adatok fogadóba való írásához használni kíván.<br/>További információ: [párhuzamos másolás](copy-activity-performance-features.md#parallel-copy). | No |
| Preserve | Itt adhatja meg, hogy szeretné-e megőrizni a metaadatokat vagy az ACL-eket az adatok másolása <br/>További információ: a [metaadatok megőrzése](copy-activity-preserve-metadata.md). |No |
| enableStaging<br/>stagingSettings | Itt adhatja meg, hogy a blob Storage szolgáltatásban lévő ideiglenes adatok közvetlenül a forrásról a fogadóba másolva legyenek-e.<br/>További információ a hasznos forgatókönyvekről és a konfigurációs adatokról: [szakaszos másolás](copy-activity-performance-features.md#staged-copy). | No |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Válassza ki, hogyan kezelje a nem kompatibilis sorokat a forrásról a fogadóba másolt adatok másolása során.<br/>További információ: [hibatűrés](copy-activity-fault-tolerance.md). | No |

## <a name="monitoring"></a>Figyelés

A másolási tevékenységet a Azure Data Factory vizuálisan és programozott módon is figyelheti. Részletekért lásd: a [másolási tevékenység figyelése](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Növekményes másolat

A Data Factory lehetővé teszi a különbözeti adatok növekményes másolását egy forrás adattárból egy fogadó adattárba. További részletek: [oktatóanyag: adatok növekményes másolása](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

A [másolási tevékenység figyelési](copy-activity-monitoring.md) élménye megmutatja a másolási teljesítmény statisztikáit az egyes tevékenységek futtatásához. A [másolási tevékenység teljesítményének és méretezhetőségének útmutatója](copy-activity-performance.md) ismerteti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés teljesítményére a Azure Data Factory másolási tevékenységén keresztül. Felsorolja továbbá a tesztelés során megfigyelt teljesítményadatokat, valamint ismerteti a másolási tevékenység teljesítményének optimalizálását.

## <a name="resume-from-last-failed-run"></a>Folytatás az utolsó sikertelen futtatásból

A másolási tevékenység támogatja a legutóbbi sikertelen futtatást, ha nagy méretű fájlokat másol bináris formátummal a fájl alapú tárolók között, és úgy dönt, hogy a mappa vagy a fájl hierarchiáját a forrásról a fogadóra másolja, például az adatok áttelepítését az Amazon S3-ból a Azure Data Lake Storage Gen2ba. A következő file-alapú összekötőket érinti: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)és [SFTP](connector-sftp.md).

A másolási tevékenységet a következő két módon használhatja:

- **Tevékenység szintje újrapróbálkozás:** Megadhatja az újrapróbálkozások száma a másolási tevékenységnél. Ha a folyamat végrehajtása során a másolási tevékenység meghiúsul, a következő automatikus újrapróbálkozás az utolsó próbaverzió meghibásodási pontjától kezdődik.
- **Újrafuttatás sikertelen tevékenységből:** A folyamat befejezését követően az ADF felhasználói felület figyelési nézetében vagy programozott módon is indíthat újra a sikertelen tevékenységből. Ha a sikertelen tevékenység egy másolási tevékenység, a folyamat nem csak a tevékenységből fut újra, hanem az előző Futtatás meghibásodási pontjától is folytatódik.

    ![Másolás folytatása](media/copy-activity-overview/resume-copy.png)

Néhány fontos szempont:

- A folytatás a fájl szintjén történik. Ha a másolási tevékenység egy fájl másolása közben meghiúsul, a következő futtatáskor a rendszer újramásolja ezt az adott fájlt.
- A folytatáshoz a megfelelő működés érdekében ne módosítsa a másolási tevékenység beállításait az ismétlések között.
- Ha az Amazon S3, az Azure Blob, a Azure Data Lake Storage Gen2 és a Google Cloud Storage szolgáltatásból másol adatokból, a másolási tevékenység tetszőleges számú másolt fájlból folytathatja a másolást. Míg a többi fájl-alapú összekötő forrásként, a másolási tevékenység csak korlátozott számú fájlból támogatja a folytatást, általában a több tízezer tartományon, és a fájlelérési utak hosszától függően változik. a rendszer az ismétlések során újra másolja az ezen a számon túli fájlokat.

Más forgatókönyvek esetén, mint a bináris fájlmásolás, a másolási tevékenység újrafuttatása az elejétől kezdődik.

## <a name="preserve-metadata-along-with-data"></a>Metaadatok megőrzése az adatokkal együtt

Amikor adatokat másol a forrásról a fogadóba, olyan helyzetekben, mint a Lake Migration, úgy is dönthet, hogy a metaadatokat és az ACL-eket is megőrizte az adatok másolási tevékenységgel történő megőrzése mellett. A részletekért lásd a [metaadatok megőrzése](copy-activity-preserve-metadata.md) című témakört.

## <a name="schema-and-data-type-mapping"></a>Séma-és adattípus-hozzárendelés

Tekintse meg a [séma-és adattípusok leképezése](copy-activity-schema-and-type-mapping.md) című témakört, amely arról nyújt tájékoztatást, hogy a másolási tevékenység hogyan képezi le a forrás adatait

## <a name="add-additional-columns-during-copy"></a>További oszlopok hozzáadása a másolás során

Az adatoknak a forrás adattárból a fogadóba való másolása mellett úgy is beállíthatja, hogy további adatoszlopokat is adjon hozzá a fogadóhoz való másoláshoz. Például:

- Fájl alapú forrásból történő másoláskor a relatív fájl elérési útját egy további oszlopként tárolja, amelyből az adatokból származó fájl származik.
- A megadott forrás oszlop duplikálása egy másik oszlopként. 
- Adjon hozzá egy olyan oszlopot, amely ADF-kifejezéssel van ellátva, hogy csatolja az ADF rendszerváltozóit, például a folyamat nevét vagy a folyamat AZONOSÍTÓját, vagy más dinamikus értéket a felsőbb rétegbeli tevékenység kimenet
- Adjon hozzá egy statikus értéket tartalmazó oszlopot az alsóbb rétegbeli fogyasztási igények kielégítéséhez.

A másolási tevékenység forrása lapon a következő konfiguráció található. Ezeket a további oszlopokat is leképezheti a másolási tevékenység [sémájának leképezése](copy-activity-schema-and-type-mapping.md#schema-mapping) során a szokásos módon a definiált oszlopnevek használatával. 

![További oszlopok hozzáadása a másolási tevékenységhez](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Ez a funkció a legújabb adatkészlet-modellel működik. Ha nem látja ezt a lehetőséget a felhasználói felületen, próbálkozzon új adatkészlet létrehozásával.

Programozott módon történő konfigurálásához adja hozzá a `additionalColumns` tulajdonságot a másolási tevékenység forrásaként:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| additionalColumns | További adatoszlopokat adhat hozzá a fogadóba való másoláshoz.<br><br>A tömb alá tartozó minden objektum `additionalColumns` egy további oszlopot jelöl. A `name` meghatározza az oszlop nevét, a pedig `value` jelzi az oszlop adatértékét.<br><br>Az engedélyezett adatértékek a következők:<br>- **`$$FILEPATH`** – a fenntartott változó azt jelzi, hogy a forrásfájlok relatív elérési útját az adatkészletben megadott mappa elérési útjára tárolja. Alkalmazás fájl alapú forrásra.<br>- **`$$COLUMN:<source_column_name>`** -egy fenntartott változó minta azt jelzi, hogy a megadott forrás oszlop duplikálása egy másik oszlopként<br>- **Kifejezés**<br>- **Statikus érték** | No |

**Példa**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>Fogadó táblák automatikus létrehozása

Ha az adatok másolása az SQL Database-be/Azure szinapszis Analyticsbe történik, ha a céltábla nem létezik, a másolási tevékenység a forrásadatok alapján automatikusan létrehozza azt. Ennek célja, hogy segítsen gyorsan megkezdeni az adatgyűjtés betöltését és az SQL Database/Azure szinapszis Analytics kiértékelését. Az adatfeldolgozás után áttekintheti és módosíthatja a fogadó tábla sémáját az igényeinek megfelelően.

Ez a funkció akkor támogatott, ha bármely forrásból másol adatokból a következő fogadó adattárakba. Megtalálhatja a lehetőséget az *ADF authoring UI* – > *másolási tevékenység* fogadó – > *tábla lehetőség* – > *automatikus létrehozási tábla* vagy `tableOption` a tulajdonság a másolási tevékenység fogadója adattartalomban.

- [Azure SQL Database](connector-azure-sql-database.md)
- [Felügyelt példány Azure SQL Database](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Fogadó táblák létrehozása](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Hibatűrés

Alapértelmezés szerint a másolási tevékenység leállítja az adatok másolását, és hibát ad vissza, ha a forrásoldali adatsorok nem kompatibilisek a fogadó adatsoraival. A másolás sikeres végrehajtásához beállíthatja a másolási tevékenységet úgy, hogy kihagyja és naplózza a nem kompatibilis sorokat, és csak a kompatibilis adatfájlokat másolja. Részletekért lásd a [másolási tevékenység hibatűrését](copy-activity-fault-tolerance.md) ismertető témakört.

## <a name="data-consistency-verification"></a>Adatkonzisztencia-ellenőrzés

Ha a forrásról a célhelyre helyezi át az adatátvitelt, Azure Data Factory másolási tevékenység lehetővé teszi további adatkonzisztencia-ellenőrzés elvégzését, hogy az adatok ne legyenek csak a forrásról a célhelyre, hanem konzisztensek legyenek a forrás-és a célhely-tároló között. Ha inkonzisztens fájlok találhatók az adatáthelyezés során, megszakíthatja a másolási tevékenységet, vagy folytathatja a másolást. ehhez engedélyezze a hibatűrési beállítást az inkonzisztens fájlok kihagyásához. A kihagyott fájlnevek lekéréséhez engedélyezze a munkamenet-napló beállításait a másolási tevékenységben. Részletekért lásd: [adatkonzisztencia-ellenőrzés a másolási tevékenységben](copy-activity-data-consistency.md) .

## <a name="session-log"></a>Munkamenet-napló
Naplózhatja a másolt fájlneveket, amelyek segítségével tovább biztosíthatja, hogy az adatok nem csak a forrásról a célhelyre legyenek átmásolva, hanem a forrás-és a cél-tároló között is konzisztensek legyenek a másolási tevékenység munkamenet-naplófájljainak áttekintésével. Részletekért lásd: [munkamenet-napló a másolási tevékenységben](copy-activity-log.md) .

## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi rövid útmutatókat, oktatóanyagokat és mintákat:

- [Adatok másolása az egyik helyről egy másik helyre ugyanazon Azure Blob Storage-fiókban](quickstart-create-data-factory-dot-net.md)
- [Adatok másolása az Azure Blob Storage-ból a Azure SQL Databaseba](tutorial-copy-data-dot-net.md)
- [Adatok másolása SQL Server-adatbázisból az Azure-ba](tutorial-hybrid-copy-powershell.md)
