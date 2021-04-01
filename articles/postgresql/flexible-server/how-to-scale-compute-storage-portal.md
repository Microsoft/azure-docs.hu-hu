---
title: Skálázási műveletek – Azure Portal-Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan hajtható végre a méretezési műveletek végrehajtása Azure Database for PostgreSQL a Azure Portalon keresztül.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936124"
---
# <a name="scale-operations-in-flexible-server"></a>Skálázási műveletek rugalmas kiszolgálón

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk a számítási és tárolási műveletek skálázási műveleteinek végrehajtását ismerteti. A számítási szintek megváltoztathatók a feltört, az általános célú és a memóriára optimalizált SKU-kal, beleértve az alkalmazás futtatásához megfelelő virtuális mag számát is. A tárhelyet is felskálázással bővítheti. A várt IOPS a számítási szintek, a virtuális mag és a tárolási kapacitás alapján jelennek meg. A becsült érték a kiválasztott lehetőségek alapján is megjelenik.

> [!IMPORTANT]
> A tárterület nem méretezhető le.

## <a name="pre-requisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

-   Azure Database for PostgreSQL-rugalmas kiszolgálóval kell rendelkeznie. Ugyanez az eljárás alkalmazható a zóna-redundanciával konfigurált rugalmas kiszolgálók esetében is.
> [!IMPORTANT]
> Ha magas rendelkezésre állással van konfigurálva, akkor nem választhat feltört SKU-t. A skálázási művelet során a rendszer először a kívánt méretre méretezi a készenléti állapotot, az elsődleges kiszolgáló feladatátvételt folytat, és az elsődleges méretezése megtörténik. 

## <a name="scaling-the-compute-tier-and-size"></a>A számítási rétegek és méretek méretezése

A számítási szintek kiválasztásához kövesse az alábbi lépéseket.
 
1.  A [Azure Portal](https://portal.azure.com/)válassza ki a rugalmas kiszolgálót, amelyen vissza szeretné állítani a biztonsági mentést.

2.  Kattintson a **számítás + tárolás** elemre.

3.  Megjelenik az aktuális beállításokkal rendelkező oldal.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="számítási és tárolási nézet":::

4.  Kiválaszthatja a számítási osztályt a feltört, az általános célú és a memóriára optimalizált rétegek között.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="számítási rétegek listázása":::


5.  Ha jó az alapértelmezett virtuális mag és a memória méretével, kihagyhatja a következő lépést.

6.  Ha módosítani szeretné a virtuális mag számát, kattintson a **számítási méret** legördülő listára, és a listából válassza ki a kívánt virtuális mag/memória értéket.
    
    - Feltört számítási szint: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="feltört számítás":::

    - Általános célú számítási réteg: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="általános célú számítás":::

    - Memória-optimalizált számítási szintek: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="memória-optimalizált számítás":::

7.  Kattintson a **Mentés** gombra. 
8.  Megerősítő üzenet jelenik meg. Ha folytatni szeretné a folytatást, kattintson **az OK gombra** . 
9.  Értesítés a skálázási műveletről.


## <a name="scaling-storage-size"></a>A tároló méretének méretezése

A tárterület méretének növeléséhez kövesse az alábbi lépéseket.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a rugalmas kiszolgálót, amelyhez a tárterület méretét szeretné emelni.
2.  Kattintson a **számítás + tárolás** elemre.

3.  Megjelenik az aktuális beállításokkal rendelkező oldal.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="kattintson a számítás + tárolás elemre.":::
4.  A mező **tárolási mérete a GIB-ban** egy görgetősávmal jelenik meg az aktuális mérettel.

5.  Csúsztassa a sávot a kívánt méretre. A megfelelő IOPS-szám jelenik meg. A IOPS a számítási szintjétől és mérettől függ. A költségadatok is megjelennek. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="tárterület felskálázása":::

6.  Ha jó a tárterület mérete, kattintson a **Mentés** gombra. 
7.  Megerősítő üzenet jelenik meg. Ha folytatni szeretné a folytatást, kattintson **az OK gombra** . 
8.  Értesítés a skálázási műveletről.

## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   Tudnivalók a [magas rendelkezésre állásról](./concepts-high-availability.md)
-   Tudnivalók a [biztonsági mentésről és a helyreállításról](./concepts-backup-restore.md)
