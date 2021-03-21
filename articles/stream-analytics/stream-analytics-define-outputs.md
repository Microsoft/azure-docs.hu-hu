---
title: Kimenetek Azure Stream Analytics
description: Ez a cikk a Azure Stream Analytics számára elérhető adatkimeneti beállításokat ismerteti.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: 3ce4f673657561e196520466b569d0cf83d75a8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019346"
---
# <a name="outputs-from-azure-stream-analytics"></a>Kimenetek Azure Stream Analytics

Az Azure Stream Analytics feladatok egy bemenetből, lekérdezésből és kimenetből állnak. Az átalakított adatokat több kimeneti típus is elküldheti. Ez a cikk az Stream Analytics támogatott kimeneteit sorolja fel. Amikor megtervezi a Stream Analytics lekérdezést, a [INTO záradék](/stream-analytics-query/into-azure-stream-analytics)használatával tekintse meg a kimenet nevét. A lekérdezéshez több INTO záradékot is használhat feladatokhoz, illetve több kimeneti adatfolyam-feladatokhoz (ha szüksége van rájuk).

Stream Analytics feladatok kimenetének létrehozásához, szerkesztéséhez és teszteléséhez használhatja a [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), a [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), a [.NET API](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations), a [REST API](/rest/api/streamanalytics/)és a [Visual Studio alkalmazást](stream-analytics-quick-create-vs.md).

Egyes kimeneti típusok támogatják a [particionálást](#partitioning), és a [kimeneti köteg mérete](#output-batch-size) eltérő az átviteli sebesség optimalizálása érdekében. A következő táblázat az egyes kimeneti típusok által támogatott szolgáltatásokat tartalmazza:

| Kimenet típusa | Particionálás | Biztonság | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Yes|Azure Active Directory felhasználó </br> , Felügyelt identitás|
|[Azure SQL Database](sql-database-output.md)|Igen, nem kötelező.|SQL-felhasználó hitelesítése, </br> Felügyelt identitás (előzetes verzió)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Yes|SQL-felhasználó hitelesítése, </br> Felügyelt identitás (előzetes verzió)|
|[BLOB Storage és Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|Yes|Hozzáférési kulcs, </br> Felügyelt identitás (előzetes verzió)|
|[Azure Event Hubs](event-hubs-output.md)|Igen, be kell állítania a partíciós kulcs oszlopot a kimeneti konfigurációban.|Hozzáférési kulcs, </br> Felügyelt identitás (előzetes verzió)|
|[Power BI](power-bi-output.md)|No|Azure Active Directory felhasználó, </br> Felügyelt identitás|
|[Azure Table storage](table-storage-output.md)|Yes|Fiókkulcs|
|[Azure Service Bus várólisták](service-bus-queues-output.md)|Yes|Hozzáférési kulcs|
|[Azure Service Bus témakörök](service-bus-topics-output.md)|Yes|Hozzáférési kulcs|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Yes|Hozzáférési kulcs|
|[Azure Functions](azure-functions-output.md)|Yes|Hozzáférési kulcs|

## <a name="partitioning"></a>Particionálás

A Stream Analytics a Power BI kivételével minden kimenethez támogatja a partíciókat. A partíciós kulcsokkal és a kimeneti írók számával kapcsolatos további információkért tekintse meg az Önt érdeklő kimeneti típust ismertető cikket. Az összes kimeneti cikk az előző szakaszban van csatolva.  

Emellett a partíciók fejlettebb hangolásához a kimeneti írók száma a `INTO <partition count>` lekérdezésben (lásd: [into](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) záradék használatával vezérelhető, ami hasznos lehet a kívánt feladatok topológiájának megvalósításában. Ha a kimeneti adapter nincs particionálva, az egyik bemeneti partícióban lévő adatok hiánya késlelteti a késői érkezési időtartamot. Ilyen esetekben a kimenet egyetlen író számára lesz egyesítve, ami szűk keresztmetszetet eredményezhet a folyamat során. Ha többet szeretne megtudni a késői beérkezési szabályzatról, tekintse meg az [Azure stream Analytics az események sorrendjét](./stream-analytics-time-handling.md)ismertető szakaszt

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Minden kimenet támogatja a kötegelt feldolgozást, de csak néhány támogatási köteget explicit módon. A Azure Stream Analytics változó méretű kötegeket használ az események feldolgozásához és a kimenetekhez való íráshoz. Általában a Stream Analytics motor nem ír egyszerre egy üzenetet, és batchs-t használ a hatékonysághoz. Ha a bejövő és a kimenő események aránya is magas, Stream Analytics nagyobb kötegeket használ. Ha a kimenő forgalom aránya alacsony, kisebb kötegeket használ a késés alacsonyra állításához.

## <a name="parquet-output-batching-window-properties"></a>A parketta kimenetének batch ablakának tulajdonságai

Azure Resource Manager sablon központi telepítésének vagy a REST APIének használatakor a két batch-ablak tulajdonságai a következők:

1. *timeWindow*

   A maximális várakozási idő tételenként. Az értéknek egy TimeSpan sztringnek kell lennie. Például: "00:02:00" két percre. Ezt követően a köteg a kimenetbe kerül, még akkor is, ha a minimális sorokra vonatkozó követelmény nem teljesül. Az alapértelmezett érték 1 perc, az engedélyezett maximum pedig 2 óra. Ha a blob kimenetének elérésiút-mintázati gyakorisága van, a várakozási idő nem lehet nagyobb, mint a partíció időtartománya.

2. *sizeWindow*

   A kötegek minimális sorainak száma. A parketta esetében minden batch létrehoz egy új fájlt. Az aktuális alapértelmezett érték 2 000 sor, és az engedélyezett maximális érték 10 000 sor.

Ezeket a Batch-ablak tulajdonságokat csak az API 2017-04-01-es verziója támogatja **– előzetes** verzió. Az alábbi példa egy REST API hívás JSON-adattartalmát mutatja be:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
