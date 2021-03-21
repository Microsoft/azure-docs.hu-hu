---
title: Azure CDN optimalizálása a tartalom kézbesítésének típusára
description: Ismerje meg, hogy az Azure Content Delivery Network hogyan optimalizálhatja a kézbesítést a tartalom típusa alapján. Az optimalizálással kapcsolatos ajánlott eljárások javítják a teljesítményt és a forrás kiszervezését.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 3d207ee09a76509a65a2645515b182f8d92753b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387920"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Azure CDN optimalizálása a tartalom kézbesítésének típusára

Amikor tartalmat továbbít egy nagy globális közönség számára, elengedhetetlen a tartalom optimalizált továbbításának biztosítása. Az [Azure Content Delivery Network (CDN)](cdn-overview.md) a tartalom típusától függően optimalizálhatja a kézbesítési élményt. A tartalom lehet egy webhely, egy élő stream, egy videó vagy egy nagyméretű fájl letöltésre. CDN-végpont létrehozásakor meg kell adnia egy forgatókönyvet az **optimalizált** beállításnál. Ön dönti el, hogy melyik optimalizálást alkalmazza a rendszer a CDN-végpontról továbbított tartalomra.

Az optimalizálási lehetőségek úgy lettek kialakítva, hogy a lehető legjobb gyakorlatok használatával javítsák a tartalmak kézbesítési teljesítményét és a jobb kiszervezést. A forgatókönyvek beállításai hatással vannak a teljesítményre a részleges gyorsítótárazás konfigurációjának módosításával, az objektumok darabolásával, valamint a hiba miatti újrapróbálkozási szabályzattal. 

Ez a cikk áttekintést nyújt a különböző optimalizálási funkciókról és azok használatáról. A szolgáltatásokkal és korlátozásokkal kapcsolatos további információkért tekintse meg az egyes optimalizálási típusok megfelelő cikkeit.

> [!NOTE]
> CDN-végpont létrehozásakor a lehetőségekre **optimalizált** beállítások a végpont által létrehozott profil típusától függően változhatnak. Azure CDN szolgáltatók különböző módokon alkalmazhatják a fejlesztést, a forgatókönyvtől függően. 

## <a name="provider-options"></a>Szolgáltatói beállítások

A **Microsoft-profilokkal Azure CDN standard** a következő optimalizálásokat támogatja:

* [Általános webes kézbesítés](#general-web-delivery). Ez az optimalizálás a médiaadatfolyam-továbbításhoz és a nagyméretű fájlok letöltéséhez is használatos.

> [!NOTE]
> A Microsoft által kínált dinamikus webhely-gyorsítás az Azure-beli [bejárati ajtó szolgáltatáson](../frontdoor/front-door-overview.md)keresztül érhető el.

**Azure CDN** a Verizon és a **Azure CDN Premium rendszerhez készült** standard a Verizon-profilokból a következő optimalizálásokat támogatja:

* [Általános webes kézbesítés](#general-web-delivery). Ez az optimalizálás a médiaadatfolyam-továbbításhoz és a nagyméretű fájlok letöltéséhez is használatos.

* [Dinamikus helygyorsítás](#dynamic-site-acceleration) 


A **Akamai-profilokból Azure CDN standard** a következő optimalizálásokat támogatja:

* [Általános webes kézbesítés](#general-web-delivery) 

* [Általános médiaadatfolyam-továbbítás](#general-media-streaming)

* [Igény szerinti video-adatfolyam](#video-on-demand-media-streaming)

* [Nagyméretű fájlok letöltése](#large-file-download)

* [Dinamikus helygyorsítás](#dynamic-site-acceleration) 

A Microsoft azt javasolja, hogy tesztelje a különböző szolgáltatók közötti teljesítménybeli különbségeket, hogy kiválassza az optimális szolgáltatót a kézbesítéshez.

## <a name="select-and-configure-optimization-types"></a>Optimalizálási típusok kiválasztása és konfigurálása

CDN-végpont létrehozásakor válasszon olyan optimalizálási típust, amely a legjobban megfelel a végpont által kézbesíteni kívánt-forgatókönyvnek és a tartalom típusának. 

Az alapértelmezett beállítás az **általános webes kézbesítés** . A Akamai-végpontok optimalizálásával csak **Azure CDN szabványt** lehet frissíteni. 

A **microsofttól Azure CDN standard szintű** Azure CDN a verizontól és **a Azure CDN Premiumtól a Verizontól** a **standard** szintig nem.

1. A Akamai-profilból **származó Azure CDN-szabványban** válasszon ki egy végpontot.

    ![Végpont kiválasztása](./media/cdn-optimization-overview/01_Akamai.png)

2. A beállítások területen válassza az **optimalizálás** lehetőséget. Ezután válasszon egy típust az **optimalizált** legördülő listából.

    ![Optimalizálás és típus kiválasztása](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Speciális forgatókönyvek optimalizálása

A CDN-végpontot az alábbi forgatókönyvek egyikére optimalizálhatja. 

### <a name="general-web-delivery"></a>Általános webes kézbesítés

Az általános webes kézbesítés a leggyakoribb optimalizálási lehetőség. A szolgáltatás általános webtartalom-optimalizálásra, például weboldalakra és webalkalmazásokra van tervezve. Ez az optimalizálás a fájl-és videó-letöltésekhez is használható.

Egy tipikus webhely statikus és dinamikus tartalmat tartalmaz. A statikus tartalom olyan képeket, JavaScript-kódtárakat és stíluslapokat tartalmaz, amelyeket a különböző felhasználók számára lehet gyorsítótárazni és továbbítani. A dinamikus tartalom személyre szabott az egyes felhasználók számára, például a felhasználói profilokhoz igazított híreket. A dinamikus tartalom, például a bevásárlókocsi tartalma nem gyorsítótárazott, mert egyedi az egyes felhasználók számára. Az általános webes kézbesítés képes optimalizálni a teljes webhelyet. 

> [!NOTE]
> Ha a Akamai-profilból **Azure CDN standardot** használ, akkor válassza ezt az optimalizálási típust, ha az átlagos FÁJLMÉRET 10 MB-nál kisebb méretű. Ellenkező esetben, ha az átlagos fájlméret nagyobb, mint 10 MB, válassza a **nagyméretű fájlok letöltése** lehetőséget az **optimalizált** legördülő listából.

### <a name="general-media-streaming"></a>Általános médiaadatfolyam-továbbítás

Ha a végpontot kell használnia az élő adatfolyam-továbbításhoz és a videó igény szerinti átviteléhez, válassza ki az általános médiaadatfolyam-továbbítási optimalizálási típust.

A médiaadatfolyam-továbbítás időérzékeny, mivel az ügyfélen későn érkező csomagok, például a videotartalom gyakori pufferelése miatt csökkenhet a megtekintési élmény. A médiaadatfolyam-továbbítás optimalizálása csökkenti a médiatartalom-kézbesítés késését, és zökkenőmentes folyamatos átviteli élményt biztosít a felhasználók számára. 

Ez a forgatókönyv gyakori az Azure Media Service ügyfelei számára. Az Azure Media Services használatakor egyetlen folyamatos átviteli végpontot kap, amely élő és igény szerinti folyamatos átvitelhez is használható. Ebben a forgatókönyvben az ügyfeleknek nem kell átváltaniuk egy másik végpontra, amikor az élőról igény szerinti folyamatos átvitelre változnak. Az általános médiaadatfolyam-továbbítási optimalizálás támogatja az ilyen típusú forgatókönyvet.

A **microsofttól Azure CDN standard**, a **verizontól Azure CDN a standard**, a **verizontól a prémium Azure CDN** pedig az általános webes kézbesítési optimalizálási típus használatával továbbíthatja az általános adatfolyam-tartalmat.

A médiaadatfolyam-továbbítás optimalizálásával kapcsolatos további információkért lásd: [Médiaadatfolyam-továbbítás optimalizálása](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Igény szerinti video-adatfolyam

Az igény szerinti video-adatfolyam-optimalizálás javítja az igény szerinti video-adatfolyam-tartalmat. Ha a videó igény szerinti átviteléhez végpontot használ, használja ezt a lehetőséget.

A Azure CDN **microsofttól Azure CDN szabványtól**, a **Verizontól a standard** szinttől és a Verizon-profilokkal való **Azure CDN prémium** szinttől függően az általános webes kézbesítés optimalizálási típusával továbbíthatja az igény szerinti videó-továbbítási tartalmat.

A médiaadatfolyam-továbbítás optimalizálásával kapcsolatos további információkért lásd: [Médiaadatfolyam-továbbítás optimalizálása](cdn-media-streaming-optimization.md).

> [!NOTE]
> Ha a CDN-végpont elsődlegesen igény szerinti tartalmat szolgáltat, használja ezt az optimalizálási típust. Az optimalizálási típus és az általános médiaadatfolyam-továbbítási optimalizálási típus közötti fő különbség a kapcsolat újrapróbálkozási időkorlátja. Az időtúllépés sokkal rövidebb, mint az élő közvetítési forgatókönyvek használata.
>

### <a name="large-file-download"></a>Nagyméretű fájlok letöltése

A Akamai-profiloktól **Azure CDN standard szintű** fájlok esetében a nagyméretű fájlok letöltése 10 MB-nál nagyobb tartalomra van optimalizálva. Ha az átlagos fájlméret 10 MB-nál kisebb, használja az általános webes kézbesítést. Ha az átlagos fájlméretek mérete folyamatosan meghaladja a 10 MB-ot, akkor lehet, hogy hatékonyabb, ha egy különálló végpontot hoz létre a nagyméretű fájlokhoz. Például a belső vezérlőprogram vagy a szoftverfrissítések általában nagy méretű fájlok. A 1,8 GB-nál nagyobb fájlok továbbításához a nagyméretű fájlok letöltésének optimalizálása szükséges.

A **microsofttól Azure CDN standard**, a **verizontól Azure CDN a standard**, a Verizon-profiloktól a **prémium Azure CDN** pedig a nagyméretű fájlok letöltéséhez használt általános webes kézbesítés optimalizálási típussal. A fájl letöltési mérete nincs korlátozva.

A nagyméretű fájlok optimalizálásával kapcsolatos további információkért lásd: [nagyméretű fájlok optimalizálása](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Dinamikus helygyorsítás

 A dinamikus hely gyorsulása (DSA) **Azure CDN standard Akamai**, **Azure CDN standard from Verizon** és a verizon-profilok **prémium Azure CDN** . Ez az optimalizálás a használat további díját is magában foglalja. További információ: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> A Microsoft által kínált dinamikus webhely-gyorsítás az [Azure bejárati szolgáltatásán](../frontdoor/front-door-overview.md) keresztül érhető el, amely a Microsoft saját globális hálózatát hasznosító, az alkalmazás számítási feladatainak ellátására szolgáló globális [önkiszolgáló szolgáltatás](https://en.wikipedia.org/wiki/Anycast) .

A DSA különféle technikákat tartalmaz, amelyek kihasználják a dinamikus tartalmak késését és teljesítményét. A technikák közé tartozik az útvonal és a hálózat optimalizálása, a TCP-optimalizálás és egyebek. 

Ezzel az optimalizálással felgyorsíthatja a webalkalmazást, amely számos, nem gyorsítótárazható választ tartalmaz. Ilyenek például a keresési eredmények, a pénztári tranzakciók vagy a valós idejű adatmennyiség. Továbbra is használhatja az alapszintű Azure CDN gyorsítótárazási funkcióit a statikus adatkezeléshez. 

A dinamikus hely gyorsításával kapcsolatos további információkért lásd: [dinamikus hely gyorsítás](cdn-dynamic-site-acceleration.md).