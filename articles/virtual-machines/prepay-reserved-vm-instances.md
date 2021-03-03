---
title: Előre fizetés Azure-beli virtuális gépek számára a pénz megtakarítása érdekében
description: Megtudhatja, hogyan vásárolhat Azure Reserved Virtual Machine Instances a számítási költségek mentéséhez.
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: 7eab410bb2344617e555a7a9d1d44cd9adcc676d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674727"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>Költségek megtakarítása Azure Reserved VM Instances


Ha egy Azure-beli fenntartott VM-példányra véglegesít, pénzt takaríthat meg. A foglalási kedvezményt a rendszer automatikusan alkalmazza a foglalási hatókörnek és attribútumoknak megfelelő futó virtuális gépek számára. A kedvezmények beszerzéséhez nincs szükség foglalások hozzárendelésére egy virtuális géphez. A fenntartott példányok vásárlása csak a virtuális gépek használatának számítási részét fedi le. Windows rendszerű virtuális gépek esetén a használati mérőszám két külön méterre oszlik. Van egy számítási mérőszám, amely ugyanaz, mint a Linux-mérő, és egy Windows IP-mérő. A vásárláskor megjelenő díjak csak a számítási költségekre vonatkoznak. A díjak nem tartalmazzák a Windows-szoftverek költségeit. További információ a szoftverekkel kapcsolatos költségekről: [a Azure Reserved VM instances nem tartalmazott szoftveres költségek](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>A megfelelő méret meghatározása a vásárlás előtt

A foglalás megvásárlása előtt meg kell határoznia a szükséges virtuális gép méretét. A következő szakaszban a virtuális gép megfelelő méretének meghatározásához talál segítséget.

### <a name="use-reservation-recommendations"></a>Foglalási javaslatok használata

A foglalási javaslatok segítségével megadhatja a megvásárolni kívánt foglalásokat.

- A vásárlási javaslatok és a javasolt mennyiségek akkor jelennek meg, ha a Azure Portalban vásárol egy virtuális gép számára fenntartott példányt.
- Azure Advisor az egyes előfizetésekre vonatkozó vásárlási javaslatokat tartalmaz.  
- Az API-k használatával vásárlási javaslatokat kaphat a megosztott hatókörhöz és az egyszeri előfizetések hatóköréhez is. További információ: [fenntartott példányok vásárlására vonatkozó ajánlás API-k nagyvállalati ügyfelek](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)számára.
- A Nagyvállalati Szerződés (EA) és a Microsoft Customer Agreement (MCA) ügyfelek esetében a megosztott és az egyszeri előfizetési hatókörökkel kapcsolatos vásárlási javaslatok a [Azure Consumption Insights Power bi a Content Pack csomaggal](/power-bi/service-connect-to-azure-consumption-insights)érhetők el.

### <a name="services-that-get-vm-reservation-discounts"></a>A virtuálisgép-foglalási kedvezményekre jogosult szolgáltatások

A virtuálisgép-foglalások a több szolgáltatás általi virtuálisgép-használatra is alkalmazhatók, nem csupán a virtuális gépek üzemelő példányaira. A foglalási kedvezményekre jogosult erőforrások a példány méretének rugalmassági beállításától függően változnak.

#### <a name="instance-size-flexibility-setting"></a>Példány méretrugalmassági beállítása

A példány méretrugalmassági beállítása határozza meg, hogy mely szolgáltatásokra vonatkozik a fenntartott példányokhoz kapcsolódó kedvezmény.

Ha a *ConsumedService* értéke `Microsoft.Compute`, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva. Ebből kifolyólag ellenőrizze a használati adatait a *ConsumedService* értékének megtekintéséhez. Néhány példa:

- Virtuális gépek
- Virtuálisgép-méretezési csoportok
- Container Service
- Azure Batch-üzemelőpéldányok (felhasználói előfizetési módban)
- Azure Kubernetes Service (AKS)
- Service Fabric

Ha a *ConsumedService* az alábbiak egyike, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Ellenőrizze a *ConsumedService* értékét a használati adatokban annak megállapításához, hogy a használatra alkalmazhatók-e a foglalási kedvezmények.

A példányok méretrugalmasságára vonatkozó további információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](reserved-vm-instance-size-flexibility.md) ismertető cikket.

### <a name="analyze-your-usage-information"></a>A használati adatok elemzése

A használati adatok elemzésével megállapíthatja, hogy mely foglalásokat kell megvásárolnia. A használati adatok a használati fájlban és az API-kon érhetők el. Ezeket együtt használva meghatározhatja, hogy melyik foglalást szeretné megvásárolni. Ellenőrizze, hogy vannak-e napi szinten magas kihasználtságú virtuálisgép-példányok a megvásárolni kívánt foglalások mennyiségének meghatározásához. Kerülje a `Meter` használati adatok alkategóriáját és `Product` mezőit. Nem tesznek különbséget a Premium Storage-t használó virtuális gépek méretei között. Ha ezeket a mezőket használja a foglalás megvásárlásához szükséges virtuális gép méretének meghatározásához, akkor a mérete nem megfelelő. Ezután nem fogja megkapni a várt foglalási kedvezményt. Ehelyett tekintse meg a `AdditionalInfo` használati fájl vagy a használati API mezőjét a virtuális gép megfelelő méretének meghatározásához.

A használati fájl számlázási időszak és napi használat szerint jeleníti meg a díjakat. A használati fájl letöltésével kapcsolatos információkért tekintse meg [Az Azure-beli használat és díjak megtekintését és letöltését](../cost-management-billing/understand/download-azure-daily-usage.md)ismertető témakört. Ezt követően a használati fájl adatai alapján [meghatározhatja, hogy milyen foglalást szeretne megvásárolni](../cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Vásárlási korlátozási megfontolások

A fenntartott VM-példányok a legtöbb virtuálisgép-mérethez érhetők el, néhány kivétellel. A foglalási kedvezmények nem vonatkoznak a következő virtuális gépekre:

- **VM-sorozat** – sorozat, Av2 sorozat vagy G sorozat.

- **Előzetes vagy promóciós virtuális gépek** – az előzetes verzióban vagy a promóciós fogyasztásmérőt használó VM-sorozatok vagy méretek.

- **Felhők** – a foglalások nem vásárolhatók meg németországi vagy kínai régiókban.

- Nem **elegendő kvóta** – egy adott előfizetésre vonatkozó foglalásnak az új ri-előfizetésben elérhető vCPU-kvótával kell rendelkeznie. Ha például a célként megadott előfizetés 10 vCPU a D sorozathoz, akkor nem vásárolhat le 11 Standard_D1 példányra vonatkozó foglalást. A foglalások kvótájának keresése magában foglalja az előfizetésben már üzembe helyezett virtuális gépeket. Ha például az előfizetés 10 vCPU rendelkezik a D sorozathoz, és két standard_D1 példánya van telepítve, akkor ebben az előfizetésben 10 standard_D1 példány foglalását is megvásárolhatja. A probléma megoldásához [létrehozhat árajánlat-növelési kérést](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

- **Kapacitás korlátozásai** – ritka körülmények között az Azure korlátozza a virtuálisgép-méretek részhalmazára vonatkozó új foglalások megvásárlását a régió alacsony kapacitása miatt.

## <a name="buy-a-reserved-vm-instance"></a>Fenntartott virtuálisgép-példány vásárlása

Egy fenntartott VM-példányt vásárolhat a [Azure Portalban](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/prepare-buy-reservation.md).
Ezek a követelmények a fenntartott VM-példányok vásárlására vonatkoznak:

- Legalább egy EA-előfizetéshez vagy egy utólagos elszámolású előfizetéshez tartozó tulajdonosi szerepkörrel kell rendelkeznie.
- Az EA-előfizetések esetében engedélyezni kell a **fenntartott példányok hozzáadása** beállítást az [EA portálon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor Önnek az előfizetés EA-rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a rendszergazdai ügynökök vagy értékesítési ügynökök vásárolhatnak foglalásokat.

Példány vásárlása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
1. Új foglalás vásárlásához válassza a **Hozzáadás** elemet, majd kattintson a **Virtuális gép** lehetőségre.
1. Töltse ki a kötelező mezőket. A foglalási kedvezményre a kiválasztott attribútumoknak megfelelő, futó virtuálisgép-példányok jogosultak. A kedvezményt megkapó virtuálisgép-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.

Ha nagyvállalati szerződéssel rendelkezik, a további példányok gyors hozzáadásához használhatja a **továbbiak hozzáadása lehetőséget** is. A beállítás más előfizetési típusok esetében nem érhető el.


| Mező      | Leírás|
|------------|--------------|
|Előfizetés|A foglalás kifizetéséhez használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a foglalás költségei. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy Microsoft ügyfél-szerződés vagy egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). A díjak az Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva (ha van ilyen), vagy kerettúllépésként lesznek számlázva. Az utólagos elszámolású előfizetések díjait a hitelkártyára vagy a számla fizetési módjára kell fizetni az előfizetésben.|    
|Hatókör       |A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: <ul><li>**Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.</li><li>**Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.</li><li>**Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. Az EA-ügyfelek esetében a számlázási környezet a beléptetés. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.</li></ul>|
|Region    |A foglalás által érintett Azure-régió.|    
|Virtuális gép mérete     |A virtuálisgép-példányok mérete.|
|Optimalizálás a következőhöz:     |A VM-példány méretének rugalmassága alapértelmezés szerint ki van választva. Kattintson a **Speciális beállítások** lehetőségre a példány méretének rugalmassági értékének módosításához, hogy a foglalási kedvezményt más virtuális gépekre alkalmazza ugyanabban a virtuálisgép- [méretezési csoportban](reserved-vm-instance-size-flexibility.md). A kapacitás prioritása kiemelt fontosságúként kezeli az adatközpont kapacitását az üzemelő példányok számára. További megbízhatóságot biztosít a virtuálisgép-példányok elindításához, amikor szüksége van rájuk. A kapacitás prioritása csak akkor érhető el, ha a foglalási hatókör egyetlen előfizetés. |
|Időszak        |Egy vagy három év. A HBv2-alapú virtuális gépek esetében 5 éves időszak is rendelkezésre áll.|
|Mennyiség    |A foglalás keretében megvásárolt példányok száma. A mennyiség azon futó virtuálisgép-példányok száma, amelyek megkapják a számlázási kedvezményt. Ha például 10 Standard_D2 virtuális gépet futtat az USA keleti régiójában, akkor a mennyiséget 10-re kell állítani, hogy maximalizálja az összes futó virtuális gép előnyeit. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Használati adatok és foglalás kihasználtsága

A használati adatok ára nulla azon használat esetében, amelyre érvényes a foglalási kedvezmény. Láthatja, hogy melyik virtuálisgép-példány fogadta el a foglalási kedvezményt az egyes foglalásokhoz.

További információ arról, hogyan jelennek meg a foglalási kedvezmények a használati adatok között: az [Azure foglalási szolgáltatás használatának ismertetése a nagyvállalati beléptetéshez](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) , ha Ön EA-ügyfél. Ha egyéni előfizetéssel rendelkezik, tekintse meg [Az Azure foglalás használatának megismerése](../cost-management-billing/reservations/understand-reserved-instance-usage.md)az utólagos elszámolású előfizetéshez című témakört.

## <a name="change-a-reservation-after-purchase"></a>Foglalás módosítása a vásárlás után

A vásárlás után a következő típusú módosításokat hajthatja végre a foglalásokon:

- Foglalás hatókörének frissítése
- Példány méretének rugalmassága (ha van ilyen)
- Tulajdonjog

A foglalásokat feloszthatja kisebb adattömbökre, és összevonhatja a már felosztott foglalásokat is. A módosítások egyike sem eredményez új kereskedelmi tranzakciót, vagy nem módosítja a foglalás befejezési dátumát.

A vásárlás után a következő típusú módosításokat nem végezheti el közvetlenül:

- Egy meglévő foglalás régiója
- Termékváltozat
- Mennyiség
- Időtartam

Ha szeretné módosítani a módosításokat, lehetősége van a foglalások *cseréjére* .

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md) szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
    - [Mi az az Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [A Reservations kezelése az Azure-ban](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [A foglalási kedvezmény alkalmazásának ismertetése](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [A Reservations díjában nem szereplő Windows-szoftverköltségek](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)