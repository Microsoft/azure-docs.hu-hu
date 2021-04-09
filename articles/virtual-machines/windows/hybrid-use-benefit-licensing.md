---
title: Azure Hybrid Benefit Windows Serverhez
description: Ismerje meg, hogyan maximalizálhatja a Windows-frissítési garanciát a helyszíni licencek Azure-ba történő bevonásához.
author: xujing-ms
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: c5a9386540f418c8f490ca146e250e780737e478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668066"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit Windows Serverhez
A frissítési garanciával rendelkező ügyfelek számára a Windows Server Azure Hybrid Benefit lehetővé teszi a helyszíni Windows Server-licencek használatát, és a Windows rendszerű virtuális gépek futtatását az Azure-ban csökkentett költségek mellett. A Windows Server Azure Hybrid Benefit használatával telepíthet új virtuális gépeket Windows operációs rendszerre. Ez a cikk áttekinti az új virtuális gépek Windows Serverre Azure Hybrid Benefit való üzembe helyezésének lépéseit, valamint azt, hogy miként frissítheti a meglévő futó virtuális gépeket. A Windows Server licenceléssel és a költségmegtakarítással Azure Hybrid Benefitával kapcsolatos további információkért tekintse meg a [Azure Hybrid Benefit a Windows Server licencelése lapon](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

A kétprocesszoros licencek vagy a 16 Magos licencek minden készlete legfeljebb 8 magos példányra jogosult, vagy egy legfeljebb 16 maggal rendelkező példányra. A Standard Edition-licencek Azure Hybrid Benefit csak egyszer használható a helyszínen vagy az Azure-ban. A Datacenter Edition előnyei lehetővé teszik a helyszíni és az Azure-ban való egyidejű használatot.

A Windows Server operációs rendszert futtató virtuális gépekkel való Azure Hybrid Benefit használata mostantól minden régióban támogatott, beleértve a további szoftverrel rendelkező virtuális gépeket, például a SQL Server vagy a harmadik féltől származó piactér szoftverét. 


## <a name="classic-vms"></a>A klasszikus virtuális gépeket

A klasszikus virtuális gépek esetében csak az új virtuális gép üzembe helyezése a helyszíni egyéni lemezképekben támogatott. A cikkben támogatott képességek kihasználásához először át kell telepítenie a klasszikus virtuális gépeket a Resource Manager-modellbe.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>A Windows Server Azure Hybrid Benefit használatának módjai
A Windows rendszerű virtuális gépeket többféleképpen is használhatja a Azure Hybrid Benefit:

1. Az Azure Marketplace-en telepítheti a virtuális gépeket az egyik megadott Windows Server-rendszerképből
2. Feltölthet egy egyéni virtuális gépet, és üzembe helyezheti egy Resource Manager-sablonnal vagy Azure PowerShell
3. A meglévő virtuális gépeket az Azure Hybrid Benefit vagy a Windows Serverre vonatkozó igény szerinti fizetési díj használatával válthat és alakíthatja át.
4. A Windows Server rendszerhez Azure Hybrid Benefit is alkalmazhat a virtuálisgép-méretezési csoporton is


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Virtuális gép létrehozása Azure Hybrid Benefit a Windows Serverhez
A Windows Server operációs rendszerhez készült összes Windows Server-alapú rendszerkép támogatott a Azure Hybrid Benefit. Használhatja az Azure platform támogatási lemezképeit, vagy feltöltheti saját Windows Server-lemezképeit. 

### <a name="portal"></a>Portál
Ha Azure Hybrid Benefit for Windows Server rendszerű virtuális gépet szeretne létrehozni, görgessen az **alapok** lap aljára a létrehozási folyamat során, és a **Licencelés** alatt jelölje be a jelölőnégyzetet a meglévő Windows Server-licencek használatához. 

### <a name="powershell"></a>PowerShell

```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Sablon
A Resource Manager-sablonokon belül meg kell adni egy további paramétert `licenseType` . További információ a [Azure Resource Manager sablonok létrehozásáról](../../azure-resource-manager/templates/template-syntax.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Meglévő virtuális gép konvertálása a Windows Server Azure Hybrid Benefit használatával
Ha rendelkezik olyan meglévő virtuális géppel, amelyet át szeretne alakítani, hogy kihasználhassa a Windows Server Azure Hybrid Benefit előnyeit, az alábbi utasításokat követve frissítheti a virtuális gép licencének típusát.

> [!NOTE]
> A licenc típusának módosítása a virtuális gépen nem okozza a rendszer újraindítását vagy a szolgáltatás interuption okát.  Ez egyszerűen egy metaadat-jelző frissítése.
> 

### <a name="portal"></a>Portál
A portál virtuálisgép-paneljén frissítheti a virtuális gépet a Azure Hybrid Benefit használatára, ha kiválasztja a "konfiguráció" lehetőséget, és bekapcsolja az "Azure Hybrid Benefit" lehetőséget.

### <a name="powershell"></a>PowerShell
- Meglévő Windows Server-alapú virtuális gépek konvertálása Azure Hybrid Benefit Windows Serverre

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- A Windows Server rendszerű virtuális gépek átalakítása az utólagos elszámolású

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>parancssori felület
- Meglévő Windows Server-alapú virtuális gépek konvertálása Azure Hybrid Benefit Windows Serverre

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Annak ellenőrzése, hogy a virtuális gép használja-e a licencelési juttatást
Miután telepítette a virtuális gépet a PowerShell, a Resource Manager-sablon vagy-portál használatával, ellenőrizheti a beállítást a következő módszerekkel.

### <a name="portal"></a>Portál
A portál virtuálisgép-paneljén a "Konfigurálás" lapon megtekintheti a Windows Server rendszerhez tartozó Azure Hybrid Benefit váltógomb.

### <a name="powershell"></a>PowerShell
Az alábbi példa egy adott virtuális gép licencének típusát mutatja be
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Kimenet:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ez a kimenet ellentétben áll a következő, a Windows Server licencelési Azure Hybrid Benefit nélkül üzembe helyezett virtuális géppel:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> A licenc típusának módosítása a virtuális gépen nem okozza a rendszer újraindítását vagy a szolgáltatás interuption okát. Csak metaadat-licencelési jelző.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>A Windows Serverhez Azure Hybrid Benefit rendelkező összes virtuális gép listázása egy előfizetésben
Az Azure Hybrid Benefit for Windows Serverrel telepített összes virtuális gép megtekintéséhez és számának megadásához futtassa az alábbi parancsot az előfizetésből:

### <a name="portal"></a>Portál
A virtuális gép vagy a virtuálisgép-méretezési készletek erőforrás paneljén megtekintheti az összes virtuális gép (ek) és licencelési típus listáját úgy, hogy a tábla oszlopot úgy konfigurálja, hogy tartalmazza a "Azure Hybrid Benefit" értéket. A virtuális gép beállítása "engedélyezve", "nincs engedélyezve" vagy "nem támogatott" állapotban lehet.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Virtuálisgép-méretezési csoport üzembe helyezése Azure Hybrid Benefit a Windows Serverhez
A virtuálisgép-méretezési csoport Resource Manager-sablonjain belül egy további paramétert is meg kell `licenseType` adni a VirtualMachineProfile tulajdonságban. Ezt a méretezési csoport létrehozása vagy frissítése során a ARM-sablon, a PowerShell, az Azure CLI vagy a REST használatával teheti meg.

Az alábbi példa egy ARM-sablont használ egy Windows Server 2016 Datacenter-lemezképpel:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Azt is megtudhatja, hogyan [módosíthatja a virtuálisgép-méretezési csoportokat](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) a méretezési csoport frissítésének további módjaira.

## <a name="next-steps"></a>Következő lépések
- További információ arról, [Hogyan lehet pénzt megtakarítani a Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- További információ a [Azure Hybrid Benefit gyakori kérdéseiről](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- További információ a [Windows Server licencelésének részletes útmutatója Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit)
- Tudjon meg többet a [Windows Server Azure Hybrid Benefitéről, és Azure site Recovery az alkalmazások áttelepítése az Azure-ba még](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/) költséghatékonyabb
- További információ az Azure-beli Windows 10-ről a több- [bérlős üzemeltetési jogosultsággal](./windows-desktop-multitenant-hosting-deployment.md)
- További információ a [Resource Manager-sablonok használatáról](../../azure-resource-manager/management/overview.md)
