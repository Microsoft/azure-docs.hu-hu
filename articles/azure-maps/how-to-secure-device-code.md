---
title: Az Azure AD-vel és a Azure Maps REST API-kkal biztonságossá teheti a bemeneti korlátozott eszközt
titleSuffix: Azure Maps
description: Olyan böngésző nélküli alkalmazás konfigurálása, amely támogatja az Azure AD-be való bejelentkezést, és meghívja a Azure Maps REST API-kat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895630"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Egy bemeneti korlátozott eszköz biztonságossá tétele az Azure AD-vel és a Azure Maps REST API-kkal

Ez az útmutató azt ismerteti, hogyan lehet biztonságossá tenni olyan nyilvános alkalmazásokat vagy eszközöket, amelyek nem tudnak biztonságosan tárolni a titkokat, illetve nem fogadják el a böngésző Az ilyen típusú alkalmazások a IoT vagy az Internet of Things kategóriába tartoznak. Ilyen alkalmazások például a következők lehetnek: intelligens TV-eszközök vagy szenzor-adatkibocsátó alkalmazások. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Alkalmazás-regisztráció létrehozása az Azure AD-ben

> [!NOTE]
> * **Előfeltétel-olvasás:** [forgatókönyv: a webes API-kat meghívó asztali alkalmazás](../active-directory/develop/scenario-desktop-overview.md)
> * A következő forgatókönyv az eszköz kódjának folyamatát használja, amely nem tartalmaz egy webböngészőt a jogkivonat beszerzéséhez.

Hozza létre az eszközön alapuló alkalmazást az Azure AD-ben az Azure AD-bejelentkezés engedélyezéséhez. Ez az alkalmazás hozzáférést kap a Azure Maps REST API-khoz.

1. Az Azure Portal az Azure-szolgáltatások listájában válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.  

    > [!div class="mx-imgBorder"]
    > ![Alkalmazásregisztráció](./media/how-to-manage-authentication/app-registration.png)

2. Adjon meg egy **nevet**, a **szervezeti könyvtárban** válassza a fiókok lehetőséget, csak a **támogatott fióktípus**. Az **átirányítási URI**-k területen adja meg a **nyilvános ügyfél/natív (Mobile & Desktop)** értéket, majd adja hozzá `https://login.microsoftonline.com/common/oauth2/nativeclient` az értéket. További részletekért tekintse meg az Azure AD [Desktop alkalmazást, amely meghívja a webes API-kat: alkalmazás-regisztráció](../active-directory/develop/scenario-desktop-app-registration.md). Ezután **regisztrálja** az alkalmazást.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás regisztrációs adatainak hozzáadása a név és az átirányítási URI számára](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Navigáljon a **hitelesítéshez** , és engedélyezze **nyilvános ügyfélként az alkalmazás kezelését**. Ez lehetővé teszi az Azure AD-ban az eszköz kódjának hitelesítését.
    
    > [!div class="mx-imgBorder"]
    > ![Alkalmazások regisztrálásának engedélyezése nyilvános ügyfélként](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Ha delegált API-engedélyeket szeretne hozzárendelni Azure Mapshoz, nyissa meg az alkalmazást. Ezután válassza az **API-engedélyek** engedély  >  **hozzáadása** lehetőséget. A **szervezetem által használt API**-k alatt keresse meg és válassza a **Azure Maps** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás API-engedélyeinek hozzáadása](./media/how-to-manage-authentication/app-permissions.png)

5. Jelölje be a **hozzáférési Azure Maps** melletti jelölőnégyzetet, majd válassza az **engedélyek hozzáadása** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás API-engedélyeinek kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

6. Konfigurálja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) felhasználókhoz vagy csoportokhoz. Lásd: [szerepköralapú hozzáférés biztosítása a felhasználók számára a Azure Maps](#grant-role-based-access-for-users-to-azure-maps).

7. Kód hozzáadása a jogkivonat-adatfolyam beszerzéséhez az alkalmazásban a megvalósítás részleteiért lásd: az [eszköz kódjának folyamata](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow). A jogkivonatok beszerzése során hivatkozzon a hatókörre: `user_impersonation` Ez a korábbi lépéseknél lett kiválasztva.

> [!Tip]
> Hozzáférési jogkivonatok beszerzése a Microsoft Authentication Library (MSAL) használatával. A [webes API-kat meghívó asztali alkalmazásra vonatkozó javaslatok megtekintése: kód konfigurálása](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Állítsa össze a HTTP-kérést a beszerzett jogkivonattal az Azure AD-ből, és küldjön egy érvényes HTTP-ügyféllel rendelkező kérelmet.

### <a name="sample-request"></a>Példa a kérelemre
Íme egy példa a kérelem törzsére, amely egy központból és egy sugárból álló, kör alakú geometriaként jelölt egyszerű Geokerítésen feltöltésére szolgál.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 Az alábbi minta-kérelem törzse a GeoJSON:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Példa a válaszra:

Fejlécek
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Törzse
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Következő lépések

A Azure Maps-fiók API-használati metrikáinak megkeresése:
> [!div class="nextstepaction"]
> [Használati metrikák megtekintése](how-to-view-api-usage.md)