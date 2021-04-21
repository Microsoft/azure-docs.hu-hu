---
title: Azure Spot-adathalmazt használó méretezési csoport Virtual Machines
description: Megtudhatja, hogyan hozhat létre Olyan Azure-beli virtuálisgép-méretezési csoportokat, amelyek az Azure Spot Virtual Machines a költségek csökkentése érdekében.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 61bb87d84b96f988ae065a70b85d445fc8b96ccf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762946"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Azure Spot Virtual Machines virtuálisgép-méretezési készletekhez 

Az Azure Spot Virtual Machines méretezési készleteken való használata lehetővé teszi, hogy jelentős költségmegtakarítás mellett használja ki a kihasználatlan kapacitásunkat. Amikor az Azure-nak bármikor szüksége van a kapacitásra, az Azure-infrastruktúra ki fogja egyenlíti az Azure Spot virtuálisgép-példányokat. Ezért az Azure Spot virtuálisgép-példányok nagyszerűen használhatók olyan számítási feladatokhoz, amelyek kezelni tudnak olyan megszakításokat, mint a kötegelt feldolgozási feladatok, a fejlesztési/tesztelési környezetek, a nagy számítási feladatok stb.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a nap napjétől és sok mástól függően változhat. Az Azure Spot virtuálisgép-példányok méretezési készletekben való üzembe helyezésekor az Azure csak akkor foglal le példányt, ha van elérhető kapacitás, de ezekhez a példányokhoz nem áll rendelkezésre SLA. Az Azure Spot virtuálisgép-méretezési csoport egyetlen tartalék tartományban van üzembe állítva, és nem nyújt magas rendelkezésre állást.


## <a name="pricing"></a>Díjszabás

Az Azure Spot virtuálisgép-példányok díjszabása régió és termékváltozat alapján változó. További információ: A [Linux és a](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) Windows [díjszabása.](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 


Változó díjszabással beállíthatja a maximális árat amerikai dollárban (USD), legfeljebb öt tizedesjegy használatával. Az érték lehet például `0.98765` egy 0,98765 USD/óra maximális ára. Ha a maximális árat a következőre adja meg: , a példány nem lesz az ár alapján `-1` lesz kiszűkülve. A példány ára az Azure Spot virtuális gép aktuális ára vagy egy standard példány ára lesz, amely a kapacitás és a kvóta rendelkezésre áll.


## <a name="limitations"></a>Korlátozások

Az Alábbi méretek nem támogatottak az Azure Spot Virtual Machines:
 - B sorozat
 - Bármilyen méretű promóciós verziók (például Dv2, NV, NC, H promóciós méretek)

Az Azure Spot virtuális gép bármely régióban üzembe helyezhető, kivéve Microsoft Azure China 21Vianet.

<a name="channel"></a>

Jelenleg a [következő ajánlattípusok](https://azure.microsoft.com/support/legal/offer-details/) támogatottak:

-   Nagyvállalati Szerződés
-   A fizetéses ajánlat kódja (003P)
-   Szponzorált (0036P és 0136P)
- Felhőszolgáltató (CSP) esetén [](/partner-center/azure-plan-get-started) tekintse meg a Partnerközpont partnerével való közvetlen kapcsolatfelvételt.

## <a name="eviction-policy"></a>Kiürítési szabályzat

Ha az Azure Spot Virtual Machines használatával hoz létre méretezési csoportokat, a kiépítési szabályzatot a *Felszabadítás* (alapértelmezett) vagy a Törlés beállításra *állíthatja.* 

A *Felszabadítás* szabályzat a kiürült példányokat a leállított-felszabadított állapotba áthelyezi, lehetővé téve a kiürült példányok ismételt üzembe állását. Azonban nem garantálható, hogy a foglalás sikeres lesz. A felszabadított virtuális gépek beleszámnak a méretezésicsoport-példányok kvótáiba, és a mögöttes lemezekért díjat számítunk fel. 

Ha azt szeretné, hogy a példányok a kiesésükkor törlődjön, a kiépítési szabályzatban a törlést *állíthatja be.* Ha a törlési szabályzat van beállítva, új virtuális gépeket hozhat létre a méretezési csoport példányszám tulajdonságának növelésével. A rendszer a mögöttes lemezekkel együtt törli az törölt virtuális gépeket, ezért nem számítunk fel díjat a tárterületért. A méretezési készletek automatikus skálázásának funkcióját is használhatja a kieső virtuális gépek automatikus kompenzációja érdekében, azonban nem garantálható, hogy a foglalás sikeres lesz. Javasoljuk, hogy csak akkor használja az automatikus skálázási funkciót az Azure-beli spot virtuálisgép-méretezési készleteken, ha a törlésre vonatkozó kilakoltatási szabályzatot a lemezek költségeinek elkerülése és a kvótakorlátok túllépése érdekében állítja be. 

A felhasználók az Azure-portálon keresztül fogadhatják a virtuális gépeken [Scheduled Events.](../virtual-machines/linux/scheduled-events.md) Ez értesíti, ha virtuális gépei ki lesznek ülve, és 30 másodperce lesz, hogy befejezze a feladatokat, és leállítási feladatokat hajtson végre a kiesés előtt. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Visszaállítás & (előzetes verzió)

Ez az új platformszintű funkció a mi segítségével automatikusan megpróbálja visszaállítani a kihelyzett Azure Spot virtuálisgép-példányokat egy méretezési készleten belül, hogy fenntartsa a célpéldányok számát. 

> [!IMPORTANT]
> Próbálja &, hogy a visszaállítás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Próbálja & a visszaállítás előnyeit:
- Az Azure Spot-Virtual Machines kapacitás miatt ki van oltva.
- A visszaállított Azure Virtual Machines-tárolók várhatóan hosszabb ideig futnak, és kisebb valószínűséggel indulnak ki a kapacitásból való kilakoltatások.
- Javítja az Azure-beli spot virtuális gépek élettartamát, így a számítási feladatok hosszabb ideig futnak.
- Segít Virtual Machine Scale Sets, hogy fenntartsa az Azure Spot-Virtual Machines célszámát, hasonlóan ahhoz, hogy fenntartsa a használat alapú fizetéses virtuális gépek célszám funkcióját.

Próbálja &, hogy a visszaállítás le van tiltva az automatikus skálázást használni [próbáló méretezési készletekben.](virtual-machine-scale-sets-autoscale-overview.md) A méretezési készletben a virtuális gépek számát az automatikus méretezési szabályok szabálya adja meg.

### <a name="register-for-try--restore"></a>Regisztrálás a visszaállítási & való próbálkozásra

Mielőtt használhatta volna a visszaállítási & funkciót, regisztrálnia kell az előfizetését az előzetes verzióra. A regisztráció több percig is eltarthat. A szolgáltatásregisztráció befejezéséhez használhatja az Azure CLI-t vagy a PowerShellt.


**A CLI használata**

Az [az feature register használatával](/cli/azure/feature#az_feature_register) engedélyezheti az előzetes verzió használatát az előfizetéséhez. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Miután regisztrálta a funkciót az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással kell befejeznie a feliratkozási folyamatot. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**A PowerShell használata** 

A [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsmag használatával engedélyezheti az előzetes verzió használatát az előfizetéséhez. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Miután regisztrálta a funkciót az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással kell befejeznie a feliratkozási folyamatot. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Elhelyezési csoportok

Az elhelyezési csoport az Azure rendelkezésre állási csoporthoz hasonló szerkezet, amely saját tartalék tartományokkal és frissítési tartományokkal rendelkezik. A méretezési csoport alapértelmezés szerint egy legfeljebb 100 virtuális gép méretű elhelyezési csoportból áll. Ha a nevű méretezési csoport tulajdonsága hamis, akkor a méretezési csoport több elhelyezési csoportból is lehet, és `singlePlacementGroup` 0–1000 virtuális gépből áll. 

> [!IMPORTANT]
> Hacsak nem infinibandet használ a HPC-vel, erősen ajánlott a méretezési csoport tulajdonságát false (hamis) értékre állítani, hogy több elhelyezési csoport is jobban skálázható legyen a régióban vagy `singlePlacementGroup` zónában.  

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Azure Spot-Virtual Machines üzembe helyezése méretezési készletekben

Az Azure Spot Virtual Machines méretezési készleteken való üzembe helyezéséhez beállíthatja az új *Prioritás* jelzőt Spot *(Spot) jelölőre.* A méretezési készletben minden virtuális gép Spot (Spot) lesz. Az Azure Spot Virtual Machines méretezési csoport létrehozásához használja az alábbi módszerek egyikét:
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-sablonok](#resource-manager-templates)

## <a name="portal"></a>Portál

Az Azure Spot-adathalmazt használó méretezési csoport Virtual Machines megegyezik az első [lépésekről () cikkben olvashat.](quick-create-portal.md) Méretezési csoport üzembe helyezésekor beállíthatja a Spot jelzőt és a kiépítési szabályzatot: Méretezési csoport létrehozása Az Azure Spot Virtual Machines ![](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

A méretezési csoport Azure Spot Virtual Machines létrehozása megegyezik az első [lépésekről () cikkben olvashat.](quick-create-cli.md) Egyszerűen adja hozzá a "--Priority Spot" gombra, és adja hozzá a `--max-price` következőt: . Ebben a példában a -et használjuk, így a példány nem lesz a price (ár) alapján `-1` `--max-price` lesz kiszűkülve.

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

A méretezési csoport Azure Spot Virtual Machines létrehozása megegyezik az első [lépésekről () cikkben olvashat.](quick-create-powershell.md)
Egyszerűen adja hozzá a "-Priority Spot" et, és adjon meg egy et a `-max-price` [New-AzVmssConfig fájlhoz.](/powershell/module/az.compute/new-azvmssconfig)

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

Az Azure Spot-adathalmazt használó méretezési csoport Virtual Machines megegyezik a [Linuxra](quick-create-template-linux.md) vagy [Windowsra](quick-create-template-windows.md)vonatkozó első lépésekről. 

Az Azure-beli spot virtuálisgép-sablonok üzembe helyezéséhez használja a vagy `"apiVersion": "2019-03-01"` újabb szolgáltatásokat. 

Adja hozzá `priority` a , és `evictionPolicy` tulajdonságokat a szakaszhoz és a `billingProfile` `"virtualMachineProfile":` `"singlePlacementGroup": false,` tulajdonságot a `"Microsoft.Compute/virtualMachineScaleSets"` sablon szakaszhoz:

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

Ha törölni szeretné a példányt a kiesés után, módosítsa a paramétert `evictionPolicy` a következőre: `Delete` .


## <a name="simulate-an-eviction"></a>Kiesés szimulálása

Szimulálhatja [egy Azure-beli](/rest/api/compute/virtualmachines/simulateeviction) spot virtuális gép kilakoltatását annak tesztelésére, hogy az alkalmazás milyen jól reagál a hirtelen kiesésre. 

Cserélje le az alábbiakat a saját adataira: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` A azt jelenti, hogy a szimulált kiesés sikeres volt. 

## <a name="faq"></a>GYIK

**K:** A létrehozás után az Azure Spot virtuálisgép-példány megegyezik a standard példányokkal?

**A:** Igen, kivéve, hogy az Azure Spot-Virtual Machines sla nincs, és bármikor ki is lehet őketlaktatni.


**K:** Mi a mi a dol, ha kiesnek, de továbbra is kapacitásra van szüksége?

**A:** Ha azonnal kapacitásra van szüksége, javasoljuk, hogy Azure Spot Virtual Machines helyett standard virtuális gépeket használjon.


**K:** Hogyan kezeli a kvóta az Azure-beli spot virtuális gépekre vonatkozó kvótát?

**A:** Az Azure-beli spot virtuálisgép-példányok és standard példányok külön kvótakészletet fognak tartalmazni. Az Azure-beli spot virtuális gépek kvótája meg lesz osztva a virtuális gépek és a méretezésikészlet-példányok között. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).


**K:** Kérhetek további kvótát az Azure-beli spot virtuális gépekhez?

**A:** Igen, a szabványos kvótakérési folyamattal elküldheti a kérést, hogy növelje az Azure Spot-Virtual Machines [kvótáját.](../azure-portal/supportability/per-vm-quota-requests.md)


**K:** Átalakíthatók a meglévő méretezési készletek Azure-beli spot virtuálisgép-méretezési készletekké?

**A:** Nem, a jelző `Spot` beállítása csak a létrehozáskor támogatott.


**K:** Ha alacsony prioritású méretezési készletekhez használtam, akkor inkább az -t kell `low` `Spot` használnom?

**A:** Jelenleg a és a is működni fog, de érdemes átváltanunk az -ről a `low` `Spot` -re. `Spot`


**K:** Létrehozhatok méretezési csoportokat normál virtuális gépekkel és Azure Spot Virtual Machines?

**A:** Nem, a méretezési készlet nem támogathat egynél több prioritástípust.


**K:**  Használhatok automatikus skálázást az Azure-beli spot virtuálisgép-méretezési készletekkel?

**A:** Igen, automatikus méretezési szabályokat állíthat be az Azure-beli spot virtuálisgép-méretezési készlethez. Ha a virtuális gépek ki vannak ülve, az automatikus skálázás megpróbálhat új Azure Spot-Virtual Machines. Ne feledje azonban, hogy ez a kapacitás nem garantált. 


**K:**  Működik az automatikus skálázás a kiépítési szabályzatokkal (felszabadítás és törlés)?

**A:** Igen, de ajánlott úgy beállítani a törlésre vonatkozó kilakoltatási szabályzatot, ha automatikus skálázást használ. Ennek az az oka, hogy a felszabadított példányok beleszámulnak a méretezési készlet kapacitásának számba. Az automatikus skálázás használata esetén valószínűleg gyorsan el fogja érni a célpéldányok számát a felszabadított, kieső példányok miatt. A méretezési műveletekre a spot-kilakoltatások is hatással lehetnek. A virtuálisgép-méretezésihalmaz-példányok például a méretezési műveletek során történő több spot-kilakoltatás miatt a beállított minimális szám alá esnek. 


**K:** Hol lehet kérdéseket feltenni?

**A:** A Q&A-ban közzéteheti `azure-spot` [és címkézheti a kérdését.](/answers/topics/azure-spot.html) 

## <a name="next-steps"></a>Következő lépések

A díjszabás [részleteiért](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) tekintse meg a virtuálisgép-méretezési készlet díjszabását tartalmazó oldalt.
