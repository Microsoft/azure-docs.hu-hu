---
title: Virtuálisgép-méretezési készletek automatikus méretezése a Azure Portal
description: Automatikus méretezési szabályok létrehozása virtuálisgép-méretezési készletekhez a Azure Portal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 2ee2db62cf43dc191da2b92f7d4b67ff775f628f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537524"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Virtuálisgép-méretezési készlet automatikus méretezése a Azure Portal
Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra.

Ez a cikk bemutatja, hogyan hozhat létre automatikus méretezési szabályokat a Azure Portal a méretezési csoportban található virtuálisgép-példányok teljesítményét monitorozni. Ezek az automatikus méretezési szabályok növelik vagy csökkentik a virtuálisgép-példányok számát ezekre a teljesítménymetrikákra válaszul. Ezeket a lépéseket az Azure [CLI-Azure PowerShell](tutorial-autoscale-powershell.md) is [végre lehet.](tutorial-autoscale-cli.md)


## <a name="prerequisites"></a>Előfeltételek
Automatikus méretezési szabályok létrehozásához egy meglévő virtuálisgép-méretezési csoportra van szükség. Méretezési csoportokat a következőkkel hozhat [létre: , Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) [Azure CLI.](quick-create-cli.md)


## <a name="create-a-rule-to-automatically-scale-out"></a>Automatikus horizontális felskálás szabályának létrehozása
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

1. Nyissa meg Azure Portal, és válassza az Erőforráscsoportok **lehetőséget** az irányítópult bal oldali menüjében.
2. Válassza ki a méretezési csoportot tartalmazó erőforráscsoportot, majd válassza ki a méretezési csoportot az erőforrások listájából.
3. Válassza **a Méretezés** lehetőséget a méretezési készlet ablakának bal oldalán található menüből. Kattintson a gombra az **Automatikus skálázás engedélyezése lehetőséghez:**

    ![Automatikus méretezés engedélyezése a Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Adjon nevet a beállításoknak, például *automatikus skálázás,* majd válassza **a Szabály hozzáadása lehetőséget.**

5. Hozzunk létre egy szabályt, amely növeli a méretezési csoportban található virtuálisgép-példányok számát, ha az átlagos processzorterhelés 10 percen keresztül meghaladja a 70%-ot. Ha a szabály aktiválódik, a virtuálisgép-példányok száma 20%-kal növekszik. Kis számú virtuálisgép-példányt számláló méretezési készletekben  beállíthatja a műveletet a Darabszám növelése beállításra, majd 1-et vagy  *2-t* is megadhat a *példányszámhoz.*  A nagy számú virtuálisgép-példányt is képes méretezési készletekben a 10%-os vagy 20%-os virtuálisgép-példányok növelése megfelelőbb lehet.

    Adja meg a következő beállításokat a szabályhoz:
    
    | Paraméter              | Magyarázat                                                                                                         | Érték          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Időösszesítő*     | Meghatározza, hogy az összegyűjtött metrikák hogyan legyenek összesítve az elemzéshez.                                                | Átlag        |
    | *Metrika neve*          | A méretezési csoporthoz tartozó műveletek monitorozásának és alkalmazásának teljesítménymutatója.                                                   | Százalékos processzorhasználat |
    | *Időfelbontási szint statisztikája* | Meghatározza, hogyan kell összesíteni az egyes időmetrikákban gyűjtött metrikákat az elemzéshez.                             | Átlag        |
    | *Művelet*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                     | Nagyobb, mint   |
    | *Küszöbérték*            | Az az arány, amely miatt az automatikus skálázási szabály aktivál egy műveletet.                                                 | 70             |
    | *Időtartam*             | A mérőszám és a küszöbértékek összehasonlítása előtt monitorozott időtartam. Nem tartalmazza a lehűtő hűtési időszakot.                                   | 10 perc     |
    | *Művelet*            | Meghatározza, hogy a méretezési készletet felfelé vagy lefelé kell-e skálázni, amikor a szabály érvényben van, és milyen növekményekkel.                        | Százalék növelése a következővel: |
    | *Példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                            | 20             |
    | *Lehűlés (perc)*  | Mennyi idő teljen el a szabály újbóli alkalmazása előtt, hogy az automatikus skálázási műveletek kifejthessék a hatásukat. | 5 perc      |

    Az alábbi példákban egy, a Azure Portal létrehozott szabály látható:

    ![Automatikus méretezési szabály létrehozása a virtuálisgép-példányok számának növeléséhez](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. A szabály létrehozásához válassza a Hozzáadás **lehetőséget.**


## <a name="create-a-rule-to-automatically-scale-in"></a>Szabály létrehozása automatikus horizontális leskálazhoz
Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

1. Válassza ismét **a Szabály hozzáadása** lehetőséget.
2. Hozzon létre egy szabályt, amely csökkenti a méretezési csoportban található virtuálisgép-példányok számát, amikor az átlagos processzorterhelés 10 percen keresztül 30% alá csökken. Ha a szabály aktiválódik, a virtuálisgép-példányok száma 20%-kal csökken.

    Használja ugyanazt a módszert, mint az előző szabályban. Módosítsa a következő beállításokat a szabályhoz:
    
    | Paraméter              | Magyarázat                                                                                                          | Érték          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operátor*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                      | Kisebb, mint   |
    | *Küszöbérték*            | Az az arány, amely miatt az automatikus skálázási szabály aktivál egy műveletet.                                                 | 30             |
    | *Művelet*            | Meghatározza, hogy a méretezési készletet felfelé vagy lefelé kell-e skálázni, amikor a szabály érvényben van, és milyen növekményt                         | Százalék csökkentése a következővel: |
    | *Példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                             | 20             |

3. A szabály létrehozásához válassza a Hozzáadás **lehetőséget.**


## <a name="define-autoscale-instance-limits"></a>Automatikus méretezési példány korlátainak meghatározása
Az automatikus méretezési profilnak meg kell határoznia a virtuálisgép-példányok minimális, maximális és alapértelmezett számát. Az automatikus méretezési szabályok alkalmazásakor ezek a példánykorlátok mindenképpen ügyeljenek arra, hogy ne skálázjon fel horizontálisan a példányok maximális számán túlra, és ne skálázjon le a minimális példányszámon túlra.

1. Állítsa be a következő példánykorlátokat:

    | Minimum | Maximum | Alapértelmezett|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Az automatikus méretezési szabályok és a példánykorlátok alkalmazáshoz válassza a **Mentés lehetőséget.**


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Egy méretezési készlet példányszámának figyelése
A virtuálisgép-példányok számának és  állapotának a méretezésihalmaz-ablak bal oldali menüjében válassza a Példányok lehetőséget. Az állapot jelzi, hogy a virtuálisgép-példány *Létrehozás,* amikor  a méretezési csoport automatikusan horizontálisan felskálul, vagy Törlés, amikor a skálázás automatikusan leméretezést jelez.

![Méretezésikészlet-virtuálisgép-példányok listájának megtekintése](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatikus skálázás ütemezés alapján
Az előző példák automatikusan horizontálisan fel- vagy felskáláltak egy méretezési készletet olyan alapvető gazdagép-metrikák segítségével, mint a CPU-használat. Az ütemezések alapján automatikus méretezési szabályokat is létrehozhat. Ezekkel az ütemezésalapú szabályokkal automatikusan felskálálhatja a virtuálisgép-példányok számát az alkalmazásigény várható növekedése , például a központi munkaidő előtt, majd automatikusan skálázhatja a példányok számát egy olyan időpontban, amikor várhatóan kisebb lesz a kereslet, például a hétvégen.

1. Válassza **a Méretezés** lehetőséget a méretezési készlet ablakának bal oldalán található menüből. Az előző példákban létrehozott meglévő automatikus méretezési szabályok törléséhez válassza a kuka ikont.

    ![A meglévő automatikus méretezési szabályok törlése](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Válassza a **Méretezési feltétel hozzáadása lehetőséget.** Kattintson a ceruza ikonra a szabály neve mellett, és adjon meg egy nevet, például: Horizontális felskálás *az egyes munkanapok során.*

    ![Az alapértelmezett automatikus méretezési szabály átnevezése](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Jelölje be a választógombot **a Skálázás adott példányszámra beállításhoz.**
4. A példányok számának felskálol a *10-et* a példányok számaként.
5. Az **Ütemezés típushoz válassza** a Megadott napok **ismétlése** lehetőséget.
6. Válassza ki az összes munkanapot, hétfőtől péntekig.
7. Válassza ki a megfelelő időzónát, majd **adjon** meg egy *09:00 kezdőidőt.*
8. Válassza ismét **a Méretezési feltétel hozzáadása** lehetőséget. Ismételje meg a folyamatot,  és hozzon létre egy horizontális leskálás nevű ütemezést este, amely *3* példányra skálázható, minden hétköznapon ismétlődik, és *18:00-kor kezdődik.*
9. Az ütemezésalapú automatikus méretezési szabályok alkalmazáshoz válassza a Mentés **lehetőséget.**

    ![Ütemezés szerint skálázású automatikus méretezési szabályok létrehozása](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Az automatikus méretezési szabályok alkalmazásának látnia kell, ha a **Méretezés** ablak tetején található Futtatáselőzmények **lehetőséget** választja. A gráfok és események listája azt mutatja, hogy mikor aktiválódnak az automatikus méretezési szabályok, és mikor nő vagy csökken a méretezési készletben található virtuálisgép-példányok száma.


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használhatja az automatikus méretezési  szabályokat horizontális skálázáshoz, illetve a méretezési készletben található virtuálisgép-példányok számának növeléséhez vagy csökkentéséhez. Vertikálisan is skálázhat a virtuálisgép-példány méretének növeléséhez vagy *csökkentéséhez.* További információ: Vertikális automatikus [skálázás virtuálisgép-méretezési készletekkel.](virtual-machine-scale-sets-vertical-scale-reprovision.md)

További információ a virtuálisgép-példányok kezelésével kapcsolatban: Virtuálisgép-méretezési készletek kezelése [a Azure PowerShell.](./virtual-machine-scale-sets-manage-powershell.md)

Ha többet szeretne megtudni arról, hogyan hozhat létre riasztásokat az automatikus méretezési szabályok aktiválása során, tekintse meg a következőt: Automatikus méretezési műveletek használata e-mail- és [webhook-riasztási](../azure-monitor/autoscale/autoscale-webhook-email.md)Azure Monitor. Auditnaplók használatával e-mailes és [webhook-riasztási](../azure-monitor/alerts/alerts-log-webhook.md)értesítéseket is küldhet a Azure Monitor.
