---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: f4bdd5332b3c10cf375c7c04ede25137328714a2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073656"
---
>[!NOTE]
>A nem rögzített erőforrások esetében nyisson meg egy támogatási jegyet, amely a kvóták növekedését kéri. Ne hozzon létre további Azure Media Services fiókokat a magasabb korlátok beszerzésére tett kísérlet során.

| Erőforrás | Korlát | 
| --- | --- | 
| Egyetlen előfizetésben lévő fiókok Azure Media Services | 25 (rögzített) |
| Media szolgáltatás számára fenntartott egységek száma Media Services fiókban |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Feladatok/Media Services fiók | 50,000<sup>2</sup> |
| Kapcsolt műveletek feladatonként | 30 (rögzített) |
| Eszközök/Media Services fiók | 1,000,000|
| Objektumok műveletenként | 50 |
| Objektumok feladatonként | 100 |
| Egy objektumhoz egyszerre társított egyedi keresők | 5<sup>4</sup> |
| Élő csatornák száma Media Services fiókkal |5|
| Leállított állapotú programok csatornánként |50|
| Futó állapotú programok csatornánként |3|
| Leállított vagy futó adatfolyam-végpontok Media Services fiókban|2|
| Streamelési egységek streamvégpontonként |10 |
| Tárfiókok | 100<sup>5</sup> (rögzített) |
| Házirendek | 1,000,000<sup>6</sup> |
| Fájlméret| Bizonyos esetekben a Media Services feldolgozásához támogatott maximális fájlméret megengedett. <sup>7</sup> |

<sup>1</sup> Ha megváltoztatja például az S2 és S1 közötti értéket, a rendszer alaphelyzetbe állítja a fenntartott egységek maximális korlátját.

<sup>2</sup> Ez a szám üzenetsor-, befejezett, aktív és megszakított feladatokat tartalmaz. Nem tartalmazza a törölt feladatokat. A régi feladatokat a **IJob. Delete** vagy a **delete** HTTP-kérelem használatával törölheti.

2017. április 1-től a fiókhoz tartozó, 90 napnál régebbi feladatokat automatikusan törli a rendszer, valamint a hozzá tartozó feladattal. Az automatikus törlés akkor is megtörténik, ha a rekordok teljes száma nem éri el a maximális kvótát. A feladat és a feladat adatainak archiválásához használja az [eszközök kezelése a Media Services .net SDK-val](../articles/media-services/previous/media-services-dotnet-manage-entities.md)című témakörben leírt kódot.

<sup>3</sup> Ha a feladat entitások listázására vonatkozó kérést küld, a kérések száma legfeljebb 1 000 feladatot ad vissza. Az összes elküldött feladat nyomon követéséhez használja a Top vagy a skip lekérdezéseket a [OData rendszerlekérdezési beállítások](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))című témakörben leírtak szerint.

<sup>4</sup> A lokátorok nem a felhasználónkénti hozzáférés-vezérlés kezelésére lettek tervezve. Ha különböző hozzáférési jogokat szeretne biztosítani az egyes felhasználók számára, használja a digitális jogkezelési (DRM) megoldásokat. További információ: [a tartalom Azure Media Servicessal való védelemmel való](../articles/media-services/latest/drm-content-protection-concept.md)ellátása.

<sup>5</sup> A Storage-fiókoknak ugyanahhoz az Azure-előfizetéshez kell tartoznia.

<sup>6</sup> A különböző Media Services-házirendekre vonatkozó 1 000 000-szabályzatok száma korlátozott. Ilyen például a lokátor házirend vagy a ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Ha mindig ugyanazokat a napokat és hozzáférési engedélyeket használja, használja ugyanazt a házirend-azonosítót. További információ és példa: [eszközök kezelése a Media Services .net SDK-val](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> Az egyes Blobok maximális mérete jelenleg legfeljebb 5 TB lehet az Azure Blob Storageban. A további korlátozások a szolgáltatás által használt virtuálisgép-méretek alapján Media Services vonatkoznak. A méretkorlát a feltöltött fájlokra, valamint a Media Services feldolgozás (kódolás vagy elemzés) eredményeképpen generált fájlokra is vonatkozik. Ha a forrásfájl mérete meghaladja a 260 GB-ot, a feladat valószínűleg sikertelen lesz. 

Az alábbi táblázat az S1, S2 és S3 Media szolgáltatás számára fenntartott egységek korlátozásait mutatja be. Ha a forrásfájl nagyobb a táblázatban megadott korlátoknál, a kódolási feladata meghiúsul. Ha hosszú ideig kódolja a 4K-feloldási forrásokat, a szükséges teljesítmény eléréséhez S3 Media szolgáltatás számára fenntartott egységeket kell használnia. Ha olyan 4K-tartalommal rendelkezik, amely nagyobb, mint az S3 Media szolgáltatás számára fenntartott egységek 260 GB-os korlátja, nyisson meg egy támogatási jegyet.

|Media szolgáltatás számára fenntartott egység típusa    |Maximális bemeneti méret (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
