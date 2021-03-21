---
title: Átviteli sebesség az Azure Cosmos-tárolók és-adatbázisok számára
description: Megtudhatja, hogyan állíthatja be az Azure Cosmos-tárolók és-adatbázisok kiépített átviteli sebességét.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/25/2021
ms.openlocfilehash: 1d5fba12ce9b1ce8d30c59a08aa36e1222abe3c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433930"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Bevezetés a kiépített átviteli sebességbe Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB lehetővé teszi a kiépített átviteli sebesség beállítását az adatbázisokon és a tárolókban. A kiosztott átviteli sebességnek, a standard (manuális) vagy az automatikus méretezésnek két típusa van. Ez a cikk áttekintést nyújt a kiépített átviteli sebesség működéséről. 

Az Azure Cosmos-adatbázis a tárolók készletének kezelési egysége. Az adatbázisok sémafüggetlen tárolókból állnak. Az Azure Cosmos-tároló az átviteli sebesség és a tárterület méretezhetőségének egysége. A tárolók horizontálisan vannak particionálva az Azure-régióban található gépek csoportjában, és az Azure Cosmos-fiókhoz társított összes Azure-régióban el vannak osztva.

A Azure Cosmos DB segítségével két részletességgel kiépítheti az átviteli sebességet:
 
- Azure Cosmos-tárolók
- Azure Cosmos-adatbázisok

## <a name="set-throughput-on-a-container"></a>Átviteli sebesség beállítása egy tárolón  

Az Azure Cosmos-tárolón kiépített átviteli sebesség kizárólag az adott tároló számára van fenntartva. A tároló minden alkalommal megkapja a kiosztott átviteli sebességet. A tárolók kiépített átviteli sebessége pénzügyi támogatást biztosít a SLA-kat illetően. Ha meg szeretné tudni, hogyan konfigurálhatja a standard (manuális) átviteli sebességet egy tárolón, tekintse meg az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiosztását Ha szeretné megtudni, hogyan konfigurálhatja az adatátviteli sebességet egy tárolón, tekintse meg a következő témakört: az automatikusan [méretezhető átviteli sebesség](how-to-provision-autoscale-throughput.md)

A kiépített átviteli sebesség egy tárolón való beállítása a leggyakrabban használt lehetőség. A tárolók kapacitása rugalmasan méretezhető, ha a [kérelmek egységei (RUs)](request-units.md)használatával bármilyen mennyiségű átviteli sebességet kiépít. 

A tárolók számára kiépített átviteli sebesség egyenletesen oszlik meg a fizikai partíciók között, és feltételezi, hogy a logikai partíciók egyenletes elosztása a fizikai partíciók között történik, az átviteli sebesség egyenletesen oszlik el a tároló összes logikai partícióján is. A logikai partíciók átviteli sebességét nem lehet szelektív módon megadni. Mivel a tárolók egy vagy több logikai partíciója egy fizikai partíción fut, a fizikai partíciók kizárólag a tárolóhoz tartoznak, és támogatják a tárolón kiosztott átviteli sebességet. 

Ha a logikai partíción futó munkaterhelés több, mint a mögöttes fizikai partícióhoz lefoglalt átviteli sebesség, lehetséges, hogy a műveletek mértéke korlátozott lesz. Az úgynevezett gyakori _partíció_ akkor fordul elő, ha egy logikai partíció aránytalanul több kérést tartalmaz, mint a többi partíciós kulcs értéke.

Az arány korlátozása esetén növelheti a kiosztott átviteli sebességet a teljes tárolón, vagy megismételheti a műveleteket. Azt is meg kell adnia, hogy olyan partíciós kulcsot válasszon, amely egyenletesen osztja el a tárolót és a kérelmek mennyiségét. További információ a particionálásról: [particionálás és horizontális skálázás Azure Cosmos DBban](partitioning-overview.md).

Javasoljuk, hogy az átviteli sebességet a tároló részletességén konfigurálja, ha a tároló kiszámítható teljesítményt kíván használni.

Az alábbi képen látható, hogy egy fizikai partíció egy vagy több tároló logikai partícióját tárolja:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Egy vagy több tároló logikai partícióját tároló fizikai partíció" border="false":::

## <a name="set-throughput-on-a-database"></a>Átviteli sebesség beállítása adatbázisokban

Amikor egy Azure Cosmos-adatbázison kiépíti az átviteli sebességet, az átviteli sebesség az adatbázisban található összes tárolóban (megosztott adatbázis-tárolókban) van megosztva. Ez alól kivételt képeznek az Ön által kiépített átviteli sebességgel rendelkező tárolók. Az adatbázis-szinten kiosztott átviteli sebesség megosztása a tárolók között hasonló ahhoz, hogy egy adatbázist a gépek egy fürtjén lehessen üzemeltetni. Mivel az adatbázisban lévő összes tároló megosztja a gépen elérhető erőforrásokat, természetesen nem kap kiszámítható teljesítményt egyetlen adott tárolón sem. A kiépített átviteli sebesség adatbázison való konfigurálásával kapcsolatos további információkért lásd: [kiépített átviteli sebesség konfigurálása egy Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md). Az adatátviteli sebesség adatbázison történő konfigurálásával kapcsolatos további információkért lásd: az [autoscale átviteli sebességének kiépítése](how-to-provision-autoscale-throughput.md).

Mivel az adatbázisban lévő összes tároló osztozik a kiépített átviteli sebességen, Azure Cosmos DB nem biztosít kiszámítható átviteli garanciát az adatbázis egy adott tárolójához. Egy adott tároló által fogadott átviteli sebesség része a következőktől függ:

* A tárolók száma.
* A különböző tárolók partíciós kulcsai közül választhat.
* A munkaterhelés elosztása a tárolók különböző logikai partíciói között. 

Azt javasoljuk, hogy az átviteli sebességet egy adatbázison konfigurálja, ha az átviteli sebességet több tárolón szeretné megosztani, de nem kívánja az átviteli sebességet egyetlen adott tárolóhoz hozzárendelni. 

Az alábbi példák azt mutatják be, hogy hol érdemes az átviteli sebességet az adatbázis szintjén kiépíteni:

* Több-bérlős alkalmazás esetében hasznos a kiosztott átviteli sebesség megosztása egy tárolón keresztül. Minden felhasználó egy különálló Azure Cosmos-tárolóban szerepelhet.

* Egy adatbázis kiosztott átviteli sebessége több tárolóban való megosztása akkor hasznos, ha olyan NoSQL-adatbázist telepít át, mint például a MongoDB vagy a Cassandra, amely virtuális gépek vagy helyszíni fizikai kiszolgálók fürtön található, és Azure Cosmos DB. Gondoljon arra, hogy az Azure Cosmos-adatbázison konfigurált kiépített átviteli sebességet logikai egyenértékűként, de költséghatékonyan és rugalmasan, a MongoDB vagy a Cassandra-fürt számítási kapacitása szerint konfigurálja.  

A kiosztott átviteli sebességgel rendelkező adatbázisban létrehozott összes tárolót [partíciós kulccsal](partitioning-overview.md)kell létrehozni. Egy adott időpontban az adatbázisban lévő tároló számára lefoglalt átviteli sebesség a tároló összes logikai partícióján át lesz osztva. Ha olyan tárolókkal rendelkezik, amelyek osztoznak egy adatbázison konfigurált kiépített átviteli sebességen, nem alkalmazhat szelektíven egy adott tárolóra vagy logikai partícióra az átviteli sebességet. 

Ha egy logikai partíció munkaterhelése több, mint az adott logikai partícióhoz lefoglalt átviteli sebesség, a műveletek díja korlátozott. A ráta korlátozása esetén növelheti a teljes adatbázis átviteli sebességét, vagy próbálja megismételni a műveletet. További információ a particionálásról: [logikai partíciók](partitioning-overview.md).

A megosztott átviteli sebességű adatbázisokban lévő tárolók között megoszlik az adott adatbázishoz lefoglalt átviteli sebesség (RU/s). A standard (manuális) kiépített átviteli sebesség esetében akár 25 tárolót is használhat, amelyek legalább 400 RU/s értékkel rendelkeznek az adatbázison. Az autoscale kiépített átviteli sebességével akár 25 tárolót is beállíthat egy olyan adatbázisban, amelyben az autoscale Max 4000 RU/s (a 400-4000 RU/s közötti skálán).

> [!NOTE]
> Február 2020-án egy olyan módosítást vezettünk be, amely lehetővé teszi, hogy legfeljebb 25 tárolót helyezzen el egy megosztott átviteli sebességű adatbázisban, ami jobb lehetővé teszi az átviteli sebesség megosztását a tárolók között. Az első 25 tároló után csak akkor adhat hozzá több tárolót az adatbázishoz, ha [dedikált átviteli sebességgel lett kiépítve](#set-throughput-on-a-database-and-a-container), amely elkülönül az adatbázis megosztott átviteli sebességével.<br>
Ha az Azure Cosmos DB-fiók már tartalmaz egy legalább 25 tárolóval rendelkező, megosztott átvételi sebességű adatbázist, a fiókra és az Azure-előfizetés többi fiókjára nem vonatkozik ez a változás. Ha visszajelzése vagy kérdése van, vegye [fel a kapcsolatot a terméktámogatási szolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . 

Ha a számítási feladatok egy adatbázisban lévő összes gyűjtemény törlését és újbóli létrehozását vonják maguk után, akkor azt javasoljuk, hogy a gyűjtemény létrehozása előtt dobja el az üres adatbázist, és hozzon létre egy új adatbázist. Az alábbi képen látható, hogyan tárolhat egy fizikai partíció egy vagy több olyan logikai partíciót, amely különböző tárolókban található egy adatbázison belül:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Olyan fizikai partíció, amely egy vagy több különböző tárolóhoz tartozó logikai partíciót üzemeltet. " border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Átviteli sebesség beállítása egy adatbázison és egy tárolón

A két modellt kombinálhatja. Az adatátviteli sebesség az adatbázison és a tárolón is engedélyezett. Az alábbi példa bemutatja, hogyan lehet a standard (manuális) kiosztott átviteli sebességet kiépíteni egy Azure Cosmos-adatbázison és egy tárolón:

* Létrehozhat egy *Z* nevű Azure Cosmos-adatbázist a standard (manuális) kiépített átviteli sebességgel a *"K"* RUs használatával. 
* Ezután hozzon létre öt tárolót a-adatbázison *belül a,* *B*, *C*, *D* és *E* névvel. A B tároló létrehozásakor ügyeljen arra, hogy engedélyezze a **tároló beállítás dedikált átviteli sebességét** , és explicit módon konfigurálja a *"P"* kiépített átviteli sebességét ezen a tárolón. A megosztott és a dedikált átviteli sebességet csak az adatbázis és a tároló létrehozásakor lehet konfigurálni. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Az átviteli sebesség beállítása a tároló szintjén":::

* A *"K"* RUs-átviteli sebesség az *a*, *C*, *D* és *E* négy tárolóban van megosztva. Az *a*, a *C*, a *D* vagy az *E* által elérhető átviteli sebesség pontos mennyisége változó. Az egyes tárolók átviteli sebességéhez nem tartoznak SLA-k.
* A *B* nevű tároló garantált, hogy minden alkalommal megkapja a *"P"* RUs átviteli sebességét. Ez a SLA-k által támogatott.

> [!NOTE]
> A kiosztott átviteli sebességgel rendelkező tárolók nem alakíthatók át megosztott adatbázis-tárolóba. A megosztott adatbázis-tárolók nem alakíthatók át dedikált átviteli sebességgel.

## <a name="update-throughput-on-a-database-or-a-container"></a>Adatbázis vagy tároló adatforgalmának frissítése

Miután létrehozta az Azure Cosmos-tárolót vagy egy adatbázist, frissítheti a kiosztott átviteli sebességet. Az adatbázison vagy a tárolón konfigurálható maximális kiépített átviteli sebességnek nincs korlátja.

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> Aktuális kiépített átviteli sebesség

Lekérheti a tárolók vagy adatbázisok kiépített átviteli sebességét a Azure Portal vagy az SDK-k használatával:

* A [Container. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync) a .net SDK-ban található.
* [CosmosContainer. readThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput) a Java SDK-ban.

Ezeknek a módszereknek a válasza a tároló vagy adatbázis [minimálisan kiosztott átviteli sebességét](concepts-limits.md#storage-and-database-operations) is tartalmazza:

* [ThroughputResponse. MinThroughput](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput) a .net SDK-ban.
* [ThroughputResponse. getMinThroughput ()](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput) a Java SDK-ban.

A tényleges RU/mp a fiók konfigurációjától függően változhat. De általában ez a maximális érték:

* 400 RU/s 
* Az aktuális tárterület (GB * 10 RU/s) (ez a megkötés bizonyos esetekben nyugodt lehet, tekintse meg a [magas tárolási/alacsony átviteli sebességű programot](#high-storage-low-throughput-program))
* Az adatbázison vagy a tárolón/100-ben kiépített legmagasabb RU/s

### <a name="changing-the-provisioned-throughput"></a>A kiosztott átviteli sebesség módosítása

A tárolók vagy adatbázisok kiépített átviteli sebessége a Azure Portal vagy az SDK-k használatával méretezhető:

* A [Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync) a .net SDK-ban található.
* [CosmosContainer. replaceThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput) a Java SDK-ban.

Ha **csökkenti a kiépített átviteli sebességet**, a [minimálisra](#current-provisioned-throughput)teheti ezt meg.

Ha **növeli a kiépített átviteli sebességet**, az idő nagy részében a művelet azonnal megtörténik. Vannak azonban olyan esetek, amikor a művelet hosszabb időt vehet igénybe, mivel a rendszerfeladatok kiépítik a szükséges erőforrásokat. Ebben az esetben a kiépített átviteli sebesség módosítására tett kísérlet, miközben a művelet folyamatban van, egy HTTP 423-választ fog eredményezni, amely elmagyarázza, hogy egy másik skálázási művelet folyamatban van.

> [!NOTE]
> Ha olyan nagy mennyiségű betöltési munkaterhelést tervez, amely nagy növekedést igényel a kiépített átviteli sebességben, vegye figyelembe, hogy a skálázási művelet nem rendelkezik SLA-val, és az előző bekezdésben említettek szerint hosszú időt vehet igénybe, ha a növekedés nagy. Érdemes előre megtervezni a méretezést, és megkezdeni a skálázást a munkaterhelés elindítása előtt, és az alábbi módszerekkel végezheti el a folyamatot.

A skálázási folyamat programozott módon ellenőrizhető az [aktuálisan kiosztott átviteli sebesség](#current-provisioned-throughput) beolvasásával és a használatával:

* [ThroughputResponse. IsReplacePending](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending) a .net SDK-ban.
* [ThroughputResponse. isReplacePending ()](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending) a Java SDK-ban.

[Azure monitor metrikák](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) használatával megtekintheti az erőforráson kiépített átviteli sebesség (ru/s) és tárterület előzményeit.

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> Magas tárolási/alacsony átviteli sebességű program

Az [aktuális kiosztott átviteli sebesség](#current-provisioned-throughput) című szakaszban leírtak szerint a tárolón vagy adatbázison kiépíthető minimális átviteli sebesség számos tényezőtől függ. Az egyik közülük a jelenleg tárolt adatok mennyisége, mivel a Azure Cosmos DB GB tárterületen legalább 10 RU/s kapacitást érvényesít.

Ez olyan helyzetekben lehet fontos, amikor nagy mennyiségű adat tárolására van szükség, de az összehasonlításhoz alacsony átviteli sebességre van szükség. A forgatókönyvek jobb kielégítése érdekében a Azure Cosmos DB egy **"nagy tárolási/alacsony átviteli sebességű" programot** vezetett be, amely csökkenti az ru/s/GB korlátozásokat a jogosult fiókokra vonatkozóan.

A programhoz való csatlakozáshoz és a teljes jogosultság felméréséhez mindössze annyit kell tennie, hogy kitölti [ezt a kérdőívet](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u). A Azure Cosmos DB csapat ezután nyomon követi és folytatja a bevezetést.

## <a name="comparison-of-models"></a>Modellek összehasonlítása
Ez a táblázat a kiépítési standard (manuális) átviteli sebességének összehasonlítását mutatja be egy adatbázison és egy tárolón. 

|**Paraméter**  |**Standard (manuális) átviteli sebesség egy adatbázison**  |**Standard (manuális) átviteli sebesség egy tárolón**|**Átviteli sebesség egy adatbázison** | **Átviteli sebesség egy tárolón**|
|---------|---------|---------|---------|---------|
|Belépési pont (minimum RU/s) |400 RU/s. Legfeljebb 25 tárolót tartalmazhat, és a tárolók minimális száma: RU/s.</li> |400| 400 – 4000 RU/s közötti autoskálázás. Legfeljebb 25 tárolót tartalmazhat, és a tárolók minimális száma: RU/s.</li> | 400 – 4000 RU/s közötti autoskálázás.|
|Legalább RU/s/tároló|--|400|--|400 – 4000 RU/s közötti autoskálázás|
|Maximális RUs|Korlátlan, az adatbázison.|Korlátlan, a tárolón.|Korlátlan, az adatbázison.|Korlátlan, a tárolón.
|Egy adott tárolóhoz rendelt vagy elérhető RUs|Nincs garancia. Az adott tárolóhoz rendelt RUs a tulajdonságoktól függ. A tulajdonságok választhatják a tárolók partíciós kulcsait, amelyek osztoznak az átviteli sebességen, a munkaterhelés eloszlásán és a tárolók számánál. |A tárolón konfigurált összes RUs kizárólag a tároló számára van fenntartva.|Nincs garancia. Az adott tárolóhoz rendelt RUs a tulajdonságoktól függ. A tulajdonságok választhatják a tárolók partíciós kulcsait, amelyek osztoznak az átviteli sebességen, a munkaterhelés eloszlásán és a tárolók számánál. |A tárolón konfigurált összes RUs kizárólag a tároló számára van fenntartva.|
|Tároló maximális tárterülete|Korlátlan.|Korlátlan|Korlátlan|Korlátlan|
|Egy tároló logikai partíciójának maximális átviteli sebessége|10K RU/s|10K RU/s|10K RU/s|10K RU/s|
|Tárolók logikai partícióinak maximális tárterülete (adatsorok és indexek)|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Következő lépések

* További információ a [logikai partíciókhoz](partitioning-overview.md).
* Ismerje meg, hogyan hozhat [létre standard (manuális) Azure Cosmos-tárolón](how-to-provision-container-throughput.md).
* Ismerje meg, hogyan hozhat [létre standard (manuális) átviteli sebességet egy Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md).
* Megtudhatja, hogyan építhet ki az [Azure Cosmos-adatbázison vagy-tárolón az adatméretezési sebességet](how-to-provision-autoscale-throughput.md).
