---
title: IPSec-alagút létrehozása az Azure VMware-megoldásba
description: Megtudhatja, hogyan hozhat létre virtuális WAN-hubot IPSec-alagút létrehozásához az Azure VMware-megoldásokban.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 21df674862b65ef6573a8a3fcfd7538b1053f04e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491842"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>IPSec-alagút létrehozása az Azure VMware-megoldásba

Ebben a cikkben elsajátítjuk a VPN (IPsec IKEv1 és IKEv2) helyek közötti alagút leállításának lépéseit a Microsoft Azure Virtual WAN hub-ban. Létrehozunk egy Azure-beli virtuális WAN-hubot és egy VPN-átjárót, amelyhez hozzá van rendelve egy nyilvános IP-cím. Ezután létrehozunk egy Azure ExpressRoute-átjárót, és létrehozunk egy Azure VMware-megoldási végpontot. A házirend-alapú VPN helyszíni telepítésének engedélyezésével kapcsolatban is tájékozódhat. 

## <a name="topology"></a>Topológia

![A VPN-helyek közötti bújtatási architektúrát ábrázoló diagram.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Az Azure-beli virtuális központ tartalmazza az Azure VMware megoldás ExpressRoute-átjáróját és a két hálózat közötti pont-pont típusú VPN-átjárót. Egy helyszíni VPN-eszközt csatlakoztat egy Azure VMware-megoldási végponttal.

## <a name="before-you-begin"></a>Előkészületek

A helyek közötti VPN-alagút létrehozásához létre kell hoznia egy nyilvános IP-címet, amely egy helyszíni VPN-eszközön leáll.

## <a name="create-a-virtual-wan-hub"></a>Virtuális WAN-központ létrehozása

1. A Azure Portal keressen rá a **virtuális WAN**-ra. Válassza a **+Hozzáadás** lehetőséget. Megnyílik a WAN létrehozása lap.  

2. Adja meg a szükséges mezőket a **WAN létrehozása** lapon, majd válassza a **felülvizsgálat + létrehozás** elemet.
   
   | Mező | Érték |
   | --- | --- |
   | **Előfizetés** | Az érték előre fel van töltve az erőforráscsoporthoz tartozó előfizetéssel. |
   | **Erőforráscsoport** | A virtuális WAN egy globális erőforrás, és nem korlátozódik egy adott régióra.  |
   | **Erőforráscsoport helye** | A virtuális WAN-központ létrehozásához meg kell adnia az erőforráscsoport helyét.  |
   | **Név** |   |
   | **Típus** | Válassza a **Standard (normál**) lehetőséget, amely több, mint a VPN Gateway-forgalmat is lehetővé teszi.  |

   :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="A Azure Portal a WAN létrehozása lapot ábrázoló képernyőkép.":::

3. A Azure Portal válassza ki az előző lépésben létrehozott virtuális WAN-t, válassza a **virtuális központ létrehozása** elemet, adja meg a szükséges mezőket, majd válassza a **Tovább: helyről a helyre** lehetőséget. 

   | Mező | Érték |
   | --- | --- |
   | **Régió** | A régió kiválasztásához felügyeleti szempontból szükséges.  |
   | **Név** |    |
   | **Központ titkos címtartománya** | Adja meg az alhálózatot a következő használatával: `/24` (minimum).  |

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="A virtuális központ létrehozása lapot ábrázoló képernyőfelvétel.":::

4. A **helyek közötti** lapon adja meg a helyek közötti átjárót úgy, hogy az összesített átviteli sebességet az **átjáró skálázási egységei** legördülő menüből állítja be. 

   >[!TIP]
   >Egy méretezési egység = 500 Mbps. A skálázási egységek a redundancia, a 500 Mbps-t támogató párok.
  
5. A **ExpressRoute** lapon hozzon létre egy ExpressRoute-átjárót. 

   >[!TIP]
   >A méretezési egység értéke 2 GB/s. 

    Az egyes hubok létrehozása körülbelül 30 percet vesz igénybe. 

## <a name="create-a-vpn-site"></a>VPN-hely létrehozása 

1. A Azure Portal **legutóbbi erőforrásaiban** válassza ki az előző szakaszban létrehozott virtuális WAN-t.

2. A virtuális központ **áttekintésében** válassza a **kapcsolati**  >  **VPN (helyek közötti)** lehetőséget, majd válassza az **új VPN-hely létrehozása** lehetőséget.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Képernyőfelvétel a virtuális központ áttekintés lapjáról, a VPN-nel (helyek közötti kapcsolat) és a kiválasztott új VPN-hely létrehozásáról.":::  
 
3. Az **alapvető beállítások** lapon adja meg a kötelező mezőket, majd válassza a **Tovább: hivatkozások** elemet. 

   | Mező | Érték |
   | --- | --- |
   | **Régió** | Az előző szakaszban megadott régió.  |
   | **Név** |  |
   | **Eszköz szállítója** |  |
   | **Border Gateway Protocol** | Az **Engedélyezés** beállítás megadásával biztosíthatja, hogy az Azure VMware-megoldás és a helyszíni kiszolgálók is meghirdessek az útvonalakat az alagúton keresztül. Ha le van tiltva, a meghirdetni kívánt alhálózatokat manuálisan kell karbantartani. Ha az alhálózatok kimaradnak, a HCX nem fogja tudni kiképezni a szolgáltatás hálóját. További információ:  [a BGP és az Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Magánhálózati címterület**  | Adja meg a helyszíni CIDR blokkot.  A rendszer az összes, a helyszíni alagúton keresztüli forgalom irányítására használatos.  A CIDR blokkra csak akkor van szükség, ha nem engedélyezi a BGP-t. |
   | **Csatlakozás a következőhöz:** |   |

4. A **hivatkozások** lapon töltse ki a kötelező mezőket, és válassza a **felülvizsgálat + létrehozás** elemet. A hivatkozások és a szolgáltatók nevének megadásával megkülönböztetni a csomópontok részeként esetlegesen létrehozott átjárók számát. A BGP és az autonóm System number (ASN) egyedinek kell lennie a szervezeten belül.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>Választható VPN-hely meghatározása házirend-alapú VPN-helyek közötti alagutakhoz

Ez a szakasz csak a házirend-alapú VPN-ek esetében érvényes. A házirend-alapú (vagy statikus, útválasztás-alapú) VPN-beállításokat a legtöbb esetben a helyszíni VPN-eszköz képességei vezérlik. Szükségük van a helyszíni és az Azure VMware megoldási hálózatokra. Azure-beli virtuális WAN-központtal rendelkező Azure VMware-megoldás esetén *egyetlen* hálózat sem választható ki. Ehelyett meg kell adnia az összes releváns helyi és Azure VMware-megoldás virtuális WAN hub-tartományát. Ezek a hub-tartományok a házirend alap VPN-alagút helyszíni végpontjának titkosítási tartományának megadására szolgálnak. Az Azure VMware megoldás oldalának csak a házirend-alapú forgalom kiválasztó jelzőjének engedélyezésére van szükség. 

1. A Azure Portal lépjen a virtuális WAN-központ webhelyére. A **kapcsolat** területen válassza a **VPN (hely helye)** lehetőséget.

2. Válassza ki a VPN-hely nevét, a jobb szélen lévő három pontot (...), majd **szerkessze a VPN-kapcsolatokat ehhez a hubhoz**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Képernyőkép az Azure-beli oldalról a virtuális WAN hub-helyhez, amely egy, a központhoz való VPN-kapcsolat szerkesztésére kijelölt három pontot jelenít meg." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Szerkessze a VPN-hely és a központ közötti kapcsolatot, majd kattintson a **Mentés** gombra.
   - Internet Protocol biztonság (IPSec), válassza az **Egyéni** lehetőséget.
   - Házirend alapú forgalom-választó használata, válassza az **Engedélyezés** lehetőséget.
   - Az **1** . és az **IKE 2. fázis (IPSec)** adatainak megadása. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Képernyőkép a VPN-kapcsolat szerkesztése oldalról."::: 
 
   A házirend-alapú titkosítási tartomány részét képező forgalmi választók vagy alhálózatok a következők:
    
   - A virtuális WAN hub/24
   - Azure VMware-megoldás saját felhő/22
   - A csatlakoztatott Azure-beli virtuális hálózat (ha van)

## <a name="connect-your-vpn-site-to-the-hub"></a>A VPN-hely összekötése a hubhoz

1. Válassza ki a VPN-hely nevét, majd válassza a **VPN-helyek összekapcsolását**. 
1. A **előmegosztott kulcs** mezőben adja meg a helyi végponthoz korábban definiált kulcsot. 

   >[!TIP]
   >Ha még nem rendelkezik korábban definiált kulccsal, ezt a mezőt üresen hagyhatja. Automatikusan létrejön egy kulcs. 
 
   >[!IMPORTANT]
   >Csak akkor engedélyezze az **alapértelmezett útválasztást** , ha tűzfalat telepít a központba, és ez a következő ugrás az adott alagúton keresztüli kapcsolatokhoz.

1. Válassza a **Kapcsolódás** lehetőséget. A kapcsolatok állapota képernyőn az alagút létrehozásának állapota látható.

2. A helyszíni végpont VPN-konfigurációs fájljának letöltéséhez nyissa meg a virtuális WAN áttekintést, és nyissa meg a VPN-hely lapot.  

3. Az Azure VMware megoldás ExpressRoute javítása a virtuális WAN-központban. Ehhez a lépéshez először létre kell hozza a saját felhőjét.

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Kapcsolja össze az Azure VMware-megoldást és a VPN-átjárót a virtuális WAN-központban. 
   1. A Azure Portal nyissa meg a korábban létrehozott virtuális WAN-t. 
   1. Válassza ki a létrehozott virtuális WAN-hubot, majd a bal oldali panelen válassza a **ExpressRoute** lehetőséget. 
   1. Válassza a **+ engedélyezési kulcs beváltása** elemet.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Képernyőfelvétel a ExpressRoute oldaláról a privát felhőhöz, a beváltási engedélyezési kulcs kiválasztásával.":::

   1. Illessze be az engedélyezési kulcsot az engedélyezési kulcs mezőbe.
   1. A ExpressRoute-azonosító lejárt a **társ-áramkör URI** mezőjébe. 
   1. Válassza **a ExpressRoute-áramkör automatikus hozzárendelése a hubhoz lehetőséget.** 
   1. A hivatkozás létrehozásához válassza a **Hozzáadás** lehetőséget. 

5. Tesztelje a [NSX-T, és hozzon létre](./tutorial-nsx-t-network-segment.md) egy virtuális gépet a hálózaton. Pingelje mind a helyszíni, mind az Azure VMware-megoldás végpontját.
