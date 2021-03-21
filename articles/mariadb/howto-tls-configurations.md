---
title: TLS-konfiguráció – Azure Portal-Azure Database for MariaDB
description: Ismerje meg, hogyan állíthatja be a TLS-konfigurációt a Azure Database for MariaDB Azure Portal használatával
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 55a664ceb3524a0c545e037dcba7a2af90034ad4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664766"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>A TLS-beállítások konfigurálása a Azure Database for MariaDB a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MariaDB kiszolgálót úgy, hogy kikényszerítse a TLS minimális verzióját a kapcsolatokhoz való csatlakozáshoz, és megtagadja az összes olyan kapcsolatot, amely alacsonyabb TLS-verzióval rendelkezik, mint a konfigurált minimális TLS-verzió,

A TLS-verziót kényszerítheti a Azure Database for MariaDBhoz való csatlakozásra, ha beállítja az adatbázis-kiszolgáló minimális TLS-verzióját. Például, ha a TLS 1,0-es minimális verziószámot állítja be a TLS-re, akkor a kiszolgáló engedélyezi a TLS 1,0, 1,1 és 1.2 + protokollt használó ügyfelek kapcsolódását. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1.2 + protokollt használó ügyfelek kapcsolatait engedélyezzük, és a TLS 1,0 és a TLS 1,1 összes kapcsolata el lesz utasítva.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>TLS-konfigurációk beállítása Azure Database for MariaDBhoz

Az alábbi lépéseket követve állíthatja be a MariaDB-kiszolgáló minimális TLS-verzióját:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MariaDB-kiszolgálót.

1. A MariaDB-kiszolgáló lap **Beállítások** területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A **TLS minimális verziója** beállításnál válassza a **1,2** lehetőséget, hogy megtagadja a TLS 1,2-nál kisebb TLS-verzióval létesített kapcsolatokat a MariaDB-kiszolgálóhoz.

    ![TLS-konfiguráció Azure Database for MariaDB](./media/howto-tls-configurations/tls-configurations.png)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt.

    ![Azure Database for MariaDB TLS-konfiguráció sikeres](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-metric.md)