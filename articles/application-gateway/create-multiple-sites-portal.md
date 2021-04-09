---
title: 'Oktatóanyag: több webhely üzemeltetése a Azure Portal használatával'
titleSuffix: Azure Application Gateway
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan Application Gateway-t, amely több webhelyet üzemeltet a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: cfbd5301bc2b24c4d5614e5f88c6ae18d4affc66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721630"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Oktatóanyag: Application Gateway létrehozása és konfigurálása több webhelynek a Azure Portal használatával történő üzemeltetéséhez

Az Azure Portal használatával [több webhely üzemeltetését konfigurálhatja](multiple-site-overview.md) az [Application Gateway](overview.md)létrehozásakor. Ebben az oktatóanyagban a háttérbeli címkészlet a virtuális gépek használatával van meghatározva. Ezután két tartományon alapuló figyelőket és szabályokat konfigurálhat, hogy megbizonyosodjon arról, hogy a webes forgalom megérkezik a megfelelő kiszolgálókon a készletekben. Ez az oktatóanyag példákat használ a *www.contoso.com* és a *www.fabrikam.com*.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek létrehozása háttérbeli kiszolgálókhoz
> * Háttér-készletek létrehozása a háttér-kiszolgálókkal
> * Háttérbeli figyelők létrehozása
> * Útválasztási szabályok létrehozása
> * Gazdagépek fájljának szerkesztése névfeloldáshoz

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="Többhelyes Application Gateway":::

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza az **erőforrás létrehozása** elemet a Azure Portal bal oldali menüjében. Megjelenik az **új** ablak.

2. Válassza a **hálózatkezelés** lehetőséget, majd a **Kiemelt** listában válassza a **Application Gateway** lehetőséget.

### <a name="basics-tab"></a>Alapbeállítások lap

1. Az **alapok** lapon adja meg a következő Application Gateway-beállításokhoz tartozó értékeket:

   - **Erőforráscsoport**: válassza ki az erőforráscsoport **myResourceGroupAG** . Ha nem létezik, válassza az **új létrehozása** elemet a létrehozásához.
   - **Application Gateway neve**: írja be a *myAppGateway* nevet az Application Gateway neveként.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="Application Gateway létrehozása":::

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni az Application Gateway létrehozásakor. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

    A **virtuális hálózat konfigurálása** területen válassza az **új létrehozása** lehetőséget az új virtuális hálózat létrehozásához. A megnyíló **virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név**: írja be a *myVNet* nevet a virtuális hálózat nevéhez.

    - **Alhálózat neve** (Application Gateway alhálózat): az **alhálózatok** rácsa egy *alapértelmezett* nevű alhálózatot fog megjeleníteni. Módosítsa az alhálózat nevét a *myAGSubnet* értékre.<br>Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttérbeli kiszolgáló alhálózata): az **alhálózatok** rácsának második sorában adja meg az *myBackendSubnet* értéket az **alhálózat neve** oszlopban.

    - **Címtartomány** (háttér-kiszolgáló alhálózata): az **alhálózatok** rácsának második sorában olyan címtartományt adjon meg, amely nem fedi át a *myAGSubnet* címtartomány-tartományát. Ha például a *myAGSubnet* 10.0.0.0/24, a *10.0.1.0/24* értéket adja meg a *myBackendSubnet*-tartományhoz.

    A **virtuális hálózat létrehozása** ablak bezárásához és a virtuális hálózat beállításainak mentéséhez kattintson **az OK gombra** .

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="VNet létrehozása":::
    
3. Az **alapvető** beállítások lapon fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **Next (tovább): frontends** elemet.

### <a name="frontends-tab"></a>Frontendek lap

1. A **frontendek** lapon ellenőrizze, hogy a előtérbeli **IP-cím típusa** **nyilvános** értékre van-e állítva. <br>Az előtérbeli IP-címet úgy állíthatja be, hogy a használati esetnek megfelelően nyilvános vagy privát legyen. Ebben a példában egy nyilvános előtérbeli IP-címet választ.
   > [!NOTE]
   > A Application Gateway v2 SKU esetében csak **nyilvános** ELŐTÉRBELI IP-konfigurációt választhat. A privát előtérbeli IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 SKU-hoz.

2. Válassza a **nyilvános IP-cím** **új hozzáadása** lehetőséget, és adja meg a *myAGPublicIPAddress* a nyilvános IP-cím neveként, majd kattintson **az OK gombra**. 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="Másik VNet létrehozása":::

3. Válassza a Next (tovább) lehetőséget **: háttérrendszer**.

### <a name="backends-tab"></a>Háttérrendszer lap

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttér-készletek lehetnek hálózati adapterek, virtuálisgép-méretezési csoportok, nyilvános IP-címek, belső IP-címek, teljes tartománynevek (FQDN) és több-bérlős háttérrendszer, például Azure App Service. Ebben a példában egy üres háttér-készletet hozunk létre az Application Gateway használatával, majd a háttérbeli célokat hozzá kell adni a háttér-készlethez.

1. A **backends (háttérrendszer** ) lapon válassza a **háttérbeli készlet hozzáadása** elemet.

2. A megnyíló **háttérbeli készlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérbeli készlet létrehozásához:

    - **Név**: adja meg a *contosoPool* nevét a háttér-készlet neveként.
    - **Háttérbeli készlet hozzáadása célok nélkül**: válassza az **Igen** lehetőséget, ha célokat nem tartalmazó háttér-készletet szeretne létrehozni. Az Application Gateway létrehozása után hozzá kell adni a háttérbeli célokat.

3. A háttérbeli **készlet hozzáadása** ablakban válassza a **Hozzáadás** elemet a háttérbeli készlet konfigurációjának mentéséhez, és térjen vissza a **háttérrendszer** lapra.
4. Most adjon hozzá egy *fabrikamPool* nevű másik háttér-készletet ugyanúgy, ahogy az előző készletet adta hozzá.
1. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="Háttérrendszer létrehozása":::

4. A **háttérrendszer** lapon válassza a **Tovább: konfigurálás** lehetőséget.

### <a name="configuration-tab"></a>Konfiguráció lap

A **konfiguráció** lapon összekapcsolja az útválasztási szabály használatával létrehozott előtér-és háttér-készleteket.

1. Válassza az útválasztási **szabály hozzáadása** lehetőséget az **útválasztási szabályok** oszlopban.

2. A megnyíló **útválasztási szabály hozzáadása** ablakban írja be a *ContosoRule* nevet a **szabály neveként**.

3. Egy útválasztási szabályhoz egy figyelő szükséges. Az **útválasztási szabály hozzáadása** ablak **figyelő** lapján adja meg az alábbi értékeket a figyelőhöz:

    - **Szabály neve**: *contosoRule*.
    - **Figyelő neve**: *contosoListener*.
    - Előtér **-IP**: válassza a **nyilvános** lehetőséget, hogy kiválassza a előtérhez létrehozott nyilvános IP-címet.

   A **További beállítások** területen:
   - **Figyelő típusa**: több hely
   - **Állomásnév**: **www.contoso.com**

   Fogadja el az alapértelmezett értékeket a **figyelő** lapon a többi beállításnál, majd válassza a **háttérbeli célok** fület a többi útválasztási szabály konfigurálásához.

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="Útválasztási szabály létrehozása":::

4. A **háttérbeli célok** lapon válassza a **ContosoPool** lehetőséget a **háttérbeli célként**.

5. A **http-beállításnál** válassza az **új hozzáadása** lehetőséget az új http-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A megnyíló **http-beállítás hozzáadása** ablakban írja be a *ContosoHTTPSetting* nevet a **http-beállítás neveként**. Fogadja el az alapértelmezett értékeket a további beállításokhoz a **http-beállítás hozzáadása** ablakban, majd válassza a **Hozzáadás** lehetőséget az **útválasztási szabály hozzáadása** ablakhoz való visszatéréshez. 

6. Az útválasztási szabály **hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéséhez és a **konfiguráció** laphoz való visszatéréshez.
7. Válassza az **útválasztási szabály hozzáadása** lehetőséget, és adjon hozzá egy hasonló szabályt, figyelőt, háttér-célt és http-beállítást a fabrikam számára.

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Fabrikam-szabály":::

7. Válassza a **Next (tovább** ) gombot, majd a **következőt: felülvizsgálat + létrehozás**.

### <a name="review--create-tab"></a>Felülvizsgálat + Létrehozás lap

Tekintse át a **felülvizsgálat + létrehozás** lapon található beállításokat, majd válassza a **Létrehozás** lehetőséget a virtuális hálózat, a nyilvános IP-cím és az Application Gateway létrehozásához. Az Azure az Application Gateway létrehozásához több percet is igénybe vehet.

Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

## <a name="add-backend-targets"></a>Háttérbeli célok hozzáadása

Ebben a példában a virtuális gépeket célként használt háttérként fogja használni. Használhat meglévő virtuális gépeket, vagy újakat is létrehozhat. Hozzon létre két virtuális gépet, amelyeket az Azure háttér-kiszolgálóként használ az Application Gateway számára.

A háttérbeli célok hozzáadásához a következőket kell tennie:

1. Hozzon létre két új virtuális gépet, *contosoVM* és *fabrikamVM* a háttér-kiszolgálóként való használathoz.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.
3. Adja hozzá a háttér-kiszolgálókat a háttérbeli készletekhez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget. Megjelenik az **új** ablak.
2. Válassza a **Windows Server 2016 Datacenter** elemet a **népszerű** listában. Megjelenik a **virtuális gép létrehozása** lap.<br>Application Gateway átirányíthatja a forgalmat a háttér-készletben használt bármilyen típusú virtuális gépre. Ebben a példában egy Windows Server 2016 Datacenter rendszert használ.
3. Adja meg ezeket az értékeket az **alapok** lapon a következő virtuálisgép-beállításokhoz:

    - **Előfizetés**: Válassza ki előfizetését.
    - **Erőforráscsoport**: válassza ki a **myResourceGroupAG** az erőforráscsoport neveként.
    - **Virtuális gép neve**: írja be a *contosoVM* nevet a virtuális gép nevéhez.
    - **Régió**: válassza ki ugyanazt a régiót, amelyet korábban használt.
    - **Felhasználónév**: adjon meg egy nevet a rendszergazda felhasználóneve számára.
    - **Password (jelszó**): adjon meg egy jelszót a rendszergazdának.
1. Fogadja el a többi alapértelmezett értéket, majd válassza a **Next: Disks** elemet.  
2. Fogadja el a **lemezek** lap alapértelmezett értékeit, majd kattintson a **Tovább gombra: hálózatkezelés** elemre.
3. A **hálózatkezelés** lapon ellenőrizze, hogy a **virtuális hálózat** **myVNet** van-e kiválasztva, és az **alhálózat** **myBackendSubnet** értékre van-e állítva. Fogadja el a többi alapértelmezett értéket, majd válassza a **Tovább: kezelés** lehetőséget.<br>A Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de gondoskodnia kell az IP-kapcsolatról.
4. A **felügyelet** lapon **Tiltsa le** a **rendszerindítási diagnosztika** beállítást. Fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.
5. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, majd válassza a **Létrehozás** lehetőséget.
6. A folytatás előtt várja meg, amíg a virtuális gép létrehozása befejeződik.

### <a name="install-iis-for-testing"></a>Az IIS telepítése teszteléshez

Ebben a példában az IIS-t csak akkor telepíti a virtuális gépekre, ha ellenőrzi, hogy az Azure sikeresen létrehozta-e az Application Gatewayt.

1. Nyissa meg [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Ehhez válassza ki a **Cloud Shell** elemet a Azure Portal felső navigációs sávján, majd válassza a **PowerShell** elemet a legördülő listából. 

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. A következő parancs futtatásával telepítse az IIS-t a virtuális gépre, és cserélje le az erőforráscsoport-régiót <helyre \> : 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban befejezett lépések használatával. Használja a *fabrikamVM* a virtuális gép nevéhez és a **set-AzVMExtension** parancsmag **VMName** beállításához.

### <a name="add-backend-servers-to-backend-pools"></a>Háttér-kiszolgálók hozzáadása a háttérbeli készletekhez

1. Válassza a **minden erőforrás** lehetőséget, majd válassza a **myAppGateway** lehetőséget.

2. Válassza ki a **háttér-készletek** elemet a bal oldali menüben.

3. Válassza a **contosoPool** lehetőséget.

4. A **cél típusa** területen válassza a **virtuális gép** lehetőséget a legördülő listából.

5. A **cél** területen válassza ki a **contosoVM** virtuális gép hálózati adapterét a legördülő listából.

    ![Háttérkiszolgálók hozzáadása](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Kattintson a **Mentés** gombra.
7. Ismételje meg a *fabrikamVM* és az illesztőfelület hozzáadását a *fabrikamPool*.

Várjon, amíg a telepítés befejeződik, mielőtt továbblép a következő lépésre.

## <a name="edit-your-hosts-file-for-name-resolution"></a>A gazdagépek fájljának szerkesztése névfeloldáshoz

Miután az Application Gateway nyilvános IP-címmel lett létrehozva, lekérheti az IP-címet, és felhasználhatja a gazdagépek fájljának a feloldásához és a megoldásához `www.contoso.com` `www.fabrikam.com` . Éles környezetben a névfeloldáshoz létrehozhat egy `CNAME` DNS-t.

1. Kattintson **a minden erőforrás** elemre, majd a **myAGPublicIPAddress** elemre.

    ![Application Gateway DNS-címe rögzítése](./media/create-multiple-sites-portal/public-ip.png)

2. Másolja ki az IP-címet, és használja a fájl új bejegyzéseinek értékeként `hosts` .
1. A helyi gépen nyisson meg egy rendszergazdai parancssort, és navigáljon a következőhöz: `c:\Windows\System32\drivers\etc` .
1. Nyissa meg a `hosts` fájlt, és adja hozzá a következő bejegyzéseket, ahol az az `x.x.x.x` Application Gateway nyilvános IP-címe:
   ```dos
   # Copyright (c) 1993-2009 Microsoft Corp.
   #
   # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
   #
   # This file contains the mappings of IP addresses to host names. Each
   # entry should be kept on an individual line. The IP address should
   # be placed in the first column followed by the corresponding host name.
   # The IP address and the host name should be separated by at least one
   # space.
   #
   # Additionally, comments (such as these) may be inserted on individual
   # lines or following the machine name denoted by a '#' symbol.
   #
   # For example:
   #
   #      102.54.94.97     rhino.acme.com          # source server
   #       38.25.63.10     x.acme.com              # x client host
   
   # localhost name resolution is handled within DNS itself.
   #    127.0.0.1       localhost
   #    ::1             localhost
   x.x.x.x www.contoso.com
   x.x.x.x www.fabrikam.com

   ```
1. Mentse a fájlt.
1. Futtassa a következő parancsokat a Hosts fájl módosításainak betöltéséhez és megjelenítéséhez:
   ```dos
    ipconfig/registerdns
    ipconfig/displaydns
   ```
   
## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Adja meg a tartomány nevét a böngésző címsorába. Például: `http://www.contoso.com`.

    ![Contoso webhely tesztelése az alkalmazásátjáróban](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Módosítsa a címeket a másik tartományra, és az alábbi példához hasonlóan kell megjelennie:

    ![Fabrikam webhely tesztelése az alkalmazásátjáróban](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításakor az Application Gateway és az összes kapcsolódó erőforrás is törlődik.

Az erőforráscsoport eltávolítása:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget.
2. Az **erőforráscsoportok** lapon keresse meg a **myResourceGroupAG** a listában, majd válassza ki azt.
3. Az **erőforráscsoport lapon** válassza az **erőforráscsoport törlése** elemet.
4. Írja be a *myResourceGroupAG* **nevet az erőforráscsoport neveként** , majd válassza a **Törlés** lehetőséget.

A Hosts fájl visszaállítása:
1. Törölje a `www.contoso.com` és a `www.fabrikam.com` sorokat a Hosts fájlból, majd futtassa `ipconfig/registerdns` `ipconfig/flushdns` a parancsot a parancssorból.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure Application Gateway](./overview.md)