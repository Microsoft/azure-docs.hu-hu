---
title: 'Oktatóanyag: virtuális gépek terheléselosztása rendelkezésre állási zónák között – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez az oktatóanyag bemutatja, hogyan hozzon létre egy standard terheléselosztót zónaredundáns előtérrel a rendelkezésre állási zónák közötti virtuális gépek Azure Portal használatával történő terheléselosztásához
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 54dbc5f4000c9ae10592b04ca55c0fc528f40633
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056079"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Oktatóanyag: Virtuális gépek terhelésének elosuztása a rendelkezésre állási zónák között az Azure Portallal

A terheléselosztás magasabb szintű rendelkezésre állást biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ez az oktatóanyag végigvezeti egy olyan nyilvános standard Load Balancer létrehozásán, amely terheléselosztást biztosít a virtuális gépek számára a rendelkezésre állási zónák között. Így biztosíthatja az alkalmazások és az adatok védelmét nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére. A zónaredundanciával az adatok elérési útja egy vagy több zóna meghibásodása után is ép marad mindaddig, amíg a régióban legalább egy zóna megfelelően működik. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Standard Load Balancer létrehozása
> * Hálózati biztonsági csoportok létrehozása bejövő forgalomra vonatkozó szabályok meghatározásához
> * Zónaredundáns virtuális gépek létrehozása több zóna között, valamint ezek hozzákapcsolása egy terheléselosztóhoz
> * Terheléselosztó állapotmintájának létrehozása
> * Terheléselosztó forgalmára vonatkozó szabályok létrehozása
> * Alapszintű IIS-webhely létrehozása
> * Terheléselosztó megtekintése működés közben

A rendelkezésre állási zónák a Standard Load Balancerrel való használatáról bővebben a [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md) című cikkben tájékozódhat.

Igény szerint az oktatóanyag az [Azure CLI](./quickstart-load-balancer-standard-public-cli.md) használatával is elvégezhető.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Ha a terheléselosztó létrehozása során létrehoz egy új nyilvános IP-címet, akkor az automatikusan standard termékváltozatként lesz konfigurálva, és automatikusan zónaredundánssá is válik.

1. A képernyő bal felső részén kattintson az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer** lehetőségre.
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a *MyResourceGroupLBAZ* szöveget a szövegmezőbe.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | Válassza a **standard** lehetőséget.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím              | Írja be a *myPublicIP* szöveget a szövegmezőbe.   |
    |A rendelkezésre állási zóna| Válassza ki a **felesleges zónát**.    |
   

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Eben a szakaszban egy virtuális hálózatot fog létrehozni, valamint virtuális gépeket a régió különböző zónái számára, majd telepíteni fogja az IIS-t a virtuális gépeken a zónaredundáns terheléselosztó tesztelése érdekében. Így ha az egyik zóna meghibásodik, az ugyanabba a zónába tartozó virtuális gép állapotmintája sikertelen lesz, és a forgalmat továbbra is szolgáltatni fogják a más zónába tartozó virtuális gépek.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLBAZ (meglévő erőforráscsoport kiválasztása) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Nyugat-Európa      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

1. A képernyő bal felső oldalán kattintson az **Erőforrás létrehozása** elemre, írja be a keresőmezőbe, hogy *Hálózati biztonsági csoport*, majd kattintson a hálózati biztonsági csoport lapján a **Létrehozás** elemre.
2. A Hálózati biztonsági csoport létrehozása lapon adja meg a következő értékeket:
    - A hálózati biztonsági csoport neve: *myNetworkSecurityGroup*.
    - A létező erőforráscsoport neve: *myResourceGroupBAZ*.
   
![A képernyőfelvétel a hálózati biztonsági csoport létrehozása panelt jeleníti meg.](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Hálózati biztonsági csoport szabályainak létrehozása

Ebben a szakaszban a hálózati biztonsági csoport szabályait fogja létrehozni az Azure Portalon a HTTP-t és RDP-t használó bejövő kapcsolatok engedélyezéséhez.

1. Az Azure Portalon kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd keresse meg a **myResourceGroupLBAZ** erőforráscsoportban található **myNetworkSecurityGroup** elemet, és kattintson rá.
2. A **Beállítások** területen kattintson a **Bejövő biztonsági szabályok** lehetőségre, majd a **Hozzáadás** gombra.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - A terheléselosztási szabály neve: *myHTTPRule*.
    - A terheléselosztási szabály leírása: *HTTP engedélyezése*.
4. Kattintson az **OK** gombra.
 
   ![A képernyőfelvétel a bejövő biztonsági szabály hozzáadása panelt jeleníti meg.](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Ismételje meg a 2–4. lépéseket egy másik, *myRDPRule* nevű szabály létrehozásához, amely engedélyezi a 3389-es portot használó, bejövő RDP-kapcsolatokat. Ehhez adja meg az alábbi értékeket:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *3389*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *200*
    - Név: *myRDPRule*
    - Leírás: *RDP engedélyezése*

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre virtuális gépeket különböző zónákban (1. zóna, 2. zóna, 3. zóna) azon régió számára, amely a terheléselosztó háttérkiszolgálójaként szolgálhat.

1. A képernyő bal felső részén kattintson az **erőforrás létrehozása**  >  **számítás**  >  **Windows Server 2016 Datacenter** elemre, és adja meg ezeket az értékeket a virtuális géphez:
    - A virtuális gép neve: *myVM1*.        
    - A rendszergazda felhasználóneve: *azureuser*.    
    - *myResourceGroupLBAZ*: Az **Erőforráscsoport** esetében válassza a **Meglévő használata** lehetőséget, majd válassza ki a *myResourceGroupLBAZ* erőforráscsoportot.
2. Kattintson az **OK** gombra.
3. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
4. Adja meg az alábbi értékeket a virtuális gép beállításainál:
    - Az a zóna, ahol elhelyezi a virtuális gépet: *1. zóna*.
    -  Győződjön meg róla, hogy a következő virtuális hálózat van kiválasztva: *myVNet*.
    - Győződjön meg róla, hogy a következő alhálózat van kiválasztva: *myBackendSubnet*.
    - A hálózati biztonsági csoport (tűzfal) neve: *myNetworkSecurityGroup*.
5. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
6. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.
7. Az 1–6. lépés követésével hozzon létre egy második virtuális gépet *VM2* néven a 2. zónában, valamint egy harmadik virtuális gépet a 3. zónában, *myVnet* virtuális hálózattal, *myBackendSubnet* alhálózattal és **myNetworkSecurityGroup* hálózati biztonsági csoporttal.

### <a name="install-iis-on-vms"></a>Az IIS telepítése virtuális gépekre

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd az erőforrások listájában kattintson a *myResourceGroupLBAZ* erőforráscsoportban található **myVM1** elemre.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. Jelentkezzen be a virtuális gépbe az *azureuser* felhasználónévvel.
4. A kiszolgáló asztalán navigáljon a **Windows felügyeleti eszközök** > **Windows PowerShell** elemre.
5. A PowerShell-ablakban futtassa az alábbi parancsokat az IIS-kiszolgáló telepítéséhez, távolítsa el az alapértelmezett iisstart.htm fájlt, majd adjon hozzá egy új iisstart.htm fájt, amely megjeleníti a virtuális gép nevét:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Az RDP-munkamenet lezárása a *myVM1*.
7. Az 1–6. lépés megismétlésével telepítse az IIS-t és a frissített iisstart.htm fájlt a *myVM2* és *myVM3* gépeken.

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, illetve terheléselosztási és NAT-szabályokat fog megadni.


### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét. Hozza létre a háttércímkészletet *myBackendPool* néven, amely magában foglalja a *VM1*,a *VM2* és a *VM3* virtuális gépeket.

1. A bal oldali menüben kattintson az **összes erőforrás** elemre, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a **Hozzáadás** gombra.
3. A **Háttérkészlet hozzáadása** oldalon tegye a következőket:
    - A név mezőbe írja be a *myBackEndPool* nevet a háttérbeli készlet neveként.
    - Válassza a **Virtuális hálózat** legördülő menüben a **myVNet** lehetőséget
    - Válassza a **Virtuális gép** legördülő menüben a **myVM1** lehetőséget.
    - Az **IP-cím** esetében a legördülő menüben kattintson a myVM1 IP-címére.
4. Kattintson az **Új háttérerőforrás hozzáadása** lehetőségre a virtuális gépek (*myVM2* és *myVM3*) a terheléselosztó háttérkészletéhez való hozzáadásához.
5. Kattintson a **Hozzáadás** parancsra.

    ![Hozzáadás a háttércímkészlethez –](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Ellenőrizze, hogy a terheléselosztó háttérkészletének beállítása mind a három virtuális gépet megjeleníti-e: **myVM1**, **myVM2** és **myVM3**.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. A bal oldali menüben kattintson az **összes erőforrás** elemre, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások** területen kattintson az **Állapotminták** elemre, majd a **Hozzáadás** gombra.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
    - Az állapotminta neve: *myHealthProbe*.
    - Protokoll típusa: **HTTP**.
    - Portszám: *80*.
    - A mintavételi kísérletek közötti **Időköz** másodpercben megadva: *15*.
    - A **Nem kifogástalan állapot küszöbértéke**, vagyis hány egymást követő meghibásodásnak kell történnie, hogy a rendszer sérültnek minősítsen egy virtuális gépet: *2*.
4. Kattintson az **OK** gombra.

   ![Mintavétel hozzáadása](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt, amely felügyeli a *FrontendLoadBalancer* előtér 80-as portját, és terhelés szempontjából elosztott forgalmat továbbít a szintén a 80-as portot használó, *myBackEndPool* nevű háttércímkészlethez. 

1. A bal oldali menüben kattintson az **összes erőforrás** elemre, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások** területen kattintson a **Terheléselosztási szabályok** elemre, majd a **Hozzáadás** gombra.
3. Konfigurálja a terheléselosztási szabályt az alábbi értékekkel:
    - A terheléselosztási szabály neve: *myHTTPRule*.
    - Protokoll típusa: **TCP**.
    - Portszám: *80*.
    - Háttérport: *80*.
    - A háttérkészlet neve: *myBackendPool*.
    - Az állapotminta neve: *myHealthProbe*.
4. Kattintson az **OK** gombra.
    
    
    ![Terheléselosztási szabály hozzáadása](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Kattintson a **Minden erőforrás**, majd a **myPublicIP** elemre.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

      ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha meg szeretné tekinteni, hogy hogyan osztja el a terheléselosztó a zónák között elosztott virtuális gépek között a forgalmat, kényszerítheti a webböngésző frissítését.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Ehhez válassza ki azt az erőforráscsoportot, amely tartalmazza a terheléselosztó elemet, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

További információ a virtuális gépek egy adott rendelkezésre állási zónán belüli terheléselosztásáról.
> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása rendelkezésre állási zónán belül](tutorial-load-balancer-standard-public-zonal-portal.md)