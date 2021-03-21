---
title: Folyamatos átviteli események rögzítése – Azure Event Hubs | Microsoft Docs
description: Ez a cikk áttekintést nyújt a capture szolgáltatásról, amely lehetővé teszi az Azure-Event Hubs keresztüli adatfolyamok rögzítését.
ms.topic: article
ms.date: 02/16/2021
ms.openlocfilehash: 9f0ec1223c06b908a9aa9f3ac5c5b19ead2fe962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100595949"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Események rögzítése Azure-Event Hubs az Azure-ban Blob Storage vagy Azure Data Lake Storage
Az Azure Event Hubs lehetővé teszi, hogy automatikusan rögzítsen egy [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) -ban található Event Hubs adatfolyam-adatait, vagy [Azure Data Lake Storage 1. vagy 2. generációs](https://azure.microsoft.com/services/data-lake-store/) fiókot, és a rugalmasságot az idő vagy a méret megadásával. A rögzítés beállítása gyorsan elvégezhető, ezért a futtatásához nincs szükség felügyeleti költségekre, és a rendszer automatikusan méretezi Event Hubs [átviteli egységekkel](event-hubs-scalability.md#throughput-units). A Event Hubs Capture a legegyszerűbb módszer az adatfolyamok Azure-ba való betöltésére, és lehetővé teszi az adatfeldolgozásra koncentrálva az adatrögzítés helyett.

> [!NOTE]
> Ha úgy konfigurálja a Event Hubs rögzítést, hogy az Azure Data Lake Storage **Gen 2** legyen használatban, ugyanúgy, mint egy Azure-Blob Storage használatára. Részletekért lásd: [Event Hubs rögzítésének konfigurálása](event-hubs-capture-enable-through-portal.md). 

A Event Hubs Capture lehetővé teszi a valós idejű és a Batch-alapú folyamatok feldolgozását ugyanazon az adatfolyamon. Ez azt jelenti, hogy olyan megoldásokat hozhat létre, amelyek az igényeknek megfelelően növekednek. Akár még ma is felhasználja a Batch-alapú rendszereket a jövőbeli valós idejű feldolgozás irányába, vagy ha egy meglévő valós idejű megoldáshoz hatékony, hideg elérési utat szeretne hozzáadni, Event Hubs a rögzítés megkönnyíti a folyamatos átviteli adatátvitelt.

> [!IMPORTANT]
> A célként megadott tárterület (Azure Storage vagy Azure Data Lake Storage) fióknak ugyanabban az előfizetésben kell lennie, mint az Event hub-nak. 

## <a name="how-event-hubs-capture-works"></a>A Event Hubs rögzítésének működése

A Event Hubs egy elosztott naplóhoz hasonlóan a telemetria-beáramláshoz használt időmegőrzési tartós puffer. Event Hubs a méretezés kulcsa a [particionált fogyasztói modell](event-hubs-scalability.md#partitions). Az egyes partíciók egymástól független adatszegmensek, és egymástól függetlenül kerülnek felhasználásra. Az adatok időbeli kiesése a konfigurálható megőrzési időtartam alapján. Ennek eredményeképpen egy adott Event hub soha nem "túl teljes" lesz.

Event Hubs rögzítéssel megadhatja saját Azure Blob Storage-fiókját és tárolóját, vagy Azure Data Lake Storage fiókot, amely a rögzített adattárolók tárolására szolgál. Ezek a fiókok lehetnek ugyanabban a régióban, mint az Event hub vagy egy másik régió, amely a Event Hubs rögzítési funkciójának rugalmasságát adja.

A rögzített adatrögzítés [Apache Avro][Apache Avro] formátumban történik: egy kompakt, gyors, bináris formátum, amely beágyazott sémával rendelkező, gazdag adatstruktúrákat biztosít. Ezt a formátumot széles körben használják a Hadoop ökoszisztémájában, Stream Analytics és Azure Data Factory. A Avro használatáról a cikk későbbi részében talál további információt.

### <a name="capture-windowing"></a>Ablak rögzítése

A Event Hubs Capture lehetővé teszi egy ablak beállítását a rögzítés vezérléséhez. Ez az ablak egy "első WINS-házirenddel" rendelkező minimális méret-és időkonfiguráció, ami azt jelenti, hogy az első eseményindító rögzítési műveletet okoz. Ha egy 15 perces, 100 MB-os rögzítési ablakmal rendelkezik, és másodpercenként 1 MB-ot küld, akkor a méret ablak az időablak előtt aktiválódik. Az egyes partíciók egymástól függetlenül rögzítik, és egy befejezett blokk blobot írnak a rögzítés időpontjában, amely a rögzítési időköz előfordulásának időpontjában lett elnevezve. A tárolási elnevezési konvenció a következő:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

A DÁTUMÉRTÉK nulla értékkel van feltöltve. a fájlnév például a következőket teheti:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Abban az esetben, ha az Azure Storage-blob átmenetileg nem érhető el, Event Hubs Capture megőrzi az adatait az Event hub-on beállított adatmegőrzési időszakra vonatkozóan, és a Storage-fiók újbóli rendelkezésre állása után ismét kitölti az adatok kitöltését.

### <a name="scaling-to-throughput-units"></a>Méretezés az átviteli egységekre

Event Hubs forgalmat az [átviteli egységek](event-hubs-scalability.md#throughput-units)vezérlik. Egy átviteli egység 1 MB/s vagy 1000 eseményt tesz lehetővé másodpercenként a bejövő forgalomhoz, és kétszer annyi kimenő forgalmat. A standard Event Hubs 1-20 átviteli egységekkel konfigurálható, és a kvóták növelésére vonatkozó [támogatási kéréssel][support request]többet is megvásárolhat. A megvásárolt átviteli egységeken túli használat szabályozása megtörténik. Event Hubs a rögzítés közvetlenül a belső Event Hubs tárterületről másolja át az adatait, kihagyva az átviteli egység kimenő kvótáit, és menti a kimenő forgalmat más feldolgozó olvasók, például a Stream Analytics vagy a Spark számára.

A konfigurálást követően Event Hubs rögzítés automatikusan elindul, amikor elküldi az első eseményt, és folytatja a futást. Annak érdekében, hogy könnyebb legyen az alsóbb rétegbeli feldolgozás, hogy tudja, hogy a folyamat működik, Event Hubs üres fájlokat ír, amikor nincs adatot. Ez a folyamat előre jelezhető ritmust és jelölőt biztosít, amely képes a Batch-processzorok takarmányozására.

## <a name="setting-up-event-hubs-capture"></a>Event Hubs rögzítésének beállítása

A rögzítést az Event hub létrehozási idején konfigurálhatja a [Azure Portal](https://portal.azure.com)használatával, vagy Azure Resource Manager sablonok használatával. További információért tekintse át a következő cikkeket:

- [Az Event Hubs Capture engedélyezése az Azure Portal használatával](event-hubs-capture-enable-through-portal.md)
- [Event Hubs-névtér létrehozása egy eseményközponttal és a Rögzítés funkció engedélyezése az Azure Resource Manager-sablonjának használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

> [!NOTE]
> Ha engedélyezi a rögzítési funkciót egy meglévő Event hub esetében, a szolgáltatás a funkció bekapcsolása **után** rögzíti az Event hub-ra érkező eseményeket. A funkció bekapcsolása előtt nem rögzíti az Event hub-ban létezett eseményeket. 

## <a name="exploring-the-captured-files-and-working-with-avro"></a>A rögzített fájlok feltárása és a Avro használata

Event Hubs rögzítés Avro formátumban hozza létre a fájlokat a beállított időablakban megadott módon. Ezeket a fájlokat bármely eszközön megtekintheti, például [Azure Storage Explorer][Azure Storage Explorer]. A fájlokat helyileg is letöltheti a munkához.

A Event Hubs Capture által létrehozott fájlok a következő Avro-sémával rendelkeznek:

![Avro séma][3]

A Avro-fájlok felfedezésének egyszerű módja a [Avro Tools][Avro Tools] jar használata az Apache-ból. Az [Apache-részletezéssel][Apache Drill] egyszerűsített SQL-alapú élményt és [Apache Spark][Apache Spark] is használhat a betöltött adatok összetett elosztott feldolgozásának elvégzéséhez. 

### <a name="use-apache-drill"></a>Apache-részletezés használata

Az [Apache Drill][Apache Drill] egy "nyílt forráskódú SQL-lekérdezési motor a Big adatfeltáráshoz", amely strukturált és részben strukturált adatforrásokat képes lekérdezni, bárhol is legyenek. A motor a nagyszerű teljesítmény érdekében önálló csomópontként vagy hatalmas fürtként is futtatható.

Az Azure Blob Storage natív támogatása elérhető, amely megkönnyíti az adatlekérdezést egy Avro-fájlban a dokumentációban leírtak szerint:

[Apache Drill: Azure Blob Storage beépülő modul][Apache Drill: Azure Blob Storage Plugin]

A rögzített fájlok egyszerű lekérdezéséhez létrehozhat és futtathat egy virtuális gépet, amely az Apache-részletezés használatával engedélyezhető egy tárolón keresztül az Azure Blob Storage eléréséhez. Tekintse meg a következő mintát: nagy [léptékű Streaming Event Hubs rögzítéssel](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-capture).

### <a name="use-apache-spark"></a>Apache Spark használata

A [Apache Spark][Apache Spark] egy "egységes analitikai motor a nagyméretű adatfeldolgozáshoz". Más nyelveket is támogat, beleértve az SQL-t, és könnyen elérheti az Azure Blob Storage-t. Az Azure-ban néhány lehetőség van Apache Spark futtatására, és az Azure Blob Storage-hoz való könnyű hozzáférést biztosít:

- [HDInsight: az Azure Storage-ban tárolt fájlok címei][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob Storage][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Avro-eszközök használata

A [Avro-eszközök][Avro Tools] jar-csomagként érhetők el. A jar-fájl letöltése után a következő parancs futtatásával megtekintheti egy adott Avro-fájl sémáját:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Ez a parancs visszaadja

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

A Avro-eszközök használatával a fájlt JSON formátumba alakíthatja át, és más feldolgozást is végezhet.

A fejlettebb feldolgozáshoz töltse le és telepítse a Avro az Ön által választott platformra. Az írás időpontjában a következő implementációk érhetők el: C, C++, C \# , Java, NodeJS, Perl, php, Python és Ruby.

Az Apache Avro teljes körű Első lépések útmutatók a [Javához][Java] és a [Pythonhoz][Python]. Olvassa el az [első lépések a Event Hubs Capture](event-hubs-capture-python.md) című cikket is.

## <a name="how-event-hubs-capture-is-charged"></a>A Event Hubs rögzítésének díja

A Event Hubs rögzítését az átviteli egységekhez hasonlóan kell mérni: óránkénti díj ellenében. A díj közvetlenül a névtérhez megvásárolt átviteli egységek számával arányos. Ahogy az átviteli egységek növekednek és csökkennek, Event Hubs rögzítési mérőszámok növelik és csökkennek a megfelelő teljesítmény biztosítása érdekében. A mérőórák párhuzamosan történnek. A díjszabással kapcsolatos részletekért tekintse meg a [Event Hubs díjszabását](https://azure.microsoft.com/pricing/details/event-hubs/). 

A rögzítés nem használja fel a kimenő forgalomra vonatkozó kvótát, mivel azt külön számlázzák. 

## <a name="integration-with-event-grid"></a>Integráció a Event Grid 

Létrehozhat egy Azure Event Grid-előfizetést egy Event Hubs névtér forrásaként. A következő oktatóanyag azt mutatja be, hogyan hozhat létre egy Event Grid-előfizetést egy Event hub forrásként és egy Azure Functions alkalmazásként fogadóként: [feldolgozhatja és áttelepítheti a rögzített Event Hubs-adatforrásokat egy Azure szinapszis analyticsbe Event Grid és Azure functions használatával](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Következő lépések
A Event Hubs Capture az Azure-ba való adatgyűjtésének legegyszerűbb módja. A Azure Data Lake, a Azure Data Factory és az Azure HDInsight használatával kötegelt feldolgozást és egyéb elemzéseket végezhet a választott eszközök és platformok használatával, bármilyen méretezéssel.

Ismerje meg, hogyan engedélyezheti ezt a funkciót a Azure Portal és a Azure Resource Manager sablonnal:

- [Az Event Hubs Capture engedélyezése az Azure Portalon](event-hubs-capture-enable-through-portal.md)
- [Event Hubs rögzítésének engedélyezése Azure Resource Manager sablon használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming at Scale: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
