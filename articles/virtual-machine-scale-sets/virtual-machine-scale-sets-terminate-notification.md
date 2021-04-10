---
title: Azure-beli virtuális gépek méretezésicsoport-példányainak megszüntetési értesítése
description: Megtudhatja, hogyan engedélyezheti az Azure-beli virtuálisgép-méretezési csoport példányainak megszüntetési értesítését
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: terminate-notification
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ed042afbcbb67a88e304c92302b14af56b26c8e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933406"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Azure-beli virtuális gépek méretezésicsoport-példányainak megszüntetési értesítése
A méretezési csoport példányai beállíthatják a példányok leállítási értesítéseinek fogadását, és előre definiált késleltetési időkorlátot állíthatnak be a megszakítási művelethez. A lemondási értesítést az Azure Metadata Service – [Scheduled Events](../virtual-machines/windows/scheduled-events.md)küldi el, amely értesítések küldését és késleltetését teszi lehetővé, például újraindítást és újbóli üzembe helyezést. A megoldás egy újabb eseményt ad – leáll – a Scheduled Events listájához, a megszakítási eseményhez kapcsolódó késés pedig a méretezési csoport modelljének felhasználói által meghatározott késési korláttól függ.

A szolgáltatásba való regisztrálás után a méretezési csoport példányainak nem kell megvárniuk a megadott időtúllépés érvényességét a példány törlése előtt. A lemondási értesítés kézhezvétele után a példány bármikor törölhető, mielőtt lejár a leállítási időkorlát.

## <a name="enable-terminate-notifications"></a>Értesítések megszakításának engedélyezése
Több módon is engedélyezheti a leállítási értesítéseket a méretezési csoport példányain, az alábbi példákban részletezve.

### <a name="azure-portal"></a>Azure Portal

A következő lépések lehetővé teszik az új méretezési csoport létrehozásakor az értesítés megszüntetését. 

1. Nyissa meg a **virtuálisgép-méretezési csoportokat**.
1. Válassza a **+ Hozzáadás** lehetőséget egy új méretezési csoport létrehozásához.
1. Nyissa meg a **felügyelet** lapot. 
1. Keresse meg a **példány lezárása** szakaszt.
1. A **példányok** leállításáról szóló értesítés esetén válassza **a be** lehetőséget.
1. A **megszakítási késleltetés (perc)** beállításnál állítsa be a kívánt alapértelmezett időkorlátot.
1. Ha elkészült az új méretezési csoport létrehozásával, válassza a **felülvizsgálat + létrehozás** gombot. 

> [!NOTE]
> Nem lehet beállítani a meglévő méretezési csoportokra vonatkozó értesítéseket Azure Portal

### <a name="rest-api"></a>REST API

A következő példa a méretezési csoport modelljében az értesítés megszüntetését teszi lehetővé.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

A fenti blokk a méretezési csoport összes példányán a leállítási művelethez 5 perces időkorlát-késleltetést határoz meg (a *PT5M* jelzi). A *notBeforeTimeout* mező értéke 5 – 15 percet vesz igénybe ISO 8601 formátumban. A leállítási művelet alapértelmezett időtúllépését a fent leírt *TerminateNotificationProfile* *notBeforeTimeout* tulajdonságának módosításával módosíthatja.

Miután engedélyezte a *scheduledEventsProfile* a méretezési csoport modelljén, és beállítja a *notBeforeTimeout*, frissítse az egyes példányokat a [legújabb modellre](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , hogy tükrözze a módosításokat.

> [!NOTE]
>A méretezési csoport példányain lévő értesítések megszakítása csak a 2019-03-01-es vagy újabb API-verzióval engedélyezhető

### <a name="azure-powershell"></a>Azure PowerShell
Új méretezési csoport létrehozásakor engedélyezheti a leállítási értesítéseket a méretezési csoporton a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmag használatával.

Ez a minta parancsfájl végigvezeti a méretezési csoport és a kapcsolódó erőforrások létrehozásán a konfigurációs fájl használatával: [hozzon létre egy teljes virtuálisgép-méretezési készletet](./scripts/powershell-sample-create-complete-scale-set.md). Megadhatja a konfigurálás leállítására vonatkozó értesítést úgy, hogy hozzáadja a *TerminateScheduledEvents* és a *TerminateScheduledEventNotBeforeTimeoutInMinutes* paramétert a méretezési csoport létrehozásához a konfigurációs objektumhoz. A következő példában a szolgáltatás 10 perces késleltetési időkorlátot engedélyez.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag használatával engedélyezheti a megszakítási értesítéseket egy meglévő méretezési csoporton.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
A fenti példa lehetővé teszi az értesítések leállítását egy meglévő méretezési csoporton, és 15 perces időkorlátot állít be a megszakítási eseményhez.

Miután engedélyezte az ütemezett eseményeket a méretezési csoport modelljén, és beállítja az időtúllépést, frissítse az egyes példányokat a [legújabb modellre](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , hogy tükrözze a módosításokat.

### <a name="azure-cli-20"></a>Azure CLI 2.0

A következő példa egy új méretezési csoport létrehozásakor felmondási értesítés engedélyezésére szolgál.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

A fenti példában a fenti példa létrehoz egy erőforráscsoportot, majd egy új méretezési csoportot hoz létre, amelyben a megszakítási értesítések engedélyezve vannak egy 10 perces alapértelmezett időkorlát esetén.

A következő példa a lemondási értesítések engedélyezését szemlélteti egy meglévő méretezési csoporton belül.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Értesítések megszakítása

A leállítási értesítések kézbesítése [Scheduled Eventson](../virtual-machines/windows/scheduled-events.md)keresztül történik, amely egy Azure-metadata Service. Az Azure metaadat-szolgáltatás a virtuális gépről elérhető REST-végpont használatával teszi elérhetővé Virtual Machines futtatásával kapcsolatos információkat. Az információk egy nem irányítható IP-címen keresztül érhetők el, hogy ne legyenek kitéve a virtuális gépen kívül.

Az Scheduled Events engedélyezve van a méretezési csoport számára, amikor első alkalommal hoz létre eseményeket. A késleltetett választ az első hívásában akár két percet is igénybe vehet. Rendszeres időközönként lekérdezheti a végpontot a közelgő karbantartási események és a folyamatban lévő karbantartási tevékenységek állapotának észlelése érdekében.

Scheduled Events le van tiltva a méretezési csoport esetében, ha a méretezési csoport példányai nem végeznek 24 órán át kérelmet.

### <a name="endpoint-discovery"></a>Végpont felderítése
A VNET-kompatibilis virtuális gépek esetében a Metadata Service statikus, nem irányítható IP-címről, 169.254.169.254 érhető el.

A Scheduled Events legújabb verziójának teljes végpontja a következő:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Lekérdezési válasz
A válasz ütemezett események tömbjét tartalmazza. Az üres tömb azt jelenti, hogy jelenleg nincsenek ütemezett események.

Abban az esetben, ha ütemezett események vannak, a válasz események tömbjét tartalmazza. A "leállítási" esemény esetén a válasz a következőképpen fog megjelenni:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
A *DocumentIncarnation* egy ETAG, és egyszerűen megvizsgálhatja, hogy az események tartalma módosult-e az utolsó lekérdezés óta.

A fenti mezőkkel kapcsolatos további információkért tekintse meg a Windows és [Linux](../virtual-machines/linux/scheduled-events.md#event-properties) [rendszerhez](../virtual-machines/windows/scheduled-events.md#event-properties) készült Scheduled Events dokumentációját.

### <a name="respond-to-events"></a>Válaszadás az eseményekre
Miután megismerte a közelgő eseményt, és elvégezte a logika kikapcsolását, jóváhagyhatja a függőben lévő eseményt úgy, hogy a metaadatokat a Napszállta. A POST hívás azt jelzi az Azure-nak, hogy folytathatja a virtuális gép törlését.

Az alábbiakban a POST kérelem törzsében várt JSON szerepel. A kérésnek tartalmaznia kell a StartRequests listáját. Minden StartRequest tartalmazza a felgyorsítani kívánt esemény Napszállta:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Győződjön meg arról, hogy a méretezési csoport minden virtuális gépe csak az adott virtuális géphez tartozó Napszállta hagyja jóvá. A virtuális gépek a [példány metaadatainak használatával](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)kaphatják meg a virtuális gép nevét. Ez a név a (z) "{Scale-set-name} _ {instance-id}" formában jelenik meg, amely a fent ismertetett lekérdezési válasz "Resources" (erőforrások) szakaszában fog megjelenni.

A [Python](../virtual-machines/linux/scheduled-events.md#python-sample)-események lekérdezéséhez és az azokra való reagáláshoz példákat is talál.

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások
-   Értesítések megszakítása csak a "Delete" műveletekben – az összes törlési művelet (manuális törlés vagy automatikus méretezés által kezdeményezett skálázás) a megszakítási eseményeket eredményezi, ha a méretezési csoport *scheduledEventsProfile* engedélyezve van. Más műveletek, például az újraindítás, az áttelepítés, az ismételt üzembe helyezés és a Leállítás/felszabadítás nem eredményeznek megszakítási eseményeket. Az alacsony prioritású virtuális gépek esetében nem engedélyezhető az értesítések megszakítása.
-   Nincs kötelező várakozás az időtúllépésre – a megszakítási műveletet bármikor elindíthatja az esemény kézhezvétele után, és az esemény *NotBefore* idő lejárta előtt.
-   Kötelező törlés időkorlátnál – nincs lehetőség az időtúllépési érték kiterjesztésére egy esemény létrehozása után. Az időtúllépés lejárta után a rendszer feldolgozza a függőben lévő megszakítási eseményt, és törli a virtuális gépet.
-   Módosítható időtúllépési érték – a példány törlése előtt bármikor módosíthatja az időtúllépési értéket, ha módosítja a *notBeforeTimeout* tulajdonságot a méretezési csoport modelljében, és frissíti a virtuálisgép-példányokat a legújabb modellre.
-   Az összes függőben lévő törlés jóváhagyása – ha van függőben lévő törlés a nem jóváhagyott VM_1on, és jóváhagyta egy másik megszakítási eseményt VM_2, akkor VM_2 nem törlődik, amíg a rendszer nem törli az VM_1 megszakítási eseményét, vagy az időtúllépése eltelt. Ha jóváhagyja VM_1 megszakítási eseményét, akkor a VM_1 és a VM_2 is törlődik.
-   Minden egyidejű törlés jóváhagyása – a fenti példát kiterjesztve, ha VM_1 és VM_2 ugyanazzal a *NotBefore* rendelkezik, akkor mindkét megszakítási eseményt jóvá kell hagyni, vagy egyetlen virtuális gépet sem kell törölni az időkorlát lejárta előtt.

## <a name="troubleshoot"></a>Hibaelhárítás
### <a name="failure-to-enable-scheduledeventsprofile"></a>Nem sikerült engedélyezni a scheduledEventsProfile
Ha "BadRequest" hibaüzenet jelenik meg, amely azt jelzi, hogy a "VirtualMachineProfile" típusú objektumon nem található "scheduledEventsProfile" tag, ellenőrizze a méretezési csoport műveleteihez használt API-verziót. A számítási API **2019-03-01** -es vagy újabb verziójára van szükség. 

### <a name="failure-to-get-terminate-events"></a>Nem sikerült beolvasni az eseményeket
Ha nem kap **megszakítási** eseményt a Scheduled Eventson keresztül, akkor ellenőrizze az események beolvasásához használt API-verziót. Az események megszakításához Metadata Service API **2019-01-01** -es vagy újabb verziójára van szükség.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Az esemény megszakítása helytelen NotBefore idővel  
Miután engedélyezte a *scheduledEventsProfile* a méretezési csoport modelljén, és beállítja a *notBeforeTimeout*, frissítse az egyes példányokat a [legújabb modellre](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , hogy tükrözze a módosításokat.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [helyezheti üzembe az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportokban.
