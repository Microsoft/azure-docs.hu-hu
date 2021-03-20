---
title: 'Azure-ExpressRoute: a BFD konfigurálása'
description: Ez a cikk útmutatást nyújt arról, hogyan konfigurálhatja a BFD (kétirányú továbbítás észlelése) egy ExpressRoute-áramkör privát társának használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511263"
---
# <a name="configure-bfd-over-expressroute"></a>BFD konfigurálása a ExpressRoute-en keresztül

A ExpressRoute támogatja a kétirányú továbbítási észlelést (BFD) a magán-és a Microsoft-partnereknél. Ha engedélyezi a BFD-t a ExpressRoute-en keresztül, felgyorsíthatja a Microsoft Enterprise Edge-(MSEE-) eszközök és a ExpressRoute-áramkör által konfigurált útválasztók (CE/PE) közötti kapcsolódási hibák észlelését. A ExpressRoute a peremhálózati útválasztási eszközökön vagy a partner peremhálózati útválasztási eszközein keresztül is konfigurálhatja (ha a felügyelt 3. rétegbeli kapcsolati szolgáltatással járt). Ez a dokumentum részletesen ismerteti a BFD szükségességét, valamint azt, hogy hogyan engedélyezhető a BFD a ExpressRoute-en keresztül.

## <a name="need-for-bfd"></a>BFD szükséges

A következő ábra a BFD engedélyezésének előnyeit mutatja be a ExpressRoute-áramkörön keresztül: [![1]][1]

A ExpressRoute áramkört a 2. rétegbeli kapcsolatok vagy a felügyelt 3. rétegbeli kapcsolatok is lehetővé teszik. Mindkét esetben, ha egynél több 2. rétegbeli eszköz van a ExpressRoute-kapcsolat elérési útjában, az elérési úton található kapcsolódási hibák észlelésének feladata a túlterhelt BGP-munkamenet.

A MSEE-eszközökön a BGP Keep-Alive és a Holding Time általában 60 és 180 másodpercre van konfigurálva. Erre az esetre, ha a kapcsolódási hiba történik, akár három percet is igénybe vehet, hogy észlelje a kapcsolódási hibát, és átváltsa a forgalmat más kapcsolatra.

A BGP-időzítők vezérléséhez konfigurálja az alacsonyabb BGP Keep-Alive-t, és őrizze meg az időt a peremhálózati társ-kezelő eszközön. Ha a BGP-időzítők nem egyeznek meg a két társ eszköz között, a BGP-munkamenet az alsó idő értékével fog megjelenni. A BGP Keep-Alive értéke akár három másodperc is lehet, a megőrzési idő pedig 10 másodpercnél kisebb. Azonban a nagyon agresszív BGP-időzítő beállítása nem ajánlott, mert a protokoll intenzíven dolgozza fel.

Ebben az esetben a BFD segíthet. A BFD alacsony terhelésű kapcsolati hibák észlelését teszi lehetővé egy másodperces időintervallumban. 


## <a name="enabling-bfd"></a>BFD engedélyezése

A BFD alapértelmezés szerint a Msee összes újonnan létrehozott ExpressRoute-társítási felületén van konfigurálva. Így a BFD engedélyezéséhez csak az elsődleges és a másodlagos eszközön kell konfigurálnia a BFD-t. A BFD konfigurálása kétlépéses folyamat. Konfigurálja a BFD a csatolón, majd kapcsolja össze a BGP-munkamenettel.

Alább látható például a CE/PE (Cisco IOS XE használatával) konfiguráció. 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>A BFD engedélyezése már meglévő privát társak között; alaphelyzetbe kell állítania a társítást. Lásd: ExpressRoute-társítások [alaphelyzetbe állítása][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD időzítő egyeztetése

A BFD-partnerek között a két partner lassabban határozzák meg az átviteli sebességet. A Msee BFD átviteli/fogadási intervallumai 300 ezredmásodpercre vannak beállítva. Bizonyos esetekben az intervallum értéke 750 ezredmásodpercnél nagyobb lehet. Ha magasabb értéket állít be, az intervallumok hosszabbak lehetnek, de nem lehetséges, hogy rövidebbek legyenek.

>[!NOTE]
>Ha a Geo-redundáns ExpressRoute-áramköröket konfigurálta, vagy a helyek közötti IPSec VPN-kapcsolatot használja biztonsági másolatként. A BFD engedélyezése a ExpressRoute kapcsolódási hibája után gyorsabban segít a feladatátvételben. 
>

## <a name="next-steps"></a>Következő lépések

További információért és segítségért tekintse meg az alábbi hivatkozásokat:

- [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása][CreateCircuit]
- [ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.pnga "BFD meggyorsítja a kapcsolati hibák levonásának idejét"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md