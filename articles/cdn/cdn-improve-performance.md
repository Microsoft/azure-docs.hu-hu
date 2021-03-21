---
title: A teljesítmény javítása a fájlok tömörítésével a Azure CDNban | Microsoft Docs
description: Megtudhatja, hogyan javíthatja a fájlátvitel sebességét, és hogyan növelheti az oldal-betöltési teljesítményt a fájlok Azure CDNban való tömörítésével.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 11a2dbfc9c6da60e4dd96f65d2a20165a3663e8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601542"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Teljesítmény javítása fájltömörítéssel az Azure CDN-ben
A fájltömörítés egy egyszerű és hatékony módszer a fájlátvitel sebességének javítására, valamint a lapozófájl teljesítményének növelésére azáltal, hogy a fájl méretét a kiszolgálóról való elküldésük előtt csökkenti. A fájltömörítés csökkentheti a sávszélesség-költségeket, és rugalmasabb felhasználói élményt nyújt a felhasználóknak.

A fájltömörítés kétféleképpen engedélyezhető:

- Engedélyezze a tömörítést a forráskiszolgáló számára. Ebben az esetben Azure CDN továbbítja a tömörített fájlokat, és továbbítja azokat az ügyfelek számára, akik ezt kérik.
- Engedélyezze a tömörítést közvetlenül a CDN POP-kiszolgálókon (*menet közbeni tömörítés*). Ebben az esetben a CDN tömöríti a fájlokat, és azokat a végfelhasználók számára is kiszolgálja, még akkor is, ha azokat nem a forráskiszolgáló tömöríti.

> [!IMPORTANT]
> Azure CDN konfiguráció módosítása hosszabb időt is igénybe vehet a hálózaton keresztül történő propagáláshoz: 
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> 
> Ha első alkalommal állítja be a tömörítést a CDN-végponthoz, akkor a hibakeresés előtt 1-2 órát várnia kell, hogy a tömörítési beállítások propagálva legyenek a pop-ra.

## <a name="enabling-compression"></a>Tömörítés engedélyezése
A standard és a prémium szintű CDN-csomagok ugyanazt a tömörítési funkciót biztosítják, de a felhasználói felület különbözik. A standard és a prémium szintű CDN-csomagok közötti különbségekről további információt a [Azure CDN áttekintése](cdn-overview.md)című témakörben talál.

### <a name="standard-cdn-profiles"></a>Szabványos CDN-profilok 
> [!NOTE]
> Ez a szakasz a **Microsoft Azure CDN szabványára** vonatkozik, **Azure CDN a standard a verizontól**, és **Azure CDN standard a Akamai-** profilokból.
> 
> 

1. A CDN-profil lapon válassza ki a felügyelni kívánt CDN-végpontot.

    ![CDN-profil végpontjai](./media/cdn-file-compression/cdn-endpoints.png)

    Megnyílik a CDN-végpont lap.
2. Válassza a **tömörítés** lehetőséget.

    ![A képernyőképen egy, a portál menüjében kiválasztott tömörítéssel rendelkező végpont látható.](./media/cdn-file-compression/cdn-compress-select-std.png)

    Megnyílik a tömörítés oldal.
3. A tömörítés bekapcsolásához válassza **a** be lehetőséget.

    ![Képernyőfelvétel: a tömörítés bekapcsolásának megjelenítése.](./media/cdn-file-compression/cdn-compress-standard.png)
4. Használja az alapértelmezett MIME-típusokat, vagy módosítsa a listát a MIME-típusok hozzáadásával vagy eltávolításával.

   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítést alkalmazni tömörített formátumokra. Például: ZIP, MP3, MP4 vagy JPG.
   > 

5. A módosítások végrehajtása után válassza a **Mentés** lehetőséget.

### <a name="premium-cdn-profiles"></a>Prémium CDN-profilok
> [!NOTE]
> Ez a szakasz csak a Verizon-profilokból **Azure CDN Premiumra** vonatkozik.
> 

1. A CDN-profil lapon válassza a **kezelés** lehetőséget.

    ![CDN-kezelés kiválasztása](./media/cdn-file-compression/cdn-manage-btn.png)

    Megnyílik a CDN felügyeleti portál.
2. Vigye a kurzort a **nagyméretű http** -lapra, majd vigye a kurzort a **gyorsítótár beállításai** menü fölé. Válassza a **tömörítés** lehetőséget.

    ![CDN-tömörítés kiválasztása](./media/cdn-file-compression/cdn-compress-select.png)

    A tömörítési beállítások megjelennek.

    ![CDN-fájlok tömörítési beállításai](./media/cdn-file-compression/cdn-compress-files.png)
3. Engedélyezze a tömörítést a **tömörítés** engedélyezése lehetőség kiválasztásával. Adja meg a tömöríteni kívánt MIME-típusokat vesszővel tagolt listaként (szóközök nélkül) a **fájltípusok** mezőben.

   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítést alkalmazni tömörített formátumokra. Például: ZIP, MP3, MP4 vagy JPG.
   > 

4. A módosítások végrehajtása után válassza a **frissítés** lehetőséget.

## <a name="compression-rules"></a>Tömörítési szabályok

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN standard a Microsoft profiljaiból

A Microsoft-profilokból **származó Azure CDN** esetén a rendszer csak a jogosult fájlokat tömöríti. A tömörítéshez a fájlnak a következő feltételeknek kell megfelelnie:
- A [tömörítéshez KONFIGURÁLT](#enabling-compression)MIME-típusnak kell lennie.
- Nagyobbnak kell lennie 1 KB-nál
- Kisebbnek kell lennie, mint 8 MB

Ezek a profilok a következő tömörítési kódolásokat támogatják:
- gzip (GNU zip)
- brotli 

Ha a kérelem több tömörítési típust is támogat, a brotli tömörítés elsőbbséget élvez.

Ha egy adott eszközre vonatkozó kérelem a gzip-tömörítést adja meg, és a kérés gyorsítótár-hiányt eredményez, a Azure CDN az eszköz gzip-tömörítését közvetlenül a POP-kiszolgálón hajtja végre. Ezt követően a tömörített fájlt a rendszer a gyorsítótárból kézbesíti.

Ha a forrás darabolásos átvitelt (TÁBLAKIFEJEZÉSEK) használ a tömörített adatoknak a CDN-POP-ba való küldéséhez, akkor a 8 MB-nál nagyobb válaszok nem támogatottak. 

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN Verizon-profilokból

A Verizon és a prémium szintű Verizon **Azure CDN** -profilokból származó **Azure CDN standard** esetén a rendszer csak a jogosult fájlokat tömöríti. A tömörítéshez a fájlnak a következő feltételeknek kell megfelelnie:
- 128 bájtnál nagyobbnak kell lennie
- 3 MB-nál kisebbnek kell lennie

Ezek a profilok a következő tömörítési kódolásokat támogatják:
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 

Ha a kérelem egynél több tömörítési típust támogat, akkor ezek a tömörítési típusok elsőbbséget élveznek a brotli tömörítéssel szemben.

Ha egy adott eszközre vonatkozó kérelem meghatározza a brotli tömörítését (HTTP `Accept-Encoding: br` -fejléc:), és a kérés gyorsítótár-hiányt eredményez, a Azure CDN az eszköz brotli-tömörítését közvetlenül a pop-kiszolgálón hajtja végre. Ezt követően a tömörített fájlt a rendszer a gyorsítótárból kézbesíti.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Standard Azure CDN Akamai-profilokból

A Akamai-profilokból való **Azure CDN standard szintű** összes fájl tömörítésre alkalmas. A fájlnak azonban olyan MIME-típusnak kell lennie, amely [tömörítésre van konfigurálva](#enabling-compression).

Ezek a profilok csak a gzip tömörítési kódolást támogatják. Ha egy profil végpontja egy gzip-kódolású fájlt kér, a rendszer mindig a forrástól kér, az ügyféltől érkező kéréstől függetlenül. 

## <a name="compression-behavior-tables"></a>Tömörítési viselkedési táblák
Az alábbi táblázatok az egyes forgatókönyvek Azure CDN tömörítési viselkedését írják le:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>A tömörítés le van tiltva, vagy a fájl nem alkalmas a tömörítésre
| Ügyfél által kért formátum (Accept-Encoding fejlécen keresztül) | Gyorsítótárazott fájlformátum | A CDN-válasz az ügyfélre | &nbsp; &nbsp; &nbsp; &nbsp; Megjegyzések &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített | |
| Tömörített |Tömörítetlen |Tömörítetlen | |
| Tömörített |Nincs gyorsítótárazva |Tömörítve vagy tömörítetlen |A forrás válasza határozza meg, hogy a CDN végez-e tömörítést. |
| Tömörítetlen |Tömörített |Tömörítetlen | |
| Tömörítetlen |Tömörítetlen |Tömörítetlen | |
| Tömörítetlen |Nincs gyorsítótárazva |Tömörítetlen | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>A tömörítés engedélyezve van, és a fájl tömörítésre alkalmas
| Ügyfél által kért formátum (Accept-Encoding fejlécen keresztül) | Gyorsítótárazott fájlformátum | CDN-válasz az ügyfélre | Jegyzetek |
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített |A CDN átkódolja a támogatott formátumok között. <br/>A **Microsoft Azure CDN** nem támogatja a formátumok közötti átkódolást, hanem a forrástól származó adatok beolvasását, és a formátumhoz külön tömöríti és gyorsítótárazza azokat. |
| Tömörített |Tömörítetlen |Tömörített |A CDN tömörítést végez. |
| Tömörített |Nincs gyorsítótárazva |Tömörített |A CDN tömörítést végez, ha a forrás tömörítetlen fájlt ad vissza. <br/>A **Verizon Azure CDN** a kibontott fájlt továbbítja az első kérelemre, majd a további kérésekhez tömöríti és gyorsítótárazza a fájlt. <br/>A `Cache-Control: no-cache` fejlécet tartalmazó fájlok tömörítése nem történik meg. |
| Tömörítetlen |Tömörített |Tömörítetlen |A CDN kibontást végez. <br/>**A Microsoft Azure CDN** nem támogatja a kibontást, hanem a forrás-és a gyorsítótár-adatoknak a tömörítetlen ügyfelek számára külön történő lekérdezését. |
| Tömörítetlen |Tömörítetlen |Tömörítetlen | |
| Tömörítetlen |Nincs gyorsítótárazva |Tömörítetlen | |

## <a name="media-services-cdn-compression"></a>CDN-tömörítés Media Services
Media Services CDN-adatfolyamhoz engedélyezett végpontok esetében a tömörítés alapértelmezés szerint engedélyezve van a következő MIME-típusokhoz: 
- Application/vnd. MS-sstr + XML 
- alkalmazás/kötőjel + XML
- alkalmazás/vnd.apple.mpegURL-cím
- Application/f4m + XML 

## <a name="see-also"></a>Lásd még
* [A CDN-fájlok tömörítési hibáinak elhárítása](cdn-troubleshoot-compression.md)    

