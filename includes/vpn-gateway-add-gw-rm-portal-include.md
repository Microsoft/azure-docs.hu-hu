---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fe95481e977d2e35c2f652b6c99b9e69ae89e2f8
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2020
ms.locfileid: "92479575"
---
1. A [Azure Portal](https://portal.azure.com)a **Search Resources, Services és docs (G +/)** Type **Virtual Network Gateway** . Keresse meg a **virtuális hálózati átjárót** a keresési eredmények között, és jelölje ki.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="Keresőmező" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. A **virtuális hálózati átjáró** lapon válassza a **+ Hozzáadás** lehetőséget. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** lapot.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="Keresőmező":::
1. Az **alapvető beállítások** lapon adja meg a virtuális hálózati átjáró értékeit.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="Keresőmező":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="Keresőmező":::

   * **Előfizetés** : válassza ki a legördülő listából használni kívánt előfizetést.
   * **Erőforráscsoport** : ezt a beállítást a rendszer a virtuális hálózat ezen a lapon való kiválasztásakor kitölti.

   **Példány adatai**

   * **Név** : adjon nevet az átjárónak. Az átjáró nem egyezhet meg az átjáró alhálózatának elnevezésével. Ez a létrehozni kívánt átjáróobjektum neve.
   * **Régió** : válassza ki azt a régiót, amelyben létre kívánja hozni ezt az erőforrást. Az átjáró régiójának meg kell egyeznie a virtuális hálózattal.
   * **Átjáró típusa** : válassza ki a **VPN** elemet. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják.
   * **VPN típusa** : válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
   * **SKU** : válassza ki az átjáró SKU-ját a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).
   * **Létrehozás** : VPN Gateway létrehozásával kapcsolatos információkért lásd: [átjáró SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)-i.
   * **Virtuális hálózat** : a legördülő listából válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót.
   * **Átjáró alhálózatának** címtartomány: Ez a mező csak akkor jelenik meg, ha a VNet nem rendelkezik átjáró-alhálózattal. Ha lehetséges, végezze el a tartományt/27 vagy nagyobb (/26,/25 stb.). Nem javasoljuk, hogy a/28-nál kisebb tartományt hozzon létre. Ha már rendelkezik átjáró-alhálózattal, a virtuális hálózatra navigálva megtekintheti a GatewaySubnet adatait. Az **alhálózatok** elemre kattintva megtekintheti a tartományt. Ha módosítani szeretné a tartományt, törölheti és újból létrehozhatja a GatewaySubnet.

   **Nyilvános IP-cím**

   Ezzel a beállítással adható meg a VPN-átjáróhoz társított nyilvános IP-cím objektum. A nyilvános IP-címet a rendszer dinamikusan rendeli hozzá ehhez az objektumhoz a VPN-átjáró létrehozásakor. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

     * **Nyilvános IP-cím** : hagyja a **Create New (új** ) elemet.
     * **Nyilvános IP-cím neve** : a szövegmezőbe írja be a nyilvános IP-cím példányának nevét.
     * **Hozzárendelés** : a VPN-átjáró csak dinamikus használatát támogatja.
     * **Aktív-aktív üzemmód engedélyezése** : csak akkor válassza az aktív **-aktív mód engedélyezése** lehetőséget, ha aktív-aktív átjáró-konfigurációt hoz létre. Ellenkező esetben hagyja **Letiltva** ezt a beállítást.
     * Hagyja **Letiltva** a **BGP konfigurálását** , kivéve, ha a konfiguráció kifejezetten ehhez a beállításhoz szükséges. Ha szükség van a beállításra, az ASN alapértelmezett értéke 65515 lesz, de ez módosítható.
1. Válassza az **ellenőrzés + létrehozás** lehetőséget az érvényesítés futtatásához.
1. Az érvényesítést követően a **Létrehozás** gombra kattintva telepítheti a VPN-átjárót. Az átjárók teljes létrehozása és üzembe helyezése akár 45 percet is igénybe vehet. A telepítési állapotot az átjáró áttekintés lapján tekintheti meg.

Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni.
