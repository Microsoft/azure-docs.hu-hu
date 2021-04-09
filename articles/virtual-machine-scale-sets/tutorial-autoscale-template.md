---
title: Oktatóanyag – méretezési csoport autoskálázása Azure-sablonokkal
description: Ismerje meg, hogyan méretezhet automatikusan egy virtuálisgép-méretezési csoportot az Azure Resource Manager-sablonok segítségével a processzorterhelés növekedésének vagy csökkenésének megfelelően
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 88cec878ca5d3ccab3a232888ff3a3c0b0faa1db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705251"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Oktatóanyag: Virtuálisgép-méretezési csoport automatikus méretezése Azure-sablonnal
Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Automatikus skálázás használata méretezési csoportokkal
> * Automatikus skálázási szabályok létrehozása és használata
> * Virtuálisgép-példányok és automatikus skálázás-aktiválási szabályok terhelési tesztje
> * Visszaméretezés, ha az igény csökken

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.29 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 


## <a name="define-an-autoscale-profile"></a>Automatikus skálázási profil meghatározása
Az Azure-sablon automatikus skálázási profilját a *Microsoft.insights/autoscalesettings* erőforrás-szolgáltató segítségével határozhatja meg. A *profil* részletes információkat tartalmaz a méretezési csoport kapacitásáról és az összes hozzárendelt szabályról. Az alábbi példa meghatározza az *Autoscale by percentage based on CPU usage* (Automatikus méretezés százalékos arányban a processzorhasználat függvényében) nevű profilt, és *2* virtuálisgép-példányt állít be alapértelmezett és minimális kapacitásként, és *10* példányra állítja a maximumot:

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Automatikus horizontális felskálázási szabály meghatározása
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

Az alábbi példában egy olyan szabály van meghatározva, amely megnöveli a méretezési csoportban lévő virtuálisgép-példányok számát, ha az átlagos processzorterhelés 5 percnél hosszabb időtartamig nagyobb, mint 70%. A szabály aktiválásakor a virtuálisgép-példányok száma hárommal nő.

Ez a szabály az alábbi paramétereket tartalmazza:

| Paraméter         | Magyarázat                                                                                                         | Érték           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | A méretezési csoporthoz tartozó műveletek monitorozásának és alkalmazásának teljesítménymutatója.                                                   | Százalékos processzorhasználat  |
| *timeGrain*       | Az elemzendő metrikák gyűjtési gyakorisága.                                                                   | 1 perc        |
| *timeAggregation* | Meghatározza, hogy az összegyűjtött metrikák hogyan legyenek összesítve az elemzéshez.                                                | Átlag         |
| *timeWindow*      | A mérőszám és a küszöbértékek összehasonlítása előtt monitorozott időtartam.                                   | 5 perc       |
| *üzemeltető*        | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                     | Nagyobb, mint    |
| *küszöb*       | Az érték, amely esetén az automatikus skálázási szabály aktivál egy műveletet.                                                      | 70%             |
| *irányba*       | Meghatározza, hogy a méretezési csoport a szabály alkalmazásakor horizontálisan le- vagy fel legyen skálázva.                                              | Növelés        |
| *típusa*            | Azt jelöli, hogy a virtuálisgép-példányok számát egy megadott értékre kell módosítani.                                    | Darabszám megváltoztatása    |
| *value*           | Hány virtuálisgép-példány legyen horizontálisan le- vagy felskálázva a szabály alkalmazásakor.                                             | 3               |
| *cooldown*        | Mennyi idő teljen el a szabály újbóli alkalmazása előtt, hogy az automatikus skálázási műveletek kifejthessék a hatásukat. | 5 perc       |

Az alábbi szabály lesz hozzáadva az előző szakaszban található *Microsoft.insights/autoscalesettings* erőforrás-szolgáltató profil szakaszához:

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Automatikus horizontális leskálázási szabály meghatározása
Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

Az alábbi példában egy olyan szabályt határozunk meg, amely horizontálisan leskálázza a virtuálisgép-példányok számát egyel, ha az átlagos processzorhasználat 5 percnél hosszabb ideig 30% alá esik. Ezt a szabályt az előző horizontális felskálázási szabály után kell hozzáadni az automatikus skálázási profilhoz:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Automatikus skálázást végző méretezési csoport létrehozása
A mintasablon segítségével hozzunk létre egy méretezési csoportot, és alkalmazzuk az automatikus skálázási szabályokat. [Áttekintheti az elkészült sablont](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json), vagy [megtekintheti a sablon *Microsoft.insights/autoscalesettings* erőforrás-szolgáltató](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220) szakaszát.

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési [csoportot az az Deployment Group Create](/cli/azure/deployment/group)paranccsal. Amikor a rendszer kéri, adja meg az egyes virtuálisgép-példányhoz hitelesítő adatként használt saját felhasználó nevét, például az *azureuser* nevet és a hozzá tartozó jelszót:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="generate-cpu-load-on-scale-set"></a>Processzorterhelés létrehozása a méretezési csoportban
Az automatikus skálázási szabályok teszteléséhez hozzon létre némi processzorterhelést a méretezési csoportban lévő virtuálisgép-példányokhoz. Ezen szimulált processzorterhelés hatására az automatikus méretezési szabályok horizontálisan felskáláznak, és megnövelik a virtuálisgép-példányok számát. A szimulált processzorterhelés ezt követő csökkentésével az automatikus méretezési szabály horizontálisan leskáláz, és csökkenti a virtuálisgép-példányok számát.

Először listázza ki a méretezési csoportban lévő virtuálisgép-példányokhoz való csatlakozáshoz használható címet és portokat az [az vmss list-instance-connection-info](/cli/azure/vmss) paranccsal:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Az alábbi kimenetpélda a példány nevét, a terheléselosztó nyilvános IP-címét és azt a portszámot mutatja, amelyre a hálózati címfordítási (NAT) szabályok továbbítják a forgalmat:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH-n keresztül csatlakozzon az első virtuálisgép-példányhoz. Adja meg saját nyilvános IP-címét és portszámát a `-p` paraméterrel az ezt megelőző parancsban látható módon:

```console
ssh azureuser@13.92.224.66 -p 50001
```

A bejelentkezést követően telepítse a **stress** segédprogramot. Indítson el *10* **terhelési** feldolgozót, amelyek processzorterhelést hoznak létre. Ezek a feldolgozók *420* másodpercig futnak. Ez elegendő idő ahhoz, hogy az automatikus méretezési szabályok alkalmazzák a kívánt műveletet.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Ha a **stress** segédprogram a *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* kimenethez hasonló értékeket mutat, nyomja le az *Enter* billentyűt a parancssorhoz való visszatéréshez.

Annak megerősítéséhez, hogy a **stress** segédprogram processzorterhelést hoz létre, vizsgálja meg az aktív rendszerterhelést a **top** segédprogram segítségével:

```console
top
```

Lépjen ki a **top** segédprogramból, majd zárja be a virtuálisgép-példánnyal létesített kapcsolatot. A **stress** segédprogram továbbra is fut a virtuálisgép-példányon.

```console
Ctrl-c
exit
```

Csatlakozzon a másik virtuálisgép-példányhoz az előző [az vmss list-instance-connection-info](/cli/azure/vmss) paranccsal listázott portszám segítségével:

```console
ssh azureuser@13.92.224.66 -p 50003
```

Telepítse és futtassa a **stress** segédprogramot, majd indítson el tíz feldolgozót a második virtuálisgép-példányon.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Ismét, ha a **stress** segédprogram a *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* kimenethez hasonló értékeket mutat, nyomja le az *Enter* billentyűt a parancssorhoz való visszatéréshez.

Zárja be a második virtuálisgép-példánnyal létesített kapcsolatot. A **stress** segédprogram továbbra is fut a virtuálisgép-példányon.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Aktív automatikus skálázási szabályok monitorozása
A méretezési csoportban lévő virtuálisgép-példányok számának monitorozásához használja a **watch** segédprogramot. Az egyes virtuálisgép-példányokon 5 percet vesz igénybe, mire az automatikus skálázási szabályok megkezdik a horizontális felskálázási folyamatot, válaszul a **stress** segédprogram által létrehozott processzorteljesítményre:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Ha elérte a processzor küszöbértékét, az automatikus méretezési szabályok megnövelik a méretezési csoportban lévő virtuálisgép-példányok számát. Az alábbi kimenet három virtuális gép létrejöttét mutatja, amint a méretezési csoport automatikusan felskálázódik:

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Amint a **stress** segédprogram leáll a kezdeti virtuálisgép-példányokon, az átlagos processzorterhelés visszatér a normális szintre. Újabb 5 perc elteltével az automatikus méretezési szabályok horizontálisan leskálázzák a virtuálisgép-példányok számát. A horizontális leskálázási műveletek először a legmagasabb azonosítóval rendelkező virtuálisgép-példányokat távolítják el. Ha egy méretezési csoport az Availability Sets vagy az Availability Zones funkciót használja, a horizontális leskálázási műveletek egyenletesen lesznek elosztva a virtuálisgép-példányok között. Az alábbi kimeneti példa egy virtuálisgép-példány törlését mutatja, ahogy a méretezési csoport automatikusan leskálázódik:

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Lépjen ki a *watch* segédprogramból a `Ctrl-c` paranccsal. A méretezési csoport folytatja az 5 percenkénti horizontális leskálázást, és eltávolít egy virtuálisgép-példányt, amíg el nem éri a minimális két példányos értéket.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és további erőforrások eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást az [az group delete](/cli/azure/group) paranccsal:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan lehet automatikusan horizontálisan le- illetve felskálázni egy méretezési csoportot az Azure CLI használatával:

> [!div class="checklist"]
> * Automatikus skálázás használata méretezési csoportokkal
> * Automatikus skálázási szabályok létrehozása és használata
> * Virtuálisgép-példányok és automatikus skálázás-aktiválási szabályok terhelési tesztje
> * Visszaméretezés, ha az igény csökken
