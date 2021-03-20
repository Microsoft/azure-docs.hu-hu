---
title: Restore-Azure Portal-Azure Database for PostgreSQL-rugalmas kiszolgáló
description: Ez a cikk bemutatja, hogyan végezheti el a visszaállítási műveleteket a Azure Database for PostgreSQL a Azure Portalon keresztül.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90935896"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Rugalmas kiszolgáló időponthoz történő visszaállítása

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk részletesen ismerteti az időponthoz tartozó helyreállításokat a rugalmas kiszolgálókon a biztonsági másolatok használatával. A megőrzési időtartamon belül a legkorábbi visszaállítási pontra vagy egy egyéni visszaállítási pontra is elvégezheti a műveletet.

## <a name="pre-requisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

-   Azure Database for PostgreSQL-rugalmas kiszolgálóval kell rendelkeznie. Ugyanez az eljárás alkalmazható a zóna-redundanciával konfigurált rugalmas kiszolgálók esetében is.

## <a name="restoring-to-the-earliest-restore-point"></a>Visszaállítás a legkorábbi visszaállítási pontra

Az alábbi lépéseket követve állíthatja vissza a rugalmas kiszolgálót a korábbi meglévő biztonsági mentés használatával.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a rugalmas kiszolgálót, amelyen vissza szeretné állítani a biztonsági mentést.

2.  Kattintson az **Áttekintés** elemre a bal oldali panelen, majd kattintson a **visszaállítás** gombra.
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Visszaállítás áttekintése":::

3.  A Restore (Visszaállítás) lap a legkorábbi visszaállítási pont és az egyéni visszaállítási pont közötti választáshoz választható.

4.  Válassza ki a **legkorábbi visszaállítási pontot** , és adjon meg egy új kiszolgálónevet a **visszaállítás új kiszolgálóként** mezőben. Ekkor megjelenik a legkorábbi időbélyeg, amelyet vissza lehet állítani. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="Legkorábbi visszaállítási idő":::

5.  Kattintson az **OK** gombra.

6.  Ekkor megjelenik egy értesítés, amely szerint a visszaállítási művelet megkezdődött.

## <a name="restoring-to-a-custom-restore-point"></a>Visszaállítás egyéni visszaállítási pontra

Az alábbi lépéseket követve állíthatja vissza a rugalmas kiszolgálót a korábbi meglévő biztonsági mentés használatával.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a rugalmas kiszolgálót, amelyen vissza szeretné állítani a biztonsági mentést.

2.  Az Áttekintés lapon kattintson a **visszaállítás** elemre.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Visszaállítás áttekintése":::
    
3.  A Restore (Visszaállítás) lap a legkorábbi visszaállítási pont és az egyéni visszaállítási pont közötti választáshoz választható.

4.  Válassza az **Egyéni visszaállítási pont** lehetőséget.

5.  Válassza a dátum és idő lehetőséget, majd adjon meg egy új kiszolgálónevet a **visszaállítás új kiszolgálóként** mezőben. 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="Egyéni visszaállítási idő":::
 
6.  Kattintson az **OK** gombra.

7.  Ekkor megjelenik egy értesítés, amely szerint a visszaállítási művelet megkezdődött.

## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
-   Tudnivalók a [biztonsági mentésről és a helyreállításról](./concepts-backup-restore.md)
