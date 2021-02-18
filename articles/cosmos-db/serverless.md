---
title: Használaton alapuló kiszolgáló nélküli ajánlat Azure Cosmos DB
description: További információ a Azure Cosmos DB használaton alapuló kiszolgáló nélküli ajánlatáról.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3ee8d5f36977a5a9f20c7e636118ffa9f6ee0b6d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570990"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Kiszolgáló nélküli Azure Cosmos DB (előzetes verzió)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB kiszolgáló nélküli használata lehetővé teszi az Azure Cosmos-fiók felhasználáson alapuló használatát, ahol csak az adatbázis-műveletek által felhasznált kérelmekért és az adatok által felhasznált tárterületért kell fizetnie. A kiszolgáló nélküli tárolók másodpercenként több ezer kérést tudnak kiszolgálni, és nincs szükség a kapacitás megtervezésére.

> [!IMPORTANT] 
> Van olyan visszajelzése, amely a kiszolgáló nélküli? Szeretnénk hallani! Nyugodtan eldobni egy üzenetet a Azure Cosmos DB kiszolgáló nélküli csapatnak: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Azure Cosmos DB használatakor minden adatbázis-művelethez a [kérelmek egységében](request-units.md)kifejezett díj tartozik. A díjszabás díja a használt Azure Cosmos-fiók típusától függ:

- A [kiépített átviteli](set-throughput.md) mód esetében az adatbázisokon és tárolókban kiépített átviteli sebesség (kérési egység/másodperc) alapján kell véglegesíteni. Az adatbázis-műveletek költségeit a rendszer levonja a másodpercenként elérhető kérelmek számáról. A számlázási időszak végén a kiépített átviteli sebesség alapján számolunk fel díjat.
- Kiszolgáló nélküli módban a tárolók létrehozásakor nem szükséges átviteli sebességet kiépíteni az Azure Cosmos-fiókban. A számlázási időszak végén az adatbázis műveletei által felhasznált kérelmek számának számlázására kerül sor.

## <a name="use-cases"></a>Használati esetek

A kiszolgáló nélküli legjobb Azure Cosmos DB olyan forgatókönyveket, amelyekben az **időszakos és kiszámíthatatlan forgalom** hosszú üresjárati időpontokat vár. Mivel az ilyen helyzetekben a kiépítési kapacitás nem szükséges, és előfordulhat, hogy a kiszolgáló nélküli Azure Cosmos DB kiszolgáló nélküli, a következő használati esetekben kell figyelembe venni:

- A Azure Cosmos DB első lépései
- Alkalmazások futtatása a
    - feltört, időszakos forgalom, amely nehezen becsülhető, vagy
    - alacsony (<10%) átlag – csúcs forgalom aránya
- Fejlesztés, tesztelés, prototípus készítése és futtatása éles környezetben új alkalmazásokban, ahol a forgalmi minta ismeretlen
- Integráció a kiszolgáló nélküli számítási szolgáltatásokkal, például a [Azure functions](../azure-functions/functions-overview.md)

Tekintse meg a [kiépített átviteli sebesség és a kiszolgáló nélküli cikk közötti választást](throughput-serverless.md) , ahol további útmutatást talál a használati esetnek leginkább megfelelő ajánlat kiválasztásához.

## <a name="using-serverless-resources"></a>Kiszolgáló nélküli erőforrások használata

A kiszolgáló nélküli egy új Azure Cosmos-fióktípus, ami azt jelenti, hogy új fiók létrehozásakor a **kiépített átviteli sebesség** és a **kiszolgáló** nélküli lehetőséget kell választania. Létre kell hoznia egy új kiszolgáló nélküli fiókot a kiszolgáló nélküli használat megkezdéséhez. Az előzetes verzióban az új kiszolgáló nélküli fiók létrehozásának egyetlen támogatott módja [a Azure Portal](create-cosmosdb-resources-portal.md). A meglévő fiókok kiszolgáló nélküli üzemmódba való áttelepítése jelenleg nem támogatott.

Bármely kiszolgáló nélküli fiókban létrehozott tároló kiszolgáló nélküli tároló. A kiszolgáló nélküli tárolók ugyanazokat a képességeket teszik elérhetővé, mint a kiépített átviteli sebesség módban létrehozott tárolók, így az adatok pontosan ugyanúgy olvashatók, írhatók és lekérdezve. A kiszolgáló nélküli fiókok és tárolók azonban speciális tulajdonságokkal is rendelkeznek:

> [!IMPORTANT]
> A korlátozások némelyike enyhíthető vagy eltávolítható, ha a kiszolgáló nélkül általánosan elérhetővé válik, és **a visszajelzés** segít nekünk eldönteni! Forduljon a kiszolgáló nélküli felhasználói élményhez: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- A kiszolgáló nélküli fiókok csak egyetlen Azure-régióban futhatnak. A létrehozása után nem lehet további Azure-régiókat felvenni a kiszolgáló nélküli fiókba.
- A [szinapszis hivatkozás előzetes funkciója](synapse-link.md) nem engedélyezhető kiszolgáló nélküli fiókban.
- A kiépítési sebesség nem szükséges a kiszolgáló nélküli tárolók esetében, ezért a következő utasítások alkalmazhatók:
    - Kiszolgáló nélküli tároló létrehozásakor nem adhat meg átviteli sebességet, és ez hibát jelez.
    - Nem lehet olvasni vagy frissíteni az átviteli sebességet egy kiszolgáló nélküli tárolón, és ez hibát jelez.
    - Nem hozható létre megosztott átviteli sebességű adatbázis egy kiszolgáló nélküli fiókban, így egy hibaüzenetet ad vissza.
- A kiszolgáló nélküli tárolók legfeljebb 50 GB adat és index tárolására használhatók.

## <a name="monitoring-your-consumption"></a>A felhasználás figyelése

Ha korábban már használta a Azure Cosmos DBt a kiépített átviteli sebesség módban, akkor a kiszolgáló nélküli szolgáltatás költséghatékony, ha a forgalom nem igazolja a kiosztott kapacitást. A kompromisszum azt jelzi, hogy a költségek kevésbé kiszámíthatók, mert az adatbázis által feldolgozott kérelmek száma alapján számolunk fel díjat. Ezért fontos, hogy figyelje a jelenlegi fogyasztását.

A fiók **metrikák** ablaktábláján megtalálhatja az Áttekintés lapon az **áttekintő** **kérési egységek** nevű diagramot. Ez a diagram azt mutatja, hogy a fiók hány kérési egységet használ fel:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="A felhasznált kérelmek egységeit bemutató diagram" border="false":::

Az [itt](monitor-request-unit-usage.md)leírtak szerint ugyanazt a diagramot használhatja Azure monitor használatakor. Vegye figyelembe, hogy Azure Monitor lehetővé teszi a [riasztások](../azure-monitor/alerts/alerts-metric-overview.md)beállítását, amelyek segítségével értesítheti, ha a kérés egységének felhasználása egy bizonyos küszöbértéket adott meg.

## <a name="performance"></a><a id="performance"></a>Teljesítmény

A kiszolgáló nélküli erőforrások bizonyos teljesítménybeli jellemzőket adnak meg, amelyek különböznek a kiosztott átviteli sebességtől. A kiszolgáló nélküli ajánlat általánosan elérhetővé válása után a kiszolgáló nélküli tárolók késését 10 ezredmásodperc vagy kevesebb szolgáltatási szintű célkitűzés (SLO) fedi le, az írások esetében pedig 30 ezredmásodperc vagy kevesebb. A pont – olvasási művelet egy adott elem beolvasását jelenti az azonosító és a partíciós kulcs értéke alapján.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a kiszolgáló nélküli használattal a következő cikkekkel:

- [Az Azure Cosmos DB kérelemegységei](request-units.md)
- [Válasszon a kiosztott átviteli sebesség és a kiszolgáló nélküli megoldás között](throughput-serverless.md)
- [Díjszabási modell az Azure Cosmos DB-ben](how-pricing-works.md)
