---
title: Azure Data Factory – gyakran ismételt kérdések
description: Gyakori kérdések a Azure Data Factoryról.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4ddadec327f1a4f6aa22d51ac073b9aebb0dee82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383194"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory – gyakran ismételt kérdések
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [Gyakori kérdések – Data Factory](../frequently-asked-questions.md)című témakört.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amely **automatizálja az adatátvitelt és-átalakítást**. Csakúgy, mint egy olyan gyár, amely a berendezéseket a nyersanyagok begyűjtésére és a kész termékekre alakítja, Data Factory összehangolja a meglévő olyan szolgáltatásokat, amelyek nyers adatokat gyűjtenek, és a használatra kész információkra alakítják át azokat.

Data Factory lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre az adatok áthelyezéséhez a helyszíni és a Felhőbeli adattárak között, valamint az adatok feldolgozását/átalakítását számítási szolgáltatásokkal, például az Azure HDInsight és a Azure Data Lake Analytics használatával. Ha olyan folyamatot hoz létre, amely a szükséges műveletet végzi, rendszeres időközönként (óránként, naponta, hetente stb.) futtathatja azt.   

További információ: [áttekintés &](data-factory-introduction.md)alapfogalmak.

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Hol találhatók a Azure Data Factory díjszabása?
A Azure Data Factory díjszabását a [Data Factory díjszabását ismertető oldalon][adf-pricing-details] tekintheti meg.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hogyan első lépések a Azure Data Factory?
* A Azure Data Factory áttekintését lásd: [a Azure Data Factory bemutatása](data-factory-introduction.md).
* Az adatok másolási tevékenységgel történő **másolásával/áthelyezésével** kapcsolatos oktatóanyagért lásd: [adatok másolása az Azure Blob Storageból a Azure SQL Databaseba](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Útmutató az **adatátalakításhoz** a HDInsight-struktúra tevékenység használatával. Lásd: az [adatfeldolgozás a kaptár-szkript futtatásával a Hadoop-fürtön](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Mi a Data Factory régió elérhetősége?
Data Factory az **USA nyugati** régiójában és **Észak-Európában** érhető el. Az adatfeldolgozók által használt számítási és tárolási szolgáltatások más régiókban is lehetnek. Lásd: [támogatott régiók](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Milyen korlátozások vonatkoznak az adat-előállítók/folyamatok/tevékenységek/adatkészletek számára?
Lásd: az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) című cikk **Azure Data Factory korlátozások** szakasza.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Mi a szerzői/fejlesztői élmény a Azure Data Factory szolgáltatással?
A következő eszközök/SDK-k egyikével hozhat létre vagy hozhat létre adatelőállítókat:

* **Visual Studio** A Visual Studióval létrehozhat egy Azure-beli adatgyárat. A részletekért lásd: [az első adatfolyamat létrehozása a Visual Studióval](data-factory-build-your-first-pipeline-using-vs.md) .
* **Azure PowerShell** Lásd: [Azure Data Factory létrehozása és figyelése a Azure PowerShell használatával](data-factory-build-your-first-pipeline-using-powershell.md) egy oktatóanyaghoz/bemutatóhoz, amely egy adatelőállítót hoz létre a PowerShell használatával. Data Factory-parancsmagok átfogó dokumentációját a [Data Factory parancsmag hivatkozási][adf-powershell-reference] tartalma az MSDN Library-ben című témakörben találja.
* **.Net-osztály könyvtára** Data Factory .NET SDK-val programozott módon hozhat létre adatgyárat. A .net SDK használatával megtekintheti az adatelőállítók létrehozásával [, figyelésével és kezelésével](data-factory-create-data-factories-programmatically.md) kapcsolatos tudnivalókat. Tekintse meg a Data Factory .NET SDK átfogó dokumentációját [Data Factory a Class Library-referenciát][msdn-class-library-reference] .
* **REST API** A Azure Data Factory szolgáltatás által elérhetővé tett REST API is használhatja az adatüzemek létrehozásához és üzembe helyezéséhez. Az Data Factory REST API átfogó dokumentációját [Data Factory REST API referenciával][msdn-rest-api-reference] foglalkozó témakörben talál.
* **Azure Resource Manager sablon** Lásd [: oktatóanyag: az első Azure-beli adat-előállító létrehozása Azure Resource Manager sablon](data-factory-build-your-first-pipeline-using-arm.md) részletes ismertetése alapján.

### <a name="can-i-rename-a-data-factory"></a>Átnevezhetem egy adatgyárat?
Nem. A többi Azure-erőforráshoz hasonlóan az Azure-beli adatelőállítók neve nem módosítható.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Áthelyezhetek egy adatgyárat egy Azure-előfizetésből egy másikba?
Igen. A következő ábrán látható módon használja a " **Move** " gombot a saját adatgyár paneljén:

![Az adatfeldolgozó áthelyezése](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Mik a Data Factory által támogatott számítási környezetek?
A következő táblázat felsorolja a Data Factory által támogatott számítási környezeteket és a rajtuk futó tevékenységeket.

| Számítási környezet | tevékenységek |
| --- | --- |
| [Igény szerinti HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) vagy [a saját HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [kaptár](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klasszikus)](data-factory-compute-linked-services.md#azure-machine-learning-studio-classic-linked-service) |[Studio (klasszikus) tevékenységek: kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure szinapszis Analytics](data-factory-compute-linked-services.md#azure-synapse-analytics-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Tárolt eljárás](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hogyan hasonlítható össze Azure Data Factory a SQL Server Integration Servicesval (SSIS)? 
Tekintse meg a [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) bemutatót az egyik MVP (legértékesebb szakemberektől): Reza rad. Előfordulhat, hogy a Data Factory közelmúltbeli módosításai nem szerepelnek a dia-pakliban. Folyamatosan bővítjük a Azure Data Factory további képességeit. Folyamatosan bővítjük a Azure Data Factory további képességeit. Ezeket a frissítéseket a Microsoft adatintegrációs technológiáinak az év későbbi részében való összehasonlításával fogjuk egyesíteni.   

## <a name="activities---faq"></a>Tevékenységek – gyakori kérdések
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Milyen típusú tevékenységeket használhat a Data Factory folyamatokban?
* Adatátviteli [tevékenységek](data-factory-data-movement-activities.md) az adatáthelyezéshez.
* [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) az adatfeldolgozáshoz/átalakításhoz.

### <a name="when-does-an-activity-run"></a>Mikor fut a tevékenység?
A kimeneti adattábla **rendelkezésre állási** konfigurációs beállítása határozza meg, hogy mikor fusson a tevékenység. Ha a bemeneti adatkészletek meg vannak adva, a tevékenység ellenőrzi, hogy az összes bemeneti adatfüggőség teljesül-e (azaz **kész** állapotban van) a futás megkezdése előtt.

## <a name="copy-activity---faq"></a>Másolási tevékenység – gyakori kérdések
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Jobb, ha több tevékenységgel rendelkező folyamattal vagy külön folyamattal rendelkezik az egyes tevékenységekhez?
A folyamatoknak a kapcsolódó tevékenységeket kellene összecsomagolni. Ha az azokat összekapcsoló adatkészleteket a folyamaton kívüli más tevékenységek nem használják, akkor egy folyamaton belül megtarthatja a tevékenységeket. Így nem kell összekapcsolnia a folyamat aktív időszakait, hogy azok illeszkedjenek egymáshoz. Emellett a folyamaton belüli belső táblákban lévő adatok integritása jobban megőrzi a folyamat frissítésekor. A folyamat frissítése lényegében leállítja az összes tevékenységet a folyamaton belül, eltávolítja őket, és újból létrehozza azokat. A létrehozási perspektívából a folyamat egy JSON-fájljában található kapcsolódó tevékenységekben lévő adatok áramlását is könnyebben megtekintheti.

### <a name="what-are-the-supported-data-stores"></a>Mik a támogatott adattárak?
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

### <a name="what-are-the-supported-file-formats"></a>Mik a támogatott fájlformátumok?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Hol történt a másolási művelet végrehajtása?
Részletekért tekintse meg a [globálisan elérhető adatáthelyezés](data-factory-data-movement-activities.md#global) szakaszt. Röviden, ha egy helyszíni adattárra van szó, a másolási műveletet a helyszíni környezetben lévő adatkezelés átjáró hajtja végre. Ha pedig az adatáthelyezés két felhőalapú tároló között van, a másolási műveletet a fogadó helyéhez legközelebb eső régióban hajtja végre.

## <a name="hdinsight-activity---faq"></a>HDInsight-tevékenység – gyakori kérdések
### <a name="what-regions-are-supported-by-hdinsight"></a>Milyen régiókat támogat a HDInsight?
Tekintse meg a földrajzi elérhetőség szakaszt a következő cikkben: vagy a [HDInsight díjszabása][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Milyen régiót használ egy igény szerinti HDInsight-fürt?
Az igény szerinti HDInsight-fürt ugyanabban a régióban jön létre, ahol a fürthöz való használatra megadott tárterület létezik.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>További Storage-fiókok hozzárendelése a HDInsight-fürthöz
Ha saját HDInsight-fürtöt használ (BYOC saját fürtöt), tekintse meg a következő témaköröket:

* [HDInsight-fürt használata alternatív Storage-fiókokkal és Metaadattárak][hdinsight-alternate-storage]
* [További Storage-fiókok használata a HDInsight-struktúrával][hdinsight-alternate-storage-2]

Ha igény szerinti fürtöt használ, amelyet a Data Factory szolgáltatás hozott létre, adja meg a HDInsight társított szolgáltatás további Storage-fiókjait, hogy a Data Factory szolgáltatás regisztrálja őket az Ön nevében. Az igény szerinti társított szolgáltatás JSON-definíciójában használja a **additionalLinkedServiceNames** tulajdonságot a másodlagos Storage-fiókok megadásához, ahogy az a következő JSON-kódrészletben látható:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
A fenti példában a otherLinkedServiceName1 és a otherLinkedServiceName2 olyan társított szolgáltatásokat jelent, amelyek definíciói olyan hitelesítő adatokat tartalmaznak, amelyekkel a HDInsight-fürtnek hozzá kell férnie a másodlagos Storage-fiókokhoz

## <a name="slices---faq"></a>Szeletek – gyakori kérdések
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Miért nem kész állapotban vannak a bemeneti szeletek?
Gyakori hiba, hogy a bemeneti adatkészlet nem állítja be a **külső** tulajdonságot **igaz** értékre, ha a bemeneti adatok kívül vannak az adat-előállítón (amelyet a adat-előállító nem állít elő).

A következő példában a **DataSet1 elemet**-on csak az igaz értékre kell beállítani a **külsőt** .  

**DataFactory1** 1. folyamat: DataSet1 elemet-> activity1-> dataset2-> activity2-> dataset3-folyamat 2: dataset3-> activity3-> dataset4

Ha van egy másik adat-előállítója egy olyan folyamattal, amely a dataset4-t (a 2. folyamat által előállított 1. adatcsatorna alapján hozza létre), akkor a dataset4 külső adatkészletként kell megjelölni, mert az adatkészletet egy másik adat-előállító (DataFactory1, nem DataFactory2)  

**DataFactory2**    
1. folyamat: dataset4->activity4->dataset5

Ha a külső tulajdonság megfelelően van beállítva, ellenőrizze, hogy a bemeneti adatok szerepelnek-e a bemeneti adatkészlet definíciójában megadott helyen.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hogyan futtathat egy szeletet az éjféltől eltérő időpontban, amikor a szeletet naponta állítják elő?
Az **eltolás** tulajdonsággal adhatja meg, hogy milyen időpontra kívánja készíteni a szeletet. Ennek a tulajdonságnak a részleteit az [adatkészletek rendelkezésre állása](data-factory-create-datasets.md#dataset-availability) című szakaszban találja. Íme egy gyors példa:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
A napi szeletek az alapértelmezett éjfél helyett **6 órakor** kezdődnek.     

### <a name="how-can-i-rerun-a-slice"></a>Hogyan lehet újrafuttatni egy szeletet?
A szeleteket a következő módszerek egyikével futtathatja újra:

* Egy tevékenység ablakának vagy szeletének újrafuttatásához használja az alkalmazás figyelése és kezelése lehetőséget. További útmutatásért lásd: a [kijelölt tevékenység ablakának újrafuttatása](data-factory-monitor-manage-app.md#perform-batch-actions) .   
* Kattintson a **Run (Futtatás** ) elemre az **adatszelet** panelének parancssáv paneljén a Azure Portal.
* Futtassa a **set-AzDataFactorySliceStatus** parancsmagot az állapot beállítással, hogy a szeletre **várjon** .   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  A parancsmag részleteit a [set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] című témakörben tekintheti meg.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Mennyi ideig tartott egy szelet feldolgozása?
A figyelés & az alkalmazás kezelése ablakban megtudhatja, hogy mennyi időt vett igénybe az adatszelet feldolgozása. A részletekért lásd: [Activity Window Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) .

A Azure Portal a következőket is elvégezheti:  

1. Az adat-előállító paneljén **kattintson az** **adatkészletek** csempére.
2. Kattintson az **adatkészletek** panelen található adott adatkészletre.
3. Válassza ki azt a szeletet, amelyet a **tábla** panel **legutóbbi szeletek** listájából szeretne felkeresni.
4. Kattintson az **ADATszelet** panel **tevékenység** -futtatások listájában a tevékenység futtatása elemre.
5. Kattintson a **Tulajdonságok** csempére a **tevékenység futtatása részletek** panelen.
6. Az **időtartam** mezőt egy értékkel kell látnia. Ez az érték a szelet feldolgozásához szükséges idő.   

### <a name="how-to-stop-a-running-slice"></a>Futó szelet leállítása
Ha le kell állítania a folyamatot a végrehajtásból, használhatja a [felfüggesztés-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) parancsmagot. Jelenleg a folyamat felfüggesztése nem állítja le a folyamatban lévő szeletek végrehajtását. A folyamatban lévő végrehajtások befejezése után a rendszer nem vesz fel külön szeletet.

Ha az összes végrehajtást azonnal le szeretné állítani, az egyetlen módszer a folyamat törlése és újbóli létrehozása. Ha a folyamat törlését választja, nem kell törölnie a folyamat által használt táblákat és társított szolgáltatásokat.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: /archive/blogs/cindygross/use-additional-storage-accounts-with-hdinsight-hive