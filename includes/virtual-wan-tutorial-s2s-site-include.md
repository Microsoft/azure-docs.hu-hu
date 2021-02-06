---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbc4d73ab5faee89895f5bd06349dfe6bc8bfd5d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627707"
---
1. A virtuális WAN portál lapjának **kapcsolat** szakaszában válassza a **VPN-helyek** lehetőséget a VPN-helyek lap megnyitásához.
2. A **VPN-helyek** lapon kattintson a **+Hely létrehozása** elemre.

   ![A képernyőképen a hely – hely ablak jelenik meg, amelyen a VPN-hely létrehozása ablaktábla meg van nyitva.](./media/virtual-wan-tutorial-site-include/basics.png "Alapbeállítások")
3. A **VPN-hely létrehozása** lap **alapok** lapján végezze el a következő mezőket:

    * **Régió** – előzőleg helynek nevezzük. Ez az a hely, ahol létre szeretné hozni a hely erőforrását a alkalmazásban.
    * **Név** – a név, amellyel a helyszíni helyre hivatkozni kíván.
    * **Eszköz szállítója** – a VPN-eszköz gyártójának neve (például: Citrix, Cisco, Barracuda). Az eszköz gyártójának hozzáadása segítheti az Azure-csapatot abban, hogy jobban megértse a környezetét, hogy később további optimalizálási lehetőségeket lehessen felvenni, vagy segítséget nyújtson a hibák megoldásához.
    * **Border Gateway Protocol** – az **Engedélyezés** beállítás azt jelenti, hogy a hely összes kapcsolata BGP engedélyezve lesz. Végül beállítja a BGP-adatokat a VPN-helyről a hivatkozások szakaszban található minden hivatkozáshoz. A BGP virtuális WAN-on való konfigurálása egyenértékű a BGP Azure-beli virtuális hálózati átjáró VPN-en való konfigurálásával. A helyszíni BGP-társ címe nem lehet azonos a VPN által az eszközre vagy a VPN-hely VNet-címére vonatkozó nyilvános IP-címmel. Használjon egy másik IP-címet a VPN-eszközön a BGP-társ IP-címéhez. Ez lehet egy olyan cím is, amely az eszköz visszacsatolási hálózatához van rendelve. Ezt a címeket a helyet képviselő megfelelő VPN-helyen adhatja meg. A BGP-előfeltételeket lásd: [a BGP és az Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). A VPN-kapcsolatot bármikor szerkesztheti, hogy a BGP-paramétereket (a kapcsolaton és a AS #-ban) frissítse, ha a VPN-hely BGP-beállítása engedélyezve van.
    * **Magánhálózati címterület** – a helyszíni helyen található IP-címtartomány. Az erre a címtérre címzett forgalom át lesz irányítva a helyszíni helyre. Erre akkor van szükség, ha a BGP nincs engedélyezve a webhelyhez.
    
      >[!NOTE]
      >Ha a hely létrehozása után szerkeszti a címtartományt (például egy további címterület hozzáadása), akkor 8-10 percet is igénybe vehet, amíg az összetevők újból létrejönnek.
      >
    * **Hubok** – az a hub, amelyhez csatlakozni szeretne a helyhez. A helyek csak olyan hubokhoz csatlakoztathatók, amelyeken VPN Gateway található. Ha nem látja a hubot, először hozzon létre egy VPN-átjárót az adott központban.
4. Válassza a **hivatkozások** lehetőséget az ág fizikai hivatkozásaival kapcsolatos információk hozzáadásához. Ha egy virtuális WAN-partner CPE-eszközzel rendelkezik, egyeztessen velük, hogy az információk az Azure-ral együtt, a saját rendszeréből való feltöltésük részeként legyenek-e kicserélve.

   ![A képernyőképen a VPN-hely létrehozása panel látható, ahol a hivatkozások lap van kiválasztva.](./media/virtual-wan-tutorial-site-include/links.png "Hivatkozások")

    * **Hivatkozás neve** – a VPN-helyen található fizikai hivatkozáshoz megadni kívánt név. Példa: mylink1.
    * **Szolgáltató neve** : a VPN-helyen található fizikai hivatkozás neve. Példa: ATT, Verizon.
    * **Sebesség** – ez a VPN-eszköz sebessége a fiókiroda helyén. Például: 50, ami azt jelenti, hogy 50 Mbps a VPN-eszköz sebessége a fiókirodában.
    * **IP-cím/FQDN** – a helyszíni eszköz nyilvános IP-címe ezen a hivatkozáson keresztül. Opcionálisan megadhatja a helyszíni VPN-eszköz magánhálózati IP-címét, amely a ExpressRoute mögött található. A teljes tartománynevet is megadhatja. Például: *Something.contoso.com*. A teljes tartománynév feloldható a VPN-átjáróról. Ez akkor lehetséges, ha a teljes tartománynevet üzemeltető DNS-kiszolgáló elérhető az interneten keresztül. Az IP-cím elsőbbséget élvez, ha az IP-cím és a teljes tartománynév is meg van adva.

      >[!NOTE]
      >
      >* A teljes tartományneven egy IPv4-t támogat. Ha a teljes tartománynevet több IP-címhez szeretné feloldani, akkor a VPN-átjáró az első IP4-címet felveszi a listából. Az IPv6-címek jelenleg nem támogatottak.
      >
      >* A VPN-átjáró olyan DNS-gyorsítótárat tart fenn, amely 5 percenként frissül. Az átjáró csak a leválasztott alagutak teljes tartománynevét próbálja meg feloldani. Az átjáró alaphelyzetbe állítása vagy A konfiguráció módosítása A teljes tartománynév feloldását is elindíthatja.
      >
5. A jelölőnégyzet segítségével további hivatkozásokat is törölhet, illetve hozzáadhat hozzájuk. VPN-helyek esetében négy hivatkozás támogatott. Ha például négy internetszolgáltatója van (internetszolgáltató) a fiókirodában, négy hivatkozást hozhat létre, amelyek mindegyike egy-egy INTERNETSZOLGÁLTATÓnál létrehozható, és az egyes hivatkozások adatai megadhatók.
6. Miután befejezte a mezők kitöltését, válassza a **felülvizsgálat + létrehozás** lehetőséget a hely ellenőrzéséhez és létrehozásához.
7. Tekintse meg az állapotot a VPN-helyek lapon. A hely **kapcsolódásra kerül a kapcsolathoz** , mert a hely még nem csatlakozott az elosztóhoz.
