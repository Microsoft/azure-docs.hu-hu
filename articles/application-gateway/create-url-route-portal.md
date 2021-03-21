---
title: 'Oktatóanyag: URL-útvonalon alapuló útválasztási szabályok a Portalon – Azure Application Gateway'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre URL-elérésiút-alapú útválasztási szabályokat egy Application Gateway és virtuálisgép-méretezési csoport számára a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: b0ab3cbd2891ef1677c0d4ba7a00821d67714b6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708951"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Oktatóanyag: Application Gateway létrehozása elérésiút-alapú útválasztási szabályokkal a Azure Portal használatával

Az [alkalmazás-átjáró](./overview.md)létrehozásakor a Azure Portal az [URL-elérésiút-alapú útválasztási szabályok](./url-route-overview.md) konfigurálására használható. Ebben az oktatóanyagban a háttér-készleteket virtuális gépek használatával hozza létre. Ezután olyan útválasztási szabályokat hozhat létre, amelyek gondoskodnak arról, hogy a webes forgalom a készletekben lévő megfelelő kiszolgálókon érkezzen.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek létrehozása háttérbeli kiszolgálókhoz
> * Háttér-készletek létrehozása a háttér-kiszolgálókkal
> * Háttér-figyelő létrehozása
> * Elérésiút-alapú útválasztási szabály létrehozása

![URL-útválasztási példa](./media/application-gateway-create-url-route-portal/scenario.png)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában három virtuális gépet hoz létre, amelyek háttér-kiszolgálóként szolgálnak az Application Gateway számára. Az IIS-t a virtuális gépeken is telepítheti annak ellenőrzéséhez, hogy az Application Gateway a várt módon működik-e.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. Válassza a **Windows Server 2016 Datacenter** elemet a népszerű listában.
3. Adja meg a következő értékeket a virtuális gép számára:

    - **Előfizetés** – válassza ki az előfizetését.
    - **Erőforráscsoport**, válassza az **új létrehozása** lehetőséget, majd írja be a következőt: *myResourceGroupAG*.
    - **Virtuális gép neve**: *myVM1*
    - Régió: *(USA) USA keleti* **régiója**
    - **Felhasználónév**: adjon meg egy felhasználónevet
    - **Password (jelszó**): írjon be egy jelszót


4. Válassza a **Tovább: lemezek** lehetőséget.
5. Válassza a **tovább lehetőséget: hálózatkezelés**
6. A **Virtual Network (virtuális hálózat**) területen válassza az **új létrehozása** elemet, majd írja be ezeket az értékeket a virtuális hálózatra:

   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - *myBackendSubnet* az első alhálózat neveként
   - *10.0.1.0/24* – az alhálózati címtartomány esetében.
   - *myAGSubnet* – a második alhálózat neveként.
   - Az alhálózat címtere *10.0.0.0/24*.
7. Válassza az **OK** lehetőséget.

8. Győződjön meg arról **, hogy az alhálózat területen az** alhálózat **myBackendSubnet** van kiválasztva, majd válassza a **Tovább: kezelés** lehetőséget.
9. A rendszerindítási diagnosztika letiltásához válassza a **Letiltás** lehetőséget.
10. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget.
11. Hozzon létre két további virtuális gépet, *myVM2* és *myVM3* , és helyezze őket a *MyVNet* virtuális hálózatba és a *myBackendSubnet* alhálózatba.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg az interaktív rendszerhéjt, és győződjön meg róla, hogy a **PowerShell**-re van beállítva.

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell
         $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. Telepítse az IIS-t a többi virtuális gépre az imént befejezett lépések használatával. *MyVM2* és *MyVM3* használata a set-AzVMExtension VMName értékeihez.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza az **erőforrás létrehozása** elemet a Azure Portal bal oldali menüjében. Megjelenik az **új** ablak.

2. Válassza a **hálózatkezelés** lehetőséget, majd a **Kiemelt** listában válassza a **Application Gateway** lehetőséget.

### <a name="basics-tab"></a>Alapbeállítások lap

1. Az **alapok** lapon adja meg a következő Application Gateway-beállításokhoz tartozó értékeket:

   - **Előfizetés**: Válassza ki előfizetését.
   - **Erőforráscsoport**: válassza ki az erőforráscsoport **myResourceGroupAG** .
   - **Application Gateway neve**: írja be a *myAppGateway* nevet az Application Gateway neveként.
   - Régió – válassza ki az USA **keleti** **régióját** .

        ![Új Application Gateway létrehozása: alapismeretek](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  A **virtuális hálózat konfigurálása** területen válassza a **myVNet** lehetőséget a virtuális hálózat nevéhez.
3. Válassza ki a **myAGSubnet** az alhálózathoz.
3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson a **Next (tovább) gombra: frontends**.

### <a name="frontends-tab"></a>Frontendek lap

1. A **frontendek** lapon ellenőrizze, hogy a előtérbeli **IP-cím típusa** **nyilvános** értékre van-e állítva.

   > [!NOTE]
   > A Application Gateway v2 SKU esetében csak **nyilvános** ELŐTÉRBELI IP-konfigurációt választhat. A privát előtérbeli IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 SKU-hoz.

2. Válassza a **nyilvános IP-cím** **új hozzáadása** lehetőséget, és adja meg a *myAGPublicIPAddress* a nyilvános IP-cím neveként, majd kattintson **az OK gombra**. 
3. Válassza a Next (tovább) lehetőséget **: háttérrendszer**.

### <a name="backends-tab"></a>Háttérrendszer lap

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttér-készletek a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek, a teljes tartománynevek (FQDN) és a több-bérlős háttér-végpontok, például a Azure App Service tagjai lehetnek.

1. A **backends (háttérrendszer** ) lapon válassza a **háttérbeli készlet hozzáadása** elemet.

2. A megnyíló **háttérbeli készlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérbeli készlet létrehozásához:

    - **Név**: adja meg a *myBackendPool* nevét a háttér-készlet neveként.
3. A **cél típusa** területen válassza a **virtuális gép** lehetőséget a legördülő listából.

5. A **cél** területen válassza ki a **myVM1** hálózati adapterét.
6. Válassza a **Hozzáadás** lehetőséget.
7. Ismételje meg a *képet* , hogy a *myVM2* , mint a cél, és egy *videó* -háttérbeli készletet adjon hozzá a *myVM3* -hez.
8. Válassza a **Hozzáadás** lehetőséget a háttérbeli készlet konfigurációjának mentéséhez, és térjen vissza a **háttérrendszer** lapra.

4. A **háttérrendszer** lapon válassza a **Tovább: konfigurálás** lehetőséget.

### <a name="configuration-tab"></a>Konfiguráció lap

A **konfiguráció** lapon összekapcsolja az útválasztási szabály használatával létrehozott előtér-és háttér-készletet.

1. Válassza az útválasztási **szabály hozzáadása** lehetőséget az **útválasztási szabályok** oszlopban.

2. A megnyíló **útválasztási szabály hozzáadása** ablakban írja be a *MyRoutingRule* nevet a **szabály neveként**.

3. Egy útválasztási szabályhoz egy figyelő szükséges. Az **útválasztási szabály hozzáadása** ablak **figyelő** lapján írja be a figyelő következő értékeit:

    - **Figyelő neve**: írja be a *myListener* nevet a figyelőnek.
    - Előtér **-IP**: válassza a **nyilvános** lehetőséget, hogy kiválassza a előtérhez létrehozott nyilvános IP-címet.
    - **Port**: Type *8080*
  
        Fogadja el az alapértelmezett értékeket a **figyelő** lapon a többi beállításnál, majd válassza a **háttérbeli célok** fület a többi útválasztási szabály konfigurálásához.

4. A **háttérbeli célok** lapon válassza a **MyBackendPool** lehetőséget a **háttérbeli célként**.

5. A **http-beállításnál** válassza az **új hozzáadása** lehetőséget az új http-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. 

6. A megnyíló **http-beállítás hozzáadása** ablakban írja be a *MyHTTPSetting* nevet a **http-beállítás neveként**. Fogadja el az alapértelmezett értékeket a további beállításokhoz a **http-beállítás hozzáadása** ablakban, majd válassza a **Hozzáadás** lehetőséget az **útválasztási szabály hozzáadása** ablakhoz való visszatéréshez.
7. Az **elérésiút-alapú útválasztás** területen válassza **a több cél hozzáadása lehetőséget egy elérésiút-alapú szabály létrehozásához**.
8. Az **elérési út** mezőbe írja be a következőt: */images/* \* .
9. A **cél neve** mezőbe írja be a *képek* nevet.
10. **Http-beállítás** esetén válassza a **myHTTPSetting** lehetőséget.
11. A **háttérbeli cél** beállításnál válassza a **lemezképek** lehetőséget.
12. A **Hozzáadás** elemre kattintva mentse az elérésiút-szabályt, és térjen vissza az **útválasztási szabály hozzáadása** lapra.
13. Újabb szabály hozzáadásához ismételje meg a videót.
14. Válassza a **Hozzáadás** lehetőséget az útválasztási szabály hozzáadásához és a **konfiguráció** laphoz való visszatéréshez.
15. Válassza a **Next (tovább** ) gombot, majd a **következőt: felülvizsgálat + létrehozás**.

> [!NOTE]
> Az */* alapértelmezett esetek kezeléséhez nem szükséges egyéni * elérésiút-szabályt hozzáadnia. Ezt automatikusan az alapértelmezett háttérrendszer-készlet kezeli.

### <a name="review--create-tab"></a>Felülvizsgálat + Létrehozás lap

Tekintse át a **felülvizsgálat + létrehozás** lapon található beállításokat, majd válassza a **Létrehozás** lehetőséget a virtuális hálózat, a nyilvános IP-cím és az Application Gateway létrehozásához. Az Azure az Application Gateway létrehozásához több percet is igénybe vehet. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.


## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Válassza a **minden erőforrás** lehetőséget, majd válassza a **myAppGateway** lehetőséget.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például, http: \/ /52.188.72.175:8080.

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Az 8080-as porton lévő figyelő a kérést az alapértelmezett háttér-készletre irányítja.

3. Módosítsa az URL-címet a *http:// &lt; IP-cím &gt; : 8080/images/test.htm* értékre, és cserélje le az IP-címet az IP-címére &lt; &gt; , és az alábbi példához hasonlóan kell megjelennie:

    ![Tesztképek URL-címe az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   A 8080-es porton lévő figyelő továbbítja ezt a kérést a *lemezképek* háttér-készletének.

4. Módosítsa az URL-címet a *http:// &lt; IP-cím &gt; : 8080/video/test.htm* értékre, és cserélje le az IP-címet az IP-címére &lt; &gt; , és az alábbi példához hasonlóan kell megjelennie:

    ![Tesztvideó URL-címe az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   A 8080-es porton lévő figyelő továbbítja ezt a kérést a *video* backend-készletnek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez válassza ki az erőforráscsoportot, majd válassza az **erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Végpontok közötti TLS engedélyezése az Azure-Application Gateway](./ssl-overview.md)