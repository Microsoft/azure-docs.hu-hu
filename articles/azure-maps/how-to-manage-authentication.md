---
title: Hitelesítés kezelése
titleSuffix: Azure Maps
description: Ismerkedjen meg Azure Maps hitelesítéssel. Megtudhatja, melyik módszer a legmegfelelőbb a forgatókönyvben. Megtudhatja, hogyan tekintheti meg a hitelesítési beállításokat a portál használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864074"
---
# <a name="manage-authentication-in-azure-maps"></a>Hitelesítés kezelése Azure Maps

Azure Maps fiók létrehozása után létrejön egy ügyfél-azonosító és egy kulcs, amely támogatja a Azure Active Directory (Azure AD) hitelesítést és a megosztott kulcsos hitelesítést.

## <a name="view-authentication-details"></a>Hitelesítés részleteinek megtekintése

Azure Maps fiók létrehozása után létrejön az elsődleges és a másodlagos kulcs. Javasoljuk, hogy egy elsődleges kulcsot használjon előfizetési kulcsként, ha [megosztott kulcsos hitelesítést használ a Azure Maps meghívásához](./azure-maps-authentication.md#shared-key-authentication). Másodlagos kulcsot használhat olyan forgatókönyvekben, mint például a kulcsok változásai. További információ: [hitelesítés Azure Mapsban](./azure-maps-authentication.md).

A hitelesítési adatokat a Azure Portalban tekintheti meg. A fiókjában a **Beállítások** menüben válassza a **hitelesítés** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Hitelesítési részletek](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Kategória és forgatókönyv felderítése

Az alkalmazásra vonatkozó igényektől függően az alkalmazás biztonságossá tétele bizonyos utakat igényel. Az Azure AD kategóriákat határoz meg a hitelesítési folyamatok széles körének támogatásához. Tekintse meg az [alkalmazások kategóriáit](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) , hogy megtudja, melyik kategóriába tartozik az alkalmazás.

> [!NOTE]
> Ha megosztott kulcsos hitelesítést használ, a kategóriák és a forgatókönyvek megismerése segít az alkalmazás biztonságossá tételében.

## <a name="determine-authentication-and-authorization"></a>Hitelesítés és engedélyezés meghatározása

Az alábbi táblázat a Azure Maps gyakori hitelesítési és engedélyezési forgatókönyveit ismerteti. A táblázat az egyes forgatókönyvek által kínált védelmi típusok összehasonlítását biztosítja.

> [!IMPORTANT]
> A Microsoft az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) alkalmazásával javasolja a Azure Active Directory (Azure AD) megvalósítását.

| Eset                                                                                    | Hitelesítés | Engedélyezés | Fejlesztési tevékenység | Működési tevékenység |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Megbízható démon/nem interaktív ügyfélalkalmazás](./how-to-secure-daemon-app.md)        | Megosztott kulcsos     | N/A           | Közepes             | Magas               |
| [Megbízható démon/nem interaktív ügyfélalkalmazás](./how-to-secure-daemon-app.md)        | Azure AD       | Magas          | Alacsony                | Közepes             |
| [Web Single Page-alkalmazás interaktív egyszeri bejelentkezéssel](./how-to-secure-spa-users.md) | Azure AD       | Magas          | Közepes             | Közepes             |
| [Web Single Page-alkalmazás nem interaktív bejelentkezéssel](./how-to-secure-spa-app.md)      | Azure AD       | Magas          | Közepes             | Közepes             |
| [Webalkalmazás interaktív egyszeri bejelentkezéssel](./how-to-secure-webapp-users.md)          | Azure AD       | Magas          | Magas               | Közepes             |
| [IoT eszköz/bemeneti korlátozott eszköz](./how-to-secure-device-code.md)                     | Azure AD       | Magas          | Közepes             | Közepes             |

A táblázatban található hivatkozások részletes konfigurációs információkat biztosítanak az egyes forgatókönyvekhez.

## <a name="view-role-definitions"></a>Szerepkör-definíciók megtekintése

A Azure Maps számára elérhető Azure-szerepkörök megtekintéséhez lépjen a hozzáférés- **vezérlés (iam)** elemre. Válassza ki a **szerepkörök** elemet, majd keressen rá a *Azure Maps* kezdetű szerepkörökre. Ezek a Azure Maps szerepkörök azok a szerepkörök, amelyekhez hozzáférést biztosíthat.

> [!div class="mx-imgBorder"]
> ![Elérhető szerepkörök megtekintése](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

A Azure Mapshoz hozzáféréssel rendelkező felhasználók és alkalmazások megtekintéséhez lépjen a **Access Control (iam)** elemre. Itt válassza ki a **szerepkör-hozzárendelések** lehetőséget, majd a szűrést **Azure Maps** alapján.

> [!div class="mx-imgBorder"]
> ![Hozzáférésre jogosult felhasználók és alkalmazások megtekintése](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Azure Maps igénylési jogkivonatai

Igényeljen tokent az Azure AD-jogkivonat végpontján. Az Azure AD-kérelemben használja a következő adatokat:

| Azure-környezet      | Azure AD-jogkivonat végpontja             | Azure-erőforrás azonosítója              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure nyilvános felhő     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government felhő | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

További információ az Azure AD-től a felhasználók és a szolgáltatások hozzáférési jogkivonatának igénylésével kapcsolatban: [hitelesítési forgatókönyvek az Azure ad-hez](../active-directory/develop/authentication-vs-authorization.md) és a [forgatókönyvekben](./how-to-manage-authentication.md#determine-authentication-and-authorization)megadott forgatókönyvek megtekintése.

## <a name="manage-and-rotate-shared-keys"></a>Megosztott kulcsok kezelése és elforgatása

A Azure Maps előfizetési kulcsai hasonlóak a Azure Maps-fiókhoz tartozó legfelső szintű jelszóhoz. Mindig ügyeljen arra, hogy megvédje az előfizetési kulcsait. A kulcsok biztonságos kezelése és elforgatása Azure Key Vault használatával. Kerülje a hozzáférési kulcsok más felhasználók számára történő terjesztését, a merevlemezek kódolását, vagy a mások számára elérhető egyszerű szövegben való mentését. Ha úgy gondolja, hogy a kulcsok biztonsága megsérült, akkor forgassa el a kulcsokat.

> [!NOTE]
> A Microsoft azt javasolja, hogy az Azure Active Directory (Azure AD) használatával engedélyezze a kérelmeket, ha lehetséges, a megosztott kulcs helyett. Az Azure AD kiváló biztonságot és könnyű használatot biztosít a megosztott kulcson keresztül.

### <a name="manually-rotate-subscription-keys"></a>Előfizetési kulcsok manuális elforgatása

A Microsoft azt javasolja, hogy rendszeresen fordítsa el az előfizetési kulcsokat, hogy segítsen a Azure Maps-fiók biztonságának megőrzésében. Ha lehetséges, használja a Azure Key Vault a hozzáférési kulcsok kezeléséhez. Ha nem Key Vault használ, manuálisan kell elforgatnia a kulcsokat.

Két előfizetési kulcs van hozzárendelve, hogy el lehessen forgatni a kulcsokat. A két kulcs biztosítja, hogy az alkalmazás a folyamat során Azure Maps a hozzáférést.

Azure Maps-előfizetési kulcsok elforgatása a Azure Portalban:

1. Frissítse az alkalmazás kódját, és hivatkozzon a Azure Maps fiók másodlagos kulcsára, és telepítse a alkalmazást.
2. Navigáljon a [Azure Portal](https://portal.azure.com/)Azure Maps-fiókjához.
3. A **Beállítások** területen válassza a **hitelesítés** lehetőséget.
4. A Azure Maps-fiók elsődleges kulcsának újralétrehozásához kattintson az elsődleges kulcs melletti **újralétrehozás** gombra.
5. Frissítse az alkalmazás kódját az új elsődleges kulcsra és a telepítésre való hivatkozáshoz.
6. A másodlagos kulcs újragenerálása ugyanúgy történik.

> [!WARNING]
> A Microsoft azt javasolja, hogy egyszerre csak az egyik kulcsot használja az összes alkalmazásban. Ha egyes helyeken és a 2. kulcsban az 1. kulcsot használja másokban, nem fogja tudni elforgatni a kulcsokat anélkül, hogy egyes alkalmazások elvesztik a hozzáférést.

## <a name="next-steps"></a>Következő lépések

További információ: [Azure ad és Azure Maps web SDK](./how-to-use-map-control.md).

A Azure Maps-fiók API-használati metrikáinak megkeresése:
> [!div class="nextstepaction"]
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

Ismerkedjen meg az Azure AD és a Azure Maps integrációját bemutató példákkal:

> [!div class="nextstepaction"]
> [Azure AD-hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)