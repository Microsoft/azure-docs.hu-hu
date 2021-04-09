---
title: Azure Media Services LiveEvent-típusok
description: Azure Media Services egy élő eseményt lehet *átadni* vagy *élő kódolásra* beállítani. Ez a cikk egy részletes táblázatot mutat be, amely összehasonlítja az élő események típusait.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3126cbf4f9d87e9baa7267e309210b751a47069d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897798"
---
# <a name="live-event-types-comparison"></a>Élő eseménytípus összehasonlítása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services egy  [élő esemény](/rest/api/media/liveevents) lehet egy *áteresztő* (egy helyszíni élő kódoló több bitrátás streamet küld) vagy *élő kódolást* (a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld). 

Ez a cikk az élő események típusaival kapcsolatos funkciókat hasonlítja össze.

## <a name="types-comparison"></a>Típusok összehasonlítása 

Az alábbi táblázat összehasonlítja az élő események típusának funkcióit. A típusok beállítása a [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)használatával történik a létrehozás során:

* **LiveEventEncodingType. None** – a helyszíni élő kódoló több bitrátás streamet küld. A betöltött adatfolyamok további feldolgozás nélkül haladnak át az élő eseményen. Más néven átmenő élő esemény.
* **LiveEventEncodingType. Standard** – a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld az élő eseménynek, és Media Services több bitrátás streamet hoz létre. Ha a hozzájárulási hírcsatorna 720p vagy nagyobb felbontású, a **Default720p** -készlet 6 feloldási/bitrátás párokat kódol (részletek a cikk későbbi részében).
* **LiveEventEncodingType. Premium1080p** – a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld az élő eseménynek, és Media Services több bitrátás adatfolyamot hoz létre. A Default1080p-készlet meghatározza a feloldási/bitráta párok kimeneti készletét (részletek a cikk későbbi részében). 

| Szolgáltatás | Átmenő élő esemény | Standard vagy Premium1080p élő esemény |
| --- | --- | --- |
| Az egyszeres sávszélességű bemenetek a felhőben több bitrátára vannak kódolva |Nem |Igen |
| A hozzájárulási csatorna maximális felbontása |4K (4096x2160 at 60 Frames/mp) |1080p (1920x1088 30 keret/mp)|
| Ajánlott maximális rétegek a hozzájárulási adatcsatornában|Legfeljebb 12|Egy hang|
| Maximális rétegek a kimenetben| Ugyanaz, mint a bevitel|Legfeljebb 6 (lásd az alábbi rendszerbeállításokat)|
| A hozzájárulási csatorna maximális összesített sávszélessége|60 Mbps|N/A|
| A hozzájárulás egyetlen rétegének maximális bitrátája |20 Mbps|20 Mbps|
| Többnyelvű hangsávok támogatása|Igen|Nem|
| Támogatott bemeneti videós kodekek |H. 264/AVC és H. 265/HEVC|H. 264/AVC|
| Támogatott kimeneti videós kodekek|Ugyanaz, mint a bevitel|H. 264/AVC|
| Támogatott videó-bitsűrűség, bemenet és kimenet|Akár 10 bites, beleértve a HDR 10/HLG|8 bites|
| Támogatott bemeneti hangkodekek|AAC-LC, it-AAC v1, it-AAC v2|AAC-LC, it-AAC v1, it-AAC v2|
| Támogatott kimeneti hangkodekek|Ugyanaz, mint a bevitel|AAC-LC|
| Kimeneti videó maximális felbontása|Ugyanaz, mint a bevitel|Standard – 720p, Premium1080p – 1080p|
| Bemeneti videó maximális képkockasebessége|60 képkocka/másodperc|Standard vagy Premium1080p – 30 keret/másodperc|
| Bemeneti protokollok|RTMP, töredezett – MP4 (Smooth Streaming)|RTMP, töredezett – MP4 (Smooth Streaming)|
| Ár|Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/media-services/) , és kattintson az "élő videó" fülre.|Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/media-services/) , és kattintson az "élő videó" fülre.|
| Maximális futási idő| 24 óra x 365 nap, élő lineáris | 24 óra x 365 nap, élő lineáris (előzetes verzió)|
| A beágyazott CEA 608/708 feliratok-adattovábbítási képesség|Igen|Yes|
| Az élő átírások bekapcsolásának lehetősége|Igen|Yes|
| A beágyazások behelyezésének támogatása|Nem|Nem|
| Az ad-jelzés támogatása API-n keresztül| Nem|Nem|
| Az ad-jelzés támogatása SCTE-35 sávon belüli üzenetek használatával|Igen|Yes|
| Lehetőség a rövid időpontokból való helyreállításra a hozzájárulási hírcsatornában|Yes|Részleges|
| Nem egységes bemeneti Pallagi Péter támogatása|Yes|Nem – a bemenetnek rögzített GOP-időtartammal kell rendelkeznie|
| Változó képarány bemenetének támogatása|Yes|Nem – a bemenetnek rögzített képkockasebességnek kell lennie. A kisebb variációk a nagy teljesítményű jeleneteknél, például a mozgásban vannak. A hozzájárulási hírcsatorna azonban nem tudja eldobni a képkockák sebességét (például 15 képkockára/másodpercre).|
| Élő esemény automatikus kitöltése a bemeneti adatcsatorna elvesztésekor|No|12 óra elteltével, ha nem fut LiveOutput|

## <a name="system-presets"></a>Rendszerbeállítás

Az élő kódoló kimenetében található felbontásokat és bitrátákat a [presetName](/rest/api/media/liveevents/create#liveeventencoding)határozza meg. Ha **standard** Live Encodert (LiveEventEncodingType. Standard) használ, akkor a *Default720p* előre megadott 6 feloldási/bitráta párokat határoz meg. Ellenkező esetben, ha **Premium1080p** élő kódolót (LiveEventEncodingType. Premium1080p) használ, akkor a *Default1080p* -készlet meghatározza a feloldási/bitráta párok kimeneti készletét.

> [!NOTE]
> A Default1080p-beállításkészlet nem alkalmazható élő eseményre, ha standard Live Encoding-kódolásra van beállítva – hibaüzenetet kap. Akkor is hibaüzenetet kap, ha a Default720p-készletet egy Premium1080p élő kódolóra próbálja alkalmazni.

### <a name="output-video-streams-for-default720p"></a>Kimeneti videó streamek a Default720p

Ha a hozzájárulási hírcsatorna 720p vagy magasabb felbontású, a **Default720p** -készlet a következő 6 rétegbe fogja kódolni a hírcsatornát. Az alábbi táblázatban a bitráta kbit/s, a MaxFPS pedig azt jelzi, hogy a képkockák maximális száma (a keret/másodpercben) a profil a használt H. 264 profilt jelöli.

| Sávszélességű | Szélesség | Magasság | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magas |
| 2200 |960 |540 |30 |Magas |
| 1350 |704 |396 |30 |Magas |
| 850 |512 |288 |30 |Magas |
| 550 |384 |216 |30 |Magas |
| 200 |340 |192 |30 |Magas |

> [!NOTE]
> Ha testre kell szabnia az élő kódolási beállításkészletet, nyisson meg egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a videó felbontásának és a bitrátának a kívánt táblázatát. Az audió kódolási bitráta testreszabása nem támogatott. Győződjön meg arról, hogy csak egy réteg 720p sebességű, és hogy legfeljebb 6 réteg van. Azt is megadhatja, hogy egy beállításkészletet kér.

### <a name="output-video-streams-for-default1080p"></a>Kimeneti videó streamek a Default1080p

Ha a hozzájárulási csatorna 1080p felbontású, a **Default1080p** -készlet a következő 6 rétegbe fogja kódolni a hírcsatornát.

| Sávszélességű | Szélesség | Magasság | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Magas |
| 3000 |1280 |720 |30 |Magas |
| 1600 |960 |540 |30 |Magas |
| 800 |640 |360 |30 |Magas |
| 400 |480 |270 |30 |Magas |
| 200 |320 |180 |30 |Magas |

> [!NOTE]
> Ha testre kell szabnia az élő kódolási beállításkészletet, nyisson meg egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Ellenőrizze, hogy csak egy réteg van-e 1080p-nél, és legfeljebb 6 rétegben. Azt is megadhatja, hogy az Premium1080p Live Encoder számára előre beállított értéket kérjen. A bitráták és a felbontások meghatározott értékei idővel módosíthatók.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Kimeneti hang stream a Default720p és a Default1080p

Mind a *Default720p* , mind a *Default1080p* előre beállított, a hang a sztereó AAC-LC-re, 128 kbps-ra van kódolva. A mintavételezési arány a hozzájárulási hírcsatorna hangfelvételi sebességét követi.

## <a name="implicit-properties-of-the-live-encoder"></a>Az élő kódoló implicit tulajdonságai

Az előző szakasz az élő kódoló olyan tulajdonságait mutatja be, amelyek explicit módon vezérelhetők az előre megadott módon, például a rétegek, a felbontások és a bitráták számával. Ez a szakasz az implicit tulajdonságokat pontosítja.

### <a name="group-of-pictures-gop-duration"></a>Képek csoportjai (GOP) időtartama

Az élő kódoló a hozzájárulási csatorna [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) -szerkezetét követi – ami azt jelenti, hogy a kimeneti rétegek UGYANAZZAL a GOP-időtartammal rendelkeznek. Ezért azt javasoljuk, hogy a helyszíni kódolót úgy konfigurálja, hogy olyan hozzájárulási csatornát hozzon létre, amely rögzített GOP-időtartammal rendelkezik (általában 2 másodperc). Így biztosíthatja, hogy a szolgáltatás kimenő HLS és MPEG DASH-adatfolyamai is rögzített GOP-időtartammal rendelkeznek. A legtöbb eszköz valószínűleg tolerálja a GOP-időtartamok kis variációit.

### <a name="frame-rate"></a>Képkocka sebessége

Az élő kódoló az egyes képkockák időtartamát is követi a hozzájárulási csatornában – ez azt jelenti, hogy a kimeneti rétegek azonos időtartammal rendelkeznek. Ezért azt javasoljuk, hogy a helyszíni kódolót úgy konfigurálja, hogy a rögzített képarányú (legfeljebb 30 képkocka/s) közreműködői adatcsatornát hozzon létre. Ezzel biztosíthatja, hogy a szolgáltatás kimenő HLS és MPEG DASH-adatfolyamai is rögzített méretűek legyenek. A legtöbb eszköz a képarányok kis variációit tolerálhatja, de nincs garancia arra, hogy az élő kódoló megfelelően fog lejátszani egy kimenetet. A helyszíni élő kódoló nem hagyhatja el a képkockákat (pl. az alacsony töltöttségi feltételek alatt) vagy bármilyen módon eltérő a képkockák száma.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>A hozzájárulási csatornák és a kimeneti rétegek feloldása

Az élő kódoló úgy van konfigurálva, hogy elkerülje a járulékos adatcsatorna konvertálását. Ennek eredményeképpen a kimeneti rétegek maximális felbontása nem lépi túl a hozzájárulási hírcsatornát.

Ha például egy 720p-beli Default1080p élő kódolásra konfigurált élő eseményt küld, a kimenetnek csak 5 rétege lesz, és a 3Mbps-től kezdve 720p-ra, 200 kbps-ra. Vagy ha a 360p-ben egy, a szabványos élő kódolásra konfigurált élő eseményre küld egy hozzájárulási csatornát, a kimenet 3 réteget fog tartalmazni (a 288p, a 216p és a 192p felbontása). Ha a degenerált esetben a (z), a (z), a (z) és a (z) 160x90 képpont egy standard élő kódolóhoz való küldését küldi el, a kimenet egy réteget fog tartalmazni a 160x90-feloldásnál, amely a hozzájárulási csatornához hasonló sávszélesség

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>A hozzájárulási csatorna és a kimeneti rétegek bitrátája

Az élő kódoló úgy van konfigurálva, hogy tiszteletben tartsák az előre beállított bitráta-beállításokat, függetlenül attól, hogy a hozzájárulási csatorna milyen bitrátát mutat. Ennek eredményeképpen a kimeneti rétegek bitrátája túllépheti a hozzájárulási hírcsatornát. Ha például egy 1 MB/s sebességű 720p felbontásban küld egy hozzájárulási csatornát, a kimeneti rétegek a fenti [táblázatban](live-event-types-comparison.md#output-video-streams-for-default720p) megmaradnak.

## <a name="next-steps"></a>Következő lépések

[Élő közvetítés – áttekintés](live-streaming-overview.md)
