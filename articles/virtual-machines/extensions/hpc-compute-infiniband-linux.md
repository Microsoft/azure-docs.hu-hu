---
title: InfiniBand-illesztőprogram kiterjesztése – Azure Linux rendszerű virtuális gépek
description: Microsoft Azure bővítmény a InfiniBand-illesztőprogramok telepítéséhez a H-és N-sorozatú, Linux rendszerű virtuális gépeken.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 1219f5befb4de81850681a53c10f03dc81249d53
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559732"
---
# <a name="infiniband-driver-extension-for-linux"></a>InfiniBand-illesztőprogram-bővítmény Linux rendszerhez

Ez a bővítmény telepíti a InfiniBand OFED-illesztőprogramokat a InfiniBand és az SR-IOV-Enabled ("r" méretek) [H-sorozat](../sizes-hpc.md) és a Linux rendszerű [N sorozatú](../sizes-gpu.md) virtuális gépek számára. A virtuálisgép-családtól függően a bővítmény telepíti a megfelelő illesztőprogramokat a csatlakozási-X hálózati adapterhez.

A OFED-illesztőprogramok manuális telepítésére vonatkozó utasítások [itt](../workloads/hpc/enable-infiniband.md#manual-installation)érhetők el.

A Windows rendszerű [virtuális gépek](hpc-compute-infiniband-windows.md)InfiniBand-illesztőprogramjainak telepítéséhez egy bővítmény is elérhető.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő operációsrendszer-disztribúciókat támogatja, az adott operációsrendszer-verzióhoz tartozó illesztőprogram-támogatástól függően.

| Disztribúció | Verzió |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS, 20,04 LTS |
| Linux: CentOS | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |

### <a name="internet-connectivity"></a>Internetkapcsolat

A InfiniBand-illesztőprogramok Microsoft Azure bővítményéhez a cél virtuális gépnek csatlakoznia kell, és hozzáféréssel kell rendelkeznie az internethez.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a bővítmény sémáját jeleníti meg.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Tulajdonságok

| Name | Érték/példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| közzétevő | Microsoft. HpcCompute | sztring |
| típus | InfiniBandDriverLinux | sztring |
| typeHandlerVersion | 1.1 | int |



## <a name="deployment"></a>Üzembe helyezés


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon 

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek a telepítés utáni konfigurációt igénylik.

A virtuálisgép-bővítmény JSON-konfigurációja beágyazható a virtuális gép erőforrásaiba, vagy egy Resource Manager JSON-sablon legfelső szintű vagy legfelső szintjén helyezhető el. A JSON-konfiguráció elhelyezése hatással van az erőforrás nevének és típusának értékére. További információ: [a gyermek erőforrások nevének és típusának beállítása](../../azure-resource-manager/templates/child-resource-name-type.md). 

Az alábbi példa azt feltételezi, hogy a bővítmény a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrásának beágyazásakor a JSON a `"resources": []` virtuális gép objektumára kerül.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Bővítmény hozzáadása virtuálisgép-méretezési csoporthoz

A következő példa telepíti a legújabb 1,1 InfiniBandDriverLinux-bővítményt az összes RDMA-kompatibilis virtuális gépen egy *myVMSS* nevű, meglévő virtuálisgép-méretezési csoportban, amely a *myResourceGroup* nevű erőforráscsoporthoz van telepítve:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból, valamint a Azure PowerShell és az Azure CLI használatával. Egy adott virtuális gép bővítményeinek központi telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény végrehajtásának kimenete a következő fájlba lesz naplózva. Tekintse át ezt a fájlt a telepítés állapotának nyomon követéséhez, valamint a hibák elhárításához.

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Kilépési kódok

A következő táblázat a bővítmény telepítési folyamatának kilépési kódjai alapján ismerteti a jelentését és a javasolt műveleteket.

| Kilépési kód | Értelmezés | Lehetséges művelet |
| :---: | --- | --- |
| 0 | Sikeres művelet |
| 1 | A bővítmény helytelen használata | Végrehajtás kimeneti naplójának keresése |
| 10 | A Hyper-V és az Azure Linux-integrációs szolgáltatása nem érhető el vagy nincs telepítve | A lspci kimenetének ellenõrzése |
| 11 | A Mellanox InfiniBand nem található a virtuális gép méretében | [Támogatott VM-méret és operációs rendszer](../sizes-hpc.md) használata |
| 12 | A rendszerkép-ajánlat nem támogatott |
| 13 | A virtuális gép mérete nem támogatott | InfiniBand-kompatibilis ("r" méretű) [H-sorozat](../sizes-hpc.md) és [n sorozatú](../sizes-gpu.md)n sorozatú virtuális gép üzembe helyezése |
| 14 | Sikertelen művelet | Végrehajtás kimeneti naplójának keresése |


### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. Másik lehetőségként az [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/)is bejelentkezhet támogatási incidensek. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések
További információ az InfiniBand-kompatibilis ("r" méretekről): [H-sorozatú](../sizes-hpc.md) és [N sorozatú](../sizes-gpu.md) virtuális gépek.

> [!div class="nextstepaction"]
> [További információ a linuxos virtuális gépek bővítményeiről és funkcióiról](features-linux.md)