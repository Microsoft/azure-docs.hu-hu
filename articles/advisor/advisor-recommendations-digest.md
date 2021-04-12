---
title: Azure Advisor kivonatának javaslata
description: Az aktív javaslatok rendszeres összefoglalásának beolvasása
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 1c82abf3c8bea07941f860b94fcfe92954fb7641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579814"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Javaslatok rendszeres összegzésének konfigurálása

Az Advisor **Ajánlói kivonatok** egyszerű és proaktív módszert biztosítanak az aktív javaslatok és a különböző kategóriák közötti tartózkodáshoz. A funkció lehetővé teszi, hogy rendszeres értesítéseket konfiguráljon az összes aktív javaslat összefoglalásához, különböző kategóriák között. A kívánt csatornát kiválaszthatja az értesítések, például az e-mailek, az SMS-cím és mások számára a műveleti csoportok használatával. Ebből a cikkből megtudhatja, hogyan állíthat be **ajánlási kivonatokat** az Advisor javaslataihoz.


## <a name="setting-up-your-recommendation-digest"></a>Ajánlási kivonat beállítása 

Az **ajánlás kivonatoló** létrehozási élménye segít az összefoglalás konfigurálásában. A konfigurációk alábbi paraméterei közül választhat:
1. Kategória: javasoljuk, hogy a költségeket, a magas rendelkezésre állást, a teljesítményt és az üzemeltetési kiválóságot is felkínáljuk. A funkció még nem érhető el biztonsági javaslatokhoz.
2. Kivonatoló gyakoriság: az összegző értesítések gyakorisága hetente, kéthetente és havonta is elvégezhető.
3. Műveleti Csoport: választhat egy meglévő műveleti csoportot, vagy létrehozhat egy új műveleti csoportot. A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése](../azure-monitor/alerts/action-groups.md).
4. A kivonatoló nyelve
5. Javaslat kivonatoló neve: használhat egy felhasználóbarát karakterláncot a kivonatok jobb nyomon követéséhez és figyeléséhez.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Javaslatok kivonatának létrehozásának lépései Azure Portal

Az **ajánlási kivonat** létrehozásának lépései a következők:
* **1. lépés:** A Azure Portal lépjen az **Advisor** elemre, és a **figyelés** szakaszban válassza az **ajánlás kivonatoló** lehetőséget. 

   ![Javaslat kivonatoló belépési pont](./media/digest-0.png)

* **2. lépés:** Válassza az **új javaslat kivonat** lehetőséget a felső sávon az alábbi módon:

   ![Javaslat kivonatának létrehozása](./media/digest-5.png)

* **3. lépés:** A **hatókör** szakaszban válassza ki a kivonatoló **előfizetését** .

   ![Ajánlási kivonatoló bemenetek megadása](./media/digest-1.png)

* **4. lépés:** A **feltétel** szakaszban válassza ki azokat a konfigurációkat, mint a **Kategória**, a **gyakoriság** és a **nyelv** .

   ![Ajánlási kivonatoló beviteli feltételek megadása](./media/digest-2.png)

* **5. lépés:** A **műveleti csoport** szakaszban válassza ki a kivonatoló **műveleti csoportot** . További információt itt talál: [műveleti csoportok létrehozása és kezelése](../azure-monitor/alerts/action-groups.md)

   ![Ajánlás kivonatoló bemeneti műveleti csoportjának megadása](./media/digest-3.png)

* **6. lépés:** Ebben az utolsó szakaszban a **kivonatoló adatokhoz** nevet és állapotot rendelhet a javaslat kivonatához. A beállítás befejezéséhez nyomja meg az **ajánlás létrehozása kivonatot** .
   ![Javaslatok kivonatának teljes létrehozása](./media/digest-4.png)

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:
* [Az Azure Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
