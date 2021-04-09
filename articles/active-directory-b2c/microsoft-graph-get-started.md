---
title: Microsoft Graph-alkalmazás regisztrálása
titleSuffix: Azure AD B2C
description: A szükséges Graph API engedélyeket biztosító alkalmazás regisztrálásával előkészítheti a Microsoft Graph Azure AD B2C-erőforrások felügyeletét.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67870a458138101f3b8a009f7c96c74991396284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98675186"
---
# <a name="register-a-microsoft-graph-application"></a>Microsoft Graph-alkalmazás regisztrálása

[Microsoft Graph][ms-graph] lehetővé teszi, hogy a Azure ad B2C bérlőn belül számos erőforrást kezelhesse, beleértve az ügyfél felhasználói fiókjait és az egyéni házirendeket is. A [Microsoft Graph API][ms-graph-api]-t meghívó parancsfájlok vagy alkalmazások írásával automatizálhatja a bérlői felügyeleti feladatokat, például a következőket:

* Meglévő felhasználói tároló migrálása Azure AD B2C-bérlőre
* Egyéni szabályzatok üzembe helyezése Azure-folyamattal az Azure DevOps-ben és az egyéni szabályzat-kulcsok kezelése
* A felhasználók regisztrálása a saját oldalán, és felhasználói fiókok létrehozása a Azure AD B2C könyvtárban a jelenetek mögött
* Alkalmazás-regisztráció automatizálása
* Naplók beszerzése

Az alábbi részekben a Microsoft Graph API használatának előkészítését ismertetjük a Azure AD B2C-címtárban lévő erőforrások kezelésének automatizálásához.

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API interakciós módok

Kétféle kommunikációs mód használható a Microsoft Graph API-val való együttműködéshez a Azure AD B2C-bérlő erőforrásainak kezeléséhez:

* **Interaktív** – a futtatási egyszeri feladatok esetében a felügyeleti feladatok elvégzéséhez a B2C-bérlőben rendszergazdai fiók szükséges. Ez a mód megköveteli, hogy a rendszergazda a Microsoft Graph API meghívása előtt jelentkezzen be a hitelesítő adataival.

* **Automatizált** – ütemezett vagy folyamatosan futtatott feladatok esetén ez a módszer egy olyan szolgáltatásfiókot használ, amelyet a felügyeleti feladatok végrehajtásához szükséges engedélyekkel konfigurál. A "szolgáltatásfiók" létrehozása Azure AD B2C egy olyan alkalmazás regisztrálásával, amelyet az alkalmazások és a parancsfájlok az *alkalmazás (ügyfél) azonosítójával* és a **OAuth 2,0 ügyfél hitelesítő adataival** történő hitelesítéshez használnak. Ebben az esetben az alkalmazás önmagára hívja a Microsoft Graph API-t, nem pedig a rendszergazda felhasználót a korábban leírt interaktív módszernek megfelelően.

Az **automatikus** interakciós forgatókönyvet az alábbi részekben bemutatott alkalmazás-regisztráció létrehozásával engedélyezheti.

Bár a OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési folyamatát jelenleg nem támogatja közvetlenül a Azure AD B2C hitelesítési szolgáltatás, beállíthatja az ügyfél hitelesítő adatait az Azure AD-vel és a Microsoft Identity platform/token-végpontjának használatával az Azure AD B2C-bérlőben lévő alkalmazásokhoz. Egy Azure AD B2C bérlő néhány funkciót megoszt az Azure AD Enterprise-Bérlővel.

## <a name="register-management-application"></a>Felügyeleti alkalmazás regisztrálása

Mielőtt a parancsfájlok és az alkalmazások kommunikálhatnak a [Microsoft Graph API][ms-graph-api] -val Azure ad B2C erőforrások kezeléséhez, létre kell hoznia egy alkalmazás-regisztrációt a Azure ad B2C-bérlőben, amely megadja a szükséges API-engedélyeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *managementapp1*.
1. **Csak a szervezeti címtárban** válassza a fiókok elemet.
1. Az **engedélyek** területen törölje a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzet jelölését.
1. Válassza a **Regisztráció** lehetőséget.
1. Jegyezze fel az alkalmazás – áttekintés oldalon megjelenő **alkalmazást (ügyfél-azonosítót)** . Ezt az értéket egy későbbi lépésben kell használni.

### <a name="grant-api-access"></a>API-hozzáférés biztosítása

Ezután adja meg a regisztrált alkalmazás engedélyeit a bérlői erőforrások kezeléséhez a Microsoft Graph API-hívásokkal.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Ügyfél titkos kulcsának létrehozása

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Már rendelkezik egy olyan alkalmazással, amely jogosult a Azure AD B2C-bérlőben lévő felhasználók *létrehozására*, *olvasására*, *frissítésére* és *törlésére* . A *jelszó-frissítési* engedélyek hozzáadásához folytassa a következő szakasszal.

## <a name="enable-user-delete-and-password-update"></a>Felhasználói törlés és jelszó frissítésének engedélyezése

Az *olvasási és írási címtáradatokat* tartalmazó engedély **nem** tartalmazza a felhasználók törlésének képességét vagy a felhasználói fiók jelszavának módosítását.

Ha az alkalmazásnak vagy a parancsfájlnak törölnie kell a felhasználókat, vagy frissítenie kell a jelszavukat, a *felhasználói rendszergazdai* szerepkört az alkalmazáshoz kell rendelnie:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és a **címtár + előfizetés** szűrő használatával váltson át a Azure ad B2C-bérlőre.
1. Keresse meg és válassza ki a **Azure ad B2C**.
1. A **kezelés** területen válassza a **szerepkörök és rendszergazdák** lehetőséget.
1. Válassza ki a **felhasználói rendszergazda** szerepkört.
1. Válassza a **hozzárendelések hozzáadása** lehetőséget.
1. A **kijelölés** szövegmezőbe írja be a korábban regisztrált alkalmazás nevét, például *managementapp1*. Válassza ki az alkalmazást, amikor megjelenik a keresési eredmények között.
1. Válassza a **Hozzáadás** lehetőséget. Az engedélyek teljes propagálása eltarthat néhány percig.

## <a name="next-steps"></a>Következő lépések

Most, hogy regisztrálta a felügyeleti alkalmazást, és megadta a szükséges engedélyeket, az alkalmazásai és szolgáltatásai (például az Azure-folyamatok) használhatják a hitelesítő adatait és a Microsoft Graph API-val való interakcióra vonatkozó engedélyeket. 

* [Hozzáférési token lekérése a Microsoft Azure Active Directory-ból](/graph/auth-v2-service#4-get-an-access-token)
* [A hozzáférési jogkivonat használata a Microsoft Graph meghívásához](/graph/auth-v2-service#4-get-an-access-token)
* [A Microsoft Graph által támogatott B2C-műveletek](microsoft-graph-operations.md)
* [Azure AD B2C felhasználói fiókok kezelése Microsoft Graph](microsoft-graph-operations.md)
* [Naplók beszerzése az Azure AD Reporting API-val](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
