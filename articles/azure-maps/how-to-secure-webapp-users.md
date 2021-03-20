---
title: Webalkalmazások biztonságossá tétele interaktív egyszeri bejelentkezéssel
titleSuffix: Azure Maps
description: Webalkalmazás konfigurálása, amely támogatja az Azure AD egyszeri bejelentkezést Azure Maps web SDK-val az OpenID Connect protokoll használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895588"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Webalkalmazás biztonságossá tétele felhasználói bejelentkezéssel

A következő útmutató egy webkiszolgálókon üzemeltetett alkalmazásra vonatkozik, több üzleti forgatókönyvet tart fenn, és üzembe helyezi a webkiszolgálókat. Az alkalmazásnak követelménye, hogy csak az Azure AD-felhasználók számára biztosítson védett erőforrásokat. A forgatókönyv célja, hogy engedélyezze a webalkalmazásnak a hitelesítést az Azure AD-ben, és hívja meg Azure Maps REST API-kat a felhasználó nevében.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Alkalmazás-regisztráció létrehozása az Azure AD-ben

A bejelentkezéshez létre kell hoznia a webalkalmazást az Azure AD-ben a felhasználók számára. Ez a webalkalmazás ezután delegálja a felhasználói hozzáférést Azure Maps REST API-khoz.

1. Az Azure Portal az Azure-szolgáltatások listájában válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.  

    > [!div class="mx-imgBorder"]
    > ![Alkalmazásregisztráció](./media/how-to-manage-authentication/app-registration.png)

2. Adjon meg egy **nevet**, válassza ki a **támogatási fiók típusát**, adjon meg egy átirányítási URI-t, amely azt az URL-címet jelöli, amelyet az Azure ad kiállít a jogkivonat számára, és az URL-címet, ahol a Térkép vezérlőelem található. További részletekért tekintse meg az Azure AD [-forgatókönyvet: webalkalmazás, amely aláírja a felhasználókat](../active-directory/develop/scenario-web-app-sign-user-overview.md). Hajtsa végre az Azure AD-forgatókönyvben ismertetett lépéseket.  

3. Az alkalmazás regisztrációjának befejeződése után ellenőrizze, hogy az alkalmazás-bejelentkezés működik-e a felhasználók számára. A bejelentkezést követően az alkalmazás delegált hozzáférést kaphat Azure Maps REST API-khoz.
    
4.  Ha delegált API-engedélyeket szeretne hozzárendelni Azure Mapshoz, nyissa meg az alkalmazást. Ezután válassza az **API-engedélyek** engedély  >  **hozzáadása** lehetőséget. A **szervezetem által használt API**-k alatt keresse meg és válassza a **Azure Maps** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás API-engedélyeinek hozzáadása](./media/how-to-manage-authentication/app-permissions.png)

5. Jelölje be a **hozzáférési Azure Maps** melletti jelölőnégyzetet, majd válassza az **engedélyek hozzáadása** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás API-engedélyeinek kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

6. Annak engedélyezése, hogy a webalkalmazás meghívjon Azure Maps REST API-kat úgy, hogy az alkalmazás regisztrációját egy alkalmazás titkos kódjával konfigurálja, részletes lépésekért tekintse meg a [webes API-kat meghívó](../active-directory/develop/scenario-web-app-call-api-app-registration.md)webalkalmazást: alkalmazás-regisztráció. A felhasználó nevében titkos kulcs szükséges az Azure AD-ben való hitelesítéshez. Az alkalmazás regisztrációs tanúsítványát vagy titkos kódját biztonságos tárolóban kell tárolni ahhoz, hogy a webalkalmazás beolvassa az Azure AD-ben való hitelesítést. 
   
   * Ha az alkalmazás már konfigurált egy Azure AD-alkalmazást és egy titkos kulcsot, akkor a lépés kihagyható.

> [!Tip]
> Ha az alkalmazás Azure-környezetben üzemel, javasoljuk, hogy [felügyelt identitásokat használjon az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) , és egy Azure Key Vault-példányt a titkok eléréséhez [egy hozzáférési jogkivonat beszerzésével](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) Azure Key Vault titkok vagy tanúsítványok eléréséhez. A titkok beolvasásához Azure Key Vaulthoz való kapcsolódáshoz lásd: [oktatóanyag a felügyelt identitáson keresztüli kapcsolódáshoz](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).
   
7. Hozzon létre egy biztonságos jogkivonat-végpontot a Azure Maps web SDK számára a jogkivonat eléréséhez. 
   
   * A minta jogkivonat-vezérlővel kapcsolatban lásd: [Azure Maps Azure ad-minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs). 
   * A nem AspNetCore megvalósításához vagy más megoldáshoz lásd: [token beszerzése az alkalmazáshoz](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) az Azure ad dokumentációjában.
   * A biztonságos jogkivonat-végpont feladata a hitelesített és jogosult felhasználó hozzáférési jogkivonatának visszaküldése Azure Maps REST API-k meghívásához.

8. Konfigurálja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) felhasználókhoz vagy csoportokhoz. Lásd: [szerepköralapú hozzáférés engedélyezése a felhasználók számára](#grant-role-based-access-for-users-to-azure-maps).

9. Konfigurálja a webalkalmazás lapot a Azure Maps web SDK-val a biztonságos jogkivonat-végpont eléréséhez. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Következő lépések

A webalkalmazási forgatókönyv további megértése:
> [!div class="nextstepaction"]
> [Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás](../active-directory/develop/scenario-web-app-sign-user-overview.md)

A Azure Maps-fiók API-használati metrikáinak megkeresése:
> [!div class="nextstepaction"]
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

Ismerkedjen meg az Azure AD és a Azure Maps integrációját bemutató példákkal:
> [!div class="nextstepaction"]
> [Azure AD-webalkalmazások Azure Maps mintái](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)