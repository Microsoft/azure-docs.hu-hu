---
title: Oktatóanyag – a webhely válaszának fejlesztése az Azure Traffic Manager
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy Traffic Manager-profilt egy rendkívül rugalmas webhely létrehozásához.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: d8262a80fac42f103d571523c75c5064d5d43949
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003820"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Oktatóanyag: a webhely válaszának javítása Traffic Manager használatával

Ez az oktatóanyag azt ismerteti, hogyan használható a Traffic Manager egy gyorsan reagáló webhely létrehozásához azáltal, hogy a legalacsonyabb késéssel irányítja át a felhasználói forgalmat a webhelyre. Általában a legalacsonyabb késésű adatközpont a földrajzi távolsághoz legközelebbi.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két, egy alapszintű webhelyet futtató virtuális gép létrehozása az IIS-en
> * Két virtuális tesztgép létrehozása a Traffic Manager működés közbeni megtekintéséhez
> * Az IIS-t futtató virtuális gépek DNS-nevének konfigurálása
> * Traffic Manager-profil létrehozása a webhely továbbfejlesztett teljesítményéhez
> * Virtuálisgép-végpontok hozzáadása a Traffic Manager-profilhoz
> * A Traffic Manager megtekintése működés közben

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ha működés közben szeretné megtekinteni a Traffic Managert, az oktatóanyaghoz a következőket kell üzembe helyeznie:

- Különböző Azure-régiókban futó alapszintű webhelyek két példánya – **USA keleti** régiója és **Nyugat-Európa**.
- Két tesztelési virtuális gép a Traffic Manager-One VM teszteléséhez az **USA keleti** régiójában és a második virtuális gépen **Nyugat-Európában**. A tesztelési virtuális gépek azt szemléltetik, hogy a Traffic Manager hogyan irányítja át a felhasználói forgalmat az ugyanabban a régióban futó webhelyre, ahol a legalacsonyabb késést biztosítja.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányt hoz létre, amelyek a két szolgáltatásvégpontot szolgáltatják két Azure-régióban a Traffic Manager-profilnak. A két webhely létrehozása a következő lépéseket foglalja magában:

1. Hozzon létre kért virtuális gépet egy alapszintű webhely futtatásához – egyet **az USA keleti régiójában**, egyet pedig **Nyugat-Európában**.
2. Telepítse az IIS-kiszolgálót mindkét virtuális gépen, és frissítse az alapértelmezett webhelylapot, amely ismerteti azon virtuális gép nevét, amelyhez a felhasználó csatlakozik a webhely megnyitásakor.

#### <a name="create-vms-for-running-websites"></a>Virtuális gépek létrehozása webhelyek futtatásához

Ebben a szakaszban két virtuális gépet hoz létre *myIISVMEastUS* és *MyIISVMWestEurope* az **USA keleti** régiójában és a **Nyugat-európai** Azure-régióban.

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**  >  **számítás**  >  **Windows Server 2019 Datacenter** elemet.
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:

   - **Előfizetés**  >  **Erőforráscsoport**: válassza az **új létrehozása** elemet, majd írja be a **myResourceGroupTM1**.
   - **Példány részletei**  >  **Virtuális gép neve**: Type *myIISVMEastUS*.
   - **Példány részletei**  >  Régió: válassza az **USA keleti** **régióját**.
   - **Rendszergazdai fiók**  >  **Username (Felhasználónév**): adja meg a választott felhasználónevet.
   - **Rendszergazdai fiók**  >  **Password (jelszó**): adja meg a választott jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövő portok szabályai**  >  **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése** lehetőséget.
   - **Bejövő portok szabályai**  >  **Bejövő portok kiválasztása**: válassza az **RDP** és a **http** elemet a legördülő listából.

3. Válassza a **kezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**, majd a **Tovább: kezelés** lehetőséget. A **figyelés** területen **kapcsolja ki** a **rendszerindítási diagnosztika** beállítást.
4. Válassza az **Áttekintés + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.  
6. A következő lépésekkel hozhat létre egy *myIISVMWestEurope* nevű második virtuális gépet a *myResourceGroupTM2* nevű **erőforráscsoport** -névvel, a Nyugat- *Európai* **helyével** és az összes többi beállítással, amely ugyanaz, mint a *myIISVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

   ![Virtuális gép létrehozása](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

Ebben a szakaszban telepítenie kell az IIS-kiszolgálót a két virtuális gépre *myIISVMEastUS* és *myIISVMWestEurope*, majd frissítenie kell az alapértelmezett webhely lapot. Az egyéni webhelylap azon virtuális gép nevét jeleníti meg, amelyhez Ön csatlakozik a webhely böngészőbeli megnyitásakor.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet.
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Válassza az **OK** lehetőséget.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán navigáljon a **Windows felügyeleti eszközök** > **Kiszolgálókezelő** elemre.
7. Indítsa el a Windows PowerShellt a VM1 gépen, majd telepítse az IIS-kiszolgálót, és frissítse az alapértelmezett htm-fájlt az alábbi parancsokkal.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Az IIS telepítése és a webhely testreszabása](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Szüntesse meg az RDP-kapcsolatot a *myIISVMEastUS* virtuális géppel.
9. Ismételje meg a 1-8-as lépést a következővel: RDP-kapcsolat létrehozásával a *myResourceGroupTM2* erőforráscsoport virtuálisgép- *myIISVMWestEurope* az IIS telepítéséhez és az alapértelmezett weblap testreszabásához.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban az IIS-kiszolgálók – *myIISVMEastUS* és *myIISVMWestEurope* DNS-neveit konfigurálja.

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd válassza a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet az erőforrások listájában.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet, majd válassza a **Mentés** lehetőséget.
4. Ismételje meg a 1-3-es lépést a *myIISVMWestEurope* nevű virtuális gép esetében, amely a *myResourceGroupTM2* -erőforráscsoporthoz található.

### <a name="create-test-vms"></a>Virtuális tesztgépek létrehozása

Ebben a szakaszban létrehoz egy virtuális gépet (*myVMEastUS* és *myVMWestEurope*) minden egyes Azure-régióban (az **USA keleti** régiójában és **Nyugat-Európában**). Ezekkel a virtuális gépekkel tesztelheti, hogy a webhely megnyitásakor a Traffic Manager hogyan irányítja át a forgalmat a legközelebbi IIS-kiszolgálónak.

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**  >  **számítás**  >  **Windows Server 2019 Datacenter** elemet.
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:

   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myResourceGroupTM1** lehetőséget.
   - **Példány részletei**  >  **Virtuális gép neve**: Type *myVMEastUS*.
   - **Példány részletei**  >  Régió: válassza az **USA keleti** **régióját**.
   - **Rendszergazdai fiók**  >  **Username (Felhasználónév**): adja meg a választott felhasználónevet.
   - **Rendszergazdai fiók**  >  **Password (jelszó**): adja meg a választott jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövő portok szabályai**  >  **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése** lehetőséget.
   - **Bejövő portok szabályai**  >  **Bejövő portok kiválasztása**: válassza az **RDP** elemet a legördülő listából.

3. Válassza a **kezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**, majd a **Tovább: kezelés** lehetőséget. A **figyelés** területen **kapcsolja ki** a **rendszerindítási diagnosztika** beállítást.
4. Válassza az **Áttekintés + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.  
6. A következő lépésekkel hozhat létre egy *myVMWestEurope* nevű második virtuális gépet a *myResourceGroupTM2* nevű **erőforráscsoport** -névvel, a Nyugat- *Európai* **helyével** és az összes többi beállítással, amely ugyanaz, mint a *myVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager-profilt, amely a legalacsonyabb késleltetésű végpontra küldi a felhasználói forgalmat.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Traffic Manager profil**  >  **létrehozása** lehetőséget.
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Név                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási metódus          | Válassza ki a **teljesítmény** -útválasztási módszert.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza ki az erőforráscsoport *myResourceGroupTM1*. |
    | Hely                | Válassza az **USA keleti régiója** lehetőséget. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan üzembe helyezendő Traffic Manager-profilra.                              |
    |

    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az IIS-kiszolgálók- *myIISVMEastUS* myIISVMWestEurope futtató két virtuális gépet a  &   felhasználói forgalom a legközelebbi végpontra történő átirányításához a felhasználó felé.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Adja meg vagy válassza ki a következő adatokat, fogadja el a többi beállítás alapértelmezett értékeit, majd kattintson az **OK gombra**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Azure-végpont                                   |
    | Name           | myEastUSEndpoint                                        |
    | Célerőforrás típusa           | Nyilvános IP-cím                          |
    | Célerőforrás          | **Válasszon nyilvános IP-címet**, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza a *myIISVMEastUS-ip* nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |        |           |

4. A 2. és a 3. lépést megismételve adjon hozzá egy *myWestEuropeEndpoint* nevű másik végpontot a nyilvános IP *-myIISVMWestEurope-IP-* címhez, amely a *myIISVMWestEurope* nevű IIS-kiszolgáló virtuális géphez van társítva.
5. Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

    ![Traffic Manager-végpont hozzáadása](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban azt teszteli, hogy a Traffic Manager hogyan irányítja át a felhasználói forgalmat a webhelyet futtató legközelebbi virtuális gépekre a minimális késés biztosításához. A Traffic Manager működés közbeni megtekintéséhez végezze el a következő lépéseket:

1. Határozza meg a Traffic Manager-profilja DNS-nevét.
2. A Traffic Manager megtekintése működés közben:
    - Az **USA keleti** régiójában található teszt virtuális gépről (*myVMEastUS*) nyissa meg a webböngészőben a Traffic Manager profiljának DNS-nevét.
    - A **Nyugat-európai** régióban található test VM (*myVMWestEurope*) webböngészőben keresse meg a Traffic Manager profiljának DNS-nevét.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>A Traffic Manager-profil DNS-nevének meghatározása

Ebben az oktatóanyagban az egyszerűség kedvéért a Traffic Manager-profil DNS-nevét használja a webhelyek megnyitásához.

A Traffic Manager-profil DNS-nevét a következőképp határozhatja meg:

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére. Kattintson a Traffic Manager-profilra a megjelenített eredmények között.
2. Kattintson az **Áttekintés** elemre.
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. Éles környezetekben egy személyes tartománynév konfigurálásával mutathat a Traffic Manager tartománynevére, egy DNS CNAME rekord segítségével.

   ![Traffic Manager DNS-neve](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

Ebben a szakaszban megtekintheti a Traffic Managert működés közben.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet.
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Válassza az **OK** lehetőséget.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
1. A *myVMEastUS* virtuális gépen egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. Mivel az **USA keleti** régiójában található virtuális gép a legközelebbi, az **USA keleti** régiójában lévő IIS-kiszolgáló *myIISVMEastUS* található webhelyre irányítja.

   ![Képernyőkép, amely a "Traffic Manager" profilt jeleníti meg egy böngészőben.](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Ezután kapcsolódjon a **Nyugat-Európában** található *myVMWestEurope* a 1-5-es lépések végrehajtásával, és keresse meg a Traffic Manager profil TARTOMÁNYNEVÉt erről a virtuális gépről. Mivel a **Nyugat-Európában** található virtuális gép a legközelebb a **Nyugat-Európában** található IIS-kiszolgáló *myIISVMWestEurope* elérhető webhelyre irányítja.

   ![Traffic Manager-profil tesztelése](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szüksége, törölje az erőforráscsoportokat (**ResourceGroupTM1** és **ResourceGroupTM2**). Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Forgalom elosztása végpontok között](traffic-manager-configure-weighted-routing-method.md)
