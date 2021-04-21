---
title: Oktatóanyag – Méretezési készlet automatikus méretezése az Azure CLI használatával
description: Ismerje meg, hogyan skálázhat automatikusan virtuálisgép-méretezési csoportokat az Azure CLI használatával a processzorterhelés növekedésének vagy csökkenésének megfelelően
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/18/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: b7fdf6d4893a6f6a970223671b28fdae6db3ef3d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762982"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Oktatóanyag: Virtuálisgép-méretezési csoport automatikus skálázása az Azure CLI használatával

Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Automatikus skálázás használata méretezési csoportokkal
> * Automatikus skálázási szabályok létrehozása és használata
> * Virtuálisgép-példányok és automatikus skálázás-aktiválási szabályok terhelési tesztje
> * Visszaméretezés, ha az igény csökken

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Az oktatóanyaghoz az Azure CLI 2.0.32-es vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal az alábbiak szerint:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) paranccsal. A következő példa *2-es* példányszámmal egy méretezési csoportot hoz létre, valamint SSH-kulcsokat is, ha azok még nem léteznének:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>Automatikus skálázási profil meghatározása

Az automatikus skálázás méretezési csoportban történő engedélyezéséhez először határozza meg az automatikus skálázási profilt. Ez a profil határozza meg a méretezési csoport alapértelmezett, minimális és maximális kapacitását. Ezekkel a korlátokkal szabályozhatja a költségeket a virtuálisgép-példányok folyamatos létrehozásával, valamint az elfogadható teljesítmény és a leskálás során fennmaradó példányok minimális számának kiegyensúlyozása között. Automatikus skálázási profilt az [az monitor autoscale create](/cli/azure/monitor/autoscale#az_monitor_autoscale_create) paranccsal hozhat létre. A következő példa a virtuálisgép-példányok alapértelmezett és egyben minimális (*2*), valamint a maximális (*10*) értékét állítja be:

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>Automatikus felskálázási szabály létrehozása

Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

Hozzunk létre egy szabályt az [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az_monitor_autoscale_rule_create) paranccsal a méretezési csoportban lévő virtuálisgép-példányok növelésére, ha az átlagos processzorterhelés 5 percen keresztül meghaladja a 70%-ot. A szabály aktiválásakor a virtuálisgép-példányok száma hárommal nő.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>Automatikus leskálázási szabály létrehozása

Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

Hozzunk létre egy másik szabályt az [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az_monitor_autoscale_rule_create) paranccsal a méretezési csoportban lévő virtuálisgép-példányok csökkentésére, ha az átlagos processzorterhelés ezután 5 percen keresztül nem éri el a 30%-ot. Az alábbi példa egy olyan szabályt határoz meg, amely a virtuálisgép-példányok számát eggyel skálázza le horizontálisan:

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

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

A méretezési csoportban lévő virtuálisgép-példányok számának monitorozásához használja a **watch** segédprogramot. Az egyes virtuálisgép-példányokon 5 percet vesz igénybe, mire az automatikus skálázási szabályok megkezdik a horizontális felskálázási folyamatot a **stress** segédprogram által létrehozott processzorterhelésre válaszul:

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
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Amint a **stress** segédprogram leáll a kezdeti virtuálisgép-példányokon, az átlagos processzorterhelés visszatér a normális szintre. Újabb 5 perc elteltével az automatikus méretezési szabályok horizontálisan leskálázzák a virtuálisgép-példányok számát. A horizontális leskálázási műveletek először a legmagasabb azonosítóval rendelkező virtuálisgép-példányokat távolítják el. Ha egy méretezési csoport az Availability Sets vagy az Availability Zones funkciót használja, a horizontális leskálázási műveletek egyenletesen lesznek elosztva a virtuálisgép-példányok között. Az alábbi kimeneti példa egy virtuálisgép-példány törlését mutatja, ahogy a méretezési csoport automatikusan leskálázódik:

```output
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Lépjen ki a *watch* segédprogramból a `Ctrl-c` paranccsal. A méretezési csoport folytatja az 5 percenkénti horizontális leskálázást, és eltávolít egy virtuálisgép-példányt, amíg el nem éri a minimális két példányos értéket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A méretezési csoport és a további erőforrások eltávolításához törölje az erőforráscsoportot és annak összes erőforrását [az az group delete parancsokkal.](/cli/azure/group) A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

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
