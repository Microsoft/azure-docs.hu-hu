---
title: Élő közvetítés a Azure Media Services használatával többszörös átviteli sebességű streamek létrehozásához | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan állíthat be egy olyan csatornát, amely egyetlen sávszélességű élő streamet fogad egy helyszíni kódolóból, majd élő kódolást végez az adaptív sávszélességű adatfolyamban Media Services használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: b9b4cd54375a13da95259e27da680255f785df45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013210"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Többszörös átviteli sebességű streamek létrehozása az Azure Media Services élő streamelési funkciójával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A 2018. május 12. után az élő csatornák már nem támogatják az RTP/MPEG-2 Transport stream betöltési protokollt. Telepítse át a következőt: RTP/MPEG-2 – RTMP vagy darabolt MP4 (Smooth Streaming) betöltési protokollok.

## <a name="overview"></a>Áttekintés
Azure Media Services (AMS) esetében a **csatorna** az élő adatfolyam tartalmának feldolgozására szolgáló folyamatot jelöli. A **csatorna** az élő bemeneti streameket kétféleképpen fogadja el:

* A helyszíni élő kódoló egy átviteli sebességű streamet küld a csatornának, amely lehetővé teszi, hogy élő kódolást végezzen Media Services az alábbi formátumok valamelyikével: RTMP vagy Smooth Streaming (töredezett MP4). A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* A helyszíni élő kódoló egy többszörös sávszélességű **RTMP** -t vagy **Smooth streaming** (darabolt MP4) küld a csatornára, amely nincs engedélyezve az AMS-mel végzett élő kódoláshoz. A betöltött adatfolyamok további feldolgozás nélkül haladnak át a **Channel** s-ben. Ezt a metódust **áteresztőnek** nevezzük. Használhatja a következő élő kódolókat, amelyek a többszörös sávszélességű Smooth Streaming: MediaExcel, Ateme, Imagine Communications, envivio, Cisco és Elemental. A következő élő kódolók kimenete RTMP: [Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, Teradek kódoló.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

  > [!NOTE]
  > Az átmenő módszer használata a leggazdaságosabb módja az élő közvetítésnek.
  > 
  > 

A Media Services 2,10 kiadástól kezdve a csatorna létrehozásakor megadhatja, hogy a csatorna milyen módon kapja meg a bemeneti adatfolyamot, és hogy szeretné-e, hogy a csatorna élő kódolást végezzen a streamben. Erre két lehetősége van:

* **Nincs** – adja meg ezt az értéket, ha helyszíni élő kódolót szeretne használni, amely a többszörös átviteli sebességű streamet (egy csatlakoztatott adatfolyamot) fogja kiadni. Ebben az esetben a bejövő adatfolyam kódolás nélkül lett átadva a kimenetnek. Ez egy csatorna viselkedése a 2,10 kiadás előtt.  További információ az ilyen típusú csatornák használatáról: [élő közvetítés több sávszélességű streamet létrehozó helyszíni kódolókkal](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – válassza ezt az értéket, ha azt tervezi, hogy Media Services használatával kódolja az egyszeres sávszélességű adatfolyamot a többszörös átviteli sebességű streambe. Vegye figyelembe, hogy az élő kódoláshoz van egy számlázási hatás, és ne feledje, hogy a "Running" állapotú élő kódolási csatorna elhagyása esetén a számlázási költségek is felmerülnek.  Azt javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje a felesleges óradíjat.

> [!NOTE]
> Ez a témakör azokat a csatornákat ismerteti, amelyek engedélyezve vannak az élő kódolás végrehajtásához (**szabványos** kódolási típus). További információ az élő kódoláshoz nem engedélyezett csatornák használatáról: [élő közvetítés a többtényezős streameket létrehozó helyszíni kódolókkal](media-services-live-streaming-with-onprem-encoders.md).
> 
> Ügyeljen rá, hogy ellenőrizze a [szempontok](media-services-manage-live-encoder-enabled-channels.md#Considerations) szakaszt.
> 
> 

## <a name="billing-implications"></a>Számlázási következmények
Az élő kódolási csatorna azonnal elkezdi a számlázást, amint az az API-n keresztül a "Running" állapotra vált.   Az állapotot a Azure Portal vagy a Azure Media Services Explorer eszközben is megtekintheti ( https://aka.ms/amse) .

Az alábbi táblázat azt mutatja be, hogyan képezhetők le a csatornák az API-ban és Azure Portal a számlázási állapotokra. Az állapotok kis mértékben eltérnek az API és a Portal UX között. Amint a csatorna "Running" állapotban van az API-n keresztül, vagy a Azure Portal "Ready" vagy "streaming" állapotban van, a számlázás aktív lesz.
Ha a csatornát továbbra is le szeretné állítani a számlázási adatokból, le kell állítania a csatornát az API-n vagy a Azure Portal.
Ön felelős azért, hogy leállítsa a csatornákat, amikor elkészült az élő kódolási csatornával.  A kódolási csatorna leállításának sikertelensége folyamatos számlázást eredményez.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Csatornák állapota és a számlázási módra való leképezésük módja
Egy csatorna aktuális állapota. A lehetséges értékek a következők:

* **Leállítva**. Ez a csatorna kezdeti állapota a létrehozás után (kivéve, ha az autostart ki lett választva a portálon.) Ebben az állapotban nem történik számlázás. Ebben az állapotban a csatorna tulajdonságai frissíthetők, de a folyamatos átvitel nem engedélyezett.
* **Kezdés**: A csatorna indítása folyamatban van. Ebben az állapotban nem történik számlázás. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel. Ha hiba történik, a csatorna visszaállított állapotba tér vissza.
* **Fut**. A csatorna képes az élő streamek feldolgozására. Most már számlázási használat. A további számlázás elkerülése érdekében le kell állítania a csatornát. 
* **Leállítás folyamatban**. A csatorna leállítása folyamatban van. Ebben az átmeneti állapotban nem történik számlázás. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel.
* **Törlés** folyamatban. A csatorna törlése folyamatban van. Ebben az átmeneti állapotban nem történik számlázás. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel.

Az alábbi táblázat azt mutatja be, hogy a csatorna állapotai hogyan képezhetők le a számlázási módra. 

| Csatorna állapota | A portál felhasználói felületének kijelzői | Számlázási? |
| --- | --- | --- |
| Indítás |Indítás |Nem (átmeneti állapot) |
| Futó |Kész (nincs futó program)<br/>vagy<br/>Streaming (legalább egy futó program) |IGEN |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |No |

### <a name="automatic-shut-off-for-unused-channels"></a>Nem használt csatornák automatikus kikapcsolása
A 2016. január 25-én kezdődően Media Services egy olyan frissítést, amely automatikusan leállítja a csatornát (ha az élő kódolás engedélyezve van), miután egy hosszú ideig nem használt állapotban fut. Ez olyan csatornákra vonatkozik, amelyek nincsenek aktív programok, és amelyek hosszabb ideig nem kaptak meg a bemeneti hozzájárulási hírcsatornát.

A fel nem használt időtartam küszöbértéke névértéken 12 óra, de a változás változhat.

## <a name="live-encoding-workflow"></a>Live Encoding munkafolyamat
A következő ábra egy élő adatfolyam-továbbítási munkafolyamatot jelöl, amelyben egy csatorna egyetlen sávszélességű adatfolyamot kap a következő protokollok egyikében: RTMP vagy Smooth Streaming; Ezután kódolja az adatfolyamot egy többszörös sávszélességű adatfolyamba. 

![Élő munkafolyamat][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Gyakori élő közvetítési forgatókönyv
A leggyakrabban használt streamelési alkalmazások kialakításához általában az alábbi lépések szükségesek.

> [!NOTE]
> Jelenleg az élő stream maximális javasolt időtartama 8 óra.
>
> Az élő kódolással kapcsolatos számlázásra van hatással, és ne feledje, hogy a "Running" állapotú élő kódolási csatorna elhagyása óradíjas díjat von maga után. Azt javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje a felesleges óradíjat. 

1. Csatlakoztasson a számítógéphez egy videokamerát. Indítson el és konfiguráljon egy helyszíni élő kódolót, amely **egyetlen** sávszélesség-adatfolyamot tud kiállítani a következő protokollok egyikében: RTMP vagy Smooth streaming. 

    Ezt a lépést a csatorna létrehozása után is elvégezheti.
2. Hozzon létre és indítson el egy csatornát. 
3. Kérje le a Channel ingest URL (Csatorna betöltési URL-címe) értékét. 

    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
4. Kérje le a csatorna előnézeti URL-címét. 

    Ezen az URL-címen győződhet meg róla, hogy a csatorna rendben megkapja-e az élő streamet.
5. Hozzon létre egy programot. 

    A Azure Portal használatakor a program létrehoz egy eszközt is. 

    Ha .NET SDK-t vagy REST-t használ, létre kell hoznia egy eszközt, és meg kell adnia, hogy a program létrehozásakor ezt az eszközt használja. 
6. A programhoz társított eszköz közzététele.   

    >[!NOTE]
    >Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie. 

7. Amikor készen áll a streamelésre és az archiválásra, indítsa el a programot.
8. Ha kívánja, a kódolólónak küldött jelzéssel hirdetést is elindíthat. A hirdetés a kimeneti adatfolyamba lesz beszúrva.
9. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.
10. Törölje a programot (ha kívánja, törölje az objektumot is).   

> [!NOTE]
> Nagyon fontos, hogy ne feledkezzen meg egy Live Encoding csatorna leállításáról. Ügyeljen arra, hogy az élő kódoláshoz óradíjas számlázásra van szüksége, és ne feledje, hogy a "Running" állapotú élő kódolási csatorna elhagyása esetén a számlázási költségek merülnek fel.  Azt javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje a felesleges óradíjat. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Csatorna bemeneti (betöltési) konfigurációi
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Streaming Protocol betöltése
Ha a **kódoló típusa** **standard**, az érvényes beállítások a következők:

* Egyetlen bitráta **RTMP**
* Egyetlen sávszélességgel **tagolt MP4** (Smooth streaming)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>Single bitrate RTMP (Egyszeres sávszélességű RTMP)
Szempontok:

* A bejövő adatfolyam nem tartalmazhat többszörös sávszélességű videót
* A videó streamnek 15 Mbps alatti átlagos sávszélességgel kell rendelkeznie
* A hangadatfolyamnak 1 MB/s-nál kisebb átlagos sávszélességgel kell rendelkeznie
* A támogatott kodekek a következők:
* MPEG-4 AVC/H. 264 videó
* Alapkonfiguráció, fő, magas profil (8 bites 4:2:0)
* Magas 10 profil (10 bites 4:2:0)
* Magas 422-profil (10 bites 4:2:2)
* MPEG-2 AAC-LC hang
* Monó, sztereó, surround (5,1, 7,1)
* 44,1 kHz mintavételi sebesség
* MPEG-2 stílusú ADTS csomagolás
* Az ajánlott kódolók a következők:
* [Wirecast](media-services-configure-wirecast-live-encoder.md)
* Flash Media élő kódoló

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Single bitrate Fragmented MP4 (Egyszeres sávszélességű, fragmentált MP4) (Smooth Streaming)
Tipikus használati eset:

Helyszíni élő kódolókat használhat olyan szállítóktól, mint például az Ateme, az Ericsson, a envivio, hogy a bemeneti streamet a nyílt interneten keresztül küldje el egy közeli Azure-adatközpontba.

Szempontok:

Ugyanaz, mint az [egyetlen BITRÁTA RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP)esetében.

#### <a name="other-considerations"></a>További szempontok
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* A beérkező videó stream maximális felbontása 1920 × 1080, és legfeljebb 60 mező/másodperc, ha váltottsoros, vagy 30 képkocka/másodperc, ha progresszív.

### <a name="ingest-urls-endpoints"></a>Betöltési URL-címek (végpontok)
A csatorna egy bemeneti végpontot (betöltési URL-címet) biztosít az élő kódolóban, így a kódoló leküldheti a streameket a csatornákra.

A betöltési URL-címeket a csatorna létrehozása után kérheti le. Az URL-címek lekéréséhez a csatornának nem kell **futó** állapotban lennie. Ha készen áll arra, hogy elindítsa az adatküldést a csatornán, a **futtatási** állapotban kell lennie. Miután a csatorna megkezdi az adatfeldolgozást, megtekintheti a streamet az előnézet URL-címén.

Lehetősége van feldarabolt MP4 (Smooth Streaming) élő streamek betöltésére TLS-kapcsolaton keresztül. A TLS-kapcsolat betöltéséhez frissítse a betöltési URL-címet HTTPS-re. Az AMS jelenleg nem támogatja a TLS-t egyéni tartományokkal.  

### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja azokat az IP-címeket, amelyek számára engedélyezett a videó közzététele a csatornán. Az engedélyezett IP-címek megadhatók egyetlen IP-címként (például "10.0.0.1"), egy IP-cím és egy CIDR alhálózati maszk használatával (például "10.0.0.1/22"), vagy egy IP-tartományt IP-cím és egy pontozott decimális alhálózati maszk (például "10.0.0.1 (255.255.252.0)") használatával.

Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

## <a name="channel-preview"></a>Csatorna előnézete
### <a name="preview-urls"></a>Előnézeti URL-címek
A csatornák olyan előzetes verziójú végpontot (előzetes verziójú URL-címet) biztosítanak, amelyet a további feldolgozás és a továbbítás előtt a stream előzetes verziójának megtekintéséhez és ellenőrzéséhez használ.

A csatorna létrehozásakor az előnézeti URL-címet is beolvashatja. Az URL-cím lekéréséhez a csatornának nem kell **futó** állapotban lennie.

Miután a csatorna megkezdi az adatfeldolgozást, megtekintheti az adatfolyamot.

> [!NOTE]
> Jelenleg az előzetes verziójú adatfolyam csak töredékes MP4 (Smooth Streaming) formátumban továbbítható, a megadott bemeneti típustól függetlenül.  Az adatfolyam megtekintéséhez használhatja a Azure Portalban üzemeltetett lejátszót.
> 
> 

### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja azokat az IP-címeket, amelyek számára engedélyezett az előnézeti végponthoz való kapcsolódás. Ha nincs megadva IP-cím, akkor a rendszer nem engedélyezi az IP-címek megadását. Az engedélyezett IP-címek megadhatók egyetlen IP-címként (például "10.0.0.1"), egy IP-cím és egy CIDR alhálózati maszk használatával (például "10.0.0.1/22"), vagy egy IP-tartományt IP-cím és egy pontozott decimális alhálózati maszk (például "10.0.0.1 (255.255.252.0)") használatával.

## <a name="live-encoding-settings"></a>Élő kódolási beállítások
Ez a szakasz azt ismerteti, hogyan lehet beállítani az élő kódoló beállításait a csatornán belül, ha a csatorna **kódolási típusa** **standard** értékre van állítva.

> [!NOTE]
> A hozzájárulási hírcsatorna csak egyetlen hangsávot tartalmazhat – a több zeneszám betöltése jelenleg nem támogatott. Ha élő kódolást végez [a helyszíni élő](media-services-live-streaming-with-onprem-encoders.md)kódolással, akkor küldhet egy hozzájárulási csatornát a Smooth streaming protokollban, amely több hangsávot tartalmaz.
> 
> 

### <a name="ad-marker-source"></a>Ad-jelölő forrása
Megadhatja a forrást az ad markerek jeleihez. Az alapértelmezett érték az **API**, amely azt jelzi, hogy a csatornán belüli élő kódolónak egy aszinkron **ad-jelölő API**-t kell figyelnie.

### <a name="cea-708-closed-captions"></a>CEA 708 zárt feliratok
Egy opcionális jelző, amely azt jelzi, hogy az élő kódoló figyelmen kívül hagyja a beérkező Videóba ágyazott összes CEA 708 feliratot. Ha a jelző hamis értékre van állítva (alapértelmezett), a kódoló felismeri és újból beszúrja a CEA 708 adatokat a kimeneti videó streambe.

#### <a name="index"></a>Index
Azt javasoljuk, hogy egyetlen programbeli Transport streamet (varrat nélküli CSŐTŐL) küldjön. Ha a bemeneti adatfolyam több programot tartalmaz, a csatornán belüli élő kódoló elemzi a program leképezési táblázatát (fiz) a bemenetben, azonosítja az MPEG-2 AAC-ADTS vagy AC-3 rendszer-A vagy AC-3 System-B vagy MPEG-2 Private PES vagy MPEG-1 hang-vagy MPEG-2 audio típusú, valamint a fiz-ban megadott sorrendben elrendezett bemeneteket. Ezután a rendszer a nulla alapú indexet használja az n-edik bejegyzés kiválasztásához az adott elrendezésben.

#### <a name="language"></a>Nyelv
A hangadatfolyam nyelvi azonosítója, amely megfelel az ISO 639-2-nek, például: ENG. Ha nem található, az alapértelmezett érték UND (nincs meghatározva).

### <a name="system-preset"></a><a id="preset"></a>Rendszerszintű beállításkészlet
Meghatározza azt a készletet, amelyet az élő kódoló használ a csatornán belül. Jelenleg az egyetlen megengedett érték a **Default720p** (alapértelmezett).

A **Default720p** a következő 6 rétegbe kódolja a videót.

#### <a name="output-video-stream"></a>Kimeneti videó stream

| Sávszélességű | Szélesség | Magasság | MaxFPS | Profil | Kimeneti adatfolyam neve |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magas |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Magas |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Magas |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Magas |Video_512x288_850kbps |
| 550 |384 |216 |30 |Magas |Video_384x216_550kbps |
| 200 |340 |192 |30 |Magas |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Kimeneti hang Stream

A hang a sztereó AAC-LC 128 kbps sebességű, 48 kHz-es mintavételi gyakorisággal van kódolva.

## <a name="signaling-advertisements"></a>Hirdetmények jelzése
Ha a csatorna Live Encoding engedélyezve van, akkor a folyamat egy olyan összetevője van, amely a videót dolgozza fel, és képes módosítani. Jelezheti, hogy a csatorna beszúrja a kimenő adaptív sávszélességű adatfolyamba az adatcsatornákat és/vagy hirdetéseket. A bementi képek továbbra is felhasználhatók a bemeneti élő adatcsatorna bizonyos esetekben történő lefedéséhez (például egy kereskedelmi szünet során). A hirdetési jelek, a kimenő adatfolyamba beágyazható idő-szinkronizált jelek, hogy a videolejátszó a megfelelő időpontban váltson egy hirdetésre. Tekintse meg ezt a [blogot](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) az erre a célra használt SCTE-35 jelző mechanizmus áttekintéséhez. Az alábbiakban egy tipikus forgatókönyv valósítható meg az élő eseményben.

1. Az esemény elkezdése előtt szerezzen be egy eseményt megelőző képet.
2. Kérje meg a megtekintőt, hogy az esemény befejeződése után egy esemény utáni képet kapjon.
3. Ha az esemény során probléma merül fel, a nézők HIBAÜZENETet kaphatnak (például áramkimaradás a stadionban).
4. Egy AD-BREAK rendszerkép küldésével elrejtheti az élő esemény-hírcsatornát a kereskedelmi szünet során.

A következő tulajdonságokat állíthatja be a hirdetmények jelzése során. 

### <a name="duration"></a>Időtartam
A kereskedelmi szünet időtartama (másodpercben). A kereskedelmi szünet elindításához nem nulla értékű pozitív értéknek kell lennie. Ha egy kereskedelmi szünet folyamatban van, és az időtartam nullára van állítva a CueId, amely megfelel a folyamatos kereskedelmi szünetnek, akkor a rendszer megszakítja a megszakítást.

### <a name="cueid"></a>CueId
A kereskedelmi szünet egyedi azonosítója, amelyet az alsóbb rétegbeli alkalmazás a megfelelő művelet (ek) elvégzéséhez használ. Pozitív egész számnak kell lennie. Ezt az értéket bármely véletlenszerű pozitív egész számra állíthatja, vagy egy felsőbb rétegbeli rendszer használatával követheti nyomon a Cue-azonosítókat. Az API-n keresztüli elküldés előtt végezzen el bizonyos azonosítókat pozitív egész számra.

### <a name="show-slate"></a>Pala megjelenítése
Választható. Azt jelzi, hogy az élő kódoló a kereskedelmi szünet során az [alapértelmezett pala](media-services-manage-live-encoder-enabled-channels.md#default_slate) -képre vált, és elrejti a bejövő videó csatornáját. A hang a pala során is elnémul. Az alapértelmezett érték a **false**. 

A használt rendszerkép a csatorna létrehozásának időpontjában az alapértelmezett Slate Asset ID tulajdonságon keresztül lesz meghatározva. A pala a megjelenítési kép méretének megfelelően lesz kifeszítve. 

## <a name="insert-slate--images"></a>Pala-lemezképek beszúrása
A csatornán belüli élő kódoló jelzéssel jelezhető, hogy átvált egy pala-rendszerképre. Azt is jelezni lehet, hogy a folyamatban lévő pala véget ért. 

Az élő kódoló beállítható úgy, hogy egy pala-képre váltson, és bizonyos helyzetekben elrejtse a beérkező videó jelét (például egy ad-szünet során). Ha egy ilyen betöltés nincs konfigurálva, a bemeneti videó nem kerül maszkolásra az ad-szünet során.

### <a name="duration"></a>Időtartam
A teljes pala időtartama másodpercben. A pala indításához nem nulla pozitív értéknek kell lennie. Ha van folyamatban lévő pala, és a nulla időtartam van megadva, akkor a folyamatban lévő pala leáll.

### <a name="insert-slate-on-ad-marker"></a>Pala beszúrása az ad-jelölőre
Ha igaz értékre van állítva, akkor ez a beállítás úgy konfigurálja az élő kódolót, hogy az ad-szünet során beszúrjon egy pala-rendszerképet. Az alapértelmezett érték az igaz. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Alapértelmezett Slate-eszköz azonosítója

Választható. Megadja a Media Services objektum azonosítóját, amely tartalmazza a pala-rendszerképet. Az alapértelmezett érték null. 


> [!NOTE] 
> A csatorna létrehozása előtt a következő megkötésekkel rendelkező, dedikált eszközként feltöltött képet kell feltölteni (más fájloknak ebben az objektumban kell lennie). Ezt a rendszerképet csak akkor használja a rendszer, ha az élő kódoló egy ad-töréspont miatt beszúrt egy Palat, vagy explicit módon beszúrta egy pala beszúrására. Jelenleg nincs lehetőség egyéni rendszerkép használatára, ha az élő kódoló "bemeneti jel elveszett" állapotba kerül. [Itt](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel)szavazhat erre a szolgáltatásra.

* A legtöbb 1920 × 1080 a megoldásban.
* Legfeljebb 3 MB méretű.
* A fájl nevének *. jpg kiterjesztéssel kell rendelkeznie.
* A képet fel kell tölteni egy adategységbe, amely az adott eszköz egyetlen AssetFile, és ezt a AssetFile elsődleges fájlként kell megjelölni. Az eszköz nem lehet titkosított tároló.

Ha az **alapértelmezett Slate-azonosító** nincs megadva, és a **Beszúrás az ad-jelölőre** beállítás értéke **true (igaz**), akkor a rendszer az alapértelmezett Azure Media Services rendszerképet használja a bemeneti videó adatfolyamának elrejtéséhez. A hang a pala során is elnémul. 

## <a name="channels-programs"></a>Csatorna programjai
A csatornákhoz programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. A programokat a csatornák kezelik. A csatorna és a program kapcsolata nagyon hasonlít a hagyományos médiához, ahol a csatornán állandó tartalom található, és a program hatóköre az adott csatornán futó eseményekre vonatkozik.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási ablak hossza azt is megköveteli, hogy az ügyfelek legfeljebb hány időt tölthetnek vissza az aktuális élő pozícióból. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program egy olyan objektumhoz van társítva, amely a továbbított tartalmat tárolja. Az eszköz az Azure Storage-fiókban lévő blokk blob-tárolóra van leképezve, és az objektumban található fájlok blobként tárolódnak a tárolóban. A program közzétételéhez, hogy az ügyfelek megtekinthessék a streamet, létre kell hoznia egy OnDemand-lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

Egy csatorna legfeljebb három párhuzamosan futó programot támogat, így több archívumot is létrehozhat ugyanahhoz a bejövő adatfolyamhoz. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett hozzon létre és indítson el egy új programot az egyes eseményekhez az élő adatfolyam-alkalmazások programozása című szakaszban leírtak szerint.

Amikor készen áll a streamelésre és az archiválásra, indítsa el a programot. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt. 

Az archivált tartalmak törléséhez állítsa le és törölje a programot, majd törölje a hozzá társított objektumot. Az objektum nem törölhető, ha a program még használja. Először törölje a programot. 

A program leállítását, majd törlését követően a felhasználók igény szerint lekért videóként képesek lesznek streamelni az archivált tartalmakat, egészen addig, amíg nem törli az objektumot.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Élő hírcsatornák miniatűr előnézetének beolvasása
Ha a Live Encoding engedélyezve van, most megtekintheti az élő hírcsatornát, mivel az eléri a csatornát. Ez értékes eszköz lehet annak a megkereséséhez, hogy az élő hírcsatorna valóban eléri-e a csatornát. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>A Channel-állapotok és az állapotok leképezése a számlázási módra
Egy csatorna aktuális állapota. A lehetséges értékek a következők:

* **Leállítva**. Ez a csatorna kezdeti állapota a létrehozás után. Ebben az állapotban a csatorna tulajdonságai frissíthetők, de a folyamatos átvitel nem engedélyezett.
* **Kezdés**: A csatorna indítása folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel. Ha hiba történik, a csatorna visszaállított állapotba tér vissza.
* **Fut**. A csatorna képes az élő streamek feldolgozására.
* **Leállítás folyamatban**. A csatorna leállítása folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel.
* **Törlés** folyamatban. A csatorna törlése folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel.

Az alábbi táblázat azt mutatja be, hogy a csatorna állapotai hogyan képezhetők le a számlázási módra. 

| Csatorna állapota | A portál felhasználói felületének kijelzői | Számlázása? |
| --- | --- | --- |
| Indítás |Indítás |Nem (átmeneti állapot) |
| Futó |Kész (nincs futó program)<br/>vagy<br/>Streaming (legalább egy futó program) |Yes |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |No |

> [!NOTE]
> Jelenleg a csatorna indítási átlaga körülbelül 2 percet vesz igénybe, de időnként akár 20 percig is eltarthat. A csatornák alaphelyzetbe állítása akár 5 percet is igénybe vehet.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Megfontolandó szempontok
* Ha a **standard** kódolási típus egy csatornája a bemeneti forrás/a hozzájárulási adatcsatorna elvesztését tapasztalja, kompenzálja azt a forrásként szolgáló videó/hang lecserélésekor a hiba lappal és a csendtel. A csatorna továbbra is kibocsátja a betöltést, amíg a bemeneti/hozzájárulási hírcsatorna újra nem indul. Azt javasoljuk, hogy egy élő csatorna ne maradjon ilyen állapotban 2 óránál hosszabb ideig. Ezen a ponton túl a csatorna viselkedése a bemeneti újrakapcsolódáson nem garantált, és nem az alaphelyzetbe állítási parancsra adott válasz. A csatornát le kell állítania, törölni kell, és létre kell hoznia egy újat.
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* Minden alkalommal, amikor újrakonfigurálja az élő kódolót, hívja meg az **alaphelyzetbe állítási** módszert a csatornán. A csatorna alaphelyzetbe állítása előtt le kell állítania a programot. A csatorna alaphelyzetbe állítása után indítsa újra a programot.
* Egy csatornát csak akkor lehet leállítani, ha fut állapotban van, és a csatornán lévő összes program le lett állítva.
* Alapértelmezés szerint csak 5 csatornát adhat hozzá a Media Services-fiókjához. Ez egy enyhe kvóta az összes új fiókra vonatkozóan. További információ: [kvóták és korlátozások](media-services-quotas-and-limitations.md).
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* Csak akkor számítunk fel díjat, ha a csatorna **fut** állapotban van. További információkért tekintse meg [ezt](media-services-manage-live-encoder-enabled-channels.md#states) a szakaszt.
* Jelenleg az élő stream maximális javasolt időtartama 8 óra. 
* Győződjön meg arról, hogy az adatfolyam-végpont, amelyről a tartalmat a **futó** állapotban szeretné továbbítani.
* A kódolási beállításkészlet a "maximális Képkockasebesség" fogalmát használja 30 fps-ként. Tehát ha a bemenet 60fps/59.94 i, a bemeneti keretek el lesznek dobva/delapoltan 30/29.97 fps. Ha a bemenet 50fps/50I, a bemeneti képkockákat a rendszer eldobta/visszaváltotta a 25 fps-ra. Ha a bemenet 25 fps, a kimenet 25 fps marad.
* Ha elkészült, ne felejtse el leállítani a CSATORNÁKat. Ha nem, a számlázás folytatódni fog.

## <a name="known-issues"></a>Ismert problémák
* A csatorna indítási ideje egy átlagos 2 percen keresztül javult, de a megnövekedett kereslet miatt akár 20 percig is eltarthat.
* A pala lemezképeknek meg kell felelniük az [itt](media-services-manage-live-encoder-enabled-channels.md#default_slate)ismertetett korlátozásoknak. Ha egy olyan csatornát próbál létrehozni, amely a 1920 × 1080 nagyobb méretű alapértelmezett lappal rendelkezik, a kérelem végül kikerül a hibába.
* mégegyszer.... Ne felejtse el leállítani a CSATORNÁKat, amikor befejezte a folyamatos átvitelt. Ha nem, a számlázás folytatódni fog.

## <a name="need-help"></a>Segítségre van szüksége?

A támogatási jegy megnyitásához lépjen az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-step"></a>Következő lépés

Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Események élő adatfolyamainak továbbítása az Azure Media Services használatával](media-services-overview.md)

[Olyan csatornákat hozhat létre, amelyek élő kódolást végeznek egy éneklő bitráta-ből az adaptív sávszélességű streamhez a portál használatával](media-services-portal-creating-live-encoder-enabled-channel.md)

[Olyan csatornákat hozhat létre, amelyek élő kódolást végeznek egy éneklő bitrátából az adaptív sávszélességű adatfolyamba a .NET SDK-val](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Csatornák kezelése REST API](/rest/api/media/operations/channel)

[Media Services fogalmak](media-services-concepts.md)

[Azure Media Services darabolt MP4 élő betöltési specifikáció](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
