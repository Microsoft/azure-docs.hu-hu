---
title: Oktatóanyag – az alhálózati forgalom útválasztásának konfigurálása az Azure Traffic Manager
description: Ez az oktatóanyag azt ismerteti, hogyan konfigurálható Traffic Manager, hogy az adott végpontokra irányítsa a forgalmat a felhasználói alhálózatokból.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: duau
ms.openlocfilehash: 5c6551e721a4bf197ea7091b8c0b71829693ba76
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398290"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Oktatóanyag: a Traffic Manager használatával a felhasználói alhálózat alapján adott végpontokra irányuló közvetlen forgalom

Ez a cikk ismerteti, hogy hogyan konfigurálhatja a forgalom-útválasztási metódust. A **Subnet** forgalom-útválasztási metódussal IP-címtartományok egy készletét képezheti le adott végpontokra, így amikor a Traffic Manager egy kérést kap, megvizsgálja annak forrás IP-címét, és visszaadja a hozzá tartozó végpontot.

Ebben az oktatóanyagban – a felhasználói lekérdezés IP-címétől függően – egy belső vagy egy éles üzemű webhelyre irányítjuk a forgalmat alhálózati útválasztással.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két, egy alapszintű webhelyet futtató virtuális gép létrehozása az IIS-en
> * Két virtuális tesztgép létrehozása a Traffic Manager működés közbeni megtekintéséhez
> * Az IIS-t futtató virtuális gépek DNS-nevének konfigurálása
> * Egy forgalom-útválasztási Traffic Manager-profil létrehozása a felhasználó alhálózata alapján
> * Virtuálisgép-végpontok hozzáadása a Traffic Manager-profilhoz
> * A Traffic Manager megtekintése működés közben

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt nekikezdene a feladatok elvégzésének.

## <a name="prerequisites"></a>Előfeltételek

Ha működés közben szeretné megtekinteni a Traffic Managert, az oktatóanyaghoz a következőket kell üzembe helyeznie:

- két alapszintű webhely, amelyek eltérő Azure-régiókban futnak – **az USA keleti régiója** (belső webhely) és **Nyugat-Európa** (éles üzemű webhely).
- két virtuális tesztgép a Traffic Manager teszteléséhez – egy **az USA keleti régiójában** és egy **Nyugat-Európában**.

A virtuális tesztgépekkel mutatjuk be, hogy a Traffic Manager hogyan irányítja a felhasználói forgalmat a belső vagy az éles üzemű webhelyre a felhasználói lekérdezés alhálózata alapján.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányt hoz létre, amelyek a két szolgáltatásvégpontot szolgáltatják két Azure-régióban a Traffic Manager-profilnak. A két webhely létrehozása a következő lépéseket foglalja magában:

1. Hozzon létre kért virtuális gépet egy alapszintű webhely futtatásához – egyet **az USA keleti régiójában**, egyet pedig **Nyugat-Európában**.
2. Telepítse az IIS-kiszolgálót mindkét virtuális gépen, és frissítse az alapértelmezett webhelylapot, amely ismerteti azon virtuális gép nevét, amelyhez a felhasználó csatlakozik a webhely megnyitásakor.

#### <a name="create-vms-for-running-websites"></a>Virtuális gépek létrehozása webhelyek futtatásához

Ebben a szakaszban két virtuális gépet hoz létre *myIISVMEastUS* és *MyIISVMWestEurope* az **USA keleti** régiójában és a **Nyugat-európai** Azure-régióban.

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**  >  **számítás**  >  **Windows Server 2019 Datacenter**elemet.
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:

   - **Előfizetés**  >  **Erőforráscsoport**: válassza az **új létrehozása** elemet, majd írja be a **myResourceGroupTM1**.
   - **Példány részletei**  >  **Virtuális gép neve**: Type *myIISVMEastUS*.
   - **Példány részletei**  >  Régió: válassza az **USA keleti** **régióját**.
   - **Rendszergazdai fiók**  >  **Username (Felhasználónév**): adja meg a választott felhasználónevet.
   - **Rendszergazdai fiók**  >  **Password (jelszó**): adja meg a választott jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövő portok szabályai**  >  **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portok szabályai**  >  **Bejövő portok kiválasztása**: válassza az **RDP** és a **http** elemet a legördülő listából.

3. Válassza a **kezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**, majd a **Tovább: kezelés**lehetőséget. A **figyelés**területen **kapcsolja ki**a **rendszerindítási diagnosztika** beállítást.
4. Válassza a **Felülvizsgálat + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.  
6. A következő lépésekkel hozhat létre egy *myIISVMWestEurope*nevű második virtuális gépet a *myResourceGroupTM2*nevű **erőforráscsoport** -névvel, a Nyugat- *Európai* **helyével** és az összes többi beállítással, amely ugyanaz, mint a *myIISVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

Ebben a szakaszban telepítenie kell az IIS-kiszolgálót a két virtuális gépre – *myIISVMEastUS*  &  *myIISVMWestEurope*, majd frissítenie kell az alapértelmezett webhely lapot. Az egyéni webhelylap azon virtuális gép nevét jeleníti meg, amelyhez Ön csatlakozik a webhely böngészőbeli megnyitásakor.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet.
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Válassza az **OK** lehetőséget.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán navigáljon a **Windows felügyeleti eszközök** > **Kiszolgálókezelő**elemre.
7. Indítsa el a Windows PowerShellt a virtuális gép *myIISVMEastUS*, és az alábbi parancsokkal telepítse az IIS-kiszolgálót, és frissítse az alapértelmezett htm-fájlt.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Az RDP-kapcsolat lezárása a *myIISVMEastUS* virtuális géppel.
9. Ismételje meg a 1-6-as lépést a következővel: RDP-kapcsolat létrehozásával a *myResourceGroupTM2* erőforráscsoport virtuálisgép- *myIISVMWestEurope* az IIS telepítéséhez és az alapértelmezett weblap testreszabásához.
10. Indítsa el a Windows PowerShellt a *myIISVMWestEurope* virtuális gépen, és az alábbi parancsok segítségével telepítse az IIS-kiszolgálót, és frissítse az alapértelmezett htm-fájlt.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban az IIS-kiszolgálók – *myIISVMEastUS* és *myIISVMWestEurope*DNS-neveit konfigurálja.

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd válassza a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet az erőforrások listájában.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet, majd válassza a **Mentés** lehetőséget.
4. Ismételje meg a 1-3-es lépést a *myIISVMWestEurope* nevű virtuális gép esetében, amely a *myResourceGroupTM2* -erőforráscsoporthoz található.

### <a name="create-test-vms"></a>Virtuális tesztgépek létrehozása

Ebben a szakaszban létrehoz egy virtuális gépet (*myVMEastUS* és *myVMWestEurope*) minden egyes Azure-régióban (az**USA keleti** régiójában és **Nyugat-Európában**). Ezeket a virtuális gépeket fogja használni annak teszteléséhez, hogy Traffic Manager hogyan irányítja át a felhasználói forgalmat a felhasználó lekérdezésének alhálózata alapján.

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**  >  **számítás**  >  **Windows Server 2019 Datacenter**elemet.
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:

   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myResourceGroupTM1**lehetőséget.
   - **Példány részletei**  >  **Virtuális gép neve**: Type *myVMEastUS*.
   - **Példány részletei**  >  Régió: válassza az **USA keleti** **régióját**.
   - **Rendszergazdai fiók**  >  **Username (Felhasználónév**): adja meg a választott felhasználónevet.
   - **Rendszergazdai fiók**  >  **Password (jelszó**): adja meg a választott jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövő portok szabályai**  >  **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portok szabályai**  >  **Bejövő portok kiválasztása**: válassza az **RDP** elemet a legördülő listából.

3. Válassza a **kezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**, majd a **Tovább: kezelés**lehetőséget. A **figyelés**területen **kapcsolja ki**a **rendszerindítási diagnosztika** beállítást.
4. Válassza a **Felülvizsgálat + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.  
6. A következő lépésekkel hozhat létre egy *myVMWestEurope*nevű második virtuális gépet a *myResourceGroupTM2*nevű **erőforráscsoport** -névvel, a Nyugat- *Európai* **helyével** és az összes többi beállítással, amely ugyanaz, mint a *myVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Létrehozhat egy Traffic Manager-profilt, amellyel a kérés forrás IP-címe alapján adott végpontokat adhat vissza.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Traffic Manager profil**  >  **létrehozása**lehetőséget.
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Név                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási metódus          | Válassza a **Subnet** útválasztási metódust.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a **Meglévő** lehetőséget, majd írja be a *myResourceGroupTM1* kifejezést. |
    | |                              |
    |

    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az IIS-kiszolgálók- *myIISVMEastUS*myIISVMWestEurope futtató két virtuális gépet a felhasználói  &  *myIISVMWestEurope* forgalom útválasztásához a felhasználó lekérdezésének alhálózata alapján.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Adja meg vagy válassza ki a következő adatokat, fogadja el a többi beállítás alapértelmezett értékeit, majd kattintson az **OK gombra**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Azure-végpont                                   |
    | Name           | myInternalWebSiteEndpoint                                        |
    | Célerőforrás típusa           | Nyilvános IP-cím                          |
    | Célerőforrás          | **Válasszon nyilvános IP-címet**, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza a *myIISVMEastUS-ip* nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |  Alhálózat útválasztási beállításai    |   Adja hozzá a *myVMEastUS* -teszt virtuális gép IP-címét. A virtuális gépről származó összes felhasználói lekérdezés a *myInternalWebSiteEndpoint*lesz átirányítva.    |

4. A 2. és a 3. lépést megismételve adjon hozzá egy *myProdWebsiteEndpoint* nevű másik végpontot a nyilvános IP *-myIISVMWestEurope-IP-* címhez, amely a *myIISVMWestEurope*nevű IIS-kiszolgáló virtuális géphez van társítva. Az **alhálózati útválasztási beállítások**területen adja hozzá a *MYVMWESTEUROPE*-teszt IP-címét. Az erről a virtuális tesztgépről érkező felhasználói lekérdezések a *myProdWebsiteEndpoint* végpontra lesznek irányítva.
5. Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban tesztelheti, hogyan irányítja a Traffic Manager egy adott alhálózat felhasználói forgalmát egy adott végpontba. A Traffic Manager működés közbeni megtekintéséhez végezze el a következő lépéseket:

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

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

Ebben a szakaszban megtekintheti a Traffic Managert működés közben.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet.
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Válassza az **OK** lehetőséget.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A *myVMEastUS* virtuális gépen egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. Mivel a virtuális gép *myVMEastUS* IP-címe a végpont *myInternalWebsiteEndpoint*van társítva, a webböngésző elindítja a teszt webhely kiszolgáló- *myIISVMEastUS*.

7. Ezután kapcsolódjon a **Nyugat-Európában** található *myVMWestEurope* a 1-5-es lépések végrehajtásával, és keresse meg a Traffic Manager profil TARTOMÁNYNEVÉt erről a virtuális gépről. Mivel a virtuális gép *myVMWestEurope* IP-címe a végpont *myProductionWebsiteEndpoint*van társítva, a webböngésző elindítja a teszt webhely kiszolgáló- *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése

Ha már nincs rájuk szüksége, törölje az erőforráscsoportokat (**ResourceGroupTM1** és **ResourceGroupTM2**). Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
