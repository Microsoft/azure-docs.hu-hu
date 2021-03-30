---
title: Médiaadatfolyam-továbbítás optimalizálása Azure CDN
description: Ismerje meg az Azure-Content Delivery Network adatfolyam-továbbítási lehetőségeit, például a részleges gyorsítótár-megosztást és a gyorsítótár kitöltésének várakozási idejét.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: c3ab722f182e32cf2f3aca6bb2f3d5a9598264af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192604"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Médiaadatfolyam-továbbítás optimalizálása Azure CDN 
 
A nagy felbontású videók használata egyre növekszik az interneten, ami nehézségeket okoz a nagyméretű fájlok hatékony kézbesítéséhez. Az ügyfelek az igény szerinti videó zökkenőmentes lejátszását, vagy a különböző hálózatokon és ügyfeleken élő videós eszközöket várnak világszerte. A folyamatos és élvezetes felhasználói élmény biztosítása érdekében elengedhetetlen, hogy a Media Streaming-fájlok gyors és hatékony kézbesítési mechanizmusa kritikus fontosságú legyen.  

Az élő adatfolyam-továbbítási média különösen nehéz az egyidejű nézők nagy mérete és száma miatt. A hosszú késések miatt a felhasználók elhagyhatják a felhasználókat. Mivel az élő streameket nem lehet időben gyorsítótárazni, és a nagy késések nem elfogadhatók a nézők számára, a videó töredékeit időben kell kiadni. 

A folyamatos átvitelre vonatkozó kérések néhány új kihívást is biztosítanak. Ha egy népszerű élő adatfolyamot vagy egy új adatsorozatot bocsátanak ki a videóhoz igény szerint, akár több millió néző is kérheti az adatfolyamot egyszerre. Ebben az esetben az intelligens kérelmek összevonása létfontosságú ahhoz, hogy ne legyenek túlterhelve a forráskiszolgáló, ha az eszközök még nincsenek gyorsítótárazva.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>A Microsoft Azure CDN Media Streaming optimalizációja

A **Microsoft-végpontok Azure CDN szabványa** közvetlenül az általános webes kézbesítés optimalizálási típusának használatával továbbítja a Streaming Media-eszközöket. 

A **Microsoft Azure CDN szabványának** multimédia-továbbítási optimalizálása olyan élő vagy igény szerinti adatfolyam-továbbítási adathordozóra érvényes, amely egyedi adathordozó-töredékeket használ a kézbesítéshez. Ez a folyamat különbözik a progresszív letöltéssel vagy a byte-Range kérelmek használatával továbbított egyetlen nagyméretű objektumtól. További információ a médiatartalom-továbbítási stílusról: [nagy fájlok letöltésének optimalizálása Azure CDNsal](cdn-large-file-optimization.md).

Az általános médiatartalom-továbbítási vagy igény szerinti adathordozó-továbbítási optimalizálási típusok az Azure Content Delivery Network (CDN) használatával, a háttér-optimalizálásokkal gyorsabban biztosíthatják a média-eszközöket. Emellett a média-eszközök konfigurációit is használják az ajánlott eljárások alapján.

### <a name="partial-cache-sharing"></a>Részleges gyorsítótár megosztása
A részleges gyorsítótár-megosztás lehetővé teszi, hogy a CDN részlegesen gyorsítótárazott tartalmakat kézbesítse az új kéréseknek. Ha például a CDN-re irányuló első kérés a gyorsítótárban marad, a kérést a rendszer elküldi a forrásnak. Bár ez a hiányos tartalom be van töltve a CDN-gyorsítótárba, a CDN-re irányuló egyéb kérések megkezdhetik ezeket az adatokat. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Médiaadatfolyam-továbbítási optimalizálás Azure CDN a Verizontól

A Verizon-végpontokból **Azure CDN standard a Verizon** és **a prémium szintű Azure CDN a Verizon** -végpontok által biztosított adatátviteli eszközöket közvetlenül az általános webes kézbesítés optimalizálási típusával továbbítják. A CDN néhány funkciója közvetlenül segíti a média-eszközök alapértelmezett módon történő kézbesítését.

### <a name="partial-cache-sharing"></a>Részleges gyorsítótár megosztása

A részleges gyorsítótár-megosztás lehetővé teszi, hogy a CDN részlegesen gyorsítótárazott tartalmakat kézbesítse az új kéréseknek. Ha például a CDN-re irányuló első kérés a gyorsítótárban marad, a kérést a rendszer elküldi a forrásnak. Bár ez a hiányos tartalom be van töltve a CDN-gyorsítótárba, a CDN-re irányuló egyéb kérések megkezdhetik ezeket az adatokat. 

### <a name="cache-fill-wait-time"></a>Gyorsítótár kitöltésének várakozási ideje

 A gyorsítótár kitöltési várakozási ideje funkció arra kényszeríti a peremhálózati kiszolgálót, hogy minden további kérelmet ugyanarra az erőforrásra várjon, amíg a HTTP-válasz fejlécei nem érkeznek meg a forráskiszolgálón. Ha a forrástól érkező HTTP-válaszüzenetek lejárnak az időzítő lejárata előtt, akkor az összes tartásra kerülő kérelem kikerül a növekvő gyorsítótárból. Ugyanakkor a gyorsítótárat a rendszer a forrástól származó adatokkal tölti ki. Alapértelmezés szerint a gyorsítótár kitöltésének várakozási ideje 3 000 ezredmásodpercre van állítva. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Médiaadatfolyam-továbbítási optimalizálások Azure CDN Akamai
 
**Azure CDN a standard from Akamai** olyan funkciót kínál, amely hatékonyan továbbítja a folyamatos átviteli eszközöket a világ különböző helyei számára a nagy léptékű felhasználók számára. A szolgáltatás csökkenti a késést, mert csökkenti a forrás-kiszolgálók terhelését. Ez a szolgáltatás a standard Akamai díjszabási szinttel érhető el. 

A **Akamai-hez Azure CDN standard szintű** Media Streaming Optimization olyan élő vagy igény szerinti adatfolyam-továbbítási adathordozóra érvényes, amely egyéni adathordozó-töredékeket használ a kézbesítéshez. Ez a folyamat különbözik a progresszív letöltéssel vagy a byte-Range kérelmek használatával továbbított egyetlen nagyméretű objektumtól. További információ a médiatartalom-továbbítási stílusról: [nagyméretű fájlok optimalizálása](cdn-large-file-optimization.md).

Az általános adathordozó-kézbesítés vagy a videó igény szerinti átvitelének optimalizálási típusai egy CDN-t használnak a háttér-optimalizálással, hogy gyorsabban továbbítsák a média-eszközöket. Emellett a média-eszközök konfigurációit is használják az ajánlott eljárások alapján.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Akamai CDN-végpont konfigurálása a médiaadatfolyam-továbbítás optimalizálásához
 
A Content Delivery Network (CDN) végpontját beállíthatja úgy, hogy az a Azure Portal használatával optimalizálja a nagyméretű fájlok kézbesítését. Ehhez használhatja a REST API-kat vagy bármelyik ügyféloldali SDK-t is. A következő lépések azt mutatják be, hogyan jelennek meg a folyamat egy **Azure CDN standard Akamai-** profilból való Azure Portal használatával:

1. Új végpont hozzáadásához egy Akamai **CDN-profil** oldalon válassza a **végpont** lehetőséget.
  
    ![Új végpont](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. A legördülő listában válassza a **video on demand Media Streaming** **lehetőséget az igény** szerinti video-eszközökhöz. Ha élő és igény szerinti videós közvetítés kombinációját hajtja végre, válassza az **általános médiaadatfolyam-továbbítás** lehetőséget.

    ![Kijelölt adatfolyam](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
A végpont létrehozása után alkalmazza az optimalizálást az összes olyan fájlra, amely megfelel bizonyos feltételeknek. A következő szakasz ezt a folyamatot ismerteti. 

### <a name="caching"></a>Gyorsítótárazás

Ha **Azure CDN standard from Akamai** azt észleli, hogy az eszköz egy folyamatos átviteli jegyzékfájl vagy töredék, az általános webes kézbesítéstől eltérő gyorsítótárazási lejárati időt használ. (Lásd a következő táblázat teljes listáját.) Mint mindig, a gyorsítótár-vezérlés vagy a lejárati fejlécek a forrásból való elküldése után teljesülnek. Ha az eszköz nem adathordozó-eszköz, az az általános webes kézbesítés lejárati idejét használva gyorsítótárazza.

A rövid negatív gyorsítótárazási idő hasznos a forrás kiszervezésében, ha sok felhasználó olyan töredéket kér, amely még nem létezik. Ilyen például egy élő stream, ahol a csomagok nem érhetők el a második forrásból. A hosszú gyorsítótárazási időköz segíti a kérelmek kiszervezését is a forrástól, mert a videó tartalma általában nem módosul.

| Gyorsítótárazás  | Általános webes kézbesítés | Általános médiaadatfolyam-továbbítás | Igény szerinti video-adatfolyam  
|--- | --- | --- | ---
| Gyorsítótárazás: pozitív <br> HTTP 200, 203, 300, <br> 301, 302 és 410 | 7 nap |365 nap | 365 nap   
| Gyorsítótárazás: negatív <br> HTTP 204, 305, 404, <br> és 405 | Nincsenek | 1 másodperc | 1 másodperc
 
### <a name="deal-with-origin-failure"></a>A származási hiba kezelése  

Az általános adathordozó-kézbesítés és a videó igény szerinti továbbítása is rendelkezik a forrás időkorláttal és az újrapróbálkozási naplóval a tipikus kérelmekre vonatkozó ajánlott eljárások alapján. Például, mivel az általános adathordozó-kézbesítés az élő és a videó igény szerinti továbbítására szolgál, az élő közvetítés időérzékeny természete miatt rövidebb kapcsolati időtúllépést használ.

Ha a kapcsolat időtúllépés miatt megszakad, a CDN többször próbálkozik, mielőtt az "504-Gateway időtúllépés" hibaüzenetet küld az ügyfélnek. 

Ha egy fájl megegyezik a fájltípus és a méret feltételei listával, a CDN a Media Streaming viselkedését használja. Ellenkező esetben általános webes kézbesítést használ.
   
### <a name="conditions-for-media-streaming-optimization"></a>A médiaadatfolyam-továbbítás optimalizálásának feltételei 

A következő táblázat felsorolja a médiaadatfolyam-továbbítás optimalizálásához teljesítendő feltételeket: 
 
Támogatott folyamatos átviteli típusok | Fájlkiterjesztések  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, Key, TS, AAC
Adobe HDS | f4m, f4x, drmmeta, bootstrap, F4F,<br>Seg-Frag URL-struktúra <br> (megfelelő regex: ^ (/. *) SEQ (\d +) – Frag (\d +)
DASH | mpd, Dash, DivX, ismv, M4s, M4V, MP4, mp4v, <br> sidx, WebM, mp4a, M4A, ISMA
Smooth streaming | /manifest/, /QualityLevels/Fragments/
  
 
