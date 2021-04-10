---
title: NVIDIA GPU illesztőprogram-bővítmény – Azure Linux rendszerű virtuális gépek
description: Microsoft Azure bővítmény NVIDIA GPU-illesztőprogramok telepítéséhez Linux rendszeren futó N-sorozatú számítási virtuális gépeken.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: fa2b82f3246fd87830010f572ba23aa2df075053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566226"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU illesztőprogram-bővítmény Linux rendszerhez

## <a name="overview"></a>Áttekintés

Ez a bővítmény az NVIDIA GPU-illesztőprogramokat telepíti a Linux N sorozatú virtuális gépeken. A virtuálisgép-családtól függően a bővítmény a CUDA vagy a GRID-illesztőprogramokat telepíti. Ha ezt a bővítményt használja az NVIDIA-illesztőprogramok telepítésekor, elfogadja és elfogadja az [nvidia End-User licencszerződés](https://go.microsoft.com/fwlink/?linkid=874330)feltételeit. A telepítési folyamat során a virtuális gép újraindulhat az illesztőprogram telepítésének befejezéséhez.

Az illesztőprogramok manuális telepítésére és az aktuálisan támogatott verziókra vonatkozó utasítások [itt](../linux/n-series-driver-setup.md)érhetők el.
A bővítmény az NVIDIA GPU [-illesztőprogramok Windows N sorozatú virtuális gépeken](hpccompute-gpu-windows.md)történő telepítéséhez is elérhető.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő operációsrendszer-disztribúciókat támogatja, az adott operációsrendszer-verzióhoz tartozó illesztőprogram-támogatástól függően.

| Disztribúció | Verzió |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |
| Linux: CentOS | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |

### <a name="internet-connectivity"></a>Internetkapcsolat

Az NVIDIA GPU-illesztőprogramok Microsoft Azure kiterjesztése megköveteli, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez, és hozzáférhessen.

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
| típus | NvidiaGpuDriverLinux | sztring |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>Beállítások

Az összes beállítás megadása nem kötelező. Az alapértelmezett viselkedés nem frissíti a rendszermagot, ha az illesztőprogram telepítéséhez nem szükséges, telepítse a legújabb támogatott illesztőprogramot és a CUDA-eszközkészletet (a megfelelő módon).

| Név | Leírás | Alapértelmezett érték | Érvényes értékek | Adattípus |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | A kernel frissítése akkor is, ha az illesztőprogram telepítése nem szükséges | hamis | igaz, hamis | boolean |
| driverVersion | NV: GRID-illesztőprogram verziója<br> NC/ND: CUDA Toolkit verziója. A kiválasztott CUDA legújabb illesztőprogramjai automatikusan települnek. | legújabb | A támogatott illesztőprogram-verziók [listája](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) | sztring |
| installCUDA | Telepítse a CUDA eszközkészletet. Csak az NC/ND sorozatú virtuális gépek esetében fontos. | true | igaz, hamis | boolean |


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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

Az alábbi példa a fenti Azure Resource Manager és PowerShell-példákat tükrözi.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

Az alábbi példa két opcionális egyéni beállítást is feltesz példaként a nem alapértelmezett illesztőprogram-telepítéshez. Pontosabban frissíti az operációs rendszer kernelét a legújabb verzióra, és telepíti az adott CUDA Toolkit-illesztőprogramját. Ismét figyelje meg, hogy a "--Settings" nem kötelező és az alapértelmezett. Vegye figyelembe, hogy a kernel frissítése növelheti a bővítmény telepítési időpontját. Ha egy adott (régebbi) CUDA tolkit-verziót is választ, előfordulhat, hogy nem minden esetben kompatibilis az újabb kernelekkel.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
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

A bővítmény végrehajtásának kimenete a következő fájlba lesz naplózva. Tekintse át ezt a fájlt, hogy nyomon követhesse a (bármilyen hosszan futó) telepítés állapotát, valamint a hibák elhárítását.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kilépési kódok

| Kilépési kód | Értelmezés | Lehetséges művelet |
| :---: | --- | --- |
| 0 | Sikeres művelet |
| 1 | A bővítmény helytelen használata | Végrehajtás kimeneti naplójának keresése |
| 10 | A Hyper-V és az Azure Linux-integrációs szolgáltatása nem érhető el vagy nincs telepítve | A lspci kimenetének ellenõrzése |
| 11 | Az NVIDIA GPU nem található a virtuális gép méretén | [Támogatott VM-méret és operációs rendszer](../linux/n-series-driver-setup.md) használata |
| 12 | A rendszerkép-ajánlat nem támogatott |
| 13 | A virtuális gép mérete nem támogatott | Az N sorozatú virtuális gépek használata az üzembe helyezéshez |
| 14 | Sikertelen művelet | Végrehajtás kimeneti naplójának keresése |


### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések
További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).

Az N sorozatú virtuális gépekkel kapcsolatos további információkért lásd: GPU-ra [optimalizált virtuálisgép-méretek](../sizes-gpu.md).
