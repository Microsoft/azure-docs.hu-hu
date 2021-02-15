---
title: Folyamatok létrehozása/beosztása, Láncbeli tevékenységek Data Factory
description: Megtudhatja, hogyan hozhat létre adatfolyamatokat a Azure Data Factoryban az adatáthelyezéshez és-átalakításhoz. Hozzon létre egy adatvezérelt munkafolyamatot a használatra kész adatok létrehozásához.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9cc81a8e157c244828a15ac82913ce9a88c3d34f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376870"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Folyamatok és tevékenységek Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-create-pipelines.md)
> * [2-es verzió (aktuális verzió)](../concepts-pipelines-activities.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [folyamatok a v2-ben](../concepts-pipelines-activities.md)című témakört.

Ennek a cikknek a segítségével megismerheti az Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját adatáthelyezési és -feldolgozási forgatókönyveihez.

> [!NOTE]
> Ez a cikk azt feltételezi, hogy elvégezte a [Azure Data Factory bevezetését](data-factory-introduction.md). Ha nem rendelkezik az adatfeldolgozók létrehozásával kapcsolatos gyakorlati tapasztalatokkal, az [Adatátalakítási oktatóanyag](data-factory-build-your-first-pipeline.md) és/vagy [az adatáthelyezési oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) segítségével jobban megismerheti ezt a cikket.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Előfordulhat például, hogy egy másolási tevékenység használatával másol egy SQL Server-adatbázisból egy Azure-Blob Storageba. Ezt követően egy Hive-tevékenység segítségével futtasson egy Hive-szkriptet egy Azure HDInsight fürtön, amely a Blob Storage-ből származó adatokat feldolgozza vagy átalakítja a kimeneti adatok előállításához. Végül pedig egy második másolási tevékenységgel másolhatja a kimeneti adatokat egy Azure szinapszis Analyticsbe, amely az üzleti intelligencia (BI) jelentéskészítési megoldásaira épül.

Minden tevékenység nulla vagy több bemeneti [adatkészletet](data-factory-create-datasets.md) képes fogadni, és egy vagy több kimeneti [adatkészletet](data-factory-create-datasets.md) képes előállítani. Az alábbi ábrán a folyamat, a tevékenység és az adat-előállító adatkészlete közötti kapcsolat látható:

![A folyamat, a tevékenység és az adatkészlet közötti kapcsolat](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Egy folyamat lehetővé teszi, hogy a tevékenységeket külön készletként kezelje. Például telepítheti, ütemezhet, felfüggesztheti és folytathatja a folyamatokat, ahelyett, hogy egymástól függetlenül kellene foglalkoznia a folyamatban lévő tevékenységekkel.

A Data Factory két típusú tevékenységet támogat: az adattovábbítási tevékenységeket és az adatátalakítási tevékenységeket. Minden tevékenység nulla vagy több bemeneti [adatkészletet](data-factory-create-datasets.md) tartalmazhat, és egy vagy több kimeneti adatkészletet hoz létre.

A bemeneti adatkészletek a folyamattevékenységek bemenetét jelölik, a kimeneti adatkészletek pedig a tevékenységek kimeneteit. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az adatkészlet létrehozását követően használhatja azt egy folyamat tevékenységei esetében. Az adatkészletek lehetnek például egy másolási tevékenység vagy egy HDInsightHive-tevékenység be- vagy kimeneti adatkészletei. Az adatkészletekről további információkat [Az Azure Data Factory adatkészletei](data-factory-create-datasets.md) cikkben talál.

### <a name="data-movement-activities"></a>Adattovábbítási tevékenységek
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

További információkért tekintse meg az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikket.

### <a name="data-transformation-activities"></a>Adatátalakítási tevékenységek
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

További információkért tekintse meg az [adatátalakítási tevékenységekről](data-factory-data-transformation-activities.md) szóló cikket.

### <a name="custom-net-activities"></a>Egyéni .NET-tevékenységek
Ha olyan adattárból/adattárba kell áthelyeznie az adattárakat, amely nem támogatja a másolási tevékenységet, vagy az adatok saját logikával történő átalakítására van szükség, hozzon létre egy **Egyéni .net-tevékenységet**. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Folyamatok ütemezett listája
A folyamat csak a **kezdő** és a **befejező** időpont között aktív. A kezdési időpont előtt vagy a befejezési időpont előtt nem hajtható végre. Ha a folyamat szüneteltetve van, a rendszer nem hajtja végre az indítási és befejezési időponttól függetlenül. Ahhoz, hogy egy folyamat fusson, nem szabad szüneteltetni. Lásd: [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) , hogy megtudja, hogyan működik az ütemezés és a végrehajtás a Azure Data Factoryban.

## <a name="pipeline-json"></a>A folyamat JSON-fájlja
Nézzük meg közelebbről, hogyan történik egy folyamat JSON-formátumban való meghatározása. A folyamatok általános szerkezete a következő:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Címke | Leírás | Kötelező |
| --- | --- | --- |
| name |A folyamat neve. Adjon meg egy, a folyamat által végrehajtandó műveletet jelölő nevet. <br/><ul><li>A karakterek maximális száma: 260</li><li>Betűvel vagy aláhúzással () kell kezdődnie \_</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", " \* ", "%", "&", ":", " \\ "</li></ul> |Yes |
| leírás | Adjon meg egy, az adott folyamat alkalmazását leíró szöveget. |Yes |
| tevékenységek | A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. A tevékenységek JSON-elemével kapcsolatos részletekért tekintse meg a következő szakaszt. | Yes |
| start | A folyamat kezdési dátuma és időpontja. [ISO formátumúnak](https://en.wikipedia.org/wiki/ISO_8601)kell lennie. Példa: `2016-10-14T16:32:41Z`. <br/><br/>Helyi időt is megadhat, például egy EST-időpontot. Íme egy példa: `2016-02-27T06:00:00-05:00` ", amely 6 est.<br/><br/>A kezdő és a záró tulajdonságok együttesen határozzák meg a folyamat aktív időszakát. A kimeneti szeletek csak ebben az aktív időszakban jönnek létre. |No<br/><br/>Ha a end tulajdonsághoz értéket ad meg, meg kell adnia a Start tulajdonság értékét.<br/><br/>Egy folyamat létrehozásához a kezdő és a záró időpont is lehet üres. Mindkét értéket meg kell adnia ahhoz, hogy aktív időszakot állítson be a folyamat futtatásához. Ha nem ad meg kezdési és befejezési időpontot a folyamat létrehozásakor, később beállíthatja őket a Set-AzDataFactoryPipelineActivePeriod parancsmaggal. |
| befejezés | A folyamat befejező dátuma és időpontja. Ha meg van adva, ISO-formátumúnak kell lennie. Például: `2016-10-14T17:32:41Z` <br/><br/>Helyi időt is megadhat, például egy EST-időpontot. Íme egy példa: `2016-02-27T06:00:00-05:00` , amely 6 est.<br/><br/>A folyamat határozatlan ideig történő futtatásához adja meg a 9999-09-09 értéket az end (befejezés) tulajdonsághoz. <br/><br/> A folyamat csak a kezdő és a befejező időpont között aktív. A kezdési időpont előtt vagy a befejezési időpont előtt nem hajtható végre. Ha a folyamat szüneteltetve van, a rendszer nem hajtja végre az indítási és befejezési időponttól függetlenül. Ahhoz, hogy egy folyamat fusson, nem szabad szüneteltetni. Lásd: [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) , hogy megtudja, hogyan működik az ütemezés és a végrehajtás a Azure Data Factoryban. |No <br/><br/>Ha megad egy értéket a Start tulajdonsághoz, meg kell adnia a end (Befejezés) tulajdonság értékét.<br/><br/>Lásd: Megjegyzések a **Start** tulajdonsághoz. |
| Ispaused fogalmak | Ha igaz értékre van állítva, a folyamat nem fut le. Ez a szüneteltetett állapotban van. Alapértelmezett érték = false. Ezt a tulajdonságot használhatja egy folyamat engedélyezéséhez vagy letiltásához. |No |
| pipelineMode | A folyamat futtatásának módszere. Az engedélyezett értékek a következők: ütemezett (alapértelmezett), egyszeri.<br/><br/>Az "ütemezett" érték azt jelzi, hogy a folyamat egy adott időintervallumban fut az aktív időszak (Kezdés és Befejezés időpontja) szerint. Az "egykori" érték azt jelzi, hogy a folyamat csak egyszer fut le. A létrehozott egyszeri folyamatok nem módosíthatók és nem frissíthetők jelenleg. Az egyszeri beállítással kapcsolatos részletekért tekintse meg az [egykori folyamat](#onetime-pipeline) című témakört. |No |
| Expirationtime tulajdonságok | Az a létrehozás utáni időtartam, ameddig az [egyszeri folyamat](#onetime-pipeline) érvényes, és a kiépítés előtt kell állnia. Ha nem rendelkezik aktív, sikertelen vagy függőben lévő futtatással, a folyamat automatikusan törlődik, amint eléri a lejárati időt. Az alapértelmezett érték: `"expirationTime": "3.00:00:00"`|No |
| adathalmazok |A folyamat során meghatározott tevékenységek által használandó adatkészletek listája. Ezzel a tulajdonsággal definiálhatja az erre a folyamatra jellemző adatkészleteket, és nem határozható meg az adat-előállítón belül. A folyamaton belül definiált adatkészleteket csak ez a folyamat használhatja, és nem osztható meg. A részletekért lásd a [hatókörrel rendelkező adatkészleteket](data-factory-create-datasets.md#scoped-datasets) . |No |

## <a name="activity-json"></a>Tevékenység JSON-fájlja
A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. Minden tevékenység a következő legfelső szintű struktúrával rendelkezik:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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

Az alábbi táblában a tevékenység JSON-definíciójában lévő tulajdonságok szerepelnek:

| Címke | Leírás | Kötelező |
| --- | --- | --- |
| name | A tevékenység neve. Adjon meg egy, a tevékenység által végrehajtandó műveletet jelölő nevet. <br/><ul><li>A karakterek maximális száma: 260</li><li>Betűvel vagy aláhúzással () kell kezdődnie \_</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":" \\ , ""</li></ul> |Yes |
| leírás | Az adott tevékenységet vagy annak alkalmazását leíró szöveg |Yes |
| típus | A tevékenység típusa. Tekintse meg az [adattovábbítási tevékenységek](#data-movement-activities) és az [Adatátalakítási tevékenységek](#data-transformation-activities) szakaszt a különböző típusú tevékenységekhez. |Yes |
| bemenetek |A tevékenység által használt bemeneti táblák<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Yes |
| kimenetek |A tevékenység által használt kimeneti táblák.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Yes |
| linkedServiceName |A tevékenység által használt társított szolgáltatás neve. <br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. |Igen a HDInsight és a Azure Machine Learning Studio (klasszikus) kötegelt pontozási tevékenységhez <br/><br/>Minden egyéb esetében: nem |
| typeProperties |A **typeProperties** szakaszban található tulajdonságok a tevékenység típusától függenek. Az adott tevékenység típustulajdonságainak megtekintéséhez kattintson az előző szakaszban szereplő tevékenységhivatkozásokra. | No |
| szabályzat |Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ha nincs megadva, a rendszer az alapértelmezett szabályzatokat használja. |No |
| scheduler | a "Scheduler" tulajdonság a tevékenység kívánt ütemezésének meghatározására szolgál. Az altulajdonságok ugyanazok, mint az [adatkészlet rendelkezésre állási tulajdonságában](data-factory-create-datasets.md#dataset-availability)lévők. |No |

### <a name="policies"></a>Házirendek
A házirendek hatással vannak egy tevékenység futásidejű viselkedésére, különösen akkor, ha egy tábla szeletét dolgozzák fel. A részleteket a következő táblázat tartalmazza.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Description |
| --- | --- | --- | --- |
| Egyidejűség |Egész szám <br/><br/>Maximális érték: 10 |1 |A tevékenység egyidejű végrehajtásának száma.<br/><br/>Meghatározza, hogy hány párhuzamos tevékenység-végrehajtás történhet a különböző szeleteken. Ha például egy tevékenységnek az elérhető adatmennyiség nagy készletén kell haladnia, a nagyobb párhuzamossági érték felgyorsítja az adatfeldolgozást. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza a feldolgozás alatt álló adatszeletek sorrendjét.<br/><br/>Ha például 2 szelete van (egy 16:00-kor történik, egy másik pedig 5 órakor), és mindkettő függőben van. Ha úgy állítja be a executionPriorityOrder, hogy a NewestFirst, a szeletet 5 ÓRAKOR dolgozza fel a rendszer. Hasonlóképpen, ha úgy állítja be a executionPriorityORder, hogy a OldestFIrst legyen, akkor a szelet 4 ÓRAKOR lesz feldolgozva. |
| retry |Egész szám<br/><br/>A maximális érték lehet 10 |0 |Az újrapróbálkozások száma, mielőtt a szelet adatfeldolgozása sikertelenként van megjelölve. Az adatszeletek tevékenység-végrehajtásának újrapróbálkozása a megadott újrapróbálkozások számával történik. Az újrapróbálkozás a hiba után a lehető leghamarabb megtörténik. |
| timeout |időtartam |00:00:00 |A tevékenység időtúllépése. Példa: 00:10:00 (a 10 perc időtúllépését jelenti)<br/><br/>Ha egy érték nincs megadva vagy 0, az időtúllépés végtelen.<br/><br/>Ha egy szelet adatfeldolgozási ideje meghaladja az időtúllépési értéket, a rendszer megszakítja, és a rendszer megkísérli a feldolgozást. Az újrapróbálkozások száma az Újrapróbálkozás tulajdonságtól függ. Időtúllépés esetén az állapot időtúllépés értékre van állítva. |
| késedelem |időtartam |00:00:00 |A szelet adatfeldolgozásának megkezdése előtti késleltetés meghatározása.<br/><br/>Az adatszeletek tevékenységének végrehajtása akkor indul el, ha a késés a várt végrehajtási idő alatt van.<br/><br/>Példa: 00:10:00 (a 10 perc késleltetését jelenti) |
| longRetry |Egész szám<br/><br/>Maximális érték: 10 |1 |A hosszú újrapróbálkozási kísérletek száma a szelet végrehajtásának meghiúsulása előtt.<br/><br/>a longRetry-kísérletek longRetryInterval szerint vannak elfoglalva. Ha tehát az újrapróbálkozási kísérletek közötti időt kell megadnia, használja a longRetry. Ha az újrapróbálkozási és a longRetry is meg van adva, az egyes longRetry-kísérletek az újrapróbálkozási kísérleteket is tartalmazzák, és a kísérletek maximális száma újrapróbálkozás * longRetry.<br/><br/>Ha például a következő beállítások szerepelnek a tevékenység-házirendben:<br/>Újrapróbálkozás: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, hogy csak egy szeletet kell végrehajtani (az állapot várakozik), és a tevékenység végrehajtása minden alkalommal meghiúsul. Kezdetben 3 egymást követő végrehajtási kísérlet lenne. Minden kísérlet után a szelet állapota újra próbálkozik. Az első 3 próbálkozás után a szelet állapota LongRetry lesz.<br/><br/>Egy óra (azaz a longRetryInteval értéke) után egy másik 3 egymást követő végrehajtási kísérlet lenne. Ezt követően a szelet állapota meghiúsul, és a rendszer nem próbálkozik újra. Ezért összesen 6 kísérlet történt.<br/><br/>Ha bármelyik végrehajtás sikeres, a szelet állapota készen áll, és a rendszer nem próbálkozik újra.<br/><br/>a longRetry olyan helyzetekben használhatók, ahol a függő adat nem determinisztikus időpontokban érkezik, vagy az általános környezet az adatfeldolgozási folyamat alatt álló adatfeldolgozás. Ilyen esetekben előfordulhat, hogy egy másik után újrapróbálkozik, és a kívánt kimenet eltelte után egy idő elteltével nem jár sikerrel.<br/><br/>Figyelmeztetés: ne állítson be magas értéket a longRetry vagy a longRetryInterval. A magasabb értékek jellemzően más rendszerszintű problémákat jelentenek. |
| longRetryInterval |időtartam |00:00:00 |A hosszú újrapróbálkozási kísérletek közötti késleltetés |

## <a name="sample-copy-pipeline"></a>Minta másolási folyamat
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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Vegye figyelembe a következő szempontokat:

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****Copy** értékre van beállítva.
* A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**. Az adatkészletek JSON-fáljban történő meghatározását lásd az [Adatkészletek](data-factory-create-datasets.md) cikket.
* A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. Az adatátviteli [tevékenységek](#data-movement-activities) szakaszban kattintson arra az adattárra, amelyet forrásként vagy fogadóként kíván használni, és többet szeretne megtudni az adatoknak az adattárba való áthelyezéséről.

A folyamat létrehozásának teljes áttekintését az [oktatóanyag: adatok másolása blob Storageról SQL Databasera](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)című témakörben tekintheti meg.

## <a name="sample-transformation-pipeline"></a>Minta átalakítási folyamat
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Vegye figyelembe a következő szempontokat:

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****HDInsightHive** értékre van beállítva.
* A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **AzureStorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.
* A `defines` szakasz a kaptári parancsfájlnak átadott futásidejű beállítások megadására szolgál kaptár-konfigurációs értékekként (például `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` ).

A **typeProperties** szakasz eltérő az egyes átalakítási tevékenységek esetében. Az átalakítási tevékenységhez támogatott típus-tulajdonságok megismeréséhez kattintson az átalakítás tevékenységre az [Adatátalakítási tevékenységek](#data-transformation-activities) táblázatban.

A folyamat létrehozásának teljes áttekintését az [oktatóanyag: az első folyamat létrehozása az Hadoop-fürttel történő adatfeldolgozáshoz](data-factory-build-your-first-pipeline.md)című témakörben tekintheti meg.

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Az előző két mintában a folyamatok csak egyetlen tevékenységet tartalmaztak. Egy folyamathoz azonban több tevékenység is tartozhat.

Ha egy folyamaton belül több tevékenység van, és egy tevékenység kimenete nem egy másik tevékenység bemenete, akkor a tevékenységek párhuzamosan futhatnak, ha a tevékenységekhez tartozó bemeneti adatszeletek készen állnak.

Két tevékenységet is láncba helyezhet, ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészletének adja. A második tevékenység csak akkor fut le, ha az első sikeresen befejeződik.

![Tevékenységek láncolása ugyanabban a folyamatban](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Ebben a példában a folyamat két tevékenységgel rendelkezik: Activity1 és Activity2. A Activity1 bemenetként veszi át a Dataset1 elemet, és kimeneti Dataset2 hoz létre. A tevékenység bemenetként veszi fel a Dataset2, és kimeneti Dataset3 hoz létre. Mivel a Activity1 (Dataset2) kimenete a Activity2 bemenete, a Activity2 csak azt követően fut le, hogy a tevékenység sikeresen befejeződött, és létrehozza a Dataset2 szeletet. Ha a Activity1 valamilyen okból meghiúsul, és nem hozza létre a Dataset2 szeletet, a 2. tevékenység nem fut az adott szelet esetében (például: 9 – 10 AM).

A különböző folyamatokban lévő tevékenységeket is láncba állíthatja.

![Tevékenységek láncolása két folyamatban](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Ebben a példában a Pipeline1 csak egy tevékenységgel rendelkezik, amely bemenetként Dataset1 elemet, és kimenetként hozza létre a Dataset2. A Pipeline2 is csak egy tevékenységgel rendelkezik, amely bemenetként és Dataset3 Dataset2 a kimenetként.

További információ: [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Folyamatok létrehozása és figyelése
Ezen eszközök vagy SDK-k egyikével hozhat létre folyamatokat.

- Másolás varázsló
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-sablon
- REST API
- .NET API

A következő oktatóanyagok részletes útmutatást biztosítanak a folyamatok ezen eszközök vagy SDK-k használatával történő létrehozásához.

- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline.md)
- [Adatátviteli tevékenységgel rendelkező folyamat létrehozása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

A folyamat létrehozása/üzembe helyezése után a folyamatokat a Azure Portal-pengék segítségével kezelheti és figyelheti, illetve felügyelheti és kezelheti az alkalmazást. Részletes útmutatást a következő témakörökben talál.

- [Folyamatok figyelése és kezelése Azure Portal-pengék használatával](data-factory-monitor-manage-pipelines.md).
- [Folyamatok figyelése és felügyelete az alkalmazás figyelése és kezelése használatával](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Egykori folyamat
A folyamatot a folyamat definíciójában megadott kezdési és befejezési időpontokon belül rendszeres időközönként (például óránként vagy naponta) is létrehozhatja és ütemezhetheti. További részletek: ütemezési tevékenységek. Létrehozhat egy olyan folyamatot is, amely csak egyszer fut le. Ehhez állítsa az **pipelineMode** tulajdonságot a folyamat definíciójában az **egykori** értékre a következő JSON-mintában látható módon. A tulajdonság alapértelmezett értéke **ütemezett**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
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
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Vegye figyelembe a következőket:

* A folyamat **kezdési** és **befejezési** időpontja nincs megadva.
* A bemeneti és a kimeneti adatkészletek **rendelkezésre állása** van megadva (**gyakoriság** és **időköz**), bár Data Factory nem használja az értékeket.
* A diagram nézet nem jeleníti meg az egyszeri folyamatokat. Ez a működésmód szándékos.
* Az egyszeri folyamatok nem frissíthetők. Létrehozhat egy egyszeri folyamatot, átnevezheti, frissítheti a tulajdonságokat, és üzembe helyezheti egy másik létrehozásához.

## <a name="next-steps"></a>Következő lépések
- Az adatkészletekről további információt az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben talál.
- További információ a folyamatok ütemezéséről és végrehajtásáról: [Azure Data Factory cikk ütemezése és végrehajtása](data-factory-scheduling-and-execution.md) .
