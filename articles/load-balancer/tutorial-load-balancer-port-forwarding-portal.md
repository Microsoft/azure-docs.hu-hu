---
title: 'Oktatóanyag: a port továbbításának konfigurálása – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a port továbbítását a Azure Load Balancer használatával az Azure virtuális hálózatban lévő virtuális gépek kapcsolatainak létrehozásához.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 63d1a08dc588f0303ccb1ae13bd4c28af2a393c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92043653"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Oktatóanyag: Port továbbításának konfigurálása Azure Load Balancer a portál használatával

A portok továbbítása lehetővé teszi, hogy egy Azure-beli virtuális hálózatban virtuális gépekhez (VM) kapcsolódjon egy Azure Load Balancer nyilvános IP-cím és portszám használatával. 

Ebben az oktatóanyagban a port továbbítását állítja be egy Azure Load Balancer. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy nyilvános standard Load balancert a virtuális gépeken keresztüli hálózati forgalom kiegyensúlyozásához. 
> * Hozzon létre egy virtuális hálózatot és virtuális gépeket egy hálózati biztonsági csoport (NSG) szabállyal. 
> * Adja hozzá a virtuális gépeket a terheléselosztó háttér-címkészletet.
> * Hozzon létre egy terheléselosztó állapotának mintavételi és forgalmi szabályait.
> * Terheléselosztó bejövő NAT-portjának létrehozása – továbbítási szabályok
> * Telepítse és konfigurálja az IIS-t a virtuális gépeken a terheléselosztás és a port továbbításának működés közbeni megtekintéséhez.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

Az oktatóanyagban szereplő összes lépésnél jelentkezzen be a Azure Portalba a következő címen: [https://portal.azure.com](https://portal.azure.com) .

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés.

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

Először hozzon létre egy nyilvános standard Load balancert, amely képes ellensúlyozni a virtuális gépek forgalmának terhelését. A standard Load Balancer csak a standard nyilvános IP-címet támogatja. Standard Load Balancer létrehozásakor létre kell hoznia egy új szabványos nyilvános IP-címet is, amely a terheléselosztó előtér-felületének és a **LoadBalancerFrontEnd** nevű alapértelmezett értéknek megfelelően van konfigurálva. 

1. A képernyő bal felső részén kattintson az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer** lehetőségre.
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a *myresourcegrouplb erőforráscsoportban* szöveget a szövegmezőbe.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | Válassza a **standard** lehetőséget.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím              | Írja be a *myPublicIP* szöveget a szövegmezőbe.   |
    |A rendelkezésre állási zóna| Válassza ki a **felesleges zónát**.    |
     
    >[!NOTE]
     >Győződjön meg arról, hogy a Load Balancer és a hozzá tartozó összes erőforrást egy olyan helyen hozza létre, amely támogatja a Availability Zones. További információ: [Availability Zones támogató régiók](../availability-zones/az-region.md). 

3. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás** gombra.  
  
## <a name="create-and-configure-back-end-servers"></a>Háttér-kiszolgálók létrehozása és konfigurálása

Hozzon létre egy virtuális hálózatot két virtuális géppel, majd adja hozzá a virtuális gépeket a terheléselosztó háttér-készletéhez. 

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | Myresourcegrouplb erőforráscsoportban (meglévő erőforráscsoport kiválasztása) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Nyugat-Európa      |
| **\<IPv4-address-space>**   | 10.3.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Virtuális gépek létrehozása és hozzáadása a terheléselosztó háttér-készletéhez

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítás**  >  **Windows Server 2016 Datacenter** elemet. 
   
1. A **virtuális gép létrehozása** területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: legördülő menüből válassza a **myresourcegrouplb erőforráscsoportban** lehetőséget.
   - **Virtuális gép neve**: Type *MyVM1*.
   - **Régió**: válassza a **Nyugat-Európa** lehetőséget. 
   - **Felhasználónév**: írja be a következőt: *azureuser*.
   - **Password (jelszó**): írja be a *Azure1234567*. 
     Írja be újra a jelszót a **Jelszó megerősítése** mezőbe.
   
1. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget. 
   
   Győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **MyVNet**
   - **Alhálózat**: **MyBackendSubnet**
   
1. A **nyilvános IP-** cím területen válassza az **új létrehozása** lehetőséget, a **nyilvános IP-cím létrehozása** lapon válassza a **standard** lehetőséget, majd kattintson **az OK gombra**. 
   
1. A **hálózati biztonsági csoport** területen válassza a **speciális** lehetőséget az új hálózati biztonsági csoport (NSG), a tűzfal típusának létrehozásához. 
   1. A **hálózati biztonsági csoport konfigurálása** mezőben válassza az **új létrehozása** lehetőséget. 
   1. Írja be a *MyNetworkSecurityGroup*, majd kattintson **az OK gombra**. 
   
   >[!NOTE]
   >Figyelje meg, hogy alapértelmezés szerint a NSG már rendelkezik egy bejövő szabállyal a 3389-es port megnyitásához a távoli asztali (RDP) porton.
   
1. Adja hozzá a virtuális gépet egy olyan terheléselosztó-háttérbeli készlethez, amelyet létrehoz:
   
   1. A **terheléselosztás alatt**  >  **Helyezze el ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögött?**, válassza az **Igen** lehetőséget. 
   1. A terheléselosztási **beállításoknál** válassza a legördülő listát, majd az **Azure Load Balancer** elemet. 
   1. A **Load Balancer kiválasztásához** válassza a legördülő listát, és válassza a **MyLoadBalancer** lehetőséget. 
   1. **A backend-készlet kiválasztása** területen válassza **az új létrehozása** elemet, majd írja be a *MyBackendPool*, és válassza a **Létrehozás** lehetőséget. 
   
   ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Válassza a **kezelés** lapot, vagy válassza a **következő**  >  **kezelés** lehetőséget. A **figyelés** területen **kapcsolja ki** a **rendszerindítási diagnosztika** beállítást.
   
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
1. Tekintse át a beállításokat, és ha az érvényesítés sikeres, válassza a **Létrehozás** lehetőséget. 

1. A *MyVM2* nevű második virtuális gép létrehozásához kövesse a lépéseket, és az összes többi beállítással megegyező, mint a MyVM1. 
   
   A **hálózati biztonsági csoportnál** a **speciális** elem kiválasztását követően a legördülő listából válassza ki a már létrehozott **MyNetworkSecurityGroup** . 
   
   **A háttérbeli készlet kiválasztása** területen győződjön meg arról, hogy a **MyBackendPool** van kiválasztva. 

### <a name="create-an-nsg-rule-for-the-vms"></a>NSG-szabály létrehozása a virtuális gépekhez

Hozzon létre egy hálózati biztonsági csoport (NSG) szabályt a virtuális gépek számára a bejövő internetes (HTTP) kapcsolatok engedélyezéséhez.

>[!NOTE]
>Alapértelmezés szerint a NSG már rendelkezik egy olyan szabállyal, amely megnyitja a 3389-as portot, a távoli asztali (RDP-) portot.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **MyNetworkSecurityGroup** elemet a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz.
   
1. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. A **bejövő biztonsági szabály hozzáadása** párbeszédpanelen írja be vagy válassza ki a következőket:
   
   - **Forrás**: válassza ki a **szolgáltatás címkéjét**.  
   - **Forrásoldali szolgáltatás címkéje**: válassza az **Internet** lehetőséget. 
   - **Célport tartományai**: Type *80*.
   - **Protokoll**: válassza a **TCP** lehetőséget. 
   - **Művelet**: válassza az **Engedélyezés** lehetőséget.  
   - **Prioritás**: Type *100*. 
   - **Név**: írja be a *: myhttprule* nevet. 
   - **Leírás**: írja be a *http engedélyezése lehetőséget*. 
   
1. Válassza a **Hozzáadás** lehetőséget. 
   
   ![NSG-szabály létrehozása](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban ellenőrzi a terheléselosztó háttér-készletét, és konfigurálja a terheléselosztó állapotának mintavételi és forgalmi szabályait.

### <a name="view-the-back-end-address-pool"></a>A háttérként szolgáló címkészlet megtekintése

A virtuális gépek felé irányuló forgalom elosztásához a terheléselosztó egy háttér-címkészletet használ, amely tartalmazza a terheléselosztó számára csatlakozó virtuális hálózati adapterek (NIC-EK) IP-címeit. 

Létrehozta a terheléselosztó háttér-készletét, és hozzáadta a virtuális gépeket a virtuális gépek létrehozásakor. Létrehozhat háttér-készleteket is, valamint virtuális gépeket adhat hozzá vagy távolíthat el a terheléselosztó **háttér-készletek** oldaláról. 

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területen válassza a **háttér-készletek** elemet.
   
1. A **háttérbeli készletek** lapon bontsa ki a **MyBackendPool** elemet, és győződjön meg arról, hogy mind a **VM1** , mind a **VM2** szerepel.

1. Válassza a **MyBackendPool** lehetőséget. 
   
   A **MyBackendPool** lapon, a **virtuális gép** és az **IP-cím** területen távolítsa el vagy adja hozzá a készlethez rendelkezésre álló virtuális gépeket.

Az új háttér-készletek létrehozásához válassza a **Hozzáadás** lehetőséget a **háttérbeli készletek** lapon.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ha engedélyezni szeretné a terheléselosztó számára a virtuális gép állapotának figyelését, az állapot-mintavételt kell használnia. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. 

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
   
1. Az **állapotadatok hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyHealthProbe* nevet.
   - **Protokoll**: legördülő menüből válassza a **http** lehetőséget. 
   - **Port**: Type *80*. 
   - **Elérési út**: fogadja el */* az alapértelmezett URI-t. Ezt az értéket bármely más URI azonosítóval helyettesítheti. 
   - **Időköz**: Type *15*. Az intervallum a mintavételi kísérletek között eltelt másodpercek száma.
   - Nem megfelelő **állapot küszöbértéke**: Type *2*. Ez az érték azon egymást követő mintavételi hibák száma, amelyek a virtuális gép nem megfelelő állapotának tekintendők.
   
1. Válassza az **OK** lehetőséget.
   
   ![Mintavétel hozzáadása](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A szabály meghatározza a bejövő forgalom előtér-IP-konfigurációját, a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás-és célport eléréséhez. 

A **MyLoadBalancerRule** nevű terheléselosztó-szabály a 80-es portot figyeli az előtér- **LoadBalancerFrontEnd**. A szabály a 80-es porton is továbbítja a hálózati forgalmat a háttérbeli címkészlet **MyBackendPool**. 

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. A **terheléselosztási szabály hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyLoadBalancerRule* nevet.
   - **Protokoll**: válassza a **TCP** lehetőséget.
   - **Port**: Type *80*.
   - **Háttér-port**: Type *80*.
   - **Háttér-készlet**: válassza a **MyBackendPool** lehetőséget.
   - **Állapot**-mintavétel: válassza a **MyHealthProbe** lehetőséget. 
   
1. Válassza az **OK** lehetőséget.
   
   ![Terheléselosztási szabály hozzáadása](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Bejövő NAT-port létrehozása – továbbítási szabály

Hozzon létre egy terheléselosztó bejövő hálózati címfordítási (NAT-) szabályt, amely az előtér-IP-cím egy adott portjáról továbbítja a forgalmat a háttérbeli virtuális gép egy adott portjára.

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területen válassza a **bejövő NAT-szabályok** elemet, majd kattintson a **Hozzáadás** gombra. 
   
1. A **bejövő NAT-szabály hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyNATRuleVM1* nevet.
   - **Port**: Type *4221*.
   - **Cél virtuális gép**: válassza a **MyVM1** lehetőséget a legördülő menüből.
   - **Hálózati IP-konfiguráció**: válassza a **ipconfig1** lehetőséget a legördülő menüből.
   - **Port leképezése**: válassza az **Egyéni** lehetőséget.
   - **Célport: Type** *3389*.
   
1. Válassza az **OK** lehetőséget.
   
1. Ismételje meg a lépéseket egy *MyNATRuleVM2* nevű bejövő NAT-szabály hozzáadásához a következő **Port** használatával: *4222* és **cél virtuális gép**: **MyVM2**.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Ebben a szakaszban telepíti a Internet Information Services (IIS) szolgáltatást a háttér-kiszolgálókra, és testreszabja az alapértelmezett weblapot a gép nevének megjelenítéséhez. Ezután a terheléselosztó nyilvános IP-címét fogja használni a terheléselosztó teszteléséhez. 

Minden háttérbeli virtuális gép az alapértelmezett IIS-weblap egy másik verzióját szolgálja ki, így láthatja, hogy a terheléselosztó a két virtuális gép között osztja el a kérelmeket.

### <a name="connect-to-the-vms-with-rdp"></a>Kapcsolódás a virtuális gépekhez RDP használatával

Kapcsolódjon az egyes virtuális gépekhez Távoli asztal (RDP) használatával. 

1. A portálon válassza a **minden erőforrás** elemet a bal oldali menüben. Az erőforrások listájából válassza ki az egyes virtuális gépeket a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz.
   
1. Az **Áttekintés** lapon válassza a **kapcsolat**, majd az RDP- **Fájl letöltése** lehetőséget. 
   
1. Nyissa meg a letöltött RDP-fájlt, és válassza a **kapcsolat** lehetőséget.
   
1. A Windows rendszerbiztonság képernyőn válassza a **további lehetőségek lehetőséget** , majd **használjon másik fiókot**. 
   
   Adja meg a username *azureuser* és a Password *Azure1234567* nevet, majd kattintson **az OK gombra**.
   
1. Válaszoljon az **Igen** értékre bármelyik tanúsítványra. 
   
   A VM Desktop új ablakban nyílik meg. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Az IIS telepítése és az alapértelmezett IIS-weblap cseréje 

A PowerShell használatával telepítse az IIS-t, és cserélje le az alapértelmezett IIS-weblapot egy olyan oldalra, amely megjeleníti a virtuális gép nevét.

1. A MyVM1 és a MyVM2 alkalmazásban indítsa el a **Windows PowerShellt** a **Start** menüből. 

2. A következő parancsok futtatásával telepítse az IIS-t, és cserélje le az alapértelmezett IIS-weblapot:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Az MyVM1 és a MyVM2 RDP-kapcsolatok bezárásához válassza a **Leválasztás** lehetőséget. Ne állítsa le a virtuális gépeket.

### <a name="test-load-balancing"></a>Terheléselosztás tesztelése

1. A portálon, a MyLoadBalancer **Áttekintés** lapján másolja a nyilvános IP-címet a **nyilvános IP-cím** területen. Vigye a kurzort a címek fölé, és válassza a **Másolás** ikont a másoláshoz. Ebben a példában ez a **40.67.218.235**. 
   
1. Illessze be vagy írja be a terheléselosztó nyilvános IP-címét (*40.67.218.235*) az Internet böngésző címsorába. 
   
   Megjelenik a testreszabott IIS-webkiszolgáló alapértelmezett lapja a böngészőben. Az üzenet beolvassa **Hello World a MyVM1-ból**, vagy **Hello World a MyVM2**.
   
   ![Új IIS alapértelmezett lap](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Frissítse a böngészőt, és tekintse meg, hogy a terheléselosztó osztja el a forgalmat a virtuális gépek között. Előfordulhat, hogy a **MyVM1** lap megjelenik, és más esetekben a **MyVM2** oldal jelenik meg, mivel a terheléselosztó az egyes háttérbeli virtuális gépekhez továbbítja a kéréseket.
   
   >[!NOTE]
   >Előfordulhat, hogy törölnie kell a böngésző gyorsítótárát, vagy új böngészőablakot kell megnyitnia a kísérletek között.

## <a name="test-port-forwarding"></a>A porttovábbítás tesztelése

A port továbbítása révén a távoli asztal egy háttérbeli virtuális géphez a terheléselosztó IP-címének és a NAT-szabályban meghatározott előtér-port értékének használatával. 

1. A portálon, a MyLoadBalancer **Áttekintés** lapján másolja a nyilvános IP-címét. Vigye a kurzort a címek fölé, és válassza a **Másolás** ikont a másoláshoz. Ebben a példában ez a **40.67.218.235**. 
   
1. Nyisson meg egy parancssort, és a következő parancs használatával hozzon létre egy távoli asztali munkamenetet a MyVM2 használatával a terheléselosztó nyilvános IP-címének és a virtuális gép NAT-szabályában megadott előtér-portnak a segítségével. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Nyissa meg a letöltött RDP-fájlt, és válassza a **kapcsolat** lehetőséget.
   
1. A Windows rendszerbiztonság képernyőn válassza a **további lehetőségek lehetőséget** , majd **használjon másik fiókot**. 
   
   Adja meg a username *azureuser* és a Password *Azure1234567* nevet, majd kattintson **az OK gombra**.
   
1. Válaszoljon az **Igen** értékre bármelyik tanúsítványra. 
   
   A MyVM2 Desktop új ablakban nyílik meg. 

Az RDP-kapcsolat sikeres, mert a bejövő NAT-szabály a 4222-es **MyNATRuleVM2** -portról a 3389-es (RDP-port) MyVM2's-portra irányítja a forgalmat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A terheléselosztó és az összes kapcsolódó erőforrás törléséhez, ha már nincs szüksége rájuk, nyissa meg a **myresourcegrouplb erőforráscsoportban** erőforráscsoportot, és válassza az **erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy standard nyilvános Load balancert. Létrehozta és konfigurálta a hálózati erőforrásokat, a háttér-kiszolgálókat, az állapot-mintavételt és a terheléselosztó szabályait. Telepítette az IIS-t a háttérbeli virtuális gépeken, és a terheléselosztó nyilvános IP-címét használta a terheléselosztó teszteléséhez. A terheléselosztó megadott portjáról egy háttérbeli virtuális gép portjára állíthatja be és tesztelte a port továbbítását. 

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a terheléselosztási oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
