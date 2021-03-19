---
title: 'Azure Cosmos DB: SQL Java API, SDK &-erőforrások'
description: Ismerkedjen meg az SQL Java API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB SQL Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 12/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f18458d0c2ce857ba71463a14844414e631a73ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595699"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK for SQL API: kibocsátási megjegyzések és erőforrások
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
> * [Spark-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Ez az eredeti Azure Cosmos DB Sync Java SDK v2 az SQL API-hoz, amely támogatja a szinkron műveleteket.

> [!IMPORTANT]  
> Ez *nem* a legújabb Java SDK a Azure Cosmos db! Vegye fontolóra [Azure Cosmos db Java SDK v4](sql-api-sdk-java-v4.md) használatát a projekthez. A frissítéshez kövesse az [áttelepítés Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -útmutató és a [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) útmutató utasításait. 
>

| | Hivatkozások |
|---|---|
|**SDK letöltése**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentáció**|[Java API-referenciák dokumentációja](/java/api/com.microsoft.azure.documentdb)|
|**Közreműködés az SDK-val**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Első lépések**|[Ismerkedés a Java SDK-val](./create-sql-api-java.md)|
|**Webalkalmazás-oktatóanyag**|[Webalkalmazás-fejlesztés Azure Cosmos DB](sql-api-java-application.md)|
|**Minimális támogatott futtatókörnyezet**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="261"></a><a name="2.6.1"></a>2.6.1
* Kijavított egy hibát a lekérdezésnek a szolgáltatási együttműködésen keresztüli kezelésére.

### <a name="260"></a><a name="2.6.0"></a>2.6.0
* A változási csatorna időpontból történő lekérdezésének támogatása.

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Kijavítja az elsődleges partíció gyorsítótárának problémáját a documentCollection-lekérdezésben.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Az 449-támogatás hozzáadva az egyéni konfigurációhoz.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Megjavítja a kapcsolati készlet időtúllépési hibáját.
* Megjavítja az Auth-jogkivonat frissítését a belső újrapróbálkozások során.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Frissítette a megfelelő ügyféloldali replika házirend-címkét a databaseAccount-ben, és a databaseAccount konfigurációját a gyorsítótárból olvassa be.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Nem sikerült elkerülni az újrapróbálkozást a partíciós kulcs tartományának érvénytelen hibája esetén, ha a felhasználó pkRangeId biztosít.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Az optimalizált partíciós kulcs tartományának gyorsítótár-frissítése.
* A kijavítja azt a forgatókönyvet, amelyben az SDK nem támogatja a partíciók felosztására mutató hivatkozásokat a kiszolgálóról, és helytelen ügyféloldali útválasztási gyorsítótárak frissítését eredményezi.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Az optimalizált gyűjtemények gyorsítótára frissül.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* A belső kivételi üzenet kérés diagnosztikai karakterláncból való beolvasásának támogatása hozzáadva.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* A PartitionKeyDefinition-on bevezetett API-verzió.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* A közvetlen üzemmódhoz külön időtúllépési támogatás lett hozzáadva.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* NULL hibaüzenetet fogyaszt a szolgáltatástól, és a dokumentum-ügyfél kivételt hoz létre.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Szoftvercsatorna-kapcsolat fejlesztése, SoKeepAlive hozzáadása alapértelmezett True (igaz).

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* A kérelem diagnosztikai karakterláncának támogatása hozzáadva.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Rögzített hiba a PartitionKey for hash v2 esetében.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Összetett indexek támogatása hozzáadva.
* Kijavítva a hiba a globális Endpoint Managerben a frissítés kényszerítéséhez.
* Rögzített hiba az előfeltételekkel rendelkező upsert közvetlen módban.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Kijavítva a hiba az átjáró címeinek gyorsítótárában.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* A többrégiós írási támogatás közvetlen üzemmódhoz lett hozzáadva.
* A ServiceUnavailable-kivételként kiváltott IOExceptions kezelése a proxytól.
* Kijavítva egy hiba a végpont-felderítési újrapróbálkozási házirendben.
* Javítva lett egy hiba, amely biztosítja, hogy a rendszer null mutató kivételeket ne dobjon a BaseDatabaseAccountConfigurationProvider.
* Kijavított egy hibát annak biztosítására, hogy a QueryIterator nem ad vissza null értéket.
* Kijavítva egy hiba a nagyméretű PartitionKey engedélyezése érdekében

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* A többrégiós írási támogatás az átjáró üzemmódhoz lett hozzáadva.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Hiba javítva a lekérdezés olvasási partíciós kulcsának tartományában.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Kijavítva egy hiba a folytatási jogkivonat fejlécének DirectHttps módban való beállításakor.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* A folyamatos átviteli feladatátvétel támogatása.
* Egyéni metaadatok támogatása.
* Továbbfejlesztett munkamenet-kezelési logika.
* Kijavítva egy hiba a partíciós kulcs tartományának gyorsítótárában.
* Rögzített NPE hiba közvetlen módban.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Új támogatás az egyedi indexekhez.
* A folytatási token méretének korlátozása a hírcsatornában – beállítások.
* Kijavítva egy hiba a JSON-Szerializálásban (timestamp).
* Kijavítva egy hiba a JSON-Szerializálásban (Enum).
* Függőség a com. fasterxml. Jackson. Core: Jackson-koordinátamezőket 2.9.5-re frissítve.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Továbbfejlesztett kapcsolatok készletezése közvetlen módban.
* Továbbfejlesztett visszahívási fejlesztések a nem OrderBy kereszt partíciós lekérdezésekhez.
* Továbbfejlesztett UUID-generáció.
* Továbbfejlesztett munkamenet-konzisztencia-logika.
* Többsokszöges támogatás hozzáadva.
* A partíciós kulcs tartományának statisztikáinak támogatása a gyűjteményhez.
* Kijavított egy hibát a többrégiós támogatásban.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Továbbfejlesztett JSON-szerializálási teljesítmény.
* Az SDK-verzióhoz a [Azure Cosmos db Emulator](https://aka.ms/cosmosdb-emulator)legújabb verziójára van szükség.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* A Microsoft barátok kódtárainak belső módosításai.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Kijavítva egy hiba az egyes partíciós kulcsok tartományának olvasásakor.
* Kijavított egy problémát a ResourceID-elemzésben, amely a rövid névvel rendelkező adatbázist érinti.
* A partíciós kulcs kódolása okozta a probléma kijavítását.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Kritikus hibajavítások kérelmek feldolgozásához a partíciók felosztásakor.
* Kijavított egy problémát az erős és a BoundedStaleness konzisztencia szintjével.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Egy új, ConsistentPrefix nevű konzisztencia-szint támogatása.
* Kijavítva egy hiba a gyűjtemény olvasásakor munkamenet módban.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Engedélyezve van a particionált gyűjtemény támogatása a 2 500 RU/s értékkel, a mérete pedig 100 RU/s.
* Kijavított egy hibát a natív szerelvényben, amely NullRef kivételt okozhat bizonyos lekérdezésekben.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Kijavított egy hibát a lekérdezési motor konfigurációjában, amely kivételeket okozhat a lekérdezésekhez az átjáró módban.
* A munkamenet-tárolóban kijavított néhány hiba, amely miatt előfordulhat, hogy a "tulajdonosi erőforrás nem található" kivételt a rendszer a gyűjtemény létrehozása után azonnal kéri.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Összesítő lekérdezések támogatása (DARABSZÁM, MIN., MAX., SUM és AVG). Lásd: [összesítési támogatás](sql-query-aggregate-functions.md).
* A módosítási hírcsatorna támogatása.
* A gyűjtési kvóta adatainak támogatása a RequestOptions. setPopulateQuotaInfo használatával.
* A tárolt eljárás parancsfájl-naplózásának támogatása a RequestOptions. setScriptLoggingEnabled használatával.
* Kijavítva a hiba, ahol a DirectHttps módban végzett lekérdezés nem válaszol a szabályozási hibák esetén.
* Rögzített hiba a munkamenet-konzisztencia módban.
* Kijavítva a hiba, amely NullReferenceException okozhat a HttpContext, ha a kérések sebessége magas.
* A DirectHttps mód jobb teljesítménye.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* A ConnectionPolicy. setProxy () API-val egyszerű ügyfél-példány-alapú proxy-támogatás lett hozzáadva.
* Hozzáadta a DocumentClient. Bezárás () API-t a DocumentClient-példány megfelelő leállításához.
* A lekérdezési teljesítmény javítása közvetlen kapcsolati módban a lekérdezési tervnek az átjáró helyett a natív szerelvényből való származtatása révén.
* Állítsa be FAIL_ON_UNKNOWN_PROPERTIES = FALSE értéket, hogy a felhasználóknak ne kelljen megadniuk a JsonIgnoreProperties a saját POJO.
* A naplózás újrabontása a SLF4J használatára.
* Javítva kell néhány más hibát a konzisztencia-olvasóban.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Kijavított egy hibát a kapcsolat kezelésében, hogy megakadályozza a kapcsolatok szivárgását a közvetlen kapcsolati módban.
* Kijavított egy hibát a felső lekérdezésben, amely NullReference kivételt okozhat.
* Jobb teljesítmény a belső gyorsítótárak hálózati hívásának számának csökkentésével.
* A jobb hibaelhárítás érdekében hozzáadott állapotkódot, tevékenységazonosító és kérelem-URI-t a DocumentClientException-ben.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Javítva lett egy probléma a stabilitási kapcsolatok kezelésében.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* A BoundedStaleness konzisztencia-szintjének támogatása.
* Közvetlen kapcsolat támogatása a particionált gyűjtemények esetében a SZIFILISZi műveletekhez.
* Hiba javítva egy adatbázis SQL-sel való lekérdezése során.
* Kijavított egy hibát a munkamenet-gyorsítótárban, ahol a munkamenet-jogkivonat helytelenül állítható be.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* További támogatás a több partíciós párhuzamos lekérdezésekhez.
* A TOP/ORDER szintű támogatás hozzáadva a particionált gyűjtemények lekérdezései alapján.
* Erős konzisztencia-támogatás hozzáadva.
* A név alapú kérelmek támogatása közvetlen kapcsolat használata esetén.
* Javítva, hogy a tevékenységazonosító az összes kérelem újrapróbálkozásakor konzisztens maradjon.
* A munkamenet-gyorsítótárhoz kapcsolódó hiba javítva, amikor létrehoz egy azonos nevű gyűjteményt.
* Sokszög-és LineString-adattípusok hozzáadása a gyűjtemények indexelési szabályzatának megadásakor a Geo-kerítés térbeli lekérdezésekhez.
* A Java doc-mel kapcsolatos kijavított problémák a Javához 1,8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Kijavított egy hibát a PartitionKeyDefinitionMap az egypartíciós gyűjtemények gyorsítótárazásához, és nem végez további lekéréses particionálást.
* Kijavítva a hiba, hogy a rendszer Ne próbálkozzon újra, amikor helytelen partíciós kulcs van megadva.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* A többrégiós adatbázis-fiókok támogatása hozzáadva.
* A maximális újrapróbálkozási kísérletek és a maximális újrapróbálkozási várakozási idő testreszabására szolgáló beállításokkal automatikusan újrapróbálkozhat a szabályozott kérelmekkel.  Lásd: RetryOptions és ConnectionPolicy. getRetryOptions ().
* Elavult IPartitionResolver-alapú egyéni particionálási kód. Használjon particionált gyűjteményeket a nagyobb tárterület és átviteli sebesség érdekében.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Ismételt újrapróbálkozási szabályzat támogatása a díjszabás korlátozásához.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* További élettartam (TTL) támogatása a dokumentumokhoz.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* A [particionált gyűjtemények](partitioning-overview.md) és a [felhasználó által definiált teljesítményszint](performance-levels.md)implementálva.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Javítva lett egy hiba a HashPartitionResolver-ben, hogy a endian a többi SDK-val konzisztensek legyenek.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Adja hozzá a kivonatoló &-tartomány partíciójának feloldóit, hogy segítséget nyújtson a több partíción lévő alkalmazások horizontális felskálázásában.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert implementálása. Új upsertXXX-metódusok lettek hozzáadva a Upsert szolgáltatás támogatásához.
* Az azonosító alapú útválasztás implementálása. Nincs nyilvános API-változás, az összes változás belső.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* A kiadás kihagyva, hogy a verziószáma más SDK-k használatával legyen összehangolva

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* A térinformatikai index támogatása
* Érvényesíti az összes erőforrás azonosító tulajdonságát. Az erőforrások azonosítói nem tartalmazhatnak szóközt,/, #, \, karakter vagy véget.
* Hozzáadja az új "index átalakítási folyamat" fejlécet a ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* A v2 indexelési házirend megvalósítása

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Kiadási és nyugdíjazási dátumok

A Microsoft legalább **12 hónappal** előre biztosít értesítést az SDK-k kivonásáról az újabb vagy támogatott verzióra való zökkenőmentes váltás érdekében. Az új funkciók és funkciók és optimalizálás csak a jelenlegi SDK-hoz adódik hozzá, ezért azt javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-verzióra.

> [!WARNING]
> A 2020. május 30. után Azure Cosmos DB a továbbiakban nem javít hibajavításokat, új funkciókat adhat hozzá, és támogatást nyújt a Azure Cosmos DB Java SDK for SQL API-hoz tartozó 1. x verzióhoz. Ha nem szeretné frissíteni, az SDK 1.x-es verziójából küldött kéréseket továbbra is kiszolgálja az Azure Cosmos DB szolgáltatás.
>
> A 2016. február 29. után Azure Cosmos DB többé nem javít hibajavításokat, új funkciókat adhat hozzá, és támogatást nyújt a Azure Cosmos DB Java SDK for SQL API-hoz. Ha nem szeretné frissíteni, az SDK 0. x verziójában küldött kérelmeket a Azure Cosmos DB szolgáltatás továbbra is kiszolgálja.


| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [2.6.1](#2.6.1) |Dec. 17, 2020 |--- |
| [2.6.0](#2.6.0) |Július 16., 2020 |--- |
| [2.5.1](#2.5.1) |2020. június 03. |--- |
| [2.5.0](#2.5.0) |2020. május 12. |--- |
| [2.4.7](#2.4.7) |Február 20, 2020 |--- |
| [2.4.6](#2.4.6) |Január 24, 2020 |--- |
| [2.4.5](#2.4.5) |November 10, 2019 |--- |
| [2.4.4](#2.4.4) |Oct 24, 2019 |--- |
| [2.4.2](#2.4.2) |Szeptember 26., 2019 |--- |
| [2.4.1](#2.4.1) |Július 18., 2019 |--- |
| [2.4.0](#2.4.0) |Május 04., 2019 |--- |
| [2.3.0](#2.3.0) |Április 24., 2019 |--- |
| [2.2.3](#2.2.3) |Április 16., 2019 |--- |
| [2.2.2](#2.2.2) |Apr 05, 2019 |--- |
| [2.2.0](#2.2.0) |Mar 27, 2019 |--- |
| [2.1.3](#2.1.3) |Mar 13, 2019 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2019 |--- |
| [2.1.1](#2.1.1) |Dec. 13, 2018 |--- |
| [2.1.0](#2.1.0) |November 20, 2018 |--- |
| [2.0.0](#2.0.0) |Szeptember 21., 2018 |--- |
| [1.16.4](#1.16.4) |Szeptember 10., 2018 |2020. május 30. |
| [1.16.3](#1.16.3) |Szeptember 9., 2018 |2020. május 30. |
| [1.16.2](#1.16.2) |2018. június 29. |2020. május 30. |
| [1.16.1](#1.16.1) |Május 16., 2018 |2020. május 30. |
| [1.16.0](#1.16.0) |Március 15., 2018 |2020. május 30. |
| [1.15.0](#1.15.0) |November 14., 2017 |2020. május 30. |
| [1.14.0](#1.14.0) |Október 28., 2017 |2020. május 30. |
| [1.13.0](#1.13.0) |Augusztus 25., 2017 |2020. május 30. |
| [1.12.0](#1.12.0) |2017. július 11. |2020. május 30. |
| [1.11.0](#1.11.0) |2017. május 10. |2020. május 30. |
| [1.10.0](#1.10.0) |2017. március 11. |2020. május 30. |
| [1.9.6](#1.9.6) |Február 21., 2017 |2020. május 30. |
| [1.9.5](#1.9.5) |2017. január 31. |2020. május 30. |
| [1.9.4](#1.9.4) |November 24., 2016 |2020. május 30. |
| [1.9.3](#1.9.3) |Október 30-ig 2016 |2020. május 30. |
| [1.9.2](#1.9.2) |Október 28., 2016 |2020. május 30. |
| [1.9.1](#1.9.1) |Október 26., 2016 |2020. május 30. |
| [1.9.0](#1.9.0) |Október 03., 2016 |2020. május 30. |
| [1.8.1](#1.8.1) |Június 30., 2016 |2020. május 30. |
| [1.8.0](#1.8.0) |Június 14., 2016 |2020. május 30. |
| [1.7.1](#1.7.1) |Április 30., 2016 |2020. május 30. |
| [1.7.0](#1.7.0) |Április 27., 2016 |2020. május 30. |
| [1.6.0](#1.6.0) |Március 29., 2016 |2020. május 30. |
| [1.5.1](#1.5.1) |December 31., 2015 |2020. május 30. |
| [1.5.0](#1.5.0) |December 04., 2015 |2020. május 30. |
| [1.4.0](#1.4.0) |Október 05.2015 |2020. május 30. |
| [1.3.0](#1.3.0) |Október 05.2015 |2020. május 30. |
| [1.2.0](#1.2.0) |Augusztus 05, 2015 |2020. május 30. |
| [1.1.0](#1.1.0) |Július 9., 2015 |2020. május 30. |
| 1.0.1 |2015. május 12. |2020. május 30. |
| [1.0.0](#1.0.0) |2015. április 7. |2020. május 30. |
| 0.9.5 – előbérlet |Mar 09, 2015 |Február 29., 2016 |
| 0.9.4 – előbérlet |Február 17., 2015 |Február 29., 2016 |
| 0.9.3 – előbérlet |2015. január 13. |Február 29., 2016 |
| 0.9.2 – előbérlet |December 19., 2014 |Február 29., 2016 |
| 0.9.1 – előbérlet |December 19., 2014 |Február 29., 2016 |
| 0.9.0 – előbérlet |2014. december 10. |Február 29., 2016 |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB-ről a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás oldalán tekinthet meg további információt.
