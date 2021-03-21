---
title: Nagyméretű fájlok letöltésének optimalizálása Azure CDN
description: Ismerje meg, hogyan optimalizálható a nagyméretű fájlok letöltése az Azure Content Delivery Networkban. Ez a cikk számos forgatókönyvet tartalmaz.
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
ms.openlocfilehash: ed5768e89482d32bb140e9ba7064de2d20809892
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020721"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Nagyméretű fájlok letöltésének optimalizálása Azure CDN

Az interneten keresztül továbbított tartalmak fájlmérete továbbra is nő a továbbfejlesztett funkciók, a továbbfejlesztett grafikák és a multimédiás tartalmak miatt. Ezt a növekedést számos tényező vezérli: a szélessáv elterjedtsége, a nagyobb, olcsó tárolóeszközök, a nagy felbontású videók és az internethez kapcsolódó eszközök (IoT) széles körű növekedése. A nagyméretű fájlok gyors és hatékony kézbesítési mechanizmusa kritikus fontosságú a zökkenőmentes és élvezetes felhasználói élmény biztosítása érdekében.

A nagyméretű fájlok kézbesítése több kihívást is jelent. Első lépésként a nagyméretű fájlok letöltésének átlagos ideje jelentős lehet, mivel előfordulhat, hogy az alkalmazások egymás után nem töltik le az összes adatmennyiséget. Bizonyos esetekben előfordulhat, hogy az alkalmazások az első rész előtt letöltik a fájl utolsó részét. Ha a rendszer csak kis mennyiségű fájlt kér le, vagy egy felhasználó szünetelteti a letöltést, akkor a letöltés sikertelen lesz. A letöltést is késleltetheti, amíg a Content Delivery Network (CDN) nem kérdezi le a teljes fájlt a forrás-kiszolgálóról. 

Másodszor, a felhasználó számítógépe és a fájl közötti késés határozza meg, hogy milyen sebességgel lehet megtekinteni a tartalmat. Emellett a hálózati torlódás és a kapacitással kapcsolatos problémák is hatással vannak az átviteli sebességre. A kiszolgálók és a felhasználók közötti nagyobb távolságok további lehetőségeket hoznak létre a csomagok elvesztése miatt, ami csökkenti a minőséget. A korlátozott átviteli sebesség által okozott minőség csökkenése és a csomagok elvesztése miatt előfordulhat, hogy a fájlok letöltésének várakozási ideje a befejezésig megnövelhető. 

Harmadszor, sok nagyméretű fájl nem kerül teljes egészében. Előfordulhat, hogy a felhasználók lemondják a letöltést félúton, vagy csak az első pár percet a hosszú MP4-videó megtekintésére. Ezért a szoftveres és a média-kézbesítési vállalatok csak a kért fájl részét szeretnék kézbesíteni. A kért részek hatékony eloszlása csökkenti a kimenő forgalmat a forrás-kiszolgálóról. A hatékony terjesztés a forrás-kiszolgálón is csökkenti a memóriát és az I/O-nyomást. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Nagy méretű fájlok kézbesítésének optimalizálása a Microsoft Azure CDNával

A **Microsoft-végpontok Azure CDN szabványa** nagy méretű fájlokat szabadít fel a fájlméret hiányában. A további funkciók alapértelmezés szerint be vannak kapcsolva a nagyméretű fájlok gyorsabb elvégzéséhez.

### <a name="object-chunking"></a>Objektumok adatdarabolása 

A **Microsoft Azure CDN standard az objektum-** kidarabolás nevű technikát használja. Nagyméretű fájl kérése esetén a CDN a fájlból lekéri a fájl kisebb részeit a forrásból. Miután a CDN POP-kiszolgáló megkapja a teljes vagy a bájtos fájlra vonatkozó kérelmet, a CDN peremhálózati kiszolgálója 8 MB-os adattömbökben kéri le a fájlt a forrásból. 

Miután az adathalmaz megérkezik a CDN-peremre, a rendszer gyorsítótárazza és azonnal kiszolgálja a felhasználót. A CDN ezt követően párhuzamosan beolvassa a következő adatrészletet. Ez a prefektus biztosítja, hogy a tartalom a felhasználó előtt egy darabban maradjon, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájl le nem töltődik (ha szükséges), az összes bájtos tartomány elérhető (ha szükséges), vagy az ügyfél leállítja a csatlakozást. 

A byte-Range kérelemmel kapcsolatos további információkért lásd: [RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN gyorsítótárazza a kapott adattömböket. A teljes fájlt nem kell gyorsítótárazni a CDN-gyorsítótárban. A fájl-vagy byte-tartományokra vonatkozó további kérelmek a CDN-gyorsítótárból lesznek kézbesítve. Ha nem az összes adathalmaz gyorsítótárazva van a CDN-ben, a rendszer a visszahívásokat használja a forrásokból származó adattömbök igénylésére. Ez az optimalizálás arra támaszkodik, hogy a forráskiszolgáló képes támogatni a bájtos tartományokra vonatkozó kérelmeket; Ha a forráskiszolgáló nem támogatja a bájtok közötti tartományra vonatkozó kérelmeket, a 8 MB-nál nagyobb adatletöltési kérelmek sikertelenek lesznek. 

### <a name="conditions-for-large-file-optimization"></a>A nagyméretű fájlok optimalizálásának feltételei
A fájlok maximális mérete nem korlátozható.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Nagy méretű fájlok kézbesítésének optimalizálása a Verizon Azure CDN

**Azure CDN** a Verizon és a **prémium szintű Azure CDN a Verizon** -végpontokról érkező, nagy méretű fájlokat a fájl mérete nélkül. A további funkciók alapértelmezés szerint be vannak kapcsolva a nagyméretű fájlok gyorsabb elvégzéséhez.

### <a name="complete-cache-fill"></a>Gyorsítótár kitöltésének befejezése

Az alapértelmezett gyorsítótár-kitöltési funkció lehetővé teszi, hogy a CDN lekérjen egy fájlt a gyorsítótárba egy kezdeti kérelem elhagyása vagy elvesztése esetén. 

A teljes gyorsítótár kitöltése nagy mennyiségű eszköz esetén hasznos. A felhasználók általában nem töltik le őket az elejétől a végéig. A progresszív letöltést használják. Az alapértelmezett viselkedés hatására a peremhálózati kiszolgáló kezdeményezi az eszköz háttérbeli beolvasását a forrás-kiszolgálóról. Ezt követően az eszköz a peremhálózati kiszolgáló helyi gyorsítótárában található. Miután a teljes objektum a gyorsítótárban van, a peremhálózati kiszolgáló a gyorsítótárazott objektumhoz tartozó, a CDN-re vonatkozó kérelmeket teljesít.

Az alapértelmezett viselkedés a **Verizon Azure CDN Premium**-ban található szabályok motorjának használatával tiltható le.

### <a name="peer-cache-fill-hot-filing"></a>Társ-gyorsítótárazási kitöltés – gyors bejelentések

A társ-gyorsítótárazás alapértelmezett kitöltési funkciója kifinomult, szabadalmaztatott algoritmust használ. A sávszélesség és az összesített kérelmek mérőszámai alapján további peremhálózati gyorsítótárazási kiszolgálókat használ a nagyméretű, rendkívül népszerű objektumok iránti kérelmek teljesítéséhez. Ez a szolgáltatás megakadályozza, hogy a rendszer nagy számú extra kérést küldjön a felhasználó forrás-kiszolgálójára. 

### <a name="conditions-for-large-file-optimization"></a>A nagyméretű fájlok optimalizálásának feltételei

Az általános webes kézbesítés optimalizálási típusának használatakor alapértelmezés szerint a Verizon és a **verizon Azure CDN Premium** **Azure CDN standard** csomag nagyméretű fájl-optimalizálási funkciói be vannak kapcsolva. A fájlok maximális mérete nem korlátozható. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Azure CDN standard szintű nagyméretű fájlok kézbesítésének optimalizálása Akamai

**Azure CDN standard from Akamai** Profile-végpontok olyan funkciót kínálnak, amely hatékonyan nyújt nagy méretű fájlokat a világ különböző helyein. A szolgáltatás csökkenti a késést, mert csökkenti a forrás-kiszolgálók terhelését.

A nagyméretű file Optimization Type funkció bekapcsolja a hálózati optimalizálásokat és konfigurációkat, hogy a nagyméretű fájlokat gyorsabban és rugalmasabban kézbesítse. A **Akamai-végpontok Azure CDN standard szintű** általános webes kézbesítése csak 1,8 GB alatti gyorsítótárba helyezi a fájlokat, és legfeljebb 150 GB-ig képes a fájlok bújtatására (gyorsítótárba helyezésére). A nagyméretű fájlok optimalizálása akár 150 GB-ig gyorsítótárazza a fájlokat.

A nagyméretű fájlok optimalizálása bizonyos feltételek teljesülése esetén érvényes. A feltételek közé tartozik a forráskiszolgáló működésének módja, valamint a kért fájlok mérete és típusa. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Akamai CDN-végpont konfigurálása nagyméretű fájlok kézbesítésének optimalizálásához

A **Azure CDN standardot a Akamai-** végponton konfigurálhatja a nagyméretű fájlok kézbesítésének a Azure Portal keresztüli optimalizálásához. Ehhez használhatja a REST API-kat vagy bármelyik ügyféloldali SDK-t is. A következő lépések azt mutatják be, hogyan jelennek meg a folyamat egy **Azure CDN standard Akamai-** profilból való Azure Portal használatával:

1. Új végpont hozzáadásához egy Akamai **CDN-profil** oldalon válassza a **végpont** lehetőséget.

    ![Új végpont](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. A **legördülő listában** válassza a **nagyméretű fájlok letöltése** lehetőséget.

    ![Nagyméretű fájlok optimalizálása kiválasztva](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Miután létrehozta a CDN-végpontot, az a nagyméretű fájl-optimalizálást alkalmazza az összes olyan fájlra, amely megfelel bizonyos feltételeknek. A következő szakasz ezt a folyamatot ismerteti.

### <a name="object-chunking"></a>Objektumok adatdarabolása 

A nagy méretű fájlok optimalizálása a **Akamai Azure CDN szabványával** az objektumok darabolásának nevezett technikát használja. Nagyméretű fájl kérése esetén a CDN a fájlból lekéri a fájl kisebb részeit a forrásból. Miután a CDN POP-kiszolgáló megkapja a teljes vagy a bájtos fájlra vonatkozó kérelmet, ellenőrzi, hogy a fájl típusa támogatott-e ehhez az optimalizáláshoz. Azt is ellenőrzi, hogy a fájl típusa megfelel-e a fájlméretre vonatkozó követelményeknek. Ha a fájlméret meghaladja a 10 MB-ot, a CDN Edge-kiszolgáló a forrástól 2 MB méretű adattömbökben kéri le a fájlt. 

Miután az adathalmaz megérkezik a CDN-peremre, a rendszer gyorsítótárazza és azonnal kiszolgálja a felhasználót. A CDN ezt követően párhuzamosan beolvassa a következő adatrészletet. Ez a prefektus biztosítja, hogy a tartalom a felhasználó előtt egy darabban maradjon, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájl le nem töltődik (ha szükséges), az összes bájtos tartomány elérhető (ha szükséges), vagy az ügyfél leállítja a csatlakozást. 

A byte-Range kérelemmel kapcsolatos további információkért lásd: [RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN gyorsítótárazza a kapott adattömböket. A teljes fájlt nem kell gyorsítótárazni a CDN-gyorsítótárban. A fájl-vagy byte-tartományokra vonatkozó további kérelmek a CDN-gyorsítótárból lesznek kézbesítve. Ha nem az összes adathalmaz gyorsítótárazva van a CDN-ben, a rendszer a visszahívásokat használja a forrásokból származó adattömbök igénylésére. Ez az optimalizálás arra támaszkodik, hogy a forráskiszolgáló képes támogatni a bájtos tartományokra vonatkozó kérelmeket; Ha a forráskiszolgáló nem támogatja a bájtok közötti kérelmeket, ez az optimalizálás nem érvényes.

### <a name="caching"></a>Gyorsítótárazás
A nagyméretű fájlok optimalizálása az általános webes kézbesítéstől eltérő alapértelmezett gyorsítótárazási lejárati időt használ. Különbséget tesz a pozitív gyorsítótárazás és a negatív gyorsítótárazás között a HTTP-válaszok kódjai alapján. Ha a forráskiszolgáló lejárati időt határoz meg egy gyorsítótár-vezérlőelem vagy a lejárati fejléc között a válaszban, a CDN kiértékeli ezt az értéket. Ha nem adja meg a forrást, és a fájl megfelel az optimalizálási típus típus-és méretének, a CDN a nagyméretű fájlok optimalizálásának alapértelmezett értékeit használja. Ellenkező esetben a CDN az általános webes kézbesítés alapértelmezett értékeit használja.

| Gyorsítótárazás  | Általános web | Nagyméretű fájlok optimalizálása 
--- | --- | --- 
Gyorsítótárazás: pozitív <br> HTTP 200, 203, 300, <br> 301, 302 és 410 | 7 nap |1 nap  
Gyorsítótárazás: negatív <br> HTTP 204, 305, 404, <br> és 405 | Nincsenek | 1 másodperc 

### <a name="deal-with-origin-failure"></a>A származási hiba kezelése

A forrás olvasási időkorlát hossza két másodpercen belül növekszik az általános webes kézbesítés két percre a nagyméretű fájl optimalizációs típusnál. Ez növelheti a nagyobb fájlméretek fiókját, hogy elkerülje a korai időtúllépési kapcsolat elkerülését.

Ha a kapcsolat időtúllépés miatt megszakad, a CDN többször próbálkozik, mielőtt az "504-Gateway időtúllépés" hibaüzenetet küld az ügyfélnek. 

### <a name="conditions-for-large-file-optimization"></a>A nagyméretű fájlok optimalizálásának feltételei

A következő táblázat felsorolja a nagyméretű fájl-optimalizáláshoz teljesítendő feltételek készletét:

Feltétel | Értékek 
--- | --- 
Támogatott fájltípusok | 3g2, 3GP, ASF, AVI, BZ2, DMG, exe, F4V, FLV, <br> GZ, HDP, ISO, jxr, M4V, MKV, MOV, MP4, <br> MPEG, MPG, MTS, pkg, QT, RM, SWF, Tar, <br> tgz, WDP, WebM, WebP, WMA, WMV, zip  
Minimális fájlméret | 10 MB 
Maximális fájlméret | 150 GB 
A forráskiszolgáló jellemzői | Támogatnia kell a byte-Range kérelmeket 

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Vegye figyelembe a következő további szempontokat az optimalizálási típushoz:

- A darabolási folyamat további kérelmeket hoz létre a forráskiszolgálón. Azonban a forrásból szállított adatok összesített mennyisége sokkal kisebb. A darabolás a CDN jobb gyorsítótárazási jellemzőit eredményezi.

- A memória és az I/O-nyomás alacsonyabb a forrásnál, mert a fájl kisebb darabokra van leszállítva.

- A CDN-ben gyorsítótárazott adattömbök esetében a forrásnak nincs további kérése, amíg a tartalom lejár, vagy a gyorsítótárból ki nem zárja őket.

- A felhasználók elvégezhetnek tartományon alapuló kéréseket a CDN-be, amelyek ugyanúgy kezelhetők, mint bármely normál fájl. Az optimalizálás csak akkor érvényes, ha a fájl típusa érvényes, és a bájt tartománya 10 MB és 150 GB között van. Ha a kért átlagos fájlméret 10 MB-nál kisebb, használja inkább az általános webes kézbesítést.

