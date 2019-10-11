---
title: Azure-beli virtuálisgép-méretezési csoport példányaira vonatkozó értesítés leállítása | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti az Azure-beli virtuálisgép-méretezési csoport példányainak megszüntetési értesítését
services: virtual-machine-scale-sets
documentationcenter: ''
author: shandilvarun
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2019
ms.author: vashan
ms.openlocfilehash: 7269c76236b7cbe60995d84e85857da596bec961
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264680"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances-preview"></a>Értesítés megszakítása az Azure virtuálisgép-méretezési csoport példányaihoz (előzetes verzió)
A méretezési csoport példányai beállíthatják a példányok leállítási értesítéseinek fogadását, és előre definiált késleltetési időkorlátot állíthatnak be a megszakítási művelethez. A lemondási értesítést az Azure Metadata Service – [Scheduled Events](../virtual-machines/windows/scheduled-events.md)küldi el, amely értesítések küldését és késleltetését teszi lehetővé, például újraindítást és újbóli üzembe helyezést. Az előzetes verziójú megoldás egy újabb eseményt ad – leáll – a Scheduled Events listájához, a megszakítási eseményhez kapcsolódó késés pedig a méretezési csoport modelljének felhasználói által megadott késleltetési korláttól függ.

A szolgáltatásba való regisztrálás után a méretezési csoport példányainak nem kell megvárniuk a megadott időtúllépés érvényességét a példány törlése előtt. A lemondási értesítés kézhezvétele után a példány bármikor törölhető, mielőtt lejár a leállítási időkorlát.

> [!IMPORTANT]
> A méretezési csoport példányaihoz tartozó értesítés megszakítása jelenleg nyilvános előzetes verzióban érhető el. Az alábbiakban ismertetett nyilvános előzetes funkciók használatához nincs szükség beavatkozásra.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-terminate-notifications"></a>Értesítések megszakításának engedélyezése
Több módon is engedélyezheti a leállítási értesítéseket a méretezési csoport példányain, az alábbi példákban részletezve.

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

A fenti blokk a méretezési csoport összes példányán a leállítási művelethez 5 perces időkorlát-késleltetést határoz meg (a *PT5M*jelzi). A *notBeforeTimeout* mező értéke 5 – 15 percet vesz igénybe ISO 8601 formátumban. A leállítási művelet alapértelmezett időtúllépését a fent leírt *TerminateNotificationProfile* *notBeforeTimeout* tulajdonságának módosításával módosíthatja.

Miután engedélyezte a *scheduledEventsProfile* a méretezési csoport modelljén, és beállítja a *notBeforeTimeout*, frissítse az egyes példányokat a [legújabb modellre](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , hogy tükrözze a módosításokat.

> [!NOTE]
>A méretezési csoport példányain lévő értesítések megszakítása csak a 2019-03-01-es vagy újabb API-verzióval engedélyezhető

### <a name="azure-powershell"></a>Azure PowerShell
Új méretezési csoport létrehozásakor engedélyezheti a leállítási értesítéseket a méretezési csoporton a [New-AzVmssVM](/powershell/module/az.compute/new-azvmss) parancsmag használatával.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents
```

A fenti példában egy új méretezési csoport jön létre, amely egy 5 perces alapértelmezett időkorláttal rendelkező megszakítási értesítésekkel rendelkezik. Új méretezési csoport létrehozásakor a *TerminateScheduledEvents* paraméter nem igényel értéket. Az időtúllépési érték módosításához a *TerminateScheduledEventNotBeforeTimeoutInMinutes* paraméterrel adhatja meg a kívánt időkorlátot.

Az [Update-AzVmssVM](/powershell/module/az.compute/update-azvmss) parancsmag használatával engedélyezheti a megszakítási értesítéseket egy meglévő méretezési csoporton.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
A fenti példa lehetővé teszi az értesítések leállítását egy meglévő méretezési csoporton, és 15 perces időkorlátot állít be a megszakítási eseményhez.

Miután engedélyezte az ütemezett eseményeket a méretezési csoport modelljén, és beállítja az időtúllépést, frissítse az egyes példányokat a [legújabb modellre](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , hogy tükrözze a módosításokat.

## <a name="get-terminate-notifications"></a>Értesítések megszakítása

A leállítási értesítések kézbesítése [Scheduled Eventson](../virtual-machines/windows/scheduled-events.md)keresztül történik, amely egy Azure-metadata Service. Az Azure metaadat-szolgáltatás a virtuális gépről elérhető REST-végpont használatával teszi elérhetővé Virtual Machines futtatásával kapcsolatos információkat. Az információk egy nem irányítható IP-címen keresztül érhetők el, hogy ne legyenek kitéve a virtuális gépen kívül.

Az Scheduled Events engedélyezve van a méretezési csoport számára, amikor első alkalommal hoz létre eseményeket. A késleltetett választ az első hívásában akár két percet is igénybe vehet. Rendszeres időközönként lekérdezheti a végpontot a közelgő karbantartási események és a folyamatban lévő karbantartási tevékenységek állapotának észlelése érdekében.

Scheduled Events le van tiltva a méretezési csoport esetében, ha a méretezési csoport példányai nem végeznek 24 órán át kérelmet.

### <a name="endpoint-discovery"></a>Végpont felderítése
A VNET-kompatibilis virtuális gépek esetében a Metadata Service statikus, nem irányítható IP-címről, 169.254.169.254 érhető el.

Az előzetes verzióhoz tartozó Scheduled Events legújabb verziójának teljes végpontja:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

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
A DocumentIncarnation egy ETag, és egyszerűen megvizsgálhatja, hogy az események tartalma módosult-e az utolsó lekérdezés óta.

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

A [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) és a [Python](../virtual-machines/linux/scheduled-events.md#python-sample)használatával is megtekintheti az eseményekre vonatkozó lekérdezési és válaszadási parancsfájlokat.

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások
-   Értesítések megszakítása csak a "Delete" műveletekben – az összes törlési művelet (manuális törlés vagy automatikus méretezés által kezdeményezett skálázás) a megszakítási eseményeket eredményezi, ha a méretezési csoport *scheduledEventsProfile* engedélyezve van. Más műveletek, például az újraindítás, az áttelepítés, az ismételt üzembe helyezés és a Leállítás/felszabadítás nem eredményeznek megszakítási eseményeket. Az alacsony prioritású virtuális gépek esetében nem engedélyezhető az értesítések megszakítása.
-   Nincs kötelező várakozás az időtúllépésre – a megszakítási műveletet bármikor elindíthatja az esemény kézhezvétele után, és az esemény *NotBefore* idő lejárta előtt.
-   Kötelező törlés időkorlátnál – az előzetes verzió nem nyújt lehetőséget az időtúllépési érték kiterjesztésére egy esemény létrehozása után. Az időtúllépés lejárta után a rendszer feldolgozza a függőben lévő megszakítási eseményt, és törli a virtuális gépet.
-   Módosítható időtúllépési érték – a példány törlése előtt bármikor módosíthatja az időtúllépési értéket, ha módosítja a *notBeforeTimeout* tulajdonságot a méretezési csoport modelljében, és frissíti a virtuálisgép-példányokat a legújabb modellre.
-   Az összes függőben lévő törlés jóváhagyása – ha van olyan függőben lévő törlés a VM_1, amely nincs jóváhagyva, és Ön jóváhagyta egy másik megszakítási eseményt a VM_2, akkor a VM_2 nem törlődik, amíg meg nem történik a VM_1 megszakítási eseménye, vagy az időtúllépése eltelt. Ha jóváhagyta a VM_1 megszakítási eseményét, akkor a VM_1 és a VM_2 is törlődik.
-   Az összes egyidejű törlés jóváhagyása – a fenti példa kibővítésével, ha a VM_1 és a VM_2 ugyanazzal a *NotBefore* -idővel rendelkezik, akkor mindkét megszakítási eseményt jóvá kell hagyni, vagy az időtúllépés lejárta előtt sem kell a virtuális gépet törölni.

## <a name="troubleshoot"></a>Hibaelhárítás
### <a name="failure-to-enable-scheduledeventsprofile"></a>Nem sikerült engedélyezni a scheduledEventsProfile
Ha "BadRequest" hibaüzenet jelenik meg, amely azt jelzi, hogy a "VirtualMachineProfile" típusú objektumon nem található "scheduledEventsProfile" tag, ellenőrizze a méretezési csoport műveleteihez használt API-verziót. Ehhez az előzetes verzióhoz a számítási API **2019-03-01** -es vagy újabb verziója szükséges.

### <a name="failure-to-get-terminate-events"></a>Nem sikerült beolvasni az eseményeket
Ha nem kap **megszakítási** eseményt a Scheduled Eventson keresztül, akkor ellenőrizze az események beolvasásához használt API-verziót. Az események megszakításához Metadata Service API **2019-01-01** -es vagy újabb verziójára van szükség.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Az esemény megszakítása helytelen NotBefore idővel  
Miután engedélyezte a *scheduledEventsProfile* a méretezési csoport modelljén, és beállítja a *notBeforeTimeout*, frissítse az egyes példányokat a [legújabb modellre](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , hogy tükrözze a módosításokat.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [helyezheti üzembe az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportokban.
