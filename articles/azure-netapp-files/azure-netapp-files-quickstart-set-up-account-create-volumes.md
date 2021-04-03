---
title: 'Gyors útmutató: Azure NetApp Files és NFS-kötet beállítása'
description: Rövid útmutató – útmutatást nyújt a Azure NetApp Files gyors beállításához és kötet létrehozásához.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: e31a1cef427062723adf4b45bd47cd8009630128
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888810"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Gyors útmutató: Azure NetApp Files beállítása és NFS-kötet létrehozása

Ez a cikk bemutatja, hogyan állíthatja be gyorsan a Azure NetApp Filest, és hogyan hozhat létre kötetet.

Ebben a rövid útmutatóban a következő elemeket fogja beállítani:

- Regisztráció a Azure NetApp Files és a NetApp erőforrás-szolgáltatóhoz
- Egy NetApp-fiók
- Egy kapacitás-készlet
- Egy NFS-kötet a Azure NetApp Files

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Előkészületek

> [!IMPORTANT]
> Hozzáférést kell biztosítania a Azure NetApp Files szolgáltatáshoz. Ha hozzáférést szeretne kérni a szolgáltatáshoz, tekintse meg a [Azure NetApp Files várólista-beküldési lapot](https://aka.ms/azurenetappfiles).  A folytatás előtt meg kell várnia a hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól.

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Regisztráljon Azure NetApp Files és a NetApp erőforrás-szolgáltatóra

> [!NOTE]
> A regisztrációs folyamat hosszabb időt is igénybe vehet.
>

# <a name="portal"></a>[Portál](#tab/azure-portal)

A portál használatával történő regisztráció lépéseihez nyisson meg egy Cloud Shell-munkamenetet a fent jelzett módon, és kövesse az alábbi Azure CLI-lépéseket:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ennek a cikknek a használatához a Azure PowerShell modul az a Version 2.6.0 vagy újabb verziója szükséges. Az aktuális verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha szeretné, használhatja a Cloud Shell-konzolt egy PowerShell-munkamenetben.

1. A PowerShell-parancssorban (vagy a PowerShell Cloud Shell munkamenetben) válassza ki a Azure NetApp Files számára jóváhagyott előfizetést:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Regisztrálja az Azure erőforrás-szolgáltatót:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Sablon](#tab/template)

Nincsenek.

A Azure Portal, a PowerShell vagy az Azure CLI használatával regisztrálhat a Azure NetApp Files és a NetApp erőforrás-szolgáltatóra.

További információért lásd: [Azure NetApp Files regisztrálása](azure-netapp-files-register.md) .

---

## <a name="create-a-netapp-account"></a>NetApp-fiók létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal keresési mezőjébe írja be a **Azure NetApp Files** , majd a megjelenő listából válassza a **Azure NetApp Files** lehetőséget.

      ![Azure NetApp Files kiválasztása](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Új NetApp-fiók létrehozásához kattintson a **+ Hozzáadás** gombra.

     ![Új NetApp-fiók létrehozása](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Az új NetApp-fiók ablakban adja meg a következő információkat:
   1. Adja meg a **myaccount1** nevét.
   2. Válassza ki előfizetését.
   3. Válassza az **új létrehozása** lehetőséget az új erőforráscsoport létrehozásához. Adja meg az erőforráscsoport-név **myRG1** . Kattintson az **OK** gombra.
   4. Válassza ki a fiókja helyét.

      ![Új NetApp-fiók ablak](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![Erőforráscsoport ablak](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Az új NetApp-fiók létrehozásához kattintson a **Létrehozás** gombra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definiáljon néhány változót, hogy a többi példa során is hivatkozhatunk rájuk:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > A támogatott régiók listájának megtekintéséhez tekintse meg a [régiók számára elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) .
    > A parancssori eszközök által támogatott régió nevének beszerzéséhez használja a következőt: `Get-AzLocation | select Location`
    >

1. Hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával:

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Hozzon létre Azure NetApp Files fiókot a [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) paranccsal:

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiáljon néhány változót, hogy a többi példa során is hivatkozhatunk rájuk:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > A támogatott régiók listájának megtekintéséhez tekintse meg a [régiók számára elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) .
    > A parancssori eszközök által támogatott régió nevének beszerzéséhez használja a következőt: `az account list-locations --query "[].{Region:name}" --out table`
    >

2. Hozzon létre egy új erőforráscsoportot az az [Group Create](/cli/azure/group#az-group-create) paranccsal:

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Azure NetApp Files fiók létrehozása az [az netappfiles Account Create](/cli/azure/netappfiles/account#az-netappfiles-account-create) paranccsal:

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Sablon](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A következő kódrészlet azt mutatja be, hogyan hozható létre NetApp-fiók egy Azure Resource Manager sablonban (ARM-sablon) a [Microsoft. NetApp/netAppAccounts](/azure/templates/microsoft.netapp/netappaccounts) erőforrás használatával. A kód futtatásához töltse le a [teljes ARM-sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) a GitHub-adattárból.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure NetApp Files-kezelés panelen válassza ki a NetApp-fiókját (**myaccount1**).

    ![NetApp-fiók kiválasztása](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. A NetApp-fiók Azure NetApp Files felügyelet paneljén kattintson a **Kapacitási készletek** elemre.

    ![Kattintson a kapacitási készletek elemre.](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. Kattintson a **+ készletek hozzáadása** lehetőségre.

    ![Kattintson a készletek hozzáadása gombra.](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Adja meg a kapacitási készlet adatait:
    * Adja meg a **mypool1** a készlet neveként.
    * Válassza a **prémium** lehetőséget a szolgáltatási szinthez.
    * A készlet méreteként **4 (TiB)** adható meg.
    * Használja az **automatikus** QoS-típust.

5. Kattintson a **Létrehozás** lehetőségre.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Új változók definiálása jövőbeli referenciához

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Új kapacitási készlet létrehozása a [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool) használatával

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Új változók definiálása jövőbeli referenciához

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Hozzon létre egy új kapacitási készletet az az [netappfiles Pool Create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) paranccsal.

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a>[Sablon](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

A következő kódrészlet azt mutatja be, hogyan hozhat létre kapacitási készletet egy Azure Resource Manager sablonban (ARM-sablon) a [Microsoft. NetApp/netAppAccounts/capacityPools](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) erőforrás használatával. A kód futtatásához töltse le a [teljes ARM-sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) a GitHub-adattárból.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>NFS-kötet létrehozása a Azure NetApp Fileshoz

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A NetApp-fiók Azure NetApp Files felügyelet paneljén kattintson a **kötetek** elemre.

    ![Kattintson a Kötetek elemre](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. Kattintson a **+ Kötet hozzáadása** gombra.

    ![Kattintson a kötetek hozzáadása gombra.](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. A kötet létrehozása ablakban adja meg a kötet adatait:
   1. A kötet neveként adja meg a **myvol1** .
   2. Válassza ki a kapacitási készletet (**mypool1**).
   3. Használja a kvóta alapértelmezett értékét.
   4. A virtuális hálózat területen kattintson az **új létrehozása** lehetőségre egy új Azure-beli virtuális hálózat (vnet) létrehozásához.  Ezután adja meg a következő információkat:
       * Adja meg a **myvnet1** nevet a vnet neveként.
       * Adja meg a beállításhoz tartozó címtartományt, például 10.7.0.0/16
       * Adja meg a **myANFsubnet** nevet az alhálózat neveként.
       * Az alhálózati címtartomány megadása, például 10.7.0.0/24. A dedikált alhálózatot nem lehet más erőforrásokkal megosztani.
       * Válassza a **Microsoft. NetApp/kötetek** lehetőséget az alhálózati delegáláshoz.
       * A vnet létrehozásához kattintson **az OK** gombra.
   5. Az alhálózat területen jelölje ki az újonnan létrehozott vnet (**myvnet1**) delegált alhálózatként.

      ![Kötet ablak létrehozása](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![Virtuális hálózat létrehozása ablak](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. Kattintson a **protokoll** elemre, majd hajtsa végre a következő műveleteket:
    * Válassza az **NFS** lehetőséget a kötethez tartozó protokoll típusaként.
    * Adja meg a **myfilepath1** a kötet exportálási elérési útjának létrehozásához használt fájl elérési útjaként.
    * Válassza ki a kötet NFS-verzióját (**NFSv3** vagy **nfsv 4.1**).
      Lásd az NFS-verziók [szempontjait](azure-netapp-files-create-volumes.md#considerations) és [ajánlott eljárásait](azure-netapp-files-create-volumes.md#best-practice) .

    ![Az NFS protokoll megadása a gyors üzembe helyezéshez](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Kattintson a **Felülvizsgálat + létrehozás** elemre.

    ![Áttekintés és létrehozás ablak](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)

6. Tekintse át a kötetre vonatkozó információkat, majd kattintson a **Létrehozás** gombra.
    A létrehozott kötet megjelenik a kötetek panelen.

    ![Kötet létrehozva](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy alhálózati delegálást a "Microsoft. NetApp/Volumes" kifejezésre a [New-AzDelegation](/powershell/module/az.network/new-azdelegation) paranccsal.

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Hozzon létre egy alhálózat-konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) parancs használatával.

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Hozza létre a virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) parancs használatával.

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Hozza létre a kötetet a [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) parancs használatával.

    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Néhány változó meghatározása a későbbi használathoz.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Hozzon létre egy alhálózat nélküli virtuális hálózatot az az [Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create) parancs használatával.

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Hozzon létre egy delegált alhálózatot az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) parancs használatával.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Hozza létre a kötetet az az [netappfiles Volume Create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) parancs használatával.

    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

# <a name="template"></a>[Sablon](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

A következő kódrészletek bemutatják, hogyan állíthat be egy VNet, és hogyan hozhat létre Azure NetApp Files kötetet egy Azure Resource Manager sablonban (ARM-sablon). A VNet telepítője a [Microsoft. Network/virtualNetworks](/azure/templates/Microsoft.Network/virtualNetworks) erőforrást használja. A kötet létrehozása a [Microsoft. NetApp/netAppAccounts/capacityPools/Volumes](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) erőforrást használja. A kód futtatásához töltse le a [teljes ARM-sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) a GitHub-adattárból.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha elkészült, és ha szeretné, törölheti az erőforráscsoportot. Egy erőforráscsoport törlésének művelete visszafordíthatatlan.

> [!IMPORTANT]
> Az erőforráscsoportok összes erőforrása véglegesen törölve lesz, és nem vonható vissza.

1. A Azure Portal keresési mezőjébe írja be a **Azure NetApp Files** , majd a megjelenő listából válassza a **Azure NetApp Files** lehetőséget.

2. Az előfizetések listájában kattintson a törölni kívánt erőforráscsoport (myRG1) elemre.

    ![Az erőforráscsoportok navigálása](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Az erőforráscsoport lapon kattintson az **erőforráscsoport törlése** elemre.

    ![Képernyőkép, amely kiemeli az erőforráscsoport törlése gombot.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    Ekkor megnyílik egy ablak, mely figyelmezteti az erőforráscsoporttal törölt erőforrásokról.

4. Adja meg az erőforráscsoport nevét (myRG1) annak megerősítéséhez, hogy véglegesen törölni kívánja az erőforráscsoportot és az összes erőforrást, majd kattintson a **Törlés** gombra.

    ![Erőforráscsoport törlésének megerősítése](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha elkészült, és ha szeretné, törölheti az erőforráscsoportot. Egy erőforráscsoport törlésének művelete visszafordíthatatlan.

> [!IMPORTANT]
> Az erőforráscsoportok összes erőforrása véglegesen törölve lesz, és nem vonható vissza.

1. Törölje az erőforráscsoportot a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancs használatával.

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha elkészült, és ha szeretné, törölheti az erőforráscsoportot. Egy erőforráscsoport törlésének művelete visszafordíthatatlan.

> [!IMPORTANT]
> Az erőforráscsoportok összes erőforrása véglegesen törölve lesz, és nem vonható vissza.

1. Törölje az erőforráscsoportot az az [Group delete](/cli/azure/group#az-group-delete) paranccsal.

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Sablon](#tab/template)

Nincsenek.

Az erőforráscsoport törléséhez használja a Azure Portal, a PowerShellt vagy az Azure CLI-t.

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure NetApp Files tárhely-hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [NFS-kötet létrehozása](azure-netapp-files-create-volumes.md)
