---
title: 'Csatlakozás VNet P2S VPN & több hitelesítési típus használatával: portál'
titleSuffix: Azure VPN Gateway
description: Kapcsolódjon egy VNet a P2S-n keresztül a Azure Portal több hitelesítési típus használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745688"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Pont – hely VPN-kapcsolat konfigurálása egy VNet több hitelesítési típus használatával: Azure Portal

Ez a cikk segítséget nyújt a Windows, Linux vagy macOS rendszerű ügyfelek biztonságos csatlakoztatásához egy Azure-VNet. A pont–hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról. Pont–hely kapcsolatot is használhat helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. Pont–hely kapcsolat esetén SSTP (Secure Socket Tunneling Protocol) vagy IKEv2-protokoll használatával jön létre a VPN-kapcsolat. További információkat a pont–hely VPN-ről a [pont–hely VPN-t ismertető](point-to-site-about.md) témakör tartalmaz.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Kapcsolódás számítógépről egy Azure VNet-pont – hely kapcsolati diagramhoz":::

További információ a pont – hely VPN-ről: [Tudnivalók a pont – hely VPN-ről](point-to-site-about.md). Ha a Azure PowerShell használatával szeretné létrehozni ezt a konfigurációt, tekintse meg [a pont – hely típusú VPN konfigurálása Azure PowerShell használatával](vpn-gateway-howto-point-to-site-rm-ps.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

Ugyanazon a VPN-átjárón több hitelesítési típust csak az OpenVPN-alagút típusa támogatott.

### <a name="example-values"></a><a name="example"></a>Példaértékek

Az alábbi értékek használatával létrehozhat egy tesztkörnyezetet, vagy segítségükkel értelmezheti a cikkben szereplő példákat:

* **Virtuális hálózat neve:** VNet1
* **Címterület:** 10.1.0.0/16<br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
* **Alhálózat neve:** FrontEnd
* **Alhálózati címtartomány:** 10.1.0.0/24
* **Előfizetés:** Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport:** TestRG1
* **Hely:** USA keleti régiója
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Virtuális hálózati átjáró neve:** VNet1GW
* **Átjáró típusa:** VPN
* **VPN típusa:** útvonalalapú
* **Nyilvános IP-cím neve:** VNet1GWpip
* **Kapcsolat típusa:** pont–hely
* **Ügyfél címkészlet:** 172.16.201.0/24<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, az ügyfélcímkészletből kapnak IP-címet.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Virtuális hálózat létrehozása

Mielőtt elkezdi végrehajtani a lépéseket, győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Virtuális hálózati átjáró

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

>[!NOTE]
>Az alapszintű átjáró SKU nem támogatja az OpenVPN-alagút típusát.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Ügyfélcímkészlet

Az ügyfélcímkészlet megadott magánhálózati IP-címek tartománya. A pont–hely VPN-kapcsolattal csatlakozó ügyfelek ebből a tartományból kapnak dinamikusan IP-címet. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Ha több protokollt konfigurál, és az SSTP a protokollok egyike, akkor a konfigurált címkészlet egyenlően oszlik meg a konfigurált protokollok között.

1. Miután létrehozta a virtuális hálózati átjárót, navigáljon a virtuális hálózati átjáró lapjának **Beállítások** részéhez. A **Beállítások** területen válassza a **pont – hely konfiguráció** lehetőséget. Válassza a **Konfigurálás most** lehetőséget a konfigurációs lap megnyitásához.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Képernyőkép a pont – hely konfiguráció lapról." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. A **pont – hely konfiguráció** lapon többféle beállítást is beállíthat. A **címkészlet** mezőben adja meg a használni kívánt magánhálózati IP-címtartományt. A VPN-ügyfelek dinamikusan kapnak egy IP-címet a megadott tartományból. A minimális alhálózati maszk 29 bites aktív/passzív és 28 bites az aktív/aktív konfigurációhoz.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="A címkészlet képernyőképe.":::

1. Folytassa a következő szakasszal a hitelesítési és bújtatási típusok konfigurálásához.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Hitelesítési és bújtatási típusok

Ebben a szakaszban a hitelesítés típusát és a bújtatás típusát konfigurálja. Ha nem látja az **alagút típusát** vagy a **hitelesítési típust**, a **pont – hely konfiguráció** lapon az átjáró az alapszintű SKU-t használja. Az alapszintű termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítést. Ha ezeket a beállításokat szeretné használni, törölnie kell, majd újra létre kell hoznia az átjárót egy másik átjáró-SKU használatával.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="A hitelesítési típus képernyőképe.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Alagúttípus

A **pont – hely konfiguráció** lapon válassza az **OpenVPN (SSL)** lehetőséget az alagút típusaként.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Hitelesítéstípus

A **Hitelesítés típusa** mezőben válassza ki a kívánt típusokat. A lehetőségek a következők:

* Azure-tanúsítvány
* RADIUS
* Azure Active Directory

A kiválasztott hitelesítési típustól (k) függően különböző konfigurációs beállításokat fog látni, amelyeket ki kell töltenie. Adja meg a szükséges adatokat, majd a lap tetején található **Mentés** gombra kattintva mentse az összes konfigurációs beállítást.

A hitelesítési típussal kapcsolatos további információkért lásd:

* [Azure-tanúsítvány](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>VPN-ügyfél konfigurációs csomagja

A VPN-ügyfeleket ügyfél-konfigurációs beállításokkal kell konfigurálni. A VPN-ügyfél konfigurációs csomagja olyan fájlokat tartalmaz, amelyek a VPN-ügyfelek konfigurálására szolgáló beállításokat tartalmaznak, hogy P2S-kapcsolaton keresztül csatlakozzanak egy VNet.

A VPN-ügyfél konfigurációs fájljainak létrehozásával és telepítésével kapcsolatos utasításokért használja a konfigurációra vonatkozó cikket:

* [VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése a natív Azure tanúsítvány-hitelesítési P2S-konfigurációkhoz](point-to-site-vpn-client-configuration-azure-cert.md).
* [Azure Active Directory hitelesítés: konfigurálja a VPN-ügyfelet a P2S OpenVPN protokoll kapcsolataihoz](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

Ez a szakasz a pont – hely konfigurációkra vonatkozó GYIK-információkat tartalmaz. A VPN Gatewayról további információt a [VPN Gateway gyakori kérdések](vpn-gateway-vpn-faq.md) című témakörben talál.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Következő lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](../index.yml). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/network-overview.md).

A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.
