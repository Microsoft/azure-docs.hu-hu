---
title: Media Services események figyelése Event Grid portálon
description: Ez a cikk bemutatja, hogyan fizethet elő Event Gridre az Azure Media Services események figyelése érdekében.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 1e288e4490916a7ee03371baa8e7cc3b798e272b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961710"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Media Services-események létrehozása és monitorozása az Event Griddel az Azure Portalon

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ez a szolgáltatás [esemény-előfizetések](../../../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Media Services események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Media Services eseményt, mert a eventType tulajdonság a "Microsoft. Media" karakterlánccal kezdődik. További információ: [Media Services esemény sémái](media-services-event-schemas.md).

Ebben a cikkben a Azure Portal az Azure Media Services-fiók eseményeire való előfizetéshez. Ezután aktiválhatja az eseményeket az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikkben egy olyan webalkalmazásnak küldünk eseményeket, amely összegyűjti és megjeleníti az üzeneteket.

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat a webalkalmazásnak.

## <a name="prerequisites"></a>Előfeltételek 

* Aktív Azure-előfizetés
* Hozzon létre egy új Azure Media Services-fiókot [az ebben a gyors útmutatóban](../account-create-how-to.md) leírt módon.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Az Media Services-fiók eseményeire való feliratkozás előtt hozzon létre egy végpontot az esemény üzenethez. A végpont általában az eseményadatok alapján hajt végre műveleteket. Ebben a cikkben egy [előre elkészített webes alkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyez üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   [![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

Ha átvált a "Azure Event Grid Viewer" webhelyre, akkor azt láthatja, hogy még nincsenek események.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Előfizetés Media Services eseményekre

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. A portálon válassza ki a Media Services fiókját, és válassza az **események** lehetőséget.
1. Ha eseményeket szeretne küldeni a megjelenítő alkalmazásba, használjon egy webhookot a végponthoz. 

   ![Webhook kiválasztása](../media/monitor-events-portal/select-web-hook.png)

1. Az esemény-előfizetés a Media Services-fiókhoz tartozó értékekkel van feltöltve. 
1. Válassza a "web Hook" lehetőséget a **végpont típusához**.
1. Ebben a témakörben az összes bejelölt eseménytípus esetében hagyjuk az **előfizetést** . Azonban kitörölheti és szűrheti az adott események típusait. 
1. Kattintson a **végpont kiválasztása** hivatkozásra.

    A webhook végponthoz adja meg a webalkalmazás URL-címét, és adja hozzá az `api/updates` elemet a kezdőlap URL-címéhez. 

1. Nyomja meg a **kijelölés megerősítése elemet**.
1. Nyomja meg a **Create** (Létrehozás) gombot.
1. Nevezze el az előfizetését.

   ![Naplók kiválasztása](../media/monitor-events-portal/create-subscription.png)

1. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. 

    Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A végpontnak a következőre kell beállítania: `validationResponse` `validationCode` . További információ: [Event Grid biztonság és hitelesítés](../../../event-grid/security-authentication.md). Megtekintheti a webalkalmazás kódját, hogy megtudja, hogyan érvényesíti az előfizetést.

Most aktiváljuk az eseményeket, hogy meglássuk, hogyan osztja el a Event Grid az üzenetet a végpontnak.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Media Services-fiókhoz tartozó eseményeket a kódolási feladatok futtatásával aktiválhatja. [Ezt](../stream-files-dotnet-quickstart.md) a rövid útmutatót követve kódolhat egy fájlt, és megkezdheti az események küldését. Ha feliratkozott az összes eseményre, a következőhöz hasonló képernyő jelenik meg:

> [!TIP]
> Az eseményadatok kibontásához kattintson a szem ikonra. Ne frissítse az oldalt, ha meg szeretné tekinteni az összes eseményt.

![Előfizetési esemény megtekintése](../media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Következő lépések

[Feltöltés, kódolás és streamelés](../stream-files-tutorial-with-api.md)
