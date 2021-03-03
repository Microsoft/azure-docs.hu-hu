---
title: Védett webes API-alkalmazások regisztrálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t és az alkalmazás regisztrálásához szükséges információkat.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: dcfedf2cceddb59d456d421c4846f3cd252a65b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651864"
---
# <a name="protected-web-api-app-registration"></a>Védett webes API: alkalmazás regisztrálása

Ez a cikk ismerteti az alkalmazások regisztrálásának sajátosságait egy védett webes API-hoz.

Az alkalmazások regisztrálásának általános lépéseiért tekintse meg a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformmal](quickstart-register-app.md)című témakört.

## <a name="accepted-token-version"></a>Elfogadott jogkivonat-verzió

A Microsoft Identity platform 1.0-s és v 2.0-tokeneket tud kibocsátani. További információ ezekről a jogkivonatokról: [hozzáférési tokenek](access-tokens.md).

Az API által elfogadott jogkivonat-verzió a **támogatott fióktípus** beállításától függ, amikor létrehozza a webes API-alkalmazás regisztrációját a Azure Portal.

- Ha a **támogatott fióktípus** a **szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com) lévő fiókok**, az elfogadott jogkivonat-verziónak v 2.0-nak kell lennie.
- Ellenkező esetben az elfogadott jogkivonat-verzió v 1.0 lehet.

Az alkalmazás létrehozása után az alábbi lépéseket követve meghatározhatja vagy módosíthatja az elfogadott jogkivonat verzióját:

1. A Azure Portal válassza ki az alkalmazást, majd válassza a **manifest (jegyzékfájl**) lehetőséget.
1. Keresse meg a jegyzékfájlban a **accessTokenAcceptedVersion** tulajdonságot.
1. Az érték határozza meg, hogy Azure Active Directory (Azure AD) melyik jogkivonat-verziót fogadja a webes API.
    - Ha az érték 2, a webes API a 2.0-s verzióban található jogkivonatokat fogadja el.
    - Ha az érték **Null**, a webes API elfogadja a v 1.0 jogkivonatokat.
1. Ha módosította a jogkivonat verzióját, válassza a **Mentés** lehetőséget.

> [!NOTE]
> A webes API meghatározza, hogy melyik jogkivonat-verziót fogadja el. Amikor egy ügyfél jogkivonatot kér a webes API-hoz a Microsoft Identity platformon, az ügyfél kap egy jogkivonatot, amely jelzi, hogy a webes API melyik token-verziót fogadja el.

## <a name="no-redirect-uri"></a>Nincs átirányítási URI

A webes API-knak nem kell regisztrálniuk egy átirányítási URI-t, mert egyetlen felhasználó sem jelentkezett be interaktív módon.

## <a name="exposed-api"></a>Elérhető API

A webes API-kra vonatkozó egyéb beállítások a kitett API-k, illetve a közzétett hatókörök vagy alkalmazás-szerepkörök.

### <a name="application-id-uri-and-scopes"></a>Alkalmazás-azonosító URI-ja és hatókörök

A hatókörök általában az űrlappal rendelkeznek `resourceURI/scopeName` . Microsoft Graph esetén a hatókörökhöz parancsikonok tartoznak. Például `User.Read` egy parancsikon a következőhöz: `https://graph.microsoft.com/user.read` .

Az alkalmazás regisztrálása során adja meg a következő paramétereket:

- Az erőforrás URI-ja
- Egy vagy több hatókör
- Egy vagy több alkalmazás-szerepkör

Alapértelmezés szerint az alkalmazás regisztrációs portálja azt javasolja, hogy az erőforrás URI-JÁT használja `api://{clientId}` . Ez az URI egyedi, de nem olvasható. Ha megváltoztatja az URI-t, ellenőrizze, hogy az új érték egyedi-e. Az alkalmazás regisztrációs portálján meg kell adni egy [konfigurált közzétevő tartományt](howto-configure-publisher-domain.md).

Az *ügyfélalkalmazások számára a* hatókörök *meghatalmazott engedélyekként* és az alkalmazás szerepköreiként jelennek meg a webes API-hoz.

A hatókörök az alkalmazás felhasználói számára megjelenített engedélyezési ablakban is megjelennek. Ezért adja meg a megfelelő karakterláncokat, amelyek a hatókört írják le:

- A felhasználó által látottak szerint.
- A bérlői rendszergazda látja, hogy ki adhat rendszergazdai jóváhagyást.

Az alkalmazási szerepköröket nem lehet egy felhasználó (például egy olyan alkalmazás használja, amely a webes API-t hívja saját maga nevében). A bérlői rendszergazdának hozzá kell adnia a webes API-nak az alkalmazás szerepköreihez tartozó ügyfélalkalmazások számára. Részletekért tekintse meg a [rendszergazdai beleegyezik](v2-admin-consent.md)

### <a name="exposing-delegated-permissions-scopes"></a>Delegált engedélyek elmagyarázása (hatókörök)

1. Válassza ki az **API közzététele** az alkalmazás regisztrálásakor lehetőséget.
1. Válassza a **Hatókör hozzáadása** lehetőséget.
1. Ha a rendszer kéri, fogadja el a javasolt alkalmazás-azonosító URI-t ( `api://{clientId}` ) a **Mentés és folytatás** lehetőség kiválasztásával.
1. A következő értékek meghatározása:
    - Válassza ki a **hatókör nevét** , és adja meg a **access_as_user**.
    - Válassza ki, hogy **ki férhet** hozzá, és győződjön meg arról, hogy a **rendszergazdák és a felhasználók** lehetőség ki van választva
    - Válassza a **rendszergazdai engedély megjelenítendő neve** lehetőséget, és adja meg **a hozzáférés TodoListService felhasználóként**.
    - Válassza a rendszergazdai jogosultságok **leírása** lehetőséget, és adja meg **a TodoListService web API-t felhasználóként**.
    - Válassza ki a **felhasználói beleegyezikés megjelenítendő nevét** , és adja meg **a hozzáférés TodoListService felhasználóként**.
    - Válassza ki a **felhasználói beleegyezikés leírását** , és adja meg **a TodoListService web API felhasználóként való elérését**.
    - Állítsa az **állapot** értéket **engedélyezve** értékre.
 1. Válassza a **hatókör hozzáadása** elemet.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Ha a webes API-t egy Daemon-alkalmazás hívja meg

Ebből a szakaszból megtudhatja, hogyan regisztrálhatja a védett webes API-t, hogy a démon-alkalmazások biztonságosan tudják hívni.

- Csak az *alkalmazásra vonatkozó engedélyek* deklarálása és közzététele, mert a Daemon-alkalmazások nem működnek együtt a felhasználókkal. A delegált engedélyek nem lennének értelme.
- A bérlői rendszergazdák megkövetelhetik, hogy az Azure AD webes API-jogkivonatokat bocsásson ki olyan alkalmazásoknak, amelyek regisztráltak az API-alkalmazási engedélyek egyikének elérésére.

#### <a name="exposing-application-permissions-app-roles"></a>Alkalmazás engedélyeinek kitöltése (alkalmazás-szerepkörök)

Az alkalmazás engedélyeinek közzététele érdekében szerkessze a jegyzékfájlt.

1. Az alkalmazás regisztrációjában válassza a **jegyzékfájl** lehetőséget.
1. A jegyzékfájl szerkesztéséhez keresse meg a `appRoles` beállítást, és adja hozzá az alkalmazás-szerepköröket. A szerepkör-definíciókat a következő JSON-blokkban kell megadnia.
1. `allowedMemberTypes`A beállítás csak a következőre hagyható: `"Application"` .
1. Győződjön meg arról `id` , hogy egyedi GUID-azonosító.
1. Ügyeljen rá `displayName` , hogy `value` ne tartalmazzon szóközt.
1. Mentse a jegyzékfájlt.

A következő minta a tartalmát mutatja `appRoles` , ahol a értéke lehet `id` bármely egyedi GUID.

```json
"appRoles": [
  {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
  }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Annak biztosítása, hogy az Azure AD a webes API-hoz tartozó jogkivonatokat csak az ügyfelek számára engedélyezzék

A webes API ellenőrzi az alkalmazás szerepkörét. Ez a szerepkör egy szoftverfejlesztői módszer, amely lehetővé teszi az alkalmazás engedélyeinek megjelenítését. Az Azure AD-t úgy is beállíthatja, hogy csak olyan alkalmazások számára bocsásson ki API-jogkivonatokat, amelyeket a bérlői rendszergazda jóváhagy az API-hozzáféréshez.

A fokozott biztonság hozzáadása:

1. Az alkalmazás regisztrálásához lépjen az alkalmazás **áttekintése** oldalra.
1. A **felügyelt alkalmazás helyi könyvtárban** válassza ki a hivatkozást az alkalmazás nevével. A kijelölés címkéje csonkítva jelenhet meg. Előfordulhat például, hogy **a felügyelt alkalmazás szerepel a** következőben:...

   > [!NOTE]
   >
   > Ha ezt a hivatkozást választja, lépjen a **vállalati alkalmazás áttekintése** lapra. Ez a lap az alkalmazáshoz tartozó egyszerű szolgáltatáshoz van társítva abban a bérlőben, amelyben létrehozta. A böngésző vissza gombjával megnyithatja az alkalmazás regisztrációs lapját.

1. Válassza a **Tulajdonságok** lapot a vállalati alkalmazások lapjainak **kezelés** szakaszában.
1. Ha azt szeretné, hogy az Azure AD csak bizonyos ügyfelektől engedélyezze a webes API-hoz való hozzáférést, állítsa be a **felhasználó-hozzárendelést?** **Igen** értékre.

   > [!IMPORTANT]
   >
   > Ha a **felhasználó-hozzárendelést kötelező megadni?** **Igen**, az Azure ad ellenőrzi az ügyfél alkalmazás-szerepkör-hozzárendeléseit, amikor webes API-hozzáférési jogkivonatot kér. Ha az ügyfél nincs hozzárendelve egyetlen alkalmazás-szerepkörhöz sem, az Azure AD visszaküldi a következő hibaüzenetet: "invalid_client: AADSTS501051: az alkalmazás \<application name\> nincs hozzárendelve a (z \<web API\> )" szerepkörhöz.
   >
   > Ha megtartja a **felhasználó-hozzárendelést?** a **nem** értékre van ÁLLÍTVA, az Azure ad nem fogja megtekinteni az alkalmazás szerepkör-hozzárendelését, ha az ügyfél hozzáférési jogkivonatot kér a webes API- Minden olyan démon-ügyfél, amely az ügyfél hitelesítő adatait használó összes ügyfelet használja, csak a célközönség megadásával kaphat hozzáférési jogkivonatot az API-hoz. Bármely alkalmazás hozzáférhet az API-hoz anélkül, hogy engedélyt kellene kérnie rá.
   >
   > Az előző szakaszban leírtaknak megfelelően azonban a webes API-k mindig ellenőrizhetik, hogy az alkalmazás rendelkezik-e a megfelelő szerepkörrel, amelyet a bérlői rendszergazda is jóváhagy. Az API végrehajtja ezt az ellenőrzést annak ellenőrzésével, hogy a hozzáférési jogkivonat rendelkezik szerepkör-jogcímvel, és hogy a jogcím értéke helyes-e. Az előző JSON-mintában az érték a ( `access_as_application` ).

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

Lépjen be a következő cikkbe ebben a forgatókönyvben, az [alkalmazás kódjának konfigurációjában](scenario-protected-web-api-app-configuration.md).
