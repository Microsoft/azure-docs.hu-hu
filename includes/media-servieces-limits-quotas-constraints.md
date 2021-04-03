---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 59ff0ba854fa609e6d29f3473f662a89ab5f3dbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95563156"
---
> [!NOTE]
> A nem rögzített erőforrások esetében nyisson meg egy támogatási jegyet, amely a kvóták növekedését kéri. Ne hozzon létre további Azure Media Services fiókokat a magasabb korlátok beszerzésére tett kísérlet során.

### <a name="account-limits"></a>Fiók korlátai

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Egyetlen előfizetésben lévő fiókok Media Services | 100 (rögzített) |

### <a name="asset-limits"></a>Eszközök korlátai

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Eszközök/Media Services fiók | 1,000,000|

### <a name="storage-media-limits"></a>Tárolási (adathordozó) korlátok

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Fájlméret| Bizonyos esetekben a Media Services feldolgozásakor támogatott maximális fájlméret megengedett. <sup>1</sup> |
| Tárfiókok | 100<sup>(2)</sup> (rögzített) |

<sup>1</sup> az egyes Blobok maximális mérete jelenleg legfeljebb 5 TB lehet az Azure Blob Storageban. A további korlátozások a szolgáltatás által használt virtuálisgép-méretek alapján Media Services vonatkoznak. A méretkorlát a feltöltött fájlokra, valamint a Media Services feldolgozás (kódolás vagy elemzés) eredményeképpen generált fájlokra is vonatkozik. Ha a forrásfájl mérete meghaladja a 260 GB-ot, a feladat valószínűleg sikertelen lesz.

Az alábbi táblázat az S1, S2 és S3 Media szolgáltatás számára fenntartott egységek korlátozásait mutatja be. Ha a forrásfájl nagyobb a táblázatban megadott korlátoknál, a kódolási feladata meghiúsul. Ha hosszú ideig kódolja a 4K-feloldási forrásokat, a szükséges teljesítmény eléréséhez S3 Media szolgáltatás számára fenntartott egységeket kell használnia. Ha olyan 4K-tartalommal rendelkezik, amely nagyobb, mint az S3 Media szolgáltatás számára fenntartott egységek 260 GB-os korlátja, nyisson meg egy támogatási jegyet.

|Media szolgáltatás számára fenntartott egység típusa|Maximális bemeneti méret (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> a Storage-fiókoknak ugyanahhoz az Azure-előfizetéshez kell tartoznia.

### <a name="jobs-encoding--analyzing-limits"></a>Feladatok (kódolási & elemzése) korlátok

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Feladatok/Media Services fiók | 500 000 <sup>(3)</sup> (rögzített)|
| Feladathoz tartozó bemenetek száma | 50 (rögzített)|
| Feladatok kimenete/feladatok | 20 (rögzített) |
| Átalakítások száma Media Services fiókkal | 100 (rögzített)|
| Kimenetek átalakítása egy átalakításban | 20 (rögzített) |
| Fájlok/feladatok bevitele|10 (rögzített)|

<sup>3</sup> ez a szám üzenetsor-, befejezett, aktív és megszakított feladatokat tartalmaz. Nem tartalmazza a törölt feladatokat. 

A fiók 90 napnál régebbi feladatait automatikusan törli a rendszer, még akkor is, ha a rekordok teljes száma nem éri el a maximális kvótát. 

### <a name="live-streaming-limits"></a>Élő közvetítés korlátai

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Élő események <sup>(4)</sup> /Media Services fiók |5|
| Élő kimenet/élő esemény |3 <sup>(5)</sup> |
| Élő kimenet maximális időtartama | [A DVR ablak mérete](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> az élő események korlátozásával kapcsolatos részletes információkért lásd az [élő eseménytípus összehasonlítását és korlátozásait](../articles/media-services/latest/live-event-types-comparison.md).

<sup>5</sup> élő kimenet létrehozásakor kezdődik, és a törléskor leáll.

### <a name="packaging--delivery-limits"></a>Csomagolási & kézbesítési korlátok

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatfolyam-végpontok (leállított vagy futó) Media Services fiókkal| 2 |
| Dinamikusjegyzék-szűrők|100|
| Streamelési szabályok | 100 <sup>(6)</sup> |
| Egy adott eszközhöz társított egyedi streaming-lokátorok | 100<sup>(7)</sup> (rögzített) |

<sup>6</sup> ha egyéni [folyamatos átviteli szabályzatot](/rest/api/media/streamingpolicies)használ, a Media Service-fiókjához korlátozott készletet kell terveznie, és újra fel kell használni a StreamingLocators, amikor ugyanazok a titkosítási beállítások és protokollok szükségesek. Ne hozzon létre új folyamatos átviteli szabályzatot minden egyes adatfolyam-keresőhöz.

<sup>7</sup> a folyamatos átviteli lokátorok nem a felhasználónkénti hozzáférés-vezérlés kezelésére szolgálnak. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

### <a name="protection-limits"></a>Védelmi korlátok

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A tartalmi kulcsokra vonatkozó házirend beállításai | 30 |
| Licencek havonta az egyes DRM-típusok esetében Media Services Key Delivery Service-fiókban|1,000,000|

### <a name="support-ticket"></a>Támogatási jegy

A nem rögzített erőforrások esetében a [támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával kérheti a kvóták kiemelését. A kérelemben szereplő részletes információkat a kívánt kvóta változásaira, a használati esetekre és a szükséges régiókra vonatkozóan adja meg. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.