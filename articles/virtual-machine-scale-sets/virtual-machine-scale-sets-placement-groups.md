---
title: Nagyméretű Azure-Virtual Machine Scale Sets használata
description: Tudnivalók a nagyméretű Azure-beli virtuálisgép-méretezési csoportokról az alkalmazásban való használat érdekében.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: ffa2a3a921e988b92ad90831041a6fb4d321bc42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747824"
---
# <a name="working-with-large-virtual-machine-scale-sets"></a>Nagyméretű virtuálisgép-méretezési csoportok használata
Mostantól akár 1000 virtuális gép kapacitású Azure [virtuálisgép-méretezési csoportokat](./index.yml) is létrehozhat. Ebben a dokumentumban a _nagyméretű virtuálisgép-méretezési csoport_ egy 100 virtuális gépnél nagyobb skálázásra képes méretezési csoportként van meghatározva. Ezt a képességet a méretezési csoport egyik tulajdonsága adja meg (_singlePlacementGroup=False_). 

A nagyméretű méretezési csoportok bizonyos szempontból, például a terheléselosztást és tartalék tartományokat illetően eltérően viselkednek, mint a standard méretezési csoportok. Ez a dokumentum bemutatja a nagyméretű méretezési csoportok jellemzőit, és azt, hogy mit kell tudni az alkalmazásokban történő sikeres használatukhoz. 

Általánosan használt megközelítés a nagyméretű felhőinfrastruktúrák üzembe helyezéséhez a _skálázási egységek_ készletének létrehozása, például több virtuálisgép-skálázási egység létrehozása több VNET-en és tárfiókon. Ez a megközelítés könnyebb kezelhetőséget biztosít az egyszeres virtuális gépekhez képest, a több skálázási egység pedig számos alkalmazás esetében hasznos, különösen azoknál, amelyeknél egyéb halmozható összetevőkre van szükség, például több a virtuális hálózatnál és a végpontoknál. Ha az alkalmazáshoz egyetlen nagyméretű fürtre van szükség, egyszerűbb lehet az akár 1000 virtuális gépet tartalmazó egyetlen skálázási egység üzembe helyezése. A példaforgatókönyvek központosított big data-alapú üzemelő példányokat, vagy a munkavégző csomópontok nagy készleteinek egyszerű kezelését igénylő számítási grideket tartalmaznak. A [csatlakoztatott adatlemezekkel](virtual-machine-scale-sets-attached-disks.md) rendelkező virtuálisgép-méretezési csoporttal kombinálva a nagyméretű méretezési csoportok lehetővé teszik olyan méretezhető infrastruktúra üzembe helyezését egyetlen műveletben, amely több ezernyi virtuális CPU-ból (vCPU) és petabájtos méretű tárolóegységekből áll.

## <a name="placement-groups"></a>Elhelyezési csoportok 
A _nagyméretű_ méretezési csoportot nem a virtuális gépek, hanem a tartalmazott _elhelyezési csoportok_ száma teszi különlegessé. Az elhelyezési csoport egy, az Azure rendelkezésre állási csoporthoz hasonló konstrukció, saját tartalék tartománnyal és frissítési tartománnyal. A méretezési csoport alapértelmezés szerint egy legfeljebb 100 virtuális gép méretű elhelyezési csoportból áll. Ha a _singlePlacementGroup_ nevű méretezésicsoport-tulajdonság _hamis_ értékre van állítva, a méretezési csoport több elhelyezési csoportból állítható össze, és 0–1000 virtuális gépből állhat. Ha csoport az alapértelmezett _igaz_ érték van beállítva, a méretezési csoport egyetlen elhelyezési csoportból és 0–100 virtuális gépből áll.

## <a name="checklist-for-using-large-scale-sets"></a>Ellenőrzőlista a nagyméretű méretezési csoportok használatához
Annak eldöntéséhez, hogy az alkalmazás hatékony tudja-e használni a nagyméretű méretezési csoportokat, vegye fontolóra a következő követelményeket:

- Ha sok virtuális gépet tervez üzembe helyezni, szükségessé válhat a Számítási vCPU-kvóta korlátozásának emelése. 
- Az Azure Marketplace rendszerképekből létrehozott méretezési csoportok akár 1000 virtuális gépig skálázhatók.
- Az egyéni rendszerképekből (olyan virtuálisgép-rendszerképek, amelyeket a felhasználó maga hoz létre és tölt fel) létrehozott méretezési csoportok akár 600 virtuális gépig skálázhatók.
- A nagyméretű méretezési csoportokhoz az Azure Managed Disks szükséges. Azokhoz a méretezési csoportokhoz, amelyeket nem a Managed Disksszel hoztak létre, több tárfiókra van szükség (egyre minden 20 virtuális géphez). A nagyméretű méretezési csoportokat arra tervezték, hogy kizárólag a Managed Disksszel működjenek annak érdekében, hogy csökkenjen a tárolókezelés munkaterhelése, és hogy megszűnjön a tárfiókhoz tartozó előfizetés-korlát elérésének veszélye. 
- A nagyméretű méretezés (SPG = false) nem támogatja a InfiniBand hálózatkezelést
- A 4. rétegbeli terheléselosztáshoz több elhelyezési csoportból álló méretezési csoport esetén szükség van az [Azure Load Balancer standard termékváltozatára](../load-balancer/load-balancer-overview.md). A Load Balancer standard termékváltozata további előnyöket biztosít, például a több méretezési csoport közötti terheléselosztást. A standard termékváltozat emellett azt is megköveteli, hogy a méretezési csoporthoz hozzá legyen rendelve egy hálózati biztonsági csoport, különben a NAT-készletek nem működnek megfelelően. Ha az Azure Load Balancer alapszintű termékváltozatát szeretné használni, győződjön meg arról, hogy a méretezési csoport egyetlen elhelyezési csoport használatára van konfigurálva, ami az alapértelmezett beállítás is.
- A 7. rétegbeli terheléselosztás az Azure Application Gatewayjel minden méretezési csoporthoz támogatott.
- A méretezési csoport egyetlen alhálózattal van meghatározva – győződjön meg róla, hogy az alhálózat megfelelő méretű névtérrel rendelkezik minden virtuális géphez. A méretezési csoport alapértelmezés szerint a szükségesnél több erőforrást hoz létre (további virtuális gépeket hoz létre az üzembe helyezés során vagy a felskálázáskor, amelyek nem járnak többletköltséggel), hogy javítsa az üzembe helyezés megbízhatóságát és teljesítményét. Lehetővé teszi egy címtér számára, hogy 20%-kal nagyobb legyen, mint a virtuális gépek száma, amelyekhez skálázni szeretne.
- A tartalék tartományok és a frissítési tartományok csak az elhelyezési csoporton belül konzisztensek. Ez az architektúra nem módosítja a méretezési csoport általános elérhetőségét, mivel a virtuális gépek egyenlően vannak elosztva a különböző fizikai hardvereken, de azt jelenti, hogy ha biztosítania kell, hogy két virtuális gép különböző hardveren legyen, meg kell győződnie arról, hogy különböző tartalék tartományban találhatók ugyanabban az elhelyezési csoportban. Tekintse meg a hivatkozás [rendelkezésre állási lehetőségeit](../virtual-machines/availability.md). 
- A tartalék tartomány és az elhelyezési csoport azonosítója a méretezési csoport virtuális gépének _példány nézetében_ tekinthető meg. A méretezési csoport virtuális gépének példány nézetét az [Azure Resource Explorerben](https://resources.azure.com/) tekintheti meg.

## <a name="creating-a-large-scale-set"></a>Nagyméretű méretezési csoport létrehozása
Amikor méretezési csoportot hoz létre az Azure Portalon, csak adja meg a *Példányszám* értékét (legfeljebb 1000-ig). Ha több mint 100 példánya van, akkor a *Skálázás engedélyezése 100-nál több példányig* beállítás értéke *Igen* lesz, így több elhelyezési csoporthoz skálázhat. 

![Ez a rendszerkép az Azure Portal példányok paneljét jeleníti meg. A példányok számának és a példány méretének kiválasztására szolgáló beállítások érhetők el.](./media/virtual-machine-scale-sets-placement-groups/portal-large-scale.png)

Az [Azure CLI](https://github.com/Azure/azure-cli) _az vmss create_ parancsával létrehozhat egy nagyméretű virtuálisgép-méretezési csoportot. Ez a parancs az intelligens alapértelmezett beállításokat (például a _példányszám_ argumentumon alapuló alhálózat méretét) adja meg:

```azurecli
az group create -l southcentralus -n biginfra
az vmss create -g biginfra -n bigvmss --image ubuntults --instance-count 1000
```

A _vmss create_ parancs alapértelmezett értékeket használ egyes konfigurációs értékekhez, ha nincsenek meghatározva. Az elérhető és felülbírálható beállítások megtekintéséhez használja a következő parancsot:

```azurecli
az vmss create --help
```

Ha egy Azure Resource Manager-sablon összeállításával hoz létre nagyméretű méretezési csoportot, győződjön meg róla, hogy a sablon az Azure Managed Disksen alapuló méretezési csoportot hoz létre. A _singlePlacementGroup_ tulajdonságot _hamis_ értékre állíthatja a _Microsoft. számítás/virtualMachineScaleSets_ erőforrás _Tulajdonságok_ szakaszában. Az alábbi JSON-töredék ábrázolja egy méretezési csoport sablon kezdetét, az 1000 virtuálisgép-kapacitást és a _"singlePlacementGroup" : hamis_ beállítást is beleértve:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "location": "australiaeast",
  "name": "bigvmss",
  "sku": {
    "name": "Standard_DS1_v2",
    "tier": "Standard",
    "capacity": 1000
  },
  "properties": {
    "singlePlacementGroup": false,
    "upgradePolicy": {
      "mode": "Automatic"
    }
```

A nagyméretű méretezési csoport sablonjának teljes példáját a következő témakörben találja: [https://github.com/gbowerman/azure-myriad/blob/main/bigtest/bigbottle.json](https://github.com/gbowerman/azure-myriad/blob/main/bigtest/bigbottle.json) .

## <a name="converting-an-existing-scale-set-to-span-multiple-placement-groups"></a>Meglévő méretezési csoportok konvertálása, hogy több elhelyezési csoportra terjedjenek ki
Ahhoz, hogy egy már meglévő virtuálisgép-méretezési csoport több mint 100 virtuális géphez skálázódhasson, a _singlePlacementGroup_ tulajdonságot _false_ értékre kell állítania a méretezésicsoport-modellben. Az [Azure Resource Explorerrel](https://resources.azure.com/) tesztelheti ennek a tulajdonságnak a módosítását. Keressen egy már létező méretezési csoportot, válassza a _Szerkesztés_ lehetőséget, majd módosítsa a _singlePlacementGroup_ tulajdonságot. Ha nem látja ezt a tulajdonságot, előfordulhat, hogy a Microsoft.Compute API egy régebbi változatával tekinti meg a méretezési csoportot.

> [!NOTE]
> Módosíthat egy méretezési csoportot, hogy ne csak (az alapértelmezett működés szerinti) egy, hanem több elhelyezési csoportot támogasson, de ennek fordítottjára nincs lehetőség. Ezért a konvertálás előtt győződjön meg róla, hogy tisztában van a nagyméretű méretezési csoportok tulajdonságaival.
