---
title: Az API Analytics használata az Azure API Managementban | Microsoft Docs
description: Az Azure API Managementban az elemzések segítségével megismerheti és kategorizálhatja az API-k és az API-k használatát.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96841437"
---
# <a name="get-api-analytics-in-azure-api-management"></a>API Analytics beszerzése az Azure API Management

Az Azure API Management beépített elemzéseket biztosít az API-khoz. Elemezze az API-k használatát és teljesítményét a API Management-példányban több dimenzió között, beleértve a következőket:

* Idő
* Földrajzi hely
* API-k
* API-műveletek
* Termékek
* Előfizetések
* Felhasználók
* Kérelmek

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Idővonal-elemzés a portálon":::

Az API-k magas szintű figyeléséhez és hibaelhárításához használja az elemzést. További figyelési funkciók, például a közel valós idejű metrikák és erőforrás-naplók a diagnosztika és a naplózás számára [: oktatóanyag: közzétett API-k figyelése](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Elemzés – portál

A Azure Portal segítségével áttekintheti az elemzési adatait egy pillantással a API Management-példányra.

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz. 
1. A bal oldali menüben kattintson a **figyelés** elemre, majd válassza az **elemzés** lehetőséget.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Válassza ki az elemzést API Management példányhoz a portálon":::  
1. Válasszon időtartományt az adatértékekhez, vagy adjon meg egy egyéni időtartományt.
1. Válasszon ki egy jelentési kategóriát az analitikai adatértékekhez, például az **idővonalhoz**, a **földrajzhoz** stb.
1. Igény szerint szűrheti a jelentést egy vagy több további kategória alapján.

## <a name="analytics---rest-api"></a>Elemzés – REST API

A API Management példány elemzési adatainak lekéréséhez és szűréséhez használja a API Management REST API [jelentési](/rest/api/apimanagement/2019-12-01/reports) műveleteit.

Az elérhető műveletek a jelentés rekordjait az API, a földrajz, az API-műveletek, a termék, a kérelem, az előfizetés, az idő vagy a felhasználó szerint adják vissza.

## <a name="next-steps"></a>Következő lépések

* A API Management Azure Monitor funkcióinak bevezetését lásd [: oktatóanyag: közzétett API-k figyelése](api-management-howto-use-azure-monitor.md)
* Részletes HTTP-naplózás és-figyelés: [az API-k monitorozása az Azure API Management, a Event Hubs és a Moesif segítségével](api-management-log-to-eventhub-sample.md).
* Ismerje meg, hogyan integrálhatja [Az Azure-API Management az Azure-Application Insightsokkal](api-management-howto-app-insights.md).