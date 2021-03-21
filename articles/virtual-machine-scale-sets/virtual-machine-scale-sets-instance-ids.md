---
title: Az Azure virtuálisgép-méretezési csoportbeli virtuális gépek példány-azonosítóinak megismerése
description: Az Azure-beli virtuálisgép-méretezési csoportok példány-azonosítóinak megismerése a virtuális gépek és azok felületének különféle módjai.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/22/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: a62c9bbde0726c8dec8fba1f69e221bd4e4b63bc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209849"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Az Azure virtuálisgép-méretezési csoportbeli virtuális gépek példány-azonosítóinak megismerése
Ez a cikk a méretezési csoportokhoz tartozó példány-azonosítókat és a felületek különböző módszereit ismerteti.

## <a name="scale-set-instance-ids"></a>Méretezési csoport példányainak azonosítói

A méretezési csoport minden virtuális gépe egy példány-azonosítót kap, amely egyedileg azonosítja azt. Ez a példány-azonosító a méretezési csoport API-jai között a méretezési csoport egy adott virtuális gépén végzett műveletek végrehajtásához használatos. Például megadhat egy adott példány-azonosítót a rendszerkép alaphelyzetbe állításához a rerendszerkép API használatakor:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/reimage?api-version={apiVersion}` (további tudnivalókért tekintse meg a [REST API dokumentációját](/rest/api/compute/virtualmachinescalesetvms/reimage))

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (további információért lásd a [PowerShell dokumentációját](/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (további információért lásd a [CLI dokumentációját](/cli/azure/vmss)).

A példány-azonosítók listáját a méretezési csoport összes példányának listázásával érheti el:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (további tudnivalókért tekintse meg a [REST API dokumentációját](/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (további információért lásd a [PowerShell dokumentációját](/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (további információért lásd a [CLI dokumentációját](/cli/azure/vmss)).

A méretezési csoportokban lévő virtuális gépek listázásához használhatja a [Resources.Azure.com](https://resources.azure.com) vagy az [Azure SDK](https://azure.microsoft.com/downloads/) -kat is.

A kimenet pontos bemutatása a parancs által megadott beállításoktól függ, de itt látható a CLI-ből származó példa kimenete:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Amint láthatja, a "instanceId" tulajdonság csak decimális szám lehet. Előfordulhat, hogy a példány-azonosítók újra felhasználhatók az új példányok számára a régi példányok törlése után.

>[!NOTE]
> Nincs **garancia** arra, hogy a példány-azonosítók a méretezési csoportba tartozó virtuális gépekhez legyenek rendelve. Úgy tűnhet, hogy egymás után egyre növekszik időnként, de ez nem mindig igaz. Ne vegyen fel függőséget arra a konkrét módszerre, amelyben a példány-azonosítók hozzá vannak rendelve a virtuális gépekhez.

## <a name="scale-set-vm-names"></a>Méretezési csoport virtuális gépek nevei

A fenti példában szereplő kimenetben a virtuális gép neve is szerepel. Ez a név a következő formát ölti: "{Scale-set-name} _ {instance-id}". Ez a név a méretezési csoport példányainak listázásakor a Azure Portal látható.

![Képernyőfelvétel: a virtuálisgép-méretezési csoport példányainak listája a Azure Portalban.](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A név {instance-ID} része a korábban tárgyalt "instanceId" tulajdonsághoz tartozó decimális szám.

## <a name="instance-metadata-vm-name"></a>Példány metaadatainak virtuális gép neve

Ha a [példány metaadatait](../virtual-machines/windows/instance-metadata-service.md) egy méretezési csoport virtuális gépről kérdezi le, a kimenetben a "név" látható.

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Ez a név megegyezik a korábban tárgyalt névvel.

## <a name="scale-set-vm-computer-name"></a>Méretezési csoport VM-számítógépének neve

A méretezési csoport minden virtuális gépe a hozzá tartozó számítógép nevét is megjeleníti. Ez a számítógépnév a virtuális gép állomásneve az [Azure által biztosított DNS-névfeloldásban a virtuális hálózaton belül](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Ez a számítógépnév "{számítógépnév-előtag} {Base-36-instance-id}" formátumú.

A {Base-36-instance-ID} érték a [36-es alapszintű](https://en.wikipedia.org/wiki/Base36) , és mindig hat számjegy hosszúságú. Ha a szám 36-os alapértéke kevesebb, mint hat számjegyből áll, a {Base-36-instance-ID} nulla értékkel van feltöltve, hogy hat számjegy hosszú legyen. Például a (z) {számítógépnév-prefix} "nsgvmss" azonosítójú példánynak és a 85-as AZONOSÍTÓJÚ példánynak a neve "nsgvmss00002D" lesz.

>[!NOTE]
> A számítógép nevének előtagja a beállítható méretezési csoport modelljének egyik tulajdonsága, így a méretezési csoport nevétől eltérő lehet.
