---
title: Azure spot virtuális gépek használata
description: Megtudhatja, hogyan használhatja az Azure spot virtuális gépeket a költségek megtakarítására.
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: 1e82da3bc45bc8fb88b3955bd59091372f56d292
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375459"
---
# <a name="use-spot-vms-in-azure"></a>Helyszíni virtuális gépek használata az Azure-ban

A helyszíni virtuális gépek használata lehetővé teszi, hogy a kihasználatlan kapacitást jelentős költségmegtakarítással használja. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. A helyszíni virtuális gépek üzembe helyezésekor az Azure kiosztja a virtuális gépeket, ha rendelkezésre áll kapacitás, de ezekhez a virtuális gépekhez nem biztosítunk SLA-t. A helyszíni virtuális gépek nem biztosítanak magas rendelkezésre állású garanciákat. Az Azure-infrastruktúra minden olyan időpontban, amikor az Azure-nak szüksége van a kapacitásra, 30 másodperces figyelmeztetéssel kizárja a helyszíni virtuális gépeket. 


## <a name="eviction-policy"></a>Kiürítési szabályzat

A virtuális gépeket a kapacitás vagy a beállított maximális ár alapján lehet kizárni. A Direktszínű virtuális gépek létrehozásakor beállíthatja a kizárási házirendet a *felszabadításhoz* (alapértelmezett) vagy a *törléshez*. 

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

A következő virtuálisgép-méretek nem támogatottak a Direktszínű virtuális gépek esetében:
 - B sorozat
 - Bármilyen méretű promóciós verzió (például Dv2, NV, NC, H promo-méretek)

A helyszíni virtuális gépek bármely régióba üzembe helyezhetők, kivéve Microsoft Azure China 21Vianet.

<a name="channel"></a>

Jelenleg a következő [típusú ajánlatok](https://azure.microsoft.com/support/legal/offer-details/) támogatottak:

-   Nagyvállalati Szerződés
-   Utólagos elszámolású ajánlat kódja 003P
-   Szponzorált
- A felhőalapú szolgáltató (CSP) esetében forduljon a partnerhez


## <a name="pricing"></a>Díjszabás

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

A díjszabással kapcsolatos információkat az [Azure kiskereskedelmi díjszabás API](/rest/api/cost-management/retail-prices/azure-retail-prices) használatával kérdezheti le a helyszíni díjszabással kapcsolatos információk lekérdezéséhez. A `meterName` és `skuName` mindkettő tartalmazni fogja `Spot` .

A változó díjszabással maximális árat állíthat be az USA dollárban (USD), legfeljebb 5 tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.

## <a name="pricing-and-eviction-history"></a>Díjszabás és kizárási előzmények

A portálon megtekintheti a korábbi díjszabást és a kizárási arányt egy adott régióban. Válassza ki a **díjszabási előzmények megtekintése lehetőséget, és hasonlítsa össze az árakat a közeli régiókban** , hogy megtekintse az adott méretű díjszabási táblázatot vagy grafikont.  A következő képek díjszabási és kizárási díjai csak példák. 

**Diagram**:

:::image type="content" source="./media/spot-chart.png" alt-text="Képernyőkép a régió lehetőségeiről a díjszabási és a kizárási arány diagramként való különbségével.":::

**Tábla**:

:::image type="content" source="./media/spot-table.png" alt-text="Képernyőfelvétel a régió lehetőségeiről a díjszabási és kizárási arányok táblázatként való különbségével.":::



##  <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** A létrehozása után a egy direkt virtuális gép, amely azonos a normál szabványos virtuális géppel?

**A:** Igen, a helyszíni virtuális gépekhez nem biztosítunk SLA-t, és a szolgáltatás bármikor kizárható.


**K:** Mi a teendő, ha kizárja, de továbbra is kapacitásra van szüksége?

**A:** Javasoljuk, hogy a virtuális gépek helyett használjon szabványos virtuális gépeket, ha a kapacitásra azonnal szükség van.


**K:** Hogyan kezelik a kvóta a helyszíni virtuális gépeket?

**A:** A helyszíni virtuális gépek külön kvóta-készlettel rendelkeznek. A helyszíni kvóta a virtuális gépek és a méretezési csoport példányai között lesz megosztva. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).


**K:** Igényelhetek további kvótát a helyszínen?

**A:** Igen, elküldheti a kérést, hogy növelje a helyszíni virtuális gépek kvótáját a [normál kvóta-kérési folyamaton](../azure-portal/supportability/per-vm-quota-requests.md)keresztül.


**K:** Hol tehetek közzé kérdéseket?

**A:** A kérdését a következő címen teheti közzé és címkézheti `azure-spot` : [Q&a](/answers/topics/azure-spot.html). 


**K:** Hogyan változtathatom meg a helyszíni virtuális gépek maximális árát?

**A:** A maximális ár módosítása előtt fel kell szabadítania a virtuális gépet. Ezt követően módosíthatja a maximális árat a portálon, a virtuális gép **konfigurációs** szakaszából. 

## <a name="next-steps"></a>Következő lépések
Helyszíni virtuális gépek üzembe helyezéséhez használja a [CLI](./linux/spot-cli.md)-t, a [portált](spot-portal.md), az [ARM-sablont](./linux/spot-template.md)vagy a [PowerShellt](./windows/spot-powershell.md) .

Helyszíni virtuálisgép-példányokkal is üzembe helyezhetők a [méretezési csoport](../virtual-machine-scale-sets/use-spot.md).

Ha hibát tapasztal, tekintse meg a [hibakódokat](./error-codes-spot.md).
