---
title: Egyéni webes API-k hívása & REST API-k
description: Saját webes API-k meghívása & REST API-khoz Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83659773"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Egyéni API-k üzembe helyezése és meghívása munkafolyamatokból az Azure Logic Appsben

Miután [létrehozta saját API](./logic-apps-create-api-app.md) -jait a logikai alkalmazás munkafolyamataiban, telepítenie kell ezeket az API-kat, mielőtt meghívja őket. Az API-kat [webalkalmazásként](../app-service/overview.md)is üzembe helyezheti, de érdemes lehet [API-alkalmazásokként](../app-service/app-service-web-tutorial-rest-api.md)üzembe helyeznie az API-kat, amelyek megkönnyítik a feladatok elvégzését a felhőben és a helyszínen lévő API-k létrehozásakor, üzemeltetése és felhasználása során. Nem kell módosítania az API-kat, csak telepítse a kódot egy API-alkalmazásba. Az API-kat üzemeltetheti [Azure app Serviceon](../app-service/overview.md), egy szolgáltatásként nyújtott platformon (Pásti), amely kiválóan méretezhető, egyszerű API-üzemeltetést biztosít.

Habár bármely API-t meghívhat egy logikai alkalmazásból, a legjobb élmény érdekében adja hozzá az API-k műveleteit és paramétereit leíró [hencegő metaadatokat](https://swagger.io/specification/) . Ez a hencegő dokumentum megkönnyíti az API-k integrálását, és jobban együttműködik a Logic apps szolgáltatással.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Az API üzembe helyezése webalkalmazásként vagy API-alkalmazásként

Ahhoz, hogy az egyéni API-t egy logikai alkalmazásból lehessen hívni, az API-t webalkalmazásként vagy API-alkalmazásként üzembe helyezheti Azure App Service. Ha szeretné, hogy a hencegő dokumentum a Logic Apps Designer által olvasható legyen, állítsa be az API-definíciók tulajdonságait, és kapcsolja be az [CORS-](../app-service/overview.md) t a webalkalmazás vagy az API-alkalmazás számára.

1. A [Azure Portal](https://portal.azure.com)válassza ki a webalkalmazást vagy az API-alkalmazást.

2. A megnyíló alkalmazás menüben az **API** alatt válassza az **API-definíció** elemet. Állítsa be az **API-definíció helyét** a swagger.jsfájl URL-címére.

   Az URL-cím általában a következő formátumban jelenik meg: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Az egyéni API-hoz kapcsolódó hencegő dokumentumra mutató hivatkozás](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Az **API** alatt válassza a **CORS** lehetőséget. Állítsa be a CORS házirendet az **engedélyezett eredetek** számára a **"*"** értékre (az összes engedélyezése).

   Ez a beállítás engedélyezi a Logic app designertől érkező kéréseket.

   ![A Logic app designertől érkező kérések engedélyezése az egyéni API-nak](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

További információkért lásd: [REST API üzemeltetése a CORS-ben Azure app Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Egyéni API meghívása a Logic app-munkafolyamatokból

Miután beállította az API-definíció tulajdonságait és a CORS, az egyéni API-eseményindítók és műveletek elérhetők lesznek a logikai alkalmazás munkafolyamatában való felvételhez. 

*  Ha a OpenAPI URL-címmel rendelkező webhelyeket szeretné megtekinteni, böngészhet az előfizetési webhelyeken a Logic Apps Designerben.

*  Az elérhető műveletek és bemenetek megtekintéséhez egy hencegő dokumentumra mutatva használja a [http + hencegés műveletet](../connectors/connectors-native-http-swagger.md).

*  Bármely API meghívásához, beleértve azokat az API-kat, amelyek nem rendelkeznek vagy nem tesznek elérhetővé egy hencegő dokumentumot, bármikor létrehozhat egy [http-művelettel](../connectors/connectors-native-http.md)rendelkező kérelmet.

## <a name="next-steps"></a>Következő lépések

* [Egyéni összekötők áttekintése](../logic-apps/custom-connector-overview.md)
