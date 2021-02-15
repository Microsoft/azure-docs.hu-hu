---
title: Azure spot virtuális gépeket használó méretezési csoport létrehozása
description: Megtudhatja, hogyan hozhat létre helyszíni virtuális gépeket használó Azure virtuálisgép-méretezési csoportokat a költségek megtakarítása érdekében.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: cynthn
ms.custom: jagaveer, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 03bf5e0ef7e6268e68139b6d73685f67d88f6231
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385931"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>Azure spot virtuális gépek virtuálisgép-méretezési csoportokhoz 

Az Azure spot on Scale sets használatával jelentős költségmegtakarítást érhet el a fel nem használt kapacitás kihasználása érdekében. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni példányokat, amikor az Azure-nak vissza kell használnia a kapacitást. Ezért a helyszíni példányok kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. Ha a méretezési csoportokban a direktszínes példányokat helyezi üzembe, az Azure csak akkor osztja ki a példányt, ha rendelkezésre áll kapacitás, de ezekhez a példányokhoz nem tartozik SLA. A direktszín-méretezési csoport egyetlen tartalék tartományban van telepítve, és nem biztosít magas rendelkezésre állású garanciákat.


## <a name="pricing"></a>Díjszabás

A helyszíni példányok díjszabása a régió és az SKU alapján változó. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) és a [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)díjszabása. 


A változó díjszabással maximális árat állíthat be az USA dollárban (USD), legfeljebb 5 tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a példány árát a rendszer nem fogja kizárni. A példány díja a helyszíni aktuális díj, vagy egy standard példány díjszabása, amely soha nem kevesebb, ha rendelkezésre áll kapacitás és kvóta.


## <a name="limitations"></a>Korlátozások

Az Azure spot esetében a következő méretek nem támogatottak:
 - B sorozat
 - Bármilyen méretű promóciós verzió (például Dv2, NV, NC, H promo-méretek)

Az Azure spot bármely régióba üzembe helyezhető, kivéve Microsoft Azure China 21Vianet.

<a name="channel"></a>

Jelenleg a következő [típusú ajánlatok](https://azure.microsoft.com/support/legal/offer-details/) támogatottak:

-   Nagyvállalati Szerződés
-   Utólagos elszámolású ajánlat kódja 003P
-   Szponzorált
- A felhőalapú szolgáltató (CSP) esetében forduljon a partnerhez

## <a name="eviction-policy"></a>Kiürítési szabályzat

A direktszínes méretezési csoportok létrehozásakor beállíthatja a kizárási házirendet a *felszabadításhoz* (alapértelmezett) vagy a *törléshez*. 

A *felszabadított* házirend áthelyezi a kizárt példányokat a leállított, lefoglalt állapotba, ami lehetővé teszi a kizárt példányok újratelepítését. Azonban nem garantálható, hogy a foglalás sikeres lesz. A delefoglalt virtuális gépek a méretezési csoport példánya kvótájának számítanak, és a mögöttes lemezek után díjat számítunk fel. 

Ha szeretné, hogy a direktszín-méretezési csoport példányai törölve legyenek a kizárásakor, beállíthatja a *törlési* szabályzatot. A törlési házirend beállításával új virtuális gépeket hozhat létre a méretezési csoport példányainak száma tulajdonság növelésével. A kizárt virtuális gépeket a rendszer a mögöttes lemezekkel együtt törli, ezért a tárterületért nem számítunk fel díjat. A méretezési csoportok automatikus skálázási funkciójának használatával automatikusan kipróbálhatja és kompenzálhatja a kizárt virtuális gépeket, azonban nem garantálható, hogy a foglalás sikeres lesz. Javasoljuk, hogy csak az automatikus méretezési funkciót használja a direktszínes méretezési csoportokon, ha a törlési szabályzatot törölni szeretné, hogy elkerülje a lemezek költségeit és a kvóták korlátait. 

A felhasználók eldönthetik, hogy a virtuális gép értesítéseit az [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)használatával kapják meg. Ez értesíti Önt, ha a virtuális gépek ki vannak zárva, és 30 másodpercen belül befejezi az összes feladatot, és leállítási feladatokat hajt végre a kizárás előtt. 

## <a name="placement-groups"></a>Elhelyezési csoportok
Az elhelyezési csoport egy Azure rendelkezésre állási készlethez hasonló szerkezet, amely saját tartalék tartományokkal és frissítési tartománnyal rendelkezik. A méretezési csoport alapértelmezés szerint egy legfeljebb 100 virtuális gép méretű elhelyezési csoportból áll. Ha a nevű méretezési csoport tulajdonság értéke `singlePlacementGroup` *false (hamis*), a méretezési csoport több elhelyezési csoportból is állhat, és 0 – 1000 virtuális gépet tartalmaz. 

> [!IMPORTANT]
> Hacsak nem használ InfiniBand-t a HPC használatával, erősen ajánlott a méretezési csoport tulajdonságot false értékre állítani, `singlePlacementGroup` hogy több elhelyezési csoport legyen a jobb skálázás a régióban vagy a zónában.  

## <a name="deploying-spot-vms-in-scale-sets"></a>Direktszínű virtuális gépek üzembe helyezése méretezési csoportokban

A helyszíni virtuális gépek méretezési csoportokon történő üzembe helyezéséhez beállíthatja az új *prioritás* jelzőt a *helyszínen*. A méretezési csoport összes virtuális gépe a következőre lesz beállítva:. A helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozásához használja az alábbi módszerek egyikét:
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-sablonok](#resource-manager-templates)

## <a name="portal"></a>Portál

A helyszíni virtuális gépeket használó méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-portal.md)részletezett eljárással. Méretezési csoport telepítésekor beállíthatja, hogy a direktszín jelzőt és a kizárási házirendet: ![ hozzon létre egy méretezési készletet a helyszíni virtuális gépekkel.](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

A helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-cli.md)részletezett eljárással. Csak adja hozzá a "--priority spot"-t, és adja hozzá a következőt: `--max-price` . Ebben a példában `-1` a (z) `--max-price` rendszert használjuk, így a példány árát nem lehet kizárni.

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

A helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-powershell.md)részletezett eljárással.
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

A helyszíni virtuális gépeket használó méretezési csoport létrehozásának folyamata megegyezik a [Linux](quick-create-template-linux.md) vagy [Windows rendszerhez](quick-create-template-windows.md)készült első lépéseket ismertető cikkben leírtakkal. 

A helyszíni sablonok üzembe helyezéséhez használja a `"apiVersion": "2019-03-01"` vagy a újabb verziót. 

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

## <a name="faq"></a>GYIK

**K:** A létrehozása után a a standard példány megegyeznek?

**A:** Igen, a helyszíni virtuális gépekhez nem biztosítunk SLA-t, és a szolgáltatás bármikor kizárható.


**K:** Mi a teendő, ha kizárja, de továbbra is kapacitásra van szüksége?

**A:** Javasoljuk, hogy a virtuális gépek helyett használjon szabványos virtuális gépeket, ha a kapacitásra azonnal szükség van.


**K:** Hogyan történik a kvóta kezelése a helyszínen?

**A:** A helyszíni példányok és a standard példányok külön kvótával rendelkeznek. A helyszíni kvóta a virtuális gépek és a méretezési csoport példányai között lesz megosztva. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).


**K:** Igényelhetek további kvótát a helyszínen?

**A:** Igen, elküldheti a kérést, hogy növelje a helyszíni virtuális gépek kvótáját a [normál kvóta-kérési folyamaton](../azure-portal/supportability/per-vm-quota-requests.md)keresztül.


**K:** Válthatok-e meglévő méretezési csoportokat a méretezési csoportok számára?

**A:** Nem, a `Spot` jelző beállítása csak a létrehozási időszakban támogatott.


**K:** Ha `low` alacsony prioritású méretezési csoportokhoz használok, érdemes inkább a használatot használni `Spot` ?

**A:** Egyelőre mind a, mind a `low` `Spot` működni fog, de a használatára való áttérést kell kezdenie `Spot` .


**K:** Létrehozhatok a normál virtuális gépekkel és a helyszíni virtuális gépekkel is rendelkező méretezési csoportokat?

**A:** Nem, a méretezési csoport legfeljebb egy prioritási típust támogat.


**K:**  Használhatom az autoskálázást a helyszíni méretezési csoportokkal?

**A:** Igen, beállíthatja az automatikus skálázási szabályokat a helyszíni méretezési csoporton. Ha a virtuális gépek ki vannak zárva, az autoscale segítségével új helyszíni virtuális gépeket hozhat létre. Ne feledje, hogy ez a kapacitás azonban nem garantált. 


**K:**  Működik az autoskálázás a kizárási házirendekkel (felszabadítás és törlés)?

**A:** Igen, de javasoljuk, hogy a kizárási szabályzatot állítsa a törlésre az autoscale használatakor. Ennek az az oka, hogy a lefoglalt példányok száma a méretezési csoport kapacitásának száma alapján történik. Az autoscale használatakor a megcélzott példányok száma gyorsan elvégezhető a delefoglalt, kizárt példányok miatt. Emellett a méretezési műveleteket a helyszíni kizárások is befolyásolhatják. A virtuálisgép-méretezési csoport példányai például a percek száma alá eshetnek a méretezési műveletek során több hely kizárása miatt. 


**K:** Hol tehetek közzé kérdéseket?

**A:** A kérdését a következő címen teheti közzé és címkézheti `azure-spot` : [Q&a](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Következő lépések

A díjszabással kapcsolatos részletekért tekintse meg a [virtuálisgép-méretezési csoport díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) .
