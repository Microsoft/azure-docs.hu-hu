---
title: Az Azure Spot-alkalmazások üzembe helyezése a CLI Virtual Machines
description: Megtudhatja, hogyan használhatja a CLI-t az Azure Spot Virtual Machines a költségek csökkentése érdekében.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789608"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure Spot-Virtual Machines üzembe helyezése az Azure CLI használatával

Az [Azure Spot Virtual Machines](../spot-vms.md) lehetővé teszi, hogy jelentős költségmegtakarítás mellett kihasználja a kihasználatlan kapacitásunkat. Amikor az Azure-nak bármikor szüksége van a kapacitásra, az Azure-infrastruktúra ki fogja Virtual Machines. Ezért az Azure Spot Virtual Machines olyan számítási feladatokhoz hasznosak, amelyek kezelni tudnak olyan megszakításokat, mint a kötegelt feldolgozási feladatok, a fejlesztési/tesztelési környezetek, a nagy számítási feladatok stb.

Az Azure Spot Virtual Machines díjszabása régió és termékváltozat alapján változó. További információ: Virtuális gépek díjszabása [Linux és](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) [Windows rendszeren.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 

Beállíthatja azt a maximális árat, amit óránként hajlandó fizetni a virtuális gépért. Az Azure Spot virtuális gépek maximális ára amerikai dollárban (USD) beállítható legfeljebb 5 tizedesjegy használatával. Az érték lehet például `0.98765` egy 0,98765 USD/óra maximális ára. Ha a maximális árat a következőre adja meg: , a virtuális gépet a rendszer nem fogja az ár alapján `-1` kiszűkülni. A virtuális gép ára az Azure Spot virtuális gép aktuális ára vagy egy standard virtuális gép ára lesz, amely a kapacitás és a kvóta rendelkezésre áll. A maximális ár beállításával kapcsolatos további információkért lásd: [Azure Spot Virtual Machines – Díjszabás.](../spot-vms.md#pricing)

Az Azure-beli kihasznált virtuális gépek Azure CLI-t használó létrehozási folyamata megegyezik a rövid [útmutatóban található cikkel.](./quick-create-cli.md) Egyszerűen adja hozzá a "--priority Spot" paramétert, állítsa a et Felszabadítás (ez az alapértelmezett beállítás) vagy a (alapértelmezett érték) értékre, és adja meg a maximális árat vagy `--eviction-policy` `Delete` a `-1` értéket. 


## <a name="install-azure-cli"></a>Telepítse az Azure CLI-t

Azure Spot-Virtual Machines az Azure CLI 2.0.74-es vagy újabb verziójával kell futnia. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

Jelentkezzen be az Azure-ba [az az login használatával.](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Azure-beli spot virtuális gép létrehozása

Ez a példa bemutatja, hogyan helyezhet üzembe egy Olyan Linux rendszerű azure-beli virtuális gépet, amely nem lesz az ár alapján lesz kiszűkülve. A kiépítési szabályzat úgy van beállítva, hogy felszabadítsa a virtuális gépet, hogy később újraindítható legyen. Ha törölni szeretné a virtuális gépet és a mögöttes lemezt a virtuális gép kieséskor, állítsa a `--eviction-policy` következőre: `Delete` .

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



A virtuális gép létrehozása után lekérdezéssel láthatja az erőforráscsoportban található összes virtuális gép maximális számlázási árát.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Kiesés szimulálása

A REST, a PowerShell vagy a parancssori felület használatával szimulálhatja egy Azure-beli kihasznált virtuális gép kiesését annak tesztelésére, hogy az alkalmazás milyen jól reagál a hirtelen kiesésre.

A legtöbb esetben a következőt kell használnia: REST API [Virtual Machines –](/rest/api/compute/virtualmachines/simulateeviction) A kilakoltatás szimulálása az alkalmazások automatizált teszteléséhez. A REST esetén a `Response Code: 204` azt jelenti, hogy a szimulált kiesés sikeres volt. A szimulált kieséseket kombinálhatja az ütemezett eseményszolgáltatással, így automatizálhatja, hogy az alkalmazás hogyan reagáljon a virtuális gép kiestekor. [](scheduled-events.md)

Az ütemezett eseményeket az Azure Friday – Az Azure Scheduled Events virtuális gépek karbantartásának előkészítésével kapcsolatos [videóban láthatja.](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)


### <a name="quick-test"></a>Gyorsteszt

A szimulált kiesés viselkedésének gyors teszteléséhez nézzük végig az ütemezett eseményszolgáltatás lekérdezésén, és nézzük meg, hogyan néz ki, ha az Azure CLI használatával szimulál egy kiépítést.

Az ütemezett eseményszolgáltatás engedélyezve van a szolgáltatáshoz, amikor először kér eseményeket. 

Távolról nyissa meg a virtuális gépet, majd nyisson meg egy parancssort. 

A virtuális gép parancssorában írja be a következőt:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ez az első válasz akár 2 percet is igénybe vehet. Ettől a képernyőtől szinte azonnal meg kell jelenni a kimenet.

Egy olyan számítógépről, amelyre telepítve van az Azure CLI (a helyi gépéhez hasonló), szimulálja a kiesést [az az vm simulate-eviction használatával.](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction) Cserélje le az erőforráscsoport és a virtuális gép nevét a saját nevére. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

A válasz kimenete akkor `Status: Succeeded` lesz, ha a kérés sikeresen le lett küldve.

Gyorsan visszamehet a távoli kapcsolatra a spot virtuális géphez, és újra lekérdezheti Scheduled Events végpontot. Ismételje meg a következő parancsot, amíg meg nem kap egy kimenetet, amely további információkat tartalmaz:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Amikor az ütemezett eseményszolgáltatás megkapja a kiesésről szóló értesítést, a következőre hasonló választ kap:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Láthatja, hogy `"EventType":"Preempt"` , és az erőforrás a virtuális gép erőforrása. `"Resources":["myspotvm"]` 

Azt is láthatja, hogy a rendszer mikor fogja bezárni a virtuális gépet. A virtuális gépet a rendszer nem zárja ki a megadott időpont előtt, így ez lesz az az időszak, amikor az alkalmazás elegánsan bezárja a `"NotBefore"` virtuális gépet.


## <a name="next-steps"></a>Következő lépések

Azure-beli kihasznált virtuális gépet a Azure PowerShell [,](../windows/spot-powershell.md) [a portál](../spot-portal.md)vagy egy sablon használatával [is létrehozhat.](spot-template.md)

Az Azure Spot virtuális géppel kapcsolatos információkért lekérdezheti az aktuális díjszabási információkat az [Azure Kiskereskedelmi](/rest/api/cost-management/retail-prices/azure-retail-prices) árak API-jának használatával. A `meterName` és a egyaránt `skuName` tartalmazni fogja a `Spot` következőt: .

Ha hibába ütközik, tekintse meg a [hibakódokat.](../error-codes-spot.md)
