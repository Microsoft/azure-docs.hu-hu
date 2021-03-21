---
title: 'Oktatóanyag: forgalom átirányítása súlyozott végpontokra – Azure Traffic Manager'
description: Ez az oktatócikk azt ismerteti, hogyan irányíthatja át a forgalmat súlyozott végpontokra a Traffic Manager használatával.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: 55c316a370b9e44e906e48b4716201384567c9c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003786"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Oktatóanyag: A forgalom-útválasztás szabályozása súlyozott végpontokkal a Traffic Manager használatával

Ez az oktatóanyag azt ismerteti, hogyan szabályozhatja a végpontok közötti felhasználói adatforgalom útválasztását az Azure Traffic Managerrel a súlyozott útválasztási móddal. Ezzel az útválasztási móddal a Traffic Manager-profil konfigurációjában súlyt rendel minden végponthoz. A felhasználói adatforgalom ezután az egyes végpontokhoz rendelt súly alapján továbbítódik. A súlyok 1 és 1000 közötti egész számok. Minél nagyobb a végponthoz hozzárendelt súlyérték, annál nagyobb a prioritása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két, alapszintű webhelyet futtató virtuális gép létrehozása az IIS-en.
> * Két virtuális tesztgép létrehozása a Traffic Manager működés közbeni megtekintéséhez.
> * Az IIS-t futtató virtuális gépek DNS-nevének konfigurálása.
> * Traffic Manager-profil létrehozása.
> * Virtuálisgép-végpontok hozzáadása a Traffic Manager-profilhoz.
> * A Traffic Manager megtekintése működés közben.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A Traffic Manager működés közbeni megtekintéséhez helyezze üzembe az alábbiakat ehhez az oktatóanyaghoz:

- Két alapszintű webhelypéldány, amelyek különböző Azure-régiókban futnak: az USA keleti régiójában és Nyugat-Európában.
- Két virtuális tesztgép a Traffic Manager teszteléséhez: egy az USA keleti régiójában, egy pedig Nyugat-Európában. A virtuális tesztgépek annak bemutatására szolgálnak, hogyan irányítja át a Traffic Manager a felhasználói adatforgalmat egy olyan webhelyre, amelynek végpontjához nagyobb súly van hozzárendelve.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányt hoz létre, amelyek a két szolgáltatásvégpontot szolgáltatják két Azure-régióban a Traffic Manager-profilnak. A két webhely létrehozásához hajtsa végre az alábbi lépéseket:

1. Hozzon létre kért virtuális gépet egy alapszintű webhely futtatásához: egyet az USA keleti régiójában, egyet pedig Nyugat-Európában.
2. Telepítsen IIS-kiszolgálót mindkét virtuális gépre. Frissítse az azon virtuális gép nevét leíró alapértelmezett weblapot, amelyhez a felhasználók csatlakoznak a webhely meglátogatásakor.

#### <a name="create-vms-for-running-websites"></a>Virtuális gépek létrehozása webhelyek futtatásához

Ebben a szakaszban két virtuális gépet hoz létre (*myIISVMEastUS* és *MYIISVMWESTEUROPE*) az USA keleti régiójában és a Nyugat-európai Azure-régióban.

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

#### <a name="install-iis-and-customize-the-default-webpage"></a>Az IIS telepítése és az alapértelmezett weblap testreszabása

Ebben a szakaszban telepítenie kell az IIS-kiszolgálót a két virtuális gépre myIISVMEastUS és myIISVMWestEurope, majd frissítenie kell az alapértelmezett weblapot. Az egyéni weblap azon virtuális gép nevét jeleníti meg, amelyhez a webhely webböngészőbeli megnyitásakor csatlakozva van.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **myResourceGroupTM1** erőforráscsoportban a **myIISVMEastUS** erőforrást.
2. Az **Áttekintés** oldalon válassza a **Csatlakozás** elemet. A **Csatlakozás virtuális géphez** területen kattintson az **RDP-fájl letöltése** elemre.
3. Nyissa meg a letöltött .rdp fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat   >  , hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania **egy másik fiók használatával**.
4. Válassza az **OK** lehetőséget.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a figyelmeztetés jelenik meg, válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolódás folytatásához.
6. A kiszolgáló asztalán keresse meg a **Windows felügyeleti eszközök**  >  **Kiszolgálókezelő eszközt**.
7. Nyissa meg a Windows PowerShellt a VM1 virtuális gépen. Használja a következő parancsokat az IIS-kiszolgáló telepítéséhez és az alapértelmezett .htm fájl frissítéséhez.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![Az IIS telepítése és a weblap testreszabása](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Szüntesse meg az RDP-kapcsolatot a **myIISVMEastUS** virtuális géppel.
9. Ismételje meg az 1–8. lépést. Hozzon létre egy RDP-kapcsolatot a virtuálisgép- **myIISVMWestEurope** a **myResourceGroupTM2** erőforráscsoporthoz, és telepítse az IIS-t, és szabja testre az alapértelmezett weblapot.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban a DNS-neveket konfigurálja az IIS-kiszolgálók myIISVMEastUS és myIISVMWestEurope.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **myResourceGroupTM1** erőforráscsoportban a **myIISVMEastUS** erőforrást.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet. Kattintson a **Mentés** gombra.
4. Ismételje meg a 1-3. lépést a **myIISVMWestEurope** nevű virtuális géphez a **myResourceGroupTM2** erőforráscsoporthoz.

### <a name="create-a-test-vm"></a>Tesztelési virtuális gép létrehozása

Ebben a szakaszban létrehoz egy virtuális gépet (*myVMEastUS* és *myVMWestEurope*) minden egyes Azure-régióban (az **USA keleti** régiójában és **Nyugat-Európában**). Ezeket a virtuális gépeket fogja használni annak tesztelésére, hogy Traffic Manager hogyan irányítja át a forgalmat a webhely-végpontra, amely a magasabb súlyozási értéket használja.

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

Hozzon létre egy Traffic Manager-profilt a **Súlyozott** útválasztási mód alapján.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Traffic Manager profil**  >  **létrehozása** lehetőséget.
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki a következő információkat. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **Létrehozás** lehetőséget.

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Név                   | Adjon meg egy egyedi nevet a trafficmanager.net zónában. Ez a trafficmanager.net DNS-nevet eredményezi, amellyel a Traffic Manager-profil elérhető.                                   |
    | Útválasztási metódus          | Válassza a **Súlyozott** útválasztási módot.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a **Meglévő használata**, majd a **myResourceGroupTM1** lehetőséget. |
    |        |   |

    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az IIS-kiszolgálókat futtató két virtuális gépet a myIISVMEastUS és a myIISVMWestEurope, hogy átirányítsa a felhasználói forgalmat.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére. Válassza ki a megjelenített eredmények között a profilt.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában válassza a **Végpontok** > **Hozzáadás** elemet.
3. Adja meg vagy válassza ki a következő információkat. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza az **OK** elemet.

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Adja meg az Azure-végpontot.                                   |
    | Name           | Adja meg a **myEastUSEndpoint** nevet.                                        |
    | Célerőforrás típusa           | Válassza a **Nyilvános IP-cím** elemet.                          |
    | Célerőforrás          | Válasszon egy nyilvános IP-címet, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza a **myIISVMEastUS-ip** nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |  Tömeg      | Adja meg a **100** értéket.        |
    |        |           |

4. Ismételje meg a 2. és a 3. lépést egy **myWestEuropeEndpoint** nevű másik végpont hozzáadásához a nyilvános IP- **myIISVMWestEurope-IP-** címhez. Ez a címe a myIISVMWestEurope nevű IIS-kiszolgáló virtuális géphez van társítva. A **Súly** mezőben adjon meg **25** értéket.
5. Miután mindkét végpontot hozzáadta, azok megjelennek a Traffic Manager-profil panelen, **Online** figyelési állapottal.

## <a name="test-the-traffic-manager-profile"></a>A Traffic Manager-profil tesztelése

A Traffic Manager működés közbeni megtekintéséhez végezze el a következő lépéseket:

1. Határozza meg a Traffic Manager-profilja DNS-nevét.
2. A Traffic Manager megtekintése működés közben.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>A Traffic Manager-profil DNS-nevének meghatározása

Ebben az oktatóanyagban az egyszerűség kedvéért a Traffic Manager-profil DNS-nevét használja a webhelyek megnyitásához.

A Traffic Manager-profil DNS-nevét a következőképp határozhatja meg:

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére. Válassza ki a Traffic Manager-profilt a megjelenített eredmények között.
2. Válassza az **Áttekintés** lehetőséget.
3. A Traffic Manager-profil mezőben megjelenik annak DNS-neve. Éles környezetekben egy személyes tartománynév konfigurálásával mutathat a Traffic Manager tartománynevére egy DNS CNAME rekord segítségével.

   ![Traffic Manager DNS-neve](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

Ebben a szakaszban megtekintheti a Traffic Managert működés közben.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **myResourceGroupTM1** erőforráscsoportban a **myVMEastUS** erőforrást.
2. Az **Áttekintés** oldalon válassza a **Csatlakozás** elemet. A **Csatlakozás virtuális géphez** területen kattintson az **RDP-fájl letöltése** elemre.
3. Nyissa meg a letöltött .rdp fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat   >  , hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania **egy másik fiók használatával**.
4. Válassza az **OK** lehetőséget.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a figyelmeztetés jelenik meg, válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolódás folytatásához.
6. A myVMEastUS virtuális gépen egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. Ekkor a myIISVMEastUS nevű IIS-kiszolgálón üzemeltetett webhelyre kerül, mert nagyobb, **100** értékű súly van hozzárendelve. Az IIS-kiszolgáló myIISVMWestEurope hozzá van rendelve a **25**-ös alsó végponti súlyozási értékhez.

   ![Traffic Manager-profil tesztelése](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Ismételje meg a 1-6-es lépéseket a virtuális gép myVMWestEurope a súlyozott webhely válaszának megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ezen oktatóanyagban létrehozott erőforráscsoportokra, törölheti őket. Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Az útválasztási módszerekről további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Forgalom-útválasztási módszer](traffic-manager-routing-methods.md)
