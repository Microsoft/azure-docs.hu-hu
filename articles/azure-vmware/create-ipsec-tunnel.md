---
title: IPSec-alagút létrehozása az Azure VMware-megoldásba
description: Ismerje meg, hogyan hozhat létre VPN-(IPsec-IKEv1 és IKEv2-) helyek közötti alagutat az Azure VMware-megoldásokban.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027000"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>IPSec-alagút létrehozása az Azure VMware-megoldásba

Ebben a cikkben elsajátítjuk a VPN (IPsec IKEv1 és IKEv2) helyek közötti alagút leállításának lépéseit a Microsoft Azure Virtual WAN hub-ban. A hub tartalmazza az Azure VMware megoldás ExpressRoute-átjáróját és a két hálózat közötti pont-pont típusú VPN-átjárót. Egy helyszíni VPN-eszközt csatlakoztat egy Azure VMware-megoldási végponttal.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="A VPN-helyek közötti bújtatási architektúrát ábrázoló diagram." border="false":::

Ebben az útmutatóban a következőkre lesz szüksége:
- Hozzon létre egy Azure-beli virtuális WAN-hubot és egy olyan VPN-átjárót, amelyhez hozzá van rendelve egy nyilvános IP-cím. 
- Hozzon létre egy Azure ExpressRoute-átjárót, és hozzon létre egy Azure VMware megoldási végpontot. 
- Házirend-alapú VPN helyszíni telepítésének engedélyezése. 

## <a name="prerequisites"></a>Előfeltételek
Egy helyszíni VPN-eszközön egy nyilvános IP-címmel kell megadnia a végpontot.

## <a name="step-1-create-an-azure-virtual-wan"></a>1. lépés Azure-beli virtuális WAN létrehozása

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>2. lépés Virtuális WAN-központ és-átjáró létrehozása

>[!TIP]
>[Egy meglévő hubhoz is létrehozhat átjárót](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Válassza ki az előző lépésben létrehozott virtuális WAN-t.

1. Válassza a **virtuális központ létrehozása** elemet, adja meg a szükséges mezőket, majd válassza a **Tovább: helyről helyre** lehetőséget. 

   Adja meg az alhálózatot a következő használatával: `/24` (minimum).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="A virtuális központ létrehozása lapot ábrázoló képernyőfelvétel.":::

4. Válassza a **helyek** közötti lapot, és adja meg a helyek közötti átjárót az összesített átviteli sebesség az **átjáró skálázási egységek** legördülő listából való beállításával. 

   >[!TIP]
   >A skálázási egységek a redundancia párban állnak, amelyek mindegyike 500 Mbps (egy méretezési egység = 500 Mbps). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="A helyek közötti adatokat bemutató képernyőkép.":::

5. Válassza a **ExpressRoute** lapot, és hozzon létre egy ExpressRoute-átjárót. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Képernyőkép a ExpressRoute beállításairól.":::

   >[!TIP]
   >A méretezési egység értéke 2 GB/s. 

    Az egyes hubok létrehozása körülbelül 30 percet vesz igénybe. 

## <a name="step-3-create-a-site-to-site-vpn"></a>3. lépés Létrehozhat helyek közötti VPN-kapcsolatot

1. A Azure Portal válassza ki a korábban létrehozott virtuális WAN-t.

2. A virtuális központ **áttekintésében** válassza a **kapcsolat**  >  **VPN (helyek közötti kapcsolat)**  >  **új VPN-hely létrehozása** lehetőséget.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Képernyőfelvétel a virtuális központ áttekintés lapjáról, a VPN-nel (helyek közötti kapcsolat) és a kiválasztott új VPN-hely létrehozásáról.":::  
 
3. Az **alapvető beállítások** lapon adja meg a kötelező mezőket. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Képernyőkép az új VPN-hely alapjai lapról.":::  

   1. Állítsa be a **Border Gateway Protocol** az **engedélyezéshez**.  Ha engedélyezve van, biztosítja, hogy mind az Azure VMware-megoldás, mind a helyszíni kiszolgálók meghirdessek az útvonalakat az alagúton keresztül. Ha le van tiltva, a meghirdetni kívánt alhálózatokat manuálisan kell karbantartani. Ha az alhálózatok kimaradnak, a HCX nem fogja tudni kiképezni a szolgáltatás hálóját. További információ:  [a BGP és az Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. A **magánhálózati címtartomány** mezőben adja meg a helyszíni CIDR blokkot. A rendszer az összes, a helyszíni alagúton keresztüli forgalom irányítására használatos. A CIDR blokkra csak akkor van szükség, ha nem engedélyezi a BGP-t.

1. Válassza a **Next (tovább** ) gombot, és töltse ki a kötelező mezőket. A hivatkozások és a szolgáltatók nevének megadásával megkülönböztetni a csomópontok részeként esetlegesen létrehozott átjárók számát. A BGP és az autonóm System number (ASN) egyedinek kell lennie a szervezeten belül.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="A hivatkozás részleteit megjelenítő képernyőkép.":::

1. Válassza az **Áttekintés + létrehozás** lehetőséget. 

1. Navigáljon a kívánt virtuális hubhoz, és törölje a hub- **társítás** kijelölését a VPN-hely hubhoz való összekapcsolásához.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Képernyőkép, amely a Előmegosztott kulcs és a társított beállításokhoz készen álló virtuális központ csatlakoztatott helyeinek paneljét jeleníti meg.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>4. lépés: Választható Házirend-alapú VPN-helyek közötti alagutak létrehozása

>[!IMPORTANT]
>Ez egy opcionális lépés, és csak a házirend-alapú VPN-ek esetében érvényes. 

A házirend-alapú VPN-beállításokhoz meg kell adni a helyszíni és az Azure VMware megoldás-hálózatokat, beleértve a hub tartományokat is.  Ezek a hub-tartományok a házirend alapú VPN-alagút helyi végpontjának titkosítási tartományát adják meg.  Az Azure VMware megoldás oldalának csak a házirend-alapú forgalom kiválasztó jelzőjének engedélyezésére van szükség. 

1. A Azure Portal lépjen a virtuális WAN-központ webhelyére. A **kapcsolat** területen válassza a **VPN (hely helye)** lehetőséget.

2. Válassza ki a VPN-hely nevét, a jobb szélen lévő három pontot (...), majd **szerkessze a VPN-kapcsolatokat ehhez a hubhoz**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Képernyőkép az Azure-beli oldalról a virtuális WAN hub-helyhez, amely egy, a központhoz való VPN-kapcsolat szerkesztésére kijelölt három pontot jelenít meg." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Szerkessze a VPN-hely és a központ közötti kapcsolatot, majd kattintson a **Mentés** gombra.
   - Internet Protocol biztonság (IPSec), válassza az **Egyéni** lehetőséget.
   - Házirend alapú forgalom-választó használata, válassza az **Engedélyezés** lehetőséget.
   - Az **1** . és az **IKE 2. fázis (IPSec)** adatainak megadása. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Képernyőkép a VPN-kapcsolat szerkesztése oldalról."::: 
 
   A házirend-alapú titkosítási tartomány részét képező forgalmi választók vagy alhálózatok a következők:
    
   - Virtuális WAN-központ `/24`
   - Azure VMware-megoldás – saját felhő `/22`
   - Csatlakoztatott Azure-beli virtuális hálózat (ha van)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>5. lépés A VPN-hely összekötése a hubhoz

1. Válassza ki a VPN-hely nevét, majd válassza a **VPN-helyek összekapcsolását**. 

1. A **előmegosztott kulcs** mezőben adja meg a helyi végponthoz korábban definiált kulcsot. 

   >[!TIP]
   >Ha még nem rendelkezik korábban definiált kulccsal, ezt a mezőt üresen hagyhatja. Automatikusan létrejön egy kulcs. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Képernyőfelvétel: az Előmegosztott kulcs és a társított beállításokhoz készen álló virtuális központ csatlakoztatott helyeinek panelje. "::: 

1. Ha tűzfalat helyez üzembe a központban, és ez a következő ugrás, az **engedélyezéshez** állítsa az **alapértelmezett útvonal propagálása** lehetőséget. 

   Ha ez a beállítás engedélyezve van, a virtuális WAN hub csak akkor propagálja a kapcsolatot, ha a központ már megtanulta az alapértelmezett útvonalat, amikor tűzfalat telepít a központban, vagy ha egy másik csatlakoztatott hely kényszerített bújtatást engedélyez. Az alapértelmezett útvonal nem a virtuális WAN-hubhoz származik.  

1. Válassza a **Kapcsolódás** lehetőséget. Néhány perc elteltével a hely megjeleníti a kapcsolat és a kapcsolat állapotát.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="A helyek közötti kapcsolat és a kapcsolat állapotát bemutató képernyőkép." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Töltse le a helyszíni végpont VPN-konfigurációs fájlját](../virtual-wan/virtual-wan-site-to-site-portal.md#device) .  

3. Az Azure VMware megoldás ExpressRoute javítása a virtuális WAN-központban. 

   >[!IMPORTANT]
   >A platform javítása előtt létre kell hoznia egy saját felhőt. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Kapcsolja össze az Azure VMware-megoldást és a VPN-átjárót a virtuális WAN-központban. Az előző lépésben az engedélyezési kulcsot és a ExpressRoute AZONOSÍTÓját (peer Circuit URI) kell használnia.

   1. Válassza ki a ExpressRoute-átjárót, majd válassza az **engedélyezési kulcs beváltása** elemet.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Képernyőfelvétel a ExpressRoute oldaláról a privát felhőhöz, a beváltási engedélyezési kulcs kiválasztásával.":::

   1. Illessze be az engedélyezési kulcsot az **engedélyezési kulcs** mezőbe.
   1. Illessze be a ExpressRoute AZONOSÍTÓját a **társ-áramkör URI** mezőjébe. 
   1. Jelölje be **a ExpressRoute-áramkör automatikus hozzárendelése a hubhoz** jelölőnégyzetet. 
   1. A hivatkozás létrehozásához válassza a **Hozzáadás** lehetőséget. 

5. Tesztelje a [NSX-T, és hozzon létre](./tutorial-nsx-t-network-segment.md) egy virtuális gépet a hálózaton. Pingelje mind a helyszíni, mind az Azure VMware-megoldás végpontját.
