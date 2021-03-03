---
title: Azure spot Virtual Machinest használó méretezési csoport létrehozása
description: Ismerje meg, hogyan hozhat létre olyan Azure-beli virtuálisgép-méretezési csoportokat, amelyek az Azure spot Virtual Machinest használják a költségek megtakarítására.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 33aa553e688b595551c20e8b1432163152865537
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675017"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Azure spot Virtual Machines a virtuálisgép-méretezési csoportokhoz 

Az Azure spot Virtual Machines a méretezési csoportokon keresztül jelentős költségmegtakarítást biztosít a fel nem használt kapacitás kihasználásához. Az Azure-infrastruktúra minden olyan időpontban kizárja az Azure spot virtuálisgép-példányokat, amikor az Azure-nak szüksége van a kapacitásra. Ezért az Azure spot virtuálisgép-példányok kiválóan alkalmasak olyan munkaterhelések kezelésére, mint például a kötegelt feldolgozási feladatok, a fejlesztési/tesztelési környezetek, a nagy számítási feladatok és egyebek.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. Az Azure spot virtuálisgép-példányok méretezési csoportokon történő telepítésekor az Azure csak akkor osztja ki a példányt, ha rendelkezésre áll kapacitás, de ezekhez a példányokhoz nem tartozik SLA. Az Azure direkt virtuálisgép-méretezési csoport egyetlen tartalék tartományban van üzembe helyezve, és nem biztosít magas rendelkezésre állású garanciát.


## <a name="pricing"></a>Díjszabás

Az Azure spot virtuálisgép-példányok díjszabása a régió és az SKU alapján változó. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) és a [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)díjszabása. 


A változó díjszabással maximális árat állíthat be az USA dollárban (USD), legfeljebb öt tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a példány árát a rendszer nem fogja kizárni. A példány díja az Azure spot virtuális gép aktuális díja, vagy egy standard példány díjszabása, amely még nem kevesebb, ha rendelkezésre áll kapacitás és kvóta.


## <a name="limitations"></a>Korlátozások

Az Azure spot Virtual Machines esetében a következő méretek nem támogatottak:
 - B sorozat
 - Bármilyen méretű promóciós verzió (például Dv2, NV, NC, H promo-méretek)

Az Azure helyszíni virtuális gépek bármely régióba üzembe helyezhetők, kivéve Microsoft Azure China 21Vianet.

<a name="channel"></a>

Jelenleg a következő [típusú ajánlatok](https://azure.microsoft.com/support/legal/offer-details/) támogatottak:

-   Nagyvállalati Szerződés
-   Utólagos elszámolású ajánlat kódja 003P
-   Szponzorált
- A felhőalapú szolgáltató (CSP) esetében tekintse meg a [partneri központot](https://docs.microsoft.com/partner-center/azure-plan-get-started) , vagy forduljon közvetlenül a partneréhez.

## <a name="eviction-policy"></a>Kiürítési szabályzat

Amikor az Azure spot Virtual Machines használatával hoz létre méretezési készletet, beállíthatja a kizárási házirendet a *felszabadításhoz* (alapértelmezett) vagy a *törléshez*. 

A *felszabadított* házirend áthelyezi a kizárt példányokat a leállított, lefoglalt állapotba, ami lehetővé teszi a kizárt példányok újratelepítését. Azonban nem garantálható, hogy a foglalás sikeres lesz. A delefoglalt virtuális gépek a méretezési csoport példánya kvótájának számítanak, és a mögöttes lemezek után díjat számítunk fel. 

Ha szeretné, hogy a példányok törölve legyenek a kizárásakor, beállíthatja a *törlési* szabályzatot. A törlési házirend beállításával új virtuális gépeket hozhat létre a méretezési csoport példányainak száma tulajdonság növelésével. A kizárt virtuális gépeket a rendszer a mögöttes lemezekkel együtt törli, ezért a tárterületért nem számítunk fel díjat. A méretezési csoportok automatikus skálázási funkciójának használatával automatikusan kipróbálhatja és kompenzálhatja a kizárt virtuális gépeket, azonban nem garantálható, hogy a foglalás sikeres lesz. Javasoljuk, hogy csak az Azure-beli virtuálisgép-méretezési csoportokban használja az autoscale funkciót, ha a törlési szabályzatot a DELETE értékre állítja, hogy elkerülje a lemezek költségeit és a kvóták korlátait. 

A felhasználók bejelentkezhetnek a virtuális gépekre vonatkozó értesítések fogadására az [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)használatával. Ez értesíti Önt, ha a virtuális gépek ki vannak zárva, és 30 másodpercen belül befejezi az összes feladatot, és leállítási feladatokat hajt végre a kizárás előtt. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Próbálkozzon & visszaállítással (előzetes verzió)

Ez az új platform szintű szolgáltatás a mesterséges intelligenciával automatikusan megpróbálja visszaállítani a kizárt Azure spot virtuálisgép-példányokat egy méretezési csoporton belül a cél példányszámának fenntartása érdekében. 

> [!IMPORTANT]
> Próbálja ki, & a visszaállítás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Próbálkozzon & visszaállítási előnyökkel:
- Alapértelmezés szerint engedélyezve van, ha egy méretezési csoportba helyez üzembe egy Azure-beli helyszíni virtuális gépet.
- Megkísérli visszaállítani a kapacitás miatt kizárt Azure spot Virtual Machines.
- A visszaállított Azure spot Virtual Machines várhatóan hosszabb ideig fut, és a kapacitás kiváltása kisebb valószínűséggel történik.
- Javítja az Azure-beli helyszíni virtuális gépek élettartamát, így a számítási feladatok hosszabb ideig futnak.
- Segít Virtual Machine Scale Sets fenntartani az Azure spot Virtual Machines céljának darabszámát, hasonlóan ahhoz, hogy megőrizze az utólagos elszámolású virtuális gépekhez már meglévő Target Count funkciót.

Próbálja meg & a visszaállítást az [autoskálázást](virtual-machine-scale-sets-autoscale-overview.md)használó méretezési csoportokban. A méretezési csoportba tartozó virtuális gépek számát az autoskálázási szabályok vezérlik.

## <a name="placement-groups"></a>Elhelyezési csoportok

Az elhelyezési csoport egy Azure rendelkezésre állási készlethez hasonló szerkezet, amely saját tartalék tartományokkal és frissítési tartománnyal rendelkezik. A méretezési csoport alapértelmezés szerint egy legfeljebb 100 virtuális gép méretű elhelyezési csoportból áll. Ha a nevű méretezési csoport tulajdonság értéke `singlePlacementGroup` *false (hamis*), a méretezési csoport több elhelyezési csoportból is állhat, és 0 – 1000 virtuális gépet tartalmaz. 

> [!IMPORTANT]
> Hacsak nem használ InfiniBand-t a HPC használatával, erősen ajánlott a méretezési csoport tulajdonságot false értékre állítani, `singlePlacementGroup` hogy több elhelyezési csoport legyen a jobb skálázás a régióban vagy a zónában.  

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Azure spot Virtual Machines üzembe helyezése méretezési csoportokban

Az Azure spot Virtual Machines méretezési csoportokon történő üzembe helyezéséhez beállíthatja az új *prioritás* jelzőt a *helyszínen*. A méretezési csoport összes virtuális gépe a következőre lesz beállítva:. Az Azure spot Virtual Machines használatával létrehozott méretezési csoport létrehozásához használja az alábbi módszerek egyikét:
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-sablonok](#resource-manager-templates)

## <a name="portal"></a>Portál

Az Azure spot Virtual Machinest használó méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-portal.md)részletezett eljárással. Méretezési csoport telepítésekor beállíthatja a direktszín jelölőt és a kizárási házirendet: ![ méretezési csoport létrehozása az Azure spot Virtual machines](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

A méretezési csoport Azure spot Virtual Machines használatával történő létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-cli.md)leírtak szerint. Csak adja hozzá a "--priority spot"-t, és adja hozzá a következőt: `--max-price` . Ebben a példában `-1` a (z) `--max-price` rendszert használjuk, így a példány árát nem lehet kizárni.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

A méretezési csoport Azure spot Virtual Machines használatával történő létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-powershell.md)leírtak szerint.
Csak adja hozzá a "-priority spot" lehetőséget, és adjon meg egy- `-max-price` t a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

Az Azure spot Virtual Machinest használó méretezési csoport létrehozásának folyamata megegyezik a [Linux](quick-create-template-linux.md) vagy [Windows rendszerhez](quick-create-template-windows.md)készült első lépések című cikkben ismertetett eljárással. 

Azure-beli virtuálisgép-sablonok üzembe helyezéséhez használja a `"apiVersion": "2019-03-01"` vagy újabb verziót. 

Adja hozzá `priority` a `evictionPolicy` és a tulajdonságokat a `billingProfile` `"virtualMachineProfile":` szakaszhoz és a `"singlePlacementGroup": false,` tulajdonsághoz a `"Microsoft.Compute/virtualMachineScaleSets"` sablon szakaszához:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Ha törölni szeretné a példányt a kizárása után, módosítsa a paramétert a következőre: `evictionPolicy` `Delete` .


## <a name="simulate-an-eviction"></a>Kizárás szimulálása

Az Azure-beli helyszíni virtuális gépek [kizárását szimulálhatja](https://docs.microsoft.com/rest/api/compute/virtualmachines/simulateeviction) annak teszteléséhez, hogy az alkalmazás milyen jól reagáljon a hirtelen kizárásra. 

Cserélje le a következőt az adataira: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` azt jelenti, hogy a szimulált kizárás sikeres volt. 

## <a name="faq"></a>GYIK

**K:** A létrehozása után egy Azure-beli virtuális gép példánya azonos a standard példánnyal?

**A:** Igen, kivéve, ha az Azure spot Virtual Machineshoz nem biztosítunk SLA-t, de bármikor kizárható.


**K:** Mi a teendő, ha kizárja, de továbbra is kapacitásra van szüksége?

**A:** Javasoljuk, hogy a standard szintű virtuális gépeket az Azure spot Virtual Machines helyett használja, ha a kapacitásra azonnal szüksége van.


**K:** Hogyan történik az Azure-beli helyszíni virtuális gépekre vonatkozó kvóta kezelése?

**A:** Az Azure spot virtuálisgép-példányok és a standard példányok külön kvótákat fognak tartalmazni. Az Azure spot virtuálisgép-kvóta a virtuális gépek és a méretezési csoport példányai között lesz megosztva. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).


**K:** Igényelhetek további kvótát az Azure spot virtuális géphez?

**A:** Igen, elküldheti a kérést, hogy növelje az Azure spot Virtual Machines kvótáját a [normál kvóta-igénylési folyamaton](../azure-portal/supportability/per-vm-quota-requests.md)keresztül.


**K:** Átalakíthatók a meglévő méretezési csoportok az Azure-beli virtuálisgép-méretezési csoportokra?

**A:** Nem, a `Spot` jelző beállítása csak a létrehozási időszakban támogatott.


**K:** Ha `low` alacsony prioritású méretezési csoportokhoz használok, érdemes inkább a használatot használni `Spot` ?

**A:** Egyelőre mind a, mind a `low` `Spot` működni fog, de a használatára való áttérést kell kezdenie `Spot` .


**K:** Létrehozhatok olyan méretezési készletet, amely normál virtuális gépekkel és Azure spot Virtual Machinesokkal is rendelkezik?

**A:** Nem, a méretezési csoport legfeljebb egy prioritási típust támogat.


**K:**  Használhatom az Azure spot virtuálisgép-méretezési csoportokkal való autoskálázást?

**A:** Igen, beállíthatja az automatikus skálázási szabályokat az Azure spot virtuálisgép-méretezési csoportján. Ha a virtuális gépek ki vannak zárva, az autoscale új Azure spot Virtual Machines létrehozására is képes. Ne feledje, hogy ez a kapacitás azonban nem garantált. 


**K:**  Működik az autoskálázás a kizárási házirendekkel (felszabadítás és törlés)?

**A:** Igen, de javasoljuk, hogy a kizárási szabályzatot állítsa a törlésre az autoscale használatakor. Ennek az az oka, hogy a lefoglalt példányok száma a méretezési csoport kapacitásának száma alapján történik. Az autoscale használatakor a megcélzott példányok száma gyorsan elvégezhető a delefoglalt, kizárt példányok miatt. Emellett a méretezési műveleteket a helyszíni kizárások is befolyásolhatják. A virtuálisgép-méretezési csoport példányai például a percek száma alá eshetnek a méretezési műveletek során több hely kizárása miatt. 


**K:** Hol tehetek közzé kérdéseket?

**A:** A kérdését a következő címen teheti közzé és címkézheti `azure-spot` : [Q&a](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Következő lépések

A díjszabással kapcsolatos részletekért tekintse meg a [virtuálisgép-méretezési csoport díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) .
