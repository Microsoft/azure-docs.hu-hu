---
title: Az Azure spot Virtual Machines használata
description: Ismerje meg, hogyan mentheti a költségeket az Azure spot Virtual Machines használatával.
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: fb53fc37227e040ed7bd7fc8e47de9aed538bc2e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721392"
---
# <a name="use-azure-spot-virtual-machines"></a>Az Azure spot Virtual Machines használata 

Az Azure spot Virtual Machines használatával jelentős költségmegtakarítás mellett kihasználhatja a fel nem használt kapacitás előnyeit. Az Azure-infrastruktúra minden olyan időpontban kizárja az Azure spot Virtual Machinest, amikor az Azure-nak szüksége van a kapacitásra. Ezért az Azure spot Virtual Machines nagyszerűek olyan munkaterhelésekhez, amelyek képesek kezelni a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. Az Azure spot Virtual Machines telepítésekor az Azure kiosztja a virtuális gépeket, ha rendelkezésre áll kapacitás, de ezekhez a virtuális gépekhez nem biztosítunk SLA-t. Az Azure-beli helyszíni virtuális gépek nem biztosítanak magas rendelkezésre állású garanciákat. Az Azure-infrastruktúra minden olyan időpontban kizárja az Azure spot Virtual Machinest, amely 30 másodperces felmondást igényel. 


## <a name="eviction-policy"></a>Kiürítési szabályzat

A virtuális gépeket a kapacitás vagy a beállított maximális ár alapján lehet kizárni. Az Azure-beli direktszínű virtuális gépek létrehozásakor beállíthatja a kizárási házirendet a *felszabadításhoz* (alapértelmezett) vagy a *törléshez*. 

A *felszabadítási* házirend áthelyezi a virtuális gépet a leállított, lefoglalt állapotba, így később újra üzembe helyezheti. Azonban nem garantálható, hogy a foglalás sikeres lesz. A fel nem osztott virtuális gépek a kvóta alapján számítanak, és a mögöttes lemezek tárolási költségei lesznek felszámítva. 

Ha azt szeretné, hogy a virtuális gép törölhető legyen a kizárása után, beállíthatja a kizárási házirendet a *törléshez*. A kizárt virtuális gépeket a rendszer a mögöttes lemezekkel együtt törli, így nem kell tovább fizetnie a tárterületért. 

Engedélyezheti, hogy a virtuális gép értesítéseit az [Azure Scheduled Eventson](./linux/scheduled-events.md)keresztül fogadja. Ez értesíti Önt, ha a virtuális gépek ki vannak zárva, és 30 másodpercen belül befejezi az összes feladatot, és leállítási feladatokat hajt végre a kizárás előtt. 


| Beállítás | Eredmény |
|--------|---------|
| A maximális díj értéke >= a jelenlegi díj. | A virtuális gép üzembe helyezése esetén a kapacitás és a kvóta elérhető. |
| A maximális árat úgy kell beállítani, hogy az aktuális árat <. | A virtuális gép nincs telepítve. Hibaüzenetet kap arról, hogy a maximális árat >= aktuális áron kell megadnia. |
| Leállítás/felszabadítási virtuális gép újraindítása, ha a maximális díj >= a jelenlegi díj | Ha van kapacitása és kvóta, akkor a rendszer telepíti a virtuális gépet. |
| Leállítás/felszabadítási virtuális gép újraindítása, ha a maximális díj < a jelenlegi áron | Hibaüzenetet kap arról, hogy a maximális árat >= aktuális áron kell megadnia. | 
| A virtuális gép ára felment, és most már > a maximális árat. | A virtuális gép el lesz távolítva. A tényleges kizárást megelőzően egy 30%-os értesítést kap. | 
| A kizárás után a virtuális gép ára visszatérhet, hogy < a maximális árat. | A virtuális gép nem indul el automatikusan újra. Saját maga is újraindíthatja a virtuális gépet, és az aktuális áron kell fizetnie. |
| Ha a maximális díj értéke `-1` | A virtuális gép díjszabása nem kerül kizárásra. A maximális díj a jelenlegi díj, amely a standard szintű virtuális gépek árával függ. A standard díj felett soha nem számítunk fel díjat.| 
| A maximális ár módosítása | A maximális díj megváltoztatásához fel kell szabadítania a virtuális gépet. Szabadítsa fel a virtuális gépet, állítson be egy új maximális árat, majd frissítse a virtuális gépet. |


## <a name="limitations"></a>Korlátozások

Az Azure spot Virtual Machines esetében a következő virtuálisgép-méretek nem támogatottak:
 - B sorozat
 - Bármilyen méretű promóciós verzió (például Dv2, NV, NC, H promo-méretek)

Az Azure spot Virtual Machines bármely régióba üzembe helyezhető, kivéve a Microsoft Azure China 21Vianet-t.

<a name="channel"></a>

Jelenleg a következő [típusú ajánlatok](https://azure.microsoft.com/support/legal/offer-details/) támogatottak:

-   Nagyvállalati Szerződés 
-   Utólagos elszámolású ajánlat kódja (003P)
-   Szponzorált (0036P és 0136P)
- A felhőalapú szolgáltató (CSP) esetében forduljon a partnerhez


## <a name="pricing"></a>Díjszabás

Az Azure spot Virtual Machines díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

A díjszabással kapcsolatos információkat az [Azure kiskereskedelmi díjszabás API](/rest/api/cost-management/retail-prices/azure-retail-prices) használatával kérdezheti le a helyszíni díjszabással kapcsolatos információk lekérdezéséhez. A `meterName` és `skuName` mindkettő tartalmazni fogja `Spot` .

A változó díjszabással maximális árat állíthat be az USA dollárban (USD), legfeljebb 5 tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.

## <a name="pricing-and-eviction-history"></a>Díjszabás és kizárási előzmények

A portálon megtekintheti a korábbi díjszabást és a kizárási arányt egy adott régióban. Válassza ki a **díjszabási előzmények megtekintése lehetőséget, és hasonlítsa össze az árakat a közeli régiókban** , hogy megtekintse az adott méretű díjszabási táblázatot vagy grafikont.  A következő képek díjszabási és kizárási díjai csak példák. 

**Diagram**:

:::image type="content" source="./media/spot-chart.png" alt-text="Képernyőkép a régió lehetőségeiről a díjszabási és a kizárási arány diagramként való különbségével.":::

**Tábla**:

:::image type="content" source="./media/spot-table.png" alt-text="Képernyőfelvétel a régió lehetőségeiről a díjszabási és kizárási arányok táblázatként való különbségével.":::



##  <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** A létrehozása után egy Azure-beli helyszíni virtuális gép azonos a normál normál virtuális géppel?

**A:** Igen, kivéve, ha az Azure spot Virtual Machineshoz nem biztosítunk SLA-t, de bármikor kizárható.


**K:** Mi a teendő, ha kizárja, de továbbra is kapacitásra van szüksége?

**A:** Javasoljuk, hogy a standard szintű virtuális gépeket az Azure spot Virtual Machines helyett használja, ha a kapacitásra azonnal szüksége van.


**K:** Hogyan kezelik a kvóta az Azure spot Virtual Machines?

**A:** Az Azure spot Virtual Machines külön kvóta-készlettel fog rendelkezni. A helyszíni kvóta a virtuális gépek és a méretezési csoport példányai között lesz megosztva. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).


**K:** Igényelhetek további kvótát az Azure spot Virtual Machines?

**A:** Igen, elküldheti a kérést, hogy növelje az Azure spot Virtual Machines kvótáját a [normál kvóta-igénylési folyamaton](../azure-portal/supportability/per-vm-quota-requests.md)keresztül.


**K:** Hol tehetek közzé kérdéseket?

**A:** A kérdését a következő címen teheti közzé és címkézheti `azure-spot` : [Q&a](/answers/topics/azure-spot.html). 


**K:** Hogyan változtathatom meg a helyszíni virtuális gépek maximális árát?

**A:** A maximális ár módosítása előtt fel kell szabadítania a virtuális gépet. Ezt követően módosíthatja a maximális árat a portálon, a virtuális gép **konfigurációs** szakaszából. 

## <a name="next-steps"></a>Következő lépések
Az Azure spot Virtual Machines üzembe helyezéséhez használja a [CLI](./linux/spot-cli.md)-t, a [portált](spot-portal.md), az [ARM-sablont](./linux/spot-template.md)vagy a [PowerShellt](./windows/spot-powershell.md) .

A [méretezési csoport Azure-beli virtuálisgép-példányokkal](../virtual-machine-scale-sets/use-spot.md)is üzembe helyezhető.

Ha hibát tapasztal, tekintse meg a [hibakódokat](./error-codes-spot.md).
