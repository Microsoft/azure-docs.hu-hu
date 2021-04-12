---
title: Azure Database for PostgreSQL-nagy kapacitású (Citus) – ütemezett karbantartás – Azure Portal
description: Megtudhatja, hogyan konfigurálhat egy Azure Database for PostgreSQL-nagy kapacitású (Citus) ütemezett karbantartási beállításait a Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108373"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) ütemezett karbantartási beállításainak kezelése

Az Azure-előfizetésben megadhatja az egyes nagy kapacitású-(Citus-) kiszolgálócsoport karbantartási lehetőségeit. A beállítások közé tartozik a karbantartási ütemterv és az értesítési beállítások a közelgő és befejezett karbantartási eseményekhez.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Egy [Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálócsoport](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Karbantartási ütemterv beállításainak megadása

1. A nagy kapacitású (Citus) kiszolgálócsoport lapjának **Beállítások** fejlécében válassza a **karbantartás** lehetőséget az ütemezett karbantartási beállítások megnyitásához.
2. Az alapértelmezett (rendszer által felügyelt) ütemterv a hét véletlenszerűen kiválasztott napja, a karbantartáshoz pedig 30 [perces időszak áll](https://go.microsoft.com/fwlink/?linkid=2143646)a rendelkezésére. Ha testre szeretné szabni ezt az ütemtervet, válassza az **Egyéni ütemterv** lehetőséget. Ezután kiválaszthatja a hét előnyben részesített napját, valamint egy 30 perces időszakot a karbantartási kezdési időponthoz.

## <a name="notifications-about-scheduled-maintenance-events"></a>Értesítés az ütemezett karbantartási eseményekről

A Azure Service Health használatával [megtekintheti](../service-health/service-notifications.md) a nagy kapacitású-(Citus-) kiszolgálócsoport közelgő és múltbeli ütemezett karbantartásával kapcsolatos értesítéseket. A riasztásokat a Azure Service Healthban is [beállíthatja](../service-health/resource-health-alert-monitor-guide.md) , hogy értesítéseket kapjon a karbantartási eseményekről.

## <a name="next-steps"></a>Következő lépések

* További információ [az ütemezett karbantartásról Azure Database for PostgreSQL – nagy kapacitású (Citus)](concepts-hyperscale-maintenance.md)
* Sovány [Azure Service Health](../service-health/overview.md)
