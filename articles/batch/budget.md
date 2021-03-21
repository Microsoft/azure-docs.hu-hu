---
title: A Cost Analysis és a költségvetések beállítása Azure Batch
description: Megtudhatja, hogyan hozhatja létre a költségek elemzését, hogyan állíthatja be a költségvetést, és csökkentheti a számítási erőforrások és a Batch-munkaterhelések futtatásához használt szoftverlicenc költségeit.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99091327"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>A Cost Analysis és a költségvetések beállítása Azure Batch

Ez a témakör segít megismerni a Azure Batchhöz köthető költségeket, a Batch-készletekhez vagy-fiókokhoz tartozó költségvetés beállítását, valamint a Batch-munkaterhelések költségeinek csökkentését.

## <a name="understand-costs-associated-with-batch-resources"></a>A Batch-erőforrásokkal kapcsolatos költségek megismerése

A Azure Batch használatára nem számítunk fel költségeket, bár a számítási erőforrások és a Batch-munkaterhelések futtatásához használt szoftverlicenc díja is felszámolható. A virtuális gépek (VM-EK) költségei felmerülhetnek a készletben, a virtuális gépről érkező adatátvitel vagy a felhőben tárolt bemeneti vagy kimeneti adatok miatt.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A virtuális gépek a legjelentősebb erőforrás a kötegelt feldolgozáshoz. A virtuális gépeknek a Batchhez való használatának költségeit a típus, a mennyiség és a használat időtartama alapján számítjuk ki. A virtuális gépek számlázási [lehetőségei közé tartozik az utólagos](https://azure.microsoft.com/offers/ms-azr-0003p/) elszámolású vagy a [foglalás](../cost-management-billing/reservations/save-compute-costs-reservations.md) (előre fizetve). A számítási feladattól függően mindkét fizetési lehetőség különböző előnyökkel jár, és a számla eltérő módon fog befolyásolni.

A [virtuális gépi konfigurációval](nodes-and-pools.md#virtual-machine-configuration) létrehozott készletekben található minden virtuális gépnek van egy társított operációsrendszer-lemeze, amely az Azure által felügyelt lemezeket használja. Az Azure által felügyelt lemezek további költségekkel járnak, és a többi lemez teljesítmény-szintjei is eltérő költségekkel rendelkeznek.

### <a name="storage"></a>Tárolás

Ha az alkalmazások batch-csomópontokra (VM) vannak telepítve [alkalmazáscsomag](batch-application-packages.md)használatával, akkor az alkalmazás csomagjai által felhasznált Azure Storage-erőforrásokért kell fizetnie. Emellett a bemeneti és kimeneti fájlok, például az erőforrás-fájlok és az egyéb naplófájlok tárolására is díjat számítunk fel.

Általánosságban elmondható, hogy a Batch szolgáltatáshoz kapcsolódó tárolási adatok ára sokkal alacsonyabb, mint a számítási erőforrások díja.

### <a name="networking-resources"></a>Hálózati erőforrások

A Batch-készletek a hálózati erőforrásokat használják, amelyek némelyike társított költségekkel rendelkezik. A virtuális gépek konfigurációs készletei esetében például standard Load balancert használunk, amelyek statikus IP-címeket igényelnek. A Batch által használt terheléselosztó a felhasználói előfizetési módban konfigurált [fiókok](accounts.md#batch-accounts) esetében látható, a Batch szolgáltatás módban nem.

A standard Load Balancer felszámítja a Batch-készletben lévő virtuális gépeknek átadott és onnan érkező összes adatforgalmi díjat. Válassza ki azokat a Batch API-kat, amelyek adatokat kérdeznek le a készlet csomópontjairól (például a feladat-és csomópont-fájlok lekérése), a feladathoz tartozó csomagok, az erőforrás-/kimeneti fájlok és a tárolók képei

### <a name="additional-services"></a>További szolgáltatások

Attól függően, hogy milyen szolgáltatásokat használ a Batch-megoldáshoz, további díjakat is igényelhet. Tekintse át a [díjszabási számológépet](https://azure.microsoft.com/pricing/calculator/) , és határozza meg az egyes szolgáltatások költségeit. A Batch szolgáltatással gyakran használt szolgáltatások a következők lehetnek:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Grafikus alkalmazásokkal rendelkező virtuális gépek

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>A költségek elemzésének megtekintése és a készlet költségvetésének létrehozása

A Azure Portal költségvetéseket hozhat létre, és elköltheti a Batch-készletekhez vagy a Batch-fiókokhoz tartozó riasztásokat. A költségvetések és a riasztások akkor hasznosak, ha az érintett feleket a túllépések kockázatával kapcsolatban értesítik, bár lehetséges, hogy a riasztások elköltése késéssel jár, és a költségvetés némileg meghaladja a költségvetést.

> [!NOTE]
> Az ebben a példában szereplő készlet a **virtuális gép konfigurációját** használja, amely a legtöbb készlet esetében ajánlott, és a Virtual Machines árképzési struktúra alapján díjköteles. A **Cloud Services konfigurációt** használó készleteket a Cloud Services árképzési struktúra alapján számítjuk fel.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Batch-készlet költséghatékonyságának megtekintése

1. A Azure Portal írja be a következőt: vagy válassza a **Cost Management + számlázás** lehetőséget.
1. Válassza ki az előfizetését a **Számlázási hatókörök** szakaszban.
1. A **Költségkezelés** területen válassza a **Költségelemzés** lehetőséget.
1. Válassza a **szűrő hozzáadása** lehetőséget. Az első legördülő menüben válassza az **erőforrás** elemet.
1. A második legördülő menüben válassza ki a Batch-készletet. Ha a készlet van kiválasztva, az itt látható példához hasonlóan megjelenik a készlethez tartozó Cost-elemzés.
    ![Képernyőfelvétel: a Azure Portal készletének Cost Analysis.](./media/batch-budget/pool-cost-analysis.png)

Az eredményül kapott Cost Analysis megjeleníti a készlet költségeit, valamint az ehhez a díjakhoz hozzájáruló erőforrásokat. Ebben a példában a készletben használt virtuális gépek a legdrágább erőforrások.

### <a name="create-a-budget-for-a-batch-pool"></a>Költségvetés létrehozása batch-készlethez

1. A **Cost Analysis** lapon válassza a **költségvetés: nincs** értéket.
1. Válassza az **új költségvetési >létrehozása** lehetőséget.
1. Az eredményül kapott ablak használatával konfigurálhat egy költségvetést kifejezetten a készlethez. További információt az [oktatóanyag: Azure-költségvetések létrehozása és kezelése](../cost-management-billing/costs/tutorial-acm-create-budgets.md)című témakörben talál.

## <a name="minimize-costs-associated-with-azure-batch"></a>Azure Batchhez kapcsolódó költségek csökkentése

A forgatókönyvtől függően előfordulhat, hogy a lehető legnagyobb mértékben csökkenteni kívánja a költségeket. Vegye fontolóra egy vagy több ilyen stratégia használatát, hogy maximalizálja a számítási feladatok hatékonyságát, és csökkentse a lehetséges költségeket.

### <a name="use-low-priority-virtual-machines"></a>Alacsony prioritású virtuális gépek használata

Az [alacsony prioritású virtuális gépek](batch-low-pri-vms.md) csökkentik a Batch-munkaterhelések költségeit azáltal, hogy kihasználják a felesleges számítási kapacitást az Azure-ban. Ha alacsony prioritású virtuális gépeket ad meg a készletekben, a Batch ezt a többletet használja a számítási feladatok futtatásához. A dedikált virtuális gépek helyett jelentős költségmegtakarítást érhet el, ha alacsony prioritású virtuális gépeket használ.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Válassza ki a virtuális gép operációsrendszer-lemezének szabványos típusát

Az Azure több [virtuális gép operációsrendszer-lemez típusát](../virtual-machines/disks-types.md)is biztosítja. A legtöbb virtuálisgép-sorozat a prémium és a standard szintű tárolást egyaránt támogató méretekkel rendelkezik. Ha egy készlethez a virtuális gép mérete van kiválasztva, a Batch a prémium szintű SSD operációsrendszer-lemezeket konfigurálja. Ha a "nem s" virtuálisgép-méret van kiválasztva, akkor a rendszer az olcsóbb, standard szintű HDD-lemez típusát használja. A prémium szintű SSD operációsrendszer-lemezeket például a `Standard_D2s_v3` és a standard HDD operációsrendszer-lemezek használják `Standard_D2_v3` .

Prémium SSD operációsrendszer-lemezek drágábbak, de nagyobb teljesítménnyel rendelkeznek. A prémium szintű lemezekkel rendelkező virtuális gépek valamivel gyorsabbak lehetnek, mint a standard HDD operációsrendszer-lemezekkel rendelkező virtuális gépek. A Batch esetében az operációsrendszer-lemez gyakran nem sokat használatos, mivel az alkalmazások és a feladatok fájljai a virtuális gép ideiglenes SSD-lemezén találhatók. Emiatt gyakran kiválaszthatja a "nem s" virtuálisgép-méretet, így nem kell megfizetnie a prémium SSD számára, amely a virtuális gép méretének megadásakor lett kiépítve.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Virtuálisgép-példányok vásárlásának foglalása

Ha hosszú ideig kívánja használni a Batch-t, csökkentheti a virtuális gépek költségeit a számítási feladatok [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) használatával. A foglalások díja jelentősen alacsonyabb, mint az utólagos elszámolású díjszabás. A foglalások nélkül használt virtuálisgép-példányokat az utólagos elszámolású díjszabás szerint számoljuk el. Foglalás vásárlása esetén a foglalási kedvezmény érvényes.

### <a name="use-automatic-scaling"></a>Automatikus skálázás használata

Az [automatikus skálázás](batch-automatic-scaling.md) dinamikusan méretezi a Batch-készletben lévő virtuális gépek számát az aktuális feladatokhoz tartozó igények alapján. Ha a készletet a feladatok élettartama alapján méretezi, az automatikus skálázás biztosítja, hogy a virtuális gépek horizontális felskálázása és használata csak akkor történjen, amikor a feladatok elvégzésére van szükség. Ha a feladat befejeződött, vagy ha nincsenek feladatok, a virtuális gépek automatikusan le lesznek méretezve a számítási erőforrások mentéséhez. A skálázás lehetővé teszi, hogy csak a szükséges erőforrások használatával csökkentse a Batch-megoldás teljes költségeit.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Cost Management és a számlázásról](../cost-management-billing/cost-management-billing-overview.md)
- Ismerje meg [, hogyan használhatja az alacsony prioritású virtuális gépeket a Batch használatával](batch-low-pri-vms.md).
