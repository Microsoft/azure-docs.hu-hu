---
title: Ultra Disks virtuális gépekhez – Azure Managed Disks
description: Tudnivalók az Azure-beli virtuális gépekhez használható Ultra-lemezekről
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e57317dce64b58e5c92684152d840955a30df660
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441186"
---
# <a name="using-azure-ultra-disks"></a>Az Azure Ultra Disks használata

Ez a cikk azt ismerteti, hogyan helyezhet üzembe és használhat Ultra-lemezeket az ultra-lemezekkel kapcsolatos elméleti információkkal kapcsolatban: [milyen típusú lemezek érhetők el az Azure-ban?](disks-types.md#ultra-disk).

Az Azure Ultra Disks nagy teljesítményű, magas IOPS és konzisztens, alacsony késésű lemezes tárolást biztosít az Azure IaaS Virtual Machines (VM) szolgáltatásokhoz. Ez az új ajánlat a vonal teljesítményét a meglévő lemezekkel megegyező rendelkezésre állási szinten biztosítja. Az ultra Disks szolgáltatás egyik fő előnye, hogy dinamikusan megváltoztathatja az SSD teljesítményét a számítási feladatokkal együtt anélkül, hogy újra kellene indítania a virtuális gépeket. Az ultralemezek olyan adatigényes számítási feladatokhoz használhatók, mint az SAP HANA, a felső szintű adatbázisok és a tranzakcióigényes számítási feladatok.

## <a name="ga-scope-and-limitations"></a>A GA hatóköre és korlátai

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>A virtuális gép méretének és a régió rendelkezésre állásának meghatározása

### <a name="vms-using-availability-zones"></a>Rendelkezésre állási zónákat használó virtuális gépek

Az ultra-lemezek kihasználása érdekében meg kell határoznia, hogy melyik rendelkezésre állási zónát használja. Nem minden régió támogatja a virtuálisgép-méretet az ultra Disks szolgáltatással. Annak megállapításához, hogy a régió, a zóna és a virtuálisgép-méret támogatja-e az ultra-lemezeket, futtassa a következő parancsok egyikét, és először cserélje le a **régiót**, a **vmSize**és az **előfizetési** értékeket:

#### <a name="cli"></a>parancssori felület

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

A válasz az alábbi űrlaphoz hasonló lesz, ahol az X a kiválasztott régióban való üzembe helyezéshez használt zóna. Az X a következők egyike lehet: 1, 2 vagy 3.

Őrizze meg a **zónák** értékét, amely a rendelkezésre állási zónát képviseli, és szüksége lesz rá egy ultra-lemez üzembe helyezése érdekében.

|ResourceType  |Név  |Hely  |Zóna  |Korlátozás  |Képesség  |Érték  |
|---------|---------|---------|---------|---------|---------|---------|
|lemezek     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Ha nem volt válasz a parancstól, akkor a kiválasztott virtuálisgép-méret nem támogatott a kiválasztott régióban lévő Ultra-lemezek esetén.

Most, hogy megismerte, hogy melyik zónát kívánja telepíteni, kövesse a jelen cikk központi telepítési lépéseit, és telepítsen egy virtuális gépet egy csatlakoztatott, vagy egy ultra lemez csatlakoztatása egy meglévő virtuális géphez.

### <a name="vms-with-no-redundancy-options"></a>Redundancia nélküli virtuális gépek

A kiválasztott régiókban üzembe helyezett Ultra lemezeket redundancia nélküli beállítások nélkül kell üzembe helyezni. Azonban nem minden olyan lemezterület, amely támogatja az ultra-lemezeket, ezekben a régiókban lehet. A következő kódrészletek bármelyikével megállapíthatja, hogy mely méretű meghajtók támogatják az ultra-lemezeket. Először cserélje le a `vmSize` és az `subscription` értékeket:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

A válasz az alábbi űrlaphoz hasonló lesz, és `UltraSSDAvailable   True` azt jelzi, hogy a virtuálisgép-méret támogatja-e az ultra-lemezeket ebben a régióban.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Ultra-lemez üzembe helyezése Azure Resource Manager használatával

Először határozza meg a telepítendő virtuális gép méretét. A támogatott virtuálisgép-méretek listáját a [GA hatókörét és korlátozásait](#ga-scope-and-limitations) ismertető szakaszban találja.

Ha több Ultra lemezzel rendelkező virtuális gépet szeretne létrehozni, tekintse meg a mintát [több Ultra lemezzel rendelkező virtuális gép létrehozása](https://aka.ms/ultradiskArmTemplate)című témakörben.

Ha saját sablont szeretne használni, győződjön meg róla, hogy a **apiVersion** a `Microsoft.Compute/virtualMachines` és a `Microsoft.Compute/Disks` `2018-06-01` (vagy újabb) értékre van beállítva.

Állítsa be a lemez SKU-jának **UltraSSD_LRS**, majd állítsa be a lemez kapacitása, a IOPS, a rendelkezésre állási zóna és az átviteli sebesség (MB/s) értéket egy ultra-lemez létrehozásához.

A virtuális gép üzembe helyezése után particionálhatja és formázhatja az adatlemezeket, és konfigurálhatja azokat a számítási feladatokhoz.


## <a name="deploy-an-ultra-disk"></a>Ultra-lemez üzembe helyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ez a szakasz a virtuális gép adatlemezként való üzembe helyezését ismerteti. Feltételezi, hogy ismeri a virtuális gép üzembe helyezését, és ha nem, tekintse meg a rövid útmutató [: Windows rendszerű virtuális gép létrehozása a Azure Portal](./windows/quick-create-portal.md).

- Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és navigáljon a virtuális gép (VM) üzembe helyezéséhez.
- Győződjön meg arról, hogy [támogatott virtuálisgép-méret és-régió](#ga-scope-and-limitations)van kiválasztva.
- Válassza ki a rendelkezésre **állási zónát** a **rendelkezésre állási beállításoknál**
- Adja meg a fennmaradó bejegyzéseket a választott lehetőségek közül.
- Válassza a **Lemezek** lehetőséget.

![Képernyőkép a virtuálisgép-létrehozási folyamatról, az alapszintű panelről.](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- A lemezek panelen válassza az **Igen** lehetőséget az **ultravékony lemezek kompatibilitásának engedélyezéséhez**.
- Válassza **a létrehozás lehetőséget, és csatoljon egy új lemezt** egy ultra-lemez csatlakoztatásához.

![Képernyőkép a virtuálisgép-létrehozási folyamatról, a lemez panelről, az ultra engedélyezve van, és új lemez létrehozása és csatolása van kiemelve.](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Az **új lemez létrehozása** panelen adjon meg egy nevet, majd válassza a **méret módosítása**lehetőséget.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-create-new-disk-flow.png" alt-text="Képernyőfelvétel: új lemez létrehozása panel, méret kiemelve.":::


- Módosítsa a **tárolási típust** **Ultra-lemezre**.
- Módosítsa az **Egyéni lemez méretének (GIB)**, a **lemez IOPS**és a **lemez átviteli sebességének** értékét.
- Mindkét panelen válassza **az OK** lehetőséget.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Képernyőfelvétel: új lemez létrehozása panel, méret kiemelve.":::

- Folytassa a virtuális gép üzembe helyezésével, ugyanúgy fog működni, mint bármely más virtuális gép üzembe helyezése esetén.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először határozza meg a telepítendő virtuális gép méretét. A támogatott virtuálisgép-méretek listáját a [GA hatókör és korlátozások](#ga-scope-and-limitations) című szakaszban találja.

Ultra-lemez csatlakoztatásához létre kell hoznia egy olyan virtuális gépet, amely képes az ultrakönnyű lemezek használatára.

Cserélje le vagy állítsa be a **$vmname**, **$rgname**, **$diskname**, **$Location**, **$Password**, **$User** változókat a saját értékeivel. Állítsa **$Zone**  értéket a [cikk elejétől](#determine-vm-size-and-region-availability)kapott rendelkezésre állási zóna értékére. Ezután futtassa az alábbi CLI-parancsot egy ultra-kompatibilis virtuális gép létrehozásához:

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Először határozza meg a telepítendő virtuális gép méretét. A támogatott virtuálisgép-méretek listáját a [GA hatókör és korlátozások](#ga-scope-and-limitations) című szakaszban találja.

Az ultra-lemezek használatához létre kell hoznia egy virtuális gépet, amely képes az ultra lemezek használatára. Cserélje le vagy állítsa be a **$resourcegroup** és a **$vmName** változót a saját értékeivel. Állítsa **$Zone** értéket a [cikk elejétől](#determine-vm-size-and-region-availability)kapott rendelkezésre állási zóna értékére. Ezután futtassa a következő [New-AzVm](/powershell/module/az.compute/new-azvm) parancsot egy ultra-kompatibilis virtuális gép létrehozásához:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>Lemez létrehozása és csatolása

Miután telepítette a virtuális gépet, létrehozhat és csatlakoztathat egy ultravékony lemezt a következő parancsfájl használatával:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="attach-an-ultra-disk"></a>Ultra-lemez csatlakoztatása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha a meglévő virtuális gép egy olyan régióban vagy rendelkezésre állási zónában van, amely képes az ultra-lemezek használatára, az új virtuális gép létrehozása nélkül is használhatja az ultra lemezeket. A meglévő virtuális gépen lévő Ultra Disks szolgáltatás engedélyezésével, majd adatlemezként való csatlakoztatásával. Az ultravékony lemezek kompatibilitásának engedélyezéséhez le kell állítania a virtuális gépet. A virtuális gép leállítása után engedélyezheti a kompatibilitást, majd újraindíthatja a virtuális gépet. Ha a kompatibilitási funkció engedélyezve van, akkor az ultra Disk:

- Navigáljon a virtuális géphez, és állítsa le, várja meg, amíg felszabadítja.
- A virtuális gép kiosztása után válassza a **lemezek**lehetőséget.
- Válassza a **Szerkesztés** elemet.

![Képernyőkép egy meglévő virtuálisgép-lemez panelről, a Szerkesztés kiemelve.](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Válassza az **Igen** lehetőséget az **ultravékony lemezek kompatibilitásának engedélyezéséhez**.

![Képernyőfelvétel: az ultravékony lemezek kompatibilitásának engedélyezése.](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Kattintson a **Mentés** gombra.
- Válassza az **adatlemez hozzáadása** lehetőséget, majd a legördülő **listából válassza a** **lemez létrehozása**lehetőséget.

![Képernyőfelvétel a lemez panelről, új lemez hozzáadása.](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Adja meg az új lemez nevét, majd válassza a **méret módosítása**lehetőséget.
- Módosítsa a **fiók típusát** **Ultra-lemezre**.
- Módosítsa az **Egyéni lemez méretének (GIB)**, a **lemez IOPS**és a **lemez átviteli sebességének** értékét.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Képernyőfelvétel: új lemez létrehozása panel, méret kiemelve.":::

- Válassza **az OK** , majd a **Létrehozás**lehetőséget.
- Miután visszatért a lemez paneljére, válassza a **Mentés**lehetőséget.
- Indítsa el újra a virtuális gépet.

![Képernyőfelvétel a virtuális gép lemezek paneljéről.](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha a meglévő virtuális gép egy olyan régióban vagy rendelkezésre állási zónában van, amely képes az ultra-lemezek használatára, az új virtuális gép létrehozása nélkül is használhatja az ultra lemezeket.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>Az ultravékony lemezek kompatibilitásának engedélyezése meglévő virtuális gépen – parancssori felület

Ha a virtuális gép megfelel a [GA-hatókörben és-korlátozásokban](#ga-scope-and-limitations) ismertetett követelményeknek, és a [fiókja megfelelő zónában](#determine-vm-size-and-region-availability)van, akkor engedélyezheti a virtuális gépen az ultra Disk-kompatibilitást.

Az ultravékony lemezek kompatibilitásának engedélyezéséhez le kell állítania a virtuális gépet. A virtuális gép leállítása után engedélyezheti a kompatibilitást, majd újraindíthatja a virtuális gépet. Ha a kompatibilitási funkció engedélyezve van, akkor az ultra Disk:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Ultravékony lemez létrehozása – parancssori felület

Most, hogy rendelkezik egy olyan virtuális géppel, amely képes az ultra-lemezek csatolására, létrehozhat és csatlakoztathat egy ultra-lemezt.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>A lemez csatolása – parancssori felület

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a meglévő virtuális gép egy olyan régióban vagy rendelkezésre állási zónában van, amely képes az ultra-lemezek használatára, az új virtuális gép létrehozása nélkül is használhatja az ultra lemezeket.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>Az ultravékony lemezek kompatibilitásának engedélyezése meglévő virtuális gépen – PowerShell

Ha a virtuális gép megfelel a [GA-hatókörben és-korlátozásokban](#ga-scope-and-limitations) ismertetett követelményeknek, és a [fiókja megfelelő zónában](#determine-vm-size-and-region-availability)van, akkor engedélyezheti a virtuális gépen az ultra Disk-kompatibilitást.

Az ultravékony lemezek kompatibilitásának engedélyezéséhez le kell állítania a virtuális gépet. A virtuális gép leállítása után engedélyezheti a kompatibilitást, majd újraindíthatja a virtuális gépet. Ha a kompatibilitási funkció engedélyezve van, akkor az ultra Disk:

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Ultra Disk-PowerShell létrehozása és csatolása

Most, hogy rendelkezik egy olyan virtuális géppel, amely képes az ultra-lemezek használatára, létrehozhat és csatlakoztathat egy ultravékony lemezt:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Ultra-lemez teljesítményének módosítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az ultra Disks egyedi képességgel rendelkezik, amely lehetővé teszi a teljesítmény módosítását. Ezeket a módosításokat a Azure Portal lemezeken is végrehajthatja.

- Navigáljon a virtuális géphez, és válassza a **lemezek**lehetőséget.
- Válassza ki azt az ultra lemezt, amelynek a teljesítményét módosítani szeretné.

![A virtuális gép lemezek paneljének képernyőképe, a lemez kiemelve.](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Válassza a **konfiguráció** lehetőséget, majd végezze el a módosításokat.
- Kattintson a **Mentés** gombra.

![Képernyőkép a konfiguráció panelről, amely az ultra lemez, a lemez mérete, a IOPS és az átviteli sebesség ki van emelve, a Mentés ki van emelve.](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az ultra Disks olyan egyedi képességet kínál, amely lehetővé teszi a teljesítmény módosítását, a következő parancs a funkció használatát mutatja be:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ultra-lemez teljesítményének módosítása a PowerShell használatával

Az ultra-lemezek egyedi képességgel rendelkeznek, amely lehetővé teszi a teljesítmény módosítását, a következő parancs egy példa, amely a lemez leválasztása nélkül módosítja a teljesítményt:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>További lépések

- [Azure Ultra Disks használata az Azure Kubernetes Service-ben (előzetes verzió)](../aks/use-ultra-disks.md).
- [Helyezze át a naplófájlt egy ultra-lemezre](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md).