---
title: 'Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózaton a Azure Portal használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Firewallt a Azure Portal használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 86e27c190b269763d8dd2f562a207b3f2020da29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051071"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózaton a Azure Portal használatával

Ha a helyszíni hálózatot egy Azure-beli virtuális hálózathoz kapcsolja egy hibrid hálózat létrehozásához, az Azure hálózati erőforrásaihoz való hozzáférés szabályozása egy átfogó biztonsági csomag fontos részét képezi.

Az Azure Firewallal az engedélyezett és letiltott forgalmat meghatározó szabályok segítségével szabályozhatja egy hibrid hálózat hálózati elérését.

Ebben az oktatóanyagban három virtuális hálózatot fog létrehozni:

- **VNet-hub** – a tűzfal ebben a virtuális hálózatban található.
- **VNet – küllő** – a küllős virtuális hálózat az Azure-ban található számítási feladatokat jelöli.
- **VNet-helyszíni** – a helyszíni virtuális hálózat egy helyszíni hálózatot jelöl. Tényleges telepítés esetén VPN-vagy ExpressRoute-kapcsolat is csatlakoztatható. Az egyszerűség kedvéért ez az oktatóanyag egy VPN Gateway-kapcsolaton alapul, és egy Azure-beli virtuális hálózatot használ a helyszíni hálózat ábrázolására.

![Tűzfal a hibrid hálózatban](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Változók deklarálása
> * A tűzfal hub virtuális hálózatának létrehozása
> * A küllős virtuális hálózat létrehozása
> * Helyszíni virtuális hálózat létrehozása
> * A tűzfal konfigurálása és üzembe helyezése
> * A VPN-átjárók létrehozása és csatlakoztatása
> * A hub és a küllős virtuális hálózatok egyenrangúak
> * Az útvonalak létrehozása
> * A virtuális gépek létrehozása
> * A tűzfal tesztelése

Ha az eljárás elvégzése helyett a Azure PowerShellt szeretné használni, tekintse meg a [Azure Firewall telepítése és konfigurálása hibrid hálózaton Azure PowerShell használatával](tutorial-hybrid-ps.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A hibrid hálózatok a sugaras architektúrát használják az Azure-virtuális hálózatok és a helyszíni hálózatok közötti forgalom irányítására. A sugaras architektúra a következő követelményekkel rendelkezik:

- Állítsa be a **AllowGatewayTransit** , ha a VNet-Hub VNet beszél. Egy sugaras hálózati architektúrában az átjáró áttelepítése lehetővé teszi, hogy a küllős virtuális hálózatok megosszák a VPN-átjárót a központban a VPN-átjárók minden küllős virtuális hálózatban való üzembe helyezése helyett. 

   Emellett az átjáróhoz csatlakoztatott virtuális hálózatokhoz vagy a helyszíni hálózatokhoz tartozó útvonalak automatikusan a virtuális hálózatok útválasztási tábláiba lesznek propagálva az átjáró átirányítása használatával. További információ: [VPN Gateway-átvitel konfigurálása virtuális hálózatokhoz](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Állítsa be a **UseRemoteGateways** , amikor a társ VNet-Spoke az VNet-hub-ra. Ha a **UseRemoteGateways** be van állítva, és a távoli **AllowGatewayTransit** is be van állítva, a küllős virtuális hálózat a távoli virtuális hálózat átjáróit használja az átvitelhez.
- Ahhoz, hogy a küllős alhálózat forgalmát a központi tűzfalon keresztül irányítsa, használhat egy felhasználó által megadott útvonalat (UDR), amely arra a tűzfalra mutat, amelyen a **virtuális hálózati átjáró útvonal-propagálási** lehetősége le van tiltva. A **virtuális hálózati átjáró útvonal-propagálási** letiltott beállítása megakadályozza a küllős alhálózatok útvonal-terjesztését. Ezzel megelőzhető, hogy a megszerzett útvonalak ütköznek legyenek a UDR. Ha meg szeretné őrizni a **virtuális hálózati átjáró útvonalának propagálását** , akkor ügyeljen arra, hogy meghatározott útvonalakat határozzon meg a tűzfalon, hogy felülírja azokat, amelyek a helyszínen, a BGP-on közzétettek.
- Konfiguráljon egy UDR a központi átjáró alhálózatán, amely a tűzfal IP-címére mutat a küllő hálózatok következő ugrásakor. Nem szükséges UDR a Azure Firewall alhálózaton, mivel a BGP-ből tanulja meg az útvonalakat.

Az útvonalak létrehozásával kapcsolatos információkért lásd az oktatóanyag [Útvonalak létrehozása](#create-the-routes) című szakaszát.

>[!NOTE]
>Az Azure Firewallnak közvetlen internetkapcsolatra van szüksége. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat **fenntartása érdekében ezt** a 0.0.0.0/0 UDR kell felülbírálnia a **NextHopType** értékkel.
>
>A Azure Firewall konfigurálható úgy, hogy támogassa a kényszerített bújtatást. További információ: [Azure Firewall kényszerített bújtatás](forced-tunneling.md).

>[!NOTE]
>A közvetlenül összekapcsolt virtuális hálózatok közötti forgalom közvetlenül akkor is átirányítva van, ha egy UDR az alapértelmezett átjáróként való Azure Firewallre mutat. Ha ebben a forgatókönyvben az alhálózatot alhálózati forgalomra szeretné küldeni a tűzfalra, a UDR mindkét alhálózaton explicit módon tartalmaznia kell a célként megadott alhálózat hálózati előtagot.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-firewall-hub-virtual-network"></a>A tűzfal hub virtuális hálózatának létrehozása

Először hozza létre az oktatóanyaghoz tartozó erőforrásokat tartalmazó erőforráscsoportot:

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. A Azure Portal kezdőlapon válassza az **erőforráscsoportok**  >  **Hozzáadás** lehetőséget.
3. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport neve** mezőbe írja be a következőt: **FW-Hybrid-test**.
2. A régió területen válassza az USA **keleti** **régióját**. A későbbiekben létrehozott összes erőforrásnak ugyanazon a helyen kell lennie.
3. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
4. Válassza a **Létrehozás** lehetőséget.

Most hozza létre a VNet:

> [!NOTE]
> A AzureFirewallSubnet-alhálózat mérete/26. További információ az alhálózat méretétől: [Azure Firewall GYIK](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A **hálózat** területen válassza a **virtuális hálózat** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Az **erőforráscsoport** területen válassza az **FW-Hybrid-test** elemet.
1. A **név** mezőbe írja be a következőt: **VNet-hub**.
1. Válassza a **Tovább: IP-címek** lehetőséget.
1. **IPv4-címterület** esetén törölje az alapértelmezett címeket, és írja be a következőt: **10.5.0.0/16**.
1. Az **alhálózat neve** területen válassza az **alhálózat hozzáadása** elemet.
1. Az **alhálózat neveként** írja be a következőt: **AzureFirewallSubnet**. Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
1. **Alhálózati címtartomány** esetében írja be a következőt: **10.5.0.0/26**. 
1. Válassza a **Hozzáadás** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-the-spoke-virtual-network"></a>A küllős virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A **hálózatkezelés** területen válassza a **virtuális hálózat** lehetőséget.
7. Az **erőforráscsoport** területen válassza az **FW-Hybrid-test** elemet.
1. A **név** mezőbe írja be a **VNet-küllő** értéket.
2. A régió területen válassza az USA **keleti** **régióját**.
3. Válassza a **Tovább: IP-címek** lehetőséget.
4. **IPv4-címterület** esetén törölje az alapértelmezett címeket, és írja be a következőt: **10.6.0.0/16**.
6. Az **alhálózat neve** területen válassza az **alhálózat hozzáadása** elemet.
7. Az **alhálózat neveként** írja be a következőt: **SN-munkaterhelés**.
8. **Alhálózati címtartomány** esetében írja be a következőt: **10.6.0.0/24**. 
9. Válassza a **Hozzáadás** lehetőséget.
10. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
11. Válassza a **Létrehozás** lehetőséget.

## <a name="create-the-on-premises-virtual-network"></a>Helyszíni virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A **hálózatkezelés** területen válassza a **virtuális hálózat** lehetőséget.
7. Az **erőforráscsoport** területen válassza az **FW-Hybrid-test** elemet.
1. A **név** mezőbe írja be a következőt: **VNet-helyszíni**.
2. A régió területen válassza az USA **keleti** **régióját**.
3. Válassza a Next (tovább) lehetőséget **: IP-címek**
4. **IPv4-címterület** esetén törölje az alapértelmezett címeket, és írja be a következőt: **192.168.0.0/16**.
5. Az **alhálózat neve** területen válassza az **alhálózat hozzáadása** elemet.
7. Az **alhálózat neveként** írja be a következőt: **SN-Corp**.
8. **Alhálózati címtartomány** esetében írja be a következőt: **192.168.1.0/24**. 
9. Válassza a **Hozzáadás** lehetőséget.
10. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
11. Válassza a **Létrehozás** lehetőséget.

Most hozzon létre egy második alhálózatot az átjáróhoz.

1. Az **VNet-helyszíni** lapon válassza az **alhálózatok** lehetőséget.
2. Válassza a **+ alhálózat** lehetőséget.
3. A **név** mezőbe írja be a következőt: **GatewaySubnet**.
4. Az **alhálózati címtartomány** típusa **192.168.2.0/24**.
5. Válassza az **OK** lehetőséget.

## <a name="configure-and-deploy-the-firewall"></a>A tűzfal konfigurálása és üzembe helyezése

Most telepítse a tűzfalat a tűzfal hub virtuális hálózatára.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A bal oldali oszlopban válassza a **hálózatkezelés** lehetőséget, és keresse meg, majd válassza a **tűzfal** lehetőséget.
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Előfizetés     |\<your subscription\>|
   |Erőforráscsoport     |**FW-Hybrid-test** |
   |Name     |**AzFW01**|
   |Region     |**USA keleti régiója**|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**:<br> **VNet – központ**|
   |Nyilvános IP-cím     |Új hozzáadása: <br>**FW – pip**. |

5. Válassza az **Áttekintés + létrehozás** lehetőséget.
6. Tekintse át az összegzést, majd válassza a **Létrehozás** lehetőséget a tűzfal létrehozásához.

   A telepítés néhány percet vesz igénybe.
7. Az üzembe helyezés befejeződése után nyissa meg az **FW-Hybrid-test** erőforráscsoportot, és válassza ki a **AzFW01** tűzfalat.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

### <a name="configure-network-rules"></a>Hálózati szabályok konfigurálása

Először adjon hozzá egy hálózati szabályt a webes forgalom engedélyezéséhez.

1. A **AzFW01** lapon válassza a **szabályok** lehetőséget.
2. Válassza a **hálózati szabályok gyűjteménye** fület.
3. Válassza a **hálózati szabálygyűjtemény hozzáadása** lehetőséget.
4. A **név** mezőbe írja be a következőt: **RCNet01**.
5. A **priority**( **100**) mezőbe írja be a következőt:.
6. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
6. A **szabályok** területen a **név** mezőbe írja be a következőt: **AllowWeb**.
7. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
8. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
9. A **forrás** mezőbe írja be a következőt: **192.168.1.0/24**.
10. A **cél típusa** beállításnál válassza az **IP-cím** lehetőséget.
11. A **cél címe** mezőbe írja be a következőt: **10.6.0.0/16**
12. A **célport** mezőbe írja be a következőt: **80**.

Most adjon hozzá egy szabályt, amely engedélyezi az RDP-forgalmat.

A második szabály sorába írja be a következő adatokat:

1. **Név** mezőbe írja be a következőt: **AllowRDP**.
2. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
3. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
4. A **forrás** mezőbe írja be a következőt: **192.168.1.0/24**.
5. A **cél típusa** beállításnál válassza az **IP-cím** lehetőséget.
6. A **cél címe** mezőbe írja be a következőt: **10.6.0.0/16**
7. A **célport** mezőbe írja be a következőt: **3389**.
8. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-and-connect-the-vpn-gateways"></a>A VPN-átjárók létrehozása és csatlakoztatása

A hub és a helyszíni virtuális hálózatok VPN-átjárón keresztül kapcsolódnak egymáshoz.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>VPN-átjáró létrehozása a hub virtuális hálózathoz

Most hozzon létre egy VPN-átjárót a hub virtuális hálózathoz. A hálózatról hálózati konfigurációkhoz Útvonalalapú VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A keresés szövegmezőbe írja be a következőt: **Virtual Network Gateway**.
3. Válassza ki a **virtuális hálózati átjáró** elemet, és válassza a **Létrehozás** lehetőséget.
4. A **név** mezőbe írja be a következőt: **GW-hub**.
5. A **régió** mezőben válassza ki ugyanazt a régiót, amelyet korábban használt.
6. Az **átjáró típusa** beállításnál válassza a **VPN** lehetőséget.
7. A **VPN típusa** beállításnál válassza az **útvonal-alapú** lehetőséget.
8. Az **SKU** esetében válassza az **alapszintű** lehetőséget.
9. A **Virtual Network** esetében válassza az **VNet-hub** elemet.
10. A **nyilvános IP-cím** mezőben válassza az **új létrehozása** lehetőséget, és írja be a **VNet-hub-GW-pip** nevet.
11. Fogadja el a fennmaradó alapértékeket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.
12. Tekintse át a konfigurációt, majd kattintson a **Létrehozás** gombra.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>VPN-átjáró létrehozása a helyszíni virtuális hálózathoz

Most hozza létre a VPN-átjárót a helyszíni virtuális hálózathoz. A hálózatról hálózati konfigurációkhoz Útvonalalapú VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A keresés szövegmezőbe írja be a **Virtual Network Gateway** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza ki a **virtuális hálózati átjáró** elemet, és válassza a **Létrehozás** lehetőséget.
4. A **név** mezőbe írja be a következőt: **GW-helyszíni**.
5. A **régió** mezőben válassza ki ugyanazt a régiót, amelyet korábban használt.
6. Az **átjáró típusa** beállításnál válassza a **VPN** lehetőséget.
7. A **VPN típusa** beállításnál válassza az **útvonal-alapú** lehetőséget.
8. Az **SKU** esetében válassza az **alapszintű** lehetőséget.
9. A **Virtual Network** esetében válassza az **VNet-helyszíni** elemet.
10. A **nyilvános IP-cím** mezőben válassza az **új létrehozása** lehetőséget, és írja be a **VNet-helyszíni-GW-pip** nevet a név mezőbe.
11. Fogadja el a fennmaradó alapértékeket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.
12. Tekintse át a konfigurációt, majd kattintson a **Létrehozás** gombra.

### <a name="create-the-vpn-connections"></a>A VPN-kapcsolatok létrehozása

Most már létrehozhatja a VPN-kapcsolatokat a központ és a helyszíni átjárók között.

Ebben a lépésben létrehozza a kapcsolódást a hub virtuális hálózatról a helyszíni virtuális hálózatra. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

1. Nyissa meg az **FW-Hybrid-test** erőforráscsoportot, és válassza ki a **GW-hub** átjárót.
2. A bal oldali oszlopban válassza a **kapcsolatok** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A kapcsolódás neve, írja be a következőt: **hub-to-helyszíni**.
5. Válassza a **VNet – VNet** **lehetőséget.**
6. A **második virtuális hálózati átjáró** esetében válassza a **GW-helyszíni** lehetőséget.
7. A **megosztott kulcs (PSK)** mezőbe írja be a következőt: **AzureA1b2C3**.
8. Válassza az **OK** lehetőséget.

Hozza létre a helyszíni és a hub közötti virtuális hálózati kapcsolatokat. Ez a lépés hasonló az előzőhöz, kivéve, ha a VNet-Onpremt a VNet-hubhoz hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat néhány perc alatt létrejön.

1. Nyissa meg az **FW-Hybrid-test** erőforráscsoportot, és válassza ki a **GW-helyszíni** átjárót.
2. A bal oldali oszlopban válassza a **kapcsolatok** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A kapcsolódási név mezőbe írja be a következőt: **helyszíni-to-hub**.
5. Válassza a **VNet – VNet** **lehetőséget.**
6. A **második virtuális hálózati átjáró** esetében válassza a **GW-hub** elemet.
7. A **megosztott kulcs (PSK)** mezőbe írja be a következőt: **AzureA1b2C3**.
8. Válassza az **OK** lehetőséget.


#### <a name="verify-the-connection"></a>A kapcsolat ellenőrzése

Körülbelül öt perc múlva mindkét kapcsolat állapotát **csatlakoztatni** kell.

![Átjárókapcsolatok](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>A hub és a küllős virtuális hálózatok egyenrangúak

A hub és a küllős virtuális hálózatok most már egyenrangúak.

1. Nyissa meg az **FW-Hybrid-test** erőforráscsoportot, és válassza ki a **VNet-hub** virtuális hálózatot.
2. A bal oldali **oszlopban válassza a** társítások lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. **A virtuális hálózat** alatt:
 
   
   |Beállítás neve  |Érték  |
   |---------|---------|
   |Társi kapcsolat neve| HubtoSpoke|
   |Távoli virtuális hálózatra irányuló forgalom|   Engedélyezés (alapértelmezett)      |
   |Távoli virtuális hálózatról továbbított forgalom    |   Engedélyezés (alapértelmezett)      |
   |Virtuális hálózati átjáró     |  A virtuális hálózat átjárójának használata       |
    
5. **Távoli virtuális hálózat** alatt:

   |Beállítás neve  |Érték  |
   |---------|---------|
   |Társi kapcsolat neve | SpoketoHub|
   |Virtuális hálózat telepítési modellje| Resource Manager|
   |Előfizetés|\<your subscription\>|
   |Virtuális hálózat| VNet-Spoke
   |Távoli virtuális hálózatra irányuló forgalom     |   Engedélyezés (alapértelmezett)      |
   |Távoli virtuális hálózatról továbbított forgalom    |   Engedélyezés (alapértelmezett)      |
   |Virtuális hálózati átjáró     |  A távoli virtuális hálózat átjárójának használata       |

5. Válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="Vnet-társítás":::

## <a name="create-the-routes"></a>Az útvonalak létrehozása

Ezután hozzon létre néhány útvonalat:

- Egy útvonalat a központi átjáró alhálózatától a küllő alhálózatához a tűzfal IP-címén keresztül
- Egy alapértelmezett útvonalat a küllő alhálózattól a tűzfal IP-címén keresztül

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A keresés szövegmezőbe írja be az **útválasztási táblázat** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza az **útválasztási táblázat** lehetőséget.
4. Válassza a **Létrehozás** lehetőséget.
6. Válassza ki a **FW-Hybrid-test** elemet az erőforráscsoporthoz.
8. A **régió** mezőben válassza ki ugyanazt a helyet, amelyet korábban használt.
1. A név mezőbe írja be a következőt: **UDR-hub-küllő**.
9. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
10. Válassza a **Létrehozás** lehetőséget.
11. Az útválasztási táblázat létrehozása után válassza ki azt az útválasztási táblázat lap megnyitásához.
12. Válassza az **útvonalak** lehetőséget a bal oldali oszlopban.
13. Válassza a **Hozzáadás** lehetőséget.
14. Az útvonal neve mezőbe írja be a következőt: **ToSpoke**.
15. A **10.6.0.0/16** mezőbe írja be a következőt:.
16. A következő ugrás típusa beállításnál válassza a **virtuális berendezés** lehetőséget.
17. A következő ugrási cím mezőbe írja be a tűzfal a korábban feljegyzett magánhálózati IP-címét.
18. Válassza az **OK** lehetőséget.

Most rendelje hozzá az útvonalat az alhálózathoz.

1. Az **UDR-hub-küllő-Routes** lapon válassza az **alhálózatok** lehetőséget.
2. Válassza a **hozzárendelés** lehetőséget.
3. A **virtuális hálózat** területen válassza az **VNet-hub** elemet.
1. Az **alhálózat** területen válassza a **GatewaySubnet** lehetőséget.
2. Válassza az **OK** lehetőséget.

Most hozza létre az alapértelmezett útvonalat a küllő alhálózatból.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A keresés szövegmezőbe írja be az **útválasztási táblázat** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza az **útválasztási táblázat** lehetőséget.
5. Válassza a **Létrehozás** lehetőséget.
7. Válassza ki a **FW-Hybrid-test** elemet az erőforráscsoporthoz.
8. A **régió** mezőben válassza ki ugyanazt a helyet, amelyet korábban használt.
1. A név mezőbe írja be a következőt: **UDR-DG**.
4. A **propagált átjáró útvonala** beállításnál válassza a **nem** lehetőséget.
5. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
6. Válassza a **Létrehozás** lehetőséget.
7. Az útválasztási táblázat létrehozása után válassza ki azt az útválasztási táblázat lap megnyitásához.
8. Válassza az **útvonalak** lehetőséget a bal oldali oszlopban.
9. Válassza a **Hozzáadás** lehetőséget.
10. Az útvonal neve mezőbe írja be a következőt: **ToHub**.
11. A címzési előtag mezőbe írja be a következőt: **0.0.0.0/0**.
12. A következő ugrás típusa beállításnál válassza a **virtuális berendezés** lehetőséget.
13. A következő ugrási cím mezőbe írja be a tűzfal a korábban feljegyzett magánhálózati IP-címét.
14. Válassza az **OK** lehetőséget.

Most rendelje hozzá az útvonalat az alhálózathoz.

1. A **UDR-DG-Routes** lapon válassza az **alhálózatok** lehetőséget.
2. Válassza a **hozzárendelés** lehetőséget.
3. A **virtuális hálózat** területen válassza a **VNet-küllő** elemet.
1. Az **alhálózat** területen válassza az **SN-munkaterhelés** elemet.
2. Válassza az **OK** lehetőséget.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a küllős számítási feladatokat és a helyszíni virtuális gépeket, és helyezze őket a megfelelő alhálózatokra.

### <a name="create-the-workload-virtual-machine"></a>A számítási feladatot futtató virtuális gép létrehozása

Hozzon létre egy virtuális gépet a küllős virtuális hálózaton, amely nyilvános IP-címmel nem rendelkező IIS-t futtat.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A **népszerű** területen válassza a **Windows Server 2016 Datacenter** elemet.
3. Adja meg a következő értékeket a virtuális gép számára:
    - **Erőforráscsoport** – válassza az **FW-Hybrid-test** elemet.
    - **Virtuális gép neve**: *VM-küllős-01*.
    - **Régió** – a korábban használt régió.
    - **Felhasználó neve**: \<type a user name\> .
    - **Jelszó**: \<type a password\>
4. **Nyilvános bejövő portok** esetében válassza a **kiválasztott portok engedélyezése** lehetőséget, majd válassza a **http (80)** és az **RDP (3389) lehetőséget.**
4. Válassza a **Tovább: lemezek** lehetőséget.
5. Fogadja el az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés** lehetőséget.
6. Válassza ki a virtuális hálózat **VNet** , és az alhálózat az **SN-munkaterhelés**.
7. A **nyilvános IP-címek** esetében válassza a **nincs** lehetőséget. 
9. Válassza a **Tovább: kezelés** lehetőséget.
10. A **rendszerindítási diagnosztika** beállításnál válassza a **Letiltás** lehetőséget.
11. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget.

### <a name="install-iis"></a>Az IIS telepítése

1. A Azure Portal nyissa meg a Cloud Shell, és győződjön meg róla, hogy a **PowerShell**-re van állítva.
2. A következő parancs futtatásával telepítse az IIS-t a virtuális gépre, és szükség esetén módosítsa a helyet:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>A helyszíni virtuális gép létrehozása

Ez egy virtuális gép, amelyet a Távoli asztal a nyilvános IP-címhez való kapcsolódáshoz használ. Innentől kezdve a tűzfalon keresztül csatlakozhat a helyszíni kiszolgálóhoz.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A **népszerű** területen válassza a **Windows Server 2016 Datacenter** elemet.
3. Adja meg a következő értékeket a virtuális gép számára:
    - **Erőforráscsoport** – válassza a meglévő lehetőséget, majd válassza az **FW-Hybrid-test** lehetőséget.
    - **Virtuális gép neve**  -  *Virtuális gép – helyszíni*.
    - **Régió** – a korábban használt régió.
    - **Felhasználó neve**: \<type a user name\> .
    - **Jelszó**: \<type a user password\> .
7. **Nyilvános bejövő portok** esetében válassza a **kiválasztott portok engedélyezése** lehetőséget, majd az **RDP (3389) lehetőséget.**
4. Válassza a **Tovább: lemezek** lehetőséget.
5. Fogadja el az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés** lehetőséget.
6. Válassza a **VNet – helyszíni** a virtuális hálózathoz lehetőséget, az alhálózat pedig az **SN-Corp**.
8. Válassza a **Tovább: kezelés** lehetőséget.
10. A **rendszerindítási diagnosztika** beállításnál válassza a **Letiltás** lehetőséget.
10. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget.

## <a name="test-the-firewall"></a>A tűzfal tesztelése

1. Először jegyezze fel a **VM-küllős-01** virtuális gép magánhálózati IP-címét.

2. Az Azure Portalról csatlakozzon a **VM-Onprem** virtuális géphez.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Nyisson meg egy webböngészőt a **VM-helyszíni**, és keresse meg a http:// \<VM-spoke-01 private IP\> .

   Megjelenik a VM- **küllős-01** weblap: ![ VM-küllős-01 weblap](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. A **VM-helyszíni** virtuális gépről nyisson meg egy távoli asztalt a virtuális gép **által küllő-01** ÉRTÉKre a magánhálózati IP-címen.

   A kapcsolódásnak sikeresnek kell lennie, és be kell tudnia jelentkezni.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

<!---- You can ping the server on the spoke VNet.--->
- Böngészhet a webkiszolgálón a küllős virtuális hálózaton.
- Az RDP használatával kapcsolódhat a kiszolgálóhoz a küllős virtuális hálózaton.

Ezután módosítsa a tűzfal hálózati szabálygyűjteményének műveletét **Deny** (Megtagadás) értékre annak ellenőrzéséhez, hogy a tűzfalszabályok a vártnak megfelelően működnek-e.

1. Válassza ki a **AzFW01** tűzfalat.
2. Válassza a **szabályok** lehetőséget.
3. Válassza a **hálózati szabályok gyűjteménye** fület, és válassza ki a **RCNet01** .
4. A **művelethez** válassza a **Megtagadás** lehetőséget.
5. Kattintson a **Mentés** gombra.

A módosított szabályok ellenőrzése előtt zárja be a meglévő távoli asztalokat. Most futtassa újra az ellenőrzéseket. Ezúttal mindegyiknek sikertelennek kell lennie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rájuk szükség, törölje az **FW-Hybrid-Test** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>Következő lépések

A következő lépésben monitorozhatja az Azure Firewall naplóit.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./firewall-diagnostics.md)