---
title: Az Azure spot Virtual Machines üzembe helyezése a CLI használatával
description: Megtudhatja, hogyan használhatja a CLI-t az Azure spot Virtual Machines üzembe helyezéséhez a költségek megtakarítása érdekében.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: abb29c0826e38af0bbbc1b59e41234acdaaca0f9
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100554787"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Az Azure spot Virtual Machines üzembe helyezése az Azure CLI használatával

Az [Azure Spot Virtual Machines](../spot-vms.md) használatával jelentős költségmegtakarítás mellett kihasználhatja a fel nem használt kapacitás előnyeit. Az Azure-infrastruktúra minden olyan időpontban kizárja az Azure spot Virtual Machinest, amikor az Azure-nak szüksége van a kapacitásra. Ezért az Azure spot Virtual Machines nagyszerűek olyan munkaterhelésekhez, amelyek képesek kezelni a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

Az Azure spot Virtual Machines díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. Az Azure-beli helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, legfeljebb 5 tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára lesz az Azure spot virtuális gép aktuális díja, vagy egy standard VM díja, ami még nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta. A maximális ár beállításával kapcsolatos további információkért lásd: [Azure Spot Virtual Machines – díjszabás](../spot-vms.md#pricing).

Az Azure-beli virtuális gépek Azure CLI-vel történő létrehozásának folyamata megegyezik a rövid útmutató [cikkben](./quick-create-cli.md)ismertetett eljárással. Csak adja hozzá a "--priority spot" paramétert, állítsa be a `--eviction-policy` (z) felszabadítását (ez az alapértelmezett érték) `Delete` , és adja meg a maximális árat vagy a értéket `-1` . 


## <a name="install-azure-cli"></a>Telepítse az Azure CLI-t

Az Azure spot Virtual Machines létrehozásához az Azure CLI 2.0.74 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

Jelentkezzen be az Azure-ba az [az login](/cli/azure/reference-index#az-login)használatával.

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Azure-beli direktszínű virtuális gép létrehozása

Ebből a példából megtudhatja, hogyan helyezhet üzembe egy olyan linuxos Azure-beli virtuális gépet, amelyet a díjszabás alapján nem fog kizárni. A kizárási szabályzat úgy van beállítva, hogy felszabadítsa a virtuális gépet, hogy később újra lehessen indítani. Ha törölni szeretné a virtuális gépet és a mögöttes lemezt a virtuális gép kizárásakor, állítsa a következőre: `--eviction-policy` `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



A virtuális gép létrehozása után a lekérdezéssel megtekintheti az erőforráscsoport összes virtuális gépe esetében a maximális számlázási árat.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Kizárás szimulálása

Az Azure-beli direktszínű virtuális gépek [kizárását szimulálhatja](/rest/api/compute/virtualmachines/simulateeviction) , így tesztelheti, hogy az alkalmazás milyen jól fogja kizárni a hirtelen kizárást. 

Cserélje le a következőt az adataira: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Következő lépések**

[Azure PowerShell](../windows/spot-powershell.md), [portál](../spot-portal.md)vagy [sablon](spot-template.md)használatával is létrehozhat egy Azure-beli helyszíni virtuális gépet.

A jelenlegi díjszabási információkat az [Azure kiskereskedelmi díjszabás API](/rest/api/cost-management/retail-prices/azure-retail-prices) használatával kérdezheti le az Azure-beli helyszíni virtuális gépekről. A `meterName` és `skuName` mindkettő tartalmazni fogja `Spot` .

Ha hibát tapasztal, tekintse meg a [hibakódokat](../error-codes-spot.md).