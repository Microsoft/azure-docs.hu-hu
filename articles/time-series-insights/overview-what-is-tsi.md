---
title: 'Áttekintés: Mi az Azure Time Series Insights Gen2? - Azure Time Series Insights Gen2 | Microsoft Docs'
description: A Gen2 változásainak, fejlesztésének és Azure Time Series Insights megismeri.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 4ff26289962aa06f9638644e28da23b646554281
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865314"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Az Azure Time Series Insights Gen2 bemutatása

Azure Time Series Insights Gen2 egy nyílt és skálázható, a legjobb felhasználói élményt és gazdag API-kat kínáló, teljes körű IoT-elemzési szolgáltatás, amely hatékony funkcióit integrálja meglévő munkafolyamatába vagy alkalmazásába.

Segítségével adatokat gyűjthet, feldolgozhat, tárolhat, lekérdezhet és vizualizálhat eszközök internetes hálózata (IoT) skálázható méretekben – olyan adatokat, amelyek nagy mértékben kontextusba vannak stb. és idősorra vannak optimalizálva.

Azure Time Series Insights Gen2 alkalmi adatfeltárásra és működéselemzésre lett tervezve, lehetővé téve a rejtett trendek feltárását, a rendellenességek észlelését és a kiváltó okokat kiváltó elemzésekhez. Ez egy nyílt és rugalmas ajánlat, amely megfelel az ipari IoT-környezetek széles körű igényeinek.

## <a name="video"></a>Videó

További információ a Gen2 Azure Time Series Insights ról.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Az IoT-adatok definíciója

Az eszközigényes szervezetekben az ipari IoT-adatok gyakran nem rendelkeznek szerkezeti konzisztenciával, mivel az eszközök és érzékelők különböző jellegűek egy ipari környezetben. Az ilyen streamek adatait jelentős hiányosságok, néha sérült üzenetek és hamis adatok jellemzik. Az IoT-adatok gyakran értelmezhetők olyan külső vagy külső forrásokból származó további adatbevitelek kontextusában, mint a CRM vagy az ERP, amelyek kontextust nyújtanak a végpontok között munkafolyamatokhoz. A külső adatforrások, például az időjárási adatok bemenetei segíthetnek az adott telepítésben a telemetriastreamek bővítésében.

Mindez azt jelenti, hogy az adatoknak csak egy töredékét használják fel működési és üzleti célokra, az elemzés pedig kontextusba kell ásni. Az ipari adatokat gyakran a trendek jobb átlátása és korrelálása érdekében hosszabb időtartamok részletes elemzése céljából használják. Az összegyűjtött IoT-adatok beavatkozást igénylő elemzésekbe való betekintő adatokba való betekintéséhez a következőre van szükség:

* Adatfeldolgozás az adatok megtisztítása, szűrése, interpolációja, átalakítása és előkészítése az elemzéshez.
* Az adatok között navigálható és megértő struktúra, azaz az adatok normalizálása és kontextusba hozása.
* Költséghatékony tárolás a feldolgozott (vagy származtatott) adatok és nyers adatok hosszú vagy végtelen megőrzéséhez.

Ezek az adatok konzisztens, átfogó, aktuális és helyes információkat kínálnak az üzleti elemzésekhez és a jelentéskészítéshez.

Az alábbi képen egy tipikus IoT-adatfolyam látható.

[![IoT-adatfolyam](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 ipari IoT-hez

Az IoT-környezet sokrétű, és számos iparági szegmensre kiterjedő ügyfelekkel rendelkezik, beleértve a gyártást, a gépjárműipart, az energiát, a segédprogramokat, az intelligens épületeket és a tanácsadást. Az ipari IoT-piac ezen széles skálája tekintetében a nagy méretű IoT-adatokat célzó, átfogó elemzéseket lehetővé tő natív felhőbeli megoldások még fejlődnek.

Azure Time Series Insights Gen2 egy kulcsrakész, végpontok között működő IoT-elemzési megoldást kínál, amely gazdag szemantikai modellezéssel teszi lehetővé az idősorozat-adatok, az eszközalapú elemzések és a legjobb felhasználói élmény felderítését, trendek felderítését, anomáliadetektálását és az operatív intelligenciát.

Az interaktív adatfeltárási képességekkel kombinált gazdag operatív elemzési platform segítségével a Azure Time Series Insights Gen2 használatával több értéket nyerhet ki az IoT-eszközökből gyűjtött adatokból. A Gen2 ajánlat a következő szolgáltatásokat támogatja:

* A többrétegű tárolási megoldás meleg és hideg elemzéssel támogatja az ügyfelek számára az adatok meleg és hideg közötti útválasztását a meleg és a hideg adatok interaktív elemzéséhez, valamint az üzemeltetési intelligenciát több évtizedes előzményadatokhoz.

  * Interaktív, gyakran használt elemzési megoldás gyakori és nagy számú lekérdezés végrehajtásához rövidebb időtartamra vonatkozó adatokhoz
  * Az Azure Storage-on alapuló, skálázható, nagy teljesítményre és költségoptimalált idősorozat-adattóra, amely lehetővé teszi az ügyfelek számára, hogy az évnyi idősorozat-adatot másodpercek alatt felrende tetsszen.

* A szemantikai modell támogatása, amely leírja az eszközökből és eszközökből származtatott és nyers jelek tartományát és metaadatait.

* Rugalmas elemzési platform az előzmény-idősorozat-adatok ügyfél tulajdonában lévő Azure Storage-fiókban való tárolásához, így lehetővé teszi az ügyfelek számára az IoT-adatok tulajdonjogát. Az adatok nyílt forráskódú Apache Parquet formátumban vannak tárolva, amely lehetővé teszi a különböző adatforgatókönyvek, például a prediktív elemzés, a gépi tanulás és más, már ismert technológiák, például a Spark és a Databricks használatával végzett egyéni számítások közötti kapcsolatot és interoptálást.

* Bővített lekérdezési API-kat és felhasználói élményt használó gazdag elemzés, amely eszközalapú adatelemzést és részletes, alkalmi adatelemzést kombinál az interpoláció, a skaláris és összesítő függvények, a kategorikus változók, a pontdiagramok és az időeltolási idősorozat-jelek támogatásával a részletes elemzéshez.

* Nagyvállalati szintű platform a nagyvállalati IoT-ügyfelek méretezési, teljesítmény-, biztonsági és megbízhatósági igényeinek támogatásához.

* A végpontok közötti elemzések extenzitása és integrációs támogatása. Azure Time Series Insights Gen2 egy extenzálható elemzési platformot biztosít számos különféle adatforgatókönyvhöz. Power BI összekötővel az ügyfelek a Azure Time Series Insights Gen2-ben lekérdezéseket közvetlenül a Power BI-ba vezényelve egyetlen ablaktáblán megtekinthetik a BI- és idősorozat-elemzéseiket.

Az alábbi ábrán a magas szintű adatfolyam látható.

  [![Főbb képességek](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights Gen2 skálázható használat alapján történő fizetéses díjszabási modellt biztosít az adatfeldolgozáshoz, a tároláshoz (adatok és metaadatok) és a lekérdezéshez, így az ügyfelek az üzleti igényeknek megfelelően finomhangolhatja a használatukat.

Ezeknek a kulcsfontosságú ipari IoT-képességeknek a bevezetésével az Azure Time Series Insights Gen2 a következő fő előnyöket is biztosítja:  

| Képesség | Előny |
| ---| ---|
| Többrétegű tárolás IoT-méretezésű idősorozat-adatokhoz | Az adatok feldolgozására megosztott adatfeldolgozási folyamatokkal az adatokat meleg és hideg tárolókba is be lehet venni. Használjon meleg tárolót interaktív lekérdezésekhez és hideg tárolót nagy mennyiségű adat tárolásához. A nagy teljesítményű eszközalapú lekérdezések előnyeivel kapcsolatos további információkért tekintse meg a [lekérdezéseket.](./concepts-query-overview.md) |
| Time Series-modell a nyers telemetria környezetének elemzésére és az eszközalapú elemzések származtatására | Az idősorozat-modellel példányokat, hierarchiákat, típusokat és változókat hozhat létre az idősorozat-adatokhoz. További információ a Time Series Modelről: [Time Series Model](./concepts-model-overview.md).  |
| Zökkenőmentes és folyamatos integráció más adatmegoldásokkal | A Azure Time Series Insights Gen2-ben tárolt [](./concepts-storage.md) adatok nyílt forráskódú Apache Parquet-fájlokban vannak tárolva. Ez lehetővé teszi az adatintegrációt más ( 1. vagy harmadik féltől származó) adatmegoldásokkal olyan forgatókönyvek esetén, amelyek üzleti intelligenciát, fejlett gépi tanulást és prediktív elemzést tartalmaznak. |
| Közel valós idejű adatfeltárás | A [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md) felhasználói élménye vizualizációt biztosít a bementi folyamaton keresztül streameléssel kapcsolatos összes adathoz. Az eseményforrás csatlakoztatása után megtekintheti, feltárhatja és lekérdezheti az eseményadatokat. Így ellenőrizheti, hogy egy eszköz a várt módon bocsát-e ki adatokat. Az IoT-eszközök állapotát, termelékenységét és általános hatékonyságát is figyelheti. |
| Extenzitás és integráció | A Power BI Connector integrációja közvetlenül a Time Series Explorer felhasználói élményében érhető el az **Exportálás** lehetőséggel, így az ügyfelek közvetlenül a Power BI desktopba exportálhatja a felhasználói élményben létrehozott idősorozat-lekérdezéseket, és más BI-elemzések mellett megtekinthetik az idősorozat-diagramokat. Ez új forgatókönyv-osztályt nyit meg az olyan ipari IoT-vállalatok számára, amelyek az Power BI-be fektettek, és egyetlen ablaktáblát kínálnak a különböző adatforrások, például az IoT idősorok elemzéséhez. |
| A Gen2 platformra Azure Time Series Insights egyéni alkalmazások | Azure Time Series Insights Gen2 támogatja a [JavaScript SDK-t.](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) Az SDK részletes vezérlőket és egyszerűsített lekérdezés-hozzáférést biztosít. Az SDK használatával egyéni IoT-alkalmazásokat hozhat létre Azure Time Series Insights Gen2-re az üzleti igényeinek megfelelően. A Gen2 lekérdezési API-Azure Time Series Insights [közvetlenül](./concepts-query-overview.md) is használhatja az adatokat egyéni IoT-alkalmazásokba való meghajtóként. |

## <a name="region-availability"></a>Régiónkénti elérhetőség

Látogasson el az [Azure globális infrastruktúra-termékek régiók szerint oldalára,](https://azure.microsoft.com/global-infrastructure/services/?products=time-series-insights) ahol megtudhatja, hol Azure Time Series Insights elérhető.

Azure Time Series Insights egyetlen régióban található. Azonban ügyféladatokat tárol/feldolgoz azon a régión kívül, amelyben az ügyfél a szolgáltatáspéldányt üzembe helyez, kivéve az alábbi régiókat, ahol az adatok nem replikálódnak a régión kívül:

* Kelet-Ázsia
* Délkelet-Ázsia
*   Délkelet-Ausztrália
*   Nyugat-Európa
*   Az Egyesült Királyság déli régiója
*   USA nyugati régiója
*   Közép-Franciaország
*   Közép-Kanada

## <a name="next-steps"></a>Következő lépések

A Gen2 Azure Time Series Insights első lépések:

> [!div class="nextstepaction"]
> [Rövid útmutató](./quickstart-explore-tsi.md)

További tudnivalók a használatot esetekkel kapcsolatban:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 használatának esete](./overview-use-cases.md)
