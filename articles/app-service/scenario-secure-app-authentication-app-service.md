---
title: Oktatóanyag – hitelesítés hozzáadása webalkalmazáshoz Azure App Serviceon | Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a hitelesítését és engedélyezését Azure App Serviceon futó webalkalmazásokhoz. Korlátozza a webalkalmazáshoz való hozzáférést a szervezeten belüli felhasználók számára.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: a8bd2ef1348692bf57f7e5cb7b6606cfcfd324fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905570"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Oktatóanyag: hitelesítés hozzáadása a Azure App Service-on futó webalkalmazáshoz

Megtudhatja, hogyan engedélyezheti a Azure App Service futó webalkalmazások hitelesítését, és korlátozhatja a felhasználók hozzáférését a szervezetében.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="A felhasználói bejelentkezést bemutató diagram." border="false":::

A App Service beépített hitelesítési és engedélyezési támogatást biztosít, így a felhasználók bejelentkezhetnek, és az adataikat a webalkalmazásban minimális vagy egyetlen kód megírásával érhetik el. A App Service hitelesítés/engedélyezési modul használata nem kötelező, de megkönnyíti az alkalmazás hitelesítésének és engedélyezésének egyszerűsítését. Ez a cikk bemutatja, hogyan védheti meg webalkalmazását a App Service hitelesítési/engedélyezési modullal Azure Active Directory (Azure AD) identitás-szolgáltatóként való használatával.

A hitelesítési/engedélyezési modul engedélyezve van és konfigurálva van a Azure Portal és az alkalmazás beállításain keresztül. Nem szükségesek SDK-k, meghatározott nyelvek vagy az alkalmazás kódjának módosítása. Az Azure AD, a Microsoft-fiók, a Facebook, a Google és a Twitter számos különböző identitás-szolgáltatót támogat. Ha a hitelesítési/engedélyezési modul engedélyezve van, minden bejövő HTTP-kérelem áthalad az alkalmazás kódjának kezelése előtt. További információ: [hitelesítés és engedélyezés Azure app Serviceban](overview-authentication-authorization.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Konfigurálja a webes alkalmazás hitelesítését.
> * Korlátozza a webalkalmazáshoz való hozzáférést a szervezeten belüli felhasználók számára.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Webalkalmazás létrehozása és közzététele App Service

Ebben az oktatóanyagban szüksége lesz egy webalkalmazásra, amely a App Servicere van telepítve. Használhat egy meglévő webalkalmazást, vagy a [ASP.net Core](quickstart-dotnetcore.md) rövid útmutatóban új webalkalmazást hozhat létre és tehet közzé app Service.

Akár meglévő webalkalmazást használ, akár újat hoz létre, jegyezze fel a webalkalmazás nevét és azon erőforráscsoport nevét, amelyre a webalkalmazás telepítve van. Ezekre a nevekre az oktatóanyag során van szükség. Ebben az oktatóanyagban például az eljárások és a képernyőképek nevei *SecureWebApp* tartalmaznak.

## <a name="configure-authentication-and-authorization"></a>Hitelesítés és engedélyezés konfigurálása

Most már fut egy webalkalmazás a App Serviceon. Ezután engedélyeznie kell a webalkalmazás hitelesítését és engedélyezését. Az Azure AD-t identitás-szolgáltatóként használhatja. További információ: az [Azure ad-hitelesítés konfigurálása a app Service alkalmazáshoz](configure-authentication-provider-aad.md).

A [Azure Portal](https://portal.azure.com) menüben válassza az **erőforráscsoportok** lehetőséget, vagy keresse meg és válassza ki az **erőforráscsoportot** bármelyik oldalon.

Az **erőforráscsoportok** területen keresse meg és válassza ki az erőforráscsoportot. Az **Áttekintés** területen válassza ki az alkalmazás felügyeleti lapját.

:::image type="content" alt-text="Képernyőkép, amely az alkalmazás felügyeleti oldalának kiválasztását mutatja be." source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

Az alkalmazás bal oldali menüjében válassza a **hitelesítés/engedélyezés** lehetőséget, majd **a** bejelöléssel engedélyezze app Service hitelesítést.

Az **Elvégzendő művelet, ha a kérés nincs hitelesítve** területen válassza a **Bejelentkezés az Azure Active Directoryval** lehetőséget.

A **hitelesítésszolgáltatók** területen válassza a **Azure Active Directory** lehetőséget. Válassza az **expressz** lehetőséget, majd fogadja el az alapértelmezett beállításokat új Active Directory alkalmazás létrehozásához. Válassza az **OK** lehetőséget.

:::image type="content" alt-text="Az expressz hitelesítést bemutató képernyőkép." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

A **hitelesítés/engedélyezés** lapon válassza a **Mentés** lehetőséget.

Amikor megjelenik az üzenettel kapcsolatos értesítés `Successfully saved the Auth Settings for <app-name> App` , frissítse a portál oldalt.

Most már rendelkezik egy olyan alkalmazással, amelyet a App Service hitelesítés és engedélyezés biztosít.

## <a name="verify-limited-access-to-the-web-app"></a>A webalkalmazáshoz való korlátozott hozzáférés ellenőrzése

Ha engedélyezte a App Service hitelesítési/engedélyezési modult, az alkalmazás regisztrálása az Azure AD-bérlőben lett létrehozva. Az alkalmazás regisztrációja ugyanaz a megjelenítendő név, mint a webalkalmazás. A beállítások megtekintéséhez válassza a **Azure Active Directory** lehetőséget a portál menüjében, és válassza a **Alkalmazásregisztrációk** lehetőséget. Válassza ki a létrehozott alkalmazás-regisztrációt. Az áttekintésben ellenőrizze, hogy a **támogatott fióktípus** **csak a saját szervezetre** van-e beállítva.

:::image type="content" alt-text="Képernyőfelvétel: a hozzáférés ellenőrzése." source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Annak ellenőrzéséhez, hogy az alkalmazáshoz való hozzáférés a szervezet felhasználói számára korlátozódik-e, indítson el egy böngészőt az inkognitóban vagy a privát módban, és lépjen a következőre: `https://<app-name>.azurewebsites.net` . A biztonságos bejelentkezési oldalra kell irányítani, hogy a nem hitelesített felhasználók ne férhessenek hozzá a helyhez. A webhelyhez való hozzáféréshez jelentkezzen be felhasználóként a szervezetében. Létrehozhat egy új böngészőt is, és megpróbálhatja bejelentkezni egy személyes fiók használatával annak ellenőrzéséhez, hogy a szervezeten kívüli felhasználók nem rendelkeznek hozzáféréssel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az Oktatóanyaggal, és már nincs szüksége a webalkalmazásra vagy a kapcsolódó erőforrásokra, [törölje a létrehozott erőforrásokat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Konfigurálja a webes alkalmazás hitelesítését.
> * Korlátozza a webalkalmazáshoz való hozzáférést a szervezeten belüli felhasználók számára.

> [!div class="nextstepaction"]
> [Az App Service hozzáfér a tárolóhoz](scenario-secure-app-access-storage.md)
