---
title: Az Azure spot Virtual Machines üzembe helyezése a CLI használatával
description: Megtudhatja, hogyan használhatja a CLI-t az Azure spot Virtual Machines üzembe helyezéséhez a költségek megtakarítása érdekében.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 90ad35757834c14abdffb017ff31b3296074ca24
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802437"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Az Azure spot Virtual Machines üzembe helyezése az Azure CLI használatával

Az [Azure Spot Virtual Machines](../spot-vms.md) használatával jelentős költségmegtakarítás mellett kihasználhatja a fel nem használt kapacitás előnyeit. Az Azure-infrastruktúra minden olyan időpontban kizárja az Azure spot Virtual Machinest, amikor az Azure-nak szüksége van a kapacitásra. Ezért az Azure spot Virtual Machines nagyszerűek olyan munkaterhelésekhez, amelyek képesek kezelni a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

Az Azure spot Virtual Machines díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. Az Azure-beli helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, legfeljebb 5 tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára lesz az Azure spot virtuális gép aktuális díja, vagy egy standard VM díja, ami még nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta. A maximális ár beállításával kapcsolatos további információkért lásd: [Azure Spot Virtual Machines – díjszabás](../spot-vms.md#pricing).

Az Azure-beli virtuális gépek Azure CLI-vel történő létrehozásának folyamata megegyezik a rövid útmutató [cikkben](./quick-create-cli.md)ismertetett eljárással. Csak adja hozzá a "--priority spot" paramétert, állítsa be a `--eviction-policy` (z) felszabadítását (ez az alapértelmezett érték) `Delete` , és adja meg a maximális árat vagy a értéket `-1` . 


## <a name="install-azure-cli"></a>Telepítse az Azure CLI-t

Az Azure spot Virtual Machines létrehozásához az Azure CLI 2.0.74 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

Jelentkezzen be az Azure-ba az [az login](/cli/azure/reference-index#az-login)használatával.

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Azure-beli direktszínű virtuális gép létrehozása

Ebből a példából megtudhatja, hogyan helyezhet üzembe egy olyan linuxos Azure-beli virtuális gépet, amelyet a díjszabás alapján nem fog kizárni. A kizárási szabályzat úgy van beállítva, hogy felszabadítsa a virtuális gépet, hogy később újra lehessen indítani. Ha törölni szeretné a virtuális gépet és a mögöttes lemezt a virtuális gép kizárásakor, állítsa a következőre: `--eviction-policy` `Delete` .

```azurecli-interactive
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

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Kizárás szimulálása

A REST, a PowerShell vagy a CLI használatával szimulálhatja az Azure-beli direktszínű virtuális gépek kizárását annak teszteléséhez, hogy az alkalmazás milyen jól reagáljon a hirtelen kizárásra.

A legtöbb esetben érdemes a REST API [Virtual Machines szimulálni a kizárást](/rest/api/compute/virtualmachines/simulateeviction) , hogy segítsen az alkalmazások automatizált tesztelésében. A REST esetében `Response Code: 204` az azt jelenti, hogy a szimulált kizárás sikeres volt. A szimulált kizárásokat kombinálhatja az [ütemezett esemény szolgáltatással](scheduled-events.md), így automatizálhatja, hogy az alkalmazás hogyan reagáljon a virtuális gép kizárásakor.

Az ütemezett események működés közbeni megtekintéséhez tekintse meg az [Azure Friday – azure Scheduled Events használatával készítse elő a virtuális gépek karbantartását](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Gyorsteszt

Ha egy gyors teszttel bemutatjuk, hogyan fog működni egy szimulált kizárás, lássuk végig az ütemezett esemény szolgáltatás lekérdezését, hogy meglássuk, mi úgy néz ki, mint amikor a kizárást az Azure CLI használatával szimulálja.

Az ütemezett esemény szolgáltatás engedélyezve van a szolgáltatáshoz, amikor az első alkalommal kérelmet küld az eseményekre. 

Távolról a virtuális gépre, majd nyisson meg egy parancssort. 

A virtuális gépen a parancssorba írja be a következőt:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ez az első válasz akár 2 percet is igénybe vehet. Mostantól szinte azonnal meg kell jelennie a kimenetnek.

Egy olyan számítógépről, amelyen telepítve van az Azure CLI (például a helyi gép), szimuláljon egy kizárást az [az VM szimulálása-kilakoltatás](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction)használatával. Cserélje le az erőforráscsoport nevét és a virtuális gép nevét a saját nevére. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

Ha a kérés sikeresen létrejött, a válasz kimenete lesz `Status: Succeeded` .

Lépjen vissza a helyszíni virtuális géphez való távoli kapcsolódásra, és ismételje meg a Scheduled Events-végpont lekérdezését. Ismételje meg a következő parancsot, amíg olyan kimenetet kap, amely további információkat tartalmaz:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ha az ütemezett esemény szolgáltatás lekéri a kizárási értesítést, a következőhöz hasonló választ kap:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Láthatja, hogy az `"EventType":"Preempt"` erőforrás a virtuális gép erőforrása `"Resources":["myspotvm"]` . 

Azt is láthatja, hogy a virtuális gép Mikor lesz kiürítve a `"NotBefore"` -ben – a virtuális gép nem lesz kizárva a megadott idő előtt, így az alkalmazásának az ablaka szabályosan lezárul.


## <a name="next-steps"></a>Következő lépések

[Azure PowerShell](../windows/spot-powershell.md), [portál](../spot-portal.md)vagy [sablon](spot-template.md)használatával is létrehozhat egy Azure-beli helyszíni virtuális gépet.

A jelenlegi díjszabási információkat az [Azure kiskereskedelmi díjszabás API](/rest/api/cost-management/retail-prices/azure-retail-prices) használatával kérdezheti le az Azure-beli helyszíni virtuális gépekről. A `meterName` és `skuName` mindkettő tartalmazni fogja `Spot` .

Ha hibát tapasztal, tekintse meg a [hibakódokat](../error-codes-spot.md).
