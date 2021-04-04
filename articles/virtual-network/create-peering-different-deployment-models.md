---
title: Azure-beli virtuális hálózati társítás létrehozása – különböző üzemi modellek – azonos előfizetés | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre virtuális hálózatokat a különböző Azure-alapú üzemi modelleken keresztül létrehozott virtuális hálózatok között, amelyek ugyanabban az Azure-előfizetésben találhatók.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 1ff9fcbb693f7e606c07985f9bce9acd60c5591a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222972"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Virtuális hálózati társítás létrehozása – különböző üzemi modellek, azonos előfizetés

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre virtuális hálózatot a különböző üzembe helyezési modelleken keresztül létrehozott virtuális hálózatok között. Mindkét virtuális hálózat ugyanabban az előfizetésben található. A két virtuális hálózat összekapcsolása lehetővé teszi, hogy a különböző virtuális hálózatok erőforrásai azonos sávszélességgel és késéssel kommunikáljanak egymással, mintha az erőforrások ugyanabban a virtuális hálózaton lennének. További információ a [virtuális hálózatok](virtual-network-peering-overview.md)összevonásáról.

A virtuális hálózati társítások létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok azonos vagy eltérő előfizetésekben találhatók-e, valamint hogy a virtuális hálózatok milyen Azure-alapú üzemi [modellt](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hoznak létre. A következő táblázat forgatókönyvére kattintva megtudhatja, hogyan hozhat létre egy virtuális hálózati társat más forgatókönyvekben:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ugyanaz|
|[Mindkét Resource Manager](create-peering-different-subscriptions.md) |Különböző|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models-subscriptions.md) |Különböző|

Nem hozható létre virtuális hálózati társítás a klasszikus üzemi modellen keresztül üzembe helyezett két virtuális hálózat között. Ha a klasszikus üzemi modellel létrehozott virtuális hálózatokat is össze kell kapcsolni, használhat Azure- [VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a virtuális hálózatok összekapcsolásához.

Ez az oktatóanyag az azonos régióban található virtuális hálózatokat ismerteti. A különböző [támogatott régiókban](virtual-network-manage-peering.md#cross-region)lévő egyenrangú virtuális hálózatokat is használhatja. Javasoljuk, hogy ismerkedjen meg a társítási [követelményekkel és a megkötésekkel](virtual-network-manage-peering.md#requirements-and-constraints) a virtuális hálózatok társítása előtt.

A virtuális hálózati társítás létrehozásához használhatja a Azure Portal, az Azure [parancssori felületét](#cli) (CLI), az Azure [PowerShellt](#powershell)vagy egy Azure Resource Manager sablont. Kattintson bármelyik előző eszköz hivatkozásaira, hogy közvetlenül a virtuális hálózati társítások létrehozásához szükséges lépéseket válassza a választott eszköz használatával.

## <a name="create-peering---azure-portal"></a>Peering-Azure Portal létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#requirements-and-constraints)társítási engedélyek.
2. Kattintson az **+ új**, majd a **hálózatkezelés**, majd a **virtuális hálózat** elemre.
3. A **virtuális hálózat létrehozása** panelen adja meg vagy válassza ki a következő beállítások értékeit, majd kattintson a **Létrehozás** gombra:
    - **Név**: *myVnet1*
    - **Címterület**: *10.0.0.0/16*
    - **Alhálózat neve**: *default*
    - **Alhálózati címtartomány**: *10.0.0.0/24*
    - **Előfizetés**: válassza ki az előfizetését
    - **Erőforráscsoport**: válassza az **új létrehozása** elemet, és adja meg a *myResourceGroup*
    - Hely: *USA keleti* **régiója**
4. Kattintson az **+ Új** elemre. A **Keresés a piactéren** mezőbe írja be a *virtuális hálózat* kifejezést. Kattintson a **virtuális hálózat** elemre, amikor megjelenik a keresési eredmények között.
5. A **virtuális hálózat** panelen válassza a **klasszikus** lehetőséget a **telepítési modell kiválasztása** mezőben, majd kattintson a **Létrehozás** gombra.
6. A **virtuális hálózat létrehozása** panelen adja meg vagy válassza ki a következő beállítások értékeit, majd kattintson a **Létrehozás** gombra:
    - **Név**: *myVnet2*
    - **Címterület**: *10.1.0.0/16*
    - **Alhálózat neve**: *default*
    - **Alhálózati címtartomány**: *10.1.0.0/24*
    - **Előfizetés**: válassza ki az előfizetését
    - **Erőforráscsoport**: válassza a **meglévő használata** lehetőséget, és válassza a *myResourceGroup* lehetőséget.
    - Hely: *USA keleti* **régiója**
7. A portál tetején található **erőforrások keresése** mezőben írja be a következőt: *myResourceGroup*. Kattintson a **myResourceGroup** elemre, amikor megjelenik a keresési eredmények között. Megjelenik egy panel a **myresourcegroup** erőforráscsoporthoz. Az erőforráscsoport az előző lépések során létrehozott két virtuális hálózatot tárolja.
8. Kattintson a **myVNet1** elemre.
9. A megjelenő **myVnet1** **panelen kattintson a** panel bal oldalán lévő beállítások függőleges listájában található társítások elemre.
10. A megjelenő **myVnet1-társak** panelen kattintson a **+ Hozzáadás** gombra.
11. A megjelenő **egyenrangú panel hozzáadása** panelen írja be vagy válassza ki a következő beállításokat, majd kattintson az **OK** gombra:
     - **Név**: *myVnet1ToMyVnet2*
     - **Virtuális hálózat telepítési modellje**: válassza a **klasszikus** lehetőséget.
     - **Előfizetés**: válassza ki az előfizetését
     - **Virtuális hálózat**: kattintson **a virtuális hálózat kiválasztása**, majd a **myVnet2** elemre.
     - **Virtuális hálózati hozzáférés engedélyezése:** Győződjön meg arról, hogy az **engedélyezve** beállítás be van jelölve.
    Ebben az oktatóanyagban más beállítások nem használhatók. Az összes egyenrangú beállítás megismeréséhez olvassa el a [virtuális hálózati](virtual-network-manage-peering.md#create-a-peering)társítások kezelése című témakört.
12. Miután az **OK gombra** kattintott az előző lépésben, a társítás **hozzáadása** panel bezárul, és újra megjelenik a **myVnet1** . Néhány másodperc elteltével a létrehozott társítás megjelenik a panelen. A **kapcsolat** a létrehozott **MyVnet1ToMyVnet2** -társítás **egyenrangú állapot** oszlopában szerepel.

    A társítás már létrejött. A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. Nem **kötelező**: bár a virtuális gépek létrehozása nem vonatkozik ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
14. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-portal) című szakaszának lépéseit.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering létrehozása – Azure CLI

Hajtsa végre az alábbi lépéseket a klasszikus Azure CLI-vel és az Azure CLI-vel. A Azure Cloud Shell lépéseinek elvégzéséhez egyszerűen válassza **ki a kipróbálás** gombot az alábbi lépések bármelyikén, vagy telepítse a [klasszikus CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) -t és a [parancssori](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) felületet, és futtassa a parancsokat a helyi számítógépen.

1. Ha a Cloud Shell használja, ugorjon a 2. lépésre, mert a Cloud Shell automatikusan bejelentkezik az Azure-ba. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-ba a `azure login` parancs használatával.
2. Futtassa a CLI-t a Service Management módban a parancs beírásával `azure config mode asm` .
3. A virtuális hálózat (klasszikus) létrehozásához adja meg a következő parancsot:

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Hajtsa végre a következő bash CLI-szkriptet a CLI használatával, ne a klasszikus CLI-vel. A Windows rendszerű számítógépen futó bash CLI-parancsfájlok futtatásával kapcsolatos lehetőségekért lásd: [Az Azure CLI telepítése Windows](/cli/azure/install-azure-cli-windows)rendszeren.

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Hozzon létre egy virtuális hálózatot a különböző üzemi modelleken keresztül létrehozott két virtuális hálózat között a parancssori felület használatával. Másolja a következő szkriptet egy szövegszerkesztőbe a SZÁMÍTÓGÉPén. Cserélje le `<subscription id>` az értékét az előfizetés-azonosítójával. Ha nem ismeri az előfizetés-AZONOSÍTÓját, írja be a `az account show` parancsot. A kimenetben lévő **azonosító** értéke az előfizetés azonosítója. Illessze be a módosított parancsfájlt a CLI-munkamenetbe, majd nyomja le a gombot `Enter` .

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. A szkript végrehajtása után tekintse át a virtuális hálózat (Resource Manager) társítását. Másolja be az alábbi parancsot, illessze be a CLI-munkamenetbe, majd nyomja le a következőt `Enter` :

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   A kimenet a **PeeringState** oszlopban **csatlakozik** .

   A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. Nem **kötelező**: bár a virtuális gépek létrehozása nem vonatkozik ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
8. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-cli) című részében ismertetett lépéseket.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering létrehozása – PowerShell

1. Telepítse a PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) és [az modulok legújabb](https://www.powershellgallery.com/packages/Az/) verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítsa el a PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be az Azure-ba a parancs beírásával `Add-AzureAccount` . A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#requirements-and-constraints)társítási engedélyek.
4. Virtuális hálózat (klasszikus) PowerShell-lel történő létrehozásához létre kell hoznia egy újat, vagy módosítania kell egy meglévő hálózati konfigurációs fájlt. Útmutató [hálózati konfigurációs fájlok exportálásához, frissítéséhez és importálásához](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). A fájlnak tartalmaznia kell a következő **VirtualNetworkSite** elemet az oktatóanyagban használt virtuális hálózathoz:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
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
5. Jelentkezzen be az Azure-ba a virtuális hálózat (Resource Manager) létrehozásához a parancs beírásával `Connect-AzAccount` . A bejelentkezett fióknak rendelkeznie kell a virtuális hálózati társítás létrehozásához szükséges engedélyekkel. Az engedélyek listáját lásd: [virtuális hálózati](virtual-network-manage-peering.md#requirements-and-constraints)társítási engedélyek.
6. Hozzon létre egy erőforráscsoportot és egy virtuális hálózatot (Resource Manager). Másolja a szkriptet, illessze be a PowerShellbe, majd nyomja meg a gombot `Enter` .

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Hozzon létre egy virtuális hálózatot a különböző üzembe helyezési modelleken keresztül létrehozott két virtuális hálózat között. Másolja a következő szkriptet egy szövegszerkesztőbe a SZÁMÍTÓGÉPén. Cserélje le `<subscription id>` az értékét az előfizetés-azonosítójával. Ha nem ismeri az előfizetés-AZONOSÍTÓját, írja be a `Get-AzSubscription` parancsot a megtekintéséhez. A visszaadott kimenetben lévő **azonosító** értéke az előfizetés azonosítója. A szkript végrehajtásához másolja a módosított parancsfájlt a szövegszerkesztőből, majd kattintson a jobb gombbal a PowerShell-munkamenetre, majd nyomja le a gombot `Enter` .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. A szkript végrehajtása után tekintse át a virtuális hálózat (Resource Manager) társítását. Másolja be az alábbi parancsot, illessze be a PowerShell-munkamenetbe, majd nyomja le a következőt `Enter` :

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    A kimenet a **PeeringState** oszlopban **csatlakozik** .

    A virtuális hálózatban létrehozott Azure-erőforrások mostantól képesek kommunikálni egymással az IP-címükkel. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldását használja, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatok között. Ha egy társon belül szeretné feloldani a neveket a virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgálóját. Megtudhatja, hogyan állíthatja be a névfeloldást [a saját DNS-kiszolgálójának használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. Nem **kötelező**: bár a virtuális gépek létrehozása nem vonatkozik ebben az oktatóanyagban, létrehozhat egy virtuális gépet az egyes virtuális hálózatokban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat ellenőrzéséhez.
10. Nem **kötelező**: az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a jelen cikk [erőforrások törlése](#delete-powershell) című részében ismertetett lépéseket.

## <a name="delete-resources"></a><a name="delete"></a>Erőforrások törlése

Az oktatóanyag befejezése után érdemes lehet törölni az oktatóanyagban létrehozott erőforrásokat, így nem számítunk fel használati díjakat. Egy erőforráscsoport törlésekor az erőforráscsoport összes erőforrása is törlődik.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. A portál keresési mezőjébe írja be a **myResourceGroup** értéket. A keresési eredmények között kattintson a **myResourceGroup** elemre.
2. A **myResourceGroup** panelen kattintson a **Törlés** ikonra.
3. A törlés megerősítéséhez írja be az **erőforráscsoport neve** mezőbe az **myResourceGroup** nevet, majd kattintson a **Törlés** gombra.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Az Azure CLI használatával törölje a virtuális hálózatot (Resource Manager) a következő paranccsal:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. A klasszikus CLI használatával törölje a virtuális hálózatot (klasszikus) a következő parancsokkal:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. A virtuális hálózat (Resource Manager) törléséhez írja be a következő parancsot:

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. A virtuális hálózat (klasszikus) PowerShell-lel való törléséhez módosítania kell egy meglévő hálózati konfigurációs fájlt. Útmutató [hálózati konfigurációs fájlok exportálásához, frissítéséhez és importálásához](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Távolítsa el a következő VirtualNetworkSite elemet az oktatóanyagban használt virtuális hálózathoz:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
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