---
title: Oktatóanyag – az első API-k importálása és közzététele az Azure-ban API Management
description: Ebben az oktatóanyagban egy OpenAPI-specifikációs API-t importál az Azure API Managementba, majd tesztelje az API-t a Azure Portalban.
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91626959"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Oktatóanyag: az első API importálása és közzététele

Ez az oktatóanyag bemutatja, hogyan importálhat egy OpenAPI-specifikációs háttérbeli API-t JSON formátumban az Azure API Managementba. A Microsoft biztosítja az ebben a példában használt háttér-API-t, és az Azure-on üzemelteti azt [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json) .

Miután a háttér-API-t API Managementba importálta, a API Management API a háttérrendszer-API homlokzata lesz. API Management igényeinek megfelelően testre szabhatja a homlokzatot a háttér-API érintése nélkül. További információ: [Az API átalakítása és védelme](transform-api.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * API importálása API Managementba
> * Az API tesztelése az Azure Portalon

Importálás után az API-t a Azure Portal kezelheti.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Új API a API Management":::

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure API Management terminológiájának](api-management-terminology.md)ismertetése.
- [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Háttérrendszeri API importálása és közzététele

Ez a szakasz bemutatja, hogyan importálhat és tehet közzé egy OpenAPI-specifikációs háttér-API-t.

1. Az API Management-példány bal oldali navigációs sávján válassza az **API**-k lehetőséget.
1. Válassza a **OpenAPI** csempét.
1. A **Létrehozás a OpenAPI specifikációja** ablakban válassza a **teljes** lehetőséget.
1. Adja meg az értékeket az alábbi táblázatból. Ezután válassza a **Létrehozás** lehetőséget az API létrehozásához.

   Az API-értékeket a létrehozás során vagy később is beállíthatja a **Beállítások** lapon.

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="API létrehozása":::


   |Beállítás|Érték|Leírás|
   |-------|-----|-----------|
   |**OpenAPI-specifikáció**|*https: \/ /conferenceapi.azurewebsites.net? Format = JSON*|Az API-t végrehajtó szolgáltatás. API Management továbbítja a kéréseket erre a címnek.|
   |**Megjelenített név**|Az előző szolgáltatás URL-címének megadása után API Management kitölti ezt a mezőt a JSON alapján.|A [fejlesztői portálon](api-management-howto-developer-portal.md)megjelenő név.|
   |**Név**|Az előző szolgáltatás URL-címének megadása után API Management kitölti ezt a mezőt a JSON alapján.|Az API egyedi neve.|
   |**Leírás**|Az előző szolgáltatás URL-címének megadása után API Management kitölti ezt a mezőt a JSON alapján.|Az API opcionális leírása.|
   |**URL-séma**|**HTTPS**|Mely protokollok férhetnek hozzá az API-hoz.|
   |**API URL-címének utótagja**|*conference*|Az API Management szolgáltatás alapszintű URL-címéhez hozzáfűzött utótag. API Management megkülönbözteti az API-kat az utótag alapján, így az utótagnak egyedinek kell lennie az adott közzétevő minden API-jának.|
   |**Címkék**| |Címkék az API-k kereséshez, csoportosításhoz vagy szűréshez való rendszerezéséhez.|
   |**Termékek**|**Korlátlan**|Egy vagy több API társítása. Minden API Management példány két minta termékkel rendelkezik: **kezdő** és **korlátlan**. Egy API-t úgy tehet közzé, hogy társítja az API-t egy termékkel, amely ebben a példában **korlátlan** .<br/><br/> Több API-t is felvehet egy termékbe, és a fejlesztői portálon keresztül biztosíthatja őket a fejlesztőknek. Ha ezt az API-t egy másik termékhez szeretné adni, írja be vagy válassza ki a termék nevét. Ismételje meg ezt a lépést az API több termékhez való hozzáadásához. A **Beállítások** lapon később is hozzáadhat API-kat a termékekhez.<br/><br/>  A termékekkel kapcsolatos további információkért lásd: [termékek létrehozása és közzététele](api-management-howto-add-products.md).|
   |**Átjárók**|**Felügyelt**|API-átjárók, amelyek elérhetővé teszik az API-t. Ez a mező csak a **fejlesztői** és **prémium** szintű szolgáltatásokban érhető el.<br/><br/>A **felügyelt** érték azt jelzi, hogy a API Management szolgáltatásba épített átjárót a Microsoft az Azure-ban üzemelteti. A saját üzemeltetésű [átjárók](self-hosted-gateway-overview.md) csak a prémium és a fejlesztői szolgáltatási szinteken érhetők el. A helyszíni vagy más felhőkben is üzembe helyezhetők.<br/><br/> Ha nincs kiválasztva átjáró, az API nem lesz elérhető, és az API-kérések sikertelenek lesznek.|
   |**Új verziót készít az API-ról?**|Kijelölés vagy kijelölés kiválasztása|További információ: [az API több verziójának közzététele](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Az API API-felhasználóknak való közzétételéhez társítsa azt egy termékhez.

2. Válassza a **Létrehozás** lehetőséget.

Ha problémája van az API-definíciók importálásával, tekintse meg az [ismert problémák és korlátozások listáját](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Az új API tesztelése a Azure Portal

Az API-műveleteket közvetlenül a Azure Portal hívhatja, amely kényelmes módot biztosít a műveletek megtekintésére és tesztelésére.

1. Az API Management-példány bal oldali navigációs sávján válassza az **API**-k  >  **bemutató konferencia API** elemet.
1. Válassza a **teszt** fület, majd válassza a **GetSpeakers** lehetőséget. A lapon láthatók a **lekérdezési paraméterek** és a **fejlécek**, ha vannak ilyenek. Az **OCP-APIM-Subscription-Key** automatikusan ki van töltve az ehhez az API-hoz társított előfizetési kulcshoz.
1. Kattintson a **Küldés** gombra.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Az API tesztelése Azure Portal":::

   A háttér **200 OK** és néhány adattal válaszol.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az első API importálása
> * Az API tesztelése az Azure Portalon

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre és tehet közzé egy terméket:

> [!div class="nextstepaction"]
> [Termékek létrehozása és közzététele](api-management-howto-add-products.md)
