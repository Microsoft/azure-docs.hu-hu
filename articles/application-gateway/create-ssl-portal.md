---
title: 'Oktatóanyag: TLS-letöltés konfigurálása a portálon – Azure Application Gateway'
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az Alkalmazásátjárót, és hogyan adhat hozzá tanúsítványt a TLS-megszakításhoz a Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: victorh
ms.openlocfilehash: aa7123a1c4dea5fcede3e94250576f6677671176
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872244"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Oktatóanyag: Application Gateway konfigurálása TLS-leállítva a Azure Portal

A háttérkiszolgálókhoz Azure Portal virtuális [](overview.md) gépeket használó TLS-lejáró tanúsítvánnyal konfigurálhatja az alkalmazásátjárót.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * A háttérkiszolgálóként használt virtuális gépek létrehozása
> * Az alkalmazásátjáró tesztelése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Jelentkezzen be a Azure Portal: [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Ebben a szakaszban a [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) használatával fog önaírt tanúsítványt létrehozni. A tanúsítványt az application gateway Azure Portal létrehozásakor kell feltöltenie a tanúsítványba.

A helyi számítógépen nyisson meg egy Windows PowerShell rendszergazdaként. Futtassa a következő parancsot a tanúsítvány létrehozásához:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

A következő válaszhoz hasonlónak kell lennie:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Az [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) és a visszaadott ujjlenyomat segítségével exportáljon pfx-fájlt a tanúsítványból. Győződjön meg arról, hogy a jelszó 4–12 karakter hosszú:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. A **bal oldali menüben** válassza az Erőforrás létrehozása Azure Portal. **Megjelenik** az Új ablak.

2. Válassza **a Hálózat lehetőséget,** majd **Application Gateway** a Kiemelt **listában.**

### <a name="basics-tab"></a>Alapvető beállítások lap

1. Az Alapvető **beállítások** lapon adja meg ezeket az értékeket az Application Gateway következő beállításaihoz:

   - **Erőforráscsoport:** Válassza ki **a myResourceGroupAG** erőforráscsoportot. Ha még nem létezik, válassza az **Új létrehozása** lehetőséget a létrehozásához.
   - **Application Gateway neve:** Az Application Gateway neveként adja meg a *myAppGateway* nevet.

        ![Új Application Gateway létrehozása: Alapok](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni az Application Gateway létrehozásakor. Application Gateway példányok külön alhálózatban vannak létrehozva. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz.

    A **Virtuális hálózat konfigurálása alatt** hozzon létre egy új virtuális hálózatot az Új létrehozása lehetőség **kiválasztásával.** A **megnyíló Virtuális hálózat** létrehozása ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név:** A virtuális hálózat neveként adja meg a *myVNet* nevet.

    - **Alhálózat neve** (Application Gateway alhálózat): **Az** Alhálózatok rács egy Default nevű alhálózatot fog *mutatni.* Módosítsa az alhálózat nevét a *következőre: myAGSubnet.*<br>Az Application Gateway alhálózata csak alkalmazásátjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttérkiszolgáló alhálózata): Az Alhálózatok rács második sorában adja meg a *myBackendSubnet* nevet az **Alhálózat neve oszlopban.** 

    - **Címtartomány** (háttérkiszolgáló alhálózata): Az Alhálózatok rács második sorában adjon meg egy olyan címtartományt, amely nincs átfedésben a *myAGSubnet címtartományával.*  Ha például a *myAGSubnet* címtartománya 10.0.0.0/24, adja meg a *10.0.1.0/24* címet a *myBackendSubnet címtartományában.*

    Kattintson **az OK** gombra a Virtuális hálózat létrehozása ablak **bezárásához** és a virtuális hálózat beállításainak mentéséhez.

    ![Új Application Gateway létrehozása: virtuális hálózat](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Az Alapvető **beállítások lapon** fogadja el a többi beállítás alapértelmezett értékeit, majd válassza a **Tovább: Előterek lehetőséget.**

### <a name="frontends-tab"></a>Előterek lap

1. Az **Előterek lapon** ellenőrizze, hogy az **előtere IP-címtípusa** Nyilvános.  <br>Az előtere IP-címét konfigurálhatja nyilvánosra vagy privátra a saját használatának megfelelően. Ebben a példában egy nyilvános előtere IP-címet fog választani.
   > [!NOTE]
   > A Application Gateway v2 termékváltozathoz csak a  Nyilvános előtere IP-konfigurációt választhatja. A privát előtere IP-konfigurációja jelenleg nincs engedélyezve ehhez a v2 termékváltozathoz.

2. A **Nyilvános IP-cím mezőben** válassza az Új hozzáadása lehetőséget, a nyilvános IP-cím neveként adja meg a *myAGPublicIPAddress* nevet, majd kattintson az **OK gombra.**  

   ![Új Application Gateway létrehozása: előterek](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Válassza **a Tovább: Háttér háttér lehetőséget.**

### <a name="backends-tab"></a>Háttéreszközök lap

A háttérkészlet arra szolgál, hogy a kéréseket a kérést kiszolgáló háttérkiszolgálókhoz irányítsa. A háttérkészletek tagjai hálózati számítógépek, virtuálisgép-méretezési készletek, nyilvános IP-k, belső IP-k, teljes tartománynevek (FQDN) és több-bérlős háttérkiszolgálók, például Azure App Service. Ebben a példában létre fog hozni egy üres háttérkészletet az alkalmazásátjáróval, majd háttércélokat ad a háttérkészlethez.

1. A **Háttérkészletek lapon** válassza **a Háttérkészlet hozzáadása lehetőséget.**

2. A **megnyíló Háttérkészlet** hozzáadása ablakban adja meg a következő értékeket egy üres háttérkészlet létrehozásához:

    - **Név:** Adja meg *a myBackendPool* nevet a háttérkészlet neveként.
    - **Háttérkészlet hozzáadása célok** nélkül: Válassza az **Igen** lehetőséget egy cél nélküli háttérkészlet létrehozásához. Az Alkalmazásátjáró létrehozása után háttércélokat fog hozzáadni.

3. A **Háttérkészlet hozzáadása** ablakban válassza  a Hozzáadás lehetőséget a háttérkészlet konfigurációjának mentésére és a Háttérkészletek **lapra való visszatéréshez.**

   ![Új Application Gateway létrehozása: háttéralkalmazások](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. A **Háttéreszközök lapon válassza** a **Tovább: Konfiguráció lehetőséget.**

### <a name="configuration-tab"></a>Konfiguráció lap

A Konfiguráció **lapon** egy útválasztási szabály használatával fogja csatlakoztatni a létrehozott elő- és háttérkészletet.

1. Az **Útválasztási szabályok oszlopban válassza** az **Útválasztási szabály hozzáadása** lehetőséget.

2. A **megnyíló Útválasztási szabály hozzáadása** ablakban adja meg a *myRoutingRule* nevet **a Szabály neveként.**

3. Az útválasztási szabályokhoz listener szükséges. Az **Útválasztási szabály hozzáadása** ablak Figyelő lapján adja meg **a** következő értékeket a listener számára:

    - **Figyelő neve:** A listener neveként adja meg a *myListener* nevet.
    - **Előtere IP-címe:** Válassza a **Nyilvános** lehetőséget az előtere számára létrehozott nyilvános IP-cím kiválasztásához.
    - **Protokoll:** Válassza a **HTTPS lehetőséget.**
    - **Port:** Ellenőrizze, hogy a 443-as port be van-e adva.

   A **HTTPS-beállítások alatt:**

   - **Válasszon tanúsítványt –** Válassza **a Tanúsítvány feltöltése lehetőséget.**
   - **PFX-tanúsítványfájl** – Keresse meg és válassza ki a korábban létrehozott c:\appgwcert.pfx fájlt.
   - **Tanúsítvány neve** – Írja be a *mycert1* nevet a tanúsítvány neveként.
   - **Jelszó** – Adja meg a tanúsítvány létrehozásához használt jelszót.
  
        Fogadja el a Figyelés  lapon található többi  beállítás alapértelmezett értékeit, majd válassza a Háttércélok lapot az útválasztási szabály további beállításainak konfigurálhoz.

   ![Új Application Gateway létrehozása: listener](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. A **Háttércélok lapon** válassza a **myBackendPool** háttércélt. 

5. A **HTTP-beállításnál** válassza az **Új hozzáadása lehetőséget** egy új HTTP-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A **megnyíló HTTP-beállítás hozzáadása** ablakban adja meg a *myHTTPSetting* nevet **a HTTP-beállítás neveként.** Fogadja el az alapértelmezett értékeket a **HTTP-beállítás** hozzáadása  ablakban található többi beállításhoz, majd válassza a Hozzáadás lehetőséget az Útválasztási szabály **hozzáadása ablakba való visszatéréshez.** 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Új Application Gateway létrehozása: HTTP-beállítás":::

6. Az **Útválasztási szabály hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéshez és a **Konfiguráció lapra való visszatéréshez.**

   ![Új Application Gateway létrehozása: útválasztási szabály](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Válassza **a Tovább: Címkék,** majd **a Tovább: Áttekintés + létrehozás lehetőséget.**

### <a name="review--create-tab"></a>Áttekintés és létrehozás lap

Tekintse át a beállításokat a Felülvizsgálat  **+ létrehozás** lapon, majd válassza a Létrehozás lehetőséget a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az Application Gateway létrehozása eltarthat néhány percig. A következő szakaszra való továbblépés előtt várja meg, amíg az üzembe helyezés sikeresen befejeződik.

## <a name="add-backend-targets"></a>Háttércélok hozzáadása

Ebben a példában virtuális gépeket fog használni cél háttérrendszerként. Használhat meglévő virtuális gépeket, vagy létrehozhat újakat. Két virtuális gépet fog létrehozni, amelyek háttérkiszolgálóként szolgálnak az Azure-ban az Alkalmazásátjáróhoz.

Ehhez a következőt kell megtennie:

1. Hozzon létre két új virtuális gép, a *myVM* és a *myVM2* virtuális gépeket háttérkiszolgálóként.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Alkalmazásátjáró sikeresen létrejött-e.
3. Adja hozzá a háttérkiszolgálókat a háttérkészlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal válassza az **Erőforrás létrehozása lehetőséget.** **Megjelenik** az Új ablak.
2. A Népszerű listában válassza a **Windows Server 2016 Datacenter** **lehetőséget.** Megjelenik **a Virtuális gép létrehozása** lap.

   Application Gateway forgalom a háttérkészletben használt bármilyen típusú virtuális géphez irányítható. Ebben a példában egy Windows Server 2016 Datacentert használ.

1. Adja meg ezeket az értékeket az **Alapvető beállítások lapon** a következő virtuálisgép-beállításokhoz:

    - **Erőforráscsoport:** Az erőforráscsoport neveként válassza a **myResourceGroupAG** lehetőséget.
    - **Virtuális gép neve:** A virtuális gép neveként adja meg a *myVM* nevet.
    - **Felhasználónév:** Adja meg a rendszergazda felhasználónevét.
    - **Jelszó:** Adja meg a rendszergazdai fiók jelszavát.
1. Fogadja el a többi alapértelmezett beállítást, majd válassza a **Tovább: Lemezek lehetőséget.**  
2. Fogadja el **a Lemezek lap** alapértelmezett beállítását, majd válassza a **Tovább: Hálózatkezelés lehetőséget.**
3. A **Hálózat lapon** ellenőrizze, hogy a **myVNet** van-e kiválasztva a Virtuális hálózat **mezőben,** és hogy az **Alhálózat** beállítása **myBackendSubnet.** Fogadja el a többi alapértelmezett beállítást, majd válassza a **Tovább: Felügyelet lehetőséget.**

   Application Gateway virtuális hálózaton kívüli példányokkal is tud kommunikálni, de gondoskodnia kell az IP-kapcsolatról.
1. A Felügyelet **lapon** állítsa a **Rendszerindítási diagnosztika adatokat** **Letiltás beállításra.** Fogadja el a többi alapértelmezett értéket, majd válassza a **Felülvizsgálat + létrehozás lehetőséget.**
2. Az Áttekintés **+ létrehozás lapon** tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, majd válassza a Létrehozás **lehetőséget.**
3. Folytatás előtt várja meg, amíg az üzembe helyezési folyamat befejeződik.

### <a name="install-iis-for-testing"></a>Az IIS telepítése teszteléshez

Ebben a példában csak annak ellenőrzéséhez telepíti az IIS-t a virtuális gépekre, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót.

1. Nyissa meg [a Azure PowerShell.](../cloud-shell/quickstart-powershell.md) Ezt a Cloud Shell  felső navigációs sávján válassza Azure Portal, majd válassza a **PowerShell** lehetőséget a legördülő listából. 

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Módosítsa a környezet helybeállítását, majd futtassa a következő parancsot az IIS virtuális gépre való telepítéséhez: 

   ```azurepowershell-interactive
          Set-AzVMExtension `
            -ResourceGroupName myResourceGroupAG `
            -ExtensionName IIS `
            -VMName myVM `
            -Publisher Microsoft.Compute `
            -ExtensionType CustomScriptExtension `
            -TypeHandlerVersion 1.4 `
            -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
            -Location <location>
   ```

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban befejezett lépésekkel. A virtuális gép neveként a *myVM2,* a **Set-AzVMExtension** parancsmag **VMName** beállításaként pedig a myVM2 nevet használja.

### <a name="add-backend-servers-to-backend-pool"></a>Háttérkiszolgálók hozzáadása a háttérkészlethez

1. Válassza **a Minden erőforrás,** majd a **myAppGateway lehetőséget.**

2. A **bal oldali menüben válassza** a Háttérkészletek lehetőséget.

3. Válassza **ki a myBackendPool készletet.**

4. A **Cél típusa alatt** válassza a Virtuális **gép** lehetőséget a legördülő listából.

5. A **Cél alatt** válassza ki a **myVM** alatti hálózati adaptert a legördülő listából.

6. Ismételje meg ezt a **myVM2 hálózati adapterének hozzáadásához.**

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

7. Várja meg, amíg az üzembe helyezés befejeződik, mielőtt továbblépne a következő lépésre.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Válassza **a Minden erőforrás** lehetőséget, majd a **myAGPublicIPAddress lehetőséget.**

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/create-ssl-portal/application-gateway-ag-address.png)

2. A böngésző címsorában írja be a *következőt: \<your application gateway ip address\> https://.*

   Ha önaírt tanúsítványt használt, a biztonsági figyelmeztetés  elfogadásához válassza a Részletek **(vagy** a Speciális a Chrome-ban) lehetőséget, majd tovább a weblapra:

    ![Biztonsági figyelmeztetés](./media/create-ssl-portal/application-gateway-secure.png)

    Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és az összes kapcsolódó erőforrást. Válassza ki az erőforráscsoportot, majd válassza az **Erőforráscsoport törlése lehetőséget.**

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a TLS Application Gateway támogatásról](ssl-overview.md)
