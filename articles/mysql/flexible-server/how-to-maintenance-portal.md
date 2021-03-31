---
title: Azure Database for MySQL – rugalmas kiszolgáló (előzetes verzió) – ütemezett karbantartás – Azure Portal
description: Megtudhatja, hogyan konfigurálhat egy Azure Database for MySQL rugalmas kiszolgáló ütemezett karbantartási beállításait a Azure Portal.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: c8251eb2a89a7481ebc981f2b89668c363651b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91315014"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Az üzemezett karbantartások beállításainak kezelése a rugalmas Azure Database for MySQL-kiszolgálón
 
Az Azure-előfizetésben található minden egyes rugalmas kiszolgálóhoz karbantartási beállításokat adhat meg. A beállítások közé tartozik a karbantartási ütemterv és az értesítési beállítások a közelgő és befejezett karbantartási eseményekhez.

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló előzetes verzióban érhető el.
 
## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL rugalmas kiszolgáló](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Karbantartási ütemterv beállításainak megadása
 
1. A MySQL-kiszolgáló lapon, a **Beállítások** fejléc alatt válassza a **karbantartás** lehetőséget az ütemezett karbantartási beállítások megnyitásához.
2. Az alapértelmezett (rendszer által felügyelt) ütemterv a hét egy véletlenszerű napja, a karbantartási időszak pedig 60, a helyi kiszolgáló ideje pedig 23:00 és 07:00 között van. Ha testre szeretné szabni ezt az ütemtervet, válassza az **Egyéni ütemterv** lehetőséget. Ezután kiválaszthatja a hét előnyben részesített napját, valamint egy 60 perces időszakot a karbantartási kezdési időponthoz.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Értesítés az ütemezett karbantartási eseményekről
 
A Azure Service Health használatával [megtekintheti](../../service-health/service-notifications.md) a rugalmas kiszolgálón a közelgő és ütemezett karbantartással kapcsolatos értesítéseket. A riasztásokat a Azure Service Healthban is [beállíthatja](../../service-health/resource-health-alert-monitor-guide.md) , hogy értesítéseket kapjon a karbantartási eseményekről.
 
## <a name="next-steps"></a>Következő lépések  
 
* Ismerje meg [Azure Database for MySQL – rugalmas kiszolgáló ütemezett karbantartását](concepts-maintenance.md)
* Sovány [Azure Service Health](../../service-health/overview.md)
