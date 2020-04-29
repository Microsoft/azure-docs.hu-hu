---
title: Az Azure Traffic Manager beállításainak ellenőrzése
description: Ebből a cikkből megtudhatja, hogyan ellenőrizheti a Traffic Manager beállításait, és tesztelheti a forgalom-útválasztási módszert.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 94ab5e550f0053fa19b9b93f1d67690211543325
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76938380"
---
# <a name="verify-traffic-manager-settings"></a>Traffic Manager-beállítások ellenőrzése

A Traffic Manager beállításainak teszteléséhez több ügyfelet kell használnia különböző helyekről, amelyekről futtathatja a teszteket. Ezután kapcsolja le a végpontokat a Traffic Manager-profilban egy időben.

* Állítsa a DNS TTL értéket alacsony értékre, hogy a változások gyorsan továbbíthatók legyenek (például 30 másodperc).
* Ismerje meg az Azure Cloud Services és websites IP-címeit a tesztelni kívánt profilban.
* A DNS-név IP-címhez való feloldására és a cím megjelenítésére szolgáló eszközök használata.

A rendszer ellenőrzi, hogy a DNS-nevek a profilban található végpontok IP-címeire vannak-e feloldva. A neveket a Traffic Manager profilban definiált forgalom-útválasztási módszerrel konzisztens módon kell feloldani. A DNS-nevek feloldásához használhatja az **nslookup** vagy a **dig** eszközt is.

Az alábbi példák segítséget nyújtanak a Traffic Manager-profil tesztelésében.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Traffic Manager profiljának keresése az nslookup és az ipconfig használatával Windows rendszerben

1. Nyisson meg egy parancsot vagy a Windows PowerShell-parancssort rendszergazdaként.
2. A `ipconfig /flushdns` DNS-feloldó gyorsítótár kiürítéséhez írja be a következőt:.
3. Gépelje be: `nslookup <your Traffic Manager domain name>`. A következő parancs például a *SajátPr. contoso* előtaggal ellenőrzi a tartománynevet.

        nslookup myapp.contoso.trafficmanager.net

    Egy tipikus eredmény a következő információkat jeleníti meg:

    + Annak a DNS-kiszolgálónak a DNS-neve és IP-címe, amely a Traffic Manager tartománynév feloldásához van elérhető.
    + A parancssorban az "nslookup" után beírt Traffic Manager tartománynevet, valamint azt az IP-címet, amelyre a Traffic Manager tartomány feloldódik. A második IP-cím az a fontos, amellyel ellenőrizhető. Meg kell egyeznie egy nyilvános virtuális IP-címmel (VIP) a tesztelni kívánt Traffic Manager profil egyik felhőalapú szolgáltatásához vagy webhelyéhez.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>A feladatátvételi forgalom útválasztási módszerének tesztelése

1. Hagyja meg az összes végpontot.
2. Egyetlen ügyfél használatával kérjen DNS-feloldást a vállalati tartomány neveként az nslookup vagy egy hasonló segédprogram használatával.
3. Győződjön meg arról, hogy a megoldott IP-cím megegyezik az elsődleges végponttal.
4. Hozza le az elsődleges végpontot, vagy távolítsa el a megfigyelési fájlt, hogy Traffic Manager úgy gondolja, hogy az alkalmazás nem áll le.
5. Várjon, amíg a Traffic Manager profil DNS élettartama (TTL) és további két perc is megmarad. Ha például a DNS-TTL 300 másodperc (5 perc), akkor hét percet kell várnia.
6. Ürítse ki a DNS-ügyfél gyorsítótárát, és kérjen DNS-feloldást az nslookup használatával. A Windows rendszerben a DNS-gyorsítótárat az ipconfig/flushdns paranccsal ürítheti ki.
7. Győződjön meg arról, hogy a megoldott IP-cím megfelel a másodlagos végpontnak.
8. Ismételje meg a folyamatot, és kapcsolja le az egyes végpontokat. Ellenőrizze, hogy a DNS visszaadja-e a listában a következő végpont IP-címét. Ha az összes végpont le van hajtva, az elsődleges végpont IP-címét újra be kell szereznie.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>A súlyozott forgalom útválasztási módszerének tesztelése

1. Hagyja meg az összes végpontot.
2. Egyetlen ügyfél használatával kérjen DNS-feloldást a vállalati tartomány neveként az nslookup vagy egy hasonló segédprogram használatával.
3. Győződjön meg arról, hogy a megoldott IP-cím megfelel a végpontok egyikének.
4. Ürítse ki a DNS-ügyfél gyorsítótárát, és ismételje meg a 2. és a 3. lépést mindegyik végpont esetében Mindegyik végponthoz különböző IP-címeket kell visszaadnia.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>A teljesítmény forgalmának útválasztási módszerének tesztelése

A teljesítmény-forgalom útválasztási módszerének hatékony teszteléséhez a világ különböző részeiben található ügyfeleknek kell lennie. A szolgáltatások teszteléséhez különböző Azure-régiókban is létrehozhat ügyfeleket. Ha globális hálózattal rendelkezik, távolról is bejelentkezhet az ügyfelekre a világ más részein, és innen futtathatja a teszteket.

Azt is megteheti, hogy elérhetővé vált a webes DNS-keresés és a kiásási szolgáltatás. Ezen eszközök némelyike lehetővé teszi a DNS-névfeloldás ellenőrzését a világ különböző helyeiről. Példák esetén keressen rá a "DNS-címkeresés" kifejezésre. A harmadik féltől származó szolgáltatások, például a Gomez vagy a Keynote használatával ellenőrizhetik, hogy a profilok a várt módon forgalmazzák-e a forgalmat.

## <a name="next-steps"></a>További lépések

* [Tudnivalók a Traffic Manager forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
* [A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
* [Hibaelhárítás a Traffic Manager „Csökkentett teljesítményű” állapota esetén](traffic-manager-troubleshooting-degraded.md)
