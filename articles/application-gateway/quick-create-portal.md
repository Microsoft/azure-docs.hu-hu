---
title: 'Gyors útmutató: webes forgalom közvetlen használata a portálon'
titleSuffix: Azure Application Gateway
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható a Azure Portal egy olyan Azure-Application Gateway létrehozásához, amely egy háttér-készletben lévő virtuális gépekre irányítja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: c1b17d8c624d4bef74278acc24ece37a736a5ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572969"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal

Ebben a rövid útmutatóban a Azure Portal használatával hozhat létre Application Gateway-t. Ezt követően ellenőrizze, hogy megfelelően működik-e. 

Az Application Gateway az alkalmazás webes forgalmát egy háttér-készlet adott erőforrásaira irányítja. A figyelőket hozzárendelheti a portokhoz, szabályokat hozhat létre, és erőforrásokat adhat hozzá egy háttér-készlethez. Az egyszerűség kedvéért ez a cikk egy egyszerű telepítőt használ egy nyilvános előtér-IP-címmel, egy alapszintű figyelővel, amely egyetlen helyet üzemeltet az Application gatewayben, egy alapszintű kérelem-útválasztási szabályt és két virtuális gépet a háttér-készletben.

Ezt a rövid útmutatót [Azure PowerShell](quick-create-powershell.md) vagy az [Azure CLI](quick-create-cli.md)használatával is elvégezheti.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Application Gateway-t az **Application Gateway létrehozása** lap lapfülei segítségével hozhatja létre.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. Megjelenik az **új** ablak.

2. Válassza a **hálózatkezelés** lehetőséget, majd a **Kiemelt** listában válassza a **Application Gateway** lehetőséget.

### <a name="basics-tab"></a>Alapbeállítások lap

1. Az **alapok** lapon adja meg a következő Application Gateway-beállításokhoz tartozó értékeket:

   - **Erőforráscsoport**: válassza ki az erőforráscsoport **myResourceGroupAG** . Ha nem létezik, válassza az **új létrehozása** elemet a létrehozásához.
   - **Application Gateway neve**: írja be a *myAppGateway* nevet az Application Gateway neveként.

     ![Új Application Gateway létrehozása: alapismeretek](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni az Application Gateway létrehozásakor. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

    > [!NOTE]
    > A [virtuális hálózati szolgáltatás végponti házirendjei](../virtual-network/virtual-network-service-endpoint-policies-overview.md) jelenleg nem támogatottak Application Gateway alhálózatban.

    A **virtuális hálózat konfigurálása** területen hozzon létre egy új virtuális hálózatot az **új létrehozása** lehetőség kiválasztásával. A megnyíló **virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név**: írja be a *myVNet* nevet a virtuális hálózat nevéhez.

    - **Alhálózat neve** (Application Gateway alhálózat): az **alhálózatok** rácsa egy *alapértelmezett* nevű alhálózatot fog megjeleníteni. Módosítsa az alhálózat nevét a *myAGSubnet* értékre.<br>Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttérbeli kiszolgáló alhálózata): az **alhálózatok** rácsának második sorában adja meg az *myBackendSubnet* értéket az **alhálózat neve** oszlopban.

    - **Címtartomány** (háttér-kiszolgáló alhálózata): az **alhálózatok** rácsának második sorában olyan címtartományt adjon meg, amely nem fedi át a *myAGSubnet* címtartomány-tartományát. Ha például a *myAGSubnet* 10.0.0.0/24, a *10.0.1.0/24* értéket adja meg a *myBackendSubnet*-tartományhoz.

    A **virtuális hálózat létrehozása** ablak bezárásához és a virtuális hálózat beállításainak mentéséhez kattintson **az OK gombra** .

     ![Új Application Gateway létrehozása: virtuális hálózat](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Az **alapvető** beállítások lapon fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **Next (tovább): frontends** elemet.

### <a name="frontends-tab"></a>Frontendek lap

1. A **frontendek** lapon ellenőrizze, hogy a előtérbeli **IP-cím típusa** **nyilvános** értékre van-e állítva. <br>Az előtérbeli IP-címet úgy állíthatja be, hogy a használati esetnek megfelelően nyilvános vagy privát legyen. Ebben a példában egy nyilvános előtérbeli IP-címet választ.
   > [!NOTE]
   > A Application Gateway v2 SKU esetében **nyilvános** ELŐTÉRBELI IP-konfigurációnak kell lennie. Továbbra is rendelkezhet nyilvános és privát előtéri IP-konfigurációval is, de a csak privát előtér-IP-konfiguráció (csak ILB mód) jelenleg nincs engedélyezve a v2 SKU-hoz. 

2. Válassza a **nyilvános IP-cím** **új hozzáadása** lehetőséget, és adja meg a *myAGPublicIPAddress* a nyilvános IP-cím neveként, majd kattintson **az OK gombra**. 

     ![Új Application Gateway létrehozása: frontendek](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Válassza a Next (tovább) lehetőséget **: háttérrendszer**.

### <a name="backends-tab"></a>Háttérrendszer lap

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttér-készletek a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek, a teljes tartománynevek (FQDN) és a több-bérlős háttér-végpontok, például a Azure App Service tagjai lehetnek. Ebben a példában egy üres háttér-készletet hozunk létre az Application Gateway használatával, majd a háttérbeli célokat hozzá kell adni a háttér-készlethez.

1. A **backends (háttérrendszer** ) lapon válassza a **háttérbeli készlet hozzáadása** elemet.

2. A megnyíló **háttérbeli készlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérbeli készlet létrehozásához:

    - **Név**: adja meg a *myBackendPool* nevét a háttér-készlet neveként.
    - **Háttérbeli készlet hozzáadása célok nélkül**: válassza az **Igen** lehetőséget, ha célokat nem tartalmazó háttér-készletet szeretne létrehozni. Az Application Gateway létrehozása után hozzá kell adni a háttérbeli célokat.

3. A háttérbeli **készlet hozzáadása** ablakban válassza a **Hozzáadás** elemet a háttérbeli készlet konfigurációjának mentéséhez, és térjen vissza a **háttérrendszer** lapra.

     ![Új Application Gateway létrehozása: háttérrendszer](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. A **háttérrendszer** lapon válassza a **Tovább: konfigurálás** lehetőséget.

### <a name="configuration-tab"></a>Konfiguráció lap

A **konfiguráció** lapon összekapcsolja az útválasztási szabály használatával létrehozott előtér-és háttér-készletet.

1. Válassza az útválasztási **szabály hozzáadása** lehetőséget az **útválasztási szabályok** oszlopban.

2. A megnyíló **útválasztási szabály hozzáadása** ablakban írja be a *MyRoutingRule* nevet a **szabály neveként**.

3. Egy útválasztási szabályhoz egy figyelő szükséges. Az **útválasztási szabály hozzáadása** ablak **figyelő** lapján adja meg az alábbi értékeket a figyelőhöz:

    - **Figyelő neve**: írja be a *myListener* nevet a figyelőnek.
    - Előtér **-IP**: válassza a **nyilvános** lehetőséget, hogy kiválassza a előtérhez létrehozott nyilvános IP-címet.
  
      Fogadja el az alapértelmezett értékeket a **figyelő** lapon a többi beállításnál, majd válassza a **háttérbeli célok** fület a többi útválasztási szabály konfigurálásához.

   ![Új Application Gateway létrehozása: figyelő](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. A **háttérbeli célok** lapon válassza a **MyBackendPool** lehetőséget a **háttérbeli célként**.

5. A **http-beállításnál** válassza az **új hozzáadása** lehetőséget az új http-beállítás hozzáadásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A megnyíló **http-beállítás hozzáadása** ablakban írja be a *MyHTTPSetting* nevet a **http-beállítás neveként** és a *80* értéket a **háttér-porthoz**. Fogadja el az alapértelmezett értékeket a további beállításokhoz a **http-beállítás hozzáadása** ablakban, majd válassza a **Hozzáadás** lehetőséget az **útválasztási szabály hozzáadása** ablakhoz való visszatéréshez. 

     ![Új Application Gateway létrehozása: HTTP-beállítás](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Az útválasztási szabály **hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéséhez és a **konfiguráció** laphoz való visszatéréshez.

     ![Új Application Gateway létrehozása: útválasztási szabály](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Válassza a **Next (tovább** ) gombot, majd a **következőt: felülvizsgálat + létrehozás**.

### <a name="review--create-tab"></a>Felülvizsgálat + Létrehozás lap

Tekintse át a **felülvizsgálat + létrehozás** lapon található beállításokat, majd válassza a **Létrehozás** lehetőséget a virtuális hálózat, a nyilvános IP-cím és az Application Gateway létrehozásához. Az Azure az Application Gateway létrehozásához több percet is igénybe vehet. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

## <a name="add-backend-targets"></a>Háttérbeli célok hozzáadása

Ebben a példában a virtuális gépeket célként használt háttérként fogja használni. Használhat meglévő virtuális gépeket, vagy újakat is létrehozhat. Hozzon létre két virtuális gépet háttér-kiszolgálóként az Application Gateway számára.

Ehhez a következőket kell tennie:

1. Hozzon létre két új virtuális gépet, *myVM* és *myVM2* a háttér-kiszolgálóként való használathoz.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.
3. Adja hozzá a háttér-kiszolgálókat a háttér-készlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. Megjelenik az **új** ablak.
2. Válassza a **Windows Server 2016 Datacenter** elemet a **népszerű** listában. Megjelenik a **virtuális gép létrehozása** lap.<br>Application Gateway átirányíthatja a forgalmat a háttér-készletben használt bármilyen típusú virtuális gépre. Ebben a példában egy Windows Server 2016 Datacenter rendszerű virtuális gépet használ.
3. Adja meg ezeket az értékeket az **alapok** lapon a következő virtuálisgép-beállításokhoz:

    - **Erőforráscsoport**: válassza ki a **myResourceGroupAG** az erőforráscsoport neveként.
    - **Virtuális gép neve**: írja be a *myVM* nevet a virtuális gép nevéhez.
    - **Régió**: válassza ki ugyanazt a régiót, ahol az Application Gatewayt létrehozta.
    - **Felhasználónév**: írja be a rendszergazda felhasználóneve nevét.
    - **Password (jelszó**): írjon be egy jelszót.
    - **Nyilvános bejövő portok**: nincs.
4. Fogadja el a többi alapértelmezett értéket, majd válassza a **Next: Disks** elemet.  
5. Fogadja el a **lemezek** lap alapértelmezett értékeit, majd kattintson a **Tovább gombra: hálózatkezelés** elemre.
6. A **hálózatkezelés** lapon ellenőrizze, hogy a **virtuális hálózat** **myVNet** van-e kiválasztva, és az **alhálózat** **myBackendSubnet** értékre van-e állítva. Fogadja el a többi alapértelmezett értéket, majd válassza a **Tovább: kezelés** lehetőséget.<br>A Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de gondoskodnia kell az IP-kapcsolatról.
7. A **felügyelet** lapon **Tiltsa le** a **rendszerindítási diagnosztika** beállítást. Fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.
8. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, majd válassza a **Létrehozás** lehetőséget.
9. A folytatás előtt várja meg, amíg a virtuális gép létrehozása befejeződik.

### <a name="install-iis-for-testing"></a>Az IIS telepítése teszteléshez

Ebben a példában az IIS-t telepíti a virtuális gépekre annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta az Application Gatewayt.

1. Nyissa meg Azure PowerShell.

   Válassza ki a **Cloud Shell** elemet a Azure Portal felső navigációs sávján, majd válassza a **PowerShell** elemet a legördülő listából. 

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. A következő parancs futtatásával telepítse az IIS-t a virtuális gépre. Szükség esetén módosítsa a *Location* paramétert: 

    ```azurepowershell
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban befejezett lépések segítségével. Használja a *myVM2* a virtuális gép nevéhez és a **set-AzVMExtension** parancsmag **VMName** beállításához.

### <a name="add-backend-servers-to-backend-pool"></a>Háttér-kiszolgálók hozzáadása a háttérbeli készlethez

1. A Azure Portal menüben válassza a **minden erőforrás** lehetőséget, vagy keresse meg és válassza ki az *összes erőforrás* elemet. Ezután válassza a **myAppGateway** lehetőséget.

2. Válassza ki a **háttér-készletek** elemet a bal oldali menüben.

3. Válassza a **myBackendPool** lehetőséget.

4. A **háttérbeli célok**, **cél típusa** területen válassza a **virtuális gép** lehetőséget a legördülő listából.

5. A **cél** területen válassza ki a **myVM** és **myVM2** virtuális gépeket, valamint a hozzájuk társított hálózati adaptereket a legördülő listából.

   > [!div class="mx-imgBorder"]
   > ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

7. Várjon, amíg a telepítés befejeződik, mielőtt továbblép a következő lépésre.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az IIS nem szükséges az Application Gateway létrehozásához, ezt a rövid útmutatóban telepítette annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Application Gatewayt. 

Az IIS használata az Application Gateway teszteléséhez:

1. Keresse meg az Application Gateway nyilvános IP-címét az **Áttekintés** oldalon. ![ Az Application Gateway nyilvános IP-címének rögzítése ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) vagy a **minden erőforrás** lehetőség kiválasztásával megadhatja a *myAGPublicIPAddress* kifejezést a keresőmezőbe, majd kiválaszthatja azt a keresési eredmények között. Az Azure megjeleníti a nyilvános IP-címet az **Áttekintés** oldalon.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába az IP-cím megkereséséhez.
3. Keresse meg a választ. Egy érvényes válasz ellenőrzi, hogy az Application Gateway sikeresen létrejött-e, és sikeresen tud-e kapcsolatot létesíteni a háttérrel.

   ![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Többször is frissítse a böngészőt, és látnia kell a myVM és a myVM2 kapcsolatait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, törölje az erőforráscsoportot. Az erőforráscsoport törlésekor az Application Gateway és az összes kapcsolódó erőforrás is törlődik.

Az erőforráscsoport törlése:

1. A Azure Portal menüben válassza az **erőforráscsoportok** lehetőséget, vagy keresse meg és válassza az *erőforráscsoportok* lehetőséget.
2. Az **erőforráscsoportok** lapon keresse meg a **myResourceGroupAG** a listában, majd válassza ki azt.
3. Az **erőforráscsoport lapon** válassza az **erőforráscsoport törlése** elemet.
4. Írja be a *myResourceGroupAG* **nevet az erőforráscsoport neve mezőbe** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Application Gateway konfigurálása TLS-megszakítással a Azure Portal használatával](create-ssl-portal.md)
