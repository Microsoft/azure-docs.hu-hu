---
title: 'Oktatóanyag: hub virtuális hálózat biztonságossá tétele a Azure Firewall Managerrel'
description: Ebből az oktatóanyagból megtudhatja, hogyan védheti a virtuális hálózatot a Azure Firewall Managerrel a Azure Portal használatával.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: victorh
ms.openlocfilehash: f631100003a4ea6e191a5bdc13a11eb9aa327268
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212542"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Oktatóanyag: hub virtuális hálózat biztonságossá tétele a Azure Firewall Managerrel

Ha a helyszíni hálózatot egy Azure-beli virtuális hálózathoz kapcsolja egy hibrid hálózat létrehozásához, az Azure hálózati erőforrásaihoz való hozzáférés szabályozása egy átfogó biztonsági csomag fontos részét képezi.

A Azure Firewall Manager használatával létrehozhat egy központi virtuális hálózatot, amellyel biztonságossá teheti a saját IP-címekre, az Azure-ra és az internetre irányuló hibrid hálózati forgalmat. A Azure Firewall Manager használatával vezérelheti a hálózati hozzáférést egy hibrid hálózatban az engedélyezett és a letiltott hálózati forgalmat definiáló házirendekkel.

A Firewall Manager a biztonságos virtuális központ architektúráját is támogatja. A biztonságos virtuális központ és a hub virtuális hálózati architektúrájának összevetéséhez tekintse meg [a mi a Azure Firewall Manager architektúrájának beállításait?](vhubs-and-vnets.md)

Ebben az oktatóanyagban három virtuális hálózatot fog létrehozni:

- **VNet-hub** – a tűzfal ebben a virtuális hálózatban található.
- **VNet – küllő** – a küllős virtuális hálózat az Azure-ban található számítási feladatokat jelöli.
- **VNet-helyszíni** – a helyszíni virtuális hálózat egy helyszíni hálózatot jelöl. Tényleges telepítés esetén VPN-vagy ExpressRoute-kapcsolat is csatlakoztatható. Az egyszerűség kedvéért ez az oktatóanyag egy VPN Gateway-kapcsolaton alapul, és egy Azure-beli virtuális hálózatot használ a helyszíni hálózat ábrázolására.

![Hibrid hálózat](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tűzfalszabály létrehozása
> * A virtuális hálózatok létrehozása
> * A tűzfal konfigurálása és üzembe helyezése
> * A VPN-átjárók létrehozása és csatlakoztatása
> * A hub és a küllős virtuális hálózatok egyenrangúak
> * Az útvonalak létrehozása
> * A virtuális gépek létrehozása
> * A tűzfal tesztelése


## <a name="prerequisites"></a>Előfeltételek

A hibrid hálózatok a sugaras architektúrát használják az Azure-virtuális hálózatok és a helyszíni hálózatok közötti forgalom irányítására. A sugaras architektúra a következő követelményekkel rendelkezik:

- Állítsa be a **AllowGatewayTransit** , ha a VNet-Hub VNet beszél. Egy sugaras hálózati architektúrában az átjáró áttelepítése lehetővé teszi, hogy a küllős virtuális hálózatok megosszák a VPN-átjárót a központban a VPN-átjárók minden küllős virtuális hálózatban való üzembe helyezése helyett. 

   Emellett az átjáróhoz csatlakoztatott virtuális hálózatokhoz vagy a helyszíni hálózatokhoz tartozó útvonalak automatikusan a virtuális hálózatok útválasztási tábláiba lesznek propagálva az átjáró átirányítása használatával. További információ: [VPN Gateway-átvitel konfigurálása virtuális hálózatokhoz](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Állítsa be a **UseRemoteGateways** , amikor a társ VNet-Spoke az VNet-hub-ra. Ha a **UseRemoteGateways** be van állítva, és a távoli **AllowGatewayTransit** is be van állítva, a küllős virtuális hálózat a távoli virtuális hálózat átjáróit használja az átvitelhez.
- Ahhoz, hogy a küllős alhálózat forgalmát a központi tűzfalon keresztül irányítsa, szüksége van egy felhasználó által megadott útvonalra (UDR), amely a **virtuális hálózati átjáró útvonal-propagálási** beállításának letiltása a tűzfalra mutat. Ez a beállítás megakadályozza a küllős alhálózatok útválasztásának elosztását. Ezzel megelőzhető, hogy a megszerzett útvonalak ütköznek legyenek a UDR.
- Konfiguráljon egy UDR a központi átjáró alhálózatán, amely a tűzfal IP-címére mutat a küllő hálózatok következő ugrásakor. Nem szükséges UDR a Azure Firewall alhálózaton, mivel a BGP-ből tanulja meg az útvonalakat.

Az útvonalak létrehozásával kapcsolatos információkért lásd az oktatóanyag [Útvonalak létrehozása](#create-the-routes) című szakaszát.

>[!NOTE]
>Az Azure Firewallnak közvetlen internetkapcsolatra van szüksége. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat **fenntartása érdekében ezt** a 0.0.0.0/0 UDR kell felülbírálnia a **NextHopType** értékkel.
>
>A Azure Firewall konfigurálható úgy, hogy támogassa a kényszerített bújtatást. További információ: [Azure Firewall kényszerített bújtatás](../firewall/forced-tunneling.md).

>[!NOTE]
>A közvetlenül összekapcsolt virtuális hálózatok közötti forgalom közvetlenül akkor is átirányítva van, ha egy UDR az alapértelmezett átjáróként való Azure Firewallre mutat. Ha ebben a forgatókönyvben az alhálózatot alhálózati forgalomra szeretné küldeni a tűzfalra, a UDR mindkét alhálózaton explicit módon tartalmaznia kell a célként megadott alhálózat hálózati előtagot.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-firewall-policy"></a>Tűzfalszabály létrehozása

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. A Azure Portal keresési sávban írja be a **Firewall Manager** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. A Azure Firewall Manager lapon válassza az **Azure tűzfal-házirendek megtekintése** lehetőséget.

   ![Tűzfal házirendje](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Válassza a **Azure Firewall házirend létrehozása** lehetőséget.
1. Válassza ki az előfizetését, és az erőforráscsoport területen válassza az **új létrehozása** elemet, és hozzon létre egy **FW-Hybrid-test** nevű erőforráscsoportot.
2. A szabályzat neve mezőbe írja be a következőt: **pol-Net01**.
3. A régió területen válassza az **USA keleti** régiója lehetőséget.
1. Válassza a **Tovább: DNS-beállítások** lehetőséget.
1. Válassza a **Next (tovább): TLS-ellenőrzés (előzetes verzió)**
1. Válassza a **Tovább: szabályok** lehetőséget.
1. Válassza **a szabálygyűjtemény hozzáadása** lehetőséget.
1. A **név** mezőbe írja be a következőt: **RCNet01**.
1. A **szabály gyűjtemény típusa** beállításnál válassza a **hálózat** lehetőséget.
1. A **priority**( **100**) mezőbe írja be a következőt:.
1. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
1. A **szabályok** területen a **név** mezőbe írja be a következőt: **AllowWeb**.
1. A **forrás** mezőbe írja be a következőt: **192.168.1.0/24**.
1. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
1. A **célport** mezőbe írja be a következőt: **80**.
1. A **cél típusa** beállításnál válassza az **IP-cím** lehetőséget.
1. A **cél** mezőbe írja be a következőt: **10.6.0.0/16**.
1. A következő szabály sorában adja meg a következő adatokat:
 
    Név: írja be a **AllowRDP**<br>
    Forrás: Type **192.168.1.0/24**.<br>
    Protokoll, válassza a **TCP** lehetőséget.<br>
    Célport, típus **3389**<br>
    Cél típusa, **IP-cím** kiválasztása<br>
    A cél mezőbe írja be a következőt: **10.6.0.0/16**

1. Válassza a **Hozzáadás** lehetőséget.
2. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
3. Tekintse át a részleteket, majd válassza a **Létrehozás** lehetőséget.

## <a name="create-the-firewall-hub-virtual-network"></a>A tűzfal hub virtuális hálózatának létrehozása

> [!NOTE]
> A AzureFirewallSubnet-alhálózat mérete/26. További információ az alhálózat méretétől: [Azure Firewall GYIK](../firewall/firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. Keressen rá a **virtuális hálózatra** , majd válassza a **virtuális hálózat** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport** területen válassza az **FW-Hybrid-test** elemet.
1. A **név** mezőbe írja be a következőt: **VNet-hub**.
1. A régió területen válassza az **USA keleti** **régiója** lehetőséget.
1. Válassza a **Tovább: IP-címek** lehetőséget.

1. **IPv4-címterület** esetén írja be a következőt: **10.5.0.0/16**.
1. Az **alhálózat neve** területen válassza az **alapértelmezett** lehetőséget.
1.  Módosítsa az **alhálózat nevét** a **AzureFirewallSubnet** értékre. A tűzfal ebben az alhálózatban található, és az **alhálózat nevének AzureFirewallSubnet kell** lennie.
1. **Alhálózati címtartomány** esetében írja be a következőt: **10.5.0.0/26**. 
1. Fogadja el a többi alapértelmezett beállítást, majd kattintson a **Mentés** gombra.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-the-spoke-virtual-network"></a>A küllős virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. Keressen rá a **virtuális hálózatra** , majd válassza a **virtuális hálózat** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport** területen válassza az **FW-Hybrid-test** elemet.
1. A **név** mezőbe írja be a **VNet-küllő** értéket.
1. A régió területen válassza az **USA keleti** **régiója** lehetőséget.
1. Válassza a **Tovább: IP-címek** lehetőséget.

1. **IPv4-címterület** esetén írja be a következőt: **10.6.0.0/16**.
1. Az **alhálózat neve** területen válassza az **alapértelmezett** lehetőséget.
1. Módosítsa az **alhálózat nevét** az **SN-munkaterhelés** értékre.
1. **Alhálózati címtartomány** esetében írja be a következőt: **10.6.0.0/24**. 
1. Fogadja el a többi alapértelmezett beállítást, majd kattintson a **Mentés** gombra.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.


## <a name="create-the-on-premises-virtual-network"></a>Helyszíni virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. Keressen rá a **virtuális hálózatra** , majd válassza a **virtuális hálózat** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport** területen válassza az **FW-Hybrid-test** elemet.
1. A **név** mezőbe írja be a következőt: **VNet-helyszíni**.
1. A régió területen válassza az **USA keleti** **régiója** lehetőséget.
1. Válassza a **Tovább: IP-címek** lehetőséget.

1. **IPv4-címterület** esetén írja be a következőt: **192.168.0.0/16**.
1. Az **alhálózat neve** területen válassza az **alapértelmezett** lehetőséget.
1. Módosítsa az **alhálózat nevét** az **SN-Corp** értékre.
1. **Alhálózati címtartomány** esetében írja be a következőt: **192.168.1.0/24**. 
1. Fogadja el a többi alapértelmezett beállítást, majd kattintson a **Mentés** gombra.
2. Válassza az **alhálózat hozzáadása** lehetőséget.
3. Az **alhálózat neve** mezőbe írja be a következőt: **GatewaySubnet**.
4. Az **alhálózati címtartomány** típusa **192.168.2.0/24**.
5. Válassza a **Hozzáadás** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.




## <a name="configure-and-deploy-the-firewall"></a>A tűzfal konfigurálása és üzembe helyezése

Ha a biztonsági házirendek egy központhoz vannak társítva, akkor azt a *hub virtuális hálózatának* nevezzük.

Alakítsa át a **VNet-hub** virtuális hálózatot egy *központi virtuális hálózatba* , és gondoskodjon Azure Firewall.

1. A Azure Portal keresési sávban írja be a **Firewall Manager** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. A Azure Firewall Manager lapon a **Biztonság hozzáadása virtuális hálózatokhoz** területen válassza a **hub virtuális hálózatok megtekintése** lehetőséget.
1. A **virtuális hálózatok** területen jelölje be a **VNet-hub** jelölőnégyzetét.
1. Válassza a **biztonság kezelése** lehetőséget, majd válassza **a tűzfal üzembe helyezése tűzfal-házirenddel** lehetőséget.
1. A **virtuális hálózatok konvertálása** lap **Tűzfalszabályok** területén jelölje be a **pol-Net01** jelölőnégyzetet.
1. Kattintson a **Tovább gombra: felülvizsgálat + megerősítés**
1. Tekintse át a részleteket, majd válassza a **Confirm (megerősítés**) lehetőséget.


   A telepítés néhány percet vesz igénybe.
7. Az üzembe helyezés befejeződése után nyissa meg az **FW-Hybrid-test** erőforráscsoportot, és válassza ki a tűzfalat.
9. Jegyezze fel a **tűzfal magánhálózati IP-** címét az **Áttekintés** oldalon. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-and-connect-the-vpn-gateways"></a>A VPN-átjárók létrehozása és csatlakoztatása

A hub és a helyszíni virtuális hálózatok VPN-átjárón keresztül kapcsolódnak egymáshoz.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>VPN-átjáró létrehozása a hub virtuális hálózathoz

Most hozzon létre egy VPN-átjárót a hub virtuális hálózathoz. A hálózatról hálózati konfigurációkhoz Útvonalalapú VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A keresés szövegmezőbe írja be a **Virtual Network Gateway** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza ki a **virtuális hálózati átjáró** elemet, és válassza a **Létrehozás** lehetőséget.
4. A **név** mezőbe írja be a következőt: **GW-hub**.
5. A régió területen válassza az USA **keleti** **régióját**.
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
5. A régió területen válassza az USA **keleti** **régióját**.
6. Az **átjáró típusa** beállításnál válassza a **VPN** lehetőséget.
7. A **VPN típusa** beállításnál válassza az **útvonal-alapú** lehetőséget.
8. Az **SKU** esetében válassza az **alapszintű** lehetőséget.
9. A **Virtual Network** esetében válassza az **VNet-helyszíni** elemet.
10. A **nyilvános IP-cím** mezőben válassza az **új létrehozása** lehetőséget, és írja be a **VNet-helyszíni-GW-pip** nevet a név mezőbe.
11. Fogadja el a fennmaradó alapértékeket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.
12. Tekintse át a konfigurációt, majd kattintson a **Létrehozás** gombra.

### <a name="create-the-vpn-connections"></a>A VPN-kapcsolatok létrehozása

Most már létrehozhatja a VPN-kapcsolatokat a központ és a helyszíni átjárók között.

Ebben a lépésben létrehozza a kapcsolódást a hub virtuális hálózatról a helyszíni virtuális hálózatra. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolatok létrehozása némi időbe telik.

1. Nyissa meg az **FW-Hybrid-test** erőforráscsoportot, és válassza ki a **GW-hub** átjárót.
2. A bal oldali oszlopban válassza a **kapcsolatok** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A kapcsolódás neve mezőbe írja be a következőt: **hub-to-helyszíni**.
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

![Átjárókapcsolatok](media/secure-hybrid-network/gateway-connections.png)

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
   |Virtuális hálózati átjáró vagy útválasztó kiszolgáló    |  A virtuális hálózat átjárójának használata       |
    
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

   :::image type="content" source="media/secure-hybrid-network/firewall-peering.png" alt-text="Vnet-társítás":::

## <a name="create-the-routes"></a>Az útvonalak létrehozása

Ezután hozzon létre néhány útvonalat:

- Egy útvonalat a központi átjáró alhálózatától a küllő alhálózatához a tűzfal IP-címén keresztül
- Egy alapértelmezett útvonalat a küllő alhálózattól a tűzfal IP-címén keresztül

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A keresés szövegmezőbe írja be az **útválasztási táblázat** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza az **útválasztási táblázat** lehetőséget.
4. Válassza a **Létrehozás** lehetőséget.
1. Válassza ki a **FW-Hybrid-test** elemet az erőforráscsoporthoz.
1. A régió területen válassza az **USA keleti** **régiója** lehetőséget.
1. A név mezőbe írja be a következőt: **UDR-hub-küllő**.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Az útválasztási táblázat létrehozása után válassza ki azt az útválasztási táblázat lap megnyitásához.
1. Válassza az **útvonalak** lehetőséget a bal oldali oszlopban.
1. Válassza a **Hozzáadás** lehetőséget.
1. Az útvonal neve mezőbe írja be a következőt: **ToSpoke**.
1. A **10.6.0.0/16** mezőbe írja be a következőt:.
1. A következő ugrás típusa beállításnál válassza a **virtuális berendezés** lehetőséget.
1. A következő ugrási cím mezőbe írja be a tűzfal a korábban feljegyzett magánhálózati IP-címét.
1. Válassza az **OK** lehetőséget.

Most rendelje hozzá az útvonalat az alhálózathoz.

1. Az **UDR-hub-küllő-Routes** lapon válassza az **alhálózatok** lehetőséget.
2. Válassza a **hozzárendelés** lehetőséget.
4. A **virtuális hálózat** területen válassza az **VNet-hub** elemet.
5. Az **alhálózat** területen válassza a **GatewaySubnet** lehetőséget.
6. Válassza az **OK** lehetőséget.

Most hozza létre az alapértelmezett útvonalat a küllő alhálózatból.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A keresés szövegmezőbe írja be az **útválasztási táblázat** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza az **útválasztási táblázat** lehetőséget.
5. Válassza a **Létrehozás** lehetőséget.
7. Válassza ki a **FW-Hybrid-test** elemet az erőforráscsoporthoz.
8. A régió területen válassza az **USA keleti** **régiója** lehetőséget.
1. A név mezőbe írja be a következőt: **UDR-DG**.
4. Az **átjáró-útvonalak propagálásához** válassza a **nem** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Az útválasztási táblázat létrehozása után válassza ki azt az útválasztási táblázat lap megnyitásához.
1. Válassza az **útvonalak** lehetőséget a bal oldali oszlopban.
1. Válassza a **Hozzáadás** lehetőséget.
1. Az útvonal neve mezőbe írja be a következőt: **ToHub**.
1. A címzési előtag mezőbe írja be a következőt: **0.0.0.0/0**.
1. A következő ugrás típusa beállításnál válassza a **virtuális berendezés** lehetőséget.
1. A következő ugrási cím mezőbe írja be a tűzfal a korábban feljegyzett magánhálózati IP-címét.
1. Válassza az **OK** lehetőséget.

Most rendelje hozzá az útvonalat az alhálózathoz.

1. A **UDR-DG-Routes** lapon válassza az **alhálózatok** lehetőséget.
2. Válassza a **hozzárendelés** lehetőséget.
4. A **virtuális hálózat** területen válassza a **VNet-küllő** elemet.
5. Az **alhálózat** területen válassza az **SN-munkaterhelés** elemet.
6. Válassza az **OK** lehetőséget.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a küllős számítási feladatokat és a helyszíni virtuális gépeket, és helyezze őket a megfelelő alhálózatokra.

### <a name="create-the-workload-virtual-machine"></a>A számítási feladatot futtató virtuális gép létrehozása

Hozzon létre egy virtuális gépet a küllős virtuális hálózaton, amely nyilvános IP-címmel nem rendelkező IIS-t futtat.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A **népszerű** területen válassza a **Windows Server 2016 Datacenter** elemet.
3. Adja meg a következő értékeket a virtuális gép számára:
    - **Erőforráscsoport** – válassza az **FW-Hybrid-test** elemet
    - **Virtuális gép neve**: *VM-küllős-01*
    - **Régió**  -  *(USA) USA keleti* régiója
    - **Felhasználónév**: adjon meg egy felhasználónevet
    - **Password (jelszó**): írjon be egy jelszót

4. Válassza a **Tovább: lemezek** lehetőséget.
5. Fogadja el az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés** lehetőséget.
6. Válassza ki a virtuális hálózat **VNet** , és az alhálózat az **SN-munkaterhelés**.
8. **Nyilvános bejövő portok** esetében válassza a **kiválasztott portok engedélyezése** lehetőséget, majd válassza a **http (80)** és az **RDP (3389) lehetőséget.**
1. Válassza a **Tovább: kezelés** lehetőséget.
1. A **rendszerindítási diagnosztika** beállításnál válassza a **Letiltás** lehetőséget.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget.

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
    - **Erőforráscsoport** – válassza a meglévő lehetőséget, majd válassza az **FW-Hybrid-test** elemet.
    - **Virtuális gép neve**  -  *Virtuális gép – helyszíni*
    - **Régió**  -  *(USA) USA keleti* régiója
    - **Felhasználónév**: adjon meg egy felhasználónevet
    - **Password (jelszó**): írja be a jelszót

4. Válassza a **Tovább: lemezek** lehetőséget.
5. Fogadja el az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés** lehetőséget.
6. Válassza a **VNet-helyszíni** a virtuális hálózat számára lehetőséget, és ellenőrizze, hogy az alhálózat az **SN-Corp**.
7. **Nyilvános bejövő portok** esetében válassza a **kiválasztott portok engedélyezése** lehetőséget, majd az **RDP (3389) lehetőséget.**
8. Válassza a **Tovább: kezelés** lehetőséget.
9. A **rendszerindítási diagnosztika** beállításnál válassza a **Letiltás** lehetőséget.
10. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget.

## <a name="test-the-firewall"></a>A tűzfal tesztelése

1. Először jegyezze fel a VM-küllős-01 virtuális gép magánhálózati IP-címét a VM-küllő-01 áttekintés oldalon.

2. Az Azure Portalról csatlakozzon a **VM-Onprem** virtuális géphez.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Nyisson meg egy webböngészőt a **VM-helyszíni**, és keresse meg a http:// \<VM-spoke-01 private IP\> .

   Megjelenik a VM- **küllős-01** weblap: ![ VM-küllős-01 weblap](media/secure-hybrid-network/vm-spoke-01-web.png)

4. A **VM-helyszíni** virtuális gépről nyisson meg egy távoli asztalt a virtuális gép **által küllő-01** ÉRTÉKre a magánhálózati IP-címen.

   A kapcsolódásnak sikeresnek kell lennie, és be kell tudnia jelentkezni.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

<!---- You can ping the server on the spoke VNet.--->
- Böngészhet a webkiszolgálón a küllős virtuális hálózaton.
- Az RDP használatával kapcsolódhat a kiszolgálóhoz a küllős virtuális hálózaton.

Ezután módosítsa a tűzfal hálózati szabálygyűjteményének műveletét **Deny** (Megtagadás) értékre annak ellenőrzéséhez, hogy a tűzfalszabályok a vártnak megfelelően működnek-e.

1. Nyissa meg az **FW-Hybrid-test** erőforráscsoportot, és válassza ki a **pol-Net01** tűzfal-házirendet.
2. A **Beállítások** területen válassza a **szabályok gyűjtemények** lehetőséget.
1. Válassza ki a **RCNet01** .
1. A **szabály-gyűjtemény műveletnél** válassza a **Megtagadás** lehetőséget.
1. Kattintson a **Mentés** gombra.

A módosított szabályok tesztelése előtt zárjunk be minden meglévő távoli asztali gépet és böngészőt a **helyszíni** . A szabály-gyűjtemény frissítésének befejeződése után futtassa újra a teszteket. Nem kell csatlakozniuk az adott időponthoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfal erőforrásai továbbra is megtarthatók a további vizsgálathoz, vagy ha már nincs rá szükség, törölje az **FW-Hybrid-test** erőforráscsoportot az összes tűzfalhoz kapcsolódó erőforrás törléséhez.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: virtuális WAN biztonságossá tétele a Azure Firewall Managerrel](secure-cloud-network.md)