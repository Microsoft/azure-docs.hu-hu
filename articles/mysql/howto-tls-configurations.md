---
title: TLS-konfiguráció – Azure Portal-Azure Database for MySQL
description: Ismerje meg, hogyan állíthatja be a TLS-konfigurációt a Azure Database for MySQL Azure Portal használatával
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 5ecf2992fa9ea56f73748a9f1f98c75f9076c68f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656889"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>A TLS-beállítások konfigurálása a Azure Database for MySQL a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MySQL kiszolgálót úgy, hogy kikényszerítse a csatlakozások számára engedélyezett minimális TLS-verziót, és megtagadja az összes olyan kapcsolatot, amely alacsonyabb TLS-verzióval rendelkezik, mint a konfigurált minimális TLS-verzió, így növelve

A TLS-verziót kényszerítheti a Azure Database for MySQLhoz való csatlakozásra. Az ügyfelek most már dönthetnek úgy, hogy az adatbázis-kiszolgáló minimális TLS-verzióját szeretnék beállítani. Ha például ezt a minimális TLS-verziót 1,0-re állítja be, akkor engedélyezni kell a TLS 1.0, 1.1 és 1,2 használatával csatlakozó ügyfeleket. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1.2 + és a TLS 1,0 és a TLS 1,1 használatával létesített összes bejövő kapcsolat el lesz utasítva.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>TLS-konfigurációk beállítása Azure Database for MySQLhoz

A MySQL-kiszolgáló minimális TLS-verziójának beállításához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL-kiszolgálót.

1. A MySQL-kiszolgáló lap **Beállítások** területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A **TLS minimális verziója** beállításnál válassza a **1,2** lehetőséget, hogy megtagadja a TLS 1,2-nál kisebb TLS-verzióval létesített kapcsolatokat a MySQL-kiszolgálóhoz.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="TLS-konfiguráció Azure Database for MySQL":::

1. Kattintson a **Mentés** gombra a módosítások mentéséhez. 

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt, és azonnal érvényben van. A kiszolgáló **újraindítása nem** szükséges vagy nem lett elvégezve. A módosítások mentése után a kiszolgálóval létesített összes új kapcsolat csak akkor fogadható el, ha a TLS verziója nagyobb vagy egyenlő, mint a portálon beállított minimális TLS-verzió.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for MySQL TLS-konfiguráció sikeres":::

## <a name="next-steps"></a>Következő lépések

- Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md)
