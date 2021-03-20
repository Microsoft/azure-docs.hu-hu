---
title: A Azure AD B2C által támogatott alkalmazások típusai
titleSuffix: Azure AD B2C
description: Ismerje meg a Azure Active Directory B2C használható alkalmazások típusait.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 07897823a3ba3b83e240e8e8dc005ea13b036fce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94952046"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Active Directory B2C használható alkalmazások típusai
 
A Azure Active Directory B2C (Azure AD B2C) számos modern alkalmazás-architektúrán támogatja a hitelesítést. Ezek mindegyike az iparági szabványnak számító [OAuth 2.0](protocols-overview.md) vagy [OpenID Connect](protocols-overview.md) protokollon alapul. Ez a cikk ismerteti a felépíthető alkalmazások típusait, a kívánt nyelvtől és platformtól függetlenül. A cikk segíthet az összetettebb feladatok megértésében, ezért érdemes elolvasni, mielőtt nekifog az alkalmazások létrehozásának.

Az Azure AD B2Ct használó összes alkalmazást regisztrálni kell a [Azure ad B2C-bérlőben](tutorial-create-tenant.md) a [Azure Portal](https://portal.azure.com/)használatával. Az alkalmazás regisztrációs folyamata az értékeket gyűjti és rendeli hozzá, például:

* Egy **alkalmazás-azonosító** , amely egyedileg azonosítja az alkalmazást.
* A **Válasz URL-címe** , amellyel a válaszok visszaállíthatók az alkalmazásba.

A Azure AD B2C elküldhető minden kérelem megadja a **felhasználói folyamatot** (beépített szabályzat) vagy egy **Egyéni szabályzatot** , amely a Azure ad B2C viselkedését vezérli. Mindkét házirend típusa lehetővé teszi, hogy a felhasználói élmények széles körben testreszabható készletét hozza létre.

Az alkalmazások közötti interakció minden esetben hasonló felső szintű mintát követ:

1. Az alkalmazás átirányítja a felhasználót a v 2.0-végpontra egy [szabályzat](user-flow-overview.md)végrehajtásához.
2. A felhasználó a szabályzat definíciója szerint teljesíti a szabályzat feltételeit.
3. Az alkalmazás biztonsági jogkivonatot kap a v 2.0-végponttól.
4. Az alkalmazás a biztonsági jogkivonatot használja a védett információk vagy egy védett erőforrás elérésére.
5. Az erőforrás-kiszolgáló ellenőrzi a biztonsági jogkivonatot, és megállapítja, hogy megadható-e hozzáférés.
6. Az alkalmazás rendszeres időközönként frissíti a biztonsági jogkivonatot.

Ezek a lépések némileg eltérőek lehetnek az Ön által felépített alkalmazás típusától függően.

## <a name="web-applications"></a>Webalkalmazások

A kiszolgálón futtatott és böngészőn keresztül elérhető webalkalmazásokhoz (beleértve a .NET, a PHP, a Java, a Ruby, a Python és a Node.js) a Azure AD B2C támogatja az [OpenID connectet](protocols-overview.md) az összes felhasználói élményhez. Az OpenID Connect Azure AD B2C implementációjában a webalkalmazás felhasználói élményt kezdeményez a hitelesítési kérések Azure AD-be való kibocsátásával. A kérés eredménye egy `id_token`. Ez a biztonsági jogkivonat tartalmazza a felhasználó identitását. Ezenfelül jogcímek formájában információkat nyújt a felhasználóról is:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

További információ a tokenek típusairól és a [Azure ad B2C jogkivonat-referenciában](tokens-overview.md)található alkalmazásokhoz elérhető jogcímekről.

Egy webalkalmazásban a [szabályzatok](user-flow-overview.md) minden végrehajtása a következő magas szintű lépéseket hajtja végre:

1. A felhasználó megkeresi a webalkalmazást.
2. A webalkalmazás átirányítja a felhasználót, hogy Azure AD B2C jelezze a végrehajtandó házirendet.
3. A felhasználó befejezi a szabályzatot.
4. Azure AD B2C visszaad egy `id_token` -t a böngészőbe.
5. A az `id_token` átirányítási URI-ba kerül.
6. A `id_token` érvényesítve van, és a munkamenet-cookie be van állítva.
7. A rendszer egy biztonságos lapot ad vissza a felhasználónak.

A felhasználó identitásának ellenőrzéséhez elegendő az Azure AD-tól kapott nyilvános aláírókulcs segítségével ellenőrizni a `id_token`érvényességét. Ez a folyamat egy munkamenet-cookie-t is beállít, amely a felhasználó azonosítására szolgál a következő lapokra vonatkozó kérelmek esetén.

Ha ezt a forgatókönyvet működés közben szeretné látni, próbálja ki az [első lépések szakaszban](overview.md)található webalkalmazás-bejelentkezési kód mintáit.

Az egyszerű bejelentkezés megkönnyítésén kívül egy webkiszolgáló-alkalmazásnak is szüksége lehet a háttérbeli webszolgáltatás elérésére. Ebben az esetben a webalkalmazás egy kis mértékben eltérő [OpenID Connect folyamatot](openid-connect.md) hajt végre, és engedélyezési kódokkal és frissítési tokenekkel szerzi be a jogkivonatokat. Ezt a folyamatot a következő, [Webes API-k](#web-apis) című fejezet írja le.

## <a name="single-page-applications"></a>Egyoldalas alkalmazások
Számos modern webalkalmazás úgy van kialakítva, mint az ügyféloldali egyoldalas alkalmazások ("gyógyfürdők"). A fejlesztők a JavaScript vagy a SPA-keretrendszer, például a szögletes, a Vue és a reakciók használatával írhatják őket. Ezek az alkalmazások egy webböngészőben futnak, és különböző hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webes alkalmazások.

Azure AD B2C **két** lehetőséget kínál az egyoldalas alkalmazások bejelentkezésére a felhasználók számára, és jogkivonatok beszerzése a háttér-szolgáltatások vagy webes API-k eléréséhez:

### <a name="authorization-code-flow-with-pkce"></a>Engedélyezési kód folyamatábrája (PKCE)
- [OAuth 2,0 engedélyezési kód folyamata (PKCE)](./authorization-code-flow.md). Az engedélyezési kód folyamatábrája lehetővé teszi, hogy az alkalmazás hitelesítő kódot cseréljen az **azonosító** jogkivonatok számára a védett API-k meghívásához szükséges hitelesített felhasználói és **hozzáférési** jogkivonatok megjelenítéséhez. Emellett visszaadja azokat a **frissítési** jogkivonatokat, amelyek hosszú távú hozzáférést biztosítanak az erőforrásokhoz a felhasználók nevében anélkül, hogy ezekkel a felhasználókkal való interakcióra lenne szükség. 

Ez az **ajánlott** módszer. A korlátozott élettartamú frissítési tokenek segítségével az alkalmazás alkalmazkodik a [modern böngésző cookie-i adatvédelmi korlátaihoz](../active-directory/develop/reference-third-party-cookies-spas.md), például a Safari itp-hez.

A folyamat előnyeit kihasználva az alkalmazás olyan hitelesítési függvénytárat használhat, amely támogatja azt, például [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![Egyoldalas alkalmazások – hitelesítés](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>Implicit engedélyezési folyamat
- [OAuth 2,0 implicit folyamat](implicit-flow-single-page-application.md). Egyes keretrendszerek, például [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core), csak az implicit engedélyezési folyamatot támogatják. Az implicit engedélyezési folyamat lehetővé teszi, hogy az alkalmazás **azonosítót** és **hozzáférési** jogkivonatokat kapjon. Az engedélyezési kód folyamatával ellentétben az implicit engedélyezési folyamat nem ad vissza **frissítési jogkivonatot**. 

Ez a hitelesítési folyamat nem tartalmaz olyan alkalmazási helyzeteket, amelyek platformfüggetlen JavaScript-keretrendszereket használnak, például az Electron-et és a reakciós Natívt. Ezek a forgatókönyvek további képességeket igényelnek a natív platformokkal való interakcióhoz.

## <a name="web-apis"></a>Webes API-k

A Azure AD B2C segítségével biztonságossá teheti a webszolgáltatásokat, például az alkalmazás REST-alapú webes API-ját. A webes API-k az OAuth 2.0 használatával biztosíthatják az adatok védelmét a bejövő HTTP-kérések jogkivonatokkal történő hitelesítésével. A webes API hívója hozzáfűz egy jogkivonatot a HTTP-kérés hitelesítési fejlécéhez:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

Így a webes API a jogkivonat segítségével ellenőrizheti az API hívójának identitását, valamint a jogkivonatban kódolt jogcímek segítségével további információkhoz juthat a hívóról. Az [Azure AD B2C-jogkivonatok referenciájából](tokens-overview.md) további információkat tudhat meg az alkalmazásban elérhető jogkivonatok és jogcímek különböző típusairól.

A webes API-k számos különböző típusú ügyféltől kaphatnak jogkivonatokat, például webalkalmazásokat, asztali és mobil alkalmazásokat, egyoldalas alkalmazásokat, kiszolgálóoldali démonokat és más webes API-kat. Íme egy példa a webes API-t meghívó webalkalmazás teljes folyamatára:

1. A webalkalmazás végrehajt egy házirendet, és a felhasználó befejezi a felhasználói élményt.
2. A Azure AD B2C egy (OpenID Connect) `id_token` és egy engedélyezési kódot ad vissza a böngészőnek.
3. A böngésző a `id_token` és az engedélyezési kódot az átirányítási URI-ra könyveli.
4. A webkiszolgáló ellenőrzi a `id_token` és beállítja a munkamenet-cookie-t.
5. A webkiszolgáló a Azure AD B2Cét kéri a számára `access_token` , hogy megadja az engedélyezési kódot, az alkalmazás ügyfél-azonosítóját és az ügyfél hitelesítő adatait.
6. A `access_token` és a `refresh_token` rendszer visszaadja a webkiszolgálónak.
7. A webes API `access_token` -t egy engedélyezési fejlécben kell meghívni.
8. A webes API ellenőrzi a jogkivonatot.
9. A rendszer visszaküldi a biztonságos adatszolgáltatást a webalkalmazásnak.

Ha többet szeretne tudni a hitelesítési kódokról, frissítési jogkivonatokról, valamint a jogkivonatok lekérésének lépéseiről, olvasson az [OAuth 2.0-protokollról](authorization-code-flow.md).

Ha szeretné megtanulni, hogyan biztosíthat védelmet a webes API-k számára az Azure AD B2C segítségével, olvassa el a [kezdeti lépéseket bemutató cikk](overview.md) webes API-kra vonatkozó oktatóanyagát.

## <a name="mobile-and-native-applications"></a>Mobil-és natív alkalmazások

Az eszközökre, például a mobil-és asztali alkalmazásokra telepített alkalmazások esetében gyakran szükséges a háttér-szolgáltatásokhoz vagy webes API-khoz hozzáférni a felhasználók nevében. A natív alkalmazásokhoz testreszabott Identitáskezelés-kezelési tapasztalatokat adhat hozzá, és biztonságos módon hívhat meg háttér-szolgáltatásokat a Azure AD B2C és a [OAuth 2,0 engedélyezési kód folyamatának](authorization-code-flow.md)használatával.

Ebben a folyamatban az alkalmazás [szabályzatokat](user-flow-overview.md) hajt végre, és az `authorization_code` Azure ad-ből fogad egy alkalmazást, miután a felhasználó befejezte a házirendet. A az `authorization_code` alkalmazás azon jogosultságát jelöli, amely a jelenleg bejelentkezett felhasználó nevében hívja meg a háttér-szolgáltatásokat. Az alkalmazás ezt követően a és a háttérben is kicserélheti a `authorization_code` hátteret `access_token` `refresh_token` .  Az alkalmazás a használatával végezheti el a `access_token` hitelesítést egy háttérbeli webes API-ban http-kérelmekben. Az `refresh_token` alkalmas ezenfelül új `access_token` kérésére is, ha a régi lejárna.

## <a name="current-limitations"></a>Aktuális korlátozások

### <a name="unsupported-application-types"></a>Nem támogatott alkalmazástípusok

#### <a name="daemonsserver-side-applications"></a>Démonok/kiszolgálóoldali alkalmazások

A hosszan futó folyamatokat vagy a felhasználó jelenléte nélkül működő alkalmazásokat is meg kell adni a biztonságos erőforrások, például a webes API-k eléréséhez. Ezek az alkalmazások hitelesítik és lekérhetik a jogkivonatokat az alkalmazás identitásával (a felhasználó delegált identitása helyett) és a OAuth 2,0 ügyfél-hitelesítő adatokkal végzett folyamat használatával. Az ügyfél-hitelesítő adatok folyamata nem ugyanaz, mint a használaton kívüli és a nem használt folyamat nem használható a kiszolgálók közötti hitelesítéshez.

Bár a OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési folyamatát jelenleg nem támogatja közvetlenül a Azure AD B2C hitelesítési szolgáltatás, beállíthatja az ügyfél hitelesítő adatait az Azure AD-vel és a Microsoft Identity platform/token-végpontjának használatával az Azure AD B2C-bérlőben lévő alkalmazásokhoz. Egy Azure AD B2C bérlő néhány funkciót megoszt az Azure AD Enterprise-Bérlővel.

Az ügyfél-hitelesítő adatok folyamatának beállításához lásd: [Azure Active Directory v 2.0 és a OAuth 2,0 ügyfél hitelesítő adatainak folyamata](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). A sikeres hitelesítés a jogkivonat formátumának beérkezését eredményezi, hogy az Azure AD az Azure ad- [jogkivonat referenciájában](../active-directory/develop/id-tokens.md)leírtak szerint használható legyen.

A felügyeleti alkalmazások regisztrálásával kapcsolatos utasításokért lásd: [Azure ad B2C kezelése Microsoft Graphsal](microsoft-graph-get-started.md).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Webes API-láncok (meghatalmazásos folyamat)

Számos architektúrában szerepelnek olyan webes API-k, amelyek más, alsóbb rétegbeli webes API-kat hívnak meg, és mindkét API biztonságát az Azure AD B2C garantálja. Ez a forgatókönyv gyakori a webes API-háttérrel rendelkező natív ügyfeleknél, és olyan Microsoft online szolgáltatást hív meg, mint a Microsoft Graph API.

Ez a láncolatba fűzött webes API-megoldás az OAuth 2.0 JWT tulajdonosi hitelesítő adatok megadásával (vagy más néven a meghatalmazásos folyamat) segítségével valósítható meg.  A meghatalmazásos folyamatot azonban még nem implementáltuk az Azure AD B2C-be.

### <a name="faulted-apps"></a>Hibás alkalmazások

A következő módokon ne szerkessze Azure AD B2C alkalmazást:

- Egyéb alkalmazáskezelési portálokon, például az [Alkalmazásregisztrációs portálon](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- A Graph API vagy a PowerShell használatával.

Ha a Azure AD B2C alkalmazást a Azure Portalon kívül szerkeszti, akkor az egy hibás alkalmazás lesz, és már nem használható a Azure AD B2C. Törölje az alkalmazást, majd hozza létre újra.

Az alkalmazás törléséhez nyissa meg az [alkalmazás regisztrációs portálját](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) , és törölje az alkalmazást. Ahhoz, hogy láthatóvá lehessen tenni az alkalmazást, Önnek kell lennie az alkalmazás tulajdonosának (és nem csak a bérlő rendszergazdájának).

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet a [Azure Active Directory B2C felhasználói folyamatai](user-flow-overview.md)által biztosított beépített szabályzatokról.