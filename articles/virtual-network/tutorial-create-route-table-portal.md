---
title: Hálózati forgalom irányítása – oktatóanyag – Azure Portal
titlesuffix: Azure Virtual Network
description: Ez az oktatóanyag azt ismerteti, hogyan irányítható a hálózati forgalom egy útválasztási táblázattal az Azure Portalon.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 63ea6821097327498803abc6577f28e595883bc0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932930"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Oktatóanyag: Hálózati forgalom útválasztási táblázattal való irányítása az Azure Portalon

Az Azure alapértelmezés szerint átirányítja a forgalmat a virtuális hálózaton belüli összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak hasznosak lehetnek, ha például az alhálózatok közötti adatforgalmat egy hálózati virtuális berendezésen (NVA) keresztül szeretné irányítani. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Forgalmat irányító hálózati virtuális berendezés létrehozása
> * Útválasztási táblázat létrehozása
> * Útvonal létrehozása
> * Útválasztási táblázat társítása alhálózattal
> * Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
> * Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ez az oktatóanyag a [Azure Portal](https://portal.azure.com)használja. Használhatja az [Azure CLI](tutorial-create-route-table-cli.md) -t vagy a [Azure PowerShell](tutorial-create-route-table-powershell.md)is.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, egy aktív előfizetéssel rendelkező Azure-fiókra van szükség. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Az Azure Marketplace-en válassza a **hálózatkezelés**  >  **virtuális hálózat** lehetőséget, vagy keressen rá **Virtual Network** a keresőmezőbe.

2. Válassza a **Létrehozás** lehetőséget.

2. A **virtuális hálózat létrehozása** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása** lehetőséget, és adja meg a **myResourceGroup**. </br> Válassza az **OK** lehetőséget. |
    | Name | Adja meg a **myVirtualNetwork**. |
    | Hely | Válassza ki az USA **keleti** régióját.|

3. Válassza az **IP-címek** fület, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IPv4-címtartomány** területen válassza ki a meglévő címtartományt, és módosítsa a **10.0.0.0/16** értékre.

4. Válassza az **+ alhálózat hozzáadása** lehetőséget, majd adja meg a **nyilvános** értéket az alhálózat **neve** és a **10.0.0.0/24** **alhálózati címtartomány** mezőben.

5. Válassza a **Hozzáadás** lehetőséget.

6. Válassza az **+ alhálózat hozzáadása** lehetőséget, majd adja meg a **saját** alhálózat **neve** és a **10.0.1.0/24** alhálózatot az **alhálózati címtartomány** mezőben.

7. Válassza a **Hozzáadás** lehetőséget.

8. Válassza az **+ alhálózat hozzáadása** lehetőséget, majd írja be a **DMZ** alhálózatot az alhálózat **neveként** és a **10.0.2.0/24** **alhálózathoz**.

9. Válassza a **Hozzáadás** lehetőséget.

10. Kattintson a **Biztonság** fülre, vagy válassza a **következő: biztonság** gombot az oldal alján.

11. A **BastionHost** területen válassza az **Engedélyezés** lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.0.3.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név** mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |

12. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

13. Válassza a **Létrehozás** lehetőséget.

## <a name="create-an-nva"></a>NVA létrehozása

A hálózati virtuális berendezések (NVA) olyan virtuális gépek, amelyek segítik a hálózati funkciókat, például az útválasztást és a tűzfal optimalizálását. Ez az oktatóanyag feltételezi, hogy a **Windows Server 2019 Datacenter** rendszert használja. Ha kívánja, választhat másik operációs rendszert is.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVMNVA** megadása |
    | Region | Válassza ki az USA **keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | **Windows Server 2019 Datacenter** kiválasztása |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövő portok szabályai** |    |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
    |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | Válassza a **myVirtualNetwork** lehetőséget. |
    | Alhálózat | **DMZ** kiválasztása |
    | Nyilvános IP-cím | Válassza a **nincs** lehetőséget |
    | NIC hálózati biztonsági csoport | **Alapszintű** kiválasztása|
    | Nyilvános bejövő portok hálózata | Válassza a **Nincs** lehetőséget. |
   
5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. A keresőmezőbe írja be az **útválasztási táblázat** kifejezést. Ha az **útválasztási tábla** megjelenik a keresési eredmények között, válassza ki.

3. Az **útválasztási táblázat** lapon válassza a **Létrehozás** lehetőséget.

4. Az **alapbeállítások** lapon az **útvonal létrehozása táblában** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. |
    | **Példány adatai** |    |
    | Region | Válassza az **USA keleti régiója** lehetőséget. |
    | Name | Adja meg a **myroutetablepublic elemet**. |
    | Átjáró-útvonalak propagálása | Válassza az **Igen** lehetőséget. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Hozzon létre egy útválasztási táblázatot, Azure Portal." border="true":::

5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

## <a name="create-a-route"></a>Útvonal létrehozása

1. Az útválasztási táblázat kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

2. Válassza ki az útválasztási tábla **myroutetablepublic elemet** nevét.

3. A **myroutetablepublic elemet** lap **Beállítások** szakaszában válassza az **útvonalak** elemet.

4. Az útvonalak lapon kattintson a **+ Hozzáadás** gombra.

5. Az **útvonal hozzáadása** mezőben adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Útvonal neve | **ToPrivateSubnet** megadása |
    | Címelőtag | Adja meg a **10.0.1.0/24** (a korábban létrehozott **privát** alhálózat címtartomány) |
    | A következő ugrás típusa | Válassza a **Virtuális berendezés** lehetőséget. |
    | A következő ugrás címe | Adja meg a **10.0.2.4** (a **DMZ** -alhálózat címtartomány tartományán belüli címeket) |

6. Válassza az **OK** lehetőséget.

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

1. A virtuális hálózat kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális hálózatokat**.

2. Válassza ki a virtuális hálózati **myVirtualNetwork** nevét.

3. A **myVirtualNetwork** lap **Beállítások** szakaszában válassza az **alhálózatok** elemet.

4. A virtuális hálózat alhálózatának listájában válassza a **nyilvános** lehetőséget.

5. Az **útválasztási táblázatban** válassza ki a **myroutetablepublic elemet** létrehozott útválasztási táblázatot. 

6. Válassza a **Mentés** lehetőséget az útválasztási táblázat **nyilvános** alhálózathoz való hozzárendeléséhez.

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Rendeljen útválasztási táblázatot, alhálózati listát, virtuális hálózatot Azure Portal." border="true":::

## <a name="turn-on-ip-forwarding"></a>Az IP-továbbítás bekapcsolása

Ezután kapcsolja be az IP-továbbítást az új NVA virtuális géphez ( **myVMNVA**). Ha az Azure hálózati forgalmat küld a **myVMNVA**, ha a forgalmat egy másik IP-címhez szánják, akkor az IP-továbbítás a megfelelő helyre küldi a forgalmat.

1. A virtuális gép kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

2. Válassza ki a virtuális gép **myVMNVA** nevét.

3. A **myVMNVA** – Áttekintés lap **Beállítások** területén válassza a **hálózatkezelés** lehetőséget.

4. A MyVMNVA **hálózatkezelés** lapján válassza a hálózati adapter melletti hálózati adaptert **.**  A csatoló neve a **myvmnva**-vel kezdődik.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Hálózatkezelés, hálózati virtuális berendezés (NVA) virtuális gép (VM), Azure Portal" border="true":::

5. A hálózati adapter áttekintése lapon, a **Beállítások** területen válassza az **IP-konfigurációk** elemet.

6. Az **IP-konfigurációk** lapon állítsa be **az IP-továbbítás** **engedélyezve** beállítást, majd kattintson a **Mentés** gombra.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="IP-továbbítás engedélyezése" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Nyilvános és privát virtuális gépek létrehozása

Hozzon létre egy nyilvános virtuális gépet és egy privát virtuális GÉPET a virtuális hálózaton. Később azt fogja látni, hogy az Azure a NVA keresztül átirányítja a **nyilvános** alhálózat forgalmát a **privát** alhálózatra.


### <a name="public-vm"></a>Nyilvános virtuális gép

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVMPublic** megadása |
    | Region | Válassza ki az USA **keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | **Windows Server 2019 Datacenter** kiválasztása |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövő portok szabályai** |    |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
    |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | Válassza a **myVirtualNetwork** lehetőséget. |
    | Alhálózat | **Nyilvános** kiválasztása |
    | Nyilvános IP-cím | Válassza a **nincs** lehetőséget |
    | NIC hálózati biztonsági csoport | **Alapszintű** kiválasztása|
    | Nyilvános bejövő portok hálózata | Válassza a **Nincs** lehetőséget. |
   
5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

### <a name="private-vm"></a>Privát virtuális gép

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVMPrivate** megadása |
    | Region | Válassza ki az USA **keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | **Windows Server 2019 Datacenter** kiválasztása |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövő portok szabályai** |    |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
    |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | Válassza a **myVirtualNetwork** lehetőséget. |
    | Alhálózat | **Magánjellegű** kijelölése |
    | Nyilvános IP-cím | Válassza a **nincs** lehetőséget |
    | NIC hálózati biztonsági csoport | **Alapszintű** kiválasztása|
    | Nyilvános bejövő portok hálózata | Válassza a **Nincs** lehetőséget. |
   
5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

### <a name="sign-in-to-private-vm"></a>Bejelentkezés a privát virtuális gépre

1. A saját virtuális gép kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

2. Válassza ki a saját virtuális gép **myVmPrivate** nevét.

3. A virtuális gép menüsorában válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

4. A **kapcsolat** lapon válassza a kék **használat Bastion** gombot.

5. A **megerősített** lapon adja meg a virtuális géphez korábban létrehozott felhasználónevet és jelszót.

6. Válassza a **Kapcsolódás** lehetőséget.

### <a name="configure-firewall"></a>Tűzfal konfigurálása

Egy későbbi lépésben a nyomkövetési útvonal eszköz használatával tesztelheti az útválasztást. A nyomkövetési útvonal a Internet Control Message Protocolt (ICMP) használja, amelyet a Windows tűzfal alapértelmezés szerint megtagad. 

Engedélyezze az ICMP-t a Windows tűzfalon keresztül.

1. A **myVMPrivate** megerősített kapcsolataiban nyissa meg a PowerShellt rendszergazdai jogosultságokkal.

2. Adja meg a következő parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ebben az oktatóanyagban nyomkövetési útvonalat használ az Útválasztás teszteléséhez. Éles környezetekben nem ajánlott az ICMP engedélyezése a Windows tűzfalon keresztül.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Az IP-továbbítás bekapcsolása a myVMNVA belül

[Bekapcsolta az IP-továbbítást](#turn-on-ip-forwarding) a virtuális gép hálózati adapteréhez az Azure használatával. A virtuális gép operációs rendszerének a hálózati forgalmat is továbbítania kell. 

Kapcsolja be az IP-továbbítást a **myVMNVA** ezekkel a parancsokkal.

1. Nyisson meg egy távoli asztalt a **myVMPrivate** virtuális gépen a powershellből a **myVMNVA** virtuális gépre:

    ```powershell
    mstsc /v:myvmnva
    ```

2. A **myVMNVA** virtuális gépen a powershellből írja be ezt a parancsot az IP-továbbítás bekapcsolásához:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Indítsa újra a **myVMNVA**.

    ```powershell
    Restart-Computer
    ```

4. A **myVMNVA** újraindítása után hozzon létre egy távoli asztali munkamenetet a **myVMPublic**. 
    
    Miközben továbbra is csatlakozik a **myVMPrivate**-hez, nyissa meg a PowerShellt, és futtassa a következő parancsot:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. A **myVMPublic** távoli asztalán nyissa meg a PowerShellt.

6. Engedélyezze az ICMP-t a Windows tűzfalon a következő parancs beírásával:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>A hálózati forgalom útválasztásának tesztelése

Először is tesztelje a **myVMPublic** és **myVMPrivate** közötti hálózati forgalom útválasztását.

1. A **MyVMPublic** PowerShell-ből írja be a következő parancsot:

    ```powershell
    tracert myvmprivate
    ```

    A válasz az alábbi példához hasonló:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * Láthatja, hogy az első Ugrás a 10.0.2.4, amely **myVMNVA** magánhálózati IP-címe. 

    * A második Ugrás a **myVMPrivate**: 10.0.1.4 magánhálózati IP-címére mutat. 

    Korábban felvette az útvonalat a **myroutetablepublic elemet** útválasztási táblázatba, és hozzárendelte a *nyilvános* alhálózathoz. Az Azure eljuttatta a forgalmat a NVA keresztül, és nem közvetlenül a *privát* alhálózatra.

2. Zárjuk be a távoli asztali munkamenetet a **myVMPublic**-be, amely továbbra is csatlakozik a **myVMPrivate**.

3. Nyissa meg a PowerShellt a **myVMPrivate**, írja be a következő parancsot:

    ```powershell
    tracert myvmpublic
    ```

    Ez a parancs a *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre irányuló hálózati forgalom útválasztását teszteli. A válasz az alábbi példához hasonló:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Láthatja, hogy az Azure közvetlenül a *myVMPrivate* -ből a *myVMPublic*-be irányítja a forgalmat. Alapértelmezés szerint az Azure közvetlenül irányítja a forgalmat az alhálózatok között.

4. A megerősített munkamenet lezárása a **myVMPrivate**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforráscsoport már nem szükséges, törölje a **myResourceGroup** és a benne lévő összes erőforrást:

1. Az erőforráscsoport kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **erőforráscsoportok** elemet.

2. Válassza ki az erőforráscsoport **myResourceGroup** nevét.

3. Válassza az **Erőforráscsoport törlése** elemet.

4. A megerősítő párbeszédpanelen írja be  a myResourceGroup **nevet az erőforráscsoport neve** mezőbe, majd válassza a **Törlés** lehetőséget. 


## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz.
* Létrehozott egy egyszerű NVA, amely átirányítja a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. 

Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)-ről különböző előre konfigurált NVA telepíthet, amelyek számos hasznos hálózati funkciót biztosítanak. 

További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózat hálózati forgalmának szűréséhez tekintse meg a következőt:
> [!div class="nextstepaction"]
> [Oktatóanyag: hálózati forgalom szűrése hálózati biztonsági csoporttal a Azure Portal használatával](tutorial-filter-network-traffic.md)
