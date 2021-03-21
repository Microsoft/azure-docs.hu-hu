---
title: Migrálás a Resource Managerbe a PowerShell használatával
description: Ez a cikk végigvezeti a platform által támogatott IaaS-erőforrások, például a virtuális gépek (VM-EK), a virtuális hálózatok és a klasszikus eszközökről a Storage-fiókok áttelepítésének Azure PowerShell parancsok használatával történő Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c750e34e8081cf5a8b3d41cc8c52584a4353a336
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695158"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](classic-vm-deprecation.md#how-does-this-affect-me).

Ezek a lépések bemutatják, hogyan használhatók Azure PowerShell parancsok a klasszikus üzemi modellből származó infrastruktúra szolgáltatásként (IaaS) a Azure Resource Manager üzembe helyezési modellbe való áttelepítéséhez.

Ha szeretné, az [Azure CLI](migration-classic-resource-manager-cli.md)használatával is áttelepítheti az erőforrásokat.

* A támogatott áttelepítési forgatókönyvek hátteréről lásd: a [IaaS-erőforrások platform által támogatott áttelepítése klasszikusról Azure Resource Managerra](migration-classic-resource-manager-overview.md).
* Részletes útmutatást és áttelepítési útmutatót a [klasszikus és Azure Resource Manager platform által támogatott áttelepítéssel foglalkozó](migration-classic-resource-manager-deep-dive.md)témakörben talál.
* [Tekintse át a leggyakoribb áttelepítési hibákat](migration-classic-resource-manager-errors.md).

<br>
Az alábbi folyamatábra azt határozza meg, hogy milyen sorrendben kell végrehajtani a lépéseket egy áttelepítési folyamat során.

![Képernyőkép a migrálási lépésekről](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>1. lépés: az áttelepítés megtervezése
Íme néhány ajánlott eljárás, amelyet a IaaS-erőforrások klasszikusról Resource Managerbe való áttelepítésének kiértékelése során ajánlunk:

* Olvassa el a [támogatott és nem támogatott funkciókat és konfigurációkat](migration-classic-resource-manager-overview.md). Ha olyan virtuális gépekkel rendelkezik, amelyek nem támogatott konfigurációkat vagy szolgáltatásokat használnak, várjon, amíg a konfiguráció vagy a szolgáltatás támogatása bejelentve lesz. Ha megfelel az igényeinek, távolítsa el ezt a szolgáltatást, vagy lépjen ki a konfigurációból az áttelepítés engedélyezéséhez.
* Ha olyan automatizált parancsfájlokkal rendelkezik, amelyek jelenleg telepítik az infrastruktúrát és az alkalmazásokat, próbáljon meg hasonló tesztet létrehozni a parancsfájlok áttelepítéshez való használatával. Azt is megteheti, hogy a Azure Portal használatával is beállíthatja a mintavételezési környezeteket.

> [!IMPORTANT]
> Az Application Gateway jelenleg nem támogatott a Klasszikusból a Resource Managerbe való áttelepítéshez. Ha egy virtuális hálózatot Application Gateway átjáróval szeretne áttelepíteni, távolítsa el az átjárót, mielőtt elkezdené a hálózat áthelyezését. Az áttelepítés befejezése után csatlakoztassuk újra az átjárót Azure Resource Manager.
>
> Az ExpressRoute-áramkörökhöz egy másik előfizetésben csatlakozó Azure ExpressRoute-átjárók nem telepíthetők át automatikusan. Ilyen esetekben távolítsa el a ExpressRoute-átjárót, telepítse át a virtuális hálózatot, majd hozza létre újra az átjárót. További információ: [a ExpressRoute-áramkörök és a társított virtuális hálózatok áttelepítése a Klasszikusból a Resource Manager](../expressroute/expressroute-migration-classic-resource-manager.md)-alapú üzemi modellbe.

## <a name="step-2-install-the-latest-version-of-powershell"></a>2. lépés: a PowerShell legújabb verziójának telepítése
Két fő lehetőség van a Azure PowerShell telepítésére: [PowerShell-Galéria](https://www.powershellgallery.com/profiles/azure-sdk/) vagy [webplatform-telepítő (WebPI)](https://aka.ms/webpi-azps). A WebPI havi frissítéseket fogad. A PowerShell-galéria folyamatosan fogadja a frissítéseket. Ez a cikk a Azure PowerShell 2.1.0 verzióján alapul.

A telepítési utasításokért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>3. lépés: Győződjön meg arról, hogy Ön az előfizetés rendszergazdája
Az áttelepítés elvégzéséhez hozzá kell adnia az előfizetéshez tartozó [Azure Portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **központi** menüben válassza az **előfizetés** lehetőséget. Ha nem látja, válassza a **minden szolgáltatás** lehetőséget.
3. Keresse meg a megfelelő előfizetési bejegyzést, majd tekintse meg a **saját szerepkör** mezőt. A rendszergazdák számára az értéknek a _fiók_ rendszergazdája kell lennie.

Ha nem tud felvenni egy társ-rendszergazda szerepkört, forduljon a szolgáltatás rendszergazdájához vagy a társ-rendszergazdához, és kérje a hozzáadást.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4. lépés: az előfizetés beállítása és regisztráció az áttelepítésre
Először indítson el egy PowerShell-parancssort. Az áttelepítéshez állítsa be a környezetet a klasszikus és a Resource Managerhez.

Jelentkezzen be a fiókjába a Resource Manager-modellhez.

```powershell
    Connect-AzAccount
```

Szerezze be az elérhető előfizetéseket a következő paranccsal:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Állítsa be az Azure-előfizetését az aktuális munkamenethez. Ez a példa az alapértelmezett előfizetés nevét állítja be az **Azure-előfizetésre**. Cserélje le a példában szereplő előfizetés nevét a saját nevére.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> A regisztráció egyszeri lépés, de csak egyszer kell megtennie az áttelepítés megkísérlése előtt. A regisztráció nélkül a következő hibaüzenet jelenik meg:
>
> *BadRequest: az előfizetés nincs regisztrálva áttelepítésre.*

Regisztrálja az áttelepítési erőforrás-szolgáltatót a következő parancs használatával:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Várjon öt percet, amíg a regisztráció befejeződik. A jóváhagyás állapotának ellenőrzéséhez használja a következő parancsot:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

A folytatás előtt győződjön meg arról, hogy a RegistrationState `Registered` .

A klasszikus üzemi modellre való áttérés előtt győződjön meg arról, hogy elegendő Azure Resource Manager virtuálisgép-vCPU van a jelenlegi üzembe helyezése vagy virtuális hálózata Azure-régiójában. A következő PowerShell-paranccsal ellenőrizhető, hogy az aktuálisan hány vCPU Azure Resource Manager. További információ a vCPU-kvótákkal kapcsolatban: [korlátok és a Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Ez a példa az **USA nyugati** régiójában lévő rendelkezésre állást ellenőrzi. Cserélje le a példában szereplő régió nevét a saját nevére.

```powershell
    Get-AzVMUsage -Location "West US"
```

Most jelentkezzen be a fiókjába a klasszikus üzemi modellhez.

```powershell
    Add-AzureAccount
```

Szerezze be az elérhető előfizetéseket a következő paranccsal:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Állítsa be az Azure-előfizetését az aktuális munkamenethez. Ez a példa az alapértelmezett előfizetést állítja be az **Azure-előfizetésre**. Cserélje le a példában szereplő előfizetés nevét a saját nevére.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>5. lépés: parancsok futtatása a IaaS-erőforrások áttelepíteni
* [Virtuális gépek migrálása felhőalapú szolgáltatásban (nem virtuális hálózatban)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Virtuális gépek migrálása virtuális hálózatban](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Storage-fiók migrálása](#step-52-migrate-a-storage-account)

> [!NOTE]
> Az itt leírt összes művelet idempotens. Ha a nem támogatott funkció vagy a konfigurációs hiba nem megfelelő, javasoljuk, hogy próbálkozzon újra az előkészítés, a megszakítás vagy a végrehajtás művelettel. A platform ezután újra próbálkozik a művelettel.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>5,1. lépés: 1. lehetőség – virtuális gépek áttelepítése felhőalapú szolgáltatásba (nem virtuális hálózatban)
Szerezze be a Cloud Services listáját a következő parancs használatával. Ezután válassza ki az áttelepíteni kívánt felhőalapú szolgáltatást. Ha a felhőalapú szolgáltatásban lévő virtuális gépek virtuális hálózaton vannak, vagy webes vagy feldolgozói szerepkörökkel rendelkeznek, a parancs hibaüzenetet ad vissza.

```powershell
    Get-AzureService | ft Servicename
```

Szerezze be a felhőalapú szolgáltatás központi telepítési nevét. Ebben a példában a szolgáltatás neve a **saját szolgáltatás**. Cserélje le a példában szereplő szolgáltatásnevet a saját szolgáltatás nevére.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Készítse elő a virtuális gépeket a Cloud Service-ben áttelepítésre. Két lehetőség közül választhat.

* **1. lehetőség: telepítse át a virtuális gépeket egy platform által létrehozott virtuális hálózatra.**

    Először is ellenőrizze, hogy a következő parancsok segítségével áttelepítheti-e a felhőalapú szolgáltatást:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    A következő parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az érvényesítési üzenetek nem tartalmaznak hiba típusú üzenetet, akkor átléphet az előkészítési lépésre.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **2. lehetőség: Migrálás egy meglévő virtuális hálózatra a Resource Manager-alapú üzemi modellben.**

    Ebben a példában az erőforráscsoport nevét **myResourceGroup**, a virtuális hálózat nevét a **myVirtualNetwork** értékre, az alhálózat nevét pedig a **mySubNet** értékre állítja. Cserélje le a példában szereplő neveket a saját erőforrásainak nevére.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Először is ellenőrizze, hogy a következő paranccsal telepítheti-e át a virtuális hálózatot:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    A következő parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az érvényesítési üzenetek nem tartalmaznak hibákat, folytassa a következő előkészítési lépéssel:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Miután az előkészítési művelet sikeresen megtörtént az előző beállítások bármelyikével, lekérdezheti a virtuális gépek áttelepítési állapotát. Győződjön meg arról, hogy az `Prepared` állapotban vannak.

Ebben a példában a virtuális gép nevét **myVM** értékre állítja. Cserélje le a példában szereplő nevet a saját virtuális gépe nevére.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

A PowerShell vagy a Azure Portal használatával vizsgálja meg az előkészített erőforrások konfigurációját. Ha nem áll készen az áttelepítésre, és vissza szeretne térni a régi állapotra, használja a következő parancsot:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Ha az előkészített konfiguráció jól néz ki, a következő parancs használatával továbbíthatja és véglegesítheti az erőforrásokat:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>5,1. lépés: 2. lehetőség – virtuális gépek migrálása virtuális hálózatban

A virtuális gépek virtuális hálózatban való áttelepíthetők a virtuális hálózat áttelepíthetők. A virtuális gépeket a rendszer automatikusan áttelepíti a virtuális hálózattal. Válassza ki az áttelepíteni kívánt virtuális hálózatot.
> [!NOTE]
> [Telepítsen egy](./windows/create-vm-specialized-portal.md) , a klasszikus üzemi modellel létrehozott virtuális gépet úgy, hogy új Resource Manager-alapú virtuális gépet hoz létre Managed Disks a virtuális gép VHD-fájljának (os-és adatfájljainak) használatával.
<br>

> [!NOTE]
> Lehet, hogy a virtuális hálózat neve eltér az új portálon láthatótól. Az új Azure Portal a nevet jeleníti meg `[vnet-name]` , de a tényleges virtuális hálózat neve típusú `Group [resource-group-name] [vnet-name]` . Az áttelepítés megkezdése előtt keresse meg a tényleges virtuális hálózat nevét a parancs használatával, `Get-AzureVnetSite | Select -Property Name` vagy tekintse meg a régi Azure Portal. 

Ez a példa a virtuális hálózat nevét állítja be a **myVnet**. Cserélje le a példában szereplő virtuális hálózat nevét a saját nevére.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Ha a virtuális hálózat webes vagy feldolgozói szerepköröket, illetve nem támogatott konfigurációval rendelkező virtuális gépeket tartalmaz, érvényesítési hibaüzenetet kap.

Először is ellenőrizze, hogy a következő paranccsal telepítheti-e át a virtuális hálózatot:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

A következő parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az ellenőrzés sikeres, folytassa a következő előkészítési lépéssel:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Az előkészített virtuális gépek konfigurációját a Azure PowerShell vagy a Azure Portal használatával vizsgálhatja meg. Ha nem áll készen az áttelepítésre, és vissza szeretne térni a régi állapotra, használja a következő parancsot:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Ha az előkészített konfiguráció jól néz ki, a következő parancs használatával továbbíthatja és véglegesítheti az erőforrásokat:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>5,2. lépés: Storage-fiók átmigrálása
Miután befejezte a virtuális gépek áttelepítését, hajtsa végre az alábbi előfeltételek ellenőrzését a Storage-fiókok áttelepítése előtt.

> [!NOTE]
> Ha a Storage-fiók nem rendelkezik társított lemezekkel vagy virtuálisgép-adatmennyiséggel, akkor közvetlenül a "Storage-fiókok ellenőrzése és az áttelepítés megkezdése" szakaszban hagyhatja ki. Azt is vegye figyelembe, hogy a klasszikus lemezek, a virtuálisgép-lemezképek vagy az operációsrendszer-lemezképek törlése nem távolítja el a forrás VHD-fájlokat a Storage-fiókban. Azonban megszakítja az adott VHD-fájlok bérletét, hogy az áttelepítést követően újra felhasználhatók legyenek az ARM-lemezek vagy-lemezképek létrehozásához.

* Előfeltételek ellenőrzése ha a virtuális gépeket áttelepítette, vagy a Storage-fiók lemezes erőforrásokkal rendelkezik:
    * Telepítse át azokat a virtuális gépeket, amelyek lemezeit a Storage-fiók tárolja.

        A következő parancs a Storage-fiókban lévő összes virtuálisgép-lemez RoleName és DiskName tulajdonságait adja vissza. A RoleName annak a virtuális gépnek a neve, amelyhez a lemez csatolva van. Ha a parancs lemezeket ad vissza, győződjön meg arról, hogy a virtuális gépeket, amelyekre ezek a lemezek csatlakoznak, a Storage-fiók átmigrálása előtt telepítse át.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Törölje a Storage-fiókban tárolt nem csatolt virtuálisgép-lemezeket.

        A következő parancs használatával keresse meg a nem csatlakoztatott virtuálisgép-lemezeket a Storage-fiókban:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Ha az előző parancs lemezeket ad vissza, törölje ezeket a lemezeket a következő parancs használatával:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Törölje a Storage-fiókban tárolt virtuálisgép-lemezképeket.

        A következő parancs visszaadja az összes virtuálisgép-lemezképet a Storage-fiókban tárolt operációsrendszer-lemezekkel.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         A következő parancs az összes virtuálisgép-rendszerképet visszaadja a Storage-fiókban tárolt adatlemezekkel.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Törölje az előző parancsokban visszaadott összes virtuálisgép-lemezképet a következő parancs használatával:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* A Storage-fiókok ellenőrzése és az áttelepítés megkezdése.

    A következő parancs használatával ellenőrizze az egyes Storage-fiókok áttelepítését. Ebben a példában a Storage-fiók neve **myStorageAccount**. Cserélje le a példában szereplő nevet a saját Storage-fiókja nevére.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    A következő lépés a Storage-fiók előkészítése az áttelepítéshez.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Az előkészített Storage-fiók konfigurációját a Azure PowerShell vagy a Azure Portal használatával vizsgálhatja meg. Ha nem áll készen az áttelepítésre, és vissza szeretne térni a régi állapotra, használja a következő parancsot:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Ha az előkészített konfiguráció jól néz ki, a következő parancs használatával továbbíthatja és véglegesítheti az erőforrásokat:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Következő lépések
* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md)
