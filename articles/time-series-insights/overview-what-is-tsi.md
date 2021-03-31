---
title: 'Áttekintés: Mi az Azure Time Series Insights Gen2? – Azure Time Series Insights Gen2 | Microsoft Docs'
description: Ismerje meg Azure Time Series Insights Gen2 módosításait, frissítéseit és funkcióit.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: ae5887657e7657e60348217c21aa0ed17f9f4b07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606300"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Az Azure Time Series Insights Gen2 bemutatása

Azure Time Series Insights Gen2 egy nyílt és skálázható, teljes körű IoT elemzési szolgáltatás, amely a legjobb felhasználói élményt és gazdag API-kat kínálja a hatékony képességeinek a meglévő munkafolyamatba vagy alkalmazásba való integrálásához.

Felhasználhatja az adatok összegyűjtését, feldolgozását, tárolását, lekérdezését és megjelenítését eszközök internetes hálózata (IoT) skálán, amely nagy mértékben kontextusban van és idősorozatra optimalizált.

Azure Time Series Insights Gen2 ad hoc adatelemzési és működési elemzési megoldás, amely lehetővé teszi a rejtett trendek feltárását, a rendellenességek észlelését és a kiváltó okok elemzését. Ez egy nyílt és rugalmas ajánlat, amely megfelel az ipari IoT üzemelő példányok széles körű igényeinek.

## <a name="video"></a>Videó

További információ a Azure Time Series Insights Gen2.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>A IoT-adatmennyiség definíciója

Az IoT-alapú szervezetekben található ipari adatmennyiségek gyakran nem rendelkeznek strukturális konzisztencia-ellenőrzéssel az eszközök és érzékelők különböző természete miatt egy ipari környezetben. A streamek adatait jelentős hiányosságok jellemzik, és néha sérültek az üzenetek, illetve a hamis beolvasás. A IoT adatok gyakran hasznosak az olyan adatbevitelek kontextusában, amelyek az első féltől származó vagy harmadik forrásból származnak, mint például a CRM vagy az ERP, amely a végpontok közötti munkafolyamatok kontextusát adja hozzá. A harmadik féltől származó adatforrásokból, például időjárási adatokból származó bemenetek segíthetnek a telemetria-adatfolyamok bővítésében egy adott telepítésben.

Mindez azt jelenti, hogy a rendszer csak az adat egy részét használja fel működési és üzleti célokra, és az elemzéshez contextualization szükséges. Az ipari jellegű adatelemzések gyakran a hosszabb ideig elhelyezkedő, részletes elemzésre szolgálnak a trendek megismerése és korrelálása érdekében. A begyűjtött IoT-adatok gyakorlatban használható információkhoz való bekapcsolásához a következőket kell tennie:

* Adatfeldolgozás az elemzések tisztításához, szűréséhez, interpolációhoz, átalakításához és előkészítéséhez.
* Egy struktúra, amellyel navigálhat és megismerheti az adatmennyiséget, vagyis az adatnormalizálás és a contextualize.
* Költséghatékony tárolás a feldolgozott (vagy származtatott) adatok és nyers adatok hosszú vagy végtelen megőrzéséhez.

Ezek az adatok konzisztens, átfogó, aktuális és helyes információkat biztosítanak az üzleti elemzéshez és jelentéskészítéshez.

Az alábbi képen egy jellemző IoT-adatfolyam látható.

[![IoT-adatfolyam](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 ipari IoT

A IoT-környezet különböző iparági szegmenseket ölel fel, többek között a gyártást, az autóipart, az energiát, a segédprogramokat, az intelligens épületeket és a tanácsadást. Az ipari IoT-piac ezen széles skáláján olyan Felhőbeli natív megoldások jelennek meg, amelyek átfogó elemzéseket biztosítanak a nagy léptékű IoT-adatmennyiségekről.

Azure Time Series Insights Gen2 ezt a piaci igényt egy kulcsrakész, teljes körű IoT elemzési megoldás biztosításával, amely részletes szemantikai modellezést biztosít az idősoros adatok contextualization, az eszköz-alapú elemzések, valamint a felderítéshez, a trendek, a rendellenességek észleléséhez és az operatív intelligenciához használható legjobb felhasználói élményhez.

Az interaktív adatfeltárási képességekkel kombinálva gazdag működési elemzési platformot használhat Azure Time Series Insights Gen2, hogy kihasználja a IoT-eszközökről gyűjtött adatok mennyiségét. A Gen2-ajánlat a következőket támogatja:

* A többrétegű tárolási megoldás a meleg és a hideg elemzés támogatásával lehetővé teszi az ügyfelek számára, hogy az adatok a meleg és a hideg közötti interaktív elemzéshez, valamint az operatív intelligencia több évtizedes adathoz való átirányítására is lehetőséget biztosítanak.

  * Egy nagymértékben interaktív, meleg elemzési megoldás, amely gyakori és nagy számú lekérdezést hajt végre a rövidebb idő alatt.
  * Az Azure Storage-on alapuló skálázható, teljesítményű és költséges idősorozat-adathalmaz, amely lehetővé teszi az ügyfelek számára, hogy másodpercek alatt is elvégezzék az idősorozat-adatok időbeli alakulását.

* Szemantikai modell támogatása, amely az eszközök és eszközök származtatott és nyers jeleihez társított tartományt és metaadatokat ismerteti.

* Rugalmas elemzési platform a korábbi idősorozat-adatmennyiségek tárolásához az ügyfél tulajdonában lévő Azure Storage-fiókban, ami lehetővé teszi, hogy az ügyfelek tulajdonjogot IoT. Az adatok tárolása a nyílt forráskódú Apache Parquet formátumban történik, amely lehetővé teszi a különböző adatforgatókönyvek, például a prediktív elemzés, a gépi tanulás és más, ismerős technológiákkal, például a Spark és a Databricks használatával végzett egyéni számítások használatát.

* Fejlett lekérdezési API-k és felhasználói élmény, amely az adatelemzési funkciók széles körű, ad hoc adatelemzését, valamint interpolációs, skaláris és aggregált függvények, kategorikus változók, Scatter-ábrák és időeltolási idősorozat-jelek használatát ötvözi a részletes elemzéshez.

* Nagyvállalati szintű platform, amely támogatja a nagyvállalati IoT-ügyfelek méretezési, teljesítménybeli, biztonsági és megbízhatósági igényeit.

* Bővíthetőség és integrációs támogatás a végpontok közötti elemzésekhez. Azure Time Series Insights a Gen2 számos különböző adatforgatókönyvhöz biztosít bővíthető elemzési platformot. A Power BI-összekötő lehetővé teszi, hogy az ügyfelek a Azure Time Series Insights Gen2 közvetlenül a Power BIban végezzék el a BI-és idősorozat-elemzések egységes áttekintését egyetlen ablaktáblán.

A következő ábrán a magas szintű adatfolyam látható.

  [![Főbb képességek](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

A Azure Time Series Insights Gen2 az adatfeldolgozáshoz, a tároláshoz (adatokhoz és metaadatokhoz) és a lekérdezéshez méretezhető utólagos elszámolású díjszabási modellt biztosít, amely lehetővé teszi, hogy az ügyfelek az üzleti igényeknek megfelelően hangolják a használatukat.

Ezeknek a kulcsfontosságú ipari IoT képességeknek a bevezetésével a Azure Time Series Insights Gen2 a következő fő előnyöket nyújtja:  

| Képesség | Előny |
| ---| ---|
| Többrétegű tárolás IoT-méretezési idősorozat-adatsorokhoz | Egy megosztott adatfeldolgozási folyamattal az adatbevitelhez a meleg és a hűtőházi tárolóba is betöltheti az adatmennyiséget. A nagyméretű adatmennyiségek tárolásához használjon meleg tárolót interaktív lekérdezésekhez és hideg tároláshoz. Ha többet szeretne megtudni arról, hogyan használhatja ki a nagy teljesítményű eszközökön alapuló lekérdezéseket, tekintse meg a [lekérdezéseket](./concepts-query-overview.md). |
| Idősorozat-modell a nyers telemetria contextualize, és az eszköz-alapú adatfelismerések származtatása | Az idősorozat-modell használatával példányokat, hierarchiákat, típusokat és változókat hozhat létre az idősoros adataihoz. Ha többet szeretne megtudni a Time Series-modellről, tekintse meg a [Time Series Model](./concepts-model-overview.md)című témakört.  |
| Zökkenőmentes és folyamatos integráció más adatmegoldásokkal | A Azure Time Series Insights Gen2-beli tárolt fájlok a nyílt forráskódú Apache Parquet-fájlokban vannak [tárolva](./concepts-storage.md) . Ez lehetővé teszi az adatintegrációt más adatmegoldásokkal (1. vagy harmadik felekkel) olyan forgatókönyvek esetén, amelyek üzleti intelligenciát, speciális gépi tanulást és prediktív elemzést tartalmaznak. |
| Közel valós idejű adatelemzés | A [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md) felhasználói élmény vizualizációt biztosít a betöltési folyamaton keresztüli összes adatfolyamhoz. Az eseményforrás összekapcsolását követően megtekintheti, feltárhatja és lekérdezheti az események adatforrásait. Ily módon ellenőrizheti, hogy az eszköz az elvárt módon bocsát-e ki adatforrásokat. Egy IoT-eszközt is nyomon követheti az állapot, a termelékenység és az általános hatékonyság érdekében. |
| Bővíthetőség és integráció | Az Power BI Connector integrációja közvetlenül a Time Series Explorer felhasználói felületén érhető el az **exportálási** lehetőséggel, így az ügyfelek a felhasználói élményben létrehozott idősorozat-lekérdezéseket közvetlenül a Power bi asztalra, a más bi Analytics szolgáltatással pedig az idősorozat-diagramokat is megtekinthetik. Ez megnyitja az ajtót egy új forgatókönyvnek az olyan ipari IoT vállalatok számára, akik befektettek a Power BIba a különböző adatforrásokból, például a IoT-sorozatokból származó elemzések egyetlen paneljét biztosítva. |
| Az Azure Time Series Insights Gen2 platformra épülő egyéni alkalmazások | Azure Time Series Insights Gen2 támogatja a [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)-t. Az SDK sokoldalú vezérlőket és egyszerűsített hozzáférést biztosít a lekérdezésekhez. Az SDK használatával egyéni IoT-alkalmazásokat hozhat létre Azure Time Series Insights Gen2, hogy megfeleljen az üzleti igényeinek. A Azure Time Series Insights Gen2- [lekérdezési API-kat](./concepts-query-overview.md) közvetlenül is használhatja az egyéni IoT-alkalmazásokba való adattovábbításhoz. |

## <a name="next-steps"></a>Következő lépések

Ismerkedés a Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Rövid útmutató](./quickstart-explore-tsi.md)

Tudnivalók a használati esetekről:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 használati esetei](./overview-use-cases.md)
