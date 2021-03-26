---
title: Kiszolgáló paramétereinek konfigurálása-Azure Portal-Azure Database for PostgreSQL-rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a postgres paramétereket Azure Database for PostgreSQL rugalmas kiszolgálón a Azure Portal használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 29d85cc33dc55f3819bba6898a265c46b7e7ef85
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607439"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>A kiszolgálói paraméterek konfigurálása Azure Database for PostgreSQL rugalmas kiszolgálón a Azure Portal használatával 

A Azure Portal használatával a Azure Database for PostgreSQL rugalmas kiszolgálók konfigurációs paramétereit listázhatja, megjelenítheti és frissítheti.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for PostgreSQL rugalmas kiszolgáló](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Paraméterek megtekintése és szerkesztése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. Válassza ki a rugalmas kiszolgálót.

3. A **Beállítások** szakaszban válassza ki a **kiszolgáló paramétereit**. Az oldalon a paraméterek, a saját értékek és a leírások listája látható.
![Paraméterek áttekintő lapja](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Kattintson a **legördülő** menüre, hogy megtekintse a felsorolt típusú paraméterek lehetséges értékeit, például a client_min_messages.
![Számbavétel legördülő lista](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Válassza ki vagy vigye a kurzort az **i** (információ) gombra, hogy megtekintse a numerikus paraméterek, például a cpu_index_tuple_cost lehetséges értékeinek tartományát.
![információ gomb](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Ha szükséges, a **keresőmező** használatával Szűkítse le az adott paramétert. A keresés a paraméterek neve és leírása alapján történik.
![Keresési eredmények](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Módosítsa a módosítani kívánt paraméterek értékét. A munkamenetekben elvégzett összes módosítás lila színnel van kiemelve. Ha módosította az értékeket, válassza a **Mentés** lehetőséget. Vagy **elvetheti** a módosításokat.
![Módosítások mentése vagy elvetése](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett** értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
![Az összes visszaállítása az alapértelmezett értékre](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a következőkkel:

- [A Azure Database for PostgreSQL kiszolgálói paramétereinek áttekintése](concepts-servers.md)
- [Paraméterek konfigurálása az Azure CLI használatával](howto-configure-server-parameters-using-cli.md)
