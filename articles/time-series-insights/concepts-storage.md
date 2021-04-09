---
title: A Storage áttekintése – Azure Time Series Insights Gen2 | Microsoft Docs
description: Tudnivalók az adattárolásról Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 748eaca93eaee5ec858ea43261995111cef8ceda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676851"
---
# <a name="data-storage"></a>Adattárolás

Ez a cikk a Azure Time Series Insights Gen2 tárolt adattárolást ismerteti. A szolgáltatás a meleg és a hideg, az adatelérhetőség és az ajánlott eljárásokat fedi le.

## <a name="provisioning"></a>Kiépítés

Azure Time Series Insights Gen2-környezet létrehozásakor a következő lehetőségek közül választhat:

* Hideg adattárolás:
  * Hozzon létre egy új Azure Storage-erőforrást az előfizetésben és a régióban, amelyet kiválasztott a környezetéhez.
  * Egy már meglévő Azure Storage-fiók csatolása. Ez a beállítás csak Azure Resource Manager [sablonból](/azure/templates/microsoft.timeseriesinsights/allversions)telepíthető, és nem látható a Azure Portalban.
* Meleg adattárolás:
  * A meleg tároló nem kötelező, és a kiépítés ideje alatt vagy után is engedélyezhető vagy letiltható. Ha úgy dönt, hogy egy későbbi időpontban engedélyezi a meleg tárolást, és a hűtőházi tárolóban már van [ilyen](concepts-storage.md#warm-store-behavior) , a várt működés megismeréséhez tekintse át az alábbi szakaszt. A meleg tároló adatmegőrzési ideje 7 – 31 nap lehet, és szükség szerint módosítható.

Egy esemény betöltése esetén a rendszer a meleg tárolóban (ha engedélyezve van) és a hűtőházi tárolóban is indexelve van.

[![A tárterület áttekintése](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Az Azure Blob Storage-fiók tulajdonosaként, ahol a hűtőházi adattárolási adat található, teljes hozzáférése van a fiókban lévő összes adathoz. Ez a hozzáférés írási és törlési engedélyeket is tartalmaz. Ne szerkessze vagy törölje azokat az adatAzure Time Series Insights Gen2, amelyek adatvesztést okozhatnak.

## <a name="data-availability"></a>Adatok rendelkezésre állása

Azure Time Series Insights Gen2 partíciókat és indexeli az optimális lekérdezési teljesítményt. Az adatok elérhetővé válnak mind a meleg (ha engedélyezve), mind a hűtőházi tároló lekérdezéséhez az indexelés után. A betöltött adatok mennyisége és a partíción belüli átviteli sebesség is befolyásolhatja a rendelkezésre állást. Tekintse át az eseményforrás [átviteli sebességére vonatkozó korlátozásokat](./concepts-streaming-ingress-throughput-limits.md) és [ajánlott eljárásokat](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) a legjobb teljesítmény érdekében. Egy késési [riasztást](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) is beállíthat, ha értesítést szeretne kapni, ha a környezete problémákat tapasztal az adatfeldolgozás során.

> [!IMPORTANT]
> Előfordulhat, hogy az adatmennyiség akár 60 másodpercig is eltarthat, amíg az adatmennyiség elérhetővé válik. Ha 60 másodpercen túli jelentős késés tapasztalható, küldjön egy támogatási jegyet a Azure Portalon keresztül.

## <a name="warm-store"></a>Meleg tároló

A meleg tárolóban tárolt adatai csak a [Time Series lekérdezési API](./concepts-query-overview.md)-kon, az [Azure Time Series Insights ÁME explorerben](./concepts-ux-panels.md)vagy az [Power bi-összekötőn](./how-to-connect-power-bi.md)keresztül érhetők el. A meleg áruházbeli lekérdezések ingyenesek, és nincs kvóta, de legfeljebb [30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) egyidejű kérés van.

### <a name="warm-store-behavior"></a>Meleg tárolási viselkedés

* Ha ez a beállítás engedélyezve van, a rendszer a környezetbe áramló összes adatfolyamot átirányítja a meleg tárolóba, függetlenül az esemény időbélyegzőtől. Vegye figyelembe, hogy a streaming betöltési folyamat közel valós idejű folyamatos átvitelhez készült, és a múltbeli események betöltése [nem támogatott](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* A megőrzési időtartamot attól függően számítjuk ki, hogy az eseményt a meleg tárolóban indexelték, nem az esemény időbélyegét. Ez azt jelenti, hogy az adatok már nem érhetők el a meleg tárolóban a megőrzési időszak lejárta után, még akkor is, ha az esemény időbélyege a jövőre vonatkozik.
  * Példa: a 10 napos időjárás-előrejelzést tartalmazó esemény betöltése és indexelése egy 7 napos megőrzési időtartammal konfigurált meleg tárolóban történik. 7 nap elteltével az előrejelzés már nem érhető el a meleg áruházban, de a lekérdezhető a hidegtől.
* Ha olyan meglévő környezetben engedélyezi a meleg tárolást, amely már rendelkezik a legutóbbi, a hűtőházi tárolóban tárolt adataival, a meleg tároló nem lesz visszatöltve ezekkel az adataival.
* Ha most engedélyezte a meleg tárolást, és problémákba ütközik a legutóbbi adatai megtekintésével a Explorerben, ideiglenesen válthat a meleg tárolási lekérdezések közül:

   [![Meleg lekérdezések letiltása](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Hűtőházi tároló

Ez a szakasz a Azure Time Series Insights Gen2 vonatkozó Azure Storage-adatokat ismerteti.

Az Azure Blob Storage részletes ismertetését olvassa el a [Storage Blobok bemutatása](../storage/blobs/storage-blobs-introduction.md)című cikkből.

### <a name="your-cold-storage-account"></a>A hűtőházi Storage-fiók

Azure Time Series Insights a Gen2 az Azure Storage-fiók minden eseményének két példányát megőrzi. Az egyik másolat a betöltési idő alapján rendezi az eseményeket, így mindig lehetővé teszi az események elérését egy időben rendezett sorrendben. Az idő múlásával Azure Time Series Insights Gen2 is létrehoz egy újraparticionált másolatot az adatokról, így optimalizálva a végrehajtás alatt álló lekérdezéseket.

Az összes adatait a rendszer határozatlan ideig tárolja az Azure Storage-fiókjában.

#### <a name="writing-and-editing-blobs"></a>Blobok írása és szerkesztése

A lekérdezés teljesítményének és az adatelérhetőségnek a biztosításához ne szerkessze vagy törölje azokat a blobokat, amelyeket Azure Time Series Insights Gen2 hozott létre.

#### <a name="accessing-cold-store-data"></a>A hűtőházi adattárolási adatok elérése

Az adatoknak a [Azure Time Series Insights Explorer](./concepts-ux-panels.md) és az [idősorozat lekérdezési API](./concepts-query-overview.md)-kkal való elérésén kívül az adatok közvetlenül a hűtőházi tárolóban tárolt Parquet-fájlokból is elérhetők. Például elolvashatja, átalakíthatja és megtisztíthatja az Jupyter-jegyzetfüzetben tárolt adatait, majd felhasználhatja a Azure Machine Learning modellnek ugyanabban a Spark-munkafolyamatban való betanításához.

Az adatok közvetlenül az Azure Storage-fiókból való eléréséhez olvasási hozzáféréssel kell rendelkeznie a Azure Time Series Insights Gen2-adatok tárolására használt fiókhoz. Ezután a Parquet fájl létrehozási ideje alapján elolvashatja a kiválasztott adatmennyiséget az `PT=Time` alább ismertetett mappában található parketta- [fájl formátuma](#parquet-file-format-and-folder-structure) szakaszban.  A Storage-fiókhoz való olvasási hozzáférés engedélyezésével kapcsolatos további információkért lásd: [a Storage-fiók erőforrásaihoz való hozzáférés kezelése](../storage/blobs/anonymous-read-access-configure.md).

#### <a name="data-deletion"></a>Adattörlés

Ne törölje a Azure Time Series Insights Gen2-fájljait. A kapcsolódó adatok csak Azure Time Series Insights Gen2 belül kezelhetők.

### <a name="parquet-file-format-and-folder-structure"></a>A parketta fájlformátuma és a mappa szerkezete

A Parquet egy nyílt forráskódú, oszlopos fájlformátum, amely hatékony tárolást és teljesítményt nyújt. Azure Time Series Insights a Gen2 a parketta használatával engedélyezi az idősoros azonosító-alapú lekérdezési teljesítményt a skálán.  

A Parquet fájltípussal kapcsolatos további információkért olvassa el a [parketta dokumentációját](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 az alábbi módon tárolja az adatai másolatait:

* Az első, a kezdeti másolat a betöltési idő alapján van particionálva, és nagyjából megérkezésük sorrendjében tárolja az adatot. Ez az adat a `PT=Time` mappában található:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A második, újraparticionált másolat az idősorozat-azonosítók szerint van csoportosítva, és a `PT=TsId` mappában található:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

A mappában lévő Blobok neveinek timestamp a `PT=Time` Azure Time Series Insights Gen2, és nem az események időbélyege.

A mappában lévő adatok a `PT=TsId` lekérdezéshez az idő múlásával lesznek optimalizálva, és nem statikus. Az újraparticionálás során előfordulhat, hogy egyes események több blobban is szerepelnek. A mappában lévő Blobok elnevezése nem garantált, hogy változatlan maradjon.

Általánosságban elmondható, hogy ha közvetlenül a Parquet-fájlokon keresztül fér hozzá az adataihoz, használja a `PT=Time` mappát.  A jövőbeli funkciók lehetővé teszik a mappa hatékony elérését `PT=TsId` .

> [!NOTE]
>
> * `<YYYY>` leképezi a négy számjegyű év ábrázolását.
> * `<MM>` leképezi a kétjegyű hónapok ábrázolását.
> * `<YYYYMMDDHHMMSSfff>` leképezi a kétjegyű (), kétjegyű hónap (), kétjegyű (), kétjegyű (), kétjegyű (), `YYYY` `MM` `DD` `HH` `MM` kétszámjegyű második ( `SS` ) és három számjegyű ezredmásodperc ( `fff` ) közötti időbélyegzőt ábrázoló ábrázolást.

Azure Time Series Insights Gen2-események a következő módon vannak leképezve a parketta-fájl tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Minden sor tartalmazza az **időbélyegző** oszlopot egy esemény időbélyegzővel. Az időbélyegző tulajdonság soha nem null értékű. Alapértelmezés szerint az **Event várólistán lévő idő** , ha nincs megadva az időbélyegző tulajdonság az eseményforrás számára. A tárolt időbélyegző mindig UTC szerint van.
* Minden sor tartalmazza a Azure Time Series Insights Gen2-környezet létrehozásakor meghatározott idősoros azonosító (TSID) oszlop (oka) t. A TSID-tulajdonság neve tartalmazza az `_string` utótagot.
* A telemetria-adatként elküldett összes többi tulajdonság a `_bool` tulajdonság típusától függően (logikai), `_datetime` (időbélyegző), ( `_long` hosszú), `_double` (Double), ( `_string` String) vagy `dynamic` (dinamikus) oszlopokra van leképezve.  További információt a [támogatott adattípusokról](./concepts-supported-data-types.md)szóló témakörben olvashat.
* Ez a leképezési séma a **(z) V = 1** néven hivatkozott fájlformátum első verziójára vonatkozik, és az azonos nevű alapmappában tárolódik. A szolgáltatás fejlődése során ez a leképezési séma változhat, és a hivatkozási név megnő.

## <a name="next-steps"></a>Következő lépések

* További információ az [adatmodellezésről](./concepts-model-overview.md).

* Tervezze meg [Azure Time Series Insights Gen2-környezetét](./how-to-plan-your-environment.md).
