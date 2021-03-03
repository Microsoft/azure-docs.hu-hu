---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: b5c0bdbb29af7b8894d86233520ff09854faa201
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732487"
---
1. Keresse meg az **összes erőforrást** , és válassza ki a létrehozott virtuális WAN-t, majd a bal oldali menüben válassza a **felhasználói VPN-konfigurációk** lehetőséget.
1. A **felhasználó VPN-konfigurációk** lapon válassza a **+ felhasználói VPN-konfiguráció létrehozása** lehetőséget az oldal tetején az **új felhasználói VPN-konfiguráció létrehozása** lap megnyitásához.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Képernyőkép a felhasználói VPN-konfigurációk oldalról.":::

1. Az **alapvető** beállítások lap **példány részletei** területén adja meg a VPN-konfigurációhoz hozzárendelni kívánt **nevet** .
1. Az **alagút típusa** beállításnál válassza ki a kívánt bújtatási típust a legördülő listából. Az alagút típusának beállításai a következők: **IKEV2 VPN**, **OpenVPN**, és **OpenVPN és IKEv2**.
1. Használja az alábbi lépéseket, amelyek megfelelnek a kiválasztott bújtatási típusnak. Az összes érték megadása után kattintson a **felülvizsgálat + létrehozás**, majd a **Létrehozás** elemre a konfiguráció létrehozásához.

   **IKEv2 VPN**

   * **Követelmények:** Ha kiválasztja a **IKEv2** alagút típusát, egy üzenet jelenik meg, amely egy hitelesítési módszer kiválasztására utasítja. A IKEv2 csak egy hitelesítési módszert adhat meg. Az Azure-tanúsítvány, a Azure Active Directory vagy a RADIUS-alapú hitelesítés lehetőség közül választhat.
 
   * **Egyéni IPSec-paraméterek:** Ha testre szeretné szabni az 1. és az IKE 2. fázisának paramétereit, állítsa be az IPsec-kapcsolót az **Egyéni** értékre, és válassza ki a paramétereket. A testre szabható paraméterekkel kapcsolatos további információkért tekintse meg az [Egyéni IPsec-](../articles/virtual-wan/point-to-site-ipsec.md) cikket.

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Képernyőkép az IPsec-váltásról az egyénire.":::

   * **Hitelesítés:** Navigáljon a használni kívánt hitelesítési mechanizmusra a lap alján lévő **tovább** gombra kattintva a hitelesítési módszerre való továbblépés előtt, vagy kattintson a lap tetején található megfelelő lapra. Váltson át az **Igen** értékre a metódus kiválasztásához.

     Ebben a példában a RADIUS-hitelesítés van kiválasztva. A RADIUS-alapú hitelesítéshez másodlagos RADIUS-kiszolgáló IP-címét és a kiszolgáló titkos kulcsát is megadhatja.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Képernyőkép az IKE-ról.":::

   **OpenVPN**

   * **Követelmények:** Az **OpenVPN** -alagút típusának kiválasztásakor megjelenik egy üzenet, amely egy hitelesítési mechanizmus kiválasztására irányítja át. Ha az OpenVPN van kiválasztva az alagút típusaként, több hitelesítési módszert is megadhat. Kiválaszthatja az Azure-tanúsítvány, a Azure Active Directory vagy a RADIUS-alapú hitelesítés bármely részhalmazát. A RADIUS-alapú hitelesítéshez másodlagos RADIUS-kiszolgáló IP-címét és a kiszolgáló titkos kulcsát is megadhatja.

   * **Hitelesítés:** Navigáljon a használni kívánt hitelesítési módszer (ek) re a lap alján található **tovább** gombra kattintva a hitelesítési módszerre, vagy kattintson a lap tetején található megfelelő lapra.
   A kijelölni kívánt összes módszernél váltson át az **Igen** értékre, és adja meg a megfelelő értékeket.

     Ebben a példában Azure Active Directory van kiválasztva.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Képernyőfelvétel az OpenVPN-oldalról.":::
