---
title: Azure Data Factory – JSON-parancsfájlok leírása
description: JSON-sémákat biztosít Data Factory entitásokhoz.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 91228b7f3243299f47c101e59d0c652749ff963d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779732"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory – JSON-parancsfájlok leírása
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik.


Ez a cikk JSON-sémákat és példákat tartalmaz Azure Data Factory entitások (folyamat, tevékenység, adatkészlet és társított szolgáltatás) definiálásához.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Folyamat
A folyamat definíciójának magas szintű szerkezete a következő:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Az alábbi táblázat a folyamat JSON-definíciójában található tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
| name | A folyamat neve. Adjon meg egy olyan nevet, amely a tevékenység vagy a folyamat számára konfigurált műveletet jelöli<br/><ul><li>A karakterek maximális száma: 260</li><li>Betűvel vagy aláhúzással () kell kezdődnie \_</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":" \\ , ""</li></ul> |Yes |
| leírás |A tevékenység vagy a folyamat felhasználási helyét leíró szöveg | No |
| tevékenységek | A tevékenységek listáját tartalmazza. | Yes |
| start |A folyamat kezdési dátuma és időpontja. [ISO formátumúnak](https://en.wikipedia.org/wiki/ISO_8601)kell lennie. Például: 2014-10-14T16:32:41. <br/><br/>Helyi időt is megadhat, például egy EST-időpontot. Íme egy példa: `2016-02-27T06:00:00**-05:00` , amely 6 est.<br/><br/>A kezdő és a záró tulajdonságok együttesen határozzák meg a folyamat aktív időszakát. A kimeneti szeletek csak ebben az aktív időszakban jönnek létre. |No<br/><br/>Ha a end tulajdonsághoz értéket ad meg, meg kell adnia a Start tulajdonság értékét.<br/><br/>Egy folyamat létrehozásához a kezdő és a záró időpont is lehet üres. Mindkét értéket meg kell adnia ahhoz, hogy aktív időszakot állítson be a folyamat futtatásához. Ha nem ad meg kezdési és befejezési időpontot a folyamat létrehozásakor, később beállíthatja őket a Set-AzDataFactoryPipelineActivePeriod parancsmaggal. |
| befejezés |A folyamat befejező dátuma és időpontja. Ha meg van adva, ISO-formátumúnak kell lennie. Például: 2014-10-14T17:32:41 <br/><br/>Helyi időt is megadhat, például egy EST-időpontot. Íme egy példa: `2016-02-27T06:00:00**-05:00` , amely 6 est.<br/><br/>A folyamat határozatlan ideig történő futtatásához adja meg a 9999-09-09 értéket az end (befejezés) tulajdonsághoz. |No <br/><br/>Ha megad egy értéket a Start tulajdonsághoz, meg kell adnia a end (Befejezés) tulajdonság értékét.<br/><br/>Lásd: Megjegyzések a **Start** tulajdonsághoz. |
| Ispaused fogalmak |Ha igaz értékre van állítva, a folyamat nem fut. Alapértelmezett érték = false. Ezt a tulajdonságot engedélyezheti vagy letilthatja. |No |
| pipelineMode |A folyamat futtatásának módszere. Az engedélyezett értékek a következők: ütemezett (alapértelmezett), egyszeri.<br/><br/>Az "ütemezett" érték azt jelzi, hogy a folyamat egy adott időintervallumban fut az aktív időszak (Kezdés és Befejezés időpontja) szerint. Az "egykori" érték azt jelzi, hogy a folyamat csak egyszer fut le. A létrehozott egyszeri folyamatok nem módosíthatók és nem frissíthetők jelenleg. Az egyszeri beállítással kapcsolatos részletekért tekintse meg az [egykori folyamat](data-factory-create-pipelines.md#onetime-pipeline) című témakört. |No |
| Expirationtime tulajdonságok |Az a létrehozás utáni időtartam, ameddig a folyamat érvényes, és a kiépítés előtt kell állnia. Ha nem rendelkezik aktív, sikertelen vagy függőben lévő futtatással, a folyamat automatikusan törlődik, amint eléri a lejárati időt. |No |


## <a name="activity"></a>Tevékenység
A folyamat definíciójában (tevékenységek elem) belüli tevékenység magas szintű szerkezete a következő:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

A következő táblázat a tevékenység JSON-definíciójában található tulajdonságokat ismerteti:

| Címke | Leírás | Kötelező |
| --- | --- | --- |
| name |A tevékenység neve. Adjon meg egy nevet, amely azt a műveletet jelöli, amely szerint a tevékenység konfigurálva van<br/><ul><li>A karakterek maximális száma: 260</li><li>Betűvel vagy aláhúzással () kell kezdődnie \_</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":" \\ , ""</li></ul> |Yes |
| leírás |Azon szöveg, amely leírja, hogy milyen tevékenység van használatban. |No |
| típus |Meghatározza a tevékenység típusát. Tekintse meg az [ADATtárakat](#data-stores) és az [Adatátalakítási tevékenységeket](#data-transformation-activities) ismertető szakaszt a különböző típusú tevékenységekhez. |Yes |
| bemenetek |A tevékenység által használt bemeneti táblák<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |HDInsightStreaming-és SqlServerStoredProcedure-tevékenységek esetében nem <br/> <br/> Igen, minden más számára |
| kimenetek |A tevékenység által használt kimeneti táblák.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Yes |
| linkedServiceName |A tevékenység által használt társított szolgáltatás neve. <br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. |Igen, az HDInsight tevékenységek, a Azure Machine Learning Studio (klasszikus) tevékenységek és a tárolt eljárási tevékenységek esetében. <br/><br/>Minden egyéb esetében: nem |
| typeProperties |A typeProperties szakaszban található tulajdonságok a tevékenység típusától függenek. |No |
| szabályzat |Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ha nincs megadva, a rendszer az alapértelmezett szabályzatokat használja. |No |
| scheduler |a "Scheduler" tulajdonság a tevékenység kívánt ütemezésének meghatározására szolgál. Az altulajdonságok ugyanazok, mint az [adatkészlet rendelkezésre állási tulajdonságában](data-factory-create-datasets.md#dataset-availability)lévők. |No |

### <a name="policies"></a>Házirendek
A házirendek hatással vannak egy tevékenység futásidejű viselkedésére, különösen akkor, ha egy tábla szeletét dolgozzák fel. A részleteket a következő táblázat tartalmazza.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Egyidejűség |Egész szám <br/><br/>Maximális érték: 10 |1 |A tevékenység egyidejű végrehajtásának száma.<br/><br/>Meghatározza, hogy hány párhuzamos tevékenység-végrehajtás történhet a különböző szeleteken. Ha például egy tevékenységnek az elérhető adatmennyiség nagy készletén kell haladnia, a nagyobb párhuzamossági érték felgyorsítja az adatfeldolgozást. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza a feldolgozás alatt álló adatszeletek sorrendjét.<br/><br/>Ha például 2 szelete van (egy 16:00-kor történik, egy másik pedig 5 órakor), és mindkettő függőben van. Ha úgy állítja be a executionPriorityOrder, hogy a NewestFirst, a szeletet 5 ÓRAKOR dolgozza fel a rendszer. Hasonlóképpen, ha úgy állítja be a executionPriorityORder, hogy a OldestFIrst legyen, akkor a szelet 4 ÓRAKOR lesz feldolgozva. |
| retry |Egész szám<br/><br/>A maximális érték lehet 10 |0 |Az újrapróbálkozások száma, mielőtt a szelet adatfeldolgozása sikertelenként van megjelölve. Az adatszeletek tevékenység-végrehajtásának újrapróbálkozása a megadott újrapróbálkozások számával történik. Az újrapróbálkozás a hiba után a lehető leghamarabb megtörténik. |
| timeout |időtartam |00:00:00 |A tevékenység időtúllépése. Példa: 00:10:00 (a 10 perc időtúllépését jelenti)<br/><br/>Ha egy érték nincs megadva vagy 0, az időtúllépés végtelen.<br/><br/>Ha egy szelet adatfeldolgozási ideje meghaladja az időtúllépési értéket, a rendszer megszakítja, és a rendszer megkísérli a feldolgozást. Az újrapróbálkozások száma az Újrapróbálkozás tulajdonságtól függ. Időtúllépés esetén az állapot időtúllépés értékre van állítva. |
| késedelem |időtartam |00:00:00 |A szelet adatfeldolgozásának megkezdése előtti késleltetés meghatározása.<br/><br/>Az adatszeletek tevékenységének végrehajtása akkor indul el, ha a késés a várt végrehajtási idő alatt van.<br/><br/>Példa: 00:10:00 (a 10 perc késleltetését jelenti) |
| longRetry |Egész szám<br/><br/>Maximális érték: 10 |1 |A hosszú újrapróbálkozási kísérletek száma a szelet végrehajtásának meghiúsulása előtt.<br/><br/>a longRetry-kísérletek longRetryInterval szerint vannak elfoglalva. Ha tehát az újrapróbálkozási kísérletek közötti időt kell megadnia, használja a longRetry. Ha az újrapróbálkozási és a longRetry is meg van adva, az egyes longRetry-kísérletek az újrapróbálkozási kísérleteket is tartalmazzák, és a kísérletek maximális száma újrapróbálkozás * longRetry.<br/><br/>Ha például a következő beállítások szerepelnek a tevékenység-házirendben:<br/>Újrapróbálkozás: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, hogy csak egy szeletet kell végrehajtani (az állapot várakozik), és a tevékenység végrehajtása minden alkalommal meghiúsul. Kezdetben 3 egymást követő végrehajtási kísérlet lenne. Minden kísérlet után a szelet állapota újra próbálkozik. Az első 3 próbálkozás után a szelet állapota LongRetry lesz.<br/><br/>Egy óra (azaz a longRetryInteval értéke) után egy másik 3 egymást követő végrehajtási kísérlet lenne. Ezt követően a szelet állapota meghiúsul, és a rendszer nem próbálkozik újra. Ezért összesen 6 kísérlet történt.<br/><br/>Ha bármelyik végrehajtás sikeres, a szelet állapota készen áll, és a rendszer nem próbálkozik újra.<br/><br/>a longRetry olyan helyzetekben használhatók, ahol a függő adat nem determinisztikus időpontokban érkezik, vagy az általános környezet az adatfeldolgozási folyamat alatt álló adatfeldolgozás. Ilyen esetekben előfordulhat, hogy egy másik után újrapróbálkozik, és a kívánt kimenet eltelte után egy idő elteltével nem jár sikerrel.<br/><br/>Figyelmeztetés: ne állítson be magas értéket a longRetry vagy a longRetryInterval. A magasabb értékek jellemzően más rendszerszintű problémákat jelentenek. |
| longRetryInterval |időtartam |00:00:00 |A hosszú újrapróbálkozási kísérletek közötti késleltetés |

### <a name="typeproperties-section"></a>typeProperties szakasz
A typeProperties szakasz minden tevékenység esetében eltér. Az átalakítási tevékenységek csak a típus tulajdonságaival rendelkeznek. A folyamaton belüli átalakítási tevékenységeket meghatározó JSON-mintákhoz lásd a jelen cikk [ADATátalakítási tevékenységek](#data-transformation-activities) című szakaszát.

A **másolási tevékenységnek** két alszakasza van a typeProperties szakaszban: **forrás** **és fogadó**. Tekintse meg a jelen cikk [adattárak](#data-stores) című szakaszát, amely azt mutatja be, hogyan használható az adattár forrásként és/vagy fogadóként.

### <a name="sample-copy-pipeline"></a>Minta másolási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **Másolás** típusú tevékenység található. Ebben a példában a [másolási tevékenység](data-factory-data-movement-activities.md) az Azure Blob Storage-ból másolja át az adatait Azure SQL Databaseba.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Vegye figyelembe a következő szempontokat:

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****Copy** értékre van beállítva.
* A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**.
* A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.

Tekintse meg a jelen cikk [adattárak](#data-stores) című szakaszát, amely azt mutatja be, hogyan használható az adattár forrásként és/vagy fogadóként.

A folyamat létrehozásának teljes áttekintését az [oktatóanyag: adatok másolása blob Storageról SQL Databasera](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)című témakörben tekintheti meg.

### <a name="sample-transformation-pipeline"></a>Minta átalakítási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **HDInsightHive** típusú tevékenység található. Ebben a mintában a [HDInsight Hive-tevékenység](data-factory-hive-activity.md) egy Azure blobtárolóból származó adatokat alakít át egy Hive-szkriptfájl Azure HDInsight Hadoop-fürtön történő futtatásával.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Vegye figyelembe a következő szempontokat:

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****HDInsightHive** értékre van beállítva.
* A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **AzureStorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.
* A defines ( **Definiálás** ) szakasz a kaptári parancsfájlnak átadott futásidejű beállítások megadására szolgál kaptár-konfigurációs értékekként (például: `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` ).

A folyamaton belüli átalakítási tevékenységeket meghatározó JSON-mintákhoz lásd a jelen cikk [ADATátalakítási tevékenységek](#data-transformation-activities) című szakaszát.

A folyamat létrehozásának teljes áttekintését az [oktatóanyag: az első folyamat létrehozása az Hadoop-fürttel történő adatfeldolgozáshoz](data-factory-build-your-first-pipeline.md)című témakörben tekintheti meg.

## <a name="linked-service"></a>Társított szolgáltatások
A társított szolgáltatás definíciójának magas szintű szerkezete a következő:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

A következő táblázat a tevékenység JSON-definíciójában található tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| name | A társított szolgáltatás neve. | Yes |
| tulajdonságok – típus | A társított szolgáltatás típusa. Például: Azure Storage, Azure SQL Database. |
| typeProperties | A typeProperties szakasz az egyes adattárakhoz és számítási környezetekhez eltérő elemeket tartalmaz. Tekintse meg az adattárak szakaszt az összes adattárhoz társított szolgáltatáshoz és [számítási környezethez](#compute-environments) az összes számítási társított szolgáltatáshoz |

## <a name="dataset"></a>Adathalmaz
Azure Data Factoryban található adatkészlet a következőképpen van meghatározva:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

A fenti JSON-tulajdonságokat a következő táblázat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| name | Az adatkészlet neve. Lásd: [Azure Data Factory-elnevezési szabályok](data-factory-naming-rules.md) az elnevezési szabályokhoz. |Yes |NA |
| típus | Az adatkészlet típusa. A Azure Data Factory által támogatott típusok egyikét kell megadnia (például: AzureBlob, tulajdonsága azuresqltable). A Data Factory által támogatott adattárakkal és adatkészletekkel kapcsolatos információk az [adattárak](#data-stores) szakaszban találhatók. |
| szerkezet | Az adatkészlet sémája. Oszlopokat, azok típusait stb. tartalmaz. | No |NA |
| typeProperties | A kiválasztott típusnak megfelelő tulajdonságok Tekintse meg a támogatott típusok és tulajdonságaik című szakaszt az [adattárak](#data-stores) szakaszban. |Yes |NA |
| external | Logikai jelző annak megadásához, hogy az adatkészletet explicit módon állították-e be egy adat-előállító folyamat vagy sem. |No |hamis |
| availability | Meghatározza a feldolgozási időszakot vagy a szeletelő modelljét az adatkészlet termeléséhez. Az adatkészlet szeletelő modelljével kapcsolatos részletekért lásd: [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) cikk. |Yes |NA |
| szabályzat |Meghatározza azokat a feltételeket vagy feltételt, amelyeknek az adatkészlet-szeleteknek teljesíteniük kell. <br/><br/>Részletekért lásd: adatkészlet-házirend szakasz. |No |NA |

A **struktúra** szakasz minden oszlopa a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| name |Az oszlop neve. |Yes |
| típus |Az oszlop adattípusa.  |No |
| kulturális környezet |A típus megadásakor használandó .NET-alapú kulturális környezet (.NET-típus `Datetime` vagy `Datetimeoffset` ). Az alapértelmezett szint a `en-us`. |No |
| formátumban |A típus megadásakor használandó formázó karakterlánc (.NET-típus `Datetime` vagy `Datetimeoffset` ). |No |

A következő példában az adatkészlet három oszlopból áll, és ezek a következők `slicetimestamp` `projectname` `pageviews` : string, string és decimális.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A következő táblázat a **rendelkezésre állási** szakaszban használható tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészlet-szelet gyártásának időegységét.<br/><br/><b>Támogatott gyakoriság</b>: perc, óra, nap, hét, hónap |Yes |NA |
| interval |A gyakoriság szorzóját adja meg<br/><br/>A "Frequency x Interval" érték határozza meg, hogy milyen gyakran történjen a szelet előállítása.<br/><br/>Ha az adatkészletet óránként kell darabolni, a <b>gyakoriságot</b> <b>óra</b>értékre kell állítani, és az <b>intervallumot</b> <b>1-re</b>kell állítania.<br/><br/><b>Megjegyzés</b>: Ha a gyakoriságot percben adja meg, azt javasoljuk, hogy az intervallumot 15-nél kevesebbre állítsa be |Yes |NA |
| stílus |Megadja, hogy a szelet az intervallum elején/végén legyen-e előkészítve.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Ha a gyakoriság értéke hónap, és a Style EndOfInterval értékre van állítva, a szelet a hónap utolsó napján jön létre. Ha a stílus StartOfInterval értékre van állítva, a szelet a hónap első napján jön létre.<br/><br/>Ha a gyakoriság beállítása nap, a stílus pedig EndOfInterval, a szelet a nap utolsó órájában jön létre.<br/><br/>Ha a gyakoriság értéke óra, és a stílus értéke EndOfInterval, a szelet az óra végén jön létre. A szeletek esetében például 1 – 2 PM-időszak esetén a SZELET 2 ÓRAKOR jön létre. |No |EndOfInterval |
| anchorDateTime |Meghatározza a ütemező által az adatkészlet-szeletek határainak kiszámításához használt abszolút pozíciót. <br/><br/><b>Megjegyzés</b>: Ha a AnchorDateTime olyan részek vannak, amelyek részletesebbek, mint a gyakoriság, akkor a rendszer figyelmen kívül hagyja a további szemcsés részeket. <br/><br/>Ha például az <b>intervallum</b> <b>óránként</b> (Frequency: Hour és Interval: 1), a <b>AnchorDateTime</b> pedig <b>perc és másodperc</b> , akkor a rendszer figyelmen kívül hagyja a AnchorDateTime <b>perc és másodperc</b> részeit. |No |01/01/0001 |
| offset |TimeSpan, amely az összes adatkészlet összes szeletének kezdetét és végét eltolja. <br/><br/><b>Megjegyzés</b>: Ha a anchorDateTime és az eltolás is meg van adva, az eredmény a kombinált eltolás. |No |NA |

A következő rendelkezésre állási szakasz azt adja meg, hogy a kimeneti adatkészlet óránkénti (vagy) bemeneti adatkészletet hoz létre óránként:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Az adatkészlet definíciójának **szabályzat** szakasza meghatározza azokat a feltételeket vagy feltételt, amelyeknek az adatkészlet-szeleteknek teljesíteniük kell.

| Házirend neve | Leírás | Alkalmazva erre | Kötelező | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ellenőrzi, hogy egy **Azure-blobban** lévő adat megfelel-e a minimális méretre vonatkozó követelményeknek (megabájtban). |Azure-blob |No |NA |
| minimumRows |Ellenőrzi, hogy **Azure SQL Database** vagy egy **Azure-táblában** lévő adatmennyiség tartalmazza-e a sorok minimális számát. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |No |NA |

**Példa**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Ha Azure Data Factory nem állít elő adatkészletet, akkor azt **külsőnek** kell megjelölni. Ez a beállítás általában a folyamat első tevékenységének bemenetére vonatkozik, kivéve, ha a tevékenység vagy a folyamat láncolására kerül sor.

| Név | Leírás | Kötelező | Alapértelmezett érték |
| --- | --- | --- | --- |
| dataDelay |Az adott szelet külső adatának rendelkezésre állására vonatkozó ellenőrzések késleltetésének ideje. Ha például az adatmennyiség óránként elérhető, az ellenőrzéssel megtekintheti a külső adatforrásokat, és a megfelelő szelet készen áll a dataDelay használatával.<br/><br/>Csak a jelen időpontra érvényes.  Ha például ez a 1:00 PM, és ez az érték 10 perc, az érvényesítés 1:10 ÓRAKOR kezdődik.<br/><br/>Ez a beállítás nem érinti a múltban lévő szeleteket (a szeletek a szeletek befejezési idejével + dataDelay < most) késleltetés nélkül lesznek feldolgozva.<br/><br/>A 23:59 óránál nagyobb időt kell megadni a formátum használatával `day.hours:minutes:seconds` . Ha például 24 órát szeretne megadni, ne használja a 24:00:00; Ehelyett használja az 1.00:00:00 értéket. Ha 24:00:00-et használ, azt 24 nap (24.00:00:00) kezeli. 1 és 4 óra esetén a 1:04:00:00-es megadását kell megadnia. |No |0 |
| retryInterval |A hiba és a következő újrapróbálkozási kísérlet közötti várakozási idő. Ha a próbálkozás sikertelen, a következő próbálkozás a retryInterval után következik be. <br/><br/>Ha jelenleg 1:00 PM, kezdjük az első próbálkozással. Ha az első érvényesítési ellenőrzés befejezésének időtartama 1 perc, és a művelet meghiúsult, a következő Újrapróbálkozás 1:00 + 1 percnél (időtartam) + 1 perc (újrapróbálkozás időköze) = 1:02 PM. <br/><br/>A múltban a szeletek esetében nincs késés. Az újrapróbálkozás azonnal megtörténik. |No |00:01:00 (1 perc) |
| retryTimeout |Az újrapróbálkozási kísérletek időtúllépése.<br/><br/>Ha ez a tulajdonság 10 percre van beállítva, az ellenőrzést 10 percen belül be kell fejezni. Ha az ellenőrzés elvégzése 10 percnél hosszabb időt vesz igénybe, az újrapróbálkozások időtúllépést jelentenek.<br/><br/>Ha az érvényesítéshez szükséges összes kísérlet túllépi az időkorlátot, a szelet időtúllépés lesz megjelölve. |No |00:10:00 (10 perc) |
| maximumRetry |Azon alkalmak száma, amelyek alapján a külső adatmennyiség rendelkezésre áll. Az engedélyezett maximális érték 10. |No |3 |


## <a name="data-stores"></a>adattárak
A [társított szolgáltatás](#linked-service) szakasz a társított szolgáltatások összes típusához közös JSON-elemek leírását tartalmazza. Ez a szakasz részletesen ismerteti az egyes adattárokra jellemző JSON-elemeket.

Az [adatkészlet](#dataset) szakasz az adathalmazok összes típusához közös JSON-elemek leírását tartalmazza. Ez a szakasz részletesen ismerteti az egyes adattárokra jellemző JSON-elemeket.

A [tevékenység](#activity) szakasz az összes típusú tevékenységhez közös JSON-elemek leírását tartalmazza. Ez a szakasz részletesen ismerteti az egyes adattárakhoz tartozó JSON-elemeket, ha a másolási tevékenység forrásaként/fogadóként van használatban.

Kattintson arra a tárolóra, amelyre kíváncsi, hogy megjelenjenek a társított szolgáltatás, az adatkészlet és a forrás/fogadó JSON-sémái a másolási tevékenységhez.

| Kategória | Adattár
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure Synapse Analytics](#azure-synapse-analytics) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Adatbázisok** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Fájl** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Fájlrendszer](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Továbbiak** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Webtábla |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Társított szolgáltatások
A társított szolgáltatások két típusa létezik: az Azure Storage társított szolgáltatás és az Azure Storage SAS társított szolgáltatása.

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Ha az Azure Storage-fiókot egy adatgyárhoz szeretné kapcsolni a **fiók kulcsa** segítségével, hozzon létre egy Azure Storage-beli társított szolgáltatást. Egy Azure Storage-beli társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **AzureStorage** értékre. Ezután megadhatja a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| connectionString |Itt adhatja meg az Azure Storage-hoz a connectionString tulajdonsághoz való kapcsolódáshoz szükséges adatokat. |Yes |

##### <a name="example"></a>Példa

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS társított szolgáltatás
Az Azure Storage SAS társított szolgáltatása lehetővé teszi egy Azure Storage-fiók összekapcsolását egy Azure-beli adatgyárhoz egy közös hozzáférési aláírás (SAS) használatával. Az adat-előállítót a tárolóban lévő összes/meghatározott erőforrás (blob/tároló) számára korlátozott/időhöz kötött hozzáféréssel biztosítja. Ha közös hozzáférésű aláírással szeretné összekapcsolni az Azure Storage-fiókot egy adatgyárhoz, hozzon létre egy Azure Storage SAS-beli társított szolgáltatást. Az Azure Storage SAS társított szolgáltatásának definiálásához állítsa be a társított szolgáltatás **típusát** a **AzureStorageSas** értékre. Ezután megadhatja a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| sasUri |Az Azure Storage-erőforrásokhoz, például a blobhoz, a tárolóhoz vagy a táblához válassza a közös hozzáférési aláírás URI-JÁT. |Yes |

##### <a name="example"></a>Példa

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

További információ ezekről a társított szolgáltatásokról: [Azure Blob Storage-összekötő](data-factory-azure-blob-connector.md#linked-service-properties) című cikk.

### <a name="dataset"></a>Adathalmaz
Az Azure Blob-adatkészlet definiálásához állítsa az adatkészlet **típusát** a **AzureBlob** értékre. Ezután adja meg a következő Azure Blob-specifikus tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A tároló és a mappa elérési útja a blob Storage-ban. Példa: myblobcontainer\myblobfolder\ |Yes |
| fileName |A blob neve. a fájlnév nem kötelező és megkülönbözteti a kis-és nagybetűket.<br/><br/>Ha fájlnevet ad meg, a tevékenység (beleértve a másolást is) az adott blobon működik.<br/><br/>Ha nincs megadva a fájlnév, a másolás a folderPath található összes blobot tartalmazza a bemeneti adatkészlethez.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a generált fájl neve a következő formátumú lesz: `Data.<Guid>.txt` (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |a partitionedBy egy nem kötelező tulajdonság. Ezzel a beállítással megadhatja a dinamikus folderPath és a fájlnevet az idősorozat-adatsorokhoz. A folderPath például minden egyes órányi adatértékhez paraméterként lehet megadni. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


További információt az [Azure Blob-összekötő](data-factory-azure-blob-connector.md#dataset-properties) című cikkben talál.

### <a name="blobsource-in-copy-activity"></a>BlobSource a másolási tevékenységben
Ha Azure-Blob Storage **másolja az adatait** , állítsa be a másolási tevékenység **BlobSource**, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. |True (alapértelmezett érték), hamis |No |

#### <a name="example-blobsource"></a>Példa: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink a másolási tevékenységben
Ha egy Azure-Blob Storage **másolja az adatok** másolását, állítsa a másolási tevékenység **BlobSink**, és adja meg a következő **tulajdonságokat a fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior |Meghatározza a másolási viselkedést a forrás BlobSource vagy fájlrendszerének használatakor. |<b>PreserveHierarchy</b>: megőrzi a fájl hierarchiáját a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><br/><b>FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén található. A célfájl automatikusan generált névvel rendelkezik. <br/><br/><b>MergeFiles (alapértelmezett):</b> egyesít minden fájlt a forrás mappájából egy fájlba. Ha meg van adva a fájl/blob neve, az egyesített fájl neve a megadott név lesz. Ellenkező esetben az automatikusan generált fájlnév lenne. |No |

#### <a name="example-blobsink"></a>Példa: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

További információt az [Azure Blob-összekötő](data-factory-azure-blob-connector.md#copy-activity-properties) című cikkben talál.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Társított szolgáltatások
Azure Data Lake Store társított szolgáltatás definiálásához állítsa be a társított szolgáltatás típusát a **AzureDataLakeStore** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **AzureDataLakeStore** | Yes |
| dataLakeStoreUri | A Azure Data Lake Store fiók adatainak meghatározása. A formátuma a következő: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/` . | Yes |
| subscriptionId | Az Azure-előfizetés azonosítója, amelyhez Data Lake Store tartozik. | A fogadóhoz szükséges |
| resourceGroupName | Az Azure-erőforráscsoport neve, amelyhez Data Lake Store tartozik. | A fogadóhoz szükséges |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Igen (az egyszerű szolgáltatás hitelesítéséhez) |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. | Igen (az egyszerű szolgáltatás hitelesítéséhez) |
| Bérlő | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Igen (az egyszerű szolgáltatás hitelesítéséhez) |
| engedélyezés | Kattintson az **Engedélyezés** gombra a **Data Factory szerkesztőben** , és adja meg a hitelesítő adatait, amely hozzárendeli az automatikusan generált engedélyezési URL-címet ehhez a tulajdonsághoz. | Igen (a felhasználói hitelesítő adatok hitelesítéséhez)|
| sessionId | OAuth munkamenet-azonosító a OAuth-engedélyezési munkamenetből. Az egyes munkamenet-AZONOSÍTÓk egyediek, és csak egyszer használhatók. A beállítás automatikusan létrejön, amikor Data Factory szerkesztőt használ. | Igen (a felhasználói hitelesítő adatok hitelesítéséhez) |

#### <a name="example-using-service-principal-authentication"></a>Példa: egyszerű szolgáltatásnév hitelesítésének használata
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Példa: felhasználói hitelesítő adatok hitelesítésének használata
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

További információ: Azure Data Lake Store- [összekötő](data-factory-azure-datalake-connector.md#linked-service-properties) cikke.

### <a name="dataset"></a>Adathalmaz
Azure Data Lake Store adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **AzureDataLakeStore** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| folderPath |A tároló és a mappa elérési útja a Azure Data Lake tárolóban. |Yes |
| fileName |A fájl neve a Azure Data Lake tárolóban. a fájlnév nem kötelező és megkülönbözteti a kis-és nagybetűket. <br/><br/>Ha fájlnevet ad meg, a tevékenység (beleértve a másolást is) az adott fájlon működik.<br/><br/>Ha nincs megadva a fájlnév, a másolás a bemeneti adatkészlethez tartozó folderPath található összes fájlt tartalmazza.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a generált fájl neve a következő formátumú lesz: `Data.<Guid>.txt` (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |a partitionedBy egy nem kötelező tulajdonság. Ezzel a beállítással megadhatja a dinamikus folderPath és a fájlnevet az idősorozat-adatsorokhoz. A folderPath például minden egyes órányi adatértékhez paraméterként lehet megadni. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example"></a>Példa
```json
{
    "name": "AzureDataLakeStoreInput",
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
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: Azure Data Lake Store- [összekötő](data-factory-azure-datalake-connector.md#dataset-properties) cikke.

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Forrás Azure Data Lake Store a másolási tevékenységben
Ha egy Azure Data Lake Store adatok másolását választja, állítsa a másolási tevékenység **AzureDataLakeStoreSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:

A **AzureDataLakeStoreSource** a következő tulajdonságokat támogatja **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. |True (alapértelmezett érték), hamis |No |

#### <a name="example-azuredatalakestoresource"></a>Példa: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

További információ: Azure Data Lake Store- [összekötő](data-factory-azure-datalake-connector.md#copy-activity-properties) cikke.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store fogadó a másolási tevékenységben
Ha Adatmásolást végez egy Azure Data Lake Storeba, állítsa a másolási tevékenység **AzureDataLakeStoreSink**, és adja **meg a következő** tulajdonságokat a **fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior |Megadja a másolási viselkedést. |<b>PreserveHierarchy</b>: megőrzi a fájl hierarchiáját a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><br/><b>FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén jön létre. A rendszer automatikusan generált névvel hozza létre a megcélzott fájlokat.<br/><br/><b>MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl/blob neve, az egyesített fájl neve a megadott név lesz. Ellenkező esetben az automatikusan generált fájlnév lenne. |No |

#### <a name="example-azuredatalakestoresink"></a>Példa: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
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
        }]
    }
}
```

További információ: Azure Data Lake Store- [összekötő](data-factory-azure-datalake-connector.md#copy-activity-properties) cikke.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Társított szolgáltatások
Azure Cosmos DB társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **DocumentDb** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| connectionString |Azure Cosmos DB adatbázishoz való kapcsolódáshoz szükséges információk megadásához. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
További információ: Azure Cosmos DB- [összekötő](data-factory-azure-documentdb-connector.md#linked-service-properties) cikke.

### <a name="dataset"></a>Adathalmaz
Azure Cosmos DB adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **DocumentDbCollection** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| collectionName |A Azure Cosmos DB gyűjtemény neve. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
További információ: Azure Cosmos DB- [összekötő](data-factory-azure-documentdb-connector.md#dataset-properties) cikke.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Azure Cosmos DB gyűjtemény forrása a másolási tevékenységben
Ha egy Azure Cosmos DB adatok másolását választja, állítsa a másolási tevékenység **DocumentDbCollectionSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:


| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| lekérdezés |Itt adhatja meg az adatolvasási lekérdezést. |Azure Cosmos DB által támogatott lekérdezési karakterlánc. <br/><br/>Például: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Ha nincs megadva, a futtatott SQL-utasítás: `select <columns defined in structure> from mycollection` |
| nestingSeparator |A dokumentum beágyazásának jelzésére szolgáló speciális karakter |Bármilyen karakter. <br/><br/>Azure Cosmos DB a JSON-dokumentumok NoSQL-tárolója, ahol beágyazott struktúrák engedélyezettek. Azure Data Factory lehetővé teszi a felhasználó számára a hierarchia jelölését a nestingSeparator-n keresztül, amely a következő: "." a fenti példákban. Az elválasztó használatával a másolási tevékenység a "név. First", a "név. középső" és a "name. Last" kifejezésnek megfelelően létrehozza a "Name" objektumot az első, középső és utolsó értékkel. |No |

#### <a name="example"></a>Példa

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB gyűjtemény fogadója a másolási tevékenységben
Ha az adatok másolása Azure Cosmos DBre történik, állítsa a másolási tevékenység **DocumentDbCollectionSink**, és adja **meg a következő** **tulajdonságokat a fogadó szakaszban:**

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| nestingSeparator |A forrás oszlop nevének egy speciális karaktere, amely azt jelzi, hogy beágyazott dokumentumra van szükség. <br/><br/>A fenti példában például a `Name.First` kimenet táblában a következő JSON-struktúra jön létre a Cosmos db dokumentumban:<br/><br/>"Name": {<br/>    "Első": "John"<br/>}, |A beágyazási szinteket elválasztó karakter.<br/><br/>Az alapértelmezett érték `.` (pont). |A beágyazási szinteket elválasztó karakter. <br/><br/>Az alapértelmezett érték `.` (pont). |
| writeBatchSize |A Azure Cosmos DB szolgáltatás számára a dokumentumok létrehozásához szükséges párhuzamos kérelmek száma.<br/><br/>Ennek a tulajdonságnak a használatával javíthatja a teljesítményt a Azure Cosmos DBba való adatmásoláskor. Nagyobb teljesítmény várható, ha a writeBatchSize növelése miatt Azure Cosmos DB több párhuzamos kérelem küldése történik. Azonban el kell kerülnie a szabályozást, amely a következő hibaüzenetet eredményezi: "a kérelmek aránya nagy".<br/><br/>A szabályozást számos tényező határozza meg, többek között a dokumentumok mérete, a dokumentumok feltételeinek száma, a célhelyek indexelési szabályzata stb. A másolási műveletekhez használhat jobb gyűjteményt (például S3), hogy a lehető legtöbb átviteli sebesség legyen elérhető (2 500 kérelem egység/másodperc). |Egész szám |Nem (alapértelmezett: 5) |
| writeBatchTimeout |Várakozási idő a művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: "00:30:00" (30 perc). |No |

#### <a name="example"></a>Példa

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

További információ: Azure Cosmos DB- [összekötő](data-factory-azure-documentdb-connector.md#copy-activity-properties) cikke.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Társított szolgáltatások
Azure SQL Database társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **AzureSqlDatabase** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| connectionString |A connectionString tulajdonsághoz Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatok megadása. |Yes |

#### <a name="example"></a>Példa
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

További információ: [Azure SQL Connector](data-factory-azure-sql-connector.md#linked-service-properties) – cikk.

### <a name="dataset"></a>Adathalmaz
Azure SQL Database adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **tulajdonsága azuresqltable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Azon Azure SQL Database-példányban található tábla vagy nézet neve, amelyre a társított szolgáltatás hivatkozik. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
További információ: [Azure SQL Connector](data-factory-azure-sql-connector.md#dataset-properties) – cikk.

### <a name="sql-source-in-copy-activity"></a>SQL-forrás a másolási tevékenységben
Ha egy Azure SQL Database adatok másolását választja, állítsa a másolási tevékenység **SqlSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Azon tárolt eljárás neve, amely beolvassa az adatokat a forrás táblából. |A tárolt eljárás neve. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```
További információ: [Azure SQL Connector](data-factory-azure-sql-connector.md#copy-activity-properties) – cikk.

### <a name="sql-sink-in-copy-activity"></a>SQL-fogadó a másolási tevékenységben
Ha az adatok másolása Azure SQL Databasere történik, állítsa a másolási tevékenység **SqlSink**, és adja **meg a következő** **tulajdonságokat a fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: "00:30:00" (30 perc). |No |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |A másolási tevékenységre vonatkozó lekérdezés megadása úgy, hogy egy adott szeletből származó adatmennyiséget takarítson meg. |Egy lekérdezési utasítás. |No |
| sliceIdentifierColumnName |Adja meg a másolási tevékenység oszlopának nevét, amely automatikusan generált szelet-azonosítóval kitöltésre kerül, amely egy adott szelet adatának az újrafuttatáskor való kitakarítására szolgál. |A bináris adattípusú oszlop neve (32). |No |
| sqlWriterStoredProcedureName |Annak a tárolt eljárásnak a neve, amely a upsert (frissítések/beszúrások) a célként megadott táblába kerül. |A tárolt eljárás neve. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |
| sqlWriterTableType |Adja meg a tárolt eljárásban használni kívánt táblanév nevét. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a meglévő adattal másolható adatmásolási műveleteket. |Egy tábla típusának neve. |No |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
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
        }]
    }
}
```

További információ: [Azure SQL Connector](data-factory-azure-sql-connector.md#copy-activity-properties) – cikk.

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="linked-service"></a>Társított szolgáltatások
Az Azure szinapszis Analytics társított szolgáltatásának definiálásához állítsa a társított szolgáltatás **típusát** **AzureSqlDW** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| connectionString |A connectionString tulajdonsághoz tartozó Azure szinapszis Analytics-példányhoz való kapcsolódáshoz szükséges információk megadása. |Yes |



#### <a name="example"></a>Példa

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

További információt az [Azure szinapszis Analytics-összekötő](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) című cikkben talál.

### <a name="dataset"></a>Adathalmaz
Az Azure szinapszis Analytics-adatkészlet definiálásához állítsa az adatkészlet **típusát** a **AzureSqlDWTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a táblának vagy nézetnek a neve, amelyre a társított szolgáltatás hivatkozik az Azure szinapszis Analytics-adatbázisban. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információt az [Azure szinapszis Analytics-összekötő](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) című cikkben talál.

### <a name="azure-synapse-analytics-source-in-copy-activity"></a>Az Azure szinapszis Analytics forrása a másolási tevékenységben
Ha az Azure szinapszis Analyticsből másol Adatmásolást, állítsa **be a** másolási tevékenység **SqlDWSource**, és adja meg a következő tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Azon tárolt eljárás neve, amely beolvassa az adatokat a forrás táblából. |A tárolt eljárás neve. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

További információt az [Azure szinapszis Analytics-összekötő](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) című cikkben talál.

### <a name="azure-synapse-analytics-sink-in-copy-activity"></a>Az Azure szinapszis Analytics fogadója a másolási tevékenységben
Ha az adatok másolása az Azure szinapszis Analytics szolgáltatásba történik,  állítsa a másolási tevékenység fogadó típusát **SqlDWSink** értékre, és adja meg a következő tulajdonságokat a **fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |A másolási tevékenységre vonatkozó lekérdezés megadása úgy, hogy egy adott szeletből származó adatmennyiséget takarítson meg. |Egy lekérdezési utasítás. |No |
| allowPolyBase |Azt jelzi, hogy a BULKINSERT mechanizmus helyett a következőt kell-e használni (ha van ilyen). <br/><br/> **Az adatok Azure szinapszis-elemzésbe való betöltésének ajánlott módja a Base használata.** |Igaz <br/>False (alapértelmezett) |No |
| polyBaseSettings |A tulajdonságok olyan csoportja, amely akkor adható meg, ha a **allowPolybase** tulajdonság értéke **true (igaz**). |&nbsp; |No |
| rejectValue |A lekérdezés sikertelensége előtt visszautasítható sorok számát vagy százalékos arányát adja meg. <br/><br/>További információ a [create External Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) című témakör **argumentumok** szakaszában található alapszintű elutasítás beállításairól. |0 (alapértelmezett), 1, 2,... |No |
| rejectType |Megadja, hogy a rejectValue beállítás literál értékként vagy százalékként van-e megadva. |Érték (alapértelmezett), százalék |No |
| rejectSampleValue |Meghatározza a lekérdezni kívánt sorok számát, mielőtt a rendszer újraszámítja az elutasított sorok százalékos arányát. |1, 2,... |Igen, ha  a rejectType **százaléka** |
| useTypeDefault |Meghatározza, hogy a rendszer hogyan kezelje a hiányzó értékeket a tagolt szövegfájlokban, ha a viszonyítási adatok beolvasása a szövegfájlból történik.<br/><br/>Erről a tulajdonságról a [külső fájlformátum létrehozása (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)argumentumai című szakaszban olvashat bővebben. |Igaz, hamis (alapértelmezett) |No |
| writeBatchSize |Az SQL-táblába szúrja be az adatmennyiséget, ha a puffer mérete eléri a writeBatchSize |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: "00:30:00" (30 perc). |No |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
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
        }]
    }
}
```

További információt az [Azure szinapszis Analytics-összekötő](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) című cikkben talál.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Társított szolgáltatások
Az Azure Cognitive Search társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **AzureSearch** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| url | A keresési szolgáltatás URL-címe. | Yes |
| kulcs | A keresési szolgáltatáshoz tartozó rendszergazdai kulcs. | Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

További információt az [Azure Cognitive Search-összekötő](data-factory-azure-search-connector.md#linked-service-properties) című cikkben talál.

### <a name="dataset"></a>Adathalmaz
Az Azure Cognitive Search adatkészlet definiálásához állítsa az adatkészlet **típusát** a **AzureSearchIndex** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| típus | A Type tulajdonságot **AzureSearchIndex** értékre kell beállítani.| Yes |
| indexName | A keresési index neve. Data Factory nem hozza létre az indexet. Az indexnek léteznie kell az Azure Cognitive Searchban. | Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

További információt az [Azure Cognitive Search-összekötő](data-factory-azure-search-connector.md#dataset-properties) című cikkben talál.

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Azure Cognitive Search index fogadó a másolási tevékenységben
Ha Adatmásolást végez egy keresési indexbe, állítsa a másolási tevékenység fogadó **típusát** **AzureSearchIndexSink** értékre, és adja meg a következő tulajdonságokat a **fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Meghatározza, hogy a rendszer egyesítse vagy lecserélje, ha már létezik dokumentum az indexben. | Egyesítés (alapértelmezett)<br/>Feltöltés| No |
| WriteBatchSize | Adatok feltöltése a keresési indexbe, ha a puffer mérete eléri a writeBatchSize. | 1 – 1 000. Az alapértelmezett érték a 1000. | No |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
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
        }]
    }
}
```

További információt az [Azure Cognitive Search-összekötő](data-factory-azure-search-connector.md#copy-activity-properties) című cikkben talál.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Társított szolgáltatások
A társított szolgáltatások két típusa létezik: az Azure Storage társított szolgáltatás és az Azure Storage SAS társított szolgáltatása.

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Ha az Azure Storage-fiókot egy adatgyárhoz szeretné kapcsolni a **fiók kulcsa** segítségével, hozzon létre egy Azure Storage-beli társított szolgáltatást. Egy Azure Storage-beli társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **AzureStorage** értékre. Ezután megadhatja a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot a következőre kell beállítani: **AzureStorage** |Yes |
| connectionString |Itt adhatja meg az Azure Storage-hoz a connectionString tulajdonsághoz való kapcsolódáshoz szükséges adatokat. |Yes |

**Példa**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS társított szolgáltatás
Az Azure Storage SAS társított szolgáltatása lehetővé teszi egy Azure Storage-fiók összekapcsolását egy Azure-beli adatgyárhoz egy közös hozzáférési aláírás (SAS) használatával. Az adat-előállítót a tárolóban lévő összes/meghatározott erőforrás (blob/tároló) számára korlátozott/időhöz kötött hozzáféréssel biztosítja. Ha közös hozzáférésű aláírással szeretné összekapcsolni az Azure Storage-fiókot egy adatgyárhoz, hozzon létre egy Azure Storage SAS-beli társított szolgáltatást. Az Azure Storage SAS társított szolgáltatásának definiálásához állítsa be a társított szolgáltatás **típusát** a **AzureStorageSas** értékre. Ezután megadhatja a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot a következőre kell beállítani: **AzureStorageSas** |Yes |
| sasUri |Az Azure Storage-erőforrásokhoz, például a blobhoz, a tárolóhoz vagy a táblához válassza a közös hozzáférési aláírás URI-JÁT. |Yes |

**Példa**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

További információ ezekről a társított szolgáltatásokról: [Azure Table Storage-összekötő](data-factory-azure-table-connector.md#linked-service-properties) című cikk.

### <a name="dataset"></a>Adathalmaz
Egy Azure Table adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **AzureTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Azon tábla neve, amely a társított szolgáltatás által hivatkozott Azure Table Database-példányban található. |Igen. Ha egy Táblanév azureTableSourceQuery nélkül van megadva, a rendszer a táblából származó összes rekordot átmásolja a célhelyre. Ha a azureTableSourceQuery is meg van adva, a rendszer a lekérdezésnek megfelelő rekordokat a célhelyre másolja. |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ ezekről a társított szolgáltatásokról: [Azure Table Storage-összekötő](data-factory-azure-table-connector.md#dataset-properties) című cikk.

### <a name="azure-table-source-in-copy-activity"></a>Azure Table forrás a másolási tevékenységben
Ha az Azure Table Storageból másol Adatmásolást, állítsa **be a** másolási tevékenység **AzureTableSource**, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| azureTableSourceQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |Azure Table lekérdezési karakterlánc. Tekintse meg a példákat a következő szakaszban. |Nem. Ha egy Táblanév azureTableSourceQuery nélkül van megadva, a rendszer a táblából származó összes rekordot átmásolja a célhelyre. Ha a azureTableSourceQuery is meg van adva, a rendszer a lekérdezésnek megfelelő rekordokat a célhelyre másolja. |
| azureTableSourceIgnoreTableNotFound |Jelezze, hogy nem létezik-e a lenyelés a tábla kivételével. |IGAZ<br/>HAMIS |No |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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
        }]
    }
}
```

További információ ezekről a társított szolgáltatásokról: [Azure Table Storage-összekötő](data-factory-azure-table-connector.md#copy-activity-properties) című cikk.

### <a name="azure-table-sink-in-copy-activity"></a>Azure Table mosogató a másolási tevékenységben
Ha Adatmásolást végez az Azure Table Storageba, állítsa a másolási tevékenység fogadó **típusát** a **AzureTableSink** értékre, és adja meg a következő **tulajdonságokat a fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |A fogadó által használható alapértelmezett partíciós kulcs értéke. |Egy karakterlánc-érték. |No |
| azureTablePartitionKeyName |Adja meg annak az oszlopnak a nevét, amelynek értékeit partíciós kulcsként használja a rendszer. Ha nincs megadva, a rendszer a AzureTableDefaultPartitionKeyValue használja a partíciós kulcsként. |Egy oszlop neve. |No |
| azureTableRowKeyName |Adja meg annak az oszlopnak a nevét, amelynek az oszlop értékeit a rendszer a sor kulcsaként használja. Ha nincs megadva, használja az egyes sorok GUID azonosítóját. |Egy oszlop neve. |No |
| azureTableInsertType |Az adatgyűjtés módja az Azure Table-be.<br/><br/>Ez a tulajdonság azt szabályozza, hogy a kimeneti táblában található, egyező partícióval és sorokkal rendelkező meglévő sorok felülírják vagy összevonták-e az értékeket. <br/><br/>Ha szeretné megtudni, hogyan működnek ezek a beállítások (egyesítés és csere), tekintse meg az [entitás beszúrása és egyesítése](/rest/api/storageservices/Insert-Or-Merge-Entity) , illetve az [entitások beszúrása vagy cseréje](/rest/api/storageservices/Insert-Or-Replace-Entity) témaköröket. <br/><br> Ez a beállítás a sor szintjére vonatkozik, nem a tábla szintjére, és egyik sem törli a bemeneti tábla azon sorait, amelyek nem szerepelnek a bemenetben. |egyesítés (alapértelmezett)<br/>csere |No |
| writeBatchSize |Beilleszti az adatbevitelt az Azure-táblába, amikor a writeBatchSize vagy a writeBatchTimeout találat. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Beilleszti az adatbevitelt az Azure-táblába a writeBatchSize vagy a writeBatchTimeout találatakor |időtartomány<br/><br/>Például: "00:20:00" (20 perc) |Nem (alapértelmezett érték a Storage-ügyfél alapértelmezett időtúllépési értéke 90 mp) |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
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
        }]
    }
}
```
További információ ezekről a társított szolgáltatásokról: [Azure Table Storage-összekötő](data-factory-azure-table-connector.md#copy-activity-properties) című cikk.

## <a name="amazon-redshift"></a>Amazon vöröseltolódása

### <a name="linked-service"></a>Társított szolgáltatások
Az Amazon vöröseltolódás társított szolgáltatásának definiálásához állítsa a társított szolgáltatás **típusát** **AmazonRedshift** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |Az Amazon Vöröseltolódási kiszolgáló IP-címe vagy állomásneve. |Yes |
| port |Az Amazon vöröseltolódás-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port száma. |Nem, alapértelmezett érték: 5439 |
| adatbázis |Az Amazon vöröseltolódás-adatbázis neve. |Yes |
| username |Az adatbázishoz hozzáférő felhasználó neve. |Yes |
| jelszó |A felhasználói fiók jelszava. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

További információ: Amazon vöröseltolódás-összekötő.

### <a name="dataset"></a>Adathalmaz
Amazon Vöröseltolódási adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **RelationalTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a táblának a neve az Amazon vöröseltolódás-adatbázisban, amelyhez a társított szolgáltatás hivatkozik. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** ) |


#### <a name="example"></a>Példa

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
További információ: Amazon vöröseltolódás-összekötő.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha az adatok másolása az Amazon Vöröseltolódásból történik, állítsa **a** másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
További információ: Amazon vöröseltolódás-összekötő.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Társított szolgáltatások
Az IBM DB2 társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **OnPremisesDB2** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A DB2-kiszolgáló neve. |Yes |
| adatbázis |A DB2-adatbázis neve. |Yes |
| schema |A séma neve az adatbázisban. A séma neve megkülönbözteti a kis-és nagybetűket. |No |
| authenticationType |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni DB2-adatbázishoz való kapcsolódáshoz. |Yes |

#### <a name="example"></a>Példa
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
További információ: az IBM DB2-összekötő cikke.

### <a name="dataset"></a>Adathalmaz
Egy DB2-adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **RelationalTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a DB2-adatbázis-példánynak a neve, amelyhez a társított szolgáltatás hivatkozik. A táblanév megkülönbözteti a kis-és nagybetűket. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** )

#### <a name="example"></a>Példa
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: az IBM DB2-összekötő cikke.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha az IBM DB2-ből másol Adatmásolást, állítsa **be a** másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `"query": "select * from "MySchema"."MyTable""`. |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |

#### <a name="example"></a>Példa
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
További információ: az IBM DB2-összekötő cikke.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Társított szolgáltatások
A MySQL-hez társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **OnPremisesMySql** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A MySQL-kiszolgáló neve. |Yes |
| adatbázis |A MySQL-adatbázis neve. |Yes |
| schema |A séma neve az adatbázisban. |No |
| authenticationType |A MySQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: `Basic` . |Yes |
| userName (Felhasználónév) |Adja meg a MySQL-adatbázishoz való kapcsolódáshoz használandó felhasználónevet. |Yes |
| jelszó |Adja meg a megadott felhasználói fiók jelszavát. |Yes |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni MySQL-adatbázishoz való kapcsolódáshoz. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

További információ: MySQL- [összekötő](data-factory-onprem-mysql-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
A MySQL-adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **RelationalTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a MySQL-adatbázis-példánynak a neve, amelyhez a társított szolgáltatás hivatkozik. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
További információ: MySQL- [összekötő](data-factory-onprem-mysql-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha MySQL-adatbázisból másol Adatmásolást, állítsa be a másolási tevékenység **RelationalSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |


#### <a name="example"></a>Példa
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információ: MySQL- [összekötő](data-factory-onprem-mysql-connector.md#copy-activity-properties) cikk.

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Társított szolgáltatások
Egy Oracle-alapú társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **OnPremisesOracle** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| driverType | Itt adhatja meg, hogy melyik illesztőprogramot kell használnia az adatok Oracle Databaseba való másolásához. Az engedélyezett értékek a következők: **Microsoft** vagy **ODP** (alapértelmezett). Lásd: a támogatott verzió és telepítés szakasz az illesztőprogram részleteiben. | No |
| connectionString | A connectionString tulajdonsághoz Oracle Database-példányhoz való kapcsolódáshoz szükséges adatok megadása. | Yes |
| Átjáró neve | A helyszíni Oracle-kiszolgálóhoz való kapcsolódáshoz használt átjáró neve |Yes |

#### <a name="example"></a>Példa
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

További információ: [Oracle Connector](data-factory-onprem-oracle-connector.md#linked-service-properties) -cikk.

### <a name="dataset"></a>Adathalmaz
Egy Oracle-adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **OracleTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a táblának a neve, amelyre a társított szolgáltatás hivatkozik Oracle Database. |Nem (ha meg van adva a **OracleSource** **oracleReaderQuery** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
További információ: [Oracle Connector](data-factory-onprem-oracle-connector.md#dataset-properties) -cikk.

### <a name="oracle-source-in-copy-activity"></a>Oracle-forrás a másolási tevékenységben
Ha Oracle-adatbázisból másol Adatmásolást, állítsa be a másolási tevékenység **OracleSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| oracleReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Például: `select * from MyTable` <br/><br/>Ha nincs megadva, a futtatott SQL-utasítás: `select * from MyTable` |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

További információ: [Oracle Connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) -cikk.

### <a name="oracle-sink-in-copy-activity"></a>Oracle-fogadó a másolási tevékenységben
Ha az adatok másolása az Oracle Database **-be történik, állítsa a** másolási tevékenység **OracleSink**, és adja meg a következő tulajdonságokat a **fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: 00:30:00 (30 perc). |No |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |A másolási tevékenységre vonatkozó lekérdezés megadása úgy, hogy egy adott szeletből származó adatmennyiséget takarítson meg. |Egy lekérdezési utasítás. |No |
| sliceIdentifierColumnName |Adja meg az oszlop nevét a másolási tevékenységhez, amely automatikusan generált szelet-azonosítóval egészül ki, amely egy adott szelet adatának az újrafuttatáskor való kitakarítására szolgál. |A bináris adattípusú oszlop neve (32). |No |

#### <a name="example"></a>Példa
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
További információ: [Oracle Connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) -cikk.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Társított szolgáltatások
A PostgreSQL-hez társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **OnPremisesPostgreSql** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A PostgreSQL-kiszolgáló neve. |Yes |
| adatbázis |A PostgreSQL-adatbázis neve. |Yes |
| schema |A séma neve az adatbázisban. A séma neve megkülönbözteti a kis-és nagybetűket. |No |
| authenticationType |A PostgreSQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni PostgreSQL-adatbázishoz való kapcsolódáshoz. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
További információ: PostgreSQL- [összekötő](data-factory-onprem-postgresql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Adathalmaz
A PostgreSQL-adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **RelationalTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a PostgreSQL-adatbázisnak a neve, amelyre a társított szolgáltatás hivatkozik. A táblanév megkülönbözteti a kis-és nagybetűket. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** ) |

#### <a name="example"></a>Példa
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
További információ: PostgreSQL- [összekötő](data-factory-onprem-postgresql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha egy PostgreSQL-adatbázisból másol adatait, állítsa a másolási tevékenység **RelationalSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: "lekérdezés": "select * from \" MySchema" \" . \" Sajáttábla \" ". |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információ: PostgreSQL- [összekötő](data-factory-onprem-postgresql-connector.md#copy-activity-properties) .

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Társított szolgáltatások
Az SAP Business Warehouse (BW) társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **SapBw** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | sztring | Yes
systemNumber | A SAP BWrendszer rendszerszáma. | String típusú kétszámjegyű decimális szám. | Yes
ügyfél-azonosító | A-ügyfél ügyfél-azonosítója az SAP W rendszeren. | Karakterláncként megjelenített háromjegyű decimális szám. | Yes
username | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Yes
jelszó | A felhasználó jelszava. | sztring | Yes
Átjáró neve | Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni SAP BW-példányhoz való kapcsolódáshoz. | sztring | Yes
encryptedCredential | A titkosított hitelesítő adat karakterlánca. | sztring | No

#### <a name="example"></a>Példa

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

További információ: az [SAP Business Warehouse-összekötő](data-factory-sap-business-warehouse-connector.md#linked-service-properties) cikke.

### <a name="dataset"></a>Adathalmaz
SAP BW adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **RelationalTable** értékre. Nincs olyan Type-specifikus tulajdonság, amely a **RelationalTable** típusú SAP BW adatkészlet esetében nem támogatott.

#### <a name="example"></a>Példa

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
További információ: az [SAP Business Warehouse-összekötő](data-factory-sap-business-warehouse-connector.md#dataset-properties) cikke.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha az SAP Business Warehouse-ból másol Adatmásolást, állítsa **be a** másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés | Megadja az MDX-lekérdezést, amely beolvassa az SAP BW-példány adatait. | MDX-lekérdezés. | Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

További információ: az [SAP Business Warehouse-összekötő](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) cikke.

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Társított szolgáltatások
SAP HANA társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **SapHana** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP HANA-példány található. Ha a kiszolgáló testreszabott portot használ, adja meg a t `server:port` . | sztring | Yes
authenticationType | A hitelesítés típusa. | karakterlánc. "Alapszintű" vagy "Windows" | Yes
username | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Yes
jelszó | A felhasználó jelszava. | sztring | Yes
Átjáró neve | Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni SAP HANA-példányhoz való kapcsolódáshoz. | sztring | Yes
encryptedCredential | A titkosított hitelesítő adat karakterlánca. | sztring | No

#### <a name="example"></a>Példa

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
További információ: SAP HANA- [összekötő](data-factory-sap-hana-connector.md#linked-service-properties) cikke.

### <a name="dataset"></a>Adathalmaz
SAP HANA adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **RelationalTable** értékre. Nincs olyan Type-specifikus tulajdonság, amely a **RelationalTable** típusú SAP HANA adatkészlet esetében nem támogatott.

#### <a name="example"></a>Példa

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
További információ: SAP HANA- [összekötő](data-factory-sap-hana-connector.md#dataset-properties) cikke.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha SAP HANA adattárból másol Adatmásolást, állítsa be a másolási tevékenység **RelationalSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés | Azt az SQL-lekérdezést adja meg, amely az SAP HANA-példányból olvassa be az adatok beolvasását. | SQL-lekérdezés. | Yes |


#### <a name="example"></a>Példa


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

További információ: SAP HANA- [összekötő](data-factory-sap-hana-connector.md#copy-activity-properties) cikke.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Társított szolgáltatások
Hozzon létre egy **OnPremisesSqlServer** típusú társított szolgáltatást egy SQL Server adatbázis egy adatgyárhoz való csatolásához. A következő táblázat a SQL Server társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

A következő táblázat a SQL Server társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesSqlServer**. |Yes |
| connectionString |Az SQL-hitelesítés vagy a Windows-hitelesítés használatával a SQL Server-adatbázishoz való kapcsolódáshoz szükséges connectionString-adatokat adjon meg. |Yes |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a SQL Server adatbázishoz való kapcsolódáshoz. |Yes |
| username |Windows-hitelesítés használata esetén adja meg a felhasználónevet. Példa: **tartománynév \\ Felhasználónév**. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |

A **New-AzDataFactoryEncryptValue** parancsmaggal titkosíthatja a hitelesítő adatokat, és a következő példában látható módon használhatja azokat a kapcsolatok karakterláncában (**EncryptedCredential** tulajdonság):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Példa: JSON az SQL-hitelesítés használatához

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Példa: JSON a Windows-hitelesítés használatához

Ha a Felhasználónév és a jelszó meg van adva, az átjáró a megadott felhasználói fiók megszemélyesítésére használja a SQL Server adatbázishoz való csatlakozáshoz. Ellenkező esetben az átjáró közvetlenül csatlakozik a SQL Serverhoz az átjáró biztonsági környezetével (az indítási fiókkal).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

További információ: SQL Server- [összekötő](data-factory-sqlserver-connector.md#linked-service-properties) cikke.

### <a name="dataset"></a>Adathalmaz
SQL Server adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **SqlServerTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a táblának vagy nézetnek a neve, amely a társított szolgáltatás által hivatkozott SQL Server adatbázis-példányban található. |Yes |

#### <a name="example"></a>Példa
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: SQL Server- [összekötő](data-factory-sqlserver-connector.md#dataset-properties) cikke.

### <a name="sql-source-in-copy-activity"></a>SQL-forrás a másolási tevékenységben
Ha SQL Server adatbázisból másol Adatmásolást, állítsa be a másolási tevékenység **SqlSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. A bemeneti adatkészlet által hivatkozott adatbázisból több táblát is hivatkozhat. Ha nincs megadva, a futtatott SQL-utasítás: válasszon a Sajáttábla közül. |No |
| sqlReaderStoredProcedureName |Azon tárolt eljárás neve, amely beolvassa az adatokat a forrás táblából. |A tárolt eljárás neve. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |

Ha a **sqlReaderQuery** meg van adva a SqlSource, a másolási tevékenység futtatja ezt a lekérdezést a SQL Server adatbázis-forráson az adatkéréshez.

Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName-t, a struktúra szakaszban definiált oszlopok a SQL Server adatbázison futtatandó választó lekérdezés összeállítására szolgálnak. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.

> [!NOTE]
> A **sqlReaderStoredProcedureName** használatakor továbbra is meg kell adnia a **Táblanév** tulajdonság értékét az adatkészlet JSON-fájljában. Erre a táblára vonatkozóan nem történt érvényesítés.


#### <a name="example"></a>Példa
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

Ebben a példában a SqlSource **sqlReaderQuery** van megadva. A másolási tevékenység ezt a lekérdezést a SQL Server adatbázis-forráson futtatja, hogy lekérje az adatgyűjtést. Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik). A sqlReaderQuery hivatkozhat több táblára a bemeneti adatkészlet által hivatkozott adatbázison belül. Nem csak az adatkészlet táblanév-typeProperty beállított táblára korlátozódik.

Ha nem ad meg sqlReaderQuery-vagy sqlReaderStoredProcedureName-t, a struktúra szakaszban definiált oszlopok a SQL Server adatbázison futtatandó választó lekérdezés létrehozásához használatosak. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.

További információ: SQL Server- [összekötő](data-factory-sqlserver-connector.md#copy-activity-properties) cikke.

### <a name="sql-sink-in-copy-activity"></a>SQL-fogadó a másolási tevékenységben
Ha az adatok másolása egy SQL Server adatbázisba történik, állítsa a másolási tevékenység **SqlSink**, és adja **meg a következő** tulajdonságokat a **fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: "00:30:00" (30 perc). |No |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |A másolási tevékenységhez tartozó lekérdezés megadása úgy, hogy az egy adott szeletből származó adatmennyiséget takarítson meg. További információ: ismételhetőség szakasz. |Egy lekérdezési utasítás. |No |
| sliceIdentifierColumnName |Adja meg az oszlop nevét a másolási tevékenységhez, amely automatikusan generált szelet-azonosítóval egészül ki, amely egy adott szelet adatának az újrafuttatáskor való kitakarítására szolgál. További információ: ismételhetőség szakasz. |A bináris adattípusú oszlop neve (32). |No |
| sqlWriterStoredProcedureName |Annak a tárolt eljárásnak a neve, amely a upsert (frissítések/beszúrások) a célként megadott táblába kerül. |A tárolt eljárás neve. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |
| sqlWriterTableType |Adja meg a tárolt eljárásban használni kívánt táblanév nevét. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a meglévő adattal másolható adatmásolási műveleteket. |Egy tábla típusának neve. |No |

#### <a name="example"></a>Példa
A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **BlobSource** értékre van állítva, a **fogadó típusa** pedig **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
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
        }]
    }
}
```

További információ: SQL Server- [összekötő](data-factory-sqlserver-connector.md#copy-activity-properties) cikke.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Társított szolgáltatások
Egy Sybase-társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **OnPremisesSybase** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A Sybase-kiszolgáló neve. |Yes |
| adatbázis |A Sybase-adatbázis neve. |Yes |
| schema |A séma neve az adatbázisban. |No |
| authenticationType |A Sybase-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni Sybase-adatbázishoz való kapcsolódáshoz. |Yes |

#### <a name="example"></a>Példa
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

További információ: Sybase- [összekötő](data-factory-onprem-sybase-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
A Sybase-adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **RelationalTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a táblának a neve, amelyre a társított szolgáltatás hivatkozik a Sybase-adatbázis példányában. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: Sybase- [összekötő](data-factory-onprem-sybase-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha egy Sybase-adatbázisból másol adatait, állítsa a másolási tevékenység **RelationalSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információ: Sybase- [összekötő](data-factory-onprem-sybase-connector.md#copy-activity-properties) cikk.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Társított szolgáltatások
Teradata társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **OnPremisesTeradata** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A Teradata-kiszolgáló neve. |Yes |
| authenticationType |A Teradata-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni Teradata-adatbázishoz való kapcsolódáshoz. |Yes |

#### <a name="example"></a>Példa
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

További információ: Teradata- [összekötő](data-factory-onprem-teradata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Adathalmaz
A Teradata blob-adatkészlet definiálásához állítsa az adatkészlet **típusát** **RelationalTable** értékre. Jelenleg nem támogatottak a Teradata adatkészlet típusának tulajdonságai.

#### <a name="example"></a>Példa
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: Teradata- [összekötő](data-factory-onprem-teradata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha Teradata-adatbázisból másol Adatmásolást, állítsa a másolási tevékenység **forrás típusát** **RelationalSource** értékre, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

További információ: Teradata- [összekötő](data-factory-onprem-teradata-connector.md#copy-activity-properties) .

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Társított szolgáltatások
A Cassandra társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **OnPremisesCassandra** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| gazda |A Cassandra-kiszolgálók egy vagy több IP-címe vagy állomásneve.<br/><br/>Megadhatja az IP-címek vagy állomásnevek vesszővel tagolt listáját, hogy az összes kiszolgálóhoz egyszerre kapcsolódjon. |Yes |
| port |A Cassandra-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem, alapértelmezett érték: 9042 |
| authenticationType |Alapszintű vagy névtelen |Yes |
| username |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha a authenticationType értéke alapszintű. |
| jelszó |A felhasználói fiók jelszavának megadása. |Igen, ha a authenticationType értéke alapszintű. |
| Átjáró neve |A helyszíni Cassandra-adatbázishoz való kapcsolódáshoz használt átjáró neve. |Yes |
| encryptedCredential |Az átjáró által titkosított hitelesítő adat. |No |

#### <a name="example"></a>Példa

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információ: Cassandra- [összekötő](data-factory-onprem-cassandra-connector.md#linked-service-properties) .

### <a name="dataset"></a>Adathalmaz
A Cassandra-adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **CassandraTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kulcstartomány |A térköz vagy séma neve a Cassandra adatbázisban. |Igen (ha a **CassandraSource** - **lekérdezés** nincs definiálva). |
| tableName |A tábla neve a Cassandra adatbázisban. |Igen (ha a **CassandraSource** - **lekérdezés** nincs definiálva). |

#### <a name="example"></a>Példa

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: Cassandra- [összekötő](data-factory-onprem-cassandra-connector.md#dataset-properties) .

### <a name="cassandra-source-in-copy-activity"></a>Cassandra-forrás a másolási tevékenységben
Ha a Cassandra-ből másol Adatmásolást, állítsa **be a** másolási tevékenység **CassandraSource**, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-92 lekérdezés vagy CQL-lekérdezés. Lásd: [CQL-hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használatakor adja meg a **térköz nevét. a tábla neve** , amely a lekérdezni kívánt táblát jelöli. |Nem (ha meg van adva a táblanév és a szóköz az adatkészleten). |
| consistencyLevel |A konzisztencia szintje határozza meg, hogy hány replikának kell válaszolnia egy olvasási kérelemre, mielőtt adatvisszaad az ügyfélalkalmazás számára. Cassandra ellenőrzi a megadott számú replikát az adatolvasási kérelem teljesítéséhez. |EGY, KETTŐ, HÁROM, KVÓRUM, MIND, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. További részletek: az [adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Nem. Az alapértelmezett érték egy. |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
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
        }]
    }
}
```

További információ: Cassandra- [összekötő](data-factory-onprem-cassandra-connector.md#copy-activity-properties) .

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Társított szolgáltatások
MongoDB társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **OnPremisesMongoDB** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A MongoDB-kiszolgáló IP-címe vagy állomásneve. |Yes |
| port |A MongoDB-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem kötelező, alapértelmezett érték: 27017 |
| authenticationType |Alapszintű vagy névtelen. |Yes |
| username |Felhasználói fiók a MongoDB eléréséhez. |Igen (ha alapszintű hitelesítést használ). |
| jelszó |A felhasználó jelszava. |Igen (ha alapszintű hitelesítést használ). |
| authSource |Annak a MongoDB-adatbázisnak a neve, amelyet a hitelesítés hitelesítő adatainak ellenőrzéséhez használni kíván. |Nem kötelező (ha alapszintű hitelesítést használ). alapértelmezett: a databaseName tulajdonsággal megadott rendszergazdai fiókot és adatbázist használja. |
| databaseName |Az elérni kívánt MongoDB-adatbázis neve. |Yes |
| Átjáró neve |Azon átjáró neve, amely hozzáfér az adattárhoz. |Yes |
| encryptedCredential |Az átjáró által titkosított hitelesítő adat. |Választható |

#### <a name="example"></a>Példa

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információ: MongoDB- [összekötői cikk](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Adathalmaz
MongoDB adatkészlet definiálásához állítsa az adatkészlet **típusát** **MongoDbCollection** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| collectionName |A gyűjtemény neve a MongoDB adatbázisban. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

További információ: MongoDB- [összekötői cikk](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB forrása a másolási tevékenységben
Ha az adatok másolása a MongoDB-ból történik, állítsa a másolási tevékenység **forrás típusát** **MongoDbSource** értékre, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-92 lekérdezési karakterlánc. Példa: `select * from MyTable`. |Nem (ha meg van adva az **adatkészlet** **collectionName** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információ: MongoDB- [összekötői cikk](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Társított szolgáltatások
Egy Amazon S3-beli társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **awsaccesskey használnia** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| accessKeyID |A titkos elérési kulcs azonosítója. |sztring |Yes |
| secretAccessKey |Maga a titkos elérési kulcs. |Titkosított titkos karakterlánc |Yes |

#### <a name="example"></a>Példa
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

További információkért lásd az [Amazon S3-összekötőt ismertető cikket](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Adathalmaz
Az Amazon S3-adatkészletek definiálásához állítsa az adatkészlet **típusát** a **AmazonS3** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| bucketName |Az S3-gyűjtő neve. |Sztring |Yes |
| kulcs |Az S3-objektum kulcsa. |Sztring |No |
| előtag |Az S3-objektum kulcsának előtagja. Azok az objektumok, amelyek esetében ezzel az előtaggal kezdődnek a kulcsok. Csak akkor érvényes, ha a kulcs üres. |Sztring |No |
| version |Az S3-verzió verziója, ha az S3 Verziószámozás engedélyezve van. |Sztring |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No | |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No | |


> [!NOTE]
> a bucketName + Key megadja annak az S3-objektumnak a helyét, ahol a gyűjtő az S3-objektumok legfelső szintű tárolója, a kulcs pedig az S3 objektum teljes elérési útja.

#### <a name="example-sample-dataset-with-prefix"></a>Példa: minta adatkészlet előtaggal

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Példa: minta adatkészlet (verzióval)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Példa: dinamikus elérési utak az S3-hoz
A mintában a kulcs-és bucketName tulajdonságok rögzített értékeit használjuk az Amazon S3-adatkészletben.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Data Factory a kulcsot és a bucketName dinamikusan kiszámíthatja futásidőben a rendszerváltozók, például a SliceStart használatával.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Ugyanezt megteheti az Amazon S3-adatkészlethez tartozó előtag tulajdonságnál is. A támogatott függvények és változók listáját [Data Factory függvények és rendszerváltozók](data-factory-functions-variables.md) című részben tekintheti meg.

További információkért lásd az [Amazon S3-összekötőt ismertető cikket](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása másolási tevékenységben
Ha az Amazon S3-ból másol Adatmásolást, állítsa **be a** másolási tevékenység **FileSystemSource**, és adja meg a következő tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Megadja, hogy a címtárban az S3 objektumok rekurzív listázása megtörténjen-e. |Igaz/hamis |No |


#### <a name="example"></a>Példa


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

További információkért lásd az [Amazon S3-összekötőt ismertető cikket](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Fájlrendszer


### <a name="linked-service"></a>Társított szolgáltatások
Helyszíni fájlrendszert kapcsolhat egy Azure-beli adatgyárhoz a helyszíni **fájlkiszolgáló** társított szolgáltatásával. A következő táblázat a helyszíni fájlkiszolgáló társított szolgáltatásához tartozó JSON-elemek leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |Győződjön meg arról, hogy a Type tulajdonság értéke **OnPremisesFileServer**. |Yes |
| gazda |Megadja a másolni kívánt mappa gyökerének elérési útját. A karakterláncban a speciális karaktereknél használja a Escape karaktert. Példákat a következő témakörben talál: példa társított szolgáltatás és adatkészlet-definíciók. |Yes |
| userid |Itt adhatja meg annak a felhasználónak az AZONOSÍTÓját, aki hozzáfér a kiszolgálóhoz. |Nem (ha a encryptedCredential választja) |
| jelszó |A felhasználó jelszavának megadása (userid). |Nem (ha a encryptedCredential választja |
| encryptedCredential |A New-AzDataFactoryEncryptValue parancsmag futtatásával adhatja meg a titkosított hitelesítő adatokat. |Nem (ha úgy dönt, hogy egyszerű szövegként adja meg a felhasználói azonosítót és a jelszót) |
| Átjáró neve |Annak az átjárónak a nevét adja meg, amelyet a Data Factory kell használnia a helyszíni fájlkiszolgálón való kapcsolódáshoz. |Yes |

#### <a name="sample-folder-path-definitions"></a>A minta mappa elérési útjának definíciói

| Eset | Gazdagép a társított szolgáltatás definíciójában | folderPath az adatkészlet definíciójában |
| --- | --- | --- |
| Helyi mappa adatkezelés átjáró számítógépén: <br/><br/>Példák: D: \\ \* vagy D:\folder\subfolder\\* |D: \\ \\ (adatkezelés átjáró 2,0-es és újabb verziói esetén) <br/><br/> localhost (a adatkezelés Gateway 2,0-es verziójánál korábbi verziók esetében) |.\\\\ vagy mappa \\ \\ almappája (adatkezelés átjáró 2,0-es és újabb verzióihoz) <br/><br/>D: \\ \\ vagy d: \\ \\ mappa \\ \\ almappája (az átjáró 2,0-es verziójához) |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\ MyServer \\ Share \\ \* vagy \\ \\ MyServer \\ Share \\ mappa \\ almappája\\* |\\\\\\\\MyServer- \\ \\ megosztás |.\\\\ vagy mappa \\ \\ almappája |


#### <a name="example-using-username-and-password-in-plain-text"></a>Példa: Felhasználónév és jelszó használata egyszerű szövegben

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Példa: a encryptedcredential használata

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információ: fájlrendszer- [összekötő cikk](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Adathalmaz
Fájlrendszerbeli adatkészlet definiálásához állítsa az adatkészlet **típusát** a **fájlmegosztás** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |Megadja a mappa alelérési útját. A karakterláncban a speciális karaktereknél használja a Escape karaktert. Példákat a következő témakörben talál: példa társított szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja a **partitionBy** , hogy a mappa elérési útjai a szelet kezdő/befejező dátum-és időpontjain alapulnak. |Yes |
| fileName |Adja meg a fájl nevét a **folderPath** , ha azt szeretné, hogy a tábla egy adott fájlra hivatkozzon a mappában. Ha nem ad meg értéket ehhez a tulajdonsághoz, a tábla a mappában található összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a generált fájl neve a következő formátumú: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Adja meg azt a szűrőt, amelyet a folderPath található fájlok részhalmazának kiválasztására kíván használni az összes fájl helyett. <br/><br/>Az engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: "fileFilter": "*. log"<br/>2. példa: "fileFilter": 2016-1-?. txt<br/><br/>Vegye figyelembe, hogy a fileFilter egy bemeneti fájlmegosztás adatkészlet esetében alkalmazható. |No |
| partitionedBy |A partitionedBy segítségével megadhatja az idősorozat-adatsorok dinamikus folderPath/fájlnevét. Egy példa a folderPath paramétert minden órában. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**; a támogatott szintek a következők: **optimális** és **leggyorsabb**. lásd [a Azure Data Factory fájl-és tömörítési formátumait](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> A fájlnév és a fileFilter egyidejű használata nem lehetséges.

#### <a name="example"></a>Példa

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: fájlrendszer- [összekötő cikk](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása másolási tevékenységben
Ha fájlrendszerből másol Adatmásolást, állítsa be a másolási tevékenység **FileSystemSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. |Igaz, hamis (alapértelmezett) |No |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
        }]
    }
}
```
További információ: fájlrendszer- [összekötő cikk](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Fájlrendszer-fogadó a másolási tevékenységben
Ha Adatmásolást végez a fájlrendszerre, állítsa a másolási tevékenység fogadó **típusát** a **FileSystemSink** értékre, és adja meg a következő tulajdonságokat a **fogadó szakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior |Meghatározza a másolási viselkedést a forrás BlobSource vagy fájlrendszerének használatakor. |**PreserveHierarchy:** Megőrzi a fájl hierarchiáját a célmappában. Ez a forrásfájl és a forrásfájl relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><br/>**FlattenHierarchy:** A rendszer a forrás mappából származó összes fájlt a célmappa első szintjén hozza létre. A célfájl automatikusan létrehozott névvel lett létrehozva.<br/><br/>**MergeFiles:** Egyesít minden fájlt a forrás mappájából egy fájlba. Ha meg van adva a fájl neve/blob neve, az egyesített fájl neve a megadott név. Ellenkező esetben az automatikusan létrehozott fájlnév. |No |

automatikus

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

További információ: fájlrendszer- [összekötő cikk](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Társított szolgáltatások
Egy FTP-hez társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **FTP** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| gazda |Az FTP-kiszolgáló neve vagy IP-címe |Yes |&nbsp; |
| authenticationType |A hitelesítés típusának megadása |Yes |Alapszintű, névtelen |
| username |Az FTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |No |&nbsp; |
| jelszó |A felhasználó jelszava (username) |No |&nbsp; |
| encryptedCredential |Titkosított hitelesítő adatok az FTP-kiszolgáló eléréséhez |No |&nbsp; |
| Átjáró neve |A helyszíni FTP-kiszolgálóhoz való kapcsolódáshoz adatkezelés átjáró neve |No |&nbsp; |
| port |Az FTP-kiszolgálót figyelő port |No |21 |
| enableSsl |Adja meg, hogy SSL/TLS-csatornán keresztül használja-e az FTP-t |No |true |
| enableServerCertificateValidation |Adja meg, hogy engedélyezi-e a kiszolgáló TLS/SSL-tanúsítványának érvényesítését SSL/TLS-csatornán keresztüli FTP használata esetén |No |true |

#### <a name="example-using-anonymous-authentication"></a>Példa: Névtelen hitelesítés használata

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Példa: a Felhasználónév és a jelszó egyszerű szövegben való használata egyszerű hitelesítéshez

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Példa: port használata, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Példa: a encryptedCredential használata a hitelesítéshez és az átjáróhoz

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

További információ: FTP- [összekötő](data-factory-ftp-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Az FTP-adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **fájlmegosztás** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa alárendelt elérési útja. A karakterláncban a speciális karaktereknél használja a Escape karaktert. Példákat a következő témakörben talál: példa társított szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja a **partitionBy** , hogy a mappa elérési útjai a szelet kezdő/befejező dátum-és időpontjain alapulnak. |Yes
| fileName |Adja meg a fájl nevét a **folderPath** , ha azt szeretné, hogy a tábla egy adott fájlra hivatkozzon a mappában. Ha nem ad meg értéket ehhez a tulajdonsághoz, a tábla a mappában található összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumú lesz: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Adja meg azt a szűrőt, amelyet a folderPath található fájlok részhalmazának kiválasztására kíván használni az összes fájl helyett.<br/><br/>Az engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2016-1-?.txt"`<br/><br/> a fileFilter egy bemeneti fájlmegosztás adatkészlet esetében alkalmazható. Ez a tulajdonság nem támogatott a HDFS. |No |
| partitionedBy |a partitionedBy használható egy dinamikus folderPath, az idősorozat-adatfájlok fájlnevének megadására. Például a folderPath paramétert minden óra adatértéknél. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**; a támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli módot használja-e. A bináris mód és a hamis ASCII esetében igaz. Alapértelmezett érték: true (igaz). Ez a tulajdonság csak akkor használható, ha a társított társított szolgáltatás típusa: FTP. |No |

> [!NOTE]
> a filename és a fileFilter nem használható egyszerre.

#### <a name="example"></a>Példa

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

További információ: FTP- [összekötő](data-factory-ftp-connector.md#dataset-properties) cikk.

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása másolási tevékenységben
Ha az adatok másolása FTP-kiszolgálóról történik, állítsa a másolási tevékenység **FileSystemSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. |Igaz, hamis (alapértelmezett) |No |

#### <a name="example"></a>Példa

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

További információ: FTP- [összekötő](data-factory-ftp-connector.md#copy-activity-properties) cikk.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Társított szolgáltatások
HDFS társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **HDFS** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **Hdfs** |Yes |
| URL-cím |A HDFS URL-címe |Yes |
| authenticationType |Névtelen vagy Windows. <br><br> Ha **Kerberos-hitelesítést** kíván használni a HDFS-összekötőhöz, tekintse meg ezt a szakaszt, és ennek megfelelően állítsa be a helyszíni környezetet. |Yes |
| userName (Felhasználónév) |Felhasználónév a Windows-hitelesítéshez. |Igen (Windows-hitelesítéshez) |
| jelszó |Jelszó a Windows-hitelesítéshez. |Igen (Windows-hitelesítéshez) |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a HDFS való kapcsolódáshoz. |Yes |
| encryptedCredential |[Új –](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) a hozzáférési hitelesítő adat AzDataFactoryEncryptValue kimenete. |No |

#### <a name="example-using-anonymous-authentication"></a>Példa: Névtelen hitelesítés használata

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Példa: Windows-hitelesítés használata

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információ: HDFS-összekötő.

### <a name="dataset"></a>Adathalmaz
HDFS adatkészlet definiálásához állítsa az adatkészlet **típusát** **fájlmegosztás** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa elérési útja. Például: `myfolder`<br/><br/>A karakterláncban a speciális karaktereknél használja a Escape karaktert. Például: a folder\subfolder esetében a mappa \\ \\ almappáját és a d:\samplefolder megadása esetén a d: samplefolder-t kell megadnia \\ \\ .<br/><br/>Ezt a tulajdonságot kombinálhatja a **partitionBy** , hogy a mappa elérési útjai a szelet kezdő/befejező dátum-és időpontjain alapulnak. |Yes |
| fileName |Adja meg a fájl nevét a **folderPath** , ha azt szeretné, hogy a tábla egy adott fájlra hivatkozzon a mappában. Ha nem ad meg értéket ehhez a tulajdonsághoz, a tábla a mappában található összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumú lesz: <br/><br/>`Data.<Guid>.txt` (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |a partitionedBy használható egy dinamikus folderPath, az idősorozat-adatfájlok fájlnevének megadására. Példa: az folderPath paramétert minden óra adatértékhez. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> a filename és a fileFilter nem használható egyszerre.

#### <a name="example"></a>Példa

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

További információ: HDFS-összekötő.

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása másolási tevékenységben
Ha az adatok másolása a HDFS-ból történik, állítsa a másolási tevékenység **forrás típusát** **FileSystemSource** értékre, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

A **FileSystemSource** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. |Igaz, hamis (alapértelmezett) |No |

#### <a name="example"></a>Példa

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információ: HDFS-összekötő.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Társított szolgáltatások
Egy SFTP-hez társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **SFTP** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| gazda | Az SFTP-kiszolgáló neve vagy IP-címe. |Yes |
| port |Az a port, amelyen az SFTP-kiszolgáló figyel. Az alapértelmezett érték: 21 |No |
| authenticationType |Adja meg a hitelesítési típust. Megengedett értékek: **Alapszintű**, **SshPublicKey**. <br><br> Tekintse meg az alapszintű hitelesítés használatát és az [SSH nyilvános kulcsú hitelesítés](#using-ssh-public-key-authentication) használatát ismertető szakaszt további tulajdonságok és JSON-minták használatával. |Yes |
| skipHostKeyValidation | Megadhatja, hogy kihagyja-e a gazdagép kulcsának érvényesítését | Nem. Az alapértelmezett érték: false |
| hostKeyFingerprint | A gazdagép kulcsának ujj-nyomtatását határozza meg. | Igen, ha a `skipHostKeyValidation` értéke hamis.  |
| Átjáró neve |A helyszíni SFTP-kiszolgálóhoz való kapcsolódáshoz használandó adatkezelés átjáró neve. | Igen, ha helyszíni SFTP-kiszolgálóról másol adatok. |
| encryptedCredential | Titkosított hitelesítő adatok az SFTP-kiszolgálóhoz való hozzáféréshez. Automatikusan jön létre, ha alapszintű hitelesítést (username + Password) vagy SshPublicKey hitelesítést (Felhasználónév + titkos kulcs elérési útja vagy tartalma) ad meg a másolási varázslóban vagy a ClickOnce előugró ablakban. | Nem. Csak a helyszíni SFTP-kiszolgálóról származó adatok másolásakor érvényes. |

#### <a name="example-using-basic-authentication"></a>Példa: egyszerű hitelesítés használata

Ha alapszintű hitelesítést szeretne használni, állítsa a as értékre `authenticationType` `Basic` , és adja meg a következő TULAJDONSÁGOKAT az SFTP-összekötő általános beállításai mellett az utolsó szakaszban bemutatott módon:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| username | Az a felhasználó, aki hozzáfér az SFTP-kiszolgálóhoz. |Yes |
| jelszó | A felhasználó jelszava (username). | Yes |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Példa: **Egyszerű hitelesítés titkosított hitelesítő adatokkal**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Nyilvános SSH-kulcsos hitelesítés használata:**

Ha alapszintű hitelesítést szeretne használni, állítsa a as értékre `authenticationType` `SshPublicKey` , és adja meg a következő TULAJDONSÁGOKAT az SFTP-összekötő általános beállításai mellett az utolsó szakaszban bemutatott módon:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| username |Az SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Yes |
| privateKeyPath | Az átjáró által elérhető titkos kulcsfájl abszolút elérési útját határozza meg. | Válassza a `privateKeyPath` vagy a `privateKeyContent` . <br><br> Csak a helyszíni SFTP-kiszolgálóról származó adatok másolásakor érvényes. |
| privateKeyContent | A titkos kulcs tartalmának szerializált karakterlánca. A másolás varázsló elolvashatja a titkos kulcs fájlját, és automatikusan kibonthatja a titkos kulcs tartalmát. Ha bármilyen más eszközt vagy SDK-t használ, használja helyette a privateKeyPath tulajdonságot. | Válassza a `privateKeyPath` vagy a `privateKeyContent` . |
| Jelszót | Adja meg a pass kifejezést/jelszót a titkos kulcs visszafejtéséhez, ha a kulcsot egy pass kifejezés védi. | Igen, ha a titkos kulcs fájlját egy pass kifejezés védi. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Példa: **SshPublicKey hitelesítés a titkos kulcs tartalmának használatával**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

További információ: SFTP- [összekötő](data-factory-sftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Adathalmaz
Az SFTP-adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **fájlmegosztás** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa alárendelt elérési útja. A karakterláncban a speciális karaktereknél használja a Escape karaktert. Példákat a következő témakörben talál: példa társított szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja a **partitionBy** , hogy a mappa elérési útjai a szelet kezdő/befejező dátum-és időpontjain alapulnak. |Yes |
| fileName |Adja meg a fájl nevét a **folderPath** , ha azt szeretné, hogy a tábla egy adott fájlra hivatkozzon a mappában. Ha nem ad meg értéket ehhez a tulajdonsághoz, a tábla a mappában található összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumú lesz: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Adja meg azt a szűrőt, amelyet a folderPath található fájlok részhalmazának kiválasztására kíván használni az összes fájl helyett.<br/><br/>Az engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2016-1-?.txt"`<br/><br/> a fileFilter egy bemeneti fájlmegosztás adatkészlet esetében alkalmazható. Ez a tulajdonság nem támogatott a HDFS. |No |
| partitionedBy |a partitionedBy használható egy dinamikus folderPath, az idősorozat-adatfájlok fájlnevének megadására. Például a folderPath paramétert minden óra adatértéknél. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli módot használja-e. A bináris mód és a hamis ASCII esetében igaz. Alapértelmezett érték: true (igaz). Ez a tulajdonság csak akkor használható, ha a társított társított szolgáltatás típusa: FTP. |No |

> [!NOTE]
> a filename és a fileFilter nem használható egyszerre.

#### <a name="example"></a>Példa

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

További információ: SFTP- [összekötő](data-factory-sftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása másolási tevékenységben
Ha az adatok másolása SFTP-forrásból történik, állítsa a másolási tevékenység **FileSystemSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. |Igaz, hamis (alapértelmezett) |No |



#### <a name="example"></a>Példa

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

További információ: SFTP- [összekötő](data-factory-sftp-connector.md#copy-activity-properties) .


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Társított szolgáltatások
HTTP-alapú társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **http** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| url | A webkiszolgáló alap URL-címe | Yes |
| authenticationType | Megadja a hitelesítési típust. Az engedélyezett értékek: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows**, **ClientCertificate**. <br><br> A hitelesítési típusokra vonatkozó további tulajdonságokért és JSON-mintákért tekintse meg a táblázat alatti szakaszt. | Yes |
| enableServerCertificateValidation | Itt adhatja meg, hogy engedélyezi-e a kiszolgáló TLS/SSL-tanúsítványának érvényesítését, ha a forrás HTTPS-webkiszolgáló | Nem, az alapértelmezett érték TRUE (igaz) |
| Átjáró neve | A helyszíni HTTP-forráshoz való kapcsolódáshoz adatkezelés átjáró neve. | Igen, ha helyszíni HTTP-forrásról másol adatok. |
| encryptedCredential | Titkosított hitelesítő adatok a HTTP-végpont eléréséhez. Automatikusan jön létre, amikor konfigurálja a hitelesítési adatokat a másolás varázslóban vagy a ClickOnce előugró ablakban. | Nem. Csak az adatok helyszíni HTTP-kiszolgálóról való másolása esetén érvényes. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Példa: alapszintű, kivonatoló vagy Windows-hitelesítés használata
A (z), vagy a (z) és a (z) beállításnál `authenticationType` `Basic` `Digest` `Windows` adja meg a következő tulajdonságokat, valamint a fent bemutatott http-összekötő általános

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| username | Felhasználónév a HTTP-végpont eléréséhez. | Yes |
| jelszó | A felhasználó jelszava (username). | Yes |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Példa: ClientCertificate-hitelesítés használata

Ha alapszintű hitelesítést szeretne használni, állítsa a as értékre `authenticationType` `ClientCertificate` , és adja meg a következő TULAJDONSÁGOKAT a http-összekötő fent bemutatott általános beállításai mellett:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| embeddedCertData | A személyes információcsere (PFX) fájl bináris adatainak Base64 kódolású tartalma. | Válassza a `embeddedCertData` vagy a `certThumbprint` . |
| certThumbprint | Az átjárót tároló számítógép tanúsítványtárolójában telepített Tanúsítvány ujjlenyomata. Csak a helyszíni HTTP-forrásból származó adatok másolásakor alkalmazható. | Válassza a `embeddedCertData` vagy a `certThumbprint` . |
| jelszó | A tanúsítványhoz tartozó jelszó. | No |

Ha `certThumbprint` hitelesítésre használja, és a tanúsítvány a helyi számítógép személyes tárolójába van telepítve, akkor olvasási engedélyt kell adnia az átjáró szolgáltatásnak:

1. Indítsa el a Microsoft Management Console (MMC) programot. Adja hozzá a **helyi számítógépet** tároló **tanúsítványok** beépülő modult.
2. Bontsa ki a **tanúsítványok**, **személyes**, majd a **tanúsítványok** elemet.
3. Kattintson a jobb gombbal a tanúsítványra a személyes tárolóban, és válassza a **minden feladat** -> **titkos kulcsok kezelése...** lehetőséget.
3. A **Biztonság** lapon adja hozzá azt a felhasználói fiókot, amely alatt a adatkezelés átjáró szolgáltatás fut, és olvasási hozzáféréssel rendelkezik a tanúsítványhoz.

**Példa: ügyfél-tanúsítvány használata:** Ez a társított szolgáltatás az adatgyárat egy helyszíni HTTP-webkiszolgálóra kapcsolja. Egy olyan ügyféltanúsítványt használ, amely telepítve van adatkezelés átjáróval rendelkező gépre.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Példa: ügyféltanúsítvány használata egy fájlban
Ez a társított szolgáltatás az adatgyárat egy helyszíni HTTP-webkiszolgálóra kapcsolja. Egy ügyféltanúsítvány-fájlt használ a gépen, amelyen telepítve van adatkezelés átjáró.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

További információt a http- [összekötő](data-factory-http-connector.md#linked-service-properties) című cikkben talál.

### <a name="dataset"></a>Adathalmaz
HTTP-adatkészlet definiálásához állítsa az adatkészlet **típusát** **http** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| relativeUrl | Az adatforrást tartalmazó erőforrás relatív URL-címe. Ha nincs megadva az elérési út, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. <br><br> A dinamikus URL-cím létrehozásához [Data Factory függvényeket és rendszerváltozókat](data-factory-functions-variables.md)használhat, például: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` . | No |
| requestMethod | Http-metódus. Az engedélyezett értékek a **Get** vagy a **post**. | Nem. Az alapértelmezett szint a `GET`. |
| additionalHeaders | További HTTP-kérelmek fejlécei. | No |
| requestBody | A HTTP-kérelem törzse. | No |
| formátumban | Ha egyszerűen **le szeretné kérdezni a http-végpont adatait** az elemzés nélkül, ugorja át ezt a formázási beállítást. <br><br> Ha a HTTP-válasz tartalmát a másolás során szeretné elemezni, a következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example-using-the-get-default-method"></a>Példa: a GET (alapértelmezett) metódus használata

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Példa: a POST metódus használata

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
További információt a http- [összekötő](data-factory-http-connector.md#dataset-properties) című cikkben talál.

### <a name="http-source-in-copy-activity"></a>HTTP-forrás a másolási tevékenységben
Ha HTTP-forrásból másol Adatmásolást, állítsa be a másolási tevékenység **HttpSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időtúllépése (TimeSpan). A válasz lekérésének időtúllépése, nem a válaszüzenetek olvasásának időtúllépése. | Nem. Alapértelmezett érték: 00:01:40 |


#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
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
        }]
    }
}
```

További információt a http- [összekötő](data-factory-http-connector.md#copy-activity-properties) című cikkben talál.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Társított szolgáltatások
OData társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **OData** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| url |A OData szolgáltatás URL-címe. |Yes |
| authenticationType |A OData-forráshoz való kapcsolódáshoz használt hitelesítés típusa. <br/><br/> A Felhőbeli OData a lehetséges értékek a következők: névtelen, alapszintű és OAuth (Megjegyzés Azure Data Factory jelenleg csak Azure Active Directory-alapú OAuth-t támogatnak). <br/><br/> A helyszíni OData a lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. |Igen (csak akkor, ha alapszintű hitelesítést használ) |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |Igen (csak akkor, ha alapszintű hitelesítést használ) |
| authorizedCredential |Ha OAuth használ, kattintson az **Engedélyezés** gombra a Data Factory másolás varázslóban vagy a szerkesztőben, és adja meg a hitelesítő adatait, majd a tulajdonság értékét automatikusan létrehozza a rendszer. |Igen (csak OAuth-hitelesítés használata esetén) |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni OData szolgáltatáshoz való kapcsolódáshoz. Csak akkor kell megadni, ha a helyszíni OData forrásból másol Adatmásolást. |No |

#### <a name="example---using-basic-authentication"></a>Példa – egyszerű hitelesítés használata
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Példa – névtelen hitelesítés használata

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Példa – helyszíni OData-forráshoz hozzáférő Windows-hitelesítés használata

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Példa – a OAuth-hitelesítés használata a felhőalapú OData-forráshoz való hozzáféréshez
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

További információ: OData- [összekötő](data-factory-odata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Adathalmaz
OData adatkészlet definiálásához állítsa az adatkészlet **típusát** **ODataResource** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| path |A OData erőforrás elérési útja |No |

#### <a name="example"></a>Példa

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

További információ: OData- [összekötő](data-factory-odata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha OData forrásból másol Adatmásolást, állítsa a másolási tevékenység **forrás típusát** **RelationalSource** értékre, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Példa | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |"? $select = név, leírás&$top = 5" |No |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

További információ: OData- [összekötő](data-factory-odata-connector.md#copy-activity-properties) .


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Társított szolgáltatások
ODBC-hez társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **OnPremisesOdbc** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| connectionString |A kapcsolati karakterlánc és egy opcionálisan titkosított hitelesítő adat nem hozzáférési hitelesítő része. Tekintse meg a példákat a következő részekben. |Yes |
| hitelesítő adat |Az illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő része. Például: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |No |
| authenticationType |Az ODBC-adattárhoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen és alapszintű. |Yes |
| username |Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell az ODBC-adattárhoz való kapcsolódáshoz. |Yes |

#### <a name="example---using-basic-authentication"></a>Példa – egyszerű hitelesítés használata

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Példa – egyszerű hitelesítés használata titkosított hitelesítő adatokkal
A hitelesítő adatokat a [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) parancsmag használatával titkosíthatja.

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Példa: Névtelen hitelesítés használata

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információ: ODBC- [összekötő](data-factory-odbc-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
ODBC-adatkészlet definiálásához állítsa az adatkészlet **típusát** a **RelationalTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Az ODBC-adattárban található tábla neve. |Yes |


#### <a name="example"></a>Példa

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: ODBC- [összekötő](data-factory-odbc-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha az adatok másolása egy ODBC-adattárból történik, állítsa a másolási tevékenység **RelationalSource**, és adja **meg a következő** tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információ: ODBC- [összekötő](data-factory-odbc-connector.md#copy-activity-properties) cikk.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Társított szolgáltatások
Salesforce társított szolgáltatás definiálásához állítsa a társított szolgáltatás **típusát** **Salesforce** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| environmentUrl | Itt adhatja meg az Salesforce-példány URL-címét. <br><br> – Az alapértelmezett érték a "https: \/ /login.Salesforce.com". <br> – Az adatok a homokozóból való másolásához válassza a " https://test.salesforce.com " lehetőséget. <br> – Az adatok egyéni tartományból történő másolásához írja be például a következőt: "https://[tartomány]. my. Salesforce. com". |No |
| username |Adja meg a felhasználói fiók felhasználónevét. |Yes |
| jelszó |A felhasználói fiókhoz tartozó jelszó megadása. |Yes |
| securityToken |A felhasználói fiók biztonsági jogkivonatának megadása. A biztonsági jogkivonat alaphelyzetbe állításával/lekérésével kapcsolatos útmutatásért lásd a [biztonsági jogkivonat beszerzése](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) című témakört. A biztonsági jogkivonatok általános megismeréséhez lásd: [Biztonság és API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Yes |

#### <a name="example"></a>Példa

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

További információ: Salesforce- [összekötő](data-factory-salesforce-connector.md#linked-service-properties) .

### <a name="dataset"></a>Adathalmaz
Salesforce adatkészlet definiálásához állítsa az adatkészlet **típusát** **RelationalTable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a Salesforce. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** ) |

#### <a name="example"></a>Példa

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

További információ: Salesforce- [összekötő](data-factory-salesforce-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenységben található viszonyítási forrás
Ha az adatok másolása a Salesforce-ból történik, állítsa a másolási tevékenység **forrás típusát** **RelationalSource** értékre, és adja meg a következő tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-92 lekérdezés vagy [Salesforce objektum lekérdezési nyelve (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezése. Például: `select * from MyTable__c`. |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |

#### <a name="example"></a>Példa



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
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
        }]
    }
}
```

> [!IMPORTANT]
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

További információ: Salesforce- [összekötő](data-factory-salesforce-connector.md#copy-activity-properties) .

## <a name="web-data"></a>Webes adattartalom

### <a name="linked-service"></a>Társított szolgáltatások
Webes társított szolgáltatás definiálásához állítsa be a társított szolgáltatás **típusát** a **web** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| URL-cím |A webes forrás URL-címe |Yes |
| authenticationType |Névtelen. |Yes |


#### <a name="example"></a>Példa


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

További információ: a [webes táblázat összekötője](data-factory-web-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Adathalmaz
Webes adatkészlet definiálásához állítsa be az adatkészlet **típusát** a **webtable** értékre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |az adatkészlet típusa. a **webtable** értékre kell beállítani |Yes |
| path |A táblázatot tartalmazó erőforrás relatív URL-címe. |Nem. Ha nincs megadva az elérési út, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. |
| index |Az erőforrásban található tábla indexe A HTML-lapokban található táblázat indexének beolvasásához szükséges lépésekért lásd: index beolvasása egy HTML-oldalon. |Yes |

#### <a name="example"></a>Példa

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
            "interval": 1
        }
    }
}
```

További információ: a [webes táblázat összekötője](data-factory-web-table-connector.md#dataset-properties) .

### <a name="web-source-in-copy-activity"></a>Webes forrás a másolási tevékenységben
Ha egy webtáblából másol Adatmásolást, állítsa a másolási tevékenység **forrás típusát** a **webforrásra**. Jelenleg, ha a másolási tevékenység forrása **webforrásként** van megadva, a további tulajdonságok nem támogatottak.

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }]
    }
}
```

További információ: a [webes táblázat összekötője](data-factory-web-table-connector.md#copy-activity-properties) .

## <a name="compute-environments"></a>SZÁMÍTÁSI KÖRNYEZETEK
A következő táblázat felsorolja a Data Factory által támogatott számítási környezeteket és a rajtuk futó átalakítási tevékenységeket. Kattintson arra a számítási feltételre, amelyre kíváncsi, hogy megjelenjenek-e a társított szolgáltatáshoz tartozó JSON-sémák ahhoz, hogy összekapcsolják azt egy adatelőállítóval.

| Számítási környezet | Tevékenységek |
| --- | --- |
| [Igény szerinti HDInsight-fürt](#on-demand-azure-hdinsight-cluster) vagy [a saját HDInsight-fürt](#existing-azure-hdinsight-cluster) |[.Net egyéni tevékenység](#net-custom-activity), [kaptár tevékenység](#hdinsight-hive-activity), [Pig tevékenység](#hdinsight-pig-activity), [MapReduce tevékenység](#hdinsight-mapreduce-activity), Hadoop streaming tevékenység, [Spark-tevékenység](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET egyéni tevékenység](#net-custom-activity) |
| [Azure Machine Learning Studio (klasszikus)](#azure-machine-learning-studio-classic) | [Azure Machine learning Studio (klasszikus) kötegelt végrehajtási tevékenység](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure Machine learning Studio (klasszikus) erőforrás-frissítési tevékenység](#azure-machine-learning-studio-classic-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Azure szinapszis Analytics](#azure-synapse-analytics), [SQL Server](#sql-server-stored-procedure) |[Tárolt eljárás](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Igény szerinti Azure HDInsight-fürt
A Azure Data Factory szolgáltatás képes automatikusan létrehozni egy Windows/Linux-alapú, igény szerinti HDInsight-fürtöt az adatfeldolgozáshoz. A fürt ugyanabban a régióban jön létre, mint a fürthöz társított Storage-fiók (a JSON linkedServiceName tulajdonsága). A következő átalakítási tevékenységeket futtathatja ezen a társított szolgáltatáson: [.net Custom Activity](#net-custom-activity), [kaptár tevékenység](#hdinsight-hive-activity), [Pig tevékenység](#hdinsight-pig-activity), [MapReduce tevékenység](#hdinsight-mapreduce-activity), Hadoop streaming Activity, [Spark-tevékenység](#hdinsight-spark-activity).

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat az igény szerinti HDInsight társított szolgáltatás Azure JSON-definíciójában használt tulajdonságok leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot **HDInsightOnDemand** értékre kell beállítani. |Yes |
| clusterSize |A fürtben lévő feldolgozó/adatcsomópontok száma. A HDInsight-fürt két fő csomóponttal, valamint a tulajdonsághoz megadott munkavégző csomópontok számával együtt jön létre. A csomópontok mérete Standard_D3 4 mag, így a 4 feldolgozó csomópont-fürt 24 magot (4 \* 4 = 16 magot tartalmaz a feldolgozó csomópontok számára, plusz 2 \* 4 = 8 magot a fő csomópontok számára). Lásd: [Linux-alapú Hadoop-fürtök létrehozása a HDInsight-ben](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) a Standard_D3 szintjével kapcsolatos részletekért. |Yes |
| TimeToLive |Az igény szerinti HDInsight-fürt számára engedélyezett üresjárati idő. Meghatározza, hogy az igény szerinti HDInsight-fürt mennyi ideig maradjon életben a tevékenység futtatása után, ha nincsenek más aktív feladatok a fürtben.<br/><br/>Ha például egy tevékenység futása 6 percet vesz igénybe, és a TimeToLive 5 percre van állítva, a fürt a tevékenység futtatásának 6 perce után 5 perccel továbbra is életben marad. Ha egy másik tevékenység futtatása a 6 perces időszakot futtatja, a rendszer ugyanazt a fürtöt dolgozza fel.<br/><br/>Egy igény szerinti HDInsight-fürt létrehozása költséges művelet (eltarthat egy darabig), ezért szükség szerint ezt a beállítást használja az adat-előállító teljesítményének növeléséhez egy igény szerinti HDInsight-fürt újbóli felhasználásával.<br/><br/>Ha a TimeToLive értéket 0 értékre állítja, akkor a rendszer azonnal törli a fürtöt, amint a tevékenység feldolgozás alatt fut. Ha viszont magas értéket állít be, akkor a fürt tétlen maradhat, ami magas költségekkel jár. Ezért fontos, hogy az igényeinek megfelelően állítsa be a megfelelő értéket.<br/><br/>Több folyamat is megoszthatja az igény szerinti HDInsight-fürt ugyanazon példányát, ha a TimeToLive tulajdonság értéke megfelelően be van állítva. |Yes |
| version |A HDInsight-fürt verziója. Részletekért lásd: [a Azure Data Factory támogatott HDInsight verziói](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |No |
| linkedServiceName |Az Azure Storage társított szolgáltatása, amelyet az igény szerinti fürt használ az adattároláshoz és az adatfeldolgozáshoz. <p>Jelenleg nem hozhat létre olyan igény szerinti HDInsight-fürtöt, amely egy Azure Data Lake Store használ tárolóként. Ha az eredményeket egy Azure Data Lake Store HDInsight-feldolgozásból szeretné tárolni, a másolási tevékenységgel másolja át az Azure Blob Storage adatait az Azure Data Lake Store.</p>  | Yes |
| additionalLinkedServiceNames |További Storage-fiókokat ad meg a HDInsight társított szolgáltatáshoz, hogy a Data Factory szolgáltatás regisztrálja őket az Ön nevében. |No |
| osType |Az operációs rendszer típusa. Az engedélyezett értékek a következők: Windows (alapértelmezett) és Linux |No |
| hcatalogLinkedServiceName |Az HCatalog-adatbázisra mutató Azure SQL társított szolgáltatás neve. Az igény szerinti HDInsight-fürtöt az Azure SQL Database metaadattár használatával hozza létre. |No |

### <a name="json-example"></a>JSON-példa
A következő JSON egy Linux-alapú igény szerinti HDInsight társított szolgáltatást definiál. Az Data Factory szolgáltatás automatikusan létrehoz egy **Linux-alapú HDInsight-** fürtöt az adatszeletek feldolgozásakor.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

További információ: [számítási társított szolgáltatások](data-factory-compute-linked-services.md) cikk.

## <a name="existing-azure-hdinsight-cluster"></a>Meglévő Azure HDInsight-fürt
Létrehozhat egy Azure HDInsight társított szolgáltatást, hogy regisztrálja a saját HDInsight-fürtöt Data Factory használatával. A következő Adatátalakítási tevékenységeket futtathatja ezen a társított szolgáltatáson: [.net Custom Activity](#net-custom-activity), [kaptár tevékenység](#hdinsight-hive-activity), [Pig tevékenység](#hdinsight-pig-activity), [MapReduce tevékenység](#hdinsight-mapreduce-activity), Hadoop streaming Activity, [Spark-tevékenység](#hdinsight-spark-activity).

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat az Azure HDInsight társított szolgáltatás Azure JSON-definíciójában használt tulajdonságok leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot **HDInsight** értékre kell beállítani. |Yes |
| clusterUri |A HDInsight-fürt URI-ja. |Yes |
| username |Adja meg a meglévő HDInsight-fürthöz való kapcsolódáshoz használni kívánt felhasználó nevét. |Yes |
| jelszó |A felhasználói fiók jelszavának megadása. |Yes |
| linkedServiceName | Az Azure Storage társított szolgáltatás neve, amely a HDInsight-fürt által használt Azure Blob Storage-tárolóra hivatkozik. <p>Jelenleg nem adhat meg Azure Data Lake Store társított szolgáltatást ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér a Data Lake Storehoz, akkor a kaptár/Pig-parancsfájlok Azure Data Lake Storeban lévő adatokhoz férhet hozzá. </p>  |Yes |

Az HDInsight-fürtök támogatott verzióival kapcsolatban lásd: [támogatott HDInsight-verziók](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>JSON-példa

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Létrehozhat egy Azure Batch társított szolgáltatást, amellyel regisztrálhatja a virtuális gépek (VM-EK) batch-készletét egy adatelőállítóval. A .NET-alapú egyéni tevékenységeket Azure Batch vagy Azure HDInsight is futtathatja. A .NET-alapú [Egyéni tevékenységeket](#net-custom-activity) futtathatja ezen a társított szolgáltatáson.

### <a name="linked-service"></a>Társított szolgáltatások
A következő táblázat a Azure Batch társított szolgáltatás Azure JSON-definíciójában használt tulajdonságok leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot **AzureBatch** értékre kell beállítani. |Yes |
| accountName |A Azure Batch fiók neve. |Yes |
| accessKey |A Azure Batch fiók elérési kulcsa. |Yes |
| poolName |A virtuális gépek készletének neve. |Yes |
| linkedServiceName |Az ehhez a Azure Batch társított szolgáltatáshoz társított Azure Storage társított szolgáltatás neve. Ez a társított szolgáltatás a tevékenység futtatásához és a tevékenység-végrehajtási naplók tárolásához szükséges átmeneti fájlokhoz használatos. |Yes |


#### <a name="json-example"></a>JSON-példa

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasszikus)
Egy Azure Machine Learning Studio (klasszikus) társított szolgáltatást hoz létre a Studio (klasszikus) batch pontozási végpontjának egy adatelőállítóval való regisztrálásához. Két Adatátalakítási tevékenység futtatható ezen a társított szolgáltatáson: [Azure Machine learning Studio (klasszikus) kötegelt végrehajtási tevékenység](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure Machine learning Studio (klasszikus) erőforrás-frissítési tevékenység](#azure-machine-learning-studio-classic-update-resource-activity).

### <a name="linked-service"></a>Társított szolgáltatások
A következő táblázat a Studio (klasszikus) társított szolgáltatás Azure JSON-definíciójában használt tulajdonságok leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| Típus |A Type tulajdonságot a következőre kell beállítani: **AzureML**. |Yes |
| mlEndpoint |A Batch-pontozási URL-cím. |Yes |
| apiKey |A közzétett munkaterület-modell API-ját. |Yes |

#### <a name="json-example"></a>JSON-példa

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Hozzon létre egy **Azure Data Lake Analytics** társított szolgáltatást egy Azure Data Lake Analytics számítási szolgáltatás Azure-beli adatgyárhoz való csatolásához, mielőtt az [Data Lake Analytics U-SQL-tevékenységet](data-factory-usql-activity.md) egy folyamaton belül használja.

### <a name="linked-service"></a>Társított szolgáltatások

A következő táblázat a Azure Data Lake Analytics társított szolgáltatás JSON-definíciójában használt tulajdonságok leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| Típus |A Type tulajdonságot a következőre kell beállítani: **AzureDataLakeAnalytics**. |Yes |
| accountName |Azure Data Lake Analytics fiók neve. |Yes |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI. |No |
| engedélyezés |A rendszer automatikusan beolvassa az engedélyezési kódot, miután a Data Factory-szerkesztőben az **Engedélyezés** gombra kattint, és befejezi a OAuth-bejelentkezést. |Yes |
| subscriptionId |Azure-előfizetés azonosítója |Nem (ha nincs megadva, a rendszer az adatfeldolgozó előfizetését használja). |
| resourceGroupName |Azure-erőforráscsoport neve |Nem (ha nincs megadva, a rendszer az adatfeldolgozó erőforráscsoportot használja). |
| sessionId |a munkamenet-azonosító a OAuth-engedélyezési munkamenetből. Az egyes munkamenet-AZONOSÍTÓk egyediek, és csak egyszer használhatók. A Data Factory szerkesztő használatakor ez az azonosító automatikusan létrejön. |Yes |


#### <a name="json-example"></a>JSON-példa
Az alábbi példa egy Azure Data Lake Analytics társított szolgáltatás JSON-definícióját tartalmazza.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>SQL Server tárolt eljárás

Létrehoz egy SQL Server társított szolgáltatást, és a [tárolt eljárási tevékenységgel](data-factory-stored-proc-activity.md) felhasználja a tárolt eljárás meghívását egy Data Factory folyamatból.

### <a name="linked-service"></a>Társított szolgáltatások
Hozzon létre egy **OnPremisesSqlServer** típusú társított szolgáltatást egy SQL Server adatbázis egy adatgyárhoz való csatolásához. A következő táblázat a SQL Server társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

A következő táblázat a SQL Server társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesSqlServer**. |Yes |
| connectionString |Az SQL-hitelesítés vagy a Windows-hitelesítés használatával a SQL Server-adatbázishoz való kapcsolódáshoz szükséges connectionString-adatokat adjon meg. |Yes |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a SQL Server adatbázishoz való kapcsolódáshoz. |Yes |
| username |Windows-hitelesítés használata esetén adja meg a felhasználónevet. Példa: **tartománynév \\ Felhasználónév**. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |

A **New-AzDataFactoryEncryptValue** parancsmaggal titkosíthatja a hitelesítő adatokat, és a következő példában látható módon használhatja azokat a kapcsolatok karakterláncában (**EncryptedCredential** tulajdonság):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Példa: JSON az SQL-hitelesítés használatához

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Példa: JSON a Windows-hitelesítés használatához

Ha a Felhasználónév és a jelszó meg van adva, az átjáró a megadott felhasználói fiók megszemélyesítésére használja a SQL Server adatbázishoz való csatlakozáshoz. Ellenkező esetben az átjáró közvetlenül csatlakozik a SQL Serverhoz az átjáró biztonsági környezetével (az indítási fiókkal).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

További információ: SQL Server- [összekötő](data-factory-sqlserver-connector.md#linked-service-properties) cikke.

## <a name="data-transformation-activities"></a>ADATÁTALAKÍTÁSI TEVÉKENYSÉGEK

Tevékenység | Leírás
-------- | -----------
[HDInsight-struktúra tevékenység](#hdinsight-hive-activity) | A Data Factory folyamat HDInsight-struktúrájának tevékenysége a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a kaptár-lekérdezéseket.
[HDInsight Pig-tevékenység](#hdinsight-pig-activity) | A HDInsight Pig-tevékenység egy Data Factory folyamat során a Pig-lekérdezéseket a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre.
[HDInsight MapReduce-tevékenység](#hdinsight-mapreduce-activity) | A Data Factory-folyamat HDInsight MapReduce-tevékenysége a MapReduce-programokat saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre.
[HDInsight Streaming-tevékenység](#hdinsight-streaming-activity) | A Data Factory-folyamat HDInsight adatfolyam-továbbítási tevékenysége a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a Hadoop streaming-programokat.
[HDInsight Spark-tevékenység](#hdinsight-spark-activity) | A Data Factory folyamat HDInsight Spark-tevékenysége Spark-programokat hajt végre a saját HDInsight-fürtön.
[Az Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenysége](#azure-machine-learning-studio-classic-batch-execution-activity) | Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre olyan folyamatokat, amelyek közzétett Studio (klasszikus) webszolgáltatást használnak a prediktív elemzésekhez. A Batch-végrehajtási tevékenység egy Azure Data Factory folyamat során meghívja a Studio (klasszikus) webszolgáltatás használatát, hogy előrejelzéseket készítsen a Batch-ben lévő adatairól.
[Az Azure Machine Learning Studio (klasszikus) erőforrás-frissítési tevékenysége](#azure-machine-learning-studio-classic-update-resource-activity) | Idővel a Azure Machine Learning Studio (klasszikus) pontozási kísérletek prediktív modelljeit új bemeneti adatkészletek használatával kell áttanítani. Miután végzett az újraképzéssel, frissítenie kell a pontozási webszolgáltatást az áttelepített gépi tanulási modellel. Az erőforrás frissítése tevékenységgel frissítheti az újonnan betanított modellt használó webszolgáltatást.
[Tárolt eljárási tevékenység](#stored-procedure-activity) | Egy Data Factory folyamat tárolt eljárása tevékenységgel egy tárolt eljárást hívhat meg a következő adattárak egyikében: Azure SQL Database, Azure szinapszis Analytics, SQL Server adatbázis a vállalatban vagy egy Azure-beli virtuális gépen.
[U-SQL-Data Lake Analytics tevékenység](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL-tevékenység egy U-SQL-szkriptet futtat egy Azure Data Lake Analytics-fürtön.
[.NET egyéni tevékenység](#net-custom-activity) | Ha olyan módon kell adatátalakítást végeznie, amelyet a Data Factory nem támogat, létrehozhat egy egyéni tevékenységet a saját adatfeldolgozási logikával, és felhasználhatja a folyamat tevékenységeit. Az egyéni .NET-tevékenységeket beállíthatja úgy, hogy az Azure Batch vagy egy Azure HDInsight-fürtön fusson.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive-tevékenység
A következő tulajdonságokat adhatja meg egy struktúra-tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **HDInsightHive**. Először létre kell hoznia egy HDInsight társított szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a HDInsightHive:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| parancsfájl |A struktúra-parancsfájl beágyazottként való megadásának meghatározása |No |
| parancsfájl elérési útja |Tárolja a kaptár parancsfájlt egy Azure Blob Storage-tárolóban, és adja meg a fájl elérési útját. Használja a "script" vagy a "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájl neve megkülönbözteti a kis-és nagybetűket. |No |
| meghatározza |Adja meg a paramétereket kulcs/érték párokként a kaptár parancsfájlban a "hiveconf" használatával való hivatkozáshoz. |No |

Az ilyen típusú tulajdonságok a kaptár tevékenységre vonatkoznak. Az egyéb tulajdonságok (az typeProperties szakaszon kívül) minden tevékenység esetében támogatottak.

### <a name="json-example"></a>JSON-példa
A következő JSON egy folyamat HDInsight-struktúráját határozza meg.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

További információ: kaptár- [tevékenységgel](data-factory-hive-activity.md) kapcsolatos cikk.

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-tevékenység
A következő tulajdonságokat adhatja meg egy Pig-tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **HDInsightPig**. Először létre kell hoznia egy HDInsight társított szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a HDInsightPig:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| parancsfájl |Adja meg a Pig-szkriptet beágyazottként |No |
| parancsfájl elérési útja |Tárolja a Pig-szkriptet egy Azure Blob Storage-tárolóban, és adja meg a fájl elérési útját. Használja a "script" vagy a "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájl neve megkülönbözteti a kis-és nagybetűket. |No |
| meghatározza |Adja meg a paramétereket kulcs/érték párokként a Pig-szkripten belüli hivatkozáshoz |No |

Az ilyen típusú tulajdonságok a Pig tevékenységre jellemzőek. Az egyéb tulajdonságok (az typeProperties szakaszon kívül) minden tevékenység esetében támogatottak.

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

További információ: Pig-tevékenységgel kapcsolatos cikk.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység
A következő tulajdonságokat adhatja meg egy MapReduce tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **HDInsightMapReduce**. Először létre kell hoznia egy HDInsight társított szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a HDInsightMapReduce:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| jarLinkedService | A JAR-fájlt tartalmazó Azure Storage-beli társított szolgáltatás neve. | Yes |
| jarFilePath | A JAR-fájl elérési útja az Azure Storage-ban. | Yes |
| className | A fő osztály neve a JAR-fájlban. | Yes |
| argumentumok | A MapReduce program vesszővel tagolt argumentumait tartalmazó lista. Futásidőben a MapReduce-keretrendszer néhány további argumentuma (például: MapReduce. job. Tags) jelenik meg. Ha meg szeretné különböztetni az argumentumokat a MapReduce argumentumokkal, érdemes lehet mindkét beállítást és értéket argumentumként használni, ahogy az alábbi példában látható (-s,--bemenet,--output stb.). | No |

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

További információ: [MapReduce Activity](data-factory-map-reduce.md) cikk.

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming-tevékenység
A következő tulajdonságokat adhatja meg egy Hadoop adatfolyam-tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **HDInsightStreaming**. Először létre kell hoznia egy HDInsight társított szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a HDInsightStreaming:

| Tulajdonság | Leírás |
| --- | --- |
| leképező | A Mapper végrehajtható fájljának neve. A példában a cat.exe a Mapper végrehajtható fájl.|
| szűkítő | A csökkentő végrehajtható fájl neve. A példában a wc.exe a csökkentő végrehajtható fájl. |
| bemenet | A Mapper bemeneti fájlja (beleértve a helyet). A példában:: adfsample a blob-tároló, például a (z), a (z) és a (z) `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` mappa, a davinci.txt pedig a blob. |
| output | A redukáló kimeneti fájlja (a helyet is beleértve). Az Hadoop streaming-feladatok kimenete a tulajdonsághoz megadott helyre íródik. |
| filePaths | A mapper és a csökkentő végrehajtható fájl elérési útjai. A példában a "adfsample/example/apps/wc.exe" adfsample a blob tároló, például az alkalmazások mappa, a wc.exe pedig a végrehajtható fájl. |
| fileLinkedService | Az Azure Storage társított szolgáltatása, amely az Azure Storage-t jelöli, amely a filePaths szakaszban megadott fájlokat tartalmazza. |
| argumentumok | A MapReduce program vesszővel tagolt argumentumait tartalmazó lista. Futásidőben a MapReduce-keretrendszer néhány további argumentuma (például: MapReduce. job. Tags) jelenik meg. Ha meg szeretné különböztetni az argumentumokat a MapReduce argumentumokkal, érdemes lehet mindkét beállítást és értéket argumentumként használni, ahogy az alábbi példában látható (-s,--bemenet,--output stb.). |
| getDebugInfo | Egy opcionális elem. Ha a hiba értékre van állítva, a naplók letöltése csak meghibásodás esetén történik meg. Ha az All értékre van állítva, a rendszer mindig letölti a naplókat a végrehajtási állapottól függetlenül. |

> [!NOTE]
> Meg kell adnia egy kimeneti adatkészletet a **kimenetek** tulajdonsághoz tartozó Hadoop streaming tevékenységhez. Ez az adatkészlet csak egy olyan próbabábu-adatkészlet lehet, amely a folyamat-ütemterv (óránként, naponta stb.) elvégzéséhez szükséges. Ha a tevékenység nem végez bemenetet, kihagyhatja a bemeneti adatkészlet megadását a **bemenetek** tulajdonság tevékenységéhez.

## <a name="json-example"></a>JSON-példa

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

További információ: [Hadoop streaming Activity](data-factory-hadoop-streaming-activity.md) cikk.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
Megadhatja a következő tulajdonságokat egy Spark-tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **HDInsightSpark**. Először létre kell hoznia egy HDInsight társított szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a HDInsightSpark:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| rootPath | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappa. A fájl neve megkülönbözteti a kis-és nagybetűket. | Yes |
| entryFilePath | A Spark-kód/csomag gyökérkönyvtárának relatív elérési útja. | Yes |
| className | Alkalmazás Java/Spark fő osztálya | No |
| argumentumok | A Spark programhoz tartozó parancssori argumentumok listája. | No |
| proxyUser | A Spark-program végrehajtásához megszemélyesíteni kívánt felhasználói fiók | No |
| sparkConfig | Spark-konfiguráció tulajdonságai. | No |
| getDebugInfo | Megadja, hogy a rendszer mikor másolja a Spark-naplófájlokat a sparkJobLinkedService által meghatározott HDInsight-fürt (vagy) által használt Azure-tárolóba. Megengedett értékek: nincs, mindig vagy sikertelen. Alapértelmezett érték: nincs. | No |
| sparkJobLinkedService | Az Azure Storage társított szolgáltatása, amely tartalmazza a Spark-feladatot tartalmazó fájlt, a függőségeket és a naplókat.  Ha nem ad meg értéket ehhez a tulajdonsághoz, a rendszer a HDInsight-fürthöz társított tárolót használja. | No |

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Vegye figyelembe a következő szempontokat:

- A **Type** tulajdonság értéke **HDInsightSpark**.
- A **rootPath** a **adfspark \\ pyFiles** értékre van beállítva, ahol a adfspark az Azure Blob-tároló, a pyFiles pedig az adott tárolóban található, kiváló mappa. Ebben a példában az Azure Blob Storage a Spark-fürthöz társított egyik. A fájlt egy másik Azure Storage-tárolóba töltheti fel. Ha így tesz, hozzon létre egy Azure Storage-beli társított szolgáltatást, amely a Storage-fiókot az adatelőállítóhoz kapcsolja. Ezután adja meg a társított szolgáltatás nevét a **sparkJobLinkedService** tulajdonság értékeként. A Spark-tevékenység tulajdonságaival és a Spark-tevékenység által támogatott egyéb tulajdonságokkal kapcsolatos részletekért tekintse meg a Spark-tevékenységek tulajdonságait.
- A **entryFilePath** beállítása a **test.py**, amely a Python-fájl.
- A **getDebugInfo** tulajdonság **mindig** értékre van állítva, ami azt jelenti, hogy a naplófájlok mindig jönnek létre (sikeres vagy sikertelen).

    > [!IMPORTANT]
    > Azt javasoljuk, hogy ne állítsa be úgy ezt a tulajdonságot, hogy mindig éles környezetben legyen, kivéve, ha problémát tapasztal.
- A **kimenetek** szakasz egy kimeneti adatkészlettel rendelkezik. A kimeneti adatkészletet akkor is meg kell adnia, ha a Spark-program nem hoz létre kimenetet. A kimeneti adatkészlet a folyamat ütemtervét (óránként, naponta stb.) vezérli.

A tevékenységgel kapcsolatos további információkért lásd a [Spark-tevékenységről](data-factory-spark.md) szóló cikket.

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Az Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenysége
A következő tulajdonságokat adhatja meg egy Azure Machine Learning Studio (klasszikus) batch-végrehajtási tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **AzureMLBatchExecution**. Először létre kell hoznia egy Studio (klasszikus) társított szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a AzureMLBatchExecution:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
webServiceInput | A Studio (klasszikus) webszolgáltatás bemenetként átadandó adatkészlet. Ennek az adatkészletnek szerepelnie kell a tevékenység bemenetei között is. |Használja a webServiceInput vagy a webServiceInputs. |
webServiceInputs | Adja meg azokat az adatkészleteket, amelyeket bemenetként kell átadni a Studio (klasszikus) webszolgáltatás számára. Ha a webszolgáltatás több bemenetet is igénybe vesz, használja a webServiceInputs tulajdonságot a webServiceInput tulajdonság használata helyett. A **webServiceInputs** hivatkozott adatkészleteket is szerepelnie kell a tevékenység **bemenetei** között. | Használja a webServiceInput vagy a webServiceInputs. |
webServiceOutputs | A Studio (klasszikus) webszolgáltatás kimenetként hozzárendelt adatkészletei. A webszolgáltatás visszaadja a kimeneti adatokat ebben az adatkészletben. | Yes |
globalParameters | A webszolgáltatás paramétereinek értékeinek megadása ebben a szakaszban. | No |

### <a name="json-example"></a>JSON-példa
Ebben a példában a tevékenység a bemeneti és a **MLSqlOutput** adatkészlettel rendelkezik, mint a kimenet **MLSqlInput** . A **MLSqlInput** a **webServiceInput** JSON-tulajdonság használatával továbbítja a webszolgáltatásnak. A **MLSqlOutput** a webszolgáltatás kimenetként adja át a **webServiceOutputs** JSON-tulajdonság használatával.

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

A JSON-példában a központilag telepített Studio (klasszikus) webszolgáltatás egy olvasót és egy író modult használ az adatok olvasására/írására egy Azure SQL Database. Ez a webszolgáltatás a következő négy paramétert teszi elérhetővé: az adatbázis-kiszolgáló neve, az adatbázis neve, a kiszolgáló felhasználói fiókjának neve és a kiszolgáló felhasználói fiókjának jelszava.

> [!NOTE]
> A webszolgáltatásnak csak a AzureMLBatchExecution tevékenység bemeneteit és kimeneteit lehet paraméterként átadni. A fenti JSON-kódrészletben például a MLSqlInput a AzureMLBatchExecution tevékenység bemenete, amelyet a rendszer a webServiceInput paraméterrel a webszolgáltatásnak bemenetként továbbít.

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Az Azure Machine Learning Studio (klasszikus) erőforrás-frissítési tevékenysége
A következő tulajdonságokat adhatja meg egy Azure Machine Learning Studio (klasszikus) frissítési erőforrás tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **AzureMLUpdateResource**. Először létre kell hoznia egy Studio (klasszikus) társított szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a AzureMLUpdateResource:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
trainedModelName | Az újratanított modell neve. | Yes |
trainedModelDatasetName | Az átképzési művelet által visszaadott iLearner-fájlra mutató adatkészlet. | Yes |

### <a name="json-example"></a>JSON-példa
A folyamat két tevékenységgel rendelkezik: **AzureMLBatchExecution** és **AzureMLUpdateResource**. A Studio (klasszikus) kötegelt végrehajtási tevékenység bemenetként veszi át a betanítási adatokat, és kimenetként létrehoz egy iLearner-fájlt. A tevékenység behívja a betanítási webszolgáltatást (webszolgáltatásként közzétett betanítási kísérlet) a bemeneti betanítási adatokkal, és a webszolgáltatásból fogadja a ilearner-fájlt. A placeholderBlob csak egy olyan próbabábu kimeneti adatkészlete, amelyre a Azure Data Factory szolgáltatásnak szüksége van a folyamat futtatásához.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő tulajdonságokat adhatja meg egy U-SQL-tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **DataLakeAnalyticsU-SQL**. Létre kell hoznia egy Azure Data Lake Analytics társított szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a DATALAKEANALYTICSU-SQL-re:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| scriptPath |A U-SQL-parancsfájlt tartalmazó mappa elérési útja. A fájl neve megkülönbözteti a kis-és nagybetűket. |Nem (ha parancsfájlt használ) |
| Scriptlinkedservice szolgáltatás |Társított szolgáltatás, amely összekapcsolja a parancsfájlt tartalmazó tárolót az adatgyárba |Nem (ha parancsfájlt használ) |
| parancsfájl |A scriptPath és a Scriptlinkedservice szolgáltatás meghatározása helyett beágyazott parancsfájlt adjon meg. Például: "script": "CREATE DATABASE test". |Nem (scriptPath és Scriptlinkedservice szolgáltatás használata esetén) |
| Analyticsunits |A feladatok futtatásához egyidejűleg használt csomópontok maximális száma. |No |
| prioritású |Meghatározza, hogy az összes várólistán lévő feladatra kiválassza az első futtatást. Minél kisebb a szám, annál magasabb a prioritás. |No |
| parameters |A U-SQL-parancsfájl paramétereinek |No |

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
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
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

További információ: [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Tárolt eljárási tevékenység
A következő tulajdonságokat adhatja meg egy tárolt eljárási tevékenység JSON-definíciójában. A tevékenység Type tulajdonságának a következőnek kell lennie: **SqlServerStoredProcedure**. Létre kell hoznia a következő társított szolgáltatások egyikét, és meg kell adnia a társított szolgáltatás nevét a **linkedServiceName** tulajdonság értékeként:

- SQL Server
- Azure SQL Database
- Azure Synapse Analytics

A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a SqlServerStoredProcedure:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| storedProcedureName |Adja meg a tárolt eljárás nevét Azure SQL Database vagy az Azure szinapszis Analyticsben, amelyet a kimeneti tábla által használt társított szolgáltatás képvisel. |Yes |
| storedProcedureParameters |A tárolt eljárás paramétereinek értékeinek megadása. Ha egy paraméternél null értéket kell átadnia, használja a következő szintaxist: "param1": null (az összes kisbetű). Tekintse meg a következő mintát, amelyből megtudhatja, hogyan használhatja ezt a tulajdonságot. |No |

Ha megad egy bemeneti adatkészletet, a tárolt eljárási tevékenység futtatásához elérhetőnek kell lennie ("Ready" (kész) állapotban). A bemeneti adatkészlet nem használható paraméterként a tárolt eljárásban. A rendszer csak a tárolt eljárási tevékenység megkezdése előtt használja a függőség ellenőrzését. Meg kell adnia egy kimeneti adatkészletet egy tárolt eljárási tevékenységhez.

A kimeneti adatkészlet meghatározza a tárolt eljárási tevékenység **ütemtervét** (óránként, hetente, havonta stb.). A kimeneti adatkészletnek olyan **társított szolgáltatást** kell használnia, amely egy Azure SQL Database vagy egy Azure szinapszis-elemzésre vagy egy SQL Server-adatbázisra hivatkozik, amelyben a tárolt eljárást futtatni kívánja. A kimeneti adatkészlet képes arra, hogy átadja a tárolt eljárás eredményét egy másik tevékenység ([láncolási tevékenységek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) további feldolgozásához a folyamatban. A Data Factory azonban nem ír automatikusan egy tárolt eljárás kimenetét erre az adatkészletre. Ez a tárolt eljárás, amely egy SQL-táblába ír, amelyre a kimeneti adatkészlet mutat. Bizonyos esetekben a kimeneti adatkészlet lehet egy **próbabábu-adatkészlet** is, amely kizárólag a tárolt eljárási tevékenység futtatási ütemtervének megadására szolgál.

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

További információ: [tárolt eljárási tevékenységgel](data-factory-stored-proc-activity.md) kapcsolatos cikk.

## <a name="net-custom-activity"></a>.NET egyéni tevékenység
A .NET egyéni tevékenység JSON-definíciójában a következő tulajdonságokat adhatja meg. A tevékenység Type tulajdonságának a következőnek kell lennie: **DotNetActivity**. Létre kell hoznia egy Azure HDInsight társított szolgáltatást vagy egy Azure Batch társított szolgáltatást, és meg kell adnia a társított szolgáltatás nevét a **linkedServiceName** tulajdonság értékeként. A **typeProperties** szakaszban a következő tulajdonságok támogatottak, amikor beállítja a tevékenység típusát a DotNetActivity:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| AssemblyName | A szerelvény neve. A példában a: **MyDotnetActivity.dll**. | Yes |
| EntryPoint |Az IDotNetActivity felületet megvalósító osztály neve. A példában a: **MyDotNetActivityNS. MyDotNetActivity** , ahol a MyDotNetActivityNS a névtér, a MyDotNetActivity pedig az osztály.  | Yes |
| PackageLinkedService | Annak az Azure Storage-beli társított szolgáltatásnak a neve, amely az egyéni tevékenység zip-fájlját tartalmazó blob-tárolóra mutat. A példában a: **AzureStorageLinkedService**.| Yes |
| PackageFile | A zip-fájl neve. A példában a: **customactivitycontainer/MyDotNetActivity.zip**. | Yes |
| Extendedproperties példányt paraméterként | Kibővített tulajdonságok, amelyek meghatározhatók és beadhatók a .NET-kódra. Ebben a példában a **SliceStart** változó a SliceStart rendszerváltozón alapuló értékre van állítva. | No |

### <a name="json-example"></a>JSON-példa

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Részletes információ: [egyéni tevékenységek használata Data Factory](data-factory-use-custom-activities.md) cikkben.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi oktatóanyagokat:

- [Oktatóanyag: másolási tevékenységgel rendelkező folyamat létrehozása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Oktatóanyag: folyamat létrehozása struktúra-tevékenységgel](data-factory-build-your-first-pipeline.md)