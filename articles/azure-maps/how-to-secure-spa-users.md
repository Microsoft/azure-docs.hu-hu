---
title: Egyoldalas alkalmazás biztonságossá tétele felhasználói bejelentkezéssel
titleSuffix: Azure Maps
description: Egy egyoldalas alkalmazás konfigurálása, amely támogatja az Azure AD egyszeri bejelentkezést Azure Maps web SDK-val.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 60d8dc45fb26ea210b1827a6938716474faa0304
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895613"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Egyoldalas alkalmazás biztonságossá tétele felhasználói bejelentkezéssel

A következő útmutató olyan alkalmazásra vonatkozik, amely egy Content Serveren fut, vagy minimális webkiszolgálói függőségekkel rendelkezik. Az alkalmazás csak az Azure AD-felhasználók számára biztosít védett erőforrásokat. A forgatókönyv célja, hogy engedélyezze a webalkalmazásnak a hitelesítést az Azure AD-ben, és hívja meg Azure Maps REST API-kat a felhasználó nevében.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Alkalmazás-regisztráció létrehozása az Azure AD-ben

Hozza létre a webalkalmazást az Azure AD-ben a felhasználók számára a bejelentkezéshez. A webalkalmazás delegálja a felhasználói hozzáférést Azure Maps REST API-khoz.

1. Az Azure Portal az Azure-szolgáltatások listájában válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.  

    > [!div class="mx-imgBorder"]
    > ![Alkalmazásregisztráció](./media/how-to-manage-authentication/app-registration.png)

2. Adjon meg egy **nevet**, válassza ki a **támogatási fiók típusát**, adjon meg egy átirányítási URI-t, amely azt az URL-címet jelöli, amelyet az Azure ad kiállít a jogkivonat számára, és az URL-címet, ahol a Térkép vezérlőelem található. Részletes minta: [Azure Maps Azure ad-minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Válassza a **Regisztráció** lehetőséget.  

3. Ha delegált API-engedélyeket szeretne hozzárendelni Azure Mapshoz, nyissa meg az alkalmazást. Ezután a **Alkalmazásregisztrációk** alatt válassza az **API-engedélyek**  >  **Hozzáadás engedélyt**. A **szervezetem által használt API**-k alatt keresse meg és válassza a **Azure Maps** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás API-engedélyeinek hozzáadása](./media/how-to-manage-authentication/app-permissions.png)

4. Jelölje be a **hozzáférési Azure Maps** melletti jelölőnégyzetet, majd válassza az **engedélyek hozzáadása** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás API-engedélyeinek kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

5. Engedélyezés `oauth2AllowImplicitFlow` . Ennek engedélyezéséhez az alkalmazás-regisztráció **jegyzékfájl** szakaszában állítsa a következőre: `oauth2AllowImplicitFlow` `true` .

6. Másolja az Azure AD-alkalmazás AZONOSÍTÓját és az Azure AD-bérlő AZONOSÍTÓját az alkalmazás regisztrációjában a web SDK-ban való használatra. Adja hozzá az Azure AD-alkalmazás regisztrációs adatait és az `x-ms-client-id` Azure Map-fiókját a web SDK-hoz.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Konfigurálja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) felhasználókhoz vagy csoportokhoz. Az [Azure RBAC engedélyezéséhez tekintse meg a következő részeket](#grant-role-based-access-for-users-to-azure-maps).
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Következő lépések

Az egyoldalas alkalmazás forgatókönyvének további megértése:
> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás](../active-directory/develop/scenario-spa-overview.md)

A Azure Maps-fiók API-használati metrikáinak megkeresése:
> [!div class="nextstepaction"]
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

Ismerkedjen meg az Azure AD és a Azure Maps integrációját bemutató példákkal:
> [!div class="nextstepaction"]
> [Azure Maps minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)