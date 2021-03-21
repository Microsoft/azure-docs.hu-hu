---
title: A mobilitási szolgáltatások (előzetes verzió) adatstruktúrája Microsoft Azure Maps-ben
description: Ismerje meg, Hogyan szerveződik az adatgyűjtés a Azure Maps mobilitási szolgáltatások (előzetes verzió) területének Metro területein. Itt megtekintheti, hogy mely mezők tárolják a nyilvános tranzittal kapcsolatos információkat és sorokat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904720"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Adatstruktúrák a Azure Maps mobilitási szolgáltatásokban (előzetes verzió) 

> [!IMPORTANT]
> Azure Maps mobilitási szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Ez a cikk a Metro terület koncepcióját mutatja be [Azure Maps mobilitási szolgáltatásokban](/rest/api/maps/mobility). Megbeszéljük azokat a gyakori mezőket, amelyeket a rendszer akkor ad vissza, ha a szolgáltatás lekérdezi a nyilvános tranzitot és a sorokat. Javasoljuk, hogy a mobilitási szolgáltatások API-kkal való fejlesztés előtt olvassa el ezt a cikket.

## <a name="metro-area"></a>Metro térség

A mobilitási szolgáltatások (előzetes verzió) az egyes támogatott Metro területek szerint vannak csoportosítva. A Metro-területek nem követik a város határait. A Metro területén több város, sűrűn kitöltött város és Környező városok is szerepelhetnek. Valójában egy ország/régió lehet egy metró terület. 

A `metroID` egy Metro-körzet azonosítója, amellyel hívható meg a [beolvasás Metro környék info API](/rest/api/maps/mobility/getmetroareainfopreview). Használja a "Metro beolvasása" API-t, hogy átAzure Maps tekintse az átviteli típusokat, az átléptető irodákat, az aktív riasztásokat és a kiválasztott Metro további részleteit. A támogatott Metro-területeket és metroIDs is kérheti. A Metro-területek azonosítói változhatnak.

**metroID:** 522   **neve:** Seattle-Tacoma-Bellevue

![Seattle – Metro-térség](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Azonosítók leállítása

Az adatátviteli leállásokat két típusú azonosító, az [általános árutovábbítási hírcsatorna-specifikáció (GFTS)](http://gtfs.org/) azonosítója és a Azure Maps leállítása azonosító lehet. A GFTS-azonosító neve stopKey, és a Azure Maps leállítás AZONOSÍTÓját stopID-ként nevezik. Ha gyakran hivatkozik az átvitelre, javasoljuk, hogy használja a Azure Maps leállítás azonosítót. a stopID stabilabb és valószínűleg változatlan marad, amíg a fizikai leállítás megtörténik. A GTFS leállítása azonosító gyakrabban frissül. A GTFS leállítási azonosítója például frissíthető a GTFS-szolgáltatói kérelemben, vagy új GTFS-verzió kiadása esetén. Bár a fizikai leállítás nem változott, a GTFS leállítási azonosítója változhat.

Először is kérheti a közeli átvitelt a [közeli tranzit API](/rest/api/maps/mobility/getnearbytransitpreview)-k használatával.

## <a name="line-groups-and-lines"></a>Sorok és sorok

A mobilitási szolgáltatások (előzetes verzió) párhuzamos adatmodellt használnak a vonalak és a sorok csoportjaihoz. Ez a modell a [GTFS](http://gtfs.org/) -útvonalakból és az utazások adatainak öröklésére szolgáló változások jobb kezelésére szolgál.


### <a name="line-groups"></a>Sorcsoportok

A Sorcsoportok olyan entitások, amelyek az összes olyan sort csoportosítják, amelyek logikailag ugyanahhoz a csoporthoz tartoznak. Egy sor általában két sort tartalmaz, egyet az A és a B pont között, a másik pedig a B pontról A A-re tér vissza. Mindkét vonal ugyanahhoz a nyilvános szállítási ügynökhöz tartozik, és ugyanazt a sorszámot fogja tartalmazni. Előfordulhatnak azonban olyan esetek, amikor egy csoport kettőnél több sora van, vagy csak egyetlen sor van bennük.


### <a name="lines"></a>Vonalak

A fentiekben leírtak szerint az egyes sorok egy sor halmazból állnak. Az egyes sorok csoportjai két sorból állnak, és mindegyik sor egy irányt ismertet.  Vannak azonban olyan esetek, amikor több sor alkot egy sornyi csoportot. Például van egy olyan sor, amely időnként egy adott szomszédságban található, és néha nem. Mindkét esetben ugyanazon a sorszámon működik. Emellett egy vonalas csoport is állhat egyetlen sorból. Az egyetlen irányú körkörös vonal egy olyan Ling-csoport, amely egy sorral rendelkezik.

Első lépésként a [beolvasás vonal API](/rest/api/maps/mobility/gettransitlineinfopreview)-val kérheti a sorokra vonatkozó kérelmeket.


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kérhet továbbítási információkat a mobilitási szolgáltatások (előzetes verzió) használatával:

> [!div class="nextstepaction"]
> [Adatátviteli adatkérés](how-to-request-transit-data.md)

Ismerje meg, hogyan kérhet valós idejű adatszolgáltatásokat a mobilitási szolgáltatások (előzetes verzió) használatával:

> [!div class="nextstepaction"]
> [Valós idejű adatkérések igénylése](how-to-request-real-time-data.md)

A Azure Maps mobilitási szolgáltatások (előzetes verzió) API dokumentációjának megismerése

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatások API dokumentációja](/rest/api/maps/mobility)