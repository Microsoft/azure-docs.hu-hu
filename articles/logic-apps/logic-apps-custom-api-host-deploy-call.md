---
title: Üzembe helyezése és hívása a webes API-k és REST API-k az Azure Logic Apps |} A Microsoft Docs
description: Üzembe helyezése és webes API-k és REST API-k hívása rendszerének hibaelhárítását munkafolyamatokat az Azure Logic Appsben
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, stepsic, LADocs
ms.topic: article
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.date: 05/26/2017
ms.openlocfilehash: a9049ba1fbd7d3bdce061d277f6a7a02d9b1e4b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740387"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Üzembe helyezése és munkafolyamataiból Azure Logic Apps egyéni API-k hívása

Miután [egyéni API-k létrehozása](./logic-apps-create-api-app.md) használni a logikai alkalmazások munkafolyamataiba, telepítenie kell az API-k őket meghívása előtt. Telepítheti az API-k [webes alkalmazások](../app-service/overview.md), érdemes megfontolni az API-t, de [API-alkalmazások](../app-service/app-service-web-tutorial-rest-api.md), könnyebben ami a a feladatot, amikor készíthet, üzemeltethet és felhasználása a felhőben és helyszíni API-k. Nem kell minden olyan kódot az API-k – csupán telepítse kódját egy API-alkalmazásba. Az API-k is üzemeltethet [Azure App Service](../app-service/overview.md), a platform--szolgáltatásként (PaaS) kínál, amely a nagy mértékben skálázható, egyszerű API-t üzemeltető biztosít.

Bár minden olyan API-t a legjobb élmény meghívhat egy logikai alkalmazást, a, adjon hozzá [OpenAPI (korábban Swagger) metaadat](https://swagger.io/specification/) , amely az API műveleteit és paramétereit írja le. Az OpenAPI-fájl segítségével könnyebben integrálhatja, és a logic apps segítségével jobban használható az API.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Az API-webalkalmazás vagy API-alkalmazás üzembe helyezése

Az egyéni API-k meghívása logikai alkalmazásból származó, előtt üzembe helyezése az Azure App Service-webalkalmazás vagy API-alkalmazás az API-t. Emellett ahhoz, hogy az OpenAPI-fájlt a Logic Apps Designerben által is olvasható, a API-definíció tulajdonságainak beállítása, és kapcsolja be a [eltérő eredetű erőforrások megosztása (CORS)](../app-service/overview.md) a webalkalmazás vagy API-alkalmazás.

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a webalkalmazás vagy API-alkalmazás.

2. Az alkalmazás menüjében, amely megnyitja a **API**, válassza a **API-definíció**. Állítsa be a **API-definíció helye** swagger.json az OpenAPI-fájl URL-címéhez.

   Az URL-cím általában a következő formátumban jelenik meg: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Egyéni API-ja OpenAPI-fájl csatolása](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. A **API**, válassza a **CORS**. Állítsa be a CORS-szabályzata **engedélyezett eredetek** való **"*"** (lehetővé teszi az összes).

   Ez a beállítás lehetővé teszi a kérelmek a Logikaialkalmazás-Tervező.

   ![Lehetővé teszik a kérelmek a Logic App Designerben az egyéni API-hoz](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

További információkért lásd: [az Azure App Service cors-támogatással rendelkező RESTful API üzemeltetése](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Az egyéni API hívása logic app-munkafolyamatok

Az API-definíció tulajdonságait és a CORS beállítása után az egyéni API-k triggereket és műveleteket, hogy a logikai alkalmazás munkafolyamatának felvétel elérhetőnek kell lennie. 

*  OpenAPI URL-címek webhelyeket megtekintéséhez tallózással keresse meg az előfizetés webhelyek, a Logic Apps Designerben.

*  OpenAPI-dokumentumok, címen elérhető műveletek és a bemenetek megtekintéséhez használja a [HTTP + Swagger műveleti](../connectors/connectors-native-http-swagger.md).

*  Bármely API-t, beleértve az API-k, amelyek nem rendelkeznek, vagy tegye elérhetővé az OpenAPI-dokumentumok, meghívásához bármikor létrehozhat egy kérelmet a [HTTP-művelet](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>További lépések

* [Egyéni összekötők áttekintése](../logic-apps/custom-connector-overview.md)