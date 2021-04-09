---
title: Nyilvános hálózati hozzáférés megtagadása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan konfigurálhatja a nyilvános hálózati hozzáférés megtagadását a Azure Database for PostgreSQL egyetlen kiszolgálójának Azure Portal használatával
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: e195c005676df27385e5e00736b04bdb689fafc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727107"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Nyilvános hálózati hozzáférés megtagadása Azure Database for PostgreSQL egy kiszolgálón Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for PostgreSQL egyetlen kiszolgálót az összes nyilvános konfiguráció megtagadásához, és a hálózati biztonság növelése érdekében csak privát végpontokon keresztüli kapcsolatokat engedélyezhet.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* [Azure Database for PostgreSQL önálló kiszolgáló](quickstart-create-server-database-portal.md) általános célú vagy memória-optimalizálva árképzési réteggel.

## <a name="set-deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadásának beállítása

Az alábbi lépéseket követve beállíthatja a PostgreSQL egyetlen kiszolgálójának a nyilvános hálózati hozzáférés megtagadását:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for PostgreSQL önálló kiszolgálót.

1. A PostgreSQL egykiszolgálós oldal **Beállítások** területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A **nyilvános hálózati hozzáférés megtagadása** területen válassza az **Igen** lehetőséget a PostgreSQL-kiszolgáló hozzáférésének megtagadásának engedélyezéséhez.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL egyetlen kiszolgáló megtagadja a hálózati hozzáférést":::

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Azure Database for PostgreSQL egyetlen kiszolgáló megtagadja a hálózati hozzáférés sikerességét":::

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).
