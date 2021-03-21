---
title: 'Azure ExpressRoute: klasszikus virtuális hálózatok migrálása a Resource Managerbe'
description: Ez a lap azt ismerteti, hogyan lehet áttelepíteni a ExpressRoute-hez társított virtuális hálózatokat a Resource Managerbe az áramkör áthelyezése után.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: 57c439cf8ac52d93d231d6ff33f72a5a942dec6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351605"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>ExpressRoute-társított virtuális hálózatok migrálása a Klasszikusból a Resource Managerbe

Ez a cikk azt ismerteti, hogyan telepítheti át a ExpressRoute-hez társított virtuális hálózatokat a klasszikus üzemi modellből a Azure Resource Manager üzemi modellbe az ExpressRoute-áramkör áthelyezése után. 

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ellenőrizze, hogy rendelkezik-e a Azure PowerShell modulok legújabb verzióival. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/) foglalkozó témakörben talál. A PowerShell Service Management modul (amely a klasszikus üzemi modellhez szükséges) telepítéséhez tekintse meg [a Azure PowerShell Service Management modul telepítését](/powershell/azure/servicemanagement/install-azure-ps)ismertető témakört.
* A konfigurálás megkezdése előtt ellenőrizze, hogy áttekintette-e az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .
* Tekintse át a [ExpressRoute-áramkör áthelyezése a Klasszikusból a Resource Managerbe](expressroute-move.md)című témakörben megadott információkat. Győződjön meg arról, hogy teljes mértékben ismeri a korlátozásokat és korlátozásokat.
* Ellenőrizze, hogy az áramkör teljesen működőképes-e a klasszikus üzemi modellben.
* Győződjön meg arról, hogy van egy erőforráscsoport, amely a Resource Manager-alapú üzemi modellben lett létrehozva.
* Tekintse át a következő erőforrás-áttelepítési dokumentációt:

    * [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való, platform által támogatott migrálása](../virtual-machines/migration-classic-resource-manager-overview.md)
    * [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
    * [Gyakori kérdések: a platform által támogatott IaaS-erőforrások áttelepítése klasszikusról Azure Resource Managerra](../virtual-machines/migration-classic-resource-manager-faq.md)
    * [A leggyakoribb áttelepítési hibák és enyhítések áttekintése](../virtual-machines/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Támogatott és nem támogatott forgatókönyvek

* Az ExpressRoute áramkör a Klasszikusból a Resource Manager-környezetbe a leállás nélkül helyezhető át. A Klasszikusból a Resource Manager-környezetbe bármilyen ExpressRoute-áramkört leállás nélkül helyezhet át. Kövesse a ExpressRoute- [áramkörök Klasszikusból a Resource Manager-alapú üzemi modellbe való áthelyezésének](expressroute-howto-move-arm.md)utasításait a PowerShell használatával. Ez a virtuális hálózathoz csatlakoztatott erőforrások áthelyezésének előfeltétele.
* A virtuális hálózatok, az átjárók és az ugyanazon előfizetésben lévő ExpressRoute-áramkörhöz kapcsolódó központi telepítések a Resource Manager-környezetbe állásidő nélkül is áttelepíthetők. A később ismertetett lépéseket követve áttelepítheti az erőforrásokat, például a virtuális hálózatokat, az átjárókat és a virtuális hálózaton belül üzembe helyezett virtuális gépeket. Győződjön meg arról, hogy a virtuális hálózatok megfelelően vannak konfigurálva az áttelepítés előtt. 
* A virtuális hálózatok, átjárók és a virtuális hálózaton belüli olyan központi telepítések, amelyek nem ugyanabba az előfizetésbe tartoznak, mint a ExpressRoute áramkör, némi állásidőt igényelnek az áttelepítés befejezéséhez. A dokumentum utolsó szakasza az erőforrások áttelepíthető lépéseit ismerteti.
* A ExpressRoute-átjárót és VPN Gatewayt egyaránt tartalmazó virtuális hálózat nem telepíthető át.
* A ExpressRoute áramkör-előfizetések közötti áttelepítés nem támogatott. További információ: [Microsoft. Network Move support](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>ExpressRoute-áramkör áthelyezése a Klasszikusból a Resource Managerbe
A ExpressRoute-áramkörhöz csatolt erőforrások áttelepítésének megkezdése előtt át kell helyeznie egy ExpressRoute-áramkört a Klasszikusból a Resource Manager-környezetbe. A feladat végrehajtásához tekintse meg a következő cikkeket:

* Tekintse át a [ExpressRoute-áramkör áthelyezése a Klasszikusból a Resource Managerbe](expressroute-move.md)című témakörben megadott információkat.
* [Áramkör áthelyezése a Klasszikusból a Resource managerbe Azure PowerShell használatával](expressroute-howto-move-arm.md).
* Használja az Azure Service Management portált. A munkafolyamatot követve [létrehozhat egy új ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , és kiválaszthatja az importálás lehetőséget. 

Ez a művelet nem jár leállással. Továbbra is továbbíthatja az adatok átvitelét a helyszíni és a Microsoft között, amíg az áttelepítés folyamatban van.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Virtuális hálózatok, átjárók és társított központi telepítések áttelepítése

Az áttelepíteni kívánt lépések attól függnek, hogy az erőforrások ugyanahhoz az előfizetéshez, különböző előfizetésekhez vagy mindkettőhöz tartoznak-e.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Virtuális hálózatok, átjárók és társított központi telepítések áttelepítése ugyanabban az előfizetésben, mint a ExpressRoute áramkör
Ez a szakasz a virtuális hálózatok, az átjárók és a társított központi telepítések áttelepítésének lépéseit ismerteti a ExpressRoute áramkörével megegyező előfizetésben. Nincs állásidő társítva ehhez az áttelepítéshez. Továbbra is használhatja az összes erőforrást az áttelepítési folyamaton keresztül. A felügyeleti sík zárolva van, amíg az áttelepítés folyamatban van. 

1. Győződjön meg arról, hogy a ExpressRoute áramkör át lett helyezve a Klasszikusból a Resource Manager-környezetbe.
2. Gondoskodjon arról, hogy a virtuális hálózat megfelelően előkészített legyen az áttelepítéshez.
3. Regisztrálja az előfizetését az erőforrás-áttelepítéshez. Az előfizetés erőforrás-áttelepítésre való regisztrálásához használja a következő PowerShell-kódrészletet:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Érvényesítse, készítse el és telepítse át. A virtuális hálózat áthelyezéséhez használja a következő PowerShell-kódrészletet:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Az áttelepítést az alábbi PowerShell-parancsmag futtatásával is megszakíthatja:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Következő lépések
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való, platform által támogatott migrálása](../virtual-machines/migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
* [Gyakori kérdések: a platform által támogatott IaaS-erőforrások áttelepítése klasszikusról Azure Resource Managerra](../virtual-machines/migration-classic-resource-manager-faq.md)
* [A leggyakoribb áttelepítési hibák és enyhítések áttekintése](../virtual-machines/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)