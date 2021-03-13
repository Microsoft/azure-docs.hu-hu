---
title: Webalkalmazás-oktatóanyag – ügyfélalkalmazás beállítása
description: Ez az oktatóanyag végigvezeti egy nyilvános alkalmazás regisztrálásának lépésein a webalkalmazások üzembe helyezésére való felkészüléshez
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bf357dcc9c4790632a1131278edc41c13ac810b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018912"
---
# <a name="client-application-registration"></a>Ügyfélalkalmazás regisztrálása
Az előző oktatóanyagban üzembe helyezte és beállította az Azure API-t a FHIR-hez. Most, hogy már rendelkezik a FHIR-hez készült Azure API-val, regisztrálni fogjuk egy nyilvános ügyfélalkalmazás-alkalmazást. További részletekért és hibaelhárításért tekintse át a [nyilvános ügyfélalkalmazás teljes regisztrációját](register-public-azure-ad-client-app.md) ismertető útmutatót, de az alábbi oktatóanyag fő lépéseit ismertetjük.

1. Navigáljon Azure Active Directory
1. Válassza az **alkalmazás regisztrálása**  -->  **új regisztráció** lehetőséget
1. Adja meg az alkalmazás nevét
1. Válassza a **nyilvános ügyfél/natív (mobil & asztali)** lehetőséget, és állítsa be az átirányítási URI-t `https://www.getpostman.com/oauth2/callback` .

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Képernyőkép az alkalmazás regisztrálása panelről, valamint egy példa alkalmazás neve és átirányítási URL-címe.":::

## <a name="client-application-settings"></a>Ügyfélalkalmazás beállításai

Az ügyfélalkalmazás regisztrálása után másolja az alkalmazás (ügyfél) AZONOSÍTÓját és a bérlő AZONOSÍTÓját az Áttekintés lapról. Ezt a két értéket később kell megadnia az ügyfélhez való hozzáféréskor.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Képernyőkép az ügyfélalkalmazás beállításai panelről, az alkalmazás és a könyvtár azonosítóinak kiemelve.":::

### <a name="connect-with-web-app"></a>Kapcsolódjon a webalkalmazáshoz

Ha [megírta a webalkalmazást](tutorial-web-app-write-web-app.md) a FHIR készült Azure API-hoz való kapcsolódáshoz, akkor a megfelelő hitelesítési beállításokat is be kell állítania. 

1. A bal oldali menüben kattintson a **kezelés** elemre, majd válassza a **hitelesítés** lehetőséget. 

1. Új platform-konfiguráció hozzáadásához válassza a **web** lehetőséget.

1. Állítsa be az átirányítási URI-t a felkészülés során, amikor létrehozza a webalkalmazást az oktatóanyag negyedik részében. Ehhez vegye fel `https://\<WEB-APP-NAME>.azurewebsites.net` az átirányítási URI-listára. Ha egy másik nevet választ a [webalkalmazás írásának](tutorial-web-app-write-web-app.md)lépése során, akkor vissza kell térnie, és frissítenie kell ezt.

1. Jelölje be a **hozzáférési jogkivonat** és az **azonosító token** jelölőnégyzetet.

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Képernyőkép az alkalmazás-hitelesítési beállítások panelről, a Kiemelt platform hozzáadásának lépéseivel.":::

## <a name="add-api-permissions"></a>API-engedélyek hozzáadása

Most, hogy beállította a megfelelő hitelesítést, állítsa be az API-engedélyeket:

1. Válassza az **API-engedélyek** lehetőséget, majd kattintson **az engedély hozzáadása** lehetőségre.
1. A **saját szervezet által használt API**-k területen keressen rá az Azure Healthcare API-k kifejezésre.
1. Válassza a **user_impersonation** lehetőséget, majd kattintson az **engedélyek hozzáadása** lehetőségre.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Képernyőkép az API-engedélyek hozzáadása panelről, amely a Kiemelt API-engedélyek hozzáadásának lépéseit írja le.":::

## <a name="next-steps"></a>Következő lépések
Most már rendelkezik egy nyilvános ügyfélalkalmazás. A következő oktatóanyagban bemutatjuk a tesztelést és az alkalmazáshoz való hozzáférést a Poster használatával.

>[!div class="nextstepaction"]
>[Ügyfélalkalmazás tesztelése a Poster-ban](tutorial-web-app-test-postman.md)
