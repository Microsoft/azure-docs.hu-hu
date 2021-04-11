---
title: A kommunikációs szolgáltatások Azure-beli internetes összevonása – útmutató
titleSuffix: Azure
description: A kommunikációs szolgáltatások Azure-beli internetes összevonása – útmutató
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498976"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>A kommunikációs szolgáltatások Azure-beli internetes összevonása – útmutató

Ez a szakasz azokat a lépéseket ismerteti, amelyeket a Communications Services-szolgáltatónak követnie kell, hogy közvetlen kapcsolatot létesítsen a Microsofttal.

**Communications Services-szolgáltatók:**  A kommunikációs szolgáltatások szolgáltatói olyan szervezetek, amelyek kommunikációs szolgáltatásokat (kommunikációt, üzenetküldést, konferenciákat stb.) kínálnak, és a kommunikációs szolgáltatások infrastruktúráját (SBC/SIP Gateway stb.) szeretnék integrálni.  Az Azure kommunikációs szolgáltatásaival és a Microsoft csapatával. 

Az Azure internetes társ-összevonása támogatja a Communications Services-szolgáltatókat, hogy közvetlen kapcsolatot létesítsenek a Microsofttal bármely peremhálózati helyén (pop-helyek). Az összes nyilvános élek listája a [PeeringDB](https://www.peeringdb.com/net/694)webhelyen érhető el.

Az Azure Internet-összevonása nagy megbízhatóságú és QoS (szolgáltatásminőség) szolgáltatást biztosít a kommunikációs szolgáltatások számára, hogy magas színvonalú és teljesítményű központú szolgáltatásokat biztosítson.

## <a name="technical-requirements"></a>Technikai követelmények
A Direct Interconnect a kommunikációs szolgáltatásokhoz való létrehozásával kapcsolatos technikai követelmények a következők:
-   A társnak saját autonóm rendszerszámot (ASN) kell megadnia, amelynek nyilvánosnak kell lennie.
-   A helyi redundancia biztosítása érdekében a társnak redundáns összekötővel (PNI) kell rendelkeznie minden egyes összekötő helyen.
-   A partnernek földrajzi redundanciával kell rendelkeznie, hogy biztosítsa a feladatátvételt a régióban/metróban a hely meghibásodása esetén.
-   A társnak a BGP-munkamenet aktív-aktívnak kell lennie a magas rendelkezésre állás és a gyorsabb konvergencia biztosításához, és nem szabad elsődlegesként és biztonsági mentésként kiépíteni.
-   A társnak meg kell őriznie egy 1:1-os arányt a társ-összevonási útválasztók és a társítási áramkörök között, és nincs alkalmazva korlátozás.
-   A társnak meg kell adnia és hirdetnie kell saját, nyilvánosan irányítható IPv4-címtartományt, amelyet a társ kommunikációs szolgáltatási végpontja használ (például: SBC). 
-   A társnak részletesen meg kell adnia a forgalom és a végpontok osztályát az egyes hirdetett alhálózatokban. 
-   A társnak BGP-t kell futtatnia a kétirányú továbbítás észlelése (BFD) használatával, hogy elősegítse a másodlagos útvonal konvergenciáját.
-   Minden kommunikációs infrastruktúra előtagja regisztrálva van Azure Portalban, és meghirdetve a 8075:8007-es közösségi karakterlánccal.
-   A társnak állapot-nyilvántartó tűzfalat futtató eszközön nem szabad megszakítani a társítást. 
-   A Microsoft alapértelmezés szerint konfigurálja az összes összekötő-hivatkozást (a hivatkozási kötegeket), ezért a társnak támogatnia kell a LACP (az összesítő vezérlő protokolljának csatolása) az összekötő hivatkozásokon.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Közvetlen összekötő létrehozása a Microsofttal a kommunikációs szolgáltatásokhoz.

Az Azure Internet-kapcsolatot használó közvetlen összekötők létrehozásához kövesse az alábbi lépéseket:

**1. a peer Public ASN társítása az Azure-előfizetéshez:**

Ha a társ már társítva van nyilvános ASN-hez az Azure-előfizetéshez, akkor hagyja figyelmen kívül ezt a lépést.

[Társközi ASN társítása az Azure-előfizetéshez a portál használatával – Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

A következő lépés egy közvetlen társ-kapcsolódás létrehozása a partneri szolgáltatáshoz.

> [!NOTE]
> Az ASN-társítás jóváhagyása után küldje el nekünk az peeringservices@microsoft.com ASN és az előfizetés-azonosítóját, hogy társítsa az előfizetést a kommunikációs szolgáltatásokhoz. 

**2. hozzon létre közvetlen társi kapcsolatot a következő partneri szolgáltatáshoz:**

Kövesse az utasításokat a [közvetlen társítások létrehozásához vagy módosításához a portál használatával](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal)

Győződjön meg arról, hogy megfelel a magas rendelkezésre állási követelményeknek.

Győződjön meg arról, hogy a következő beállításokat választja a "társ létrehozása" oldalon:

Egyenrangú típus:   **közvetlen**

Microsoft Network:  **8075**

SKU:        **ingyenes prémium**


A "közvetlen társítású kapcsolatok" oldalon válassza a következő lehetőségek közül:

Munkamenet-címek szolgáltatója:   **Microsoft**

**Társítási** szolgáltatások használata: engedélyezve

> [!NOTE] 
> A következő üzenet figyelmen kívül hagyása a társítási szolgáltatások aktiválásának kiválasztásakor.
> *Ha nem szeretne kapcsolatba lépni a MAPS-szolgáltatóval, ne engedélyezzen peering@microsoft.com .*


  **2a. a meglévő közvetlen társ-létesítési kapcsolatok használata a partneri szolgáltatásokhoz**

Ha van egy meglévő közvetlen társa, amelyet a partneri szolgáltatás támogatásához kíván használni, aktiválhatja Azure Portal.
1.  Kövesse az utasításokat [egy örökölt közvetlen, az Azure-beli erőforrásra való konvertáláshoz a portál használatával](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal).
Szükség szerint rendeljen további áramköröket a magas rendelkezésre állási követelmények teljesítéséhez.

2.  A következő lépésekkel [engedélyezheti](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal) a társítási szolgáltatást a portálon keresztüli közvetlen társon.




**3. az előtagok regisztrálása az optimalizált útválasztáshoz**

A kommunikációs szolgáltatások infrastrukturális előtagjainak optimalizált útválasztásához regisztrálnia kell az összes előtagokat a társ-összekapcsolásokkal.
[Azure-partneri szolgáltatás regisztrálása – Azure Portal | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

A kommunikációs szolgáltatás partnerei számára az előtag-kulcs automatikusan ki van töltve, így a partnernek nem kell a regisztrációhoz semmilyen előtag-kulcsot használnia. 

Győződjön meg arról, hogy a regisztrált előtagokat a rendszer a régióhoz létrehozott közvetlen kapcsolatokon keresztül hirdeti meg.


## <a name="faqs"></a>Gyakori kérdések:

**Q.**  Kisebb alhálózatokkal (</24) rendelkezem a kommunikációs szolgáltatásokhoz. Lekérhetem a kisebb alhálózatokat is?

**Egy.**  Igen, Microsoft Azure a társ-kezelő szolgáltatás a kisebb előtag-útválasztást is támogatja. Győződjön meg arról, hogy regisztrálja a kisebb előtagokat az útválasztáshoz, és ugyanazokat a rendszer a kapcsolaton keresztül teszi közzé.

**Q.**  Milyen Microsoft-útvonalakat fogadunk a kapcsolaton keresztül?

**Egy.** A Microsoft a Microsoft összes nyilvános szolgáltatásának előtagjait bejelenti a kapcsolaton keresztül. Ez biztosítja, hogy ne csak a kommunikáció, hanem más felhőalapú szolgáltatások is elérhetők legyenek ugyanabból az összekötőből.

**Q.**  Be kell állítania az előtag-korlátot, hány útvonalat szeretne bejelenteni a Microsoft?

**Egy.** A Microsoft nagyjából 280 előtagot jelent az interneten, és a későbbiekben 10-15%-kal növekedhet. Így a 400-500-es biztonságos korlát lehet a "maximális előtag száma" beállítás.

**Q.** A Microsoft újra reklámozni fogja a társ-előtagokat az interneten?

**Egy.** Nem.

**Q.** Díjköteles a szolgáltatás díja?

**Egy.** Nem, azonban a partnernek el kell végeznie a helyek közötti kapcsolat költségeit.

**Q.** Mi a minimális kapcsolat sebessége egy összekötőhöz?

**Egy.** 10Gbps.

**Q.** A társ egy SLA-ra van kötve?

**Egy.** Igen, ha a kihasználtság eléri a 40%-ot, a 45 60day LAG-bővítési folyamatnak meg kell kezdődnie.

**Q.** Mi az előnye ennek a szolgáltatásnak az aktuális közvetlen vagy expressz útvonalon?

**Egy.** Az elszámolás ingyenes és a teljes elérési út a Microsoft WAN-on keresztüli hangforgalomra van optimalizálva, és a konvergencia a BFD-vel való almásodpercre van beállítva.

**Q.** Hogyan hajtja végre a bevezetési folyamatot?

**Egy.** Az idő változó a helyek számától és helyétől függően változik, és ha a társ egy meglévő privát társat telepít át vagy új kábelezést hoz létre. A szolgáltatónak 3 és hét közötti időszakot kell megterveznie.

**Q.** Használhatok API-kat a bevezetéshez?

**Egy.** Jelenleg nincs API-támogatás, és a konfigurációt a webes portálon keresztül kell végrehajtani. 
