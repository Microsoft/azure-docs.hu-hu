---
title: Application gateway létrehozása több hely üzemeltetése – Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy application gateway, amelyen több helyet az Azure portal használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 53c816ee8fa670c8a4dde3212325524a17d25aab
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314432"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Application gateway létrehozása több hely üzemeltetése az Azure portal használatával

Az Azure Portalon való konfigurálásához használható [, több webhely üzemeltetése](application-gateway-multi-site-overview.md) létrehozásakor egy [az application gateway](application-gateway-introduction.md). Ebben az oktatóanyagban háttérkészletek virtuálisgép-méretezési csoportok használatával hoz létre. Ezután az Ön tulajdonában lévő tartományok alapján konfigurálhat figyelőket és szabályokat a webes forgalom a készletekben lévő megfelelő kiszolgálókra irányításához. Ez az oktatóanyag feltételezi, hogy Ön több tartománnyal rendelkezik. Példaként a *www.contoso.com* és a *www.fabrikam.com* tartományt használja.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek a háttérkiszolgálók létrehozása
> * Háttérkészletek a háttérkiszolgálók létrehozása
> * Figyelők és az útválasztási szabályok létrehozása
> * CNAME rekord létrehozása a tartományban

![Többhelyes útválasztási példa](./media/application-gateway-create-multisite-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Ön által létrehozott erőforrások közti kommunikációt egy virtuális hálózaton van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket az alkalmazásátjáróhoz:

    - Az alkalmazásátjáró neve *myAppGateway*.
    - Az új erőforráscsoport *myResourceGroupAG*.

    ![Új alkalmazásátjáró létrehozása](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
5. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

    - A virtuális hálózat neve *myVNet*.
    - A virtuális hálózat címtere *10.0.0.0/16*.
    - Az alhálózat neve *myAGSubnet*.
    - Az alhálózat címtere *10.0.0.0/24*.

    ![Virtuális hálózat létrehozása](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
7. Kattintson a **egy nyilvános IP-cím választása**, kattintson a **új létrehozása**, majd adja meg a nyilvános IP-cím nevére. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
8. Fogadja el az alapértelmezett értékeket, a figyelőt a konfigurációhoz, hagyja meg a webalkalmazás-tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd kattintson a **myVNet** lehetőségre az erőforráslistában.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. Adja meg a *myBackendSubnet* nevet az alhálózat neveként, majd kattintson az **OK** gombra.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában két virtuális gépet hozunk létre, amelyeket az alkalmazásátjáró háttérkiszolgálóiként fogunk használni. Az IIS-et is telepíti a virtuális gépeket, ellenőrizze, hogy a forgalom megfelelő útválasztási-e.

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** a kiemelt lista.
3. Adja meg a következő értékeket a virtuális gép számára:

    - *contosoVM* – a virtuális gép neve.
    - A rendszergazda felhasználóneve: *azureuser*.
    - A jelszó *Azure123456!* .
    - Válassza a **Meglévő használata**, majd a *myResourceGroupAG* lehetőséget.

4. Kattintson az **OK** gombra.
5. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
6. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva. 
7. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
8. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

### <a name="install-iis"></a>Az IIS telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Nyissa meg az interaktív felületet, és győződjön meg róla, hogy a **PowerShell** van beállítva.

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. A második virtuális gép létrehozása és az IIS telepítése a lépéseket, amelyek csak végzett használatával. Adja meg a nevei *fabrikamVM* nevét és a Set-AzVMExtension VMName értékét.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Háttérkészletek a virtuális gépek létrehozása

1. Kattintson a **összes erőforrás** majd **myAppGateway**.
2. Kattintson a **háttérkészletek**, és kattintson a **Hozzáadás**.
3. Adjon meg egy *contosoPool* , és adja hozzá *contosoVM* használatával **Hozzáadás cél**.

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. Kattintson az **OK** gombra.
5. Kattintson a **háttérkészletek** majd **Hozzáadás**.
6. Hozzon létre a *fabrikamPool* együtt a *fabrikamVM* használatával a lépéseket, amelyek az imént fejeződött be.

## <a name="create-listeners-and-routing-rules"></a>Figyelők és az útválasztási szabályok létrehozása

1. Kattintson a **figyelői** majd **többhelyes**.
2. Adja meg a figyelő ezeket az értékeket:
    
    - *contosoListener* - a figyelő nevét.
    - *www.contoso.com* -cserélje le a gazdagép neve példa a tartomány nevére.

3. Kattintson az **OK** gombra.
4. Hozzon létre egy második figyelő neve használatával *fabrikamListener* és használja a második tartomány nevét. Ebben a példában *www.fabrikam.com* szolgál.

A szabályok a felsorolásuk sorrendjében vannak feldolgozva, és a forgalom az első egyező szabály alapján lesz átirányítva, függetlenül annak sajátlagosságától. Ha például egy adott porton egy alapszintű figyelőt használó és egy többhelyes figyelőt használó szabály is aktív, a többhelyes figyelővel rendelkező szabályt az alapszintű figyelővel rendelkező elé kell venni, hogy a többhelyes szabály a várakozásnak megfelelően működjön. 

Ebben a példában két új szabályt hoz létre, és törli az alkalmazásátjáró létrehozásakor létrehozott alapértelmezett szabályt. 

1. Kattintson a **szabályok** majd **alapszintű**.
2. Adja meg *contosoRule* neve.
3. Válassza ki *contosoListener* a figyelőhöz.
4. Válassza ki *contosoPool* háttérkészlete számára.

    ![Útvonalalapú szabály létrehozása](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. Kattintson az **OK** gombra.
6. Hozzon létre egy második szabályt, a neveket *fabrikamRule*, *fabrikamListener*, és *fabrikamPool*.
7. Törölje az alapértelmezett szabály nevű *felhasználóval a rule1* , majd kattintás a **törlése**.

## <a name="create-a-cname-record-in-your-domain"></a>CNAME rekord létrehozása a tartományban

Az alkalmazásátjáró nyilvános IP-címmel történő létrehozása után lekérheti a DNS-címet, és a segítségével létrehozhat egy CNAME rekordot a tartományban. Az A rekordok használata nem javasolt, mivel a virtuális IP-cím változhat az alkalmazásátjáró újraindításakor.

1. Kattintson a **összes erőforrás**, és kattintson a **myAGPublicIPAddress**.

    ![Rekord application gateway DNS-cím](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. Másolja a DNS-címet, és használni értékét egy új CNAME-rekordot a tartományban.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Adja meg a tartománya nevét a böngésző címsorában. Például: http://www.contoso.com.

    ![Contoso webhely tesztelése az alkalmazásátjáróban](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. Változtassa meg a címet a másik tartományára. Ekkor az eredmény a következő példához hasonló:

    ![Fabrikam webhely tesztelése az alkalmazásátjáróban](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek a háttérkiszolgálók létrehozása
> * Háttérkészletek a háttérkiszolgálók létrehozása
> * Figyelők és az útválasztási szabályok létrehozása
> * CNAME rekord létrehozása a tartományban

> [!div class="nextstepaction"]
> [Ismerje meg részletesebben az alkalmazásátjárók lehetőségeit](application-gateway-introduction.md)
