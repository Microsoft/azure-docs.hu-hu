---
title: Ütemezett karbantartás – Azure Database for PostgreSQL – nagy kapacitású (Citus)
description: Ez a cikk a Azure Database for PostgreSQL-nagy kapacitású (Citus) ütemezett karbantartási funkcióját ismerteti.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106785"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Ütemezett karbantartás Azure Database for PostgreSQL – nagy kapacitású (Citus)

A Azure Database for PostgreSQL-nagy kapacitású (Citus) rendszeres karbantartást biztosít a felügyelt adatbázis biztonságos, stabil és naprakészen tartásához.  A karbantartás során a kiszolgálócsoport összes csomópontja új funkciókat, frissítéseket és javításokat kap.

A nagy kapacitású (Citus) ütemezett karbantartásának legfontosabb funkciói a következők:

* A rendszer a kiszolgálói csoport összes csomópontján egy időben alkalmazza a frissítéseket.
* A közelgő karbantartással kapcsolatos értesítéseket Azure Service Health öt nappal előre közzétesszük
* Általában legalább 30 nap van a kiszolgálói csoport sikeres karbantartási eseményei között
* A hét és az időablak preferált napja a karbantartási kezdéshez minden egyes kiszolgálócsoport esetében meghatározható

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Karbantartási időszak kiválasztása és értesítés a közelgő karbantartásról

A karbantartást a hét egy adott napján és az adott napon belül egy időablakban is ütemezhet. Vagy megadhatja, hogy a rendszer válasszon egy napot és egy időablakot az automatikus indításhoz. Mindkét esetben a rendszer öt nappal a karbantartás futtatása előtt riasztást küld. A rendszer azt is lehetővé teszi, hogy a karbantartás elindításakor, valamint a sikeres befejezés után is tájékoztassa.

Az ütemezett karbantartásra vonatkozó értesítéseket a rendszer a Azure Service Healthre küldi, és a következő lehet:

* Adott címre e-mailben
* E-mailben egy Azure Resource Manager szerepkörbe
* SMS-ben elküldve a mobileszközök számára
* Leküldéses értesítésként Azure-alkalmazásba
* Hangüzenetként

A karbantartási ütemezés beállításainak megadásakor kiválaszthatja a hét valamelyik napját és egy időszakot. Ha nem adja meg, a rendszer a kiszolgáló csoportjának időpontjában 23:00 és 07:00 közötti időt vesz igénybe. Az Azure-előfizetésében különböző időpontokat határozhat meg az egyes nagy kapacitású-(Citus-) kiszolgálócsoport számára.

> [!IMPORTANT]
> Általában legalább 30 nap van a kiszolgálócsoport sikeres ütemezett karbantartási eseményei között.
>
> A kritikus fontosságú vészhelyzeti frissítések, például súlyos biztonsági rések esetén azonban előfordulhat, hogy kevesebb mint 5 nappal korábban kap értesítést a frissítésről. A kritikus fontosságú frissítés akkor is telepíthető a kiszolgálón, ha az elmúlt 30 napban sikeres ütemezett karbantartást hajtottak végre azon.

Bármikor frissítheti az ütemezési beállításokat. Ha a nagy kapacitású-(Citus-) kiszolgálócsoport karbantartása ütemezve van, és frissíti az ütemezést, a meglévő események az eredetileg ütemezett módon fognak folytatódni. A beállítások módosítása a meglévő események sikeres befejezése után lép életbe.

Ha a karbantartás sikertelen vagy megszakad, a rendszer létrehoz egy értesítést.
A rendszer az aktuális ütemezési beállításoknak megfelelően újra megpróbálja elvégezni a karbantartást, és öt nappal a következő karbantartási esemény előtt értesíti Önt.

## <a name="next-steps"></a>Következő lépések

* Útmutató [a karbantartási ütemterv módosításához](howto-hyperscale-maintenance.md)
* Ismerje meg, hogyan [kaphat értesítéseket a közelgő karbantartásról](../service-health/service-notifications.md) a Azure Service Health használatával
* Megtudhatja, hogyan [állíthat be riasztásokat a közelgő ütemezett karbantartási eseményekről](../service-health/resource-health-alert-monitor-guide.md)
