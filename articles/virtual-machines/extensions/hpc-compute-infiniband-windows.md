---
title: InfiniBand-illesztőprogram kiterjesztése – Azure Windows rendszerű virtuális gépek
description: Microsoft Azure bővítmény a InfiniBand-illesztőprogramok a Windows rendszerű H-és N-sorozatú virtuális gépeken való telepítéséhez.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: amverma
ms.openlocfilehash: 767d6da7701261836b367ccad121bf3569b43b72
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260163"
---
# <a name="infiniband-driver-extension-for-windows"></a>A Windows InfiniBand-illesztőprogram-bővítménye

Ez a bővítmény telepíti a InfiniBand ND-illesztőprogramokat (nem SR-IOV engedélyezve) és OFED-illesztőprogramokat (SR-IOV-enabled esetén) (r-méretek) [H-sorozatú](../sizes-hpc.md) és [N sorozatú](../sizes-gpu.md) , Windows rendszerű virtuális gépeket. A virtuálisgép-családtól függően a bővítmény telepíti a megfelelő illesztőprogramokat a csatlakozási-X hálózati adapterhez.

A Linux rendszerű [virtuális gépekhez](hpc-compute-infiniband-linux.md)InfiniBand-illesztőprogramok is telepíthetők.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő operációsrendszer-disztribúciókat támogatja, az adott operációsrendszer-verzióhoz tartozó illesztőprogram-támogatástól függően. Jegyezze fel a megfelelő InfiniBand hálózati adaptert a H és N sorozatú VM-méretekhez.

| Disztribúció | InfiniBand NIC-illesztőprogramok |
|---|---|
| Windows 10 | CX5, CX6 |
| Windows Server 2019 | CX5, CX6 |
| Windows Server 2016 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 R2 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 | CX3-Pro, CX5, CX6 |

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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
| típus | InfiniBandDriverWindows | sztring |
| typeHandlerVersion | 1.2 | int |



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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Bővítmény hozzáadása virtuálisgép-méretezési csoporthoz

A következő példa telepíti a legújabb 1,2 InfiniBandDriverWindows-bővítményt az összes RDMA-kompatibilis virtuális gépen egy *myVMSS* nevű, meglévő virtuálisgép-méretezési csoportban, amely a *myResourceGroup* nevű erőforráscsoporthoz van telepítve:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
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

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Kilépési kódok

A következő táblázat a bővítmény telepítési folyamatának kilépési kódjai alapján ismerteti a jelentését és a javasolt műveleteket.

| Hibakód | Értelmezés | Lehetséges művelet |
| :---: | --- | --- |
| 0 | Sikeres művelet |
| 3010 | A művelet sikeresen befejeződött. Újraindítás szükséges. |
| 100 | A művelet nem támogatott, vagy nem hajtható végre. | Lehetséges okok: a PowerShell verziója nem támogatott, a virtuális gép mérete nem InfiniBand-kompatibilis virtuális gép, az adatletöltés meghiúsul. A hibák okának megállapításához ellenőrizze a naplófájlokat. |
| 240, 840 | Művelet időtúllépése. | Újrapróbálkozás művelet. |
| -1 | Kivétel történt. | Ellenőrizze a naplófájlokat a kivétel okának meghatározásához. |

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. Másik lehetőségként az [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/)is bejelentkezhet támogatási incidensek. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések
További információ az InfiniBand-kompatibilis ("r" méretekről): [H-sorozatú](../sizes-hpc.md) és [N sorozatú](../sizes-gpu.md) virtuális gépek.

> [!div class="nextstepaction"]
> [További információ a linuxos virtuális gépek bővítményeiről és funkcióiról](features-linux.md)
