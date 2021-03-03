---
title: VNet-társítás létrehozása – különböző előfizetések
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan hozhat létre virtuális hálózatokat a Resource Manageren keresztül létrehozott virtuális hálózatok között, amelyek különböző Azure-előfizetésekben találhatók ugyanazon vagy különböző Azure Active Directory-bérlőben.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: kumud
ms.openlocfilehash: 9f3ea595c3cfacc479deea7b7db7689785e3b520
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694954"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions-and-azure-active-directory-tenants"></a>Hozzon létre egy virtuális hálózati társ-erőforrás-kezelőt, különböző előfizetéseket és Azure Active Directory bérlőket

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre virtuális hálózatot a Resource Manager használatával létrehozott virtuális hálózatok között. A virtuális hálózatok különböző előfizetésekben találhatók, amelyek különböző Azure Active Directory (Azure AD-) bérlőhöz tartozhatnak. A két virtuális hálózat összekapcsolása lehetővé teszi, hogy a különböző virtuális hálózatok erőforrásai azonos sávszélességgel és késéssel kommunikáljanak egymással, mintha az erőforrások ugyanabban a virtuális hálózaton lennének. További információ a [virtuális hálózatok](virtual-network-peering-overview.md)összevonásáról.

A virtuális hálózati társítások létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok azonos vagy eltérő előfizetésekben találhatók-e, valamint hogy a virtuális hálózatok milyen Azure-alapú üzemi [modellt](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hoznak létre. A következő táblázat forgatókönyvének kiválasztásával megtudhatja, hogyan hozhat létre virtuális hálózati társítást más forgatókönyvekben:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ugyanaz|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models.md) |Ugyanaz|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models-subscriptions.md) |Különböző|

Nem hozható létre virtuális hálózati társítás a klasszikus üzemi modellen keresztül üzembe helyezett két virtuális hálózat között. Ha a klasszikus üzemi modellel létrehozott virtuális hálózatokat is össze kell kapcsolni, használhat Azure- [VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a virtuális hálózatok összekapcsolásához.

Ez az oktatóanyag az azonos régióban található virtuális hálózatokat ismerteti. A különböző [támogatott régiókban](virtual-network-manage-peering.md#cross-region)lévő egyenrangú virtuális hálózatokat is használhatja. Javasoljuk, hogy ismerkedjen meg a társítási [követelményekkel és a megkötésekkel](virtual-network-manage-peering.md#requirements-and-constraints) a virtuális hálózatok társítása előtt.

A virtuális hálózati társítás létrehozásához használhatja a [Azure Portal](#portal), az Azure [parancssori felületét](#cli) (CLI), az Azure [PowerShellt](#powershell)vagy egy [Azure Resource Manager sablont](#template) . Válassza ki bármelyik előző eszköz hivatkozását, hogy közvetlenül a virtuális hálózati társítások létrehozásához szükséges lépésekhez lépjen a választott eszköz használatával.

Ha a virtuális hálózatok különböző előfizetésekben találhatók, és az előfizetések különböző Azure Active Directory bérlőhöz vannak társítva, a folytatás előtt végezze el a következő lépéseket:
1. Adja hozzá a felhasználót minden Active Directory bérlőről [vendégként](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) a szemközti Azure Active Directory-bérlőben.
1. Minden egyes felhasználónak el kell fogadnia az ellenkező Azure Active Directory-bérlő vendégfelhasználói meghívását.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Peering-Azure Portal létrehozása

Az alábbi lépések különböző fiókokat használnak az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik engedéllyel, használhatja ugyanazt a fiókot az összes lépéshez, hagyja ki a portálról való kijelentkezés lépéseit, és hagyja ki a lépéseket a virtuális hálózatokra vonatkozó további felhasználói engedélyek kiosztásához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) *felhasználóként*. A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#permissions)társítási engedélyek.
2. Válassza **az + erőforrás létrehozása** lehetőséget, válassza a **hálózatkezelés**, majd a **virtuális hálózat** lehetőséget.
3. Válassza ki vagy adja meg a következő értékeket a következő beállításokhoz, majd válassza a **Létrehozás** lehetőséget:
    - **Név**: *myVnetA*
    - **Címterület**: *10.0.0.0/16*
    - **Alhálózat neve**: *default*
    - **Alhálózati címtartomány**: *10.0.0.0/24*
    - **Előfizetés**: válassza az A előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** elemet, és adja meg a *myResourceGroupA*
    - Hely: *USA keleti* **régiója**
4. A portál tetején található **erőforrások keresése** mezőben írja be a következőt: *myVnetA*. Válassza ki a **myVnetA** , amikor megjelenik a keresési eredmények között.
5. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget a bal oldalon található beállítások függőleges listájában.
6. A **myVnetA-hozzáférés-vezérlés (iam)** területen válassza a **+ szerepkör-hozzárendelés hozzáadása** elemet.
7. Válassza a **hálózati közreműködő** elemet a **szerepkör** mezőben.
8. A **kiválasztás** mezőben válassza a *felhasználób* lehetőséget, vagy írja be a felhasználób e-mail-címét a kereséshez.
9. Kattintson a **Mentés** gombra.
10. A **myVnetA-hozzáférés-vezérlés (iam)** területen válassza a **Tulajdonságok** lehetőséget a bal oldalon található beállítások függőleges listájában. Másolja ki az **erőforrás-azonosítót**, amelyet egy későbbi lépésben használ. Az erőforrás-azonosító a következő példához hasonló: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA` .
11. Jelentkezzen ki a portálról felhasználóként, majd jelentkezzen be Felhasználób-ként.
12. Hajtsa végre a 2-3 lépést, írja be vagy válassza ki az alábbi értékeket a 3. lépésben:

    - **Név**: *myVnetB*
    - **Címterület**: *10.1.0.0/16*
    - **Alhálózat neve**: *default*
    - **Alhálózati címtartomány**: *10.1.0.0/24*
    - **Előfizetés**: válassza a B előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** elemet, és adja meg a *myResourceGroupB*
    - Hely: *USA keleti* **régiója**

13. A portál tetején található **erőforrások keresése** mezőben írja be a következőt: *myVnetB*. Válassza ki a **myVnetB** , amikor megjelenik a keresési eredmények között.
14. A **myVnetB** területen válassza a **Tulajdonságok** lehetőséget a bal oldalon található beállítások függőleges listájában. Másolja ki az **erőforrás-azonosítót**, amelyet egy későbbi lépésben használ. Az erőforrás-azonosító a következő példához hasonló: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB` .
15. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget a **myVnetB** területen, majd hajtsa végre a myVnetB 5-10 **-es lépéseket a 8** . lépésben.
16. Jelentkezzen ki a portálról Felhasználób, és jelentkezzen be felhasználóként.
17. A portál tetején található **erőforrások keresése** mezőben írja be a következőt: *myVnetA*. Válassza ki a **myVnetA** , amikor megjelenik a keresési eredmények között.
18. Válassza a **myVnetA** lehetőséget.
19. A **Beállítások** **területen válassza a** társítások lehetőséget.
20. A **myVnetA** alatt válassza a **+ Hozzáadás** lehetőséget.
21. A társítás **hozzáadása** területen adja meg vagy válassza ki a következő beállításokat, majd kattintson **az OK gombra**:
     - **Név**: *myVnetAToMyVnetB*
     - **Virtuális hálózat telepítési modellje**: válassza a **Resource Manager** lehetőséget.
     - **Ismerem az erőforrás-azonosítót**: jelölje be ezt a jelölőnégyzetet.
     - **Erőforrás-azonosító**: adja meg a 14. lépés erőforrás-azonosítóját.
     - **Virtuális hálózati hozzáférés engedélyezése:** Győződjön meg arról, hogy az **engedélyezve** beállítás be van jelölve.
    Ebben az oktatóanyagban más beállítások nem használhatók. Az összes egyenrangú beállítás megismeréséhez olvassa el a [virtuális hálózati](virtual-network-manage-peering.md#create-a-peering)társítások kezelése című témakört.
22. A létrehozott társítás megjelenik egy rövid várakozás után, miután az előző lépésben kiválasztja az **OK gombot** . A **kezdeményezve** érték szerepel a létrehozott **MyVnetAToMyVnetB** -társítás **egyenrangú állapot** oszlopában. MyVnetA a myVnetB, de most már egyenrangú myVnetB kell a myVnetA. A társítást mindkét irányban létre kell hozni ahhoz, hogy a virtuális hálózatok erőforrásai kommunikálhassanak egymással.
23. Jelentkezzen ki a portálról felhasználóként, és jelentkezzen be Felhasználób néven.
24. Hajtsa végre ismét a 17-21 lépést a myVnetB. A 21. lépésben adja meg a társ- *myVnetBToMyVnetA* nevet, válassza a *MyVnetA* lehetőséget a **virtuális hálózat** számára, majd írja be az azonosítót a 10. lépésből az **erőforrás-azonosító** mezőbe.
25. Néhány másodpercet követően, miután az **OK gombra** kattintott a myVnetB való társítás létrehozásához, az imént létrehozott **myVnetBToMyVnetA** -TÁRSÍTÁS a társítási **állapot** oszlopban található **kapcsolattal** jelenik meg.
26. Jelentkezzen ki a portálról Felhasználób, és jelentkezzen be felhasználóként.
27. Hajtsa végre ismét a 17-19 lépést. A **myVnetAToVNetB** -társítás társítási **állapota** mostantól szintén **csatlakoztatva** van. A rendszer sikeresen létrehozta a társítást, miután a társítás **állapota** oszlopban mindkét virtuális hálózat esetében megjelenik a **Csatlakozás** . A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatokban lévő erőforrások nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. Nem **kötelező**: bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
29. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-portal) című szakaszának lépéseit.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering létrehozása – Azure CLI

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik engedéllyel, használhatja ugyanazt a fiókot az összes lépéshez, ugorja át az Azure-ba való kijelentkezés lépéseit, és távolítsa el a felhasználói szerepkör-hozzárendeléseket létrehozó parancsfájl sorait. Cserélje le az UserA@azure.com és UserB@azure.com az összes következő parancsfájlt a felhasználó és a felhasználób használt felhasználónevek közül.

A következő parancsfájlok:

- Az Azure CLI 2.0.4 vagy újabb verziójára van szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha frissítenie kell, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)ismertető témakört.
- Egy bash-rendszerhéjban működik. Az Azure CLI-szkriptek Windows-ügyfeleken való futtatásának lehetőségeiről az [Azure CLI Windows rendszeren való telepítésével](/cli/azure/install-azure-cli-windows) foglalkozó témakörben talál további információt.

A CLI és a hozzá tartozó függőségek telepítése helyett használhatja a Azure Cloud Shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki az alábbi szkriptben a **TRY IT (kipróbálás** ) gombot, amely meghívja a Cloud shell, amelybe be tud jelentkezni az Azure-fiókjába.

1. Nyisson meg egy CLI-munkamenetet, és jelentkezzen be az Azure-ba felhasználóként a `azure login` parancs használatával. A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#permissions)társítási engedélyek.
2. Másolja a következő parancsfájlt egy szövegszerkesztőbe a SZÁMÍTÓGÉPén, cserélje le `<SubscriptionA-Id>` az előfizetést azonosítóra, majd másolja a módosított parancsfájlt, illessze be a CLI-munkamenetbe, majd nyomja meg a gombot `Enter` . Ha nem ismeri az előfizetés-azonosítóját, írja be a `az account show` parancsot. A kimenetben lévő **azonosító** értéke az előfizetés azonosítója.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Jelentkezzen ki az Azure-ból felhasználóként a `az logout` parancs használatával, majd jelentkezzen be az Azure-ba felhasználób. A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#permissions)társítási engedélyek.
4. Hozzon létre myVnetB. Másolja a 2. lépésben szereplő parancsfájl tartalmát a számítógép egy szövegszerkesztőbe. Cserélje le `<SubscriptionA-Id>` a helyére a SUBSCRIPTIONB azonosítóját. Módosítsa a 10.0.0.0/16 – 10.1.0.0/16 változót, módosítsa az összeset a B értékre, és az összes BS-et A-re. másolja a módosított parancsfájlt, illessze be a CLI-munkamenetbe, majd nyomja meg a gombot `Enter` .
5. Jelentkezzen ki az Azure-ból Felhasználób, és jelentkezzen be az Azure-ba felhasználóként.
6. Hozzon létre egy virtuális hálózati társat a myVnetA-ből a myVnetB-be. Másolja a következő parancsfájl tartalmát egy szövegszerkesztőbe a SZÁMÍTÓGÉPén. Cserélje le `<SubscriptionB-Id>` a helyére a SUBSCRIPTIONB azonosítóját. A szkript végrehajtásához másolja a módosított parancsfájlt, illessze be a CLI-munkamenetbe, majd nyomja le az ENTER billentyűt.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Tekintse meg a myVnetA egyenrangú állapotát.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Az állapot **kezdeményezve**. Ha a társítást a myVnetB-ből a myVnetA-ből hozza létre, akkor a **kapcsolat a csatlakozás** után módosul.

8. Jelentkezzen ki a felhasználótól az Azure-ból, és jelentkezzen be az Azure-ba Felhasználób.
9. Hozza létre a társítást a myVnetB-ből a myVnetA-be. Másolja a 6. lépésben szereplő parancsfájl tartalmát a számítógép egy szövegszerkesztőbe. Cserélje le `<SubscriptionB-Id>` az azonosítót az előfizetés-azonosítóra, és módosítsa az összest a B és az összes BS értékre a értékre. A módosítások elvégzése után másolja a módosított parancsfájlt, illessze be a CLI-munkamenetbe, majd nyomja meg a gombot `Enter` .
10. Tekintse meg a myVnetB egyenrangú állapotát. Másolja a 7. lépésben szereplő parancsfájl tartalmát a számítógép egy szövegszerkesztőbe. Módosítsa az A – B nevet az erőforráscsoport és a virtuális hálózati nevek számára, másolja a parancsfájlt, illessze be a módosított parancsfájlt a CLI-munkamenetbe, majd nyomja meg a gombot `Enter` . A társítási állapot **csatlakoztatva** van. A myVnetA társítási állapota akkor változik, ha a myVnetB-ről a myVnetA-re való társítást követően létrehozta a **kapcsolatot** . Visszaállíthatja a felhasználót az Azure-ba, és újra elvégezheti a 7. lépést a myVnetA társ-állapotának ellenőrzéséhez.

    > [!NOTE]
    > A rendszer nem hozza meg a társítást, amíg a társítási állapot nem **csatlakozik** mindkét virtuális hálózathoz.

11. Nem **kötelező**: bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
12. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-cli) című részében ismertetett lépéseket.

A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatokban lévő erőforrások nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering létrehozása – PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik engedéllyel, használhatja ugyanazt a fiókot az összes lépéshez, ugorja át az Azure-ba való kijelentkezés lépéseit, és távolítsa el a felhasználói szerepkör-hozzárendeléseket létrehozó parancsfájl sorait. Cserélje le az UserA@azure.com és UserB@azure.com az összes következő parancsfájlt a felhasználó és a felhasználób használt felhasználónevek közül.

1. Győződjön meg róla, hogy Azure PowerShell a 1.0.0 vagy újabb verzió van telepítve. Ehhez futtassa a következőt: `Get-Module -Name Az` telepítse a PowerShell legújabb verzióját. [](/powershell/azure/install-az-ps) Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítsa el a PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be a felhasználóként az Azure-ba a parancs beírásával `Connect-AzAccount` . A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#permissions)társítási engedélyek.
4. Hozzon létre egy erőforráscsoportot és egy virtuális hálózatot. másolja a következő parancsfájlt a számítógép egy szövegszerkesztőbe. A helyére írja be `<SubscriptionA-Id>` az előfizetés azonosítóját. Ha nem ismeri az előfizetés-azonosítóját, írja be a `Get-AzSubscription` parancsot a megtekintéséhez. A visszaadott kimenetben lévő **azonosító** értéke az előfizetés azonosítója. A szkript végrehajtásához másolja a módosított parancsfájlt, illessze be a PowerShellbe, majd nyomja le a gombot `Enter` .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Jelentkezzen ki a felhasználótól az Azure-ból, és jelentkezzen be a Felhasználób. A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#permissions)társítási engedélyek.
6. Másolja a 4. lépésben szereplő parancsfájl tartalmát a számítógép egy szövegszerkesztőbe. Cserélje le `<SubscriptionA-Id>` az azonosítót a B előfizetés azonosítójával. módosítsa a 10.0.0.0/16 10.1.0.0/16 értékre. Módosítsa az összeset a B és az összes BS értékre A következőre:. A szkript végrehajtásához másolja a módosított parancsfájlt, illessze be a PowerShellbe, majd nyomja meg a gombot `Enter` .
7. Jelentkezzen ki a Felhasználób az Azure-ból, és jelentkezzen be a felhasználóval.
8. Hozza létre a társítást a myVnetA-ből a myVnetB-be. Másolja a következő szkriptet egy szövegszerkesztőbe a SZÁMÍTÓGÉPén. A helyére írja be `<SubscriptionB-Id>` a B előfizetés azonosítóját. A szkript végrehajtásához másolja a módosított parancsfájlt, illessze be a PowerShellbe, majd nyomja meg a gombot `Enter` .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Tekintse meg a myVnetA egyenrangú állapotát.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Az állapot **kezdeményezve**. Ha a társítást a myVnetB-ből a myVnetA-be állítja be, a rendszer a **csatlakoztatott** állapotra vált.

10. Jelentkezzen ki a felhasználótól az Azure-ból, és jelentkezzen be a Felhasználób.
11. Hozza létre a társítást a myVnetB-ből a myVnetA-be. Másolja a 8. lépésben szereplő parancsfájl tartalmát a számítógép egy szövegszerkesztőbe. A helyére írja be `<SubscriptionB-Id>` az a-előfizetés azonosítóját, és módosítsa az összest a B és az összes BS értékre. A szkript végrehajtásához másolja a módosított parancsfájlt, illessze be a PowerShellbe, majd nyomja le a gombot `Enter` .
12. Tekintse meg a myVnetB egyenrangú állapotát. Másolja a 9. lépésben szereplő parancsfájl tartalmát a számítógép egy szövegszerkesztőbe. Módosítsa az A – B értékre az erőforráscsoportot és a virtuális hálózatok nevét. A szkript végrehajtásához illessze be a módosított parancsfájlt a PowerShellbe, majd nyomja meg a gombot `Enter` . Az állapot **csatlakoztatva** van. A **myVnetA** társítási állapota akkor változik, ha a **myVnetB** -ről a **myVnetA**-re való társítást követően létrehozta a **kapcsolatot** . Visszaállíthatja a felhasználót az Azure-ba, és elvégezheti a 9. lépést újra a myVnetA társítási állapotának ellenőrzéséhez.

    > [!NOTE]
    > A rendszer nem hozza meg a társítást, amíg a társítási állapot nem **csatlakozik** mindkét virtuális hálózathoz.

    A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatokban lévő erőforrások nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. Nem **kötelező**: bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
14. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-powershell) című részében ismertetett lépéseket.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Egyenrangú – Resource Manager-sablon létrehozása

1. Virtuális hálózat létrehozásához és a megfelelő [engedélyek](virtual-network-manage-peering.md#permissions)kiosztásához hajtsa végre a jelen cikk [portálon](#portal), az [Azure CLI](#cli)-ben vagy a [PowerShellben](#powershell) ismertetett lépéseket.
2. Mentse a következő szöveget a helyi számítógép egyik fájljába. Cserélje le `<subscription ID>` a felhasználót az előfizetés-azonosítóra. Előfordulhat, hogy a fájlt vnetpeeringA.jsként menti, például:.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Jelentkezzen be az Azure-ba felhasználóként, és telepítse a sablont a [portál](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), a [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)vagy az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file)használatával. Adja meg azt a fájlnevet, amelyet a 2. lépésben a JSON-szöveg mentett.
4. Másolja a példában szereplő JSON-t a 2. lépésben a számítógép egyik fájljába, és hajtsa végre a következővel kezdődő sorokat:
   - **név**: módosítsa a *myVnetA/myVnetAToMyVnetB* a *myVnetB/myVnetBToMyVnetA* értékre.
   - **azonosító**: cserélje le `<subscription ID>` a FELHASZNÁLÓB előfizetési azonosítóját, és módosítsa a *myVnetB* a *myVnetA* értékre.
5. Fejezze be a 3. lépést újra, jelentkezzen be az Azure-ba Felhasználób néven.
6. Nem **kötelező**: bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
7. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete) című szakaszának lépéseit a Azure Portal, a POWERSHELL vagy az Azure CLI használatával.

## <a name="delete-resources"></a><a name="delete"></a>Erőforrások törlése
Az oktatóanyag befejezése után érdemes lehet törölni az oktatóanyagban létrehozott erőforrásokat, így nem számítunk fel használati díjakat. Egy erőforráscsoport törlésekor az erőforráscsoport összes erőforrása is törlődik.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Jelentkezzen be a Azure Portal felhasználóként.
2. A portál keresési mezőjébe írja be a **myResourceGroupA** értéket. A keresési eredmények között válassza a **myResourceGroupA** lehetőséget.
3. Válassza a **Törlés** elemet.
4. A törlés megerősítéséhez írja be az **erőforráscsoport neve** mezőbe az **myResourceGroupA** nevet, majd válassza a **Törlés** lehetőséget.
5. Jelentkezzen ki a portálról felhasználóként, és jelentkezzen be Felhasználób néven.
6. Hajtsa végre a 2-4-es lépést a myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Jelentkezzen be az Azure-ba felhasználóként, és hajtsa végre a következő parancsot:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Jelentkezzen ki az Azure-ból felhasználóként, és jelentkezzen be Felhasználób néven.
3. Futtassa a következő parancsot:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Jelentkezzen be az Azure-ba felhasználóként, és hajtsa végre a következő parancsot:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Jelentkezzen ki az Azure-ból felhasználóként, és jelentkezzen be Felhasználób néven.
3. Futtassa a következő parancsot:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Következő lépések

- Alaposan megismerheti a fontos [virtuális hálózati társítási korlátozásokat és a viselkedést](virtual-network-manage-peering.md#requirements-and-constraints) , mielőtt létrehozza a virtuális hálózatokat az éles környezetben való használatra.
- További információ az összes [virtuális hálózati társ](virtual-network-manage-peering.md#create-a-peering)-összevonási beállításról.
- Megtudhatja, hogyan [hozhat létre egy sugaras hálózati topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) a virtuális hálózati társítással.