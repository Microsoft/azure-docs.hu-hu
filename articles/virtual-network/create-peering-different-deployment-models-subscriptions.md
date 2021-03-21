---
title: Azure-beli virtuális hálózati társítás létrehozása – különböző üzemi modellek – különböző előfizetések
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan hozhat létre virtuális hálózatokat a különböző Azure-előfizetésekben található különböző Azure-alapú üzemi modelleken keresztül létrehozott virtuális hálózatok között.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 29a64931ac92eacf8948ae067118b6b25198c85d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223465"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Virtuális hálózati társítás létrehozása – különböző üzembe helyezési modellek és előfizetések

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre virtuális hálózatot a különböző üzembe helyezési modelleken keresztül létrehozott virtuális hálózatok között. A virtuális hálózatok különböző előfizetésekben találhatók. A két virtuális hálózat összekapcsolása lehetővé teszi, hogy a különböző virtuális hálózatok erőforrásai azonos sávszélességgel és késéssel kommunikáljanak egymással, mintha az erőforrások ugyanabban a virtuális hálózaton lennének. További információ a [virtuális hálózatok](virtual-network-peering-overview.md)összevonásáról.

A virtuális hálózati társítások létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok azonos vagy eltérő előfizetésekben találhatók-e, valamint hogy a virtuális hálózatok milyen Azure-alapú üzemi [modellt](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hoznak létre. A következő táblázat forgatókönyvére kattintva megtudhatja, hogyan hozhat létre egy virtuális hálózati társat más forgatókönyvekben:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ugyanaz|
|[Mindkét Resource Manager](create-peering-different-subscriptions.md) |Különböző|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models.md) |Ugyanaz|

Nem hozható létre virtuális hálózati társítás a klasszikus üzemi modellen keresztül üzembe helyezett két virtuális hálózat között. Ez az oktatóanyag az ugyanabban a régióban található virtuális hálózatokat használja. Ez az oktatóanyag az azonos régióban található virtuális hálózatokat ismerteti. A különböző [támogatott régiókban](virtual-network-manage-peering.md#cross-region)lévő egyenrangú virtuális hálózatokat is használhatja. Javasoljuk, hogy ismerkedjen meg a társítási [követelményekkel és a megkötésekkel](virtual-network-manage-peering.md#requirements-and-constraints) a virtuális hálózatok társítása előtt.

A különböző előfizetésekben található virtuális hálózatok közötti virtuális hálózati társítások létrehozásakor az előfizetések ugyanahhoz a Azure Active Directory bérlőhöz társíthatók. Ha még nem rendelkezik Azure Active Directory Bérlővel, gyorsan [létrehozhat egyet](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant).

A virtuális hálózati társítás létrehozásához használhatja a [Azure Portal](#portal), az Azure [parancssori felületét](#cli) (CLI) vagy az Azure [PowerShellt](#powershell) . Kattintson bármelyik előző eszköz hivatkozásaira, hogy közvetlenül a virtuális hálózati társítások létrehozásához szükséges lépéseket válassza a választott eszköz használatával.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Peering-Azure Portal létrehozása

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik engedéllyel, használhatja ugyanazt a fiókot az összes lépéshez, hagyja ki a portálról való kijelentkezés lépéseit, és hagyja ki a lépéseket a virtuális hálózatokra vonatkozó további felhasználói engedélyek kiosztásához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) felhasználóként. A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#permissions)társítási engedélyek.
2. Kattintson az **+ új**, majd a **hálózatkezelés**, majd a **virtuális hálózat** elemre.
3. A **virtuális hálózat létrehozása** panelen adja meg vagy válassza ki a következő beállítások értékeit, majd kattintson a **Létrehozás** gombra:
    - **Név**: *myVnetA*
    - **Címterület**: *10.0.0.0/16*
    - **Alhálózat neve**: *default*
    - **Alhálózati címtartomány**: *10.0.0.0/24*
    - **Előfizetés**: válassza az A előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** elemet, és adja meg a *myResourceGroupA*
    - Hely: *USA keleti* **régiója**
4. A portál tetején található **erőforrások keresése** mezőben írja be a következőt: *myVnetA*. Kattintson a **myVnetA** elemre, amikor megjelenik a keresési eredmények között. Megjelenik egy panel a **myVnetA** virtuális hálózathoz.
5. A megjelenő **myVnetA** panelen kattintson a **hozzáférés-vezérlés (iam)** elemre a panel bal oldalán található beállítások függőleges listájában.
6. A megjelenő **myVnetA-hozzáférés-vezérlés (iam)** panelen kattintson a **+ szerepkör-hozzárendelés hozzáadása** lehetőségre.
7. A megjelenő **szerepkör-hozzárendelés hozzáadása** panelen válassza a **hálózati közreműködő** elemet a **szerepkör** mezőben.
8. A **kiválasztás** mezőben válassza a felhasználób lehetőséget, vagy írja be a felhasználób e-mail-címét a kereséshez. A megjelenített felhasználók listája ugyanabból a Azure Active Directory bérlőből származik, mint az a virtuális hálózat, amelyhez a társítást beállítja. Kattintson a Felhasználób elemre, amikor megjelenik a listában.
9. Kattintson a **Mentés** gombra.
10. Jelentkezzen ki a portálról felhasználóként, majd jelentkezzen be Felhasználób-ként.
11. Kattintson az **+ új** elemre, írja be a *virtuális hálózat* **kifejezést a keresés a piactéren** mezőbe, majd kattintson a **virtuális hálózat** elemre a keresési eredmények között.
12. A megjelenő **Virtual Network** panelen válassza a **klasszikus** lehetőséget a **telepítési modell kiválasztása** mezőben, majd kattintson a **Létrehozás** gombra.
13. A megjelenő virtuális hálózat létrehozása (klasszikus) mezőben adja meg a következő értékeket:

    - **Név**: *myVnetB*
    - **Címterület**: *10.1.0.0/16*
    - **Alhálózat neve**: *default*
    - **Alhálózati címtartomány**: *10.1.0.0/24*
    - **Előfizetés**: válassza a B előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** elemet, és adja meg a *myResourceGroupB*
    - Hely: *USA keleti* **régiója**

14. A portál tetején található **erőforrások keresése** mezőben írja be a következőt: *myVnetB*. Kattintson a **myVnetB** elemre, amikor megjelenik a keresési eredmények között. Megjelenik egy panel a **myVnetB** virtuális hálózathoz.
15. A megjelenő **myVnetB** panelen kattintson a **Tulajdonságok** elemre a panel bal oldalán található beállítások függőleges listájában. Másolja ki az **erőforrás-azonosítót**, amelyet egy későbbi lépésben használ. Az erőforrás-azonosító az alábbi példához hasonló: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Hajtsa végre a 5-9-es lépést a myVnetB, majd írja be a **felhasználót** a 8. lépésben.
17. Jelentkezzen ki a portálról Felhasználób, és jelentkezzen be felhasználóként.
18. A portál tetején található **erőforrások keresése** mezőben írja be a következőt: *myVnetA*. Kattintson a **myVnetA** elemre, amikor megjelenik a keresési eredmények között. Megjelenik egy panel a **myVnet** virtuális hálózathoz.
19. Kattintson a **myVnetA** elemre.
20. A megjelenő **myVnetA** **panelen kattintson a** panel bal oldalán lévő beállítások függőleges listájában található társítások elemre.
21. A megjelenő **myVnetA-társak** panelen kattintson a **+ Hozzáadás** gombra.
22. A megjelenő **egyenrangú panel hozzáadása** panelen írja be vagy válassza ki a következő beállításokat, majd kattintson az **OK** gombra:
     - **Név**: *myVnetAToMyVnetB*
     - **Virtuális hálózat telepítési modellje**: válassza a **klasszikus** lehetőséget.
     - **Ismerem az erőforrás-azonosítót**: jelölje be ezt a jelölőnégyzetet.
     - **Erőforrás-azonosító**: adja meg a myVnetB erőforrás-azonosítóját a 15. lépésből.
     - **Virtuális hálózati hozzáférés engedélyezése:** Győződjön meg arról, hogy az **engedélyezve** beállítás be van jelölve.
    Ebben az oktatóanyagban más beállítások nem használhatók. Az összes egyenrangú beállítás megismeréséhez olvassa el a [virtuális hálózati](virtual-network-manage-peering.md#create-a-peering)társítások kezelése című témakört.
23. Miután az **OK gombra** kattintott az előző lépésben, a társítás **hozzáadása** panel bezárul, és újra megjelenik a **myVnetA** . Néhány másodperc elteltével a létrehozott társítás megjelenik a panelen. A **kapcsolat** a létrehozott **MyVnetAToMyVnetB** -társítás **egyenrangú állapot** oszlopában szerepel. A társítás már létrejött. Nincs szükség a virtuális hálózat (klasszikus) és a virtuális hálózat (Resource Manager) társítására.

    A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatokban lévő erőforrások nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. Nem **kötelező**: bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
25. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-portal) című szakaszának lépéseit.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering létrehozása – Azure CLI

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik engedéllyel, használhatja ugyanazt a fiókot az összes lépéshez, ugorja át az Azure-ba való kijelentkezés lépéseit, és távolítsa el a felhasználói szerepkör-hozzárendeléseket létrehozó parancsfájl sorait. Cserélje le az UserA@azure.com és UserB@azure.com az összes következő parancsfájlt a felhasználó és a felhasználób használt felhasználónevek közül. Hajtsa végre az alábbi lépéseket a klasszikus Azure CLI-vel és az Azure CLI-vel. A Azure Cloud Shell lépéseinek elvégzéséhez egyszerűen válassza **ki a kipróbálás** gombot az alábbi lépések bármelyikén, vagy telepítse a [klasszikus CLI](/cli/azure/install-classic-cli) -t és a [parancssori](/cli/azure/install-azure-cli) felületet, és futtassa a parancsokat a helyi számítógépen.

1. Ha a Cloud Shell használja, ugorjon a 2. lépésre, mert a Cloud Shell automatikusan bejelentkezik az Azure-ba. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-ba a `azure login` parancs használatával.
2. Futtassa a klasszikus CLI-t a Service Management módban a parancs beírásával `azure config mode asm` .
3. A virtuális hálózat (klasszikus) létrehozásához adja meg a következő klasszikus CLI-parancsot:

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. A fennmaradó lépéseket egy bash-rendszerhéj használatával kell befejezni az Azure CLI-vel (nem a klasszikus CLI-vel).
5. Másolja a következő szkriptet egy szövegszerkesztőbe a SZÁMÍTÓGÉPén. Cserélje le `<SubscriptionB-Id>` az értékét az előfizetés-azonosítójával. Ha nem ismeri az előfizetés-azonosítóját, írja be a `az account show` parancsot. A kimenetben lévő **azonosító** értéke az előfizetés azonosítója. másolja a módosított parancsfájlt, illessze be a CLI-munkamenetbe, majd nyomja meg a gombot `Enter` .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Amikor a 4. lépésben létrehozta a virtuális hálózatot (klasszikus), az Azure létrehozta a virtuális hálózatot az *alapértelmezett hálózati* erőforráscsoporthoz.
6. Jelentkezzen be Felhasználób az Azure-ból, és jelentkezzen be felhasználóként a CLI-ben.
7. Hozzon létre egy erőforráscsoportot és egy virtuális hálózatot (Resource Manager). Másolja a következő parancsfájlt, illessze be a CLI-munkamenetbe, majd nyomja meg a gombot `Enter` .

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Hozzon létre egy virtuális hálózatot a különböző üzembe helyezési modelleken keresztül létrehozott két virtuális hálózat között. Másolja a következő szkriptet egy szövegszerkesztőbe a SZÁMÍTÓGÉPén. Cserélje le `<SubscriptionB-id>` az értékét az előfizetés-azonosítójával. Ha nem ismeri az előfizetés-azonosítóját, írja be a `az account show` parancsot. A kimenetben lévő **azonosító** értéke az előfizetés azonosítója. az Azure létrehozta a 4. lépésben létrehozott virtuális hálózatot (klasszikus) az *alapértelmezett hálózatkezelés* nevű erőforráscsoport esetében. Illessze be a módosított parancsfájlt a CLI-munkamenetbe, majd nyomja le a gombot `Enter` .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. A szkript végrehajtása után tekintse át a virtuális hálózat (Resource Manager) társítását. Másolja a következő parancsfájlt, majd illessze be a CLI-munkamenetbe:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    A kimenet a **PeeringState** oszlopban **csatlakozik** .

    A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatokban lévő erőforrások nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. Nem **kötelező**: bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
11. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-cli) című részében ismertetett lépéseket.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering létrehozása – PowerShell

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik engedéllyel, használhatja ugyanazt a fiókot az összes lépéshez, ugorja át az Azure-ba való kijelentkezés lépéseit, és távolítsa el a felhasználói szerepkör-hozzárendeléseket létrehozó parancsfájl sorait. Cserélje le az UserA@azure.com és UserB@azure.com az összes következő parancsfájlt a felhasználó és a felhasználób használt felhasználónevek közül. 

1. Telepítse a PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) és [az modulok legújabb](https://www.powershellgallery.com/packages/Az) verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítsa el a PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be a Felhasználób előfizetésére Felhasználób néven a parancs beírásával `Add-AzureAccount` . A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#permissions)társítási engedélyek.
4. Virtuális hálózat (klasszikus) PowerShell-lel történő létrehozásához létre kell hoznia egy újat, vagy módosítania kell egy meglévő hálózati konfigurációs fájlt. Útmutató [hálózati konfigurációs fájlok exportálásához, frissítéséhez és importálásához](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). A fájlnak tartalmaznia kell a következő **VirtualNetworkSite** elemet az oktatóanyagban használt virtuális hálózathoz:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > A módosított hálózati konfigurációs fájlok importálása a meglévő virtuális hálózatok (klasszikus) módosítását eredményezheti az előfizetésében. Győződjön meg arról, hogy csak az előző virtuális hálózatot adja hozzá, és hogy nem módosítja vagy nem távolítja el az előfizetéshez tartozó meglévő virtuális hálózatokat. 

5. Jelentkezzen be a Felhasználób-előfizetésbe a Resource Manager-parancsok Felhasználób való használatához a parancs beírásával `Connect-AzAccount` .
6. Rendeljen felhasználói engedélyeket a B virtuális hálózathoz. másolja a következő szkriptet egy szövegszerkesztőbe a SZÁMÍTÓGÉPén, és cserélje le a (z `<SubscriptionB-id>` ) b előfizetés azonosítójával. Ha nem ismeri az előfizetés azonosítóját, írja be a `Get-AzSubscription` parancsot a megtekintéshez. A visszaadott kimenetben lévő **azonosító** értéke az előfizetés azonosítója. Az Azure létrehozta az *alapértelmezett-hálózatkezelés* nevű erőforráscsoport 4. lépésében létrehozott virtuális hálózatot (klasszikus). A szkript végrehajtásához másolja a módosított parancsfájlt, illessze be a PowerShellbe, majd nyomja le a gombot `Enter` .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Jelentkezzen ki az Azure-ból Felhasználób, és jelentkezzen be a felhasználó előfizetéséhez a Usera néven a parancs beírásával `Connect-AzAccount` . A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#permissions)társítási engedélyek.
8. Hozza létre a virtuális hálózatot (Resource Manager) úgy, hogy átmásolja a következő parancsfájlt a PowerShellbe, majd lenyomja a parancsot `Enter` :

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Felhasználób engedélyek kiosztása a myVnetA. Másolja a következő szkriptet egy szövegszerkesztőbe a SZÁMÍTÓGÉPén, és cserélje le az `<SubscriptionA-Id>` a előfizetés-azonosítóra. Ha nem ismeri az előfizetés azonosítóját, írja be a `Get-AzSubscription` parancsot a megtekintéshez. A visszaadott kimenetben lévő **azonosító** értéke az előfizetés azonosítója. Illessze be a parancsfájl módosított verzióját a PowerShellbe, majd nyomja meg a parancsot a `Enter` végrehajtásához.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Másolja a következő parancsfájlt egy szövegszerkesztőbe a SZÁMÍTÓGÉPén, és cserélje le a (z `<SubscriptionB-id>` ) B előfizetés azonosítójával. A myVnetA myVNetB való társításához másolja a módosított parancsfájlt, illessze be a PowerShellbe, majd nyomja meg a gombot `Enter` .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Tekintse meg a myVnetA társítási állapotát a következő parancsfájl másolásával, a PowerShellbe való beillesztésével és a gomb megnyomásával `Enter` .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Az állapot **csatlakoztatva** van. Ha a társítást a myVnetB-ből a myVnetA-be állítja be, a rendszer a **csatlakoztatott** állapotra vált.

    A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatokban lévő erőforrások nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. Nem **kötelező**: bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
13. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-powershell) című részében ismertetett lépéseket.

## <a name="delete-resources"></a><a name="delete"></a>Erőforrások törlése
Az oktatóanyag befejezése után érdemes lehet törölni az oktatóanyagban létrehozott erőforrásokat, így nem számítunk fel használati díjakat. Egy erőforráscsoport törlésekor az erőforráscsoport összes erőforrása is törlődik.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. A portál keresési mezőjébe írja be a **myResourceGroupA** értéket. A keresési eredmények között kattintson a **myResourceGroupA** elemre.
2. A **myResourceGroupA** panelen kattintson a **Törlés** ikonra.
3. A törlés megerősítéséhez írja be az **erőforráscsoport neve** mezőbe az **myResourceGroupA** nevet, majd kattintson a **Törlés** gombra.
4. A portál tetején található **erőforrások keresése** mezőben írja be a következőt: *myVnetB*. Kattintson a **myVnetB** elemre, amikor megjelenik a keresési eredmények között. Megjelenik egy panel a **myVnetB** virtuális hálózathoz.
5. A **myVnetB** panelen kattintson a **Törlés** elemre.
6. A törlés megerősítéséhez kattintson az **Igen** gombra a **virtuális hálózat törlése** mezőben.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Jelentkezzen be az Azure-ba a CLI használatával a virtuális hálózat (Resource Manager) törléséhez a következő paranccsal:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Jelentkezzen be az Azure-ba a klasszikus CLI használatával a virtuális hálózat (klasszikus) törléséhez a következő parancsokkal:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. A PowerShell-parancssorba írja be a következő parancsot a virtuális hálózat (Resource Manager) törléséhez:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. A virtuális hálózat (klasszikus) PowerShell-lel való törléséhez módosítania kell egy meglévő hálózati konfigurációs fájlt. Útmutató [hálózati konfigurációs fájlok exportálásához, frissítéséhez és importálásához](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Távolítsa el a következő VirtualNetworkSite elemet az oktatóanyagban használt virtuális hálózathoz:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
     <AddressSpace>
       <AddressPrefix>10.1.0.0/16</AddressPrefix>
     </AddressSpace>
     <Subnets>
       <Subnet name="default">
         <AddressPrefix>10.1.0.0/24</AddressPrefix>
       </Subnet>
     </Subnets>
   </VirtualNetworkSite>
   ```

   > [!WARNING]
   > A módosított hálózati konfigurációs fájlok importálása a meglévő virtuális hálózatok (klasszikus) módosítását eredményezheti az előfizetésében. Győződjön meg arról, hogy csak az előző virtuális hálózatot távolítja el, és hogy nem módosítja vagy nem távolítja el az előfizetéshez tartozó többi meglévő virtuális hálózatot. 

## <a name="next-steps"></a>Következő lépések

- Alaposan megismerheti a fontos [virtuális hálózati társítási korlátozásokat és a viselkedést](virtual-network-manage-peering.md#requirements-and-constraints) , mielőtt létrehozza a virtuális hálózatokat az éles környezetben való használatra.
- További információ az összes [virtuális hálózati társ](virtual-network-manage-peering.md#create-a-peering)-összevonási beállításról.
- Megtudhatja, hogyan [hozhat létre egy sugaras hálózati topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) a virtuális hálózati társítással.