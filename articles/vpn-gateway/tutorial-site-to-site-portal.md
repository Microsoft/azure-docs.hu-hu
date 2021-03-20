---
title: 'Oktatóanyag – helyszíni hálózat összekapcsolása virtuális hálózattal: Azure Portal'
description: Hozzon létre egy helyek közötti VPN Gateway IPsec-kapcsolatot a helyszíni hálózatról egy Azure-beli virtuális hálózatra a nyilvános interneten keresztül a portál használatával.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945209"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Oktatóanyag: helyek közötti kapcsolat létrehozása a Azure Portalban

Az Azure VPN-átjárók helyszínek közötti kapcsolatot biztosítanak az ügyfél helyszínei és az Azure között. Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Portal egy helyek közötti VPN Gateway-kapcsolat létrehozásához a helyszíni hálózatról a VNet. Ezt a konfigurációt [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) vagy az [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)használatával is létrehozhatja.

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram":::

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * VPN-átjáró létrehozása
> * Helyi hálózati átjáró létrehozása
> * VPN-kapcsolat létrehozása
> * A kapcsolat ellenőrzése
> * Csatlakozás virtuális géphez

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. Ha még nem rendelkezik ilyennel, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Győződjön meg arról, hogy rendelkezésre áll egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot (VNet) a következő értékek használatával:

* **Erőforráscsoport:** TestRG1
* **Név:** VNet1
* **Régió:** (USA) USA keleti régiója
* **IPv4-címterület:** 10.1.0.0/16
* **Alhálózat neve:** FrontEnd
* **Alhálózat címterület:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>VPN-átjáró létrehozása

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

### <a name="about-the-gateway-subnet"></a>Az átjáró alhálózatának ismertetése

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Az átjáró létrehozása

Hozzon létre egy VPN-átjárót a következő értékek használatával:

* **Név:** VNet1GW
* **Régió:** USA keleti régiója
* **Átjáró típusa:** VPN
* **VPN típusa:** útvonalalapú
* **SKU:** VpnGw1
* **Generáció:** Generation1
* **Virtuális hálózat:** VNet1
* **Átjáró-alhálózati címtartomány:** 10.1.255.0/27
* **Nyilvános IP-cím:** Új létrehozása
* **Nyilvános IP-cím neve:** VNet1GWpip
* **Aktív-aktív üzemmód engedélyezése:** Tiltva
* A **BGP konfigurálása:** Tiltva

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>A nyilvános IP-cím megtekintése

Az átjáró nyilvános IP-címét az átjáró **Áttekintés** oldalán tekintheti meg.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Áttekintő oldal":::

Ha további információkat szeretne látni a nyilvános IP-cím objektumról, kattintson a **nyilvános IP-cím** melletti név/IP-cím hivatkozásra.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Helyi hálózati átjáró létrehozása

A helyi hálózati átjáró egy adott objektum, amely útválasztás céljából a helyszíni helyet (a helyet) jelöli. Olyan nevet adjon a helynek, amellyel az Azure hivatkozhat rá, majd határozza meg annak a helyszíni VPN-eszköznek az IP-címét, amellyel létre kívánja hozni a kapcsolatot. Emellett megadhatja azokat az IP-címelőtagokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok. Ha a helyszíni hálózat megváltozik, vagy módosítania kell a VPN-eszköz nyilvános IP-címét, könnyen frissítheti az értékeket később.

Hozzon létre egy helyi hálózati átjárót a következő értékek használatával:

* **Név:** Hely1
* **Erőforráscsoport:** TestRG1
* **Hely:** USA keleti régiója

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>VPN-eszköz konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben a VPN-eszköz konfigurálása következik. A VPN-eszköz konfigurálásakor a következő értékeket kell megadnia:

* Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
* A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A VPN-átjáró nyilvános IP-címének a Azure Portal használatával történő megkereséséhez navigáljon a **virtuális hálózati átjárók** elemre, majd válassza ki az átjáró nevét.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>VPN-kapcsolat létrehozása

Hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a helyszíni VPN-eszköz között.

Hozzon létre egy kapcsolatokat a következő értékek használatával:

* **Helyi hálózati átjáró neve:** Hely1
* **Kapcsolatok neve:** VNet1toSite1
* **Megosztott kulcs:** Ebben a példában az abc123 kulcsot használjuk. Használhat azonban bármi mást is, ami kompatibilis a VPN-hardverrel. A lényeg az, hogy az értékek azonosak legyenek a kapcsolat két oldalán.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>A VPN-kapcsolat ellenőrzése

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>Nem kötelező lépések

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>További kapcsolatok hozzáadása az átjáróhoz

Hozzáadhat további kapcsolatokat, feltéve, hogy a címterek nincsenek egymással átfedésben a kapcsolatok között.

1. További kapcsolat hozzáadásához nyissa meg a VPN-átjárót, majd válassza a **kapcsolatok** lehetőséget a kapcsolatok lap megnyitásához.
1. Válassza a **+ Hozzáadás** lehetőséget a kapcsolódás hozzáadásához. Adja a kapcsolat típusát virtuális hálózatok közötti kapcsolatként (ha egy másik VNet-átjáróhoz kapcsolódik) vagy helyek közötti kapcsolatként.
1. Ha a helyek közötti beállítással kapcsolódik, és még nem hozott létre helyi hálózati átjárót ahhoz a helyhez, amelyhez csatlakozni szeretne, létrehozhat egy újat.
1. Adja meg a használni kívánt megosztott kulcsot, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Átjáró SKU-jának átméretezése

Az átméretezésre és az átjáró SKU-jának módosítására vonatkozó konkrét szabályok vonatkoznak. Ebben a szakaszban átméretezi az SKU-t. További információ: [átjáró beállításai – az SKU-i átméretezése és módosítása](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Átjáró alaphelyzetbe állítása

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>További konfigurációs megfontolások

A S2S-konfigurációk többféleképpen testreszabhatók. További információért tekintse át a következő cikkeket:

* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).
* További információ a kényszerített bújtatásról: [Tudnivalók a kényszerített](vpn-gateway-forced-tunneling-rm.md)bújtatásról.
* Információk a magas rendelkezésre állású aktív-aktív kapcsolatokról: [Magas rendelkezésre állású kapcsolatok létesítmények, illetve virtuális hálózatok között](vpn-gateway-highlyavailable.md).
* Információ egy virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom korlátozásáról: [Hálózati biztonság](../virtual-network/network-security-groups-overview.md).
* Információ arról, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását: [Virtuális hálózat forgalmának útválasztása](../virtual-network/virtual-networks-udr-overview.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, vagy folytassa a következő oktatóanyaggal, törölje ezeket az erőforrásokat a következő lépésekkel:

1. Adja meg az erőforráscsoport nevét a portál tetején található **keresőmezőbe** , és válassza ki a keresési eredmények közül.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Adja meg az erőforráscsoport **nevét, írja be az erőforráscsoport nevét** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A S2S-kapcsolatok konfigurálása után P2S-kapcsolatokat adhat hozzá ugyanahhoz az átjáróhoz.

> [!div class="nextstepaction"]
> [Pont – hely típusú VPN-kapcsolatok](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
