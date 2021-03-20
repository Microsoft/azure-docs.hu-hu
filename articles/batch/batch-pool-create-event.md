---
title: Azure Batch készlet létrehozási eseménye
description: A Batch-készlet létrehozási eseményének hivatkozása, amely a készlet létrehozása után lett kibocsátva. A napló tartalma a készlet általános információit fogja közzétenni.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: f8b020401443b2a9e80837599d6fd4b2a3001d27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97609003"
---
# <a name="pool-create-event"></a>Készlet létrehozása esemény

 Ezt az eseményt a rendszer a készlet létrehozása után bocsátja ki. A napló tartalma a készlet általános információit fogja közzétenni. Vegye figyelembe, hogy ha a készlet célként megadott mérete meghaladja a 0 számítási csomópontot, akkor a készlet átméretezése az esemény után azonnal megkezdődik.

 Az alábbi példa egy készlet létrehozási eseményét mutatja be a tulajdonság használatával létrehozott készlethez `CloudServiceConfiguration` .

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elem|Típus|Jegyzetek|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`displayName`|Sztring|A készlet megjelenítendő neve.|
|`vmSize`|Sztring|A készletben lévő virtuális gépek mérete. A készletben lévő összes virtuális gép mérete azonos. <br/><br/> További információ a Cloud Services készletekhez (cloudServiceConfiguration létrehozott készletek) elérhető virtuális gépek rendelkezésre álló méretéről: [Cloud Services méretei](../cloud-services/cloud-services-sizes-specs.md). A Batch az összes Cloud Services VM-méretet támogatja, a kivételével `ExtraSmall` .<br/><br/> További információ a Virtual Machines piactérről származó rendszerképeket használó készletek számára elérhető virtuálisgép-méretekről (virtualMachineConfiguration létrehozott készletek) a [Virtual Machines](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) vagy a Virtual Machines (Windows) [méreteit](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ismertető témakörben talál. A Batch a `STANDARD_A0`, illetve a Premium Storage típusú méretek (`STANDARD_GS`, `STANDARD_DS` és `STANDARD_DSV2` sorozat) kivételével az összes Azure virtuálisgép-méretet támogatja.|
|`imageType`|Sztring|A rendszerkép központi telepítési módszere. A támogatott értékek a következők, `virtualMachineConfiguration` vagy `cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Összetett típus|A készlet Cloud Service-konfigurációja.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Összetett típus|A készlet virtuálisgép-konfigurációja.|
|[`networkConfiguration`](#bk_netconf)|Összetett típus|A készlet hálózati konfigurációja.|
|`resizeTimeout`|Idő|A számítási csomópontok kiosztásának időtúllépése a készlet utolsó átméretezési műveletéhez megadott készletre.  (A készlet létrehozásakor a kezdeti méretezés átméretezésnek számít.)|
|`targetDedicatedNodes`|Int32|A készlethez igényelt dedikált számítási csomópontok száma.|
|`targetLowPriorityNodes`|Int32|A készlethez igényelt alacsony prioritású számítási csomópontok száma.|
|`enableAutoScale`|Logikai|Meghatározza, hogy a készlet mérete automatikusan igazodik-e az idő múlásával.|
|`enableInterNodeCommunication`|Logikai|Megadja, hogy a készlet be van-e állítva a csomópontok közötti közvetlen kommunikációhoz.|
|`isAutoPool`|Logikai|Azt határozza meg, hogy a készlet a feladatok autopool mechanizmusán keresztül lett-e létrehozva.|
|`taskSlotsPerNode`|Int32|A készlet egyetlen számítási csomópontján egyszerre futtatható feladatok maximális száma.|
|`vmFillType`|Sztring|Azt határozza meg, hogyan osztja el a Batch szolgáltatás a feladatokat a készlet számítási csomópontjai között. Az érvényes értékek: spread vagy Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a> cloudServiceConfiguration

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`osFamily`|Sztring|A készletben lévő virtuális gépekre telepítendő Azure vendég operációsrendszer-család.<br /><br /> Lehetséges értékek:<br /><br /> **2** – operációsrendszer-család 2, a Windows Server 2008 R2 SP1-gyel egyenértékű.<br /><br /> **3** – operációsrendszer-család 3, a Windows Server 2012-rel egyenértékű.<br /><br /> **4** – operációsrendszer-család 4, a Windows Server 2012 R2-vel egyenértékű.<br /><br /> További információ: [Azure vendég operációs rendszer kiadásai](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|
|`targetOSVersion`|Sztring|A készletben lévő virtuális gépekre telepítendő Azure vendég operációs rendszer verziója.<br /><br /> Az alapértelmezett érték az, **\*** amely az operációs rendszer legújabb verzióját adja meg a megadott családhoz.<br /><br /> Más megengedett értékekért lásd: az [Azure vendég operációs rendszer kiadásai](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a> virtualMachineConfiguration

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Összetett típus|A használni kívánt platform vagy Marketplace-rendszerkép adatait adja meg.|
|`nodeAgentId`|Sztring|A számítási csomóponton kiépített batch-csomóponti ügynök SKU-a.|
|[`windowsConfiguration`](#bk_winconf)|Összetett típus|Megadja a Windows operációs rendszer beállításait a virtuális gépen. Ez a tulajdonság nem adható meg, ha a imageReference egy Linux operációs rendszer rendszerképére hivatkozik.|

###  <a name="imagereference"></a><a name="bk_imgref"></a> imageReference

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`publisher`|Sztring|A rendszerkép közzétevője.|
|`offer`|Sztring|A rendszerkép ajánlata.|
|`sku`|Sztring|A rendszerkép SKU-jának.|
|`version`|Sztring|A rendszerkép verziója.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a> windowsConfiguration

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Logikai|Azt jelzi, hogy a virtuális gép engedélyezve van-e az automatikus frissítésekhez. Ha ez a tulajdonság nincs megadva, az alapértelmezett érték TRUE (igaz).|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a> networkConfiguration

|Elem neve|Típus|Jegyzetek|
|------------------|--------------|----------|
|`subnetId`|Sztring|Meghatározza annak az alhálózatnak az erőforrás-azonosítóját, amelyben a készlet számítási csomópontjait létrehozták.|