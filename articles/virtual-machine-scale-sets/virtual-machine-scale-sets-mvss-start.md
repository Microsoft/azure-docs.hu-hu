---
title: Tudnivalók a virtuálisgép-méretezési csoport sablonjairól | Microsoft Docs
description: Útmutató a virtuálisgép-méretezési csoportok alapszintű méretezési csoportjának létrehozásához
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 267c715de67df57abd30ac18966b8b3b8440810c
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376108"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Tudnivalók a virtuálisgép-méretezési csoport sablonjairól
Az [Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) remek megoldást kínálnak egymáshoz kapcsolódó erőforráscsoportok üzembe helyezésére. Ez az oktatóanyag-sorozat bemutatja, hogyan hozható létre egy alapszintű méretezési csoport sablonja, és hogyan módosítható a sablon különböző helyzetekben. Az összes példa ebből a [GitHub-adattárból](https://github.com/gatneil/mvss)származik.

Ez a sablon egyszerű. A méretezési csoport sablonjaival kapcsolatos további példákért tekintse meg az [Azure gyorsindítási sablonok GitHub-tárházát](https://github.com/Azure/azure-quickstart-templates) , és keressen `vmss`rá a karakterláncot tartalmazó mappákra.

Ha már ismeri a sablonok létrehozását, ugorjon a "következő lépések" szakaszra, ahol megtudhatja, hogyan módosíthatja a sablont.

## <a name="define-schema-and-contentversion"></a>$schema és contentVersion meghatározása
Először határozza meg `$schema` a `contentVersion` és a sablont a sablonban. Az `$schema` elem határozza meg a sablon nyelvének verzióját, és a Visual Studio szintaxisának kiemeléséhez és hasonló érvényesítési funkciókhoz használatos. Az `contentVersion` Azure nem használja az elemet. Ehelyett a sablon verziójának nyomon követését segíti.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Paraméterek definiálása
Ezután definiáljon két paramétert `adminUsername` és `adminPassword`. A paraméterek az üzembe helyezés időpontjában megadott értékek. A `adminUsername` paraméter egyszerűen egy `string` típus, de mivel `adminPassword` egy titkos kulcs, adja meg a típust `securestring`. Később ezeket a paramétereket átadja a méretezési csoport konfigurációjának.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Változók meghatározása
A Resource Manager-sablonok lehetővé teszik a sablon későbbi részében használandó változók megadását is. A példa nem használ változókat, így a JSON-objektum üres.

```json
  "variables": {},
```

## <a name="define-resources"></a>Erőforrások definiálása
A következő a sablon erőforrások szakasza. Itt adhatja meg, hogy mit szeretne telepíteni valójában. A `parameters` és `variables` a (amelyek JSON-objektumok) `resources` eltérően a JSON-objektumok JSON-listája.

```json
   "resources": [
```

Minden erőforráshoz `type`szükség `name`van `apiVersion`,, `location` és tulajdonságok szükségesek. Ebben a példában az első erőforrás típusa [Microsoft. Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), name `myVnet`és apiVersion. `2018-11-01` (Az erőforrástípus legújabb API-verziójának megkereséséhez tekintse meg a [Azure Resource Manager sablon referenciáját](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Hely meghatározása
A virtuális hálózat helyének megadásához használja a [Resource Manager-sablon függvényt](../azure-resource-manager/resource-group-template-functions.md). Ezt a függvényt idézőjelek és szögletes zárójelek közé kell foglalni, például `"[<template-function>]"`:. Ebben az esetben használja a `resourceGroup` függvényt. Nem tartalmaz argumentumokat, és egy olyan JSON-objektumot ad vissza, amely az erőforráscsoporthoz tartozó metaadatokkal rendelkezik, és a központi telepítés folyamatban van. Az erőforráscsoportot az üzembe helyezés időpontjában a felhasználó állítja be. Ezt az értéket ezután a JSON `.location` -objektumba indexeli a rendszer, hogy lekérje a helyet a JSON-objektumból.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Virtuális hálózat tulajdonságainak megadása
Minden Resource Manager-erőforráshoz tartozik `properties` egy saját szakasz az erőforrásra jellemző konfigurációkhoz. Ebben az esetben azt kell megadnia, hogy a virtuális hálózatnak egy alhálózattal kell `10.0.0.0/16`rendelkeznie a magánhálózati IP-címtartomány használatával. Egy méretezési csoport mindig egy alhálózaton belül található. Az alhálózatok nem terjedhetnek ki.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>DependsOn-lista hozzáadása
`type`A kötelező `name`,, és`location` tulajdonságok mellett minden erőforrás rendelkezhet választható `dependsOn` karakterláncok listájával is. `apiVersion` Ez a lista azt határozza meg, hogy a központi telepítés mely más erőforrásainak kell befejezniük az erőforrás telepítése előtt.

Ebben az esetben csak egy elem szerepel a listán, a virtuális hálózat az előző példából. Ezt a függőséget adja meg, mert a méretezési csoportnak a virtuális gépek létrehozása előtt léteznie kell a hálózatnak. Így a méretezési csoport ezeket a virtuális gépek magánhálózati IP-címeit a hálózat tulajdonságainál korábban megadott IP-címtartomány alapján is megadhatja. A dependsOn listában `<type>/<name>`szereplő egyes sztringek formátuma. Használja a `type` virtuális hálózati `name` erőforrás-definícióban korábban használt és használatban lévőket.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Méretezési csoport tulajdonságainak megadása
A méretezési csoportok számos tulajdonsággal rendelkeznek a méretezési csoportba tartozó virtuális gépek testreszabásához. A tulajdonságok teljes listájáért tekintse meg a [sablonra vonatkozó referenciát](/azure/templates/microsoft.compute/virtualmachinescalesets). Ebben az oktatóanyagban csak néhány gyakran használt tulajdonság van beállítva.
### <a name="supply-vm-size-and-capacity"></a>Adja meg a virtuális gép méretét és kapacitását
A méretezési csoportnak tudnia kell, hogy a létrehozandó virtuális gép mekkora mérete ("SKU Name") és hány ilyen virtuális gép hozható létre ("SKU Capacity"). Ha szeretné megtudni, hogy mely virtuálisgép-méretek érhetők el, tekintse meg a [VM-méretek dokumentációját](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>A frissítések típusának kiválasztása
A méretezési csoportnak ismernie kell a méretezési csoport frissítéseinek kezelését is. Jelenleg három lehetőség `Manual`áll rendelkezésre:, `Rolling` és `Automatic`. A kettő közötti különbségekkel kapcsolatos további információkért tekintse meg a [méretezési csoport frissítésének](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)dokumentációját.

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Virtuális gép operációs rendszerének kiválasztása
A méretezési csoportnak tudnia kell, hogy milyen operációs rendszerre kell helyezni a virtuális gépeket. Itt hozza létre a virtuális gépeket egy teljesen javított Ubuntu 16,04-LTS rendszerképpel.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>ComputerNamePrefix meghatározása
A méretezési csoport több virtuális gépet helyez üzembe. Az egyes virtuális gépek nevének megadása helyett adja meg `computerNamePrefix`a (z) értéket. A méretezési csoport az egyes virtuális gépekhez tartozó előtaghoz hozzáfűz egy indexet, így a virtuális `<computerNamePrefix>_<auto-generated-index>`gépek nevei rendelkeznek az űrlappal.

A következő kódrészletben használja a paramétereket, mielőtt beállítja a rendszergazdai felhasználónevet és jelszót a méretezési csoport összes virtuális gépe számára. Ez a folyamat a `parameters` sablon függvényt használja. Ez a függvény egy karakterláncot használ, amely megadja, hogy melyik paraméterre hivatkozik, és a paraméter értékét adja meg.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Virtuálisgép-hálózat konfigurációjának meghatározása
Végül adja meg a méretezési csoportba tartozó virtuális gépek hálózati konfigurációját. Ebben az esetben csak a korábban létrehozott alhálózat AZONOSÍTÓját kell megadnia. Ez azt jelzi, hogy a méretezési csoport a hálózati adaptereket ezen az alhálózaton helyezi el.

Az alhálózatot tartalmazó virtuális hálózat azonosítóját a `resourceId` sablon függvény használatával érheti el. Ez a függvény az erőforrás típusát és nevét veszi figyelembe, és az adott erőforrás teljes azonosítóját adja vissza. Ennek az AZONOSÍTÓnak a formája a következő:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

A virtuális hálózat azonosítója azonban nem elég. Adja meg azt a megadott alhálózatot, amelyhez a méretezési csoport virtuális gépei tartoznak. Ehhez fűzze `/subnets/mySubnet` össze a virtuális hálózat azonosítóját. Az eredmény az alhálózat teljes azonosítója. Ezt az összefűzést a `concat` függvénnyel végezheti el, amely karakterláncok sorozatát veszi figyelembe, és visszaadja az összefűzését.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>További lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
