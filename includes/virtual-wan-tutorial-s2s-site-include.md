---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/11/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 708baa83ca919adcc374be36c229ce3ff30da384
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362944"
---
1. A virtuális WAN portál lapjának **kapcsolat** szakaszában válassza a **VPN-helyek** lehetőséget a VPN-helyek lap megnyitásához.
1. A **VPN-helyek** lapon kattintson a **+Hely létrehozása** elemre.
1. A **VPN-hely létrehozása** lap **alapok** lapján végezze el a következő mezőket:

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Alapbeállítások lap" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **Régió** – előzőleg helynek nevezzük. Ez az a hely, ahol létre szeretné hozni a hely erőforrását a alkalmazásban.
    * **Név** – a név, amellyel a helyszíni helyre hivatkozni kíván.
    * **Eszköz szállítója** – a VPN-eszköz gyártójának neve (például: Citrix, Cisco, Barracuda). Az eszköz gyártójának hozzáadása segítheti az Azure-csapatot abban, hogy jobban megértse a környezetét, hogy később további optimalizálási lehetőségeket lehessen felvenni, vagy segítséget nyújtson a hibák megoldásához.
    * **Magánhálózati címterület** – a helyszíni helyen található IP-címtartomány. Az erre a címtérre címzett forgalom át lesz irányítva a helyszíni helyre. Erre akkor van szükség, ha a BGP nincs engedélyezve a webhelyhez.
    
      >[!NOTE]
      >Ha a hely létrehozása után szerkeszti a címtartományt (például egy további címterület hozzáadása), akkor 8-10 percet is igénybe vehet, amíg az összetevők újból létrejönnek.
      >
1. Válassza a **hivatkozások** lehetőséget az ág fizikai hivatkozásaival kapcsolatos információk hozzáadásához. Ha egy virtuális WAN-partner CPE-eszközzel rendelkezik, egyeztessen velük, hogy az információk az Azure-ral együtt, a saját rendszeréből való feltöltésük részeként legyenek-e kicserélve.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Hivatkozások lap" lightbox="./media/virtual-wan-tutorial-site-include/site-links.png":::

   * **Hivatkozás neve** – a VPN-helyen található fizikai hivatkozáshoz megadni kívánt név. Példa: mylink1.
   * **Kapcsolat sebessége** – ez a VPN-eszköz sebessége a fiókiroda helyén. Például: 50, ami azt jelenti, hogy 50 Mbps a VPN-eszköz sebessége a fiókirodában.
   * **Hivatkozási szolgáltató neve** – a fizikai hivatkozás neve a VPN-helyen. Példa: ATT, Verizon.
   * **Kapcsolat IP-címe/teljes tartományneve** – a helyszíni eszköz nyilvános IP-címe ezen a hivatkozáson keresztül. Opcionálisan megadhatja a helyszíni VPN-eszköz magánhálózati IP-címét, amely a ExpressRoute mögött található. A teljes tartománynevet is megadhatja. Például: *Something.contoso.com*. A teljes tartománynév feloldható a VPN-átjáróról. Ez akkor lehetséges, ha a teljes tartománynevet üzemeltető DNS-kiszolgáló elérhető az interneten keresztül. Az IP-cím elsőbbséget élvez, ha az IP-cím és a teljes tartománynév is meg van adva.

     >[!NOTE]
     >
     >* A teljes tartományneven egy IPv4-t támogat. Ha a teljes tartománynevet több IP-címhez szeretné feloldani, akkor a VPN-átjáró az első IP4-címet felveszi a listából. Az IPv6-címek jelenleg nem támogatottak.
     >
     >* A VPN-átjáró olyan DNS-gyorsítótárat tart fenn, amely 5 percenként frissül. Az átjáró csak a leválasztott alagutak teljes tartománynevét próbálja meg feloldani. Az átjáró alaphelyzetbe állítása vagy A konfiguráció módosítása A teljes tartománynév feloldását is elindíthatja.
     >
   * **Hivatkozás Border Gateway Protocol** – a BGP konfigurálása virtuális WAN-kapcsolaton egyenértékű a BGP Azure-beli virtuális hálózati átjáró VPN-en való konfigurálásával. A helyszíni BGP-társ címe nem lehet azonos a VPN által az eszközre vagy a VPN-hely VNet-címére vonatkozó nyilvános IP-címmel. Használjon egy másik IP-címet a VPN-eszközön a BGP-társ IP-címéhez. Ez lehet egy olyan cím is, amely az eszköz visszacsatolási hálózatához van rendelve. Ezt a címeket a helyet képviselő megfelelő VPN-helyen adhatja meg.  A BGP-előfeltételeket lásd: [a BGP és az Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). A VPN-kapcsolati kapcsolat bármikor szerkeszthető a BGP-paraméterek (a kapcsolaton és a AS #) frissítésével.
1. További hivatkozásokat is hozzáadhat vagy törölhet. VPN-helyek esetében négy hivatkozás támogatott. Ha például négy internetszolgáltatója van (internetszolgáltató) a fiókirodában, négy hivatkozást hozhat létre, amelyek mindegyike egy-egy INTERNETSZOLGÁLTATÓnál létrehozható, és az egyes hivatkozások adatai megadhatók.
1. Miután befejezte a mezők kitöltését, válassza a **felülvizsgálat + létrehozás** lehetőséget a hely ellenőrzéséhez és létrehozásához.
1. Navigáljon a kívánt virtuális hubhoz, és törölje a hub- **társítás** kijelölését a VPN-hely hubhoz való összekapcsolásához.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="Kapcsolódás ehhez a hubhoz" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
