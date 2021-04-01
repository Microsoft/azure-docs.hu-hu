---
title: Virtuális hálózati átjáró összekötése egy Azure-beli virtuális WAN-kapcsolattal
description: Ez a cikk segítséget nyújt az Azure-beli virtuális hálózati átjárók Azure-beli virtuális WAN VPN-átjáróhoz való összekapcsolásához
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 469d7ba9e86751312ebf6a6c82b35f065ee6cb50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880372"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>VPN Gateway (virtuális hálózati átjáró) összekötése virtuális WAN-kapcsolattal

Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure-VPN Gateway (virtuális hálózati átjáró) egy Azure-beli virtuális WAN-ra (VPN-átjáróra) való kapcsolódást. Egy VPN Gateway (virtuális hálózati átjáró) és egy virtuális WAN (VPN Gateway) közötti kapcsolat létrehozása hasonló ahhoz, hogy a virtuális WAN-hoz való kapcsolódást a fiókirodai VPN-helyekről hozza létre.

A két funkció közötti esetleges félreértések csökkentése érdekében az átjárót a szolgáltatásra hivatkozó funkció nevével fogjuk megtekinteni. Például VPN Gateway Virtual Network Gateway és a Virtual WAN VPN Gateway.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, hozza létre a következő erőforrásokat:

Azure Virtual WAN

* [Hozzon létre egy virtuális WAN](virtual-wan-site-to-site-portal.md#openvwan)-t.
* [Hozzon létre egy hubot](virtual-wan-site-to-site-portal.md#hub). A virtuális központ tartalmazza a virtuális WAN VPN-átjárót.

Azure Virtual Network

* Virtuális hálózat létrehozása virtuális hálózati átjárók nélkül. Győződjön meg arról, hogy a helyszíni hálózatok egyik alhálózata sem fedi át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Ha virtuális hálózatot szeretne létrehozni a Azure Portalban, tekintse meg a rövid [útmutatót.](../virtual-network/quick-create-portal.md)

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. VPN Gateway virtuális hálózati átjáró létrehozása

Hozzon létre egy **VPN Gateway** virtuális hálózati átjárót aktív-aktív módban a virtuális hálózat számára. Az átjáró létrehozásakor használhat meglévő nyilvános IP-címeket az átjáró két példányához, vagy új nyilvános IP-címeket is létrehozhat. Ezeket a nyilvános IP-címeket a virtuális WAN-helyek beállításakor fogja használni. További információ az aktív-aktív VPN-átjárókkal és a konfigurációs lépésekkel kapcsolatban: [Active-Active VPN Gateway-átjárók konfigurálása](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Aktív-aktív üzemmód beállítása

A virtuális hálózati átjáró **konfigurációja** lapon engedélyezze az aktív-aktív üzemmódot.

![aktív – aktív](./media/connect-virtual-network-gateway-vwan/active.png "aktív-aktív")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP-beállítás

A virtuális hálózati átjáró **konfigurálása** lapon beállíthatja a **BGP ASN**-t. Módosítsa a BGP ASN-t. A BGP ASN nem lehet 65515. a 65515-et az Azure Virtual WAN fogja használni.

![Képernyőfelvétel: a virtuális hálózati átjáró konfigurációs lapja, amelyen be van jelölve a BGP ASN konfigurálása.](./media/connect-virtual-network-gateway-vwan/bgp.png "BGP")

### <a name="public-ip-addresses"></a><a name="pip"></a>Nyilvános IP-címek

Az átjáró létrehozásakor navigáljon a **Tulajdonságok** lapra. A tulajdonságok és a konfigurációs beállítások az alábbi példához hasonlóak lesznek. Figyelje meg, hogy az átjáróhoz milyen két nyilvános IP-cím van használatban.

![Tulajdonságok](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. virtuális WAN VPN-helyek létrehozása

Virtuális WAN VPN-helyek létrehozásához navigáljon a virtuális WAN-hoz, és a **kapcsolat** területen válassza a **VPN-helyek** lehetőséget. Ebben a szakaszban két virtuális WAN VPN-helyet fog létrehozni, amelyek megfelelnek az előző szakaszban létrehozott virtuális hálózati átjáróknak.

1. Válassza a **+ hely létrehozása** lehetőséget.
2. A **VPN-helyek létrehozása** lapon írja be a következő értékeket:

   * **Régió** – az Azure VPN Gateway Virtual Network átjáróval megegyező régió.
   * **Eszköz szállítója** – adja meg az eszköz gyártóját (bármilyen név).
   * **Magánhálózati címtartomány** – adjon meg egy értéket, vagy hagyja üresen, ha a BGP engedélyezve van.
   * **Border Gateway Protocol** – beállíthatja **, hogy** az Azure VPN Gateway virtuális hálózati átjárón engedélyezve van-e a BGP.
   * **Kapcsolódás hubokhoz** – a legördülő listából válassza ki az előfeltételekben létrehozott hubot. Ha nem lát hubot, ellenőrizze, hogy létrehozott-e helyek közötti VPN-átjárót a hubhoz.
3. A **hivatkozások** területen adja meg a következő értékeket:

   * **Szolgáltató neve** – adja meg a hivatkozás nevét és a szolgáltató nevét (bármilyen név).
   * **Sebesség** (tetszőleges szám).
   * **IP-cím** – adja meg az IP-címet (ugyanaz, mint az első nyilvános IP-cím, amely a (VPN Gateway) virtuális hálózati átjáró tulajdonságainál látható).
   * **BGP-címe** és **ASN** -BGP-címe és ASN. Ezeknek meg kell egyezniük a BGP-társ IP-címeinek egyikével, és az [1. lépésben](#vnetgw)konfigurált VPN Gateway virtuális hálózati átjáró ASN-jét.
4. Tekintse át, majd válassza a **jóváhagyás** lehetőséget a hely létrehozásához.
5. Az előző lépések megismétlésével hozza létre a második helyet, hogy egyezzen a VPN Gateway virtuális hálózati átjáró második példányával. Ugyanezeket a beállításokat kell megtartania, kivéve a második nyilvános IP-cím és második BGP-társ IP-cím használatát VPN Gateway konfigurációból.
6. Most már két hely sikeresen kiépítve, és a következő szakaszba léphet a konfigurációs fájlok letöltéséhez.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. a VPN-konfigurációs fájlok letöltése

Ebben a szakaszban az előző szakaszban létrehozott összes helyhez letölti a VPN-konfigurációs fájlt.

1. A virtuális WAN **VPN-helyek** lap tetején válassza ki a **helyet**, majd válassza a **helyek közötti VPN-konfiguráció letöltése** lehetőséget. Az Azure konfigurációs fájlt hoz létre a beállításokkal.

   ![Képernyőfelvétel: a "VPN-helyek" lap "a helyek közötti VPN-konfiguráció letöltése" művelet van kiválasztva.](./media/connect-virtual-network-gateway-vwan/download.png "letöltés")
2. Töltse le és nyissa meg a konfigurációs fájlt.
3. Ismételje meg ezeket a lépéseket a második helyen. Ha mindkét konfigurációs fájl meg van nyitva, folytassa a következő szakasszal.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. a helyi hálózati átjárók létrehozása

Ebben a szakaszban két Azure VPN Gateway helyi hálózati átjárót hoz létre. Az előző lépésből származó konfigurációs fájlok tartalmazzák az átjáró konfigurációs beállításait. Ezekkel a beállításokkal hozhatja létre és konfigurálhatja az Azure VPN Gateway helyi hálózati átjárókat.

1. Hozza létre a helyi hálózati átjárót ezekkel a beállításokkal. VPN Gateway helyi hálózati átjáró létrehozásával kapcsolatos további információkért tekintse meg a [helyi hálózati átjáró létrehozása](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway)című cikket VPN Gateway.

   * **IP-cím** – használja a *gatewayconfiguration* számára megjelenített Instance0 IP-címet a konfigurációs fájlból.
   * **BGP** – ha a kapcsolat meghaladja a BGP-t, válassza a **BGP-beállítások konfigurálása** lehetőséget, és adja meg az ASN "65515" értéket. Adja meg a BGP-társ IP-címét. Használja az "Instance0 BgpPeeringAddresses" beállítást a *gatewayconfiguration* a konfigurációs fájlból.
   * Az **előfizetés, az erőforráscsoport és a hely** megegyezik a virtuális WAN-hubhoz.
2. Tekintse át és hozza létre a helyi hálózati átjárót. A helyi hálózati átjárónak ehhez a példához hasonlóan kell kinéznie.

   ![Képernyőkép: a "Configuration" (konfiguráció) oldal, amely kiemelt IP-címmel és a "BGP-beállítások konfigurálása" beállítással jelenik meg.](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Ismételje meg ezeket a lépéseket egy másik helyi hálózati átjáró létrehozásához, de ezúttal használja a "Peldany1" elemet "értékeket a konfigurációs fájl" Instance0 "értékei helyett.

   ![konfigurációs fájl letöltése](./media/connect-virtual-network-gateway-vwan/lng2.png "peldany1 "elemet")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. kapcsolatok létrehozása

Ebben a szakaszban kapcsolatot hoz létre a VPN Gateway helyi hálózati átjárók és a virtuális hálózati átjáró között. VPN Gateway kapcsolatok létrehozásával kapcsolatos lépésekért lásd: [Kapcsolatok konfigurálása](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection).

1. A portálon navigáljon a virtuális hálózati átjáróhoz, és kattintson a **kapcsolatok** elemre. A Kapcsolatok lapon kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** lap megnyitásához.
2. A **kapcsolatok hozzáadása** lapon adja meg a következő értékeket a kapcsolatban:

   * **Név:** Nevezze el a kapcsolatot.
   * **Kapcsolattípus:** **Helyek közötti (IPSec)** kijelölése
   * **Virtuális hálózati átjáró:** Az értéke rögzített, mivel erről az átjáróról csatlakozik.
   * **Helyi hálózati átjáró:** Ez a kapcsolat csatlakoztatja a virtuális hálózati átjárót a helyi hálózati átjáróhoz. Válassza ki a korábban létrehozott helyi hálózati átjárók egyikét.
   * **Megosztott kulcs:** Adjon meg egy megosztott kulcsot.
   * **IKE protokoll:** Válassza ki az IKE protokollt.
3. A kapcsolat létrehozásához kattintson az **OK** gombra.
4. A kapcsolatot a virtuális hálózat átjárójának **Kapcsolatok** lapján tekintheti meg.

   ![Kapcsolat](./media/connect-virtual-network-gateway-vwan/connect.png "kapcsolat")
5. Egy második kapcsolódás létrehozásához ismételje meg a fenti lépéseket. A második kapcsolatok esetében válassza ki a létrehozott másik helyi hálózati átjárót.
6. Ha a kapcsolatok a BGP-kapcsolaton keresztül jönnek létre, a kapcsolatok létrehozása után navigáljon a kapcsolathoz, és válassza a **konfiguráció** lehetőséget. A **konfiguráció** lapon a **BGP** beállításnál válassza az **engedélyezve** lehetőséget. Ezután kattintson a **Mentés** gombra. Ismételje meg a műveletet a második kapcsolatban.

## <a name="6-test-connections"></a><a name="test"></a>6. a kapcsolatok tesztelése

A kapcsolat teszteléséhez hozzon létre két virtuális gépet, egyet a VPN Gateway virtuális hálózati átjáró oldalán, és egy virtuális hálózatot a virtuális WAN számára, majd Pingelje a két virtuális gépet.

1. Hozzon létre egy virtuális gépet a virtuális hálózaton (Test1-VNet) az Azure VPN Gatewayhoz (Test1-VNG). Ne hozza létre a virtuális gépet a GatewaySubnet.
2. Hozzon létre egy másik virtuális hálózatot a virtuális WAN-hoz való kapcsolódáshoz. Hozzon létre egy virtuális gépet a virtuális hálózat alhálózatában. Ez a virtuális hálózat nem tartalmazhat virtuális hálózati átjárókat. A [helyek közötti kapcsolat](virtual-wan-site-to-site-portal.md#vnet) cikkben található PowerShell-lépésekkel gyorsan létrehozhat egy virtuális hálózatot. Ügyeljen arra, hogy a parancsmagok futtatása előtt módosítsa az értékeket.
3. Csatlakoztatja a VNet a virtuális WAN-hubhoz. A virtuális WAN lapján válassza a **virtuális hálózati kapcsolatok**, majd a **kapcsolat hozzáadása** elemet. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A virtuális hálózati kapcsolatok létrehozásához kattintson **az OK** gombra.
5. A kapcsolat már be van állítva a virtuális gépek között. Képesnek kell lennie arra, hogy egy virtuális gépet Pingelje a másikból, kivéve, ha vannak olyan tűzfalak vagy más szabályzatok, amelyek blokkolja a kommunikációt.

## <a name="next-steps"></a>Következő lépések

Az Egyéni IPsec-házirend konfigurálásának lépéseiért lásd: [Egyéni IPsec-házirend konfigurálása a virtuális WAN-](virtual-wan-custom-ipsec-portal.md)hoz.
További információ a virtuális WAN-ról: az [Azure Virtual WAN](virtual-wan-about.md) és az [Azure Virtual WAN gyakori kérdései](virtual-wan-faq.md).