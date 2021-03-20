---
title: 'Társ-szolgáltatási kapcsolatok regisztrálása – Azure PowerShell '
description: Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat egy társ-szolgáltatási kapcsolatot a PowerShell-lel.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: a12bfa042c8741814cedc70f1dcb67dedbfd331e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400434"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Oktatóanyag: társ-szolgáltatási kapcsolatok regisztrálása Azure PowerShell használatával

Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat Azure PowerShell használatával a társ-nyilvántartó szolgáltatást.

Az Azure társközi szolgáltatás olyan hálózati szolgáltatás, amely a Microsoft Cloud Services, például a Microsoft 365, a Dynamics 365, a szolgáltatott szoftver (SaaS) szolgáltatásai, az Azure vagy bármely, a nyilvános interneten keresztül elérhető Microsoft-szolgáltatás számára teszi lehetővé az ügyfelek kapcsolódását. Ebből a cikkből megtudhatja, hogyan regisztrálhat egy társ-szolgáltatási kapcsolatot Azure PowerShell használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre most egy [fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha ehelyett a PowerShell helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz Azure PowerShell-modul 1.0.0-es vagy újabb verzióját kell használnia. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. A telepítési és frissítési információkért lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps).

Végül, ha helyileg futtatja a PowerShellt, a parancsot is futtatnia kell `Connect-AzAccount` . Ez a parancs létrehozza az Azure-hoz való hozzáférést.

A Azure PowerShell modul használatával regisztrálja és kezelheti a társ-kezelési szolgáltatást. A PowerShell-parancssorból vagy parancsfájlokból is regisztrálhatja vagy kezelheti a társ-kezelési szolgáltatást.


## <a name="prerequisites"></a>Előfeltételek  
A következőkkel kell rendelkeznie:

### <a name="azure-account"></a>Azure-fiók

Érvényes és aktív Microsoft Azure fiókkal kell rendelkeznie. Ez a fiók szükséges a társ-szolgáltatási kapcsolatok beállításához. Az egyenrangú szolgáltatás az Azure-előfizetéseken belüli erőforrás.

### <a name="connectivity-provider"></a>Kapcsolatszolgáltató

A hálózat Microsoft hálózattal való összekapcsolásához egy internetszolgáltatóval vagy egy internetes Exchange-partnerrel is dolgozhat.

Győződjön meg arról, hogy a kapcsolati szolgáltatók a Microsofttal együttműködtek.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Előfizetés regisztrálása az erőforrás-szolgáltató és a szolgáltatás jelzővel

Mielőtt folytatná a partneri szolgáltatás regisztrálásának lépéseit, regisztrálja az előfizetését az erőforrás-szolgáltató és a szolgáltatás jelölővel Azure PowerShell használatával. A Azure PowerShell parancsok itt vannak megadva:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>A hely és a szolgáltató beolvasása 

Futtassa az alábbi parancsokat a Azure PowerShellban annak a helynek és szolgáltatónak a beolvasásához, amelyhez engedélyezni szeretné a társítási szolgáltatást. 

Egyenrangú szolgáltatások helyeinek beolvasása:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Egyenrangú szolgáltatók beolvasása:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>A társ-szolgáltatási kapcsolatok regisztrálása

Regisztrálja a társ-szolgáltatási kapcsolatot a következő parancsok használatával Azure PowerShellon keresztül. Ez a példa regisztrálja a myPeeringService nevű társ-szolgáltatást.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>A társ-szolgáltatási előtag regisztrálása

Regisztrálja a kapcsolat szolgáltatója által biztosított előtagot az alábbi parancsok Azure PowerShell használatával történő végrehajtásával. Ez a példa regisztrálja az számára nevű előtagot.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Az összes társ-szolgáltatási kapcsolat listázása

Az összes egyenrangú szolgáltatás listájának megtekintéséhez futtassa a következő parancsot:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Az összes egyenrangú szolgáltatás előtagjainak listázása

A következő parancs futtatásával tekintheti meg az összes társ-szolgáltatási előtagok listáját:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>A társ-szolgáltatási előtag eltávolítása

A társ-szolgáltatási előtag eltávolításához futtassa a következő parancsot:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Következő lépések

- A társ-szolgáltatási kapcsolatok megismeréséhez tekintse meg a társítási [szolgáltatás kapcsolódása](connection.md)című témakört.
- A társ-szolgáltatási kapcsolatok telemetria kapcsolatos további tudnivalókért lásd: társítási [szolgáltatás kapcsolódási telemetria](connection-telemetry.md).
- Ha a Azure Portal segítségével szeretne regisztrálni egy társ-szolgáltatási kapcsolatot, tekintse meg a következő témakört: [társ-szolgáltatási kapcsolatok regisztrálása – Azure Portal](azure-portal.md).
- Ha az Azure CLI használatával szeretne regisztrálni egy társ-szolgáltatási kapcsolatot, tekintse meg a következő témakört: [társ-szolgáltatási kapcsolatok regisztrálása – Azure CLI](cli.md).
