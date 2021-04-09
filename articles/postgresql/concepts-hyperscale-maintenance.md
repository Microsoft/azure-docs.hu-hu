---
title: Ütemezett karbantartás – Azure Database for PostgreSQL – nagy kapacitású (Citus)
description: Ez a cikk a Azure Database for PostgreSQL-nagy kapacitású (Citus) ütemezett karbantartási funkcióját ismerteti.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027604"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Ütemezett karbantartás Azure Database for PostgreSQL – nagy kapacitású (Citus)

A Azure Database for PostgreSQL-nagy kapacitású (Citus) rendszeres karbantartást biztosít a felügyelt adatbázis biztonságos, stabil és naprakészen tartásához.  A karbantartás során a kiszolgálócsoport összes csomópontja új funkciókat, frissítéseket és javításokat kap.

A nagy kapacitású (Citus) ütemezett karbantartásának legfontosabb funkciói a következők:

* A rendszer a kiszolgálói csoport összes csomópontján egy időben alkalmazza a frissítéseket.
* A közelgő karbantartással kapcsolatos értesítéseket Azure Service Health öt nappal előre közzétesszük
* Általában legalább 30 nap van a kiszolgálói csoport sikeres karbantartási eseményei között

## <a name="notification-about-upcoming-maintenance"></a>Értesítés a közelgő karbantartásról

Az ütemezett karbantartásra vonatkozó értesítéseket a rendszer a Azure Service Healthre küldi, és a következő lehet:

* Adott címre e-mailben
* E-mailben egy Azure Resource Manager szerepkörbe
* SMS-ben elküldve a mobileszközök számára
* Leküldéses értesítésként Azure-alkalmazásba
* Hangüzenetként

> [!IMPORTANT]
> Általában legalább 30 nap van a kiszolgálócsoport sikeres ütemezett karbantartási eseményei között.
>
> A kritikus fontosságú vészhelyzeti frissítések, például súlyos biztonsági rések esetén azonban előfordulhat, hogy kevesebb mint 5 nappal korábban kap értesítést a frissítésről. A kritikus fontosságú frissítés akkor is telepíthető a kiszolgálón, ha az elmúlt 30 napban sikeres ütemezett karbantartást hajtottak végre azon.

Ha a karbantartás sikertelen vagy megszakad, a rendszer létrehoz egy értesítést.
A rendszer az aktuális ütemezési beállításoknak megfelelően újra megpróbálja elvégezni a karbantartást, és öt nappal a következő karbantartási esemény előtt értesíti Önt.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [kaphat értesítéseket a közelgő karbantartásról](../service-health/service-notifications.md) a Azure Service Health használatával
* Megtudhatja, hogyan [állíthat be riasztásokat a közelgő ütemezett karbantartási eseményekről](../service-health/resource-health-alert-monitor-guide.md)
