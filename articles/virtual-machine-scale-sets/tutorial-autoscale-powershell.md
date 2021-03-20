---
title: Oktatóanyag – méretezési csoport autoskálázása Azure PowerShell
description: Ismerje meg, hogyan méretezhet automatikusan virtuálisgép-méretezési csoportokat az Azure PowerShell-lel a processzorterhelés növekedésének vagy csökkenésének megfelelően.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 8ee124f866a5241620671ff84c24f3713f62efe1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078469"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Oktatóanyag: virtuálisgép-méretezési csoportok automatikus skálázása az Azure PowerShell-lel

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Automatikus skálázás használata méretezési csoportokkal
> * Automatikus skálázási szabályok létrehozása és használata
> * Virtuálisgép-példányok és automatikus skálázás-aktiválási szabályok terhelési tesztje
> * Visszaméretezés, ha az igény csökken

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Létezik egy ismert hiba, amely az Azure PowerShell-modul 6.8.1-es vagy újabb verzióját érinti, beleértve az Azure Cloud Shell aktuális verzióját is. Ez az oktatóanyag csak az Azure PowerShell-modul 6.0.0–6.8.0 közötti verziójával futtatható. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Az automatikus skálázási szabályok könnyebb létrehozása érdekében definiáljon néhány változót a méretezési csoporthoz. A következő példában változókat definiálunk a *myScaleSet* nevű méretezési csoporthoz a *myResourceGroup* nevű erőforráscsoportban az *USA keleti régiójában*. Az előfizetés-azonosítót a [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) paranccsal kérheti le. Ha a vonatkozó fiók több előfizetést is tartalmaz, a parancs csak az elsőt adja vissza. Állítsa be a neveket és előfizetés-azonosítókat a következők szerint:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Most hozzon létre egy virtuálisgép-méretezési csoportot a [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) paranccsal. A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó szabályai elosztják a 80-as TCP-porton érkező forgalmat, valamint lehetővé teszik a távoli asztali forgalmat a 3389-es, valamint a PowerShell távoli eljáráshívásokat az 5985-ös TCP-porton. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

## <a name="create-a-rule-to-autoscale-out"></a>Automatikus felskálázási szabály létrehozása
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

Hozzunk létre egy szabályt a [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) paranccsal a méretezési csoportban lévő virtuálisgép-példányok növelésére, ha az átlagos processzorterhelés 5 percen keresztül meghaladja a 70%-ot. A szabály aktiválásakor a virtuálisgép-példányok száma hárommal nő.

Ez a szabály az alábbi paramétereket tartalmazza:

| Paraméter               | Magyarázat                                                                                                         | Érték          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *– MetricName*           | A méretezési csoporthoz tartozó műveletek monitorozásának és alkalmazásának teljesítménymutatója.                                                   | Százalékos processzorhasználat |
| *– TimeGrain*            | Az elemzendő metrikák gyűjtési gyakorisága.                                                                   | 1 perc       |
| *-MetricStatistic*      | Meghatározza, hogy az összegyűjtött metrikák hogyan legyenek összesítve az elemzéshez.                                                | Átlag        |
| *– TimeWindow*           | A mérőszám és a küszöbértékek összehasonlítása előtt monitorozott időtartam.                                   | 5 perc      |
| *– Operátor*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                     | Nagyobb, mint   |
| *– Küszöbérték*            | Az érték, amely esetén az automatikus skálázási szabály aktivál egy műveletet.                                                      | 70%            |
| *-ScaleActionDirection* | Meghatározza, hogy a szabály alkalmazásakor a méretezési csoport fel- vagy leskáláz.                                             | Növelés       |
| *-ScaleActionScaleType* | Azt jelöli, hogy a virtuálisgép-példányok számát egy megadott értékre kell módosítani.                                    | Darabszám megváltoztatása   |
| *-ScaleActionValue*     | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                            | 3              |
| *-ScaleActionCooldown*  | Mennyi idő teljen el a szabály újbóli alkalmazása előtt, hogy az automatikus skálázási műveletek kifejthessék a hatásukat. | 5 perc      |

A következő példában egy *myRuleScaleOut* nevű objektumot hozunk létre, amely a vertikális felskálázási szabályt tárolja. A *-MetricResourceId* az előfizetés-azonosítóhoz, az erőforráscsoport-névhez és méretezésicsoport-névhez korábban definiált változókat alkalmazza:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Automatikus leskálázási szabály létrehozása
Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

Hozzunk létre egy másik szabályt a [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) paranccsal a méretezési csoportban lévő virtuálisgép-példányok csökkentésére, ha az átlagos processzorterhelés ezután 5 percen keresztül nem éri el a 30%-ot. A szabály indításakor a virtuálisgép-példányok száma eggyel csökken. A következő példa egy *myRuleScaleDown* nevű objektumot hoz létre, amely tartalmazza ezt a leskálázási szabályt. A *-MetricResourceId* az előfizetés-azonosítóhoz, az erőforráscsoport-névhez és méretezésicsoport-névhez korábban definiált változókat alkalmazza:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Automatikus skálázási profil meghatározása
Profilok létrehozásával társíthatja az automatikus skálázási szabályokat a méretezési csoportokhoz. Az automatikus skálázási profil határozza meg a méretezési csoport alapértelmezett, minimális és maximális kapacitását, és társítja az automatikus skálázási szabályokat. Automatikus skálázási profilt a [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile) paranccsal hozhat létre. Az alábbi példa az alapértelmezett és a minimális kapacitást, a *2* virtuálisgép-példányok számát és a maximum *10* értéket állítja be. Ezután csatolja az előző lépésekben létrehozott horizontális fel- és leskálázási szabályokat:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>Az autoskálázási profil alkalmazása méretezési csoportra
Az utolsó lépés az automatikus skálázási profil alkalmazása a méretezési csoportra. A méretezési csoport ezután képes lesz az automatikus le- és felskálázásra az alkalmazás igényei szerint. Az automatikus skálázási profil az [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) paranccsal alkalmazható az alábbiak szerint:

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Processzorterhelés létrehozása a méretezési csoportban
Az automatikus skálázási szabályok teszteléséhez hozzon létre némi processzorterhelést a méretezési csoportban lévő virtuálisgép-példányokhoz. Ezen szimulált processzorterhelés hatására az automatikus méretezési szabályok horizontálisan felskáláznak, és megnövelik a virtuálisgép-példányok számát. A szimulált processzorterhelés ezt követő csökkentésével az automatikus méretezési szabály horizontálisan leskáláz, és csökkenti a virtuálisgép-példányok számát.

A méretezési csoportokban lévő virtuálisgép-példányokhoz való kapcsolódáshoz használható NAT-portok listázásához először kérje le a terheléselosztói objektumot a [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer) paranccsal. Ezután tekintse meg a bejövő NAT-szabályokat a [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) paranccsal:

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Az alábbi példakimeneten a példány neve, a terheléselosztó nyilvános IP-címe és a portszám látható, amelyeket a NAT-szabályok a forgalom továbbításához használnak:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

A szabály *neve* illeszkedik a VM-példány nevéhez, amelyet egy előző [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) parancs adott vissza. Például a *0* nevű virtuálisgép-példányhoz való csatlakozáshoz használja a *myRDPRule.0* szabályt, és kapcsolódjon az *50001*-es porthoz. Az *1* nevű virtuálisgép-példányhoz való csatlakozáshoz használja a *myRDPRule.1* szabályban lévő értéket, és kapcsolódjon az *50002*-es porthoz.

Tekintse meg a terheléselosztó nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) paranccsal.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Példa a kimenetre:

```powershell
IpAddress
---------
52.168.121.216
```

Távoli kapcsolaton keresztül csatlakozzon az első virtuálisgép-példányhoz. Adja meg a kívánt VM-példány saját nyilvános IP-címét és portszámát, amint az az előző parancsokban látható. Ha a rendszer kéri, adja meg a méretezési csoport létrehozásakor használt hitelesítő adatokat (a minta parancsaiban alapértelmezés szerint az *azureuser* és a *P \@ ssw0rd!*). Az Azure Cloud Shell használata esetén ezt a lépést egy helyi PowerShell-parancssorból vagy egy távoli asztali ügyfélről hajtsa végre. A következő példa a *0* nevű virtuálisgép-példányhoz csatlakozik:

```powershell
mstsc /v 52.168.121.216:50001
```

Miután bejelentkezett, indítsa el az Internet Explorert a tálcáról.

- Kattintson az **OK** gombra *Az ajánlott biztonsági, adatvédelmi és kompatibilitási beállítások használata* üzenet elfogadásához
- Írja be *http://download.sysinternals.com/files/CPUSTRES.zip* a címsorba.
- Mivel az Internet Explorer fokozott biztonsági beállításai engedélyezve vannak, **Hozzáadás** gombbal vegye fel a *http://download.sysinternals.com* tartományt a megbízható helyek listájára.
- Ha a rendszer a fájl letöltésére kéri, kattintson a **Megnyitás** gombra, majd válassza ki és **futtassa** a *CPUSTRES.EXE* eszközt.

A CPU-terhelés szimulálásához jelöljön be két jelölőnégyzetet az **Aktív** szálaknál. Mindkét szál **Tevékenység** legördülő menüjében válassza a *Maximum* lehetőséget. A Feladatkezelőt megnyitva győződhet meg róla, hogy a virtuális gép processzorterhelése 100%.

![A CPU Stress segédprogram terhelést hoz létre a virtuálisgép-példányon](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Hagyja nyitva a távoli asztali kapcsolat munkamenetét, és nyisson meg egy másikat. Csatlakozzon a második virtuálisgép-példányhoz az előző [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) parancsmag által listázott portszám használatával:

```powershell
mstsc /v 52.168.121.216:50002
```

Miután bejelentkezett a második VM-példányra, az előző lépések ismételt végrehajtásával töltse le és futtassa a *CPUSTRES.EXE* eszközt. Itt is indítson el két **Aktív** szálat, és állítsa a tevékenységet a *Maximum* értékre.

Annak érdekében, hogy a **CPU Stress** eszköz tovább futhasson, mindkét távoli asztali kapcsolat munkamenetet hagyja nyitva.


## <a name="monitor-the-active-autoscale-rules"></a>Aktív automatikus skálázási szabályok monitorozása
A méretezési csoportban lévő virtuálisgép-példányok számának monitorozásához használja a **while** parancsot. 5 percet vesz igénybe, hogy az automatikus skálázás a **CPUStress** által a virtuálisgép-példányok által generált CPU-terhelésre adott válaszként Felskálázási folyamattal kezdődjön:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Ha elérte a processzor küszöbértékét, az automatikus méretezési szabályok megnövelik a méretezési csoportban lévő virtuálisgép-példányok számát. Az alábbi kimenet három virtuális gép létrejöttét mutatja, amint a méretezési csoport automatikusan felskálázódik:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

Mindegyik VM-példány távoli asztali kapcsolat munkamenetében zárja be a **CPU Stress** eszközt. Az átlagos processzorterhelés a teljes méretezési csoportban visszatér a normál értékre. Újabb 5 perc elteltével az automatikus méretezési szabályok horizontálisan leskálázzák a virtuálisgép-példányok számát. A horizontális leskálázási műveletek először a legmagasabb azonosítóval rendelkező virtuálisgép-példányokat távolítják el. Ha egy méretezési csoport az Availability Sets vagy az Availability Zones funkciót használja, a horizontális leskálázási műveletek egyenletesen lesznek elosztva a virtuálisgép-példányok között. Az alábbi kimeneti példa egy virtuálisgép-példány törlését mutatja, ahogy a méretezési csoport automatikusan leskálázódik:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Lépjen ki a *while* parancsból a `Ctrl-c` használatával. A méretezési csoport folytatja az 5 percenkénti horizontális leskálázást, és eltávolít egy virtuálisgép-példányt, amíg el nem éri a minimális két példányos értéket.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és további erőforrások eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal. A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan lehet automatikusan horizontálisan le- illetve felskálázni egy méretezési csoportot az Azure PowerShell használatával:

> [!div class="checklist"]
> * Automatikus skálázás használata méretezési csoportokkal
> * Automatikus skálázási szabályok létrehozása és használata
> * Virtuálisgép-példányok és automatikus skálázás-aktiválási szabályok terhelési tesztje
> * Visszaméretezés, ha az igény csökken
