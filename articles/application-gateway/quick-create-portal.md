---
title: 'Rövid útmutató: Webes forgalom közvetlen használata a portál használatával'
titleSuffix: Azure Application Gateway
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre a Azure Portal használatával olyan Azure Application Gateway, amely a háttérkészletben lévő virtuális gépekre irányítja a webes forgalmat.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/19/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 92e966f5a9c4a21a6a55862ad582f0238802720f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538568"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal

Ebben a rövid útmutatóban a Azure Portal egy Alkalmazásátjárót hoz létre. Ezután tesztelje, hogy megfelelően működik-e. 

Az Application Gateway a háttérkészlet adott erőforrásaihoz irányítja az alkalmazás webes forgalmát. Figyelőket rendelhet a portokhoz, szabályokat hozhat létre, és erőforrásokat adhat a háttérkészlethez. Az egyszerűség kedvéért ez a cikk egy egyszerű beállítást használ nyilvános előtere IP-címmel, egy alapszintű listenerrel, amely egyetlen helyet kezel az alkalmazásátjárón, egy egyszerű kérés-útválasztási szabályt és két virtuális gépet a háttérkészletben.

A rövid útmutatót az Azure CLI vagy a [Azure PowerShell](quick-create-powershell.md) [használatával is befejezheti.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Alkalmazásátjáró létrehozása lapfülek használatával fogja létrehozni **az Alkalmazásátjárót.**

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. **Megjelenik** az Új ablak.

2. Válassza **a Hálózat lehetőséget,** majd **Application Gateway** a Kiemelt **listában.**

### <a name="basics-tab"></a>Alapvető beállítások lap

1. Az Alapvető **beállítások** lapon adja meg ezeket az értékeket az Application Gateway következő beállításaihoz:

   - **Erőforráscsoport:** Válassza ki **a myResourceGroupAG** erőforráscsoportot. Ha még nem létezik, válassza az **Új létrehozása** lehetőséget a létrehozásához.
   - **Application Gateway neve:** Az Alkalmazásátjáró neveként adja meg a *myAppGateway* nevet.

     ![Új Application Gateway létrehozása: Alapvető ismeretek](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni az Application Gateway létrehozásakor. Application Gateway példányok külön alhálózatban vannak létrehozva. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz.

    > [!NOTE]
    > [A virtuális hálózati szolgáltatásvégpont-szabályzatok](../virtual-network/virtual-network-service-endpoint-policies-overview.md) jelenleg nem támogatottak Application Gateway alhálózaton.

    A **Virtuális hálózat konfigurálása alatt** hozzon létre egy új virtuális hálózatot az Új létrehozása lehetőség **kiválasztásával.** A **megnyíló Virtuális hálózat** létrehozása ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név:** A virtuális hálózat neveként adja meg a *myVNet* nevet.

    - **Alhálózat neve** (Application Gateway alhálózat): **Az** Alhálózatok rács egy Default nevű alhálózatot fog *mutatni.* Módosítsa az alhálózat nevét a *következőre: myAGSubnet.*<br>Az Application Gateway alhálózata csak alkalmazásátjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttérkiszolgáló alhálózata): Az Alhálózatok rács második sorában adja meg a *myBackendSubnet* nevet az **Alhálózat neve oszlopban.** 

    - **Címtartomány** (háttérkiszolgáló alhálózata): Az Alhálózatok rács második sorában adjon meg egy olyan címtartományt, amely nincs átfedésben a *myAGSubnet címtartományával.*  Ha például a *myAGSubnet* címtartománya 10.0.0.0/24, adja meg a *10.0.1.0/24* címet a *myBackendSubnet címtartományában.*

    Kattintson **az OK** gombra a Virtuális hálózat létrehozása ablak **bezárásához** és a virtuális hálózat beállításainak mentéséhez.

     ![Új Application Gateway létrehozása: virtuális hálózat](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Az Alapvető **beállítások lapon** fogadja el a többi beállítás alapértelmezett értékeit, majd válassza a **Tovább: Előterek lehetőséget.**

### <a name="frontends-tab"></a>Előtereik lap

1. Az **Előterek lapon** ellenőrizze, hogy az **Előtere IP-címtípusa** Nyilvános **beállításra van-e állítva.** <br>Az előtere IP-címét konfigurálhatja nyilvánosra vagy privátra a saját használatának megfelelően. Ebben a példában egy nyilvános előtere IP-címet fog választani.
   > [!NOTE]
   > A Application Gateway v2 termékváltozathoz nyilvános előtere IP-konfigurációnak kell lennie.  Továbbra is lehet nyilvános és privát előtere IP-konfiguráció is, de a csak magánhálózati IP-konfiguráció (csak ILB mód) jelenleg nincs engedélyezve a v2 termékváltozathoz. 

2. A **Nyilvános IP-cím mezőben** válassza az Új hozzáadása lehetőséget, a nyilvános IP-cím neveként adja meg a *myAGPublicIPAddress* nevet, majd kattintson az **OK gombra.**  

     ![Új Application Gateway létrehozása: előterek](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Válassza **a Tovább: Háttér háttér lehetőséget.**

### <a name="backends-tab"></a>Háttéreszközök lap

A háttérkészlet arra szolgál, hogy a kéréseket a kérést kiszolgáló háttérkiszolgálókhoz irányítsa. A háttérkészletek tagjai hálózati számítógépek, virtuálisgép-méretezési készletek, nyilvános IP-címek, belső IP-címek, teljes tartománynevek (FQDN) és több-bérlős háttérkiszolgálók, például a Azure App Service. Ebben a példában létre fog hozni egy üres háttérkészletet az alkalmazásátjáróval, majd háttércélokat ad a háttérkészlethez.

1. A **Háttérkészletek lapon válassza** **a Háttérkészlet hozzáadása lehetőséget.**

2. A **megnyíló Háttérkészlet** hozzáadása ablakban adja meg a következő értékeket egy üres háttérkészlet létrehozásához:

    - **Név:** Adja meg *a myBackendPool* nevet a háttérkészlet neveként.
    - **Háttérkészlet hozzáadása célok** nélkül: Válassza az **Igen** lehetőséget egy cél nélküli háttérkészlet létrehozásához. Az Alkalmazásátjáró létrehozása után háttércélokat fog hozzáadni.

3. A **Háttérkészlet hozzáadása** ablakban válassza  a Hozzáadás lehetőséget a háttérkészlet konfigurációjának mentésére és a Háttérkészletek **lapra való visszatéréshez.**

     ![Új Application Gateway létrehozása: háttéralkalmazások](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. A **Háttéreszközök lapon** válassza a **Tovább: Konfiguráció lehetőséget.**

### <a name="configuration-tab"></a>Konfiguráció lap

A Konfiguráció **lapon** egy útválasztási szabály használatával fogja csatlakoztatni a létrehozott elő- és háttérkészletet.

1. Az **Útválasztási szabályok oszlopban válassza** az **Útválasztási szabály hozzáadása** lehetőséget.

2. A **megnyíló Útválasztási szabály hozzáadása** ablakban adja meg a *myRoutingRule* nevet **a Szabály neveként.**

3. Az útválasztási szabályokhoz listener szükséges. Az **Útválasztási szabály hozzáadása** ablak Figyelő lapján adja meg **a** következő értékeket a listener számára:

    - **Figyelő neve:** A listener neveként adja meg a *myListener* nevet.
    - **Előtere IP-címe:** Válassza a **Nyilvános** lehetőséget az előtere számára létrehozott nyilvános IP-cím kiválasztásához.
  
      Fogadja el a Figyelés  lapon található többi  beállítás alapértelmezett értékeit, majd válassza a Háttércélok lapot az útválasztási szabály további beállításainak konfigurálhoz.

   ![Új Application Gateway létrehozása: listener](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. A **Háttércélok lapon** válassza a **myBackendPool** háttércélt. 

5. A **HTTP-beállításnál válassza** az **Új hozzáadása lehetőséget** egy új HTTP-beállítás hozzáadásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A **megnyíló HTTP-beállítás** hozzáadása ablakban adja meg a *myHTTPSetting* nevet a **HTTP-beállítás** neveként, a háttérportnál pedig a *80-at.*  Fogadja el az alapértelmezett értékeket a **HTTP-beállítás** hozzáadása  ablakban található többi beállításhoz, majd válassza a Hozzáadás lehetőséget az Útválasztási szabály **hozzáadása ablakba való visszatéréshez.** 

     ![Új Application Gateway létrehozása: HTTP-beállítás](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Az **Útválasztási szabály hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéshez és a **Konfiguráció lapra való visszatéréshez.**

     ![Új Application Gateway létrehozása: útválasztási szabály](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Válassza **a Tovább: Címkék,** majd **a Tovább: Áttekintés + létrehozás lehetőséget.**

### <a name="review--create-tab"></a>Áttekintés és létrehozás lap

Tekintse át a beállításokat a Felülvizsgálat  **+ létrehozás** lapon, majd válassza a Létrehozás lehetőséget a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az Alkalmazásátjáró létrehozása eltarthat néhány percig. Várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

## <a name="add-backend-targets"></a>Háttércélok hozzáadása

Ebben a példában virtuális gépeket fog használni cél háttérrendszerként. Használhat meglévő virtuális gépeket, vagy létrehozhat újakat. Két virtuális gépet fog háttérkiszolgálóként létrehozni az Alkalmazásátjáróhoz.

Ehhez a következőt kell megtennie:

1. Hozzon létre két új virtuális gép, *a myVM* és a *myVM2* virtuális gépeket, amelyek háttérkiszolgálóként szolgálnak.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.
3. Adja hozzá a háttérkiszolgálókat a háttérkészlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. **Megjelenik** az Új ablak.
2. A Népszerű listában válassza a **Windows Server 2016 Datacenter** **lehetőséget.** Megjelenik **a Virtuális gép létrehozása** lap.<br>Application Gateway forgalom bármilyen, a háttérkészletben használt virtuális géphez irányítható. Ebben a példában egy Windows Server 2016 Datacenter virtuális gépet használ.
3. Adja meg ezeket az értékeket az **Alapvető beállítások** lapon a következő virtuálisgép-beállításokhoz:

    - **Erőforráscsoport:** Az erőforráscsoport neveként válassza a **myResourceGroupAG** lehetőséget.
    - **Virtuális gép neve:** A virtuális gép neveként adja meg a *myVM* nevet.
    - **Régió:** Válassza ki ugyanazt a régiót, amelyben az Alkalmazásátjárót létrehozta.
    - **Felhasználónév:** Adja meg a rendszergazda felhasználónevét.
    - **Jelszó:** Adjon meg egy jelszót.
    - **Nyilvános bejövő portok:** Nincs.
4. Fogadja el a többi alapértelmezett értéket, majd válassza **a Tovább: Lemezek lehetőséget.**  
5. Fogadja el **a Lemezek lap alapértelmezett** beállítását, majd válassza a **Tovább: Hálózatkezelés lehetőséget.**
6. A **Hálózat lapon** ellenőrizze, hogy a **myVNet** van-e kiválasztva a **Virtuális** hálózat mezőben, és hogy az **Alhálózat** beállítása **myBackendSubnet.** Fogadja el a többi alapértelmezett értéket, majd válassza **a Tovább: Felügyelet lehetőséget.**<br>Application Gateway virtuális hálózaton kívüli példányokkal is tud kommunikálni, de gondoskodnia kell az IP-kapcsolatról.
7. A Felügyelet **lapon** állítsa a **Rendszerindítási diagnosztika adatokat** **Letiltás beállításra.** Fogadja el a többi alapértelmezett értéket, majd válassza a **Felülvizsgálat + létrehozás lehetőséget.**
8. Az Áttekintés **+ létrehozás lapon** tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, majd válassza a Létrehozás **lehetőséget.**
9. A folytatás előtt várja meg, amíg a virtuális gép létrehozása befejeződik.

### <a name="install-iis-for-testing"></a>Az IIS telepítése teszteléshez

Ebben a példában telepíti az IIS-t a virtuális gépekre annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Alkalmazásátjárót.

1. Nyissa meg Azure PowerShell.

   A **Cloud Shell** navigációs sávján válassza a Azure Portal, majd a legördülő listából válassza a **PowerShell** lehetőséget. 

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS virtuális gépre való telepítéséhez. Ha *szükséges,* módosítsa a Location paramétert: 

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

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban befejezett lépésekkel. A virtuális gép neveként használja a *myVM2* virtuális gépet, a **Set-AzVMExtension** parancsmag **VMName** beállítását pedig.

### <a name="add-backend-servers-to-backend-pool"></a>Háttérkiszolgálók hozzáadása a háttérkészlethez

1. A Azure Portal válassza a **Minden erőforrás** lehetőséget, vagy keresse meg és válassza a Minden erőforrás *lehetőséget.* Ezután válassza **a myAppGateway lehetőséget.**

2. A **bal oldali menüben válassza** a Háttérkészletek lehetőséget.

3. Válassza **a myBackendPool lehetőséget.**

4. A **Háttércélok alatt a** Cél **típusa** mezőben válassza a **Virtuális** gép lehetőséget a legördülő listából.

5. A **Cél mezőben** válassza ki a **myVM** és **myVM2** virtuális gépeket és a hozzájuk tartozó hálózati adaptereket a legördülő listákból.

   > [!div class="mx-imgBorder"]
   > ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

7. Várja meg, amíg az üzembe helyezés befejeződik, mielőtt továbblépne a következő lépésre.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az IIS nem szükséges az Alkalmazásátjáró létrehozásához, ebben a rövid útmutatóban telepítette azt annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót. 

Használja az IIS-t az Application Gateway tesztelésére:

1. Keresse meg az Alkalmazásátjáró nyilvános IP-címét az Áttekintés **lapon.** ![ Rögzítse az Application Gateway nyilvános IP-címét Vagy válassza a Minden erőforrás lehetőséget, írja be a ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) *myAGPublicIPAddress* címet a keresőmezőbe, majd válassza ki a keresési eredmények között.  Az Azure az Áttekintés oldalon jeleníti meg a nyilvános **IP-címet.**
2. Másolja ki a nyilvános IP-címet, majd illessze be a böngésző címsorába az IP-cím tallózáshoz.
3. Ellenőrizze a választ. Egy érvényes válasz ellenőrzi, hogy az Application Gateway sikeresen létrejött-e, és sikeresen tud-e csatlakozni a háttérkiszolgálóhoz.

   ![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Frissítse többször a böngészőt, és látnia kell a myVM és a myVM2 kapcsolatát is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Alkalmazásátjáróval létrehozott erőforrásokra, törölje az erőforráscsoportot. Az erőforráscsoport törlésekor az Application Gatewayt és az összes kapcsolódó erőforrást is eltávolítja.

Az erőforráscsoport törlése:

1. A Azure Portal válassza az **Erőforráscsoportok** lehetőséget, vagy keresse meg és válassza az *Erőforráscsoportok elemet.*
2. Az **Erőforráscsoportok lapon** keresse meg a **myResourceGroupAG** listában, majd jelölje ki.
3. Az **Erőforráscsoport lapon válassza az** **Erőforráscsoport törlése lehetőséget.**
4. Írja be *a myResourceGroupAG nevet* az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT** mezőbe, majd válassza a Törlés **lehetőséget**

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Application Gateway konfigurálása TLS-leállítva a Azure Portal](create-ssl-portal.md)
