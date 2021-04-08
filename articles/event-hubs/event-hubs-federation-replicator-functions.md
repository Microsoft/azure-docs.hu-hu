---
title: Esemény-replikációs feladatok és alkalmazások – Azure Event Hubs | Microsoft Docs
description: Ez a cikk áttekintést nyújt az esemény-replikációs feladatok és alkalmazások létrehozásáról Azure Functions
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97663643"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Esemény-replikációs feladatok és alkalmazások Azure Functions

> [!TIP]
> Az összes állapot-nyilvántartó replikációs feladathoz, ahol figyelembe kell vennie az események hasznos adatait, és át kell alakítania, összesíteni, bővíteni vagy csökkentenie kell, [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) helyett használja Azure functions.

Ahogy az az [esemény replikálása és a régiók közötti összevonás](event-hubs-federation-overview.md) című cikkben leírtak szerint, az esemény-adatfolyamok állapot nélküli replikálása Event Hubs és a Event Hubs és az egyéb esemény-adatfolyam-források és a célok között a Azure Functionsre támaszkodik.

A [Azure functions](../azure-functions/functions-overview.md) egy méretezhető és megbízható végrehajtási környezet a kiszolgáló nélküli alkalmazások konfigurálásához és futtatásához, beleértve az esemény-replikációs és az összevonási feladatokat.

Ebben az áttekintésben megismerheti az ilyen alkalmazások Azure Functions beépített képességeit, az átalakítási feladatokhoz alkalmazkodó és módosítható kódrészleteket, valamint arról, hogyan konfigurálhat egy Azure Functions alkalmazást úgy, hogy az ideális módon integrálható legyen Event Hubs és más Azure Messaging-szolgáltatásokkal. Ez a cikk számos információt tartalmaz a Azure Functions dokumentációra.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









