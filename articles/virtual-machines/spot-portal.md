---
title: Az Azure spot Virtual Machines üzembe helyezése a portál használatával
description: A Azure PowerShell használata a helyszíni Virtual Machines üzembe helyezéséhez a költségek megtakarítása érdekében.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 879a3e9b3d3f651a1dea17e76dba503cd2816b9e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098570"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Az Azure spot Virtual Machines üzembe helyezése a Azure Portal használatával

Az [Azure Spot Virtual Machines](spot-vms.md) használatával jelentős költségmegtakarítás mellett kihasználhatja a fel nem használt kapacitás előnyeit. Az Azure-infrastruktúra minden olyan időpontban kizárja az Azure spot Virtual Machinest, amikor az Azure-nak szüksége van a kapacitásra. Ezért az Azure spot Virtual Machines nagyszerűek olyan munkaterhelésekhez, amelyek képesek kezelni a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

Az Azure spot Virtual Machines díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). A maximális ár beállításával kapcsolatos további információkért lásd: [Azure Spot Virtual Machines – díjszabás](spot-vms.md#pricing).

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. Az Azure-beli helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, legfeljebb 5 tizedesjegyet használva. Az érték például a `0.05701` maximális díj $0,05701 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.

A virtuális gép kizárásakor lehetősége van törölni a virtuális gépet és a mögöttes lemezt, vagy felszabadítani a virtuális gépet, hogy később újra lehessen indítani.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

A virtuális gépek üzembe helyezésekor dönthet úgy, hogy egy Azure spot-példányt használ.


Az alapbeállítások lap **példány részletei** **szakaszában a** **nem** érték az Azure spot-példányok használatának alapértelmezett értéke.

![Képernyőfelvétel a nem lehetőség kiválasztásához, ne használjon Azure spot-példányt](./media/spot-portal/no.png)

Ha az **Igen** lehetőséget választja, a szakasz kibontása után kiválaszthatja a [kizárási típusát és a kizárási szabályzatot](spot-vms.md#eviction-policy). 

![Képernyőfelvétel az igen lehetőség kiválasztásához, Azure spot-példány használata](./media/spot-portal/yes.png)

A díjszabást és a kizárási arányt más hasonló régiókkal is összehasonlíthatja, ha kiválasztja a **díjszabási előzmények megtekintése lehetőséget, és összehasonlítja az árakat a közeli régiókban**.

Ebben a példában a közép-Kanada középső régiója kevésbé költséges, és alacsonyabb a kizárási arány, mint az USA keleti régiója.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Képernyőfelvétel a régió lehetőségeiről a díjszabási és kizárási arányok különbségével.":::

A régiót úgy változtathatja meg, hogy kiválasztja a legmegfelelőbbet az Ön számára, majd az **OK gombra** kattint.

## <a name="simulate-an-eviction"></a>Kizárás szimulálása

Az Azure-beli helyszíni virtuális gépek [kizárását szimulálhatja](/rest/api/compute/virtualmachines/simulateeviction) , így tesztelheti, hogy az alkalmazás milyen mértékben válaszol a hirtelen kizárásra. 

Cserélje le a következőt az adataira: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` azt jelenti, hogy a szimulált kizárás sikeres volt. 

## <a name="next-steps"></a>Következő lépések

Az Azure spot Virtual Machines [PowerShell](./windows/spot-powershell.md), [parancssori](./linux/spot-cli.md)felület vagy [sablon](./linux/spot-template.md)használatával is létrehozható.
