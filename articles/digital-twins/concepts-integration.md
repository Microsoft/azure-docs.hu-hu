---
title: Integráció más szolgáltatásokkal
titleSuffix: Azure Digital Twins
description: A bejövő és a kimenő forgalomra vonatkozó követelmények megismerése az Azure digitális Twins üzembe helyezése során.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 118b02ab694d27dbe4e13cbfa1a617a56b052772
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043068"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Az Azure Digital Twins integrálása más szolgáltatásokkal

Az Azure digitális Twins jellemzően más szolgáltatásokkal együtt használható rugalmas, csatlakoztatott megoldások létrehozására, amelyek többféle módon használják az adatait.

Az [**Event Routes**](concepts-route-events.md)használatával az Azure Digital Twins a felsőbb rétegbeli szolgáltatásokból, például [IoT hub](../iot-hub/about-iot-hub.md) vagy [Logic Appsból](../logic-apps/logic-apps-overview.md)is fogadhat adatokból, amelyek telemetria és értesítések kézbesítésére szolgálnak. 

Az Azure Digital Twins az olyan alsóbb rétegbeli szolgáltatásokhoz is irányíthatja az adattovábbítást, mint például a [Azure Maps](../azure-maps/about-azure-maps.md) és a [Time Series Insights](../time-series-insights/overview-what-is-tsi.md), a tárolás, a munkafolyamat-integráció, az elemzés és egyebek terén. 

## <a name="data-ingress"></a>Bejövő adatforgalom

Az Azure Digital Twins bármely szolgáltatás adataival és eseményeivel is elvégezhető –[IoT hub](../iot-hub/about-iot-hub.md), [Logic apps](../logic-apps/logic-apps-overview.md), a saját egyéni szolgáltatása stb.). Így telemetria gyűjthet a környezetében található fizikai eszközökről, és feldolgozhatja ezeket az adatokat a felhőben található Azure digitális Twins gráf használatával.

Ahelyett, hogy beépített IoT Hub a háttérben, az Azure Digital Twins lehetővé teszi, hogy a szolgáltatással való használathoz saját IoT Hub használjon. Használhat egy meglévő IoT Hub, amelyet jelenleg éles környezetben, vagy üzembe helyezhet egy újat erre a célra. Ez teljes hozzáférést biztosít a IoT Hub összes eszköz-felügyeleti képességeihez.

Ha bármilyen forrásból szeretne adatot befogadni az Azure digitális Twinsba, használjon [**Azure-függvényt**](../azure-functions/functions-overview.md). További információ erről a mintázatról [*: telemetria beolvasása a IoT hubból*](how-to-ingest-iot-hub-data.md), vagy próbálja ki magát az Azure Digital Twins [*oktatóanyagában: teljes körű megoldás összekötése*](tutorial-end-to-end.md). 

Azt is megtudhatja, hogyan csatlakoztathatók az Azure digitális Twins egy Logic Apps triggerhez a következő [*útmutatóban: integráció a Logic apps*](how-to-integrate-logic-apps.md)használatával.

## <a name="data-egress-services"></a>Adatforgalom-szolgáltatások

Az Azure Digital Twins képes az adatküldés a csatlakoztatott **végpontokra**. A támogatott végpontok a következőket tehetik:
* [Event Hub](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Szolgáltatásbusz](../service-bus-messaging/service-bus-messaging-overview.md)

A végpontok felügyeleti API-kkal vagy az Azure Portallal csatolhatók az Azure Digital Twinshez. További információ a végpontok Azure digitális Ikrekhez való csatlakoztatásáról [*: végpontok és útvonalak kezelése*](how-to-manage-routes-apis-cli.md).

Az adatokat számos más szolgáltatásnak is átirányíthatja, például az [Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md) vagy a[Time Series Insights](../time-series-insights/overview-what-is-tsi.md) szolgáltatásnak. Ahhoz, hogy az Ön adatait az alábbi szolgáltatásokhoz küldje, csatolja a rendeltetési szolgáltatást egy végponthoz.

Ha például az Azure Maps is használja, és szeretné összekapcsolni a helyet az Azure Digital Twins [Twin gráfmal](concepts-twins-graph.md), a Azure Functions a Event Grid használatával a telepítésben lévő összes szolgáltatás közötti kommunikációt is létrehozhat. További információ a következő [ *útmutatóban: az Azure Digital Twins használata Azure Maps beltéri Térkép frissítéséhez*](how-to-integrate-maps.md)

Azt is megtudhatja, hogyan irányíthatja az adatútvonalat hasonló módon a Time Series Insights, a [*How-to: Integration with Time Series Insights*](how-to-integrate-time-series-insights.md)használatával.

## <a name="next-steps"></a>Következő lépések

További információ a végpontokról és az útválasztási eseményekről a külső szolgáltatásokra:
* [*Fogalmak: az Azure Digital Twins eseményeinek továbbítása*](concepts-route-events.md)

Ismerje meg, hogyan állíthatja be az Azure digitális Twins-t az adatok IoT Hubból való betöltéséhez:
* [*Útmutató: telemetria beolvasása IoT Hubból*](how-to-ingest-iot-hub-data.md)