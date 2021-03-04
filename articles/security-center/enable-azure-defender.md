---
title: Azure Security Center integrált munkaterhelés-védelmének engedélyezése
description: Ismerje meg, hogyan bővítheti az Azure Defender Azure Security Center védelmét a hibrid és a többfelhős erőforrásokra
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108873"
---
# <a name="quickstart-enable-azure-defender"></a>Gyors útmutató: az Azure Defender engedélyezése

Az Azure Defender előnyeinek megismeréséhez lásd: [Security Center Free vs Azure Defender engedélyezve](security-center-pricing.md).

## <a name="prerequisites"></a>Előfeltételek

A Security Center rövid útmutatók és oktatóanyagok esetében engedélyeznie kell az Azure Defendert. 

Az Azure Defender teljes Azure-előfizetését védelemmel láthatja el, és az előfizetésben lévő összes erőforrás örökli a védelmet.

Ingyenes 30 napos próbaverzió érhető el. A díjszabással kapcsolatos részletekért a választott pénznemben és a régiója szerint tekintse meg a [Security Center díjszabását](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Az Azure Defender engedélyezése a Azure Portal

Az összes Security Center funkció engedélyezéséhez, beleértve a veszélyforrások elleni védelmi képességeket is, engedélyeznie kell az Azure Defender szolgáltatást a megfelelő munkaterheléseket tartalmazó előfizetésben. A munkaterület szintjének engedélyezése nem teszi lehetővé az Azure-erőforrások igény szerinti elérését, az adaptív alkalmazások vezérlését és a hálózati észleléseket. Emellett az egyetlen Azure Defender-csomag is elérhető a munkaterület szintjén az Azure Defender for Servers és az Azure Defender a gépeken futó SQL-kiszolgálókhoz.

- Engedélyezheti az **Azure Defender számára a Storage-fiókokat** az előfizetés szintjén vagy az erőforrás szintjén.
- Az **Azure Defender for SQL** engedélyezhető az előfizetés szintjén vagy az erőforrás szintjén is
- A fenyegetések védelmét a **Azure Database for MariaDB/MySQL/PostgreSQL** -hez engedélyezheti csak az erőforrás szintjén

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Az Azure Defender engedélyezése az előfizetések és munkaterületek számára:

- Az Azure Defender engedélyezése egy előfizetésben:

    1. A Security Center főmenüjében válassza a **díjszabás & beállítások** lehetőséget.
    1. Válassza ki a védelemmel ellátni kívánt előfizetést vagy munkaterületet.
    1. A frissítéshez válassza az **Azure Defender** lehetőséget.
    1. Kattintson a **Mentés** gombra.

    > [!TIP]
    > Megfigyelheti, hogy az Azure Defender minden csomagja külön díjszabású, és egyénileg be-és kikapcsolható. Előfordulhat például, hogy ki szeretné kapcsolni a App Service Azure Defendert olyan előfizetésekhez, amelyek nem rendelkeznek társított Azure App Service csomaggal. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="A Security Center díjszabási oldala a portálon":::

- Az Azure Defender engedélyezése több előfizetésre vagy munkaterületre:

    1. Az Security Center oldalsávján válassza az **első lépések** lehetőséget.

        A **frissítés** lap felsorolja a bevezetésre jogosult előfizetéseket és munkaterületeket.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Az első lépések lap upgrade (frissítés) lapja"::: 

    1. A válassza ki az előfizetések **és munkaterületek lehetőséget az Azure Defender listájának engedélyezéséhez** , válassza ki a frissítendő előfizetéseket és munkaterületeket, és válassza a **frissítés** lehetőséget az Azure Defender engedélyezéséhez.

       - Ha olyan előfizetéseket és munkaterületeket választ, amelyek nem jogosultak a próbaverzióra, a következő lépés a frissítés után megkezdődik.
       - Ha olyan munkaterületet választ, amely jogosult az ingyenes próbaverzióra, a következő lépés egy próbaverziót indít el.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Az összes kiválasztott munkaterület és előfizetés frissítése a kezdeti lépések lapról":::


## <a name="next-steps"></a>Következő lépések

Most, hogy engedélyezte az Azure Defender használatát, engedélyezze az automatikus adatgyűjtést az automatikus [kiépítési ügynökök és bővítmények Azure Security Centerból](security-center-enable-data-collection.md)című részében ismertetett szükséges ügynökök és bővítmények számára.