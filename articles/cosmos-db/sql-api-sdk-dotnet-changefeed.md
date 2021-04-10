---
title: Azure Cosmos DB .NET Change Feed Processor API, SDK kibocsátási megjegyzései
description: Tudjon meg mindent a Change Feed Processor API-ról és SDK-ról, beleértve a kiadási dátumokat, a kivezetési dátumokat, valamint a .NET Change Feed Processor SDK egyes verziói között eszközölt módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: b1f2efa887fb56d555f354c6d0a3262a7f178e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577138"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK: Letöltés és kibocsátási megjegyzések
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [REST erőforrás-szolgáltató](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

|   | Hivatkozások  |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentáció**|[A Change Feed Processor kódtár API referenciadokumentációja](/dotnet/api/microsoft.azure.documents.changefeedprocessor)|
|**Első lépések**|[Bevezetés a Change Feed Processor .NET SDK használatába](change-feed.md)|
|**Jelenleg támogatott keretrendszer**| [Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Ha a változáscsatorna-feldolgozót használja, tekintse át a [.NET SDK](change-feed-processor.md) legújabb, 3.x verzióját, amelybe bele van építve a változáscsatorna. 

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="v2-builds"></a>2-es verziójú buildek

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* Mostantól a címbérlettárolók kompatibilisek a 3-as verziójú SDK-val, ami lehetővé teszi a gyors migrálási útvonalak használatát. Az alkalmazások migrálhatók a 3-as verziójú SDK-ra, és visszamigrálhatók a változáscsatorna-feldolgozó kódtárába, állapotvesztés nélkül.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Javítottuk azt az esetet, amikor a `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` bezárási ok lett elküldve a `FeedProcessing.IChangeFeedObserver.CloseAsync` részére, ha a partíció nem található, vagy ha a célreplika nem naprakész az olvasási munkamenethez képest. Ezekben az esetekben már a `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` és `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` bezárási okokat használjuk.
* Valamint hozzáadtunk egy új `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` bezárási okot, amely azért küldhető el, hogy bezárja a változáscsatorna-figyelőt, ha a célreplika nem naprakész az olvasási munkamenethez képest.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* Hozzáadtunk egy új `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` metódust és a hozzá tartozó `ICheckpointPartitionProcessorFactory` nyilvános felületet. Ez lehetővé teszi a `IPartitionProcessor` interfész implementálását, amely így a beépített ellenőrzőpont-mechanizmust használja. Az új előállító hasonló a meglévő `IPartitionProcessorFactory` előállítóhoz, azzal a különbséggel, hogy `Create` metódusa a `ILeaseCheckpointer` paramétert is használja.
* A két metódus közül csak az egyik, tehát vagy a `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory`, vagy a `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` használható egy adott `ChangeFeedProcessorBuilder`-példány esetében.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Stabilitással és diagnosztizálhatósággal kapcsolatos fejlesztések:
  * Mostantól támogatott annak az észlelése, ha a változáscsatorna olvasása túl sokáig tart. Ha több időt vesz igénybe, mint a `ChangeFeedProcessorOptions.ChangeFeedTimeout` tulajdonságban megadott érték, akkor a rendszer a következő lépéseket végzi el:
    * Megszakítja a változáscsatorna olvasásának műveletét a problémás partíción.
    * A változáscsatorna-feldolgozó példány elveti a problémás bérlet tulajdonjogát. Az elvetett bérlet a következő bérletbeolvasási lépés során lesz újra felvéve, amelyet ugyanaz a változáscsatorna-feldolgozó példány vagy egy másik változáscsatorna-feldolgozó példány is végrehajthat. Ezáltal a változáscsatorna olvasása elölről kezdődik.
    * A rendszer jelenti a problémát az állapotfigyelő felé. Az alapértelmezett állapotfigyelő az összes jelentett problémát elküldi a nyomkövetési naplóba.
  * Hozzáadtunk egy új nyilvános tulajdonságot: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. E tulajdonság alapértelmezett értéke 10 perc.
  * Hozzáadtunk egy új felsorolási értéket: `Monitoring.MonitoredOperation.ReadChangeFeed`. Ha a `HealthMonitoringRecord.Operation` tulajdonság beállítása `Monitoring.MonitoredOperation.ReadChangeFeed`, az azt jelzi, hogy az állapotbeli probléma a változáscsatorna olvasásával kapcsolatos.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Továbbfejlesztettük annak a forgatókönyvnek a terheléselosztási stratégiáját, amikor az összes bérlet lekérése tovább tart, mint a bérletek lejárati időtartama, például hálózati problémák miatt:
  * Ebben a forgatókönyvben a terheléselosztási algoritmus tévesen tekintette lejártnak a bérleteket, és ezzel az aktív tulajdonosok elől vett el bérleteket. Ez időnként sok bérlet felesleges újraosztását eredményezte.
  * A problémát ebben a kiadásban úgy orvosoltuk, hogy ütközés esetén a rendszer ne próbálkozzon újra az olyan lejárt bérletek lekérésével, amelynek a tulajdonosa nem változott, és halassza el a lejárt bérletek lekérését a terheléselosztás következő iterációjáig.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Javult a figyelői kivételek kezelése.
* Részletesebbek az információk a figyelői hibákról:
  * Ha a rendszer bezár egy figyelőt egy kivétel miatt, amelyet a figyelő ProcessChangesAsync feladata váltott ki, akkor a CloseAsync esetében az okparaméter értéke a következő lesz: ChangeFeedObserverCloseReason.ObserverError.
  * Nyomkövetések lettek hozzáadva, amelyekkel azonosíthatók a felhasználói kódokon belüli hibák a figyelőkben.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Mostantól támogatott a felosztások kezelése az olyan gyűjteményekben, amelyek megosztott átviteli sebességet használnak az adatbázisokhoz.
  * Ebben a kiadásban javítottunk egy problémát, amely akkor fordulhatott elő, ha az adatbázisokhoz megosztott átviteli sebességet használó gyűjteményekben elvégeztünk egy felosztást, amely a partíció terhelésének újraelosztását eredményezte, de csak egy alárendelt partíciókulcs-tartomány jött létre, nem kettő. Ebben az esetben a változáscsatorna-feldolgozó gyakran elakadt a régi partíciókulcs-tartomány bérletének törlésénél, és nem hozott létre új bérleteket. A probléma javítva lett ebben a kiadásban.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* Hozzáadtuk a ChangeFeedProcessorOptions.StartContinuation tulajdonságot, amely lehetővé teszi a változáscsatorna elindítását a folytatási jogkivonat kérésével. Ez csak akkor használatos, ha egy bérletgyűjtemény üres, vagy nincs beállítva a ContinuationToken tulajdonsága. Az olyan bérletgyűjteményeknél, amelyeknek be van állítva a ContinuationToken tulajdonsága, a rendszer a ContinuationToken tulajdonságot használja, és a ChangeFeedProcessorOptions.StartContinuation tulajdonságot figyelmen kívül hagyja.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Mostantól támogatott az egyéni tárolók használata a folytonossági jogkivonatok partíciónként történő megőrzéséhez.
  * Például egy egyéni bérlettároló lehet egy Azure Cosmos DB-bérletgyűjtemény, amely bármilyen egyéni módon particionálható.
  * Az egyéni bérlettárolók használhatják az új ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) bővítési pontot és az ILeaseStoreManager nyilvános felületet.
  * Az ILeaseManager interfész újra lett bontva több szerepkör-interfészre.
* Kisebb kompatibilitástörő változás: a ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager) bővítési pont el lett távolítva, ehelyett használja a ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) bővítési pontot.

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* Ebben a kiadásban kijavítottunk egy problémát, amely egy megfigyelt gyűjteményben található felosztás feldolgozásakor és a particionált bérletgyűjtemények használatakor fordult elő. A felosztott partíció bérletének feldolgozásakor előfordulhat, hogy a partíciónak megfelelő bérlet nem törölhető. A probléma javítva lett ebben a kiadásban.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Kijavítottuk a becslő számításának működését a több írási régióval rendelkező fiókok esetében, és a munkamenet-jogkivonat formátuma megújult.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Mostantól támogatottak a particionált bérletgyűjtemények. A partíciókulcsot a következőképpen kell megadni: /id.
* Kisebb kompatibilitástörő változás: az IChangeFeedDocumentClient felület és a ChangeFeedDocumentClient osztály metódusai módosultak, és mostantól tartalmazzák a RequestOptions és a CancellationToken paramétert. Az IChangeFeedDocumentClient egy fejlett bővítési pont, amely lehetővé teszi a DocumentClient olyan egyéni implementációjának a biztosítását, amely a változáscsatorna feldolgozójával használható, például a DocumentClient pontos megadásához és a felé irányuló összes hívás elfogásához további nyomkövetés, hibakezelés stb. végrehajtása céljából. A frissítés miatt az IChangeFeedDocumentClient felületet implementáló kódot módosítani kell, hogy az implementáció tartalmazza az új paramétereket.
* Kisebb diagnosztikai fejlesztések.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* Új API érhető el: a Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Az API az egyes partíciókhoz kapcsolódó becsült munka megjelenítésére használható.
* Támogatja a Microsoft.Azure.DocumentDB SDK 2.0-t. A használatához a Microsoft.Azure.DocumentDB 2.0-s vagy újabb verziója szükséges.

### <a name="206"></a><a id="2.0.6"></a>clickstream
* Mostantól elérhető a ChangeFeedEventHost.HostName nyilvános tulajdonság az 1-es verzióval való kompatibilitáshoz.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* Kijavítottuk a partíciófelosztás során bekövetkező versenyhelyzetet. A versenyhelyzet a bérlet megszerzéséhez, majd azonnali elvesztéséhez vezethet a partíciófelosztás során, ami versenyt okoz. A versenyhelyzettel kapcsolatos probléma javítva lett ebben a kiadásban.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3 – előzetes kiadás
* Az alábbi problémákat javítja:
  * Amikor partíciófelosztás történt, a felosztás előtt módosított dokumentumok feldolgozása ismétlődően megtörténhetett.
  * A GetEstimatedRemainingWork API 0 értéket adott vissza, amikor nem voltak bérletek a bérletgyűjteményben.

* Nyilvánosak lettek az alábbi kivételek. Az IPartitionProcessort implementáló bővítmények okozhatják ezeket a kivételeket.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2 – előzetes kiadás
* Kisebb API-módosítások:
  * El lett távolítva az elavultként megjelölt ChangeFeedProcessorOptions.IsAutoCheckpointEnabled.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1 – előzetes kiadás
* Stabilitással kapcsolatos fejlesztések:
  * A bérlettároló inicializálásának jobb kezelése. Ha a bérlettároló üres, akkor a feldolgozónak csak egy példánya inicializálhatja, a többinek várakoznia kell.
  * Stabilabb/hatékonyabb bérletmegújítás/-kiadás. Olyan bérlet megújítása és kiadása, amelytől egyetlen partíció függ, a többi bérlet megújításától függetlenül történik. Az 1-es verzióban ezt a rendszer egymást követően hajtotta végre minden partícióra vonatkozóan.
* Új, 2-es verziójú API:
  * Buildelőminta a feldolgozó rugalmas összeállításához: a ChangeFeedProcessorBuilder osztály.
    * A paraméterek tetszőleges kombinációját felveheti.
    * DocumentClient-példányt is felvehet monitorozáshoz és/vagy bérletgyűjtéshez (nem elérhető el az 1-es verzióban).
  * Az IChangeFeedObserver.ProcessChangesAsync már felveszi a CancellationToken paramétert.
  * IRemainingWorkEstimator – a hátralévő munka becslője a feldolgozótól függetlenül használható.
  * Új bővítési pontok:
    * IPartitionLoadBalancingStrategy – a partícióknak a feldolgozó példányai közötti egyéni terheléselosztásához.
    * ILease, ILeaseManager – egyéni bérletkezeléshez.
    * IPartitionProcessor – egyéni feldolgozási módosításokhoz a partíciókon.
* Naplózás – a [LibLog](https://github.com/damianh/LibLog) kódtárat használja.
* 100%-ban visszamenőlegesen kompatibilis az 1-es verziójú API-val.
* Új kódbázis.
* Az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1-es és újabb verzióival kompatibilis.

### <a name="v1-builds"></a>1-es verziójú buildek

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* További naplózási lehetőségek érhetők el.
* Kijavít egy DocumentClient-hiányosságot a függőben lévő munka becslésének többszöri meghívásakor.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Javítások a függőben lévő munka becslésében.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Stabilitással kapcsolatos fejlesztések.
  * Kijavítja a megszakított feladatok kezelésével kapcsolatos problémát, amely a figyelők egyes partíciókon való leállítását eredményezhette.
* Ellenőrzőpontok manuális létrehozásának támogatása.
* Az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21-es és újabb verzióival kompatibilis.

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* Mostantól a .NET Standard 2.0 támogatott. A csomag már támogatja a(z) `netstandard2.0` és `net451` keretrendszer-monikert.
* Az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0-s és újabb verzióival kompatibilis.
* Az [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1-es és újabb verzióival kompatibilis.

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Kijavítja a hátralévő munka becslésének kiszámításával kapcsolatos problémát, amely akkor jelentkezett, amikor a változáscsatorna üres volt, vagy nem volt függőben lévő munka.
* Az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2-es és újabb verzióival kompatibilis.

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Új metódus érhető el a változáscsatornában feldolgozandó hátralévő munka becslésének beszerzéséhez.
* Az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2-es és újabb verzióival kompatibilis.

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* GA SDK
* Az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1-es és korábbi verzióival kompatibilis.

## <a name="release--retirement-dates"></a>Kiadási és kivonási dátumok

A Microsoft legalább **12 hónappal** előre biztosít értesítést az SDK-k kivonásáról az újabb vagy támogatott verzióra való zökkenőmentes váltás érdekében. Új szolgáltatásokkal, funkciókkal és optimalizálásokkal csak az aktuális SDK bővül, ezért azt javasoljuk, hogy a mindig lehető leghamarabb frissítsen a legújabb SDK-verzióra.

> [!WARNING]
> 2022. augusztus 31-ét követően az Azure Cosmos DB nem biztosít hibajavításokat, új szolgáltatásokat és támogatást az SQL API-hoz készült Azure Cosmos DB .NET vagy .NET Core SDK 1.x-es verzióihoz. Ha nem szeretné frissíteni, az SDK 1.x-es verziójából küldött kéréseket továbbra is kiszolgálja az Azure Cosmos DB szolgáltatás.

<br/>

| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [2.3.2](#2.3.2) |2020. augusztus 11. |--- |
| [2.3.1](#2.3.1) |2020. július 30. |--- |
| [2.3.0](#2.3.0) |2020. április 2. |--- |
| [2.2.8](#2.2.8) |2019. október 28. |--- |
| [2.2.7](#2.2.7) |2019. május 14. |--- |
| [2.2.6](#2.2.6) |2019. január 29. |--- |
| [2.2.5](#2.2.5) |2018. december 13. |--- |
| [2.2.4](#2.2.4) |2018. november 29. |--- |
| [2.2.3](#2.2.3) |2018. november 19. |--- |
| [2.2.2](#2.2.2) |2018. október 31. |--- |
| [2.2.1](#2.2.1) |2018. október 24. |--- |
| [1.3.3](#1.3.3) |2018. május 8. |--- |
| [1.3.2](#1.3.2) |2018. április 18. |--- |
| [1.3.1](#1.3.1) |2018. március 13. |--- |
| [1.2.0](#1.2.0) |2017. október 31. |--- |
| [1.1.1](#1.1.1) |2017. augusztus 29. |--- |
| [1.1.0](#1.1.0) |2017. augusztus 13. |--- |
| [1.0.0](#1.0.0) |2017. július 7. |--- |

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még

A Cosmos DB-ről a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás oldalán tekinthet meg további információt.
