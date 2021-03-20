---
title: Kvóták és korlátozások a Azure Media Servicesban
description: Ez a témakör a Microsoft Azure Media Services kvótáit és korlátozásait ismerteti.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: d6ca7a444f2a3d4babe220548edb10bd37784be7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92678110"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Kvóták és korlátozások Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk a leggyakoribb Microsoft Azure Media Services-korlátozásokat sorolja fel, amelyeket esetenként kvótának is nevezünk.

> [!NOTE]
> A nem rögzített erőforrások esetében nyisson meg egy támogatási jegyet, amely a kvóták növekedését kéri. Ne hozzon létre további Azure Media Services fiókokat a magasabb korlátok beszerzésére tett kísérlet során.

## <a name="account-limits"></a>Fiók korlátai

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Egyetlen előfizetésben lévő [fiókok Media Services](media-services-account-concept.md) | 100 (rögzített) |

## <a name="asset-limits"></a>Eszközök korlátai

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| [Eszközök](assets-concept.md) /Media Services fiók | 1,000,000|

## <a name="storage-limits"></a>Tárolási korlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Fájlméret| Bizonyos esetekben a Media Services feldolgozásakor támogatott maximális fájlméret megengedett. <sup>1</sup> |
| [Tárfiókok](storage-account-concept.md) | 100<sup>(2)</sup> (rögzített) |

<sup>1</sup> az egyes Blobok maximális mérete jelenleg legfeljebb 5 TB lehet az Azure Blob Storageban. A további korlátozások a szolgáltatás által használt virtuálisgép-méretek alapján Media Services vonatkoznak. A méretkorlát a feltöltött fájlokra, valamint a Media Services feldolgozás (kódolás vagy elemzés) eredményeképpen generált fájlokra is vonatkozik. Ha a forrásfájl mérete meghaladja a 260 GB-ot, a feladat valószínűleg sikertelen lesz. 

Az alábbi táblázat az S1, S2 és S3 Media szolgáltatás számára fenntartott egységek korlátozásait mutatja be. Ha a forrásfájl nagyobb a táblázatban megadott korlátoknál, a kódolási feladata meghiúsul. Ha hosszú ideig kódolja a 4K-feloldási forrásokat, a szükséges teljesítmény eléréséhez S3 Media szolgáltatás számára fenntartott egységeket kell használnia. Ha olyan 4K-tartalommal rendelkezik, amely nagyobb, mint az S3 Media szolgáltatás számára fenntartott egységek 260 GB-os korlátja, nyisson meg egy támogatási jegyet.

|Media szolgáltatás számára fenntartott egység típusa|Maximális bemeneti méret (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> a Storage-fiókoknak ugyanahhoz az Azure-előfizetéshez kell tartoznia.

## <a name="jobs-encoding--analyzing-limits"></a>Feladatok (kódolási & elemzése) korlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| [Feladatok](transforms-jobs-concept.md) /Media Services fiók | 500 000 <sup>(3)</sup> (rögzített)|
| Feladathoz tartozó bemenetek száma | 50 (rögzített)|
| Feladatok kimenete/feladatok | 20 (rögzített) |
| [Átalakítások](transforms-jobs-concept.md) száma Media Services fiókkal | 100 (rögzített)|
| Kimenetek átalakítása egy átalakításban | 20 (rögzített) |
| Fájlok/feladatok bevitele|10 (rögzített)|

<sup>3</sup> ez a szám üzenetsor-, befejezett, aktív és megszakított feladatokat tartalmaz. Nem tartalmazza a törölt feladatokat. 

A fiók 90 napnál régebbi feladatait automatikusan törli a rendszer, még akkor is, ha a rekordok teljes száma nem éri el a maximális kvótát. 

## <a name="live-streaming-limits"></a>Élő közvetítés korlátai

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| [Élő események](live-events-outputs-concept.md) <sup>(4)</sup> /Media Services fiók |5|
| Élő kimenet/élő esemény |3 <sup>(5)</sup> |
| Élő kimenet maximális időtartama | [A DVR ablak mérete](live-event-cloud-dvr.md) |

<sup>4</sup> az élő események korlátaival kapcsolatos részletes információkért lásd: [élő eseménytípus összehasonlítása és korlátai](live-event-types-comparison.md).

<sup>5</sup> élő kimenet létrehozásakor kezdődik, és a törléskor leáll.

## <a name="packaging--delivery-limits"></a>Csomagolási & kézbesítési korlátok

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| [Adatfolyam-végpontok](streaming-endpoint-concept.md) (leállított vagy futó) Media Services fiókkal | 2 |
| Prémium szintű folyamatos átviteli egységek | 10 |
| [Dinamikusjegyzék-szűrők](filters-dynamic-manifest-overview.md)|100|
| [Folyamatos átviteli házirendek](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Egy adott eszközhöz társított egyedi [streaming-lokátorok](streaming-locators-concept.md) | 100<sup>(7)</sup> (rögzített) |

<sup>6</sup> ha egyéni [folyamatos átviteli szabályzatot](/rest/api/media/streamingpolicies)használ, a Media Service-fiókjához korlátozott készletet kell terveznie, és újra fel kell használni a StreamingLocators, amikor ugyanazok a titkosítási beállítások és protokollok szükségesek. Ne hozzon létre új folyamatos átviteli szabályzatot minden egyes adatfolyam-keresőhöz.

<sup>7</sup> a folyamatos átviteli lokátorok nem a felhasználónkénti hozzáférés-vezérlés kezelésére szolgálnak. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

## <a name="protection-limits"></a>Védelmi korlátok

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A [tartalmi kulcsokra vonatkozó házirend](content-key-policy-concept.md) beállításai |30 |
| Licencek havonta az egyes DRM-típusok esetében Media Services Key Delivery Service-fiókban|1,000,000|

## <a name="support-ticket"></a>Támogatási jegy

A nem rögzített erőforrások esetében a [támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával kérheti a kvóták kiemelését. A kérelemben szereplő részletes információkat a kívánt kvóta változásaira, a használati esetekre és a szükséges régiókra vonatkozóan adja meg. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

## <a name="next-steps"></a>Következő lépések

[Áttekintés](media-services-overview.md)
