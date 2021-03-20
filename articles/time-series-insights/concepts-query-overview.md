---
title: Adatlekérdezés – Azure Time Series Insights Gen2 | Microsoft Docs
description: Az Adatlekérdezési fogalmak és a REST API áttekintése Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: b1b055fa7f083bd8bccda16498e2894d5d67eace
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374133"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Adatok lekérése az Azure Time Series Insights Gen2-ből

Azure Time Series Insights Gen2 lehetővé teszi az adatok lekérdezését a környezetben tárolt eseményeken és metaadatokon a nyilvános felületi API-kon keresztül. Ezeket az API-kat a [Azure Time Series INSIGHTS ÁME Explorer](./concepts-ux-panels.md)is használja.

A Azure Time Series Insights Gen2 három elsődleges API-kategória érhető el:

* **Környezeti API**-k: ezek az API-k lehetővé teszik a Azure Time Series Insights Gen2-környezet lekérdezéseit. Ezek összegyűjthetők azon környezetek listáját, amelyekhez a hívó hozzáfér és környezeti metaadatokat használ.
* **Time Series Model-Query (TSM-Q) API-k**: lehetővé teszi a létrehozási, olvasási, frissítési és törlési (szifilisz-) műveleteket a környezet idősorozat-modelljében tárolt metaadatokon. Ezek a példányok, típusok és hierarchiák eléréséhez és szerkesztéséhez használhatók.
* **Idősorozat-lekérdezés (TSQ) API-k**: lehetővé teszi a telemetria és az események adatainak lekérését a forrás szolgáltató által rögzített módon, és lehetővé teszi a teljesítménybeli számítások és összesítések használatát az adatokhoz a speciális skaláris és összesítő függvények használatával.

Azure Time Series Insights a Gen2 egy Rich string-alapú kifejezési nyelvet, [idősorozat-kifejezést (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax)használ az [Idősorozat-változókhoz](./concepts-variables.md)tartozó számítások kifejezésére.

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure Time Series Insights Gen2 API-k áttekintése

A következő alapvető API-k támogatottak.

[![Idősorozat-lekérdezés áttekintése](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Környezeti API-k

* [Környezetek beolvasása API](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): azon környezetek listáját adja vissza, amelyekhez a hívó jogosult az elérésére.
* [Környezetek rendelkezésre állási API-k beolvasása](/rest/api/time-series-insights/dataaccessgen2/query/getavailability): az események számának eloszlását adja vissza az esemény időbélyegén `$ts` . Ez az API segít meghatározni, hogy van-e olyan esemény a környezetben, amely az események számát időintervallumra bontva adja vissza, ha vannak ilyenek.
* [Event Schema API beolvasása](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): egy adott keresési span esemény-séma metaadatainak beolvasása. Ez az API segít beolvasni a sémában elérhető összes metaadatot és tulajdonságot a megadott keresési tartományhoz.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series Model-Query (TSM-Q) API-k

Ezen API-k többsége támogatja a Batch-végrehajtási műveletet, hogy több idősorozatos modell entitáson engedélyezze a Batch-SZIFILISZi műveleteket:

* [Model Settings API](/rest/api/time-series-insights/reference-model-apis): a *Get* és a *patch* engedélyezése az alapértelmezett típus és a környezet modellje számára.
* [Típusok API](/rest/api/time-series-insights/reference-model-apis#types-api): lehetővé teszi a szifilisz használatát az idősorozat-típusoknál és a hozzájuk társított változóknál.
* [Hierarchiák API](/rest/api/time-series-insights/reference-model-apis#hierarchies-api): lehetővé teszi a szifilisz használatát az idősorozat-hierarchiákban és a hozzájuk tartozó mezők elérési útjain.
* [Példányok API](/rest/api/time-series-insights/reference-model-apis#instances-api): lehetővé teszi a szifilisz használatát a Time Series-példányokon és a hozzájuk társított példány mezőin. A példányok API emellett a következő műveleteket is támogatja:
  * [Keresés](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): lekéri a találatok részleges listáját az idősorozat-példányok keresésekor a példány attribútumai alapján.
  * [Javaslat](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): megkeresi és javasolja a találatok részleges listáját az idősorozat-példányok a példány attribútumai alapján történő keresésekor.

## <a name="time-series-query-tsq-apis"></a>Idősorozat-lekérdezési (TSQ) API-k

Ezek az API-k a többrétegű tárolási megoldásban mindkét áruházban (meleg és hideg) érhetők el. 

* [Események beolvasása API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): lehetővé teszi a nyers események lekérdezését és beolvasását, valamint a hozzájuk tartozó esemény-időbélyegeket, ahogy azokat a forrás szolgáltató Azure Time Series Insights Gen2 rögzíti. Ez az API lehetővé teszi a nyers események lekérését egy adott idősorozat-azonosító és keresési tartomány számára. Ez az API támogatja a tördelést a kiválasztott bemenet teljes válasz adatkészletének beolvasásához.

  > [!IMPORTANT]
  > A [JSON-összeolvasztási és-Escape-szabályok közelgő változásainak](./ingestion-rules-update.md)részeként a tömbök **dinamikus** típusúak lesznek tárolva. Az ebben a típusban tárolt hasznos adatok tulajdonságai **csak a Get Events API-n keresztül érhetők** el.

* [Series API beolvasása](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): lehetővé teszi a kiszámított értékek lekérdezését és lekérését, valamint a kapcsolódó esemény időbélyegét a változók által a nyers eseményeken definiált számítások alkalmazásával. Ezek a változók az idősorozat-modellben definiálhatók, vagy a lekérdezésben beágyazottan is elérhetők. Ez az API támogatja a tördelést a kiválasztott bemenet teljes válasz adatkészletének beolvasásához.

* [Összesítő sorozat API](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): lehetővé teszi az összesített értékek lekérdezését és lekérését, valamint a hozzájuk tartozó intervallum-időbélyegeket a változók által a nyers eseményeken definiált számítások alkalmazásával. Ezek a változók az idősorozat-modellben definiálhatók, vagy a lekérdezésben beágyazottan is elérhetők. Ez az API támogatja a tördelést a kiválasztott bemenet teljes válasz adatkészletének beolvasásához.
  
  A megadott keresési időtartam és intervallum esetében ez az API egy idősorozat-AZONOSÍTÓhoz tartozó változó időszakonkénti összesített választ adja vissza. A válasz adatkészletben lévő intervallumok számának kiszámítása a következő időpontok számlálásával történik (a UNIX EPOCH óta eltelt ezredmásodpercek száma – január 1., 1970), és az osztásjelek osztása a lekérdezésben megadott intervallum-tartomány méretével.

  A válaszban visszaadott időbélyegek a bal oldali intervallumok, nem pedig az intervallumban szereplő események.


### <a name="selecting-store-type"></a>Tár típusának kiválasztása

A fenti API-k csak a két tárolási típus egyikén hajthatók végre (hideg vagy meleg) egyetlen hívásban. A lekérdezés URL-paramétereinek használatával megadható, hogy a lekérdezés milyen [típusú tárolón](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) fusson. 

Ha nincs megadva paraméter, a lekérdezés alapértelmezés szerint a hűtőházi tárolóban lesz végrehajtva. Ha egy lekérdezés a hideg és a meleg tárolóban is átfedésben lévő időtartományra mutat, javasoljuk, hogy a lekérdezést a hűtőházi tárolóba irányítsa a legjobb megoldás érdekében, mivel a meleg tár csak részleges adattárolást fog tartalmazni. 

A [Azure Time Series Insights Explorer](./concepts-ux-panels.md) és [Power bi összekötő](./how-to-connect-power-bi.md) kezdeményezi a fenti API-kat, és automatikusan kiválasztja a megfelelő storeType paramétert, ahol szükséges. 


## <a name="next-steps"></a>Következő lépések

* További információ az [idősorozat-modellben](./concepts-model-overview.md)definiálható különböző változókról.
* További információ az adatok lekérdezéséről a [Azure Time Series Insights Explorerben](./concepts-ux-panels.md).
