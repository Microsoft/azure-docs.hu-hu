---
title: 'Azure Cosmos DB: SQL Java API, SDK & erőforrások'
description: Ismerje meg az SQL Java API-t és az SDK-t, beleértve a kiadási dátumokat, a kieső dátumokat, valamint a Azure Cosmos DB SQL Java SDK egyes verziói között végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6644f495f28fb76503948c18354a5af0fcf832e5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364753"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB SQL API-hoz készült Java SDK: Kibocsátási megjegyzések és erőforrások
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
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
> * [Spark 3 OLTP-összekötő](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Ez az SQL API-hoz Azure Cosmos DB Sync Java SDK v2 eredeti verziója, amely támogatja a szinkron műveleteket.

> [!IMPORTANT]  
> Ez nem *a* legújabb Java SDK for Azure Cosmos DB! Fontolja meg [Azure Cosmos DB Java SDK v4-es verzióját](sql-api-sdk-java-v4.md) a projektjéhez. A frissítéshez kövesse A [Java SDK v4-re](migrate-java-v4-sdk.md) való Azure Cosmos DB áttelepítése és a [Reactor és az RxJava útmutató utasításait.](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) 
>

| | Hivatkozások |
|---|---|
|**SDK letöltése**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentáció**|[A Java API referenciadokumentációja](/java/api/com.microsoft.azure.documentdb)|
|**Közreműködés az SDK-ban**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Első lépések**|[Első lépések a Java SDK-val](./create-sql-api-java.md)|
|**Webalkalmazás oktatóanyag**|[Webalkalmazások fejlesztése Azure Cosmos DB](sql-api-java-application.md)|
|**Minimális támogatott futási idő**|[Java fejlesztői készlet (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="261"></a><a name="2.6.1"></a>2.6.1
* Kijavítottunk egy hibát, amely a lekérdezések szolgáltatás-interopon keresztüli kezelésével kapcsolatos.

### <a name="260"></a><a name="2.6.0"></a>2.6.0
* A változáscsatorna időpontról való lekérdezésének támogatása hozzáadva.

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Kijavítja a documentCollection lekérdezés elsődleges partíciógyorsítótárával kapcsolatban problémákat.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* A 449-es újrapróbálkozási egyéni konfiguráció támogatása hozzáadva.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Kijavítja a kapcsolatkészlet időtúllépésével kapcsolatos problémát.
* Kijavítja a hitelesítési jogkivonat frissítését a belső újraküldetésen.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Frissült a megfelelő ügyféloldali replika házirendcímkéje a databaseAccount adatbázison, és a databaseAccount konfiguráció beolvassa a gyorsítótárból.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Kerülje az újrapróbálkozásokat érvénytelen partíciókulcs-tartományhiba esetén, ha a felhasználó a pkRangeId azonosítót biztosítja.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Frissül az optimalizált partíciókulcs-tartomány gyorsítótára.
* Kijavítja azt a forgatókönyvet, amelyben az SDK nem particionálási tippeket ad vissza a kiszolgálóról, és helytelen ügyféloldali útválasztási gyorsítótár-frissítést ad vissza.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Frissül az optimalizált gyűjtemény-gyorsítótár.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Mostantól támogatott a belső kivételüzenet lekérése a kérelem diagnosztikai sztringből.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* A PartitionKeyDefinition bevezette a verzió api-t.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Külön időtúllépési támogatás hozzáadva a közvetlen módhoz.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Null értékű hibaüzenetek fogadása a szolgáltatásból és a dokumentum ügyfél kivételének előállítása.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Javult a szoftvercsatorna-kapcsolat, hozzáadva a SoKeepAlive alapértelmezett igaz értéket.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Diagnosztikai sztringek kérésének támogatása hozzáadva.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Ki lett javítva a PartitionKey hiba a kivonat V2-ben.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Az összetett indexek támogatása hozzáadva.
* Ki lett javítva a frissítés kényszerítve a globális végpontkezelőben.
* Kijavítva a közvetlen módban előfeltételekkel történő upserts hiba.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Ki van javítva az átjárócím-gyorsítótárban található hiba.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Többrépontos írási támogatás hozzáadva a közvetlen módhoz.
* Mostantól támogatott a proxyk által a ServiceUnavailable kivételként okozott IOException-kivételek kezelése.
* Ki van javítva a végpontfelderítés újrapróbálkozási szabályzatának egyik hibaa.
* Kijavítottunk egy hibát, amely gondoskodott arról, hogy a BaseDatabaseAccountConfigurationProvider ne ad vissza null mutató kivételeket.
* Kijavítottunk egy hibát, amely gondoskodott arról, hogy a QueryIterator ne ad vissza null értékeket.
* Kijavítottunk egy hibát, amely gondoskodott arról, hogy a nagy PartitionKey engedélyezve legyen

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Többrépontú írási támogatás hozzáadva az átjáró üzemmódhoz.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Ki van javítva a lekérdezés partíciókulcs-tartományának olvasása hiba.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Kijavítottunk egy hibát a folytatási token fejlécméretének DirectHttps módban való beállításakor.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Streamelési feladatátvétel támogatása hozzáadva.
* Egyéni metaadatok támogatása hozzáadva.
* Továbbfejlesztett munkamenet-kezelési logika.
* Ki van javítva egy hiba a partíciókulcs-tartomány gyorsítótárában.
* Kijavítottunk egy NPE-hibát közvetlen módban.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Egyedi index támogatása hozzáadva.
* Támogatás hozzáadva a folytatási token méretének korlátozásához a hírcsatorna beállításaiban.
* Kijavítottunk egy hibát a JSON-szerializálásban (időbélyeg).
* Kijavítottunk egy hibát a JSON-szerializálásban (enum).
* Függőség a com.fasterxml.annál.core:azen-databind a 2.9.5-ös verzióra frissítve.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Továbbfejlesztett kapcsolatkészletezés a közvetlen módhoz.
* Továbbfejlesztett előzetes becsatorna-fejlesztés a nem sorrendi, többpartíciós lekérdezéssel.
* Továbbfejlesztett UUID-generáció.
* Továbbfejlesztett munkamenetkonzisztencia-logika.
* Többpogonos támogatás hozzáadva.
* Hozzá van adva a partíciókulcs-tartománystatisztikák támogatása a gyűjteményhez.
* Kijavítottunk egy hibát a többrépontos támogatásban.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Jobb JSON-szerializálási teljesítmény.
* Ehhez az SDK-verzióhoz a Azure Cosmos DB [legújabb verziója szükséges.](https://aka.ms/cosmosdb-emulator)

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* A Microsoft barátok könyvtárainak belső változásai.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Ki van javítva az egypartíciós kulcstartományok olvasásának problémája.
* Kijavítottunk egy hibát a ResourceID-parsingben, amely hatással van a rövid névvel szereplő adatbázisra.
* Ki van javítva a partíciókulcs-kódolás által okozhatott hiba.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Kritikus hibajavítások a kérések feldolgozásához partíciófelosztók során.
* Kijavítottunk egy hibát az Erős és a BoundedStaleness konzisztenciaszinttel.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Új konzisztenciaszint támogatása ConsistentPrefix néven.
* Kijavítottunk egy hibát a gyűjtemény munkamenet módban való olvasása során.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Particionált gyűjtemények támogatása engedélyezve, akár 2500 RU/s értékekkel, és skálázható 100 RU/s növekményekben.
* Kijavítottunk egy hibát a natív szerelvényben, amely NullRef kivételt okozhat bizonyos lekérdezésekben.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Kijavítottunk egy hibát a lekérdezési motor konfigurációjában, amely kivételeket okozhat az átjáró módú lekérdezéseknél.
* Kijavítottunk néhány hibát a munkamenet-tárolóban, amelyek a "Tulajdonosi erőforrás nem található" kivételt okozhatnak a kérések esetén közvetlenül a gyűjtemény létrehozása után.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Az összesítési lekérdezések (COUNT, MIN, MAX, SUM és AVG) támogatása hozzáadva. Lásd: [Összesítési támogatás.](sql-query-aggregate-functions.md)
* Változáscsatorna támogatása hozzáadva.
* A gyűjtési kvótainformációk támogatása a RequestOptions.setPopulateQuotaInfo segítségével.
* A tárolt eljárási szkriptek naplózásának támogatása a RequestOptions.setScriptLoggingEnabled használatával.
* Kijavítottunk egy hibát, amely miatt a DirectHttps módban a lekérdezések nem válaszolnak, amikor hiba lép fel a beállításban.
* Kijavítottunk egy hibát a munkamenet-konzisztencia módban.
* Kijavítottunk egy hibát, amely NullReferenceException hibát okozhat a HttpContextben, ha a kérelmek sebessége magas.
* A DirectHttps mód jobb teljesítménye.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Egyszerű ügyfélpéldány-alapú proxytámogatás hozzáadva a ConnectionPolicy.setProxy() API-hoz.
* DocumentClient.close() API hozzáadva a DocumentClient-példány megfelelő leállításához.
* Jobb lekérdezési teljesítmény közvetlen kapcsolati módban azáltal, hogy a lekérdezéstervet az átjáró helyett a natív szerelvényből származtatjuk.
* Állítsa FAIL_ON_UNKNOWN_PROPERTIES = false értéket, hogy a felhasználóknak ne kell JsonIgnoreProperties értéket meghatározniuk a POJO-ban.
* Újragyártott naplózás az SLF4J használatára.
* Néhány további hiba kijavítva a konzisztenciaolvasóban.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Kijavítottunk egy hibát a kapcsolatkezelésben, amely megakadályozza a közvetlen kapcsolati módban történő kapcsolatszivárgást.
* Kijavítottunk egy hibát a TOP lekérdezésben, amely NullReference kivételt okozhatott.
* Jobb teljesítmény a belső gyorsítótárak hálózati hívási számának csökkentésével.
* Hozzá lett adva az állapotkód, az ActivityID és a Kérés URI a DocumentClientExceptionben a jobb hibaelhárítás érdekében.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Kijavítottunk egy problémát a kapcsolatkezelés stabilitása érdekében.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* A BoundedStaleness konzisztenciaszint támogatása hozzáadva.
* Közvetlen kapcsolat támogatása a particionált gyűjtemények CRUD-műveleteihez.
* Kijavítottunk egy hibát egy adatbázis SQL-lekérdezésekor.
* Kijavítottunk egy hibát a munkamenet-gyorsítótárban, amely miatt előfordulhat, hogy a munkamenet-jogkivonat helytelenül lett beállítva.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Többpartíciós párhuzamos lekérdezések támogatása hozzáadva.
* A PARTICIONált gyűjtemények TOP/ORDER BY lekérdezései már támogatottak.
* Az erős konzisztencia támogatása.
* Névalapú kérések támogatása közvetlen kapcsolat használata esetén.
* Kijavítva, hogy az ActivityId konzisztens maradjon az összes kérés-újrakérés során.
* Kijavítottunk egy, a munkamenet-gyorsítótárral kapcsolatos hibát, amikor egy azonos nevű gyűjteményt hozunk létre újra.
* A Polygon és a LineString DataTypes hozzáadva a térinformatikai lekérdezések gyűjteményindexelési szabályzatának megadása közben.
* Kijavítottuk a Java Doc for Java 1.8 problémáit.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Ki van javítva egy hiba a PartitionKeyDefinitionMapben az egypartíciós gyűjtemények gyorsítótárazása és a további partíciókulcs-lekérési kérelmek mellőzése érdekében.
* Kijavítottunk egy hibát, amely miatt a rendszer nem próbálkozott újra, ha helytelen partíciókulcs-értéket adott meg.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* A többrépontos adatbázisfiókok támogatása hozzáadva.
* Az automatikus újrapróbálkozás támogatása a lekért kérelmeken a maximális újrapróbálkozási kísérletek és a maximális újrapróbálkozási várakozási idő testreszabási lehetőségekkel.  Lásd: RetryOptions és ConnectionPolicy.getRetryOptions().
* Elavult egyéni IPartitionResolver-alapú particionálási kód. Használjon particionált gyűjteményeket a nagyobb tárterülethez és átviteli sebességhez.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Újrapróbálkozási szabályzat támogatása a sebességkorlátozáshoz.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* A dokumentumokhoz hozzáadott idő (TTL) támogatása.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [Particionált gyűjteményeket és](partitioning-overview.md) felhasználói [teljesítményszinteket valósított meg.](performance-levels.md)

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Javítva lett egy hiba a HashPartitionResolverben, amely kivonatértékeket generált a little-endian oszlopban, hogy az konzisztens legyen más SDK-okkal.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Kivonatoló & tartománypartíció-feloldók hozzáadása az alkalmazások több partícióra történő horizontális skálázásának támogatásához.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementálja az Upsert-et. Új upsertXXX metódusok hozzáadva az Upsert funkció támogatásához.
* Implementálja az azonosítóalapú útválasztást. Nincs nyilvános API-módosítás, minden belső módosítás.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* A kiadás kihagyva, hogy a verziószám igazodva egymáshoz igazodva ugorható a többi SZOFTVERDK-val

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Támogatja a térinformatikai indexet
* Ellenőrzi az azonosító tulajdonságot az összes erőforráshoz. Az erőforrások azonosítói nem tartalmazhatnak ?, /, #, karaktereket vagy \, végződés szóközt.
* Új fejléc "indexátalakítási folyamat" hozzáadása a ResourceResponse erőforráshoz.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Megvalósítja a V2 indexelési szabályzatot

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Kiadási és kieső dátumok

A Microsoft legalább **12 hónappal** előre biztosít értesítést az SDK-k kivonásáról az újabb vagy támogatott verzióra való zökkenőmentes váltás érdekében. Az új funkciók, funkciók és optimalizálások csak az aktuális SDK-hoz vannak hozzáadva, ezért javasoljuk, hogy mindig a lehető leghamarabb frissítsen a legújabb SDK-verzióra.

> [!WARNING]
> 2020. május 30., Azure Cosmos DB a Azure Cosmos DB már nem fog hibajavításokat és új funkciókat hozzáadni, és támogatást nyújtani az Azure Cosmos DB Sql API-hoz készült Java SDK 1.x-es verzióihoz. Ha nem szeretné frissíteni, az SDK 1.x-es verziójából küldött kéréseket továbbra is kiszolgálja az Azure Cosmos DB szolgáltatás.
>
> 2016. február 29-től a Azure Cosmos DB már nem fog hibajavításokat és új funkciókat hozzáadni, és támogatást nyújtani az Azure Cosmos DB SQL API-hoz készült Java SDK 0.x verzióihoz. Ha nem szeretne frissíteni, az SDK 0.x verziójából küldött kéréseket továbbra is az Azure Cosmos DB kiszolgálja.


| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [2.6.1](#2.6.1) |2020. dec. 17. |--- |
| [2.6.0](#2.6.0) |2020. július 16. |--- |
| [2.5.1](#2.5.1) |2020. június 3. |--- |
| [2.5.0](#2.5.0) |2020. május 12. |--- |
| [2.4.7](#2.4.7) |2020. február 20. |--- |
| [2.4.6](#2.4.6) |2020. január 24. |--- |
| [2.4.5](#2.4.5) |2019. nov. 10. |--- |
| [2.4.4](#2.4.4) |2019. október 24. |--- |
| [2.4.2](#2.4.2) |2019. szeptember 26. |--- |
| [2.4.1](#2.4.1) |2019. július 18. |--- |
| [2.4.0](#2.4.0) |2019. május 4. |--- |
| [2.3.0](#2.3.0) |2019. április 24. |--- |
| [2.2.3](#2.2.3) |2019. április 16. |--- |
| [2.2.2](#2.2.2) |2019. április 5. |--- |
| [2.2.0](#2.2.0) |2019. március 27. |--- |
| [2.1.3](#2.1.3) |2019. március 13. |--- |
| [2.1.2](#2.1.2) |2019. március 09. |--- |
| [2.1.1](#2.1.1) |2018. dec. 13. |--- |
| [2.1.0](#2.1.0) |2018. nov. 20. |--- |
| [2.0.0](#2.0.0) |2018. szeptember 21. |--- |
| [1.16.4](#1.16.4) |2018. szeptember 10. |2020. május 30. |
| [1.16.3](#1.16.3) |2018. szeptember 09. |2020. május 30. |
| [1.16.2](#1.16.2) |2018. június 29. |2020. május 30. |
| [1.16.1](#1.16.1) |2018. május 16. |2020. május 30. |
| [1.16.0](#1.16.0) |2018. március 15. |2020. május 30. |
| [1.15.0](#1.15.0) |2017. nov. 14. |2020. május 30. |
| [1.14.0](#1.14.0) |2017. október 28. |2020. május 30. |
| [1.13.0](#1.13.0) |2017. augusztus 25. |2020. május 30. |
| [1.12.0](#1.12.0) |2017. július 11. |2020. május 30. |
| [1.11.0](#1.11.0) |2017. május 10. |2020. május 30. |
| [1.10.0](#1.10.0) |2017. március 11. |2020. május 30. |
| [1.9.6](#1.9.6) |2017. február 21. |2020. május 30. |
| [1.9.5](#1.9.5) |2017. január 31. |2020. május 30. |
| [1.9.4](#1.9.4) |2016. november 24. |2020. május 30. |
| [1.9.3](#1.9.3) |2016. október 30. |2020. május 30. |
| [1.9.2](#1.9.2) |2016. október 28. |2020. május 30. |
| [1.9.1](#1.9.1) |2016. október 26. |2020. május 30. |
| [1.9.0](#1.9.0) |2016. október 3. |2020. május 30. |
| [1.8.1](#1.8.1) |2016. június 30. |2020. május 30. |
| [1.8.0](#1.8.0) |2016. június 14. |2020. május 30. |
| [1.7.1](#1.7.1) |2016. április 30. |2020. május 30. |
| [1.7.0](#1.7.0) |2016. április 27. |2020. május 30. |
| [1.6.0](#1.6.0) |2016. március 29. |2020. május 30. |
| [1.5.1](#1.5.1) |2015. december 31. |2020. május 30. |
| [1.5.0](#1.5.0) |2015. december 4. |2020. május 30. |
| [1.4.0](#1.4.0) |2015. október 5. |2020. május 30. |
| [1.3.0](#1.3.0) |2015. október 5. |2020. május 30. |
| [1.2.0](#1.2.0) |2015. augusztus 05. |2020. május 30. |
| [1.1.0](#1.1.0) |2015. július 9. |2020. május 30. |
| 1.0.1 |2015. május 12. |2020. május 30. |
| [1.0.0](#1.0.0) |2015. április 07. |2020. május 30. |
| 0.9.5 előzetes |2015. március 09. |2016. február 29. |
| 0.9.4 előzetes |2015. február 17. |2016. február 29. |
| 0.9.3 előzetes |2015. január 13. |2016. február 29. |
| 0.9.2 előzetes |2014. december 19. |2016. február 29. |
| 0.9.1 előzetes |2014. december 19. |2016. február 29. |
| 0.9.0 –előzetes |2014. december 10. |2016. február 29. |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB-ről a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás oldalán tekinthet meg további információt.
