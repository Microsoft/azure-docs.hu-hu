---
title: Availability Zonest használó Azure-méretezési csoport létrehozása
description: Ismerje meg, hogyan hozhat létre olyan Azure-beli virtuálisgép-méretezési csoportokat, amelyek a Availability Zonest használják az kimaradások nagyobb mértékű redundancia érdekében
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c5ddd5846be91e9fc99a251d6ad45ade8bde2937
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016658"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Availability Zonest használó virtuálisgép-méretezési csoport létrehozása

A virtuálisgép-méretezési csoportok adatközpont-szintű meghibásodások elleni ellátásához létrehozhat egy méretezési csoportot Availability Zones között. A Availability Zones támogató Azure-régiók legalább három különálló zónával rendelkeznek, amelyek mindegyike saját független áramforrással, hálózattal és hűtéssel rendelkezik. További információ: [Availability Zones áttekintése](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Ha regionális (nem zónákból álló) méretezési készletet telepít egy vagy több zónába az API *2017-12-01*-es verziójával, a következő rendelkezésre állási lehetőségek érhetők el:
- Maximális terjesztés (platformFaultDomainCount = 1)
- Statikus rögzített terjesztés (platformFaultDomainCount = 5)
- A tárolási lemezek tartalék tartományával igazított elosztás (platforFaultDomainCount = 2 vagy 3)

A maximális terjesztéssel a méretezési csoport a lehető legtöbb tartalék tartományba terjeszti a virtuális gépeket az egyes zónákon belül. Ez a terjesztés több vagy kevesebb, mint öt tartalék tartományba kerülhet. A statikus rögzített terjesztéssel a méretezési csoport minden zónájában pontosan öt tartalék tartományba helyezi át a virtuális gépeket. Ha a méretezési csoport nem talál öt különálló tartalék tartományt egy zónában a foglalási kérelem kielégítése érdekében, a kérelem meghiúsul.

Javasoljuk, hogy a legtöbb számítási feladathoz a **maximális terjesztéssel végezze el a telepítést**, mivel ez a módszer a lehető legjobb terjesztést biztosítja a legtöbb esetben. Ha a replikák különböző hardveres elkülönítési egységekben való elosztására van szükség, javasoljuk, hogy az egész Availability Zones, és az egyes zónákon belüli maximális eloszlást használja.

> [!NOTE]
> A maximális terjesztéssel csak egy tartalék tartomány jelenik meg a méretezési csoport virtuálisgép-példányának nézetében és a példány metaadataiban, függetlenül attól, hogy a virtuális gépek hány tartalék tartomány között oszlanak meg. Az egyes zónákon belüli terjesztés implicit.

### <a name="placement-groups"></a>Elhelyezési csoportok

Méretezési csoport központi telepítésekor lehetősége van arra is, hogy rendelkezésre állási zónaként vagy több zónán kívül egyetlen [elhelyezési csoportot](./virtual-machine-scale-sets-placement-groups.md) telepítsen. A regionális (nem zónákra épülő) méretezési csoportok esetében a választás az, hogy egyetlen elhelyezési csoport legyen a régióban, vagy több a régióban. A legtöbb számítási feladathoz több elhelyezési csoportot is ajánlunk, ami nagyobb skálázást tesz lehetővé. Az API *2017-12-01*-es verziójában a méretezés beállítja az alapértelmezett értéket több elhelyezési csoport számára az egyzónás és a többzónás méretezési csoportokhoz, de alapértelmezés szerint az egyhelyes csoportok számára a regionális (nem zónákra kiterjedő) méretezési csoportok esetében.

> [!NOTE]
> Ha a maximális terjesztést használja, több elhelyezési csoportot kell használnia.

### <a name="zone-balancing"></a>Zónák terheléselosztása

Végül a több zónában üzembe helyezett méretezési csoportok esetében lehetősége van arra is, hogy a "legjobb erőkifejtési zóna egyenlege" vagy a "szigorú zónák egyenlege" lehetőséget választja. A méretezési csoport "kiegyensúlyozottnak" minősül, ha minden zónában azonos számú virtuális \\ gép vagy +-1 virtuális gép található a méretezési csoport minden más zónájában. Például:

- Egy 2 virtuális géppel rendelkező méretezési csoport az 1. zónában, 3 virtuális gép a 2. zónában, a 3. zónában 3 virtuális gép pedig kiegyensúlyozottnak számít. Csak egy, eltérő virtuálisgép-számmal rendelkező zóna van, és csak 1 kisebb, mint a többi zóna. 
- Egy 1 virtuális géppel rendelkező méretezési csoport az 1. zónában, 3 virtuális gép a 2. zónában és 3 virtuális gép a 3. zónában kiegyensúlyozatlan számít. 1. zóna a 2. és 3. zónánál kevesebb virtuális gépet tartalmaz.

Lehetséges, hogy a méretezési csoportba tartozó virtuális gépek létrehozása sikeresen megtörtént, de az ezeken a virtuális gépeken lévő bővítmények nem lesznek telepítve. Ezek a virtuális gépek a bővítmények meghibásodása esetén is megmaradnak, ha meghatározza, hogy a méretezési csoport kiegyensúlyozott-e. Ilyen például az 1. zónában 3 virtuális géppel rendelkező méretezési csoport, a 2. zónában 3 virtuális gép és 3 virtuális gép a 3. zónában akkor is, ha az összes bővítmény meghiúsult az 1. zónában, és az összes bővítmény sikeres volt a 2. és 3. zónában.

A méretezési csoport a legjobb megoldás, ha az egyensúly fenntartása mellett megkísérli a skálázást és a méretezést. Ha azonban valamilyen okból ez nem lehetséges (például ha az egyik zóna leáll, a méretezési csoport nem tud új virtuális gépet létrehozni az adott zónában), a méretezési csoport lehetővé teszi, hogy az ideiglenes egyensúlyhiány sikeresen be-vagy kiskálázásra kerüljön. A további kibővített kísérletek esetén a méretezési csoport olyan virtuális gépeket hoz létre a zónákhoz, amelyeknek több virtuális gépre van szükségük a méretezési csoport kiegyensúlyozásához. Hasonlóképpen, a próbálkozások későbbi skálázásakor a méretezési csoport eltávolítja a virtuális gépeket olyan zónákból, amelyeknek kevesebb virtuális gépre van szükségük a méretezési csoport kiegyensúlyozásához. A "szigorú zónák egyenlege" esetén a méretezési csoport nem tesz elérhetővé vagy kicsinyíti a méretezési kísérleteket, ha ezzel kiegyensúlyozatlanságot okozna.

A legjobb erőfeszítést használó zónák egyenlegének használatához állítsa  a zoneBalance *hamis* értékre. Ez a beállítás az API *2017-12-01*-es verziójának alapértelmezett értéke. A zónák szigorú elosztásához állítsa a *zoneBalance* *igaz* értékre.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Egyetlen zónából és zónából – redundáns méretezési csoportok

Virtuálisgép-méretezési csoport telepítésekor dönthet úgy, hogy egyetlen rendelkezésre állási zónát használ egy régióban vagy több zónában.

Ha egyetlen zónában hoz létre méretezési készletet, akkor szabályozhatja, hogy az összes virtuálisgép-példány melyik zónában fusson, és a méretezési csoport felügyelt, és csak az adott zónán belüli autoskálázást hajtja végre. Egy zóna – redundáns méretezési csoport lehetővé teszi, hogy egyetlen méretezési készletet hozzon létre, amely több zónára terjed ki. A virtuálisgép-példányok létrehozásakor alapértelmezés szerint egyenlőek a zónák között. Ha az egyik zónában megszakítás történik, a méretezési csoport nem lesz automatikusan felskálázás a kapacitás növelésére. Az ajánlott eljárás az, ha az autoskálázási szabályokat CPU vagy memóriahasználat alapján konfigurálja. Az automatikus skálázási szabályok lehetővé teszik, hogy a méretezési csoport az adott zónában lévő virtuálisgép-példányok elvesztésére reagáljon azáltal, hogy az új példányokat a többi működési zónában fel kell méretezni.

Availability Zones használatához a méretezési csoportnak egy [támogatott Azure-régióban](../availability-zones/az-region.md)kell létrehoznia. Availability Zonest használó méretezési csoport létrehozása az alábbi módszerek egyikével végezhető el:

- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-sablonok](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A rendelkezésre állási zónát használó méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-portal.md)részletezett eljárással. Egy támogatott Azure-régió kiválasztásakor létrehozhat egy méretezési készletet egy vagy több elérhető zónában, ahogy az alábbi példában is látható:

![Méretezési csoport létrehozása egyetlen rendelkezésre állási zónában](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

A méretezési csoport és a támogató erőforrások, például az Azure Load Balancer és a nyilvános IP-cím, az Ön által megadott egyetlen zónában jönnek létre.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

A rendelkezésre állási zónát használó méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-cli.md)részletezett eljárással. A Availability Zones használatához létre kell hoznia a méretezési csoportját egy támogatott Azure-régióban.

Adja hozzá a `--zones` paramétert az az [vmss Create](/cli/azure/vmss) parancshoz, és adja meg a használni kívánt zónát (például *1*., *2*. vagy *3*. zóna). Az alábbi példa egy *myScaleSet* nevű egyzónás méretezési csoport létrehozását hozza létre az *1*. zónában:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Az egyzónás méretezési csoport és a hálózati erőforrások teljes példáját ebben a [CLI-parancsfájlban](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh) találhatja.

### <a name="zone-redundant-scale-set"></a>Zóna – redundáns méretezési csoport

Zóna – redundáns méretezési csoport létrehozásához *szabványos* SKU nyilvános IP-címet és terheléselosztó-t kell használnia. A bővített redundancia érdekében a *standard* SKU zóna-redundáns hálózati erőforrásokat hoz létre. További információ: [Azure Load Balancer standard áttekintés](../load-balancer/load-balancer-overview.md) és [standard Load Balancer és Availability Zones](../load-balancer/load-balancer-standard-availability-zones.md).

Zóna – redundáns méretezési csoport létrehozásához adja meg a paraméterrel több zónát `--zones` . A következő példa létrehoz egy *myScaleSet* nevű zóna-redundáns méretezési készletet az *1., 2. és 3*. zónában:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

A rendszer néhány percet vesz igénybe a méretezési csoport erőforrásainak és virtuális gépei létrehozásához és konfigurálásához a megadott zóná (k) ban. A zóna-redundáns méretezési csoport és a hálózati erőforrások teljes példáját ebben a [CLI-parancsfájlban](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh) találhatja.

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A Availability Zones használatához létre kell hoznia a méretezési csoportját egy támogatott Azure-régióban. Adja hozzá a `-Zone` paramétert a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancshoz, és adja meg a használni kívánt zónát (például *1*., *2*. vagy *3*. zóna).

A következő példa egy *myScaleSet* nevű egyzónás méretezési csoport létrehozását hozza létre az *USA 2. keleti* régiójában *1*. A rendszer automatikusan létrehozza az Azure-beli hálózati erőforrásokat a virtuális hálózathoz, a nyilvános IP-címhez és a terheléselosztóhoz. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zóna – redundáns méretezési csoport

Zóna – redundáns méretezési csoport létrehozásához adja meg a paraméterrel több zónát `-Zone` . A következő példa létrehoz egy *myScaleSet* nevű zóna-redundáns méretezési készletet az *USA 2. keleti* régiójában, *1, 2 és 3* zónában. A zóna – a virtuális hálózat, a nyilvános IP-cím és a terheléselosztó redundáns Azure hálózati erőforrásai automatikusan létrejönnek. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Használjon Azure Resource Manager-sablonokat

A rendelkezésre állási zónát használó méretezési csoport létrehozásának folyamata megegyezik a [Linux](quick-create-template-linux.md) vagy [Windows rendszerhez](quick-create-template-windows.md)készült első lépések című cikkben ismertetett eljárással. A Availability Zones használatához létre kell hoznia a méretezési csoportját egy támogatott Azure-régióban. Adja hozzá a `zones` tulajdonságot a sablonban a *Microsoft. számítási/virtualMachineScaleSets* erőforrástípus számára, és adja meg a használni kívánt zónát (például *1*., *2*. vagy *3*. zóna).

A következő példa létrehoz egy *myScaleSet* nevű Linux-alapú egyzónás méretezési készletet az *USA 2. keleti* régiójában *1*. zónában:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Az egyzónás méretezési csoportra és a hálózati erőforrásokra vonatkozó teljes példa: [Ez a példa Resource Manager-sablon](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zóna – redundáns méretezési csoport

Zóna – redundáns méretezési csoport létrehozásához adjon meg több értéket a `zones` tulajdonságban a *Microsoft. számítás/virtualMachineScaleSets* erőforrástípus mezőben. A következő példa létrehoz egy *myScaleSet* nevű zóna-redundáns méretezési készletet az *USA 2. keleti* régiójában, *1, 2, 3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Ha létrehoz egy nyilvános IP-címet vagy egy terheléselosztó-t, adja meg a *"SKU": {"Name": "standard"} "* tulajdonságot a zóna redundáns hálózati erőforrásainak létrehozásához. A forgalom engedélyezéséhez létre kell hoznia egy hálózati biztonsági csoportot és szabályokat is. További információ: [Azure Load Balancer standard áttekintés](../load-balancer/load-balancer-overview.md) és [standard Load Balancer és Availability Zones](../load-balancer/load-balancer-standard-availability-zones.md).

A zóna-redundáns méretezési csoport és a hálózati erőforrások teljes példája: [Ez a példa Resource Manager-sablon](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta a méretezési csoportot egy rendelkezésre állási zónában, megtudhatja, hogyan [telepíthet alkalmazásokat virtuálisgép-méretezési](tutorial-install-apps-cli.md) csoportokon, illetve hogyan [használhatja az autoscalet a virtuálisgép-méretezési csoportokkal](tutorial-autoscale-cli.md).
