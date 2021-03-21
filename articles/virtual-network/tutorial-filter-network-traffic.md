---
title: Hálózati forgalom szűrése – oktatóanyag – Azure Portal
titlesuffix: Azure Virtual Network
description: Ebből az oktatóanyagból megtudhatja, hogyan szűrheti az alhálózatra irányuló hálózati forgalmat egy hálózati biztonsági csoporttal a Azure Portal használatával.
services: virtual-network
author: KumudD
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 746e44c85d4dd9a662556a73f1e4ab0701d31400
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435918"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Oktatóanyag: hálózati forgalom szűrése hálózati biztonsági csoporttal a Azure Portal használatával

Egy hálózati biztonsági csoport használatával szűrheti a bejövő és kimenő hálózati forgalmat egy virtuális hálózat alhálózatáról.

A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak, amelyek IP-cím, port és protokoll szerint szűrik a hálózati forgalmat. A biztonsági szabályok az egyes alhálózatokban üzembe helyezett erőforrásokra vonatkoznak. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati biztonsági csoport és biztonsági szabályok létrehozása
> * Virtuális hálózat létrehozása és hálózati biztonsági csoport hozzárendelése egy alhálózathoz
> * Virtuális gépek üzembe helyezése egy alhálózaton
> * Forgalomszűrők tesztelése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső sarkában.

2. A keresőmezőbe írja be a **Virtual Network** kifejezést. A keresési eredmények között válassza a **Virtual Network** lehetőséget.

3. A **Virtual Network** lapon válassza a **Létrehozás** lehetőséget.

4. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza az **Új létrehozása** lehetőséget.  </br> Adja meg a **myResourceGroup**. </br> Válassza az **OK** lehetőséget. |
    | **Példány adatai** |   |
    | Name | Adja meg a **myVNet** értéket. |
    | Region | Válassza ki az USA **keleti** régióját. |

5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

6. Válassza a **Létrehozás** lehetőséget.

## <a name="create-application-security-groups"></a>Alkalmazásbiztonsági csoportok létrehozása

Az alkalmazásbiztonsági csoportok lehetővé teszik, hogy egy csoportba rendezze a hasonló funkciójú kiszolgálókat, például a webkiszolgálókat.

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső sarkában.

2. A keresőmezőbe írja be az **alkalmazás biztonsági csoportja** mezőt. Válassza ki az **alkalmazás biztonsági csoportját** a keresési eredmények között.

3. Az **alkalmazás biztonsági csoport** lapján válassza a **Létrehozás** elemet.

4. Az **alkalmazás biztonsági csoportjának létrehozása** területen adja meg vagy válassza ki az alábbi adatokat az **alapok** lapon:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Projekt részletei** |  |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. |
    | **Példány adatai** |  |
    | Name | Adja meg a **myAsgWebServers**. |
    | Region | Válassza ki az USA **keleti** régióját. | 

5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

6. Válassza a **Létrehozás** lehetőséget.

7. Ismételje meg a 4. lépést újra, a következő értékek megadásával:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Projekt részletei** |  |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. |
    | **Példány adatai** |  |
    | Name | Adja meg a **myAsgMgmtServers**. |
    | Region | Válassza ki az USA **keleti** régióját. |

8. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

9. Válassza a **Létrehozás** lehetőséget.

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A hálózati biztonsági csoportok biztosítják a virtuális hálózat hálózati forgalmát.

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső sarkában.

2. A keresőmezőbe írja be a **hálózati biztonsági csoportot**. A keresési eredmények között válassza a **hálózati biztonsági csoport** lehetőséget.

3. A **hálózati biztonsági csoport** lapon válassza a **Létrehozás** lehetőséget.

4. A **hálózati biztonsági csoport létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. |
    | **Példány adatai** |   |
    | Name | Adja meg a **myNSG**. |
    | Hely | Válassza ki az USA **keleti** régióját. | 

5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

6. Válassza a **Létrehozás** lehetőséget.

## <a name="associate-network-security-group-to-subnet"></a>Hálózati biztonsági csoport társítása alhálózathoz

Ebben a szakaszban a hálózati biztonsági csoportot a korábban létrehozott virtuális hálózat alhálózatához társítjuk.

1. A portál felső részében található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőben kezdje el beírni a **myNsg** nevet. Amikor a **myNsg** elem megjelenik a keresési eredmények között, válassza ki.

2. A **myNSG** áttekintés lapján válassza az **alhálózatok** lehetőséget a **Beállítások** területen.

3. A **Beállítások** lapon válassza a **hozzárendelés** lehetőséget:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="NSG hozzárendelése az alhálózathoz." border="true":::

3. Az **alhálózat hozzárendelése** területen válassza a **virtuális hálózat** elemet, majd válassza a **myVNet** lehetőséget. 

4. Válassza az **alhálózat** lehetőséget, válassza az **alapértelmezett** lehetőséget, majd kattintson **az OK gombra**.

## <a name="create-security-rules"></a>Biztonsági szabályok létrehozása

1. A **MyNSG** **Beállítások** területén válassza a **bejövő biztonsági szabályok** elemet.

2. A **bejövő biztonsági szabályok** területen válassza a **+ Hozzáadás** lehetőséget:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Bejövő biztonsági szabály hozzáadása." border="true":::

3. Hozzon létre egy biztonsági szabályt, amely engedélyezi a 80-as és a 443-as portot a **myAsgWebServers** alkalmazásbiztonsági csoport számára. A **bejövő biztonsági szabály hozzáadása** lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Forrás | Hagyja **meg az alapértelmezett értéket.** |
    | Forrásporttartományok | Hagyja meg az alapértelmezett értéket **(*)** |
    | Cél | Válassza az **alkalmazás biztonsági csoport** lehetőséget. |
    | Célalkalmazás biztonsági csoportja | Válassza a **myAsgWebServers** lehetőséget. |
    | Szolgáltatás | Hagyja meg az alapértelmezett **Egyéni** értéket. |
    | Célporttartományok | Adja meg a **80 443** értéket. |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Művelet | Hagyja meg az alapértelmezett **engedélyezést**. |
    | Prioritás | Hagyja meg az alapértelmezett **100**-es értéket. |
    | Name | Adja meg **az Allow-web-all** értéket. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Bejövő biztonsági szabály." border="true":::

3. Hajtsa végre ismét a 2. lépést az alábbi értékeket használva:

    | Beállítás | Érték |
    | ------- | ----- |
    | Forrás | Hagyja **meg az alapértelmezett értéket.** |
    | Forrásporttartományok | Hagyja meg az alapértelmezett értéket **(*)** |
    | Cél | Válassza az **alkalmazás biztonsági csoport** lehetőséget. |
    | Célalkalmazás biztonsági csoportja | Válassza a **myAsgMgmtServers** lehetőséget. |
    | Szolgáltatás | Hagyja meg az alapértelmezett **Egyéni** értéket. |
    | Célporttartományok | Adja meg a **3389** értéket. |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Művelet | Hagyja meg az alapértelmezett **engedélyezést**. |
    | Prioritás | Hagyja meg az alapértelmezett **110**-es értéket. |
    | Name | Adja meg **az Allow-RDP-all** értéket. |

    > [!CAUTION]
    > Ebben a cikkben az RDP (3389-es port) a **myAsgMgmtServers** alkalmazás biztonsági csoportjához rendelt virtuális gép internetére van kitéve. 
    >
    > Éles környezetek esetén, a 3389-as port internetre való kihelyezése helyett javasolt a felügyelni kívánt Azure-erőforrásokhoz való kapcsolódás VPN-, magánhálózati vagy Azure-alapú kapcsolaton keresztül.
    >
    > További információ az Azure Bastion-ről: [Mi az az Azure Bastion?](../bastion/bastion-overview.md).

Miután végrehajtotta az 1–3. lépést, tekintse át a létrehozott szabályokat. A listának a következő példában szereplő listához hasonlóan kell kinéznie:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Biztonsági szabályok." border="true":::

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső sarkában.

2. Válassza a **számítás**, majd a **virtuális gép** lehetőséget.

3. A **virtuális gép létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. |
    | **Példány adatai** |   |
    | Virtuális gép neve | Adja meg a **myVMWeb**. |
    | Region | Válassza ki az USA **keleti** régióját. |
    | Rendelkezésre állási beállítások | Hagyja meg az alapértelmezett nem szükséges redundanciát. |
    | Kép | Válassza a **Windows Server 2019 Datacenter-Gen1** elemet. |
    | Azure Spot-példány | Hagyja meg az alapértelmezett jelölést. |
    | Méret | Válassza a **Standard_D2s_V3** lehetőséget. |
    | **Rendszergazdai fiók** |   |
    | Felhasználónév | Adjon meg egy felhasználónevet. |
    | Jelszó | Adjon meg egy jelszót. |
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **Bejövő portok szabályai** |   |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |

4. Válassza a **Hálózatkezelés** lapot.

5. A **hálózatkezelés** lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Hálózati adapter** |   |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Alhálózat | Válassza az **alapértelmezett lehetőséget (10.0.0.0/24)**. |
    | Nyilvános IP-cím | Az új nyilvános IP-cím alapértelmezett értékének meghagyása. |
    | NIC hálózati biztonsági csoport | Válassza a **Nincs** lehetőséget. | 

6. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

7. Válassza a **Létrehozás** lehetőséget.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Hajtsa végre ismét a 1-7 lépést, de a 3. lépésben nevezze el a virtuális gép **myVMMgmt**. A virtuális gép üzembe helyezése néhány percet vesz igénybe. 

A virtuális gép üzembe helyezése előtt ne folytassa a következő lépéssel.

## <a name="associate-network-interfaces-to-an-asg"></a>Hálózati adapterek társítása ASG-hez

Amikor a portál létrehozta a virtuális gépeket, létrehozott egy hálózati adaptert mindegyik virtuális géphez, és a hálózati adaptert a virtuális géphez csatolta. 

Adja hozzá az egyes virtuális gépek hálózati adapterét a korábban létrehozott alkalmazásbiztonsági csoporthoz valamelyikéhez:

1. A portál tetején található **erőforrások, szolgáltatások és dokumentumok keresése** mezőbe kezdje el beírni a **myVMWeb**. Ha a **myVMWeb** virtuális gép megjelenik a keresési eredmények között, válassza ki.

2. A **Beállítások** területen válassza a **hálózatkezelés** lehetőséget.  

3. Válassza ki az **alkalmazás biztonsági csoportjai** lapot, majd válassza **az alkalmazás biztonsági csoportjai konfigurálása** lehetőséget.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Adja meg az alkalmazás biztonsági csoportjait." border="true":::

4. Az **alkalmazás biztonsági csoportjai konfigurálása** területen válassza a **myAsgWebServers** lehetőséget. Kattintson a **Mentés** gombra.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Válassza az alkalmazás biztonsági csoportok lehetőséget." border="true":::

5. Hajtsa végre ismét az 1. és a 2. lépést, keresse meg a **myVMMgmt** virtuális gépet, és válassza ki a  **myAsgMgmtServers** ASG.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

1. Kapcsolódjon a **myVMMgmt** virtuális géphez. Adja meg a **myVMMgmt** a portál felső részén található keresőmezőbe. Ha a **myVMMgmt** megjelenik a keresési eredmények között, válassza ki. Kattintson a **Csatlakozás** gombra.

2. Válassza az **RDP-fájl letöltése** lehetőséget.

3. Nyissa meg a letöltött RDP-fájlt, és válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

4. Válassza az **OK** lehetőséget.

5. A csatlakoztatási folyamat során a tanúsítványra vonatkozó figyelmeztetés jelenhet meg. Ha a figyelmeztetés jelenik meg, válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolódás folytatásához.

    A kapcsolódás sikeres, mert az 3389-es port engedélyezi az internetről a **myAsgMgmtServers** alkalmazás biztonsági csoportba való bejövő hozzáférést. 
    
    A **myVMMgmt** hálózati adaptere a **myAsgMgmtServers** alkalmazás biztonsági csoportjához van társítva, és lehetővé teszi a kapcsolat használatát.

6. Nyisson meg egy PowerShell-munkamenetet a **myVMMgmt**. Kapcsolódjon a **myVMWeb** a következő példa használatával: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    A **myVMMgmt** -ről a **myVMWeb** -re irányuló RDP-kapcsolat sikeres, mert az azonos hálózatban lévő virtuális gépek alapértelmezés szerint bármelyik porton kommunikálhatnak egymással.
    
    Nem hozható létre RDP-kapcsolat a **myVMWeb** virtuális géppel az internetről. A **myAsgWebServers** biztonsági szabálya megakadályozza az internetről bejövő 3389-es porthoz való kapcsolódást. Az internetről érkező bejövő forgalom alapértelmezés szerint minden erőforrás számára meg van tagadva.

7. Ha a Microsoft IIS-t a **myVMWeb** virtuális gépre szeretné telepíteni, írja be a következő parancsot egy PowerShell-munkamenetből a **myVMWeb** virtuális gépen:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Az IIS telepítésének befejezése után válassza le a **myVMWeb** virtuális gépről, amely elhagyja a **myVMMgmt** virtuális gép távoli asztali kapcsolatát.

9. Válassza le a **myVMMgmt** virtuális gépről.

10. A Azure Portal tetején található **erőforrások, szolgáltatások és dokumentumok keresése** mezőbe kezdje el beírni a **myVMWeb** a számítógépről. Ha a **myVMWeb** megjelenik a keresési eredmények között, válassza ki. Jegyezze fel a virtuális gép **Nyilvános IP-cím** értékét. Az alábbi példában látható 23.96.39.113 a címe, de a címe eltér:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Nyilvános IP-cím." border="true":::
    
11. Annak ellenőrzéséhez, hogy el tudja-e érni a **myVMWeb** webkiszolgálót az internetről, nyisson meg egy webböngészőt a számítógépen, és keresse meg a következőt: `http://<public-ip-address-from-previous-step>` . 

Megjelenik az IIS-üdvözlőképernyő, mert az 80-es port engedélyezi az internetről a **myAsgWebServers** alkalmazás biztonsági csoportba való bejövő csatlakozást. 

A **myVMWeb** csatolt hálózati adapter a **myAsgWebServers** alkalmazás biztonsági csoportjához van társítva, és lehetővé teszi a kapcsolat használatát. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a **myResourceGroup** nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a **myResourceGroup** nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Létrehozott egy hálózati biztonsági csoportot, és hozzárendelte egy virtuális hálózati alhálózathoz. 
* Létrehozott alkalmazás-biztonsági csoportok a webes és a felügyeleti szolgáltatásokhoz.
* Két virtuális gép lett létrehozva.
* Tesztelte az alkalmazás biztonsági csoportjának hálózati szűrését.

A hálózati biztonsági csoportokkal kapcsolatos további információ: [Hálózati biztonsági csoportok áttekintése](./network-security-groups-overview.md) és [Hálózati biztonsági csoportok kezelése](manage-network-security-group.md).

Az Azure alapértelmezés szerint irányítja a forgalmat az alhálózatok között. Ehelyett lehetősége van arra, hogy egy virtuális gépen keresztül irányítsa a forgalmat az alhálózatok között, amely így például tűzfalként is szolgálhat. 

Ha meg szeretné ismerni, hogyan hozhat létre útválasztási táblázatot, folytassa a következő oktatóanyaggal.
> [!div class="nextstepaction"]
> [Útválasztási táblázat létrehozása](./tutorial-create-route-table-portal.md)