---
title: Azure App Service erőforrások áthelyezése
description: A Azure Resource Manager használatával áthelyezheti App Service erőforrásait egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531372"
---
# <a name="move-guidance-for-app-service-resources"></a>Útmutató a App Service-erőforrások áthelyezéséhez

Ez a cikk a App Service erőforrások áthelyezésének lépéseit ismerteti. A App Service-erőforrások új előfizetésre való áthelyezésének speciális követelményei vannak.

## <a name="move-across-subscriptions"></a>Áthelyezés az előfizetések között

Ha egy webalkalmazást az előfizetések között helyez át, a következő útmutatók érvényesek:

- A cél erőforráscsoport nem rendelkezhet meglévő App Service erőforrásokkal. App Service erőforrások a következők:
    - Web Apps
    - App Service-csomagok
    - Feltöltött vagy importált TLS/SSL-tanúsítványok
    - App Service-környezetek
- Az erőforráscsoporthoz tartozó összes App Service-erőforrást együtt kell áthelyezni.
- App Service környezetek nem helyezhetők át új erőforráscsoporthoz vagy előfizetésbe. Egy webalkalmazást és egy app Service-csomagot azonban áthelyezhet egy új előfizetésre anélkül, hogy áthelyezi a App Service Environment. Az áthelyezést követően a webalkalmazás már nem fut a App Service Environmentban.
- A tanúsítványokat a TLS-kötések törlése nélkül helyezheti át a web-re, ha a tanúsítványt az erőforráscsoport összes többi erőforrásával áthelyezi.
- App Service erőforrásokat csak abban az erőforráscsoporthoz lehet áthelyezni, amelyben eredetileg létrehozták őket. Ha egy App Service erőforrás már nem szerepel az eredeti erőforráscsoporthoz, helyezze vissza az eredeti erőforráscsoporthoz. Ezután helyezze át az erőforrást az előfizetések között.

Ha nem emlékszik az eredeti erőforráscsoporthoz, megkeresheti azt a diagnosztika használatával. A webalkalmazáshoz válassza a **diagnosztizálás és problémák megoldása** lehetőséget. Ezután válassza a **konfiguráció és kezelés** lehetőséget.

![Diagnosztika kiválasztása](./media/app-service-move-limitations/select-diagnostics.png)

Válassza az **áttelepítési beállítások** lehetőséget.

![Áttelepítési beállítások kiválasztása](./media/app-service-move-limitations/select-migration.png)

A webalkalmazás áthelyezéséhez válassza a javasolt lépések lehetőséget.

![Javasolt lépések kiválasztása](./media/app-service-move-limitations/recommended-steps.png)

Az erőforrások áthelyezése előtt el kell végeznie az ajánlott műveleteket. Az információ tartalmazza a webalkalmazás eredeti erőforrás-csoportját.

![A képernyőfelvételen a Microsoft dot web-erőforrások áthelyezésének javasolt lépései láthatók.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Támogatás áthelyezése

A következő témakörben megtudhatja, hogy mely App Service erőforrásokat lehet áthelyezni:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Következő lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../move-resource-group-and-subscription.md).
