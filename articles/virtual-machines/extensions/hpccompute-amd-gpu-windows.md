---
title: AMD GPU illesztőprogram-bővítmény – Azure Windows rendszerű virtuális gépek
description: Microsoft Azure bővítmény az AMD GPU-illesztőprogramok telepítéséhez Windows rendszerű NVv4-sorozatú virtuális gépeken.
services: virtual-machines-windows
author: vikancha-MSFT
manager: jkabat
ms.topic: article
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: 42dac9edc91d7cb935e8c20398c4d31343b358e1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559681"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>AMD GPU illesztőprogram-bővítmény Windows rendszerhez

Ez a cikk áttekintést nyújt az AMD GPU-illesztőprogramok Windows [NVv4-sorozatú](../nvv4-series.md) virtuális gépeken való üzembe helyezéséhez szükséges virtuálisgép-bővítményről. Ha ezt a bővítményt használó AMD-illesztőprogramokat telepít, elfogadja és elfogadja az [amd End-User licencszerződés](https://amd.com/radeonsoftwarems)feltételeit. A telepítési folyamat során a virtuális gép újraindulhat az illesztőprogram telepítésének befejezéséhez.

Az illesztőprogramok manuális telepítésére és az aktuálisan támogatott verziókra vonatkozó utasítások [itt](../windows/n-series-amd-driver-setup.md)érhetők el.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő OSs-t támogatja:

| Disztribúció | Verzió |
|---|---|
| Windows 10 EMS | Build 1903 |
| Windows 10 | Build 1809 |
| Windows Server 2016 | Mag |
| Windows Server 2019 | Mag |

### <a name="internet-connectivity"></a>Internetkapcsolat

Az AMD GPU-illesztőprogramok Microsoft Azure kiterjesztése megköveteli, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez, és hozzáférhessen.

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
| típus | AmdGpuDriverWindows | sztring |
| typeHandlerVersion | 1.0 | int |


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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
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

A bővítmény végrehajtásának kimenete a következő könyvtárba van naplózva:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.AmdGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Hibakódok

| Hibakód | Értelmezés | Lehetséges művelet |
| :---: | --- | --- |
| 0 | Sikeres művelet |
| 1 | A művelet sikeresen befejeződött. Újraindítás szükséges. |
| 100 | A művelet nem támogatott, vagy nem hajtható végre. | Lehetséges okok: a PowerShell verziója nem támogatott, a virtuális gép mérete nem N sorozatú virtuális gép, az adatletöltés meghiúsul. A hibák okának megállapításához ellenőrizze a naplófájlokat. |
| 240, 840 | Művelet időtúllépése. | Újrapróbálkozás művelet. |
| -1 | Kivétel történt. | Ellenőrizze a naplófájlokat a kivétel okának meghatározásához. |
| -5x | A művelet a függőben lévő újraindítás miatt megszakadt. | Indítsa újra a virtuális gépet. A telepítés az újraindítás után is folytatódni fog. Az eltávolítást manuálisan kell meghívni. |


### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések
További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](features-windows.md).

Az N sorozatú virtuális gépekkel kapcsolatos további információkért lásd: GPU-ra [optimalizált virtuálisgép-méretek](../sizes-gpu.md).
