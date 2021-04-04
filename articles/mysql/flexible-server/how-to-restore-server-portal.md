---
title: Restore-Azure Portal-Azure Database for MySQL-rugalmas kiszolgáló
description: Ez a cikk bemutatja, hogyan végezheti el a visszaállítási műveleteket a Azure Database for MySQL a Azure Portalon keresztül.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241956"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL rugalmas kiszolgáló időponthoz való visszaállítása (előzetes verzió)


> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk részletesen ismerteti az időponthoz tartozó helyreállításokat a rugalmas kiszolgálókon a biztonsági másolatok használatával.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

-   Azure Database for MySQL rugalmas kiszolgálóval kell rendelkeznie.

## <a name="restore-to-the-latest-restore-point"></a>Visszaállítás a legújabb visszaállítási pontra

Az alábbi lépéseket követve állíthatja vissza a rugalmas kiszolgálót a korábbi meglévő biztonsági mentés használatával.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a rugalmas kiszolgálót, amelyen vissza szeretné állítani a biztonsági mentést.

2.  A bal oldali panelen kattintson az **Áttekintés** elemre.

3.  Az Áttekintés lapon kattintson a **visszaállítás** elemre.

    Helyőrző

4.  A Restore (Visszaállítás) lap a **legutóbbi visszaállítási pont** és az egyéni visszaállítási pont közötti választáshoz választható.

5.  Válassza a **legutóbbi visszaállítási pontot**.


6.  Adja meg az új kiszolgáló nevét a **visszaállítás új kiszolgálóként** mezőben.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Legkorábbi visszaállítási idő":::

8.  Kattintson az **OK** gombra.

9.  Ekkor megjelenik egy értesítés, amely szerint a visszaállítási művelet megkezdődött.

## <a name="restoring-to-a-custom-restore-point"></a>Visszaállítás egyéni visszaállítási pontra

Az alábbi lépéseket követve állíthatja vissza a rugalmas kiszolgálót a korábbi meglévő biztonsági mentés használatával.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a rugalmas kiszolgálót, amelyen vissza szeretné állítani a biztonsági mentést.

2.  Az Áttekintés lapon kattintson a **visszaállítás** elemre.

    Helyőrző

3.  A Restore (Visszaállítás) lap a legkorábbi visszaállítási pont és az egyéni visszaállítási pont közötti választáshoz választható.

4.  Válassza az **Egyéni visszaállítási pont** lehetőséget.

5.  Válassza ki a dátumot és az időt.

6.  Adja meg az új kiszolgáló nevét a **visszaállítás új kiszolgálóként** mezőben.

6.  Adja meg az új kiszolgáló nevét a **visszaállítás új kiszolgálóként** mezőben. 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="megtekintés áttekintése":::
 
7.  Kattintson az **OK** gombra.

8.  Ekkor megjelenik egy értesítés, amely szerint a visszaállítási művelet megkezdődött.

## <a name="next-steps"></a>Következő lépések

Helyőrző
