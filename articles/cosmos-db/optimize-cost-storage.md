---
title: A tárterület költségeinek optimalizálása az Azure Cosmos DB-ben
description: Ez a cikk azt ismerteti, hogyan kezelhető a Azure Cosmos DB tárolt adattárolási költségei.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93080850"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>A tárterület költségeinek optimalizálása az Azure Cosmos DB-ben
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB korlátlan tárterületet és átviteli sebességet biztosít. Az átviteli sebességtől eltérően, amelyet az Azure Cosmos-tárolókban vagy-adatbázisokban kell kiépíteni/konfigurálni, a tárterületet fogyasztási alapon számítjuk fel. Csak az Ön által felhasznált logikai tárterületért kell fizetnie, és nem kell előzetesen lefoglalnia a tárterületet. A tárterület automatikusan fel-és leskálázásra kerül az Azure Cosmos-tárolóba felvett vagy eltávolított adathalmazok alapján.

## <a name="storage-cost"></a>Tárolási költség

A tárterületet a GB-os egységgel számoljuk el. A helyi SSD által támogatott tárterületet az adatai és az indexelése használja. A felhasznált teljes tárterület megegyezik az összes olyan régióban használt adattárolási és indexekkel, amelyeket Azure Cosmos DB használ. Ha globálisan replikál egy Azure Cosmos-fiókot három régióban, akkor a teljes tárolási díjat a három régióban kell fizetnie. A tárolási követelmények becsléséhez tekintse meg a [Capacity Planner](https://www.documentdb.com/capacityplanner) eszköz című témakört. A Azure Cosmos DB tárterületének díja $0,25 GB/hó, a legújabb frissítések [díjszabási oldalán](https://azure.microsoft.com/pricing/details/cosmos-db/) talál. Beállíthatja a riasztásokat az Azure Cosmos-tároló által használt tárterület meghatározásához, a tárhely figyeléséhez lásd: [Azure Cosmos db figyelése](./monitor-cosmos-db.md)).

## <a name="optimize-cost-with-item-size"></a>A Cost optimalizálása az elemek méretével

Az optimális teljesítmény és a költséghatékonyság érdekében a Azure Cosmos DB 2 MB vagy kevesebb méretet vár. Ha olyan elemre van szüksége, amely 2 MB-nál nagyobb mennyiségű adattárolást igényel, érdemes lehet újratervezni az elem sémáját. Abban a ritka esetben, ha nem tudja újratervezni a sémát, feloszthatja az elemet alelemekre, és logikailag összekapcsolhatja őket egy közös azonosítóval (AZONOSÍTÓval). Az összes Azure Cosmos DB funkció konzisztensen működik a logikai azonosítóra való rögzítéssel.

## <a name="optimize-cost-with-indexing"></a>A Cost optimalizálása az indexeléssel

Alapértelmezés szerint a rendszer automatikusan indexeli az adatmennyiséget, ami növelheti a felhasznált teljes tárterületet. Azonban egyéni index-házirendeket is alkalmazhat a terhelés csökkentése érdekében. A szabályzattal nem hangolt Automatikus indexelés az elem méretének 10-20%-a. Az indexelési szabályzatok eltávolításával vagy testreszabásával nem számítunk fel extra díjat az írásokhoz, és nem igényelnek további átviteli kapacitást. Az egyéni indexelési házirendek konfigurálásához lásd: [Azure Cosmos DB indexelése](index-policy.md) . Ha korábban már használta a kapcsolatok adatbázisait, úgy gondolhatja, hogy az "index mindent" kifejezés azt jelenti, hogy megduplázta a tárterületet vagy magasabbra. A medián esetben azonban a Azure Cosmos DB sokkal alacsonyabb. A Azure Cosmos DBban az index tárolási terhelése általában alacsony (10-20%) még az automatikus indexeléssel is, mivel az alacsony tárolási helyigényhez lett tervezve. Az indexelési szabályzat kezelésével részletesebben szabályozhatja az index-lábnyom és a lekérdezési teljesítmény kompromisszumát.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimalizálja a költségeket az élettartam és a váltás során

Ha már nincs szüksége az adatokra, könnyedén törölheti azt az Azure Cosmos-fiókjából, ha [időt](time-to-live.md)használ, [megváltoztathatja a hírcsatornát](change-feed.md) , vagy áttelepítheti a régi adatait egy másik adattárba, például az Azure Blob Storage szolgáltatásba vagy az Azure-adattárházba. Az élettartam vagy a TTL esetében a Azure Cosmos DB lehetővé teszi, hogy egy adott időszak után automatikusan törölje a tárolóból az elemeket. Alapértelmezés szerint beállíthatja az időt a tároló szintjén, és felülbírálhatja az értéket cikkenként. Miután beállította az ÉLETTARTAMot egy tárolóban vagy egy elem szintjén, Azure Cosmos DB automatikusan eltávolítja ezeket az elemeket az utolsó módosítás óta eltelt idő után. A Change feed használatával áttelepítheti az adatátvitelt egy másik tárolóba Azure Cosmos DB vagy egy külső adattárba. Az áttelepítés nulla időt vesz igénybe, és amikor végzett az áttelepítés során, törölheti vagy beállíthatja az élettartamot, hogy törölje a forrásként szolgáló Azure Cosmos-tárolót.

## <a name="optimize-cost-with-rich-media-data-types"></a>A Cost optimalizálása a multimédiás adattípusokkal 

Ha olyan multimédiás típusokat szeretne tárolni, mint például a videók, a képek stb., számos lehetőség közül választhat Azure Cosmos DB. Az egyik lehetőség, hogy ezeket a gazdag adathordozó-típusokat Azure Cosmos-elemekként tárolja. Az egyes elemekhez 2 MB-os korlátot kell megadni, és ezt a korlátot elkerülheti az adatelem több alelembe való láncolásával. Az Azure Blob Storage-ban is tárolhatja őket, és a metaadatok használatával hivatkozhat rájuk az Azure Cosmos-elemekből. Ennek a megközelítésnek számos előnye és hátránya van. Az első módszer a legjobb teljesítményt nyújtja a késés, az átviteli SLA-EK és a kulcsrakész globális terjesztési képességek terén a multimédiás adattípusok mellett, a normál Azure Cosmos-elemek mellett. A támogatás azonban magasabb áron érhető el. Az adathordozók Azure Blob Storage-tárolóban való tárolásával csökkentheti a teljes költséget. Ha a késés kritikus, akkor az Azure Cosmos-elemek által hivatkozott Rich Media-fájlok Premium Storage szolgáltatását is használhatja. Ez natív módon integrálható a CDN szolgáltatással, hogy a földrajzi korlátozás megkerülése érdekében alacsonyabb áron szolgálja ki a képeket a peremhálózati kiszolgálóról. Ennek a forgatókönyvnek a lejárati helye, hogy két szolgáltatással kell foglalkoznia – Azure Cosmos DB és az Azure Blob Storage-hoz, ami növelheti a működési költségeket. 

## <a name="check-storage-consumed"></a>A felhasznált tároló keresése

Egy Azure Cosmos-tároló tárolási felhasználásának ellenőrzéséhez futtathat egy HEAD vagy GET kérelmet a tárolón, és megvizsgálhatja a `x-ms-request-quota` és a `x-ms-request-usage` fejléceket is. Ha a .NET SDK-val dolgozik, használhatja a [DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100))és a [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) tulajdonságot is a felhasznált tárterület beszerzéséhez.

## <a name="using-sdk"></a>Az SDK használata

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a Azure Cosmos DB a Cost optimizationról:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](./optimize-cost-reads-writes.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)
