---
title: Azure Load Balancer szabály létrehozása fürthöz
description: Azure Load Balancer konfigurálása az Azure Service Fabric-fürthöz tartozó portok megnyitásához.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 7e09c7b0b3e2bfa5a5ff834e243f5098cbbd947b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319904"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Service Fabric-fürt portjainak megnyitása

Az Azure Service Fabric-fürttel üzembe helyezett terheléselosztó átirányítja a forgalmat a csomóponton futó alkalmazás felé. Ha úgy módosítja az alkalmazást, hogy másik portot használjon, ki kell jelölnie ezt a portot (vagy egy másik portot kell átirányítani) a Azure Load Balancer.

Amikor üzembe helyezte a Service Fabric-fürtöt az Azure-ban, a terheléselosztó létrehozása automatikusan megtörténik. Ha nincs terheléselosztó, tekintse meg [az internetre irányuló terheléselosztó konfigurálása](../load-balancer/quickstart-load-balancer-standard-public-portal.md)című témakört.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>A Service Fabric konfigurálása

A Service Fabric alkalmazás **ServiceManifest.xml** konfigurációs fájl határozza meg az alkalmazás által használni kívánt végpontokat. Miután a konfigurációs fájlt frissítette egy végpont definiálásához, frissítenie kell a terheléselosztó számára, hogy elérhető legyen (vagy egy másik) port. A Service Fabric-végpont létrehozásával kapcsolatos további információkért lásd: [végpont beállítása](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A Load Balancer szabály egy internetkapcsolattal rendelkező portot nyit meg, és továbbítja a forgalmat az alkalmazás által használt belső csomópont portjához. Ha nincs terheléselosztó, tekintse meg [az internetre irányuló terheléselosztó konfigurálása](../load-balancer/quickstart-load-balancer-standard-public-portal.md)című témakört.

Load Balancer szabály létrehozásához a következő információkat kell összegyűjtenie:

- Terheléselosztó neve.
- A Load Balancer és a Service Fabric-fürt erőforráscsoport.
- Külső port.
- Belső port.

## <a name="azure-cli"></a>Azure CLI
Csak egyetlen paranccsal hozható létre terheléselosztó-szabály az **Azure CLI**-vel. Új szabály létrehozásához csak a terheléselosztó és az erőforráscsoport nevét kell ismernie.

>[!NOTE]
>Ha meg kell határoznia a terheléselosztó nevét, ezzel a paranccsal gyorsan lekérheti az összes terheléselosztó és a kapcsolódó erőforráscsoportok listáját.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Az Azure CLI-parancs néhány paraméterrel rendelkezik, amelyek a következő táblázatban olvashatók:

| Paraméter | Leírás |
| --------- | ----------- |
| `--backend-port`  | Az Service Fabric-alkalmazás által figyelt port. |
| `--frontend-port` | A terheléselosztó által a külső kapcsolatok számára elérhető port. |
| `-lb-name` | A módosítandó terheléselosztó neve. |
| `-g`       | Az erőforráscsoport, amelyben a terheléselosztó és a Service Fabric fürt is szerepel. |
| `-n`       | A szabály kívánt neve. |


>[!NOTE]
>A terheléselosztó Azure CLI-vel történő létrehozásával kapcsolatos további információkért lásd: terheléselosztó [létrehozása az Azure CLI-vel](../load-balancer/quickstart-load-balancer-standard-internal-cli.md).

## <a name="powershell"></a>PowerShell

A PowerShell valamivel bonyolultabb, mint az Azure CLI. Egy szabály létrehozásához kövesse az alábbi elméleti lépéseket:

1. A terheléselosztó beszerzése az Azure-ból.
2. Hozzon létre egy szabályt.
3. Adja hozzá a szabályt a terheléselosztó számára.
4. Frissítse a Load balancert.

>[!NOTE]
>Ha meg kell határoznia a terheléselosztó nevét, ezzel a paranccsal gyorsan lekérheti az összes terheléselosztó és a hozzá tartozó erőforráscsoport listáját.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

A `New-AzLoadBalancerRuleConfig` parancshoz tartozó érték azt a `-FrontendPort` portot jelöli, amelyet a terheléselosztó a külső kapcsolatok számára tesz elérhetővé, és `-BackendPort` azt a portot jelöli, amelyen a Service Fabric-alkalmazás figyeli a szolgáltatást.

>[!NOTE]
>A terheléselosztó PowerShell-lel történő létrehozásával kapcsolatos további információkért lásd: [Load Balancer létrehozása a PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md)-lel.

## <a name="next-steps"></a>Következő lépések

További információ a [Service Fabric hálózatkezeléséről](service-fabric-patterns-networking.md).