---
title: 'Azure ExpressRoute: klasszikus áramkörök áthelyezése a Resource Managerbe'
description: Ez a lap leírja, hogyan helyezheti át a klasszikus áramkört a Resource Manager-alapú üzemi modellbe a PowerShell használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 460ea446fc6dfc43e81a1a57bbba032a61f3a72d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532545"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>ExpressRoute-áramkörök áthelyezése a Klasszikusból a Resource Manager-alapú üzemi modellbe a PowerShell használatával

Ahhoz, hogy a klasszikus és a Resource Manager-alapú üzemi modellhez is ExpressRoute áramkört lehessen használni, át kell helyeznie az áramkört a Resource Manager-alapú üzemi modellbe. A következő részekben a PowerShell használatával helyezheti át az áramkörét.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Győződjön meg arról, hogy a klasszikus és az Azure PowerShell modulok helyileg is telepítve vannak a számítógépen. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/) foglalkozó témakörben talál.
* A konfigurálás megkezdése előtt ellenőrizze, hogy áttekintette-e az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .
* Tekintse át a [ExpressRoute-áramkör áthelyezése a Klasszikusból a Resource Managerbe](expressroute-move.md)című témakörben megadott információkat. Győződjön meg arról, hogy teljes mértékben ismeri a korlátozásokat és korlátozásokat.
* Ellenőrizze, hogy az áramkör teljesen működőképes-e a klasszikus üzemi modellben.
* Győződjön meg arról, hogy van egy erőforráscsoport, amely a Resource Manager-alapú üzemi modellben lett létrehozva.

## <a name="move-an-expressroute-circuit"></a>ExpressRoute áramkör áthelyezése

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>1. lépés: áramkör részleteinek összegyűjtése a klasszikus üzemi modellből

Jelentkezzen be a klasszikus Azure-környezetbe, és Gyűjtse össze a szolgáltatás kulcsát.

1. Jelentkezzen be Azure-fiókjába.

   ```powershell
   Add-AzureAccount
   ```

2. Válassza ki a megfelelő Azure-előfizetést.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importálja az Azure és a ExpressRoute PowerShell-modulját.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Használja az alábbi parancsmagot az összes ExpressRoute-áramkörhöz tartozó szolgáltatási kulcsok beszerzéséhez. A kulcsok beolvasása után másolja át a Resource Manager-alapú üzemi modellbe áthelyezni kívánt áramköri **szolgáltatás kulcsát** .

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>2. lépés: Jelentkezzen be, és hozzon létre egy erőforráscsoportot

Jelentkezzen be a Resource Manager-környezetbe, és hozzon létre egy új erőforráscsoportot.

1. Jelentkezzen be Azure Resource Manager-környezetbe.

   ```powershell
   Connect-AzAccount
   ```

2. Válassza ki a megfelelő Azure-előfizetést.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Az alábbi kódrészlet módosításával hozzon létre egy új erőforráscsoportot, ha még nem rendelkezik erőforráscsoport-csoporttal.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>3. lépés: a ExpressRoute áramkör áthelyezése a Resource Manager-alapú üzemi modellbe

Most már készen áll a ExpressRoute-áramkör áthelyezésére a klasszikus üzemi modellből a Resource Manager-alapú üzemi modellbe. A továbblépés előtt tekintse át az [ExpressRoute áramkör Klasszikusból a Resource Manager-alapú üzemi modellbe való áthelyezésével](expressroute-move.md)kapcsolatos információkat.

Az áramkör áthelyezéséhez módosítsa és futtassa a következő kódrészletet:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

Klasszikus módban egy ExpressRoute-áramkör nem rendelkezik egy régióhoz kötött koncepcióval. A Resource Managerben azonban minden erőforrást egy Azure-régióhoz kell rendelni. A Move-AzExpressRouteCircuit parancsmagban megadott régió technikailag bármely régió lehet. A szervezeti célokra érdemes lehet olyan régiót választani, amely szorosan reprezentálja a társi helyet.

> [!NOTE]
> * Miután a klasszikus ExpressRoute-áramkört a Resource Manager-alapú üzemi modellbe helyezte, alapértelmezés szerint a klasszikus és a Resource Manager-alapú üzemi modellhez is hozzáférhet.
> * Az előző parancsmagban szereplő új nevet fogja használni a rendszer az erőforrás kezeléséhez. Az áramkör alapvetően átnevezve lesz.

## <a name="modify-circuit-access"></a>Áramköri hozzáférés módosítása

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>A ExpressRoute áramkör-hozzáférés engedélyezése mindkét üzembe helyezési modellhez

Engedélyezheti a hozzáférést a klasszikus üzemi modellhez a Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-áramkörökhöz. A következő parancsmagok futtatásával engedélyezheti mindkét telepítési modell elérését:

1. Az áramkör részleteinek beolvasása.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Állítsa a "klasszikus műveletek engedélyezése" értéket igaz értékre.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Az áramkör frissítése. A művelet sikeres befejezését követően megtekintheti az áramkört a klasszikus üzembehelyezési modellben.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Futtassa a következő parancsmagot a ExpressRoute áramkör részleteinek lekéréséhez. Meg kell tudnia tekinteni a felsorolt szolgáltatási kulcsot.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Most már kezelheti a ExpressRoute-áramkörre mutató hivatkozásokat a klasszikus virtuális hálózatok, valamint a Resource Manager-virtuális hálózatok Resource Manager-parancsainak használatával. A következő cikkek segítenek a ExpressRoute áramkörre mutató hivatkozások kezelésében:

    * [A virtuális hálózat összekapcsolása a ExpressRoute-áramkörrel a Resource Manager-alapú üzemi modellben](expressroute-howto-linkvnet-arm.md)
    * [A virtuális hálózat összekapcsolása a ExpressRoute-áramkörrel a klasszikus üzemi modellben](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>A ExpressRoute-áramköri hozzáférés letiltása a klasszikus üzemi modellhez

Futtassa a következő parancsmagokat a klasszikus üzemi modellhez való hozzáférés letiltásához.

1. A ExpressRoute áramkör részleteinek beolvasása.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Állítsa a "klasszikus műveletek engedélyezése" értéket hamis értékre.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Az áramkör frissítése. A művelet sikeres befejezését követően nem fogja tudni megtekinteni az áramkört a klasszikus üzemi modellben.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Következő lépések

* [Az ExpressRoute-áramkör útválasztásának létrehozása és módosítása](expressroute-howto-routing-arm.md)
* [A virtuális hálózat összekapcsolása a ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
