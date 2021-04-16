---
title: 'Rövid útmutató: A Azure NetApp Files NFS-kötet beállítása'
description: Rövid útmutató – Leírja, hogyan állíthat be gyorsan Azure NetApp Files és hozhat létre kötetet.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: 0b48963fa6cb28c836c57de8b46861ef83752231
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388558"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Rövid útmutató: A Azure NetApp Files beállítása és NFS-kötet létrehozása

Ez a cikk bemutatja, hogyan állíthat be gyorsan egy Azure NetApp Files és hozhat létre NFS-kötetet. 

Ebben a rövid útmutatóban a következő elemeket fogja beállítani:

- Regisztráció Azure NetApp Files NetApp erőforrás-szolgáltatóra
- NetApp-fiók
- Egy kapacitáskészlet
- NfS-kötet a Azure NetApp Files

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Az NFS-kötetek összes engedélyezhető funkcióját és a kapcsolódó szempontokat lásd: [NFS-kötet létrehozása.](azure-netapp-files-create-volumes.md) 

## <a name="before-you-begin"></a>Előkészületek

> [!IMPORTANT]
> Hozzáférést kell biztosítani a Azure NetApp Files szolgáltatáshoz. A szolgáltatáshoz való hozzáférés igénylését lásd a Azure NetApp Files [a várakozási lista beküldési oldalán.](https://aka.ms/azurenetappfiles)  A folytatás előtt meg kell várnia a csapattól Azure NetApp Files visszaigazoló e-mailt.

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Regisztrálás Azure NetApp Files NetApp erőforrás-szolgáltatóra

> [!NOTE]
> A regisztrációs folyamat befejezése némi időt vehet igénybe.
>

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Portallal való regisztráció lépéseiért nyisson meg egy Cloud Shell-munkamenetet a fent láthatóak szerint, és kövesse az Alábbi Azure CLI-lépéseket:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ehhez az ehhez a cikkhez a Azure PowerShell Az 2.6.0-s vagy újabb verziójára lesz szükség. Az aktuális verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha szeretné, ehelyett használhatja a Cloud Shell egy PowerShell-munkamenetben.

1. Egy PowerShell-parancssorban (vagy a PowerShell Cloud Shell-munkamenetben) adja meg azt az előfizetést, amely számára jóvá lett hagyva a Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Regisztrálja az Azure erőforrás-szolgáltatót:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A környezet előkészítése az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Sablon](#tab/template)

Nincsenek.

A Azure Portal, a PowerShell vagy az Azure CLI használatával regisztráljon a Azure NetApp Files NetApp erőforrás-szolgáltatóra.

További [információ: Azure NetApp Files](azure-netapp-files-register.md) regisztrálása a regisztrációhoz.

---

## <a name="create-a-netapp-account"></a>NetApp-fiók létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal keresőmezőbe írja be a következőt: **Azure NetApp Files,** majd Azure NetApp Files a megjelenő listából. 

      ![Válassza a Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Új NetApp-fiók létrehozásához kattintson a **+ Hozzáadás** gombra.

     ![Új NetApp-fiók létrehozása](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Az Új NetApp-fiók ablakban adja meg a következő adatokat:
   1. A fiók neveként adja meg a **myaccount1** nevet.
   2. Válassza ki előfizetését.
   3. Új **erőforráscsoport létrehozásához** válassza az Új létrehozása lehetőséget. Az **erőforráscsoport neveként** adja meg a myRG1 nevet. Kattintson az **OK** gombra.
   4. Válassza ki a fiók helyét.

      ![Új NetApp-fiók ablak](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![Erőforráscsoport ablak](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Kattintson **a Létrehozás** gombra az új NetApp-fiók létrehozásához.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Határozzon meg néhány változót, hogy a többi példában is hivatkozni tudjuk őket:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > A támogatott [régiók listájáért tekintse meg](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) az elérhető termékek régiónkénti listáját.
    > A parancssori eszközök által támogatott régiónév beszerzéséhez használja a következőt: `Get-AzLocation | select Location`
    >

1. Hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup paranccsal:](/powershell/module/az.resources/new-azresourcegroup)

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Hozzon Azure NetApp Files a [New-AzNetAppFilesAccount paranccsal:](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Határozzon meg néhány változót, hogy a többi példában is hivatkozni tudjuk őket:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > A támogatott [régiók listájáért tekintse meg](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) az elérhető termékek régiónkénti listáját.
    > A parancssori eszközök által támogatott régiónév beszerzéséhez használja a következőt: `az account list-locations --query "[].{Region:name}" --out table`
    >

2. Hozzon létre egy új erőforráscsoportot [az az group create paranccsal:](/cli/azure/group#az-group-create)

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Hozzon Azure NetApp Files fiókot [az az netappfiles account create paranccsal:](/cli/azure/netappfiles/account#az-netappfiles-account-create)

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Sablon](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Az alábbi kódrészlet bemutatja, hogyan hozhat létre NetApp-fiókot egy Azure Resource Manager-sablonban (ARM-sablonban) a [Microsoft.NetApp/netAppAccounts erőforrás](/azure/templates/microsoft.netapp/netappaccounts) használatával. A kód futtatásához töltse le [a teljes ARM-sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) a GitHub-adattárból.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure NetApp Files panelen válassza ki a NetApp-fiókját **(myaccount1).**

    ![Válassza a NetApp-fiók lehetőséget](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. A NetApp Azure NetApp Files felügyeleti panelen kattintson a **Kapacitáskészletek elemre.**

    ![Kattintson a Kapacitáskészletek elemre](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. Kattintson **a + Készletek hozzáadása elemre.**

    ![Kattintson a Készletek hozzáadása elemre.](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Adja meg a kapacitáskészlet adatait:
    * A készlet neveként adja meg a **mypool1** nevet.
    * A **szolgáltatásszinthez** válassza a Prémium lehetőséget.
    * A készlet méreteként adja meg a **4 (TiB)** értéket.
    * Használja az **Automatikus** QoS típust.

5. Kattintson a **Létrehozás** lehetőségre.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Új változók definiálása későbbi referenciaként

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Új kapacitáskészlet létrehozása a [New-AzNetAppFilesPool használatával](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Új változók definiálása későbbi referenciaként

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Új kapacitáskészlet létrehozása az [az netappfiles pool create használatával](/cli/azure/netappfiles/pool#az-netappfiles-pool-create)

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

Az alábbi kódrészlet bemutatja, hogyan hozhat létre kapacitáskészletet egy Azure Resource Manager-sablonban (ARM-sablonban) a [Microsoft.NetApp/netAppAccounts/capacityPools erőforrás](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) használatával. A kód futtatásához töltse le a [teljes ARM-sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) a GitHub-adattárból.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>NFS-kötet létrehozása az Azure NetApp Files számára

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A NetApp Azure NetApp Files felügyeleti panelen kattintson a **Kötetek elemre.**

    ![Kattintson a Kötetek elemre](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. Kattintson a **+ Kötet hozzáadása** gombra.

    ![Kattintson a Kötetek hozzáadása elemre.](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. A Kötet létrehozása ablakban adja meg a kötet adatait:
   1. A kötet neveként adja meg a **myvol1** nevet.
   2. Válassza ki a kapacitáskészletet **(mypool1).**
   3. A kvóta beállításhoz használja az alapértelmezett értéket.
   4. A virtuális hálózat alatt kattintson az **Új létrehozása** elemre egy új Azure-beli virtuális hálózat (Vnet) létrehozásához.  Ezután adja meg a következő adatokat:
       * A virtuális hálózat neveként adja meg a **myvnet1** nevet.
       * Adjon meg egy címterületet a beállításhoz, például: 10.7.0.0/16
       * Adja **meg a myANFsubnet** nevet az alhálózat neveként.
       * Adja meg az alhálózat címtartományát, például: 10.7.0.0/24. A dedikált alhálózatot nem oszthatja meg más erőforrásokkal.
       * Válassza **a Microsoft.NetApp/volumes** lehetőséget az alhálózat delegáláshoz.
       * Kattintson **az OK** gombra a virtuális hálózat létrehozásához.
   5. Az alhálózaton válassza ki az újonnan létrehozott VNetet **(myvnet1**) delegált alhálózatként.

      ![Kötet létrehozása ablak](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![Virtuális hálózat létrehozása ablak](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. Kattintson **a Protokoll** elemre, majd a következő műveleteket kell végrehajtania:
    * A **kötet protokolltípusaként** válassza az NFS lehetőséget.
    * Adja **meg a myfilepath1** elérési utat a kötet exportálási útvonalának létrehozásához használt fájl elérési útjaként.
    * Válassza ki az **NFS-verziót (NFSv3** vagy **NFSv4.1**) a kötethez.
      Tekintse meg [az](azure-netapp-files-create-volumes.md#considerations) [NFS-verziókra](azure-netapp-files-create-volumes.md#best-practice) vonatkozó szempontokat és ajánlott eljárásokat.

    ![NFS-protokoll megadása a rövid útmutatóhoz](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Kattintson a **Felülvizsgálat + létrehozás** elemre.

    ![Áttekintés és létrehozás ablak](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)

6. Tekintse át a kötet adatait, majd kattintson a **Létrehozás gombra.**
    A létrehozott kötet megjelenik a Kötetek panelen.

    ![Létrehozott kötet](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy alhálózatdelegálást a "Microsoft.NetApp/volumes" számára [a New-AzDelegation paranccsal.](/powershell/module/az.network/new-azdelegation)

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Hozzon létre egy alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig paranccsal.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Hozza létre a virtuális hálózatot a [New-AzVirtualNetwork paranccsal.](/powershell/module/az.network/new-azvirtualnetwork)

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Hozza létre a kötetet a [New-AzNetAppFilesVolume paranccsal.](/powershell/module/az.netappfiles/new-aznetappfilesvolume)

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

1. Változók definiálása későbbi használatra.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Hozzon létre virtuális hálózatot alhálózat nélkül az [az network vnet create paranccsal.](/cli/azure/network/vnet#az-network-vnet-create)

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Hozzon létre egy delegált alhálózatot [az az network vnet subnet create paranccsal.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Hozza létre a kötetet [az az netappfiles volume create paranccsal.](/cli/azure/netappfiles/volume#az-netappfiles-volume-create)

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

Az alábbi kódrészletek azt mutatják be, hogyan állíthat be virtuális hálózatokat, és hogyan hozhat létre Azure NetApp Files kötetet egy Azure Resource Manager sablonban (ARM-sablon). A virtuális hálózat beállítása a [Microsoft.Network/virtualNetworks erőforrást](/azure/templates/Microsoft.Network/virtualNetworks) használja. A kötetek létrehozása a [Microsoft.NetApp/netAppAccounts/capacityPools/volumes erőforrást](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) használja. A kód futtatásához töltse le a [teljes ARM-sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) a GitHub-adattárból.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha végzett, és ha szeretné, törölheti az erőforráscsoportot. Az erőforráscsoport törlésének művelete nem visszafordítható.

> [!IMPORTANT]
> Az erőforráscsoportokban található összes erőforrás véglegesen törlődik, és nem vonható vissza.

1. A Azure Portal keresőmezőbe írja be a következőt: **Azure NetApp Files,** majd Azure NetApp Files a megjelenő listából. 

2. Az előfizetések listájában kattintson a törölni kívánt erőforráscsoportra (myRG1).

    ![Lépjen az erőforráscsoportokhoz](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Az erőforráscsoport oldalán kattintson az **Erőforráscsoport törlése elemre.**

    ![Képernyőkép az Erőforráscsoport törlése gombról.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    Ekkor megnyílik egy ablak, mely figyelmezteti az erőforráscsoporttal törölt erőforrásokról.

4. Adja meg az erőforráscsoport nevét (myRG1) annak megerősítéséhez, hogy véglegesen törölni szeretné az erőforráscsoportot és az összes benne álló erőforrást, majd kattintson a **Törlés gombra.**

    ![Erőforráscsoport törlésének megerősítése](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha végzett, és ha szeretné, törölheti az erőforráscsoportot. Az erőforráscsoport törlésének művelete nem visszafordítható.

> [!IMPORTANT]
> Az erőforráscsoportokban található összes erőforrás véglegesen törlődik, és nem vonható vissza.

1. Törölje az erőforráscsoportot a [Remove-AzResourceGroup paranccsal.](/powershell/module/az.resources/remove-azresourcegroup)

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha végzett, és ha szeretné, törölheti az erőforráscsoportot. Az erőforráscsoport törlésének művelete nem visszafordítható.

> [!IMPORTANT]
> Az erőforráscsoportokban található összes erőforrás véglegesen törlődik, és nem vonható vissza.

1. Törölje az erőforráscsoportot [az az group delete paranccsal.](/cli/azure/group#az-group-delete)

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Sablon](#tab/template)

Nincsenek.

Használja a Azure Portal, a PowerShellt vagy az Azure CLI-t az erőforráscsoport törléséhez.

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure NetApp Files tárhely-hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [NFS-kötet létrehozása](azure-netapp-files-create-volumes.md)
