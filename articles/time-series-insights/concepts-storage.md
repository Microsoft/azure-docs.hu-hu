---
title: A Storage áttekintése – Azure Time Series Insights Gen2 | Microsoft Docs
description: Ismerje meg a Gen2-ben Azure Time Series Insights adattárolást.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: cd26df1de86ee4bdb33050d0bc4769663707733e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725025"
---
# <a name="data-storage"></a>Adattárolás

Ez a cikk a Gen2-ben Azure Time Series Insights adattárolást ismerteti. Lefedi a meleg és hideg adatokat, az adatok rendelkezésre állását és az ajánlott eljárásokat.

## <a name="provisioning"></a>Kiépítés

2. generációs Azure Time Series Insights létrehozásakor a következő lehetőségek állnak rendelkezésre:

* Hideg adattárolás:
  * Hozzon létre egy új Azure Storage-erőforrást a környezetéhez kiválasztott előfizetésben és régióban.
  * Meglévő Azure Storage-fiók csatolása. Ez a lehetőség csak akkor érhető el, ha egy Azure Resource Manager [sablonból](/azure/templates/microsoft.timeseriesinsights/allversions)telepíti, és nem látható a Azure Portal.
* Meleg adattárolás:
  * A meleg tároló nem kötelező, és a kiépítés ideje alatt vagy után engedélyezhető vagy letiltható. Ha úgy dönt, hogy később engedélyezi a meleg tárolást, és [](concepts-storage.md#warm-store-behavior) már vannak adatok a hidegen tárolt tárolóban, tekintse át az alábbi szakaszt, hogy megértse az elvárt viselkedést. A meleg tároló adatmegőrzési ideje 7–31 napra konfigurálható, és ez szükség szerint módosítható.

Amikor egy esemény be van úsodva, a rendszer a meleg tárolóban (ha engedélyezve van) és a hidegen tárolt tárolóban is indexeli.

[![A Storage áttekintése](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> A hidegen tárolt adatokat tároló Azure Blob Storage-fiók tulajdonosaként teljes hozzáféréssel rendelkezik a fiókban lévő összes adathoz. Ez a hozzáférés magában foglalja az írási és törlési engedélyeket is. Ne szerkessze vagy törölje a Gen2 írási Azure Time Series Insights adatokat, mert ez adatvesztést okozhat.

## <a name="data-availability"></a>Adatok rendelkezésre állása

Azure Time Series Insights Gen2 partíciókat és indexeli az adatokat az optimális lekérdezési teljesítmény érdekében. Az adatok a meleg (ha engedélyezve) és a hideg tárolóból is lekérdezhetővé válnak az indexelés után. A betöltött adatok mennyisége és a partíciónkénti átviteli sebesség hatással lehet a rendelkezésre állásra. A legjobb teljesítmény érdekében tekintse át az eseményforrás [átviteli](./concepts-streaming-ingress-throughput-limits.md) sebességére vonatkozó korlátozásokat [és](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) ajánlott eljárásokat. Egy késési riasztást is konfigurálhat, hogy értesítést kap, ha a környezetben problémákat tapasztal az adatok feldolgozása során. [](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)

> [!IMPORTANT]
> Akár 60 másodperc is eltelhet, amíg az adatok elérhetővé válnak. Ha 60 másodpercnél nagyobb késést tapasztal, küldjön támogatási jegyet a Azure Portal.

## <a name="warm-store"></a>Meleg tároló

A meleg tárolóban tárolt adatok csak a [Time Series Query](./concepts-query-overview.md)API-kon, a Azure Time Series Insights [TSI Exploreren](./concepts-ux-panels.md)vagy a [Power BI-összekötőn keresztül érhetők el.](./how-to-connect-power-bi.md) A meleg tároló lekérdezései ingyenesek, és nincs kvóta, de az egyidejű kérések száma [legfeljebb 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) lehet.

### <a name="warm-store-behavior"></a>A meleg tároló viselkedése

* Ha engedélyezve van, a környezetbe streamelt összes adat a meleg tárolóba lesz irányítva, az esemény időbélyegétől függetlenül. Vegye figyelembe, hogy a streamelési adatbecslési folyamat közel valós idejű streamelésre és előzményesemények be nem átvitele nem [támogatott.](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)
* A megőrzési idő kiszámítása az esemény meleg tárolóban történt indexelésének ideje alapján történik, nem az esemény időbélyege alapján. Ez azt jelenti, hogy az adatok már nem érhetők el a meleg tárolóban a megőrzési időtartam eltelte után, még akkor sem, ha az esemény időbélyegzője a jövőre vonatkozik.
  * Példa: egy 10 napos időjárás-előrejelzést tartalmazó esemény egy 7 napos megőrzési időtartammal konfigurált, meleg tárolóba van beállítva és indexelve. Hét nap után az előrejelzés már nem érhető el a meleg tárolóban, de lekérdezhető a hidegtől.
* Ha olyan meglévő környezetben engedélyezi a meleg tárolást, amely már tartalmaz friss adatokat indexelt a hideg tárolásban, vegye figyelembe, hogy a meleg tároló nem lesz feltöltve az adatokkal.
* Ha most engedélyezte a meleg tárolót, és problémákat tapasztal a legutóbbi adatok megtekintésekor az Explorerben, ideiglenesen ki- és bekapcsolhatja a tárolók lekérdezéseit:

   [![Az meleg lekérdezések letiltása](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Hidegtároló

Ez a szakasz az Azure Storage 2. generációs Azure Time Series Insights ismerteti.

Az Azure Blob Storage részletes ismertetését a [Storage-blobok bemutatásában olvashatja el.](../storage/blobs/storage-blobs-introduction.md)

### <a name="your-cold-storage-account"></a>A hidegen ázott tárfiókja

Azure Time Series Insights Gen2 legfeljebb két példányt őriz meg az Azure Storage-fiókban az egyes eseményekből. Az egyik példány a behozási idő szerint rendezett eseményeket tárolja, és mindig engedélyezi az eseményekhez való hozzáférést egy időrendbe rendezett sorrendben. Idővel a Azure Time Series Insights Gen2 létrehoz egy újraparticionált másolatot is az adatokról a nagy teljesítményű lekérdezésekhez való optimalizálás érdekében.

Minden adata határozatlan ideig az Azure Storage-fiókban lesz tárolva.

> [!WARNING]
> Ne korlátozza a nyilvános internet-hozzáférést egy központra vagy eseményforrásra, Time Series Insights a szükséges kapcsolat megszakad.

#### <a name="writing-and-editing-blobs"></a>Blobok írása és szerkesztése

A lekérdezési teljesítmény és az adatok rendelkezésre állásának biztosítása érdekében ne szerkessze vagy törölje a Gen2 által Azure Time Series Insights blobokat.

#### <a name="accessing-cold-store-data"></a>Hozzáférés a hidegen tárolt adatokhoz

Az adatoknak a Azure Time Series Insights [Explorerből](./concepts-ux-panels.md) és a [Time Series Query](./concepts-query-overview.md)API-kból való elérése mellett előfordulhat, hogy közvetlenül is hozzá szeretne férni az adatokhoz a ritkán tárolt Parquet-fájlokból. Például olvashatja, átalakíthatja és megtisztíthatja az adatokat egy Jupyter-notebookban, majd a használatával betaníthatja Azure Machine Learning modellét ugyanabban a Spark-munkafolyamatban.

Ha közvetlenül az Azure Storage-fiókjából fér hozzá az adatokhoz, olvasási hozzáférésre van szüksége ahhoz a fiókhoz, amely a 2. generációs Azure Time Series Insights tárolására szolgál. Ezután a Parquet-fájl létrehozási ideje alapján beolvashatja a kiválasztott adatokat, amelyek a `PT=Time` [Parquet-fájlformátum](#parquet-file-format-and-folder-structure) szakaszban leírt mappában találhatók.  További információ a tárfiók olvasási hozzáférésének engedélyezéséről: A tárfiók erőforrásaihoz való [hozzáférés kezelése.](../storage/blobs/anonymous-read-access-configure.md)

#### <a name="data-deletion"></a>Adattörlés

A Gen2-Azure Time Series Insights ne törölje. A kapcsolódó adatok kezelése csak a Gen2 Azure Time Series Insights belülről.

### <a name="parquet-file-format-and-folder-structure"></a>A Parquet fájlformátuma és mappastruktúrája

A Parquet egy nyílt forráskódú oszlopos fájlformátum, amelyet hatékony tárolásra és teljesítményre terveztek. Azure Time Series Insights Gen2 a Parquet használatával engedélyezi a Time Series ID-alapú lekérdezési teljesítményt nagy méretekben.

A Parquet-fájltípussal kapcsolatos további információkért olvassa el a [Parquet dokumentációját.](https://parquet.apache.org/documentation/latest/)

Azure Time Series Insights Gen2 a következőképpen tárolja az adatok másolatát:

* Az első, kezdeti másolat a beérkezés ideje szerint van particionálva, és nagyjából érkezési sorrendben tárolja az adatokat. Ezek az adatok a következő mappában `PT=Time` találhatók:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A második, újraparticionált másolat idősor-adatok szerint van csoportosítva, és a mappában `PT=TsId` található:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

A mappában lévő blobnevek időbélyegzői nem az események időbélyegének, Azure Time Series Insights Gen2-nek, és nem az adatok érkezési `PT=Time` idejét egyezik.

A mappában lévő adatok idővel lekérdezésre lesznek optimalizálva, `PT=TsId` és nem statikusak. Az újraparticionáció során előfordulhat, hogy egyes események több blobban is jelen vannak. Az ebben a mappában lévő blobok elnevezése nem garantált, hogy változatlan marad.

Ha az adatokat általában közvetlenül Parquet-fájlokon keresztül kell elérnie, használja a `PT=Time` mappát.  A jövőbeli funkciók lehetővé teszik a mappa hatékony `PT=TsId` hozzáférését.

> [!NOTE]
>
> * `<YYYY>` A egy négyjegyű évképre van leképezve.
> * `<MM>` A egy kétjegyű havi megjelenítésre van leképezve.
> * `<YYYYMMDDHHMMSSfff>` egy időbélyegző-ábrázolás négyjegyű évszámmal ( ), kétjegyű hónappal ( ), két számjegyű nap ( ), két számjegyű óra ( ), két számjegyű perc ( ), két számjegyű másodperc ( ), és három számjegyű `YYYY` `MM` `DD` `HH` `MM` `SS` ezredmásodperc ( `fff` ).

Azure Time Series Insights Gen2-események a Következőképpen vannak leképezve a Parquet-fájlok tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Minden sor tartalmazza az **időbélyegző oszlopot** egy esemény-időbélyeggel. Az időbélyeg-tulajdonság soha nem null értékű. Ha az időbélyeg tulajdonság nincs megadva az eseményforrásban, az alapértelmezett érték az eseménysorba állítva.  A tárolt időbélyeg mindig UTC időzónában van megadva.
* Minden sor tartalmazza a 2. generációs Azure Time Series Insights létrehozásakor meghatározott Time Series ID (TSID) oszlopokat. A TSID tulajdonság neve tartalmazza az `_string` utótagot.
* A telemetriai adatokként küldött többi tulajdonság a tulajdonság típusától függően olyan oszlopnevekre van leképezve, amelyek végződnek `_bool` `_datetime` (logikai), (időbélyeg), `_long` (hosszú), `_double` (dupla), `_string` (sztring) vagy (dinamikus) végződő `dynamic` oszlopnevekre.  További információ: Támogatott [adattípusok.](./concepts-supported-data-types.md)
* Ez a leképezési séma a fájlformátum első verziójára vonatkozik, amely **V=1** néven hivatkozik rá, és az azonos nevű alapmappában van tárolva. A funkció fejlődésével ez a leképezési séma változhat, és a hivatkozás neve növekszik.

## <a name="next-steps"></a>Következő lépések

* További információ az [adatmodellezésről:](./concepts-model-overview.md).

* Tervezze meg [Azure Time Series Insights Gen2-környezetet.](./how-to-plan-your-environment.md)
