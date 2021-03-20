---
title: Üzenet-replikációs feladatok és alkalmazások – Azure Service Bus | Microsoft Docs
description: Ez a cikk áttekintést nyújt az üzenetek replikálásával kapcsolatos feladatok és alkalmazások létrehozásáról Azure Functions
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657499"
---
# <a name="message-replication-tasks-and-applications"></a>Üzenetreplikációs feladatok és alkalmazások

Ahogy az az [üzenet replikálása és a régiók közötti összevonás](service-bus-federation-overview.md) című cikkben leírtak szerint, a Service Bus entitások és a Service Bus és az egyéb üzenetek között, illetve a célok között általában a Azure Functionsre támaszkodó üzenetkezelési műveletek replikálása.

A [Azure functions](../azure-functions/functions-overview.md) méretezhető és megbízható végrehajtási környezet kiszolgáló nélküli alkalmazások konfigurálásához és futtatásához, beleértve az [üzenetek replikálását és az összevonási](service-bus-federation-overview.md) feladatokat.

Ebben az áttekintésben megismerheti az ilyen alkalmazások Azure Functions beépített képességeit, az átalakítási feladatokhoz alkalmazkodó és módosítható kódrészleteket, valamint arról, hogyan konfigurálhat egy Azure Functions alkalmazást úgy, hogy az ideális módon integrálható legyen Service Bus és más Azure Messaging-szolgáltatásokkal. Ez a cikk számos információt tartalmaz a Azure Functions dokumentációra.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
