---
title: Virtuálisgép-méretezési csoportok méretezése a Azure Portal
description: A virtuálisgép-méretezési csoportokra vonatkozó autoskálázási szabályok létrehozása a Azure Portalban
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms:service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 4bb4084a7ee0d9d719a88c077fa8c63803aa6934
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591363"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Virtuálisgép-méretezési csoport automatikus méretezése a Azure Portal
Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra.

Ebből a cikkből megtudhatja, hogyan hozhat létre a méretezési csoporton belüli virtuálisgép-példányok teljesítményét figyelő Azure Portalban található autoskálázási szabályokat. Ezek az autoskálázási szabályok fokozzák vagy csökkentik a virtuálisgép-példányok számát, válaszul ezekre a teljesítmény-mérőszámokra. Ezeket a lépéseket [Azure PowerShell](tutorial-autoscale-powershell.md) vagy az [Azure CLI](tutorial-autoscale-cli.md)-vel is elvégezheti.


## <a name="prerequisites"></a>Előfeltételek
Az autoskálázási szabályok létrehozásához szükség van egy meglévő virtuálisgép-méretezési csoportra. Létrehozhat egy méretezési csoport [Azure Portal](quick-create-portal.md), [Azure POWERSHELL](quick-create-powershell.md)vagy [Azure CLI](quick-create-cli.md)használatával.


## <a name="create-a-rule-to-automatically-scale-out"></a>Automatikus skálázási szabály létrehozása
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

1. Nyissa meg a Azure Portal, majd az irányítópult bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget.
2. Válassza ki a méretezési csoportot tartalmazó erőforráscsoportot, majd válassza ki a méretezési csoportot az erőforrások listájából.
3. A méretezési csoport ablak bal oldali menüjében válassza a **skálázás** lehetőséget. Kattintson a gombra az **autoscale engedélyezéséhez**:

    ![Az autoskálázás engedélyezése a Azure Portalban](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Adja meg a beállítások nevét, például az *autoskálázást*, majd válassza a **szabály hozzáadásának** lehetőségét.

5. Hozzon létre egy szabályt, amely növeli a méretezési csoportokban lévő virtuálisgép-példányok számát, ha az átlagos CPU-terhelés meghaladja a 70%-ot egy 10 perces időszak alatt. A szabály indításakor a virtuálisgép-példányok száma 20%-kal nő. Kis mennyiségű virtuálisgép-példánnyal rendelkező méretezési csoportokban beállíthatja, hogy  a művelet *növelje a Count értéket* , majd adja meg az *1* vagy a *2* értéket a *példányszámhoz*. Nagy mennyiségű virtuálisgép-példánnyal rendelkező méretezési csoportokban a 10%-os vagy 20%-os virtuálisgép-példányok nagyobb mértékben megfelelőbbek lehetnek.

    Adja meg a következő beállításokat a szabályhoz:
    
    | Paraméter              | Magyarázat                                                                                                         | Érték          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Idő összesítése*     | Meghatározza, hogy az összegyűjtött metrikák hogyan legyenek összesítve az elemzéshez.                                                | Átlag        |
    | *Metrika neve*          | A méretezési csoporthoz tartozó műveletek monitorozásának és alkalmazásának teljesítménymutatója.                                                   | Százalékos processzorhasználat |
    | *Időfelbontási szint statisztikája* | Azt határozza meg, hogy az összegyűjtött mérőszámok hogyan legyenek összesítve az elemzéshez.                             | Átlag        |
    | *Művelet*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                     | Nagyobb, mint   |
    | *Küszöbérték*            | Az az érték, amelynek hatására az autoskálázási szabály elindítja a műveletet.                                                 | 70             |
    | *Időtartam*             | A mérőszám és a küszöbértékek összehasonlítása előtt monitorozott időtartam. Nem tartalmazza a lehűtési időszakot.                                   | 10 perc     |
    | *Művelet*            | Meghatározza, hogy a méretezési csoport fel vagy le legyen-e méretezve a szabály alkalmazása és a növekmény alapján.                        | Százalék növelésének aránya |
    | *Példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                            | 20             |
    | *Lehűlés (perc)*  | Mennyi idő teljen el a szabály újbóli alkalmazása előtt, hogy az automatikus skálázási műveletek kifejthessék a hatásukat. | 5 perc      |

    Az alábbi példák egy olyan szabályt mutatnak be, amely a Azure Portal a következő beállításoknak felel meg:

    ![Hozzon létre egy autoskálázási szabályt a virtuálisgép-példányok számának növeléséhez](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. A szabály létrehozásához válassza a **Hozzáadás** lehetőséget.


## <a name="create-a-rule-to-automatically-scale-in"></a>Automatikus skálázási szabály létrehozása
Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

1. Adja meg ismét **a szabályt** .
2. Hozzon létre egy szabályt, amely csökkenti a méretezési csoportokban lévő virtuálisgép-példányok számát, ha az átlagos CPU-terhelés, majd a 10 perces időszak alatt 30% alá csökken. A szabály indításakor a virtuálisgép-példányok száma 20%-kal csökken.

    Ugyanazt a módszert használja, mint az előző szabályhoz. Módosítsa a szabály következő beállításait:
    
    | Paraméter              | Magyarázat                                                                                                          | Érték          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operátor*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                      | Kisebb, mint   |
    | *Küszöbérték*            | Az az érték, amelynek hatására az autoskálázási szabály elindítja a műveletet.                                                 | 30             |
    | *Művelet*            | Meghatározza, hogy a méretezési csoport fel-vagy leskálázása legyen, ha a szabály érvényes, és milyen növekmény                         | Százalék csökkentése a következővel: |
    | *Példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                             | 20             |

3. A szabály létrehozásához válassza a **Hozzáadás** lehetőséget.


## <a name="define-autoscale-instance-limits"></a>Az autoscale-példányokra vonatkozó korlátok meghatározása
Az autoskálázási profilnak meg kell határoznia a virtuálisgép-példányok minimális, maximális és alapértelmezett számát. Az autoskálázási szabályok alkalmazása esetén ezek a példányok korlátozásai biztosítják, hogy a példányok maximális száma túllépve ne legyen kibővítve, vagy a minimálisan megengedettnél nagyobb méretű.

1. Adja meg a következő példányok korlátait:

    | Minimum | Maximum | Alapértelmezett|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Az autoskálázási szabályok és a példányok korlátainak alkalmazásához válassza a **Mentés** lehetőséget.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>A méretezési csoport példányainak számának figyelése
A virtuálisgép-példányok számának és állapotának megtekintéséhez a méretezési csoport ablak bal oldali menüjében válassza a **példányok** lehetőséget. Az állapot azt jelzi, hogy a virtuálisgép-példány *létrehozása* folyamatban van-e a méretezési csoport automatikus skálázása esetén, vagy ha a méretezés automatikusan méretezi a *-t.*

![A méretezési csoport virtuálisgép-példányainak listájának megtekintése](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Ütemterv alapján történő autoskálázás
Az előző példákban az alapszintű gazdagép-mérőszámokkal, például a CPU-használattal automatikusan méretezhető egy méretezési csoport. Az ütemterveken alapuló autoskálázási szabályokat is létrehozhat. Ezek az ütemezési szabályok lehetővé teszik a virtuálisgép-példányok számának automatikus méretezését az alkalmazás iránti igény várható növekedésének, például az alapszintű munkaidőnek a növelésével, majd a példányok számának automatikus skálázásával, amikor a kevesebb igényt, például a hétvégét tervezi.

1. A méretezési csoport ablak bal oldali menüjében válassza a **skálázás** lehetőséget. Az előző példákban létrehozott meglévő autoskálázási szabályok törléséhez válassza a Kuka ikont.

    ![A meglévő autoskálázási szabályok törlése](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Válassza a **méretezési feltétel hozzáadása** lehetőséget. Válassza a szabály neve melletti ceruza ikont, és adjon meg egy nevet, például a *Felskálázást az egyes munkanapokon*.

    ![Az alapértelmezett autoskálázási szabály átnevezése](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Válassza ki a választógombot, hogy **egy adott példányszámot szeretne méretezni**.
4. A példányok számának vertikális felskálázásához adja meg a *10-es* értéket a példányszám mezőben.
5. Válassza az **adott napok megismétlése** lehetőséget az **ütemterv** típusához.
6. Válassza ki az összes munkanap, hétfőtől péntekig.
7. Válassza ki a megfelelő időzónát, majd adja meg a *09:00* **kezdési időpontját** .
8. Válassza a **méretezési feltétel ismételt hozzáadását** . Ismételje meg a folyamatot, ha a Scale (méretezés) nevű ütemtervet szeretné létrehozni *az esti időszakban* , amely *3* példányra méretezi, minden hétköznap ismétlődik, és *18:00*-kor kezdődik.
9. Az ütemterv szerinti autoskálázási szabályok alkalmazásához válassza a **Mentés** lehetőséget.

    ![Ütemterv szerinti méretezést biztosító autoskálázási szabályok létrehozása](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Az automatikus skálázási szabályok alkalmazásának megtekintéséhez válassza a **futtatási előzmények** lehetőséget a **skálázási** ablak tetején. A Graph és az Events (diagramok és események) lista azt mutatja be, hogy az autoskálázási szabályok triggere és a méretezési csoport virtuálisgép-példányainak száma nő vagy csökken.


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használhatja az autoskálázási szabályokat horizontális méretezésre, és növelheti vagy csökkentheti a méretezési csoport virtuálisgép-példányainak *számát* . A virtuálisgép-példány *méretének* növeléséhez vagy csökkentéséhez függőlegesen is méretezheti a méretezést. További információkért lásd: [vertikális autoskálázás virtuális gépi méretezési csoportokkal](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A virtuálisgép-példányok kezelésével kapcsolatos információkért lásd: [virtuálisgép-méretezési csoportok kezelése Azure PowerShellokkal](./virtual-machine-scale-sets-manage-powershell.md).

Ha meg szeretné tudni, hogyan hozhatók riasztások az automatikusan méretezhető szabályok indításakor, tekintse meg az [e-mailek és webhookok riasztási értesítéseinek Azure monitor-ben történő küldését](../azure-monitor/autoscale/autoscale-webhook-email.md)ismertető témakört. A [naplók használatával e-mail-és webhook](../azure-monitor/alerts/alerts-log-webhook.md)-értesítéseket is küldhet a Azure monitor.
