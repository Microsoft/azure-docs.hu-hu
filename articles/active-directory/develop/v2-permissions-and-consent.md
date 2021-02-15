---
title: Microsoft Identity platform Scopes, permissions, & jóváhagyva
description: Tudnivalók a Microsoft Identity platform végpontjának engedélyezéséről, beleértve a hatóköröket, az engedélyeket és a jóváhagyást.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2658c088304eba457b25bb3dc421b356ba70b57f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102478"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Engedélyek és beleegyezett a Microsoft Identity platform

A Microsoft Identity platformmal integrált alkalmazások olyan engedélyezési modellt követnek, amely lehetővé teszi a felhasználók és a rendszergazdák számára az adatok elérésének szabályozását. Az engedélyezési modell megvalósítása frissítve lett a Microsoft Identity platformon. Megváltoztatja, hogy az alkalmazásnak hogyan kell kommunikálnia a Microsoft Identity platformmal. Ez a cikk az engedélyezési modell alapvető fogalmait ismerteti, beleértve a hatóköröket, az engedélyeket és a hozzájárulást is.

## <a name="scopes-and-permissions"></a>Hatókörök és engedélyek

A Microsoft Identity platform implementálja a [OAuth 2,0](active-directory-v2-protocols.md) hitelesítési protokollt. A OAuth 2,0 egy olyan módszer, amellyel a harmadik féltől származó alkalmazások a felhasználók nevében férhetnek hozzá a webkiszolgálók erőforrásaihoz. Bármely, a Microsoft Identity platformmal integrált webszolgáltatáshoz erőforrás-azonosító vagy *alkalmazás-azonosító URI* tartozik. 

Íme néhány példa a Microsoft által üzemeltetett erőforrásokra:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 mail API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Ugyanez érvényes a Microsoft Identity platformmal integrált, harmadik féltől származó erőforrások esetében is. Ezen erőforrások bármelyike meghatározhat olyan engedélyeket is, amelyek az adott erőforrás funkcióinak kisebb adattömbökbe való felosztására használhatók. A [Microsoft Graph](https://graph.microsoft.com) például a következő feladatok elvégzésére vonatkozó engedélyekkel rendelkezik, egyebek között:

* Felhasználó naptárának beolvasása
* Írás a felhasználó naptárába
* E-mail küldése felhasználóként

Az ilyen típusú engedélyezési definíciók miatt az erőforrás részletesen szabályozhatja az adatmennyiségét, és az API-funkciók elérhetővé válnak. Egy harmadik féltől származó alkalmazás kérheti ezeket az engedélyeket a felhasználóktól és a rendszergazdáktól, akiknek jóvá kell hagynia a kérést, mielőtt az alkalmazás hozzáférhessen az adatokhoz, vagy a felhasználó nevében cselekszik. 

Ha egy erőforrás funkcióit kis engedélyekre darabolják, a harmadik féltől származó alkalmazások csak a működésük elvégzéséhez szükséges engedélyek igénylésére használhatók. A felhasználók és a rendszergazdák megtudhatják, hogy az alkalmazás milyen információhoz férhet hozzá. És biztos lehet abban, hogy az alkalmazás nem működik a kártékony szándékkal. A fejlesztőknek mindig meg kell felelniük a legalacsonyabb jogosultsági szintnek, és csak azokat az engedélyeket kérik, amelyekre az alkalmazásoknak szükségük van a működéséhez.

A OAuth 2,0-es verziójában az ilyen típusú engedélyezési készletek *hatókörnek* nevezzük. Ezeket gyakran *engedélyeknek* is nevezik. A Microsoft Identity platformon az engedélyek karakterlánc-értékként jelennek meg. A Microsoft Graph például az egyes engedélyekhez tartozó karakterlánc értékét:

* A felhasználó naptárának beolvasása a következő használatával: `Calendars.Read`
* Írás a felhasználó naptárába a következő használatával: `Calendars.ReadWrite`
* E-mail küldése felhasználóként a általi használatával `Mail.Send`

Az alkalmazások leggyakrabban a Microsoft Identity platform engedélyezés végpontjának megadásával kérik le ezeket az engedélyeket. Bizonyos magas jogosultsági szintű engedélyek azonban csak rendszergazdai jogosultsággal adhatók meg. A [rendszergazdák a rendszergazdai jogosultságok végpontjának](#admin-restricted-permissions)használatával kérhetik vagy adhatják meg őket. További információért olvassa el az olvasót.

## <a name="permission-types"></a>Engedélyezési típusok

A Microsoft Identity platform két típusú engedélyt támogat: a *delegált engedélyeket* és az *alkalmazás engedélyeit*.

* A **delegált engedélyeket** a bejelentkezett felhasználóval rendelkező alkalmazások használják. Ezekhez az alkalmazásokhoz a felhasználó vagy a rendszergazda beleegyezett az alkalmazás által kért engedélyekkel. Az alkalmazás delegált engedéllyel rendelkezik a bejelentkezett felhasználóként való részvételre, amikor hívásokat kezdeményez a célként megadott erőforráshoz. 

    Egyes delegált engedélyeket a nem rendszergazdai jogosultságokkal is el lehet juttatni. Bizonyos magas jogosultsági szintű engedélyek azonban [rendszergazdai](#admin-restricted-permissions)jogosultságot igényelnek. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök jogosultak a delegált engedélyek megadására, tekintse meg a [rendszergazdai szerepkörre vonatkozó engedélyeket Azure Active Directory (Azure ad)](../roles/permissions-reference.md).

* Az **alkalmazás engedélyeit** olyan alkalmazások használják, amelyek bejelentkezett felhasználó nélkül futnak, például a háttér-szolgáltatásként vagy démonként futó alkalmazások. Csak [egy rendszergazda csatlakozhat az](#requesting-consent-for-an-entire-tenant) alkalmazás engedélyeihez.

A _hatályos engedélyek_ azok az engedélyek, amelyeket az alkalmazás akkor használ, amikor kéréseket tesz a célként megadott erőforrásnak. Fontos megérteni az alkalmazás által biztosított delegált engedélyek és az alkalmazás engedélyei közötti különbséget, valamint azokat az érvényes engedélyeket, amelyeket az alkalmazás akkor kap, amikor hívásokat kezdeményez a célként megadott erőforráshoz.

- A delegált engedélyek esetében az alkalmazás _hatályos engedélyei_ az alkalmazás által biztosított delegált engedélyek legkevesebb jogosultsággal rendelkező szakasza, valamint a jelenleg bejelentkezett felhasználó jogosultságai. Az alkalmazásnak soha nem lehet több jogosultsága, mint a bejelentkezett felhasználónak. 

   A szervezeteken belül a bejelentkezett felhasználó jogosultságait házirend szerint vagy egy vagy több rendszergazdai szerepkör tagsága alapján lehet meghatározni. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök jogosultak a delegált engedélyekre, tekintse meg az [Azure ad-beli rendszergazdai szerepkörre vonatkozó engedélyeket](../roles/permissions-reference.md).

   Tegyük fel például, hogy az alkalmazás megkapta a _User. ReadWrite. All_ delegált engedélyt. Ez az engedély névlegesen ad engedélyt az alkalmazás számára egy cégben lévő összes felhasználó profiljának olvasásához és frissítéséhez. Ha a bejelentkezett felhasználó globális rendszergazda, akkor az alkalmazás frissítheti a szervezet összes felhasználójának profilját. Ha azonban a bejelentkezett felhasználó nem rendelkezik rendszergazdai szerepkörrel, az alkalmazás csak a bejelentkezett felhasználó profilját tudja frissíteni. Nem tudja frissíteni a szervezet többi felhasználójának profilját, mert a felhasználónak nincs jogosultsága a nevében.

- Az alkalmazások engedélyei esetében az alkalmazás _hatályos engedélyei_ a jogosultságok teljes szintje. Például egy olyan alkalmazás, amely rendelkezik a _User. ReadWrite. All_ Application engedélyekkel, frissítheti a szervezet minden felhasználójának profilját.

## <a name="openid-connect-scopes"></a>OpenID Connect-hatókörök

Az OpenID Connect Microsoft Identity platform megvalósítása néhány jól definiált hatókörrel rendelkezik, amelyek a Microsoft Graph:,, és rendszeren is futnak `openid` `email` `profile` `offline_access` . Az `address` és az `phone` OpenID Connect hatókörök nem támogatottak.

Ha az OpenID Connect-hatóköröket és egy tokent kér le, a rendszer egy tokent kap az [UserInfo végpont](userinfo.md)meghívásához.

### <a name="openid"></a>OpenID

Ha egy alkalmazás az [OpenID Connect](active-directory-v2-protocols.md)használatával jelentkezik be, akkor a hatókört kell kérnie `openid` . A `openid` hatókör a munkahelyi fiók beleegyezése lapon jelenik meg, mint a **Bejelentkezés** engedéllyel. A személyes Microsoft-fiók beleegyezése lapon megjelenik a **Profil megtekintése és az alkalmazásokhoz és szolgáltatásokhoz való kapcsolódás a Microsoft-fiók engedély használatával** . 

Ezen engedély használatával egy alkalmazás a jogcím formájában egyedi azonosítót kaphat a felhasználó számára `sub` . Az engedély lehetővé teszi az alkalmazás számára az UserInfo-végpont elérését is. A `openid` hatókört a Microsoft Identity platform token végpontján lehet használni azonosító jogkivonatok beszerzéséhez. Az alkalmazás használhatja ezeket a jogkivonatokat a hitelesítéshez.

### <a name="email"></a>e-mail

A `email` hatókör a `openid` hatókörrel és az egyéb hatókörökkel is használható. Az alkalmazás a kérelem formájában hozzáférést biztosít a felhasználó elsődleges e-mail-címéhez `email` . 

A `email` jogcímek csak akkor szerepelnek a jogkivonatban, ha az e-mail-cím a felhasználói fiókhoz van társítva, amely nem mindig az eset. Ha az alkalmazás a `email` hatókört használja, az alkalmazásnak képesnek kell lennie olyan eset kezelésére, amelyben nincs `email` jogcím a jogkivonatban.

### <a name="profile"></a>profil

A `profile` hatókör a `openid` hatókörrel és az egyéb hatókörrel is használható. Hozzáférést biztosít az alkalmazásnak a felhasználóval kapcsolatos nagy mennyiségű információhoz. Az általa elérhető információ magában foglalja a következőket:, de nem kizárólagosan, a felhasználó vezetéknevét, vezetéknevét, előnyben részesített felhasználónevét és objektum-AZONOSÍTÓját. 

Az `profile` adott felhasználó paraméterében elérhető jogcímek teljes listájáért `id_tokens` tekintse meg a [ `id_tokens` hivatkozást](id-tokens.md).

### <a name="offline_access"></a>offline_access

A [ `offline_access` hatókör](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) lehetővé teszi az alkalmazás számára, hogy a felhasználó nevében egy hosszabb ideig hozzáférjen az erőforrásokhoz. A hozzájárulás lapon ez a hatókör jelenik meg, mivel a **hozzáférés megtartása az engedélyhez megadott adathoz** . 

Amikor a felhasználó jóváhagyja a `offline_access` hatókört, az alkalmazás a Microsoft Identity platform jogkivonat-végpontján fogadhatja a frissítési jogkivonatokat. A frissítési tokenek hosszú életűek. Az alkalmazás új hozzáférési jogkivonatokat kaphat, mint a régebbiek lejárnak.

> [!NOTE]
> Ez az engedély jelenleg az összes beleegyezési oldalon jelenik meg, még olyan folyamatok esetében is, amelyek nem biztosítanak frissítési jogkivonatot (például az [implicit folyamatot](v2-oauth2-implicit-grant-flow.md)). Ez a beállítás azokat a forgatókönyveket ismerteti, amelyekben az ügyfél megkezdődhet az implicit folyamaton belül, majd áthelyezhető arra a kódra, amelyben frissítési jogkivonat várható.

A Microsoft Identity platformon (a v 2.0-végpontra irányuló kérelmek esetében) az alkalmazásnak explicit módon kell megkérnie a `offline_access` hatókört a frissítési tokenek fogadásához. Tehát amikor bevált egy engedélyezési kódot a [OAuth 2,0 engedélyezési kód folyamatában](active-directory-v2-protocols.md), csak a végponttól kap hozzáférési jogkivonatot `/token` . 

A hozzáférési jogkivonat rövid ideig érvényes. Általában egy órán belül lejár. Ezen a ponton az alkalmazásnak újra kell irányítani a felhasználót a `/authorize` végpontra egy új engedélyezési kód beszerzéséhez. Az átirányítás során az alkalmazás típusától függően előfordulhat, hogy a felhasználónak újra be kell írnia a hitelesítő adatait, vagy újra meg kell adnia az engedélyeket.

További információ a frissítési tokenek beszerzéséről és használatáról: a [Microsoft Identity platform protokolljának referenciája](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Egyéni felhasználói engedély kérése

Egy [OpenID Connect vagy OAuth 2,0](active-directory-v2-protocols.md) engedélyezési kérelemben az alkalmazás a lekérdezési paraméter használatával kérheti le a szükséges engedélyeket `scope` . Ha például egy felhasználó bejelentkezik egy alkalmazásba, az alkalmazás a következő példához hasonló kérelmet küld. (Sortöréseket adnak hozzá az olvashatósághoz.)

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

A `scope` paraméter az alkalmazás által kért delegált engedélyek szóközzel tagolt listája. Az engedélyek az erőforrás azonosítójának (az alkalmazás-azonosító URI-ja) hozzáfűzésével vannak jelezve. A kérelem példájában az alkalmazásnak engedéllyel kell rendelkeznie a felhasználó naptárának olvasásához és a levelezés felhasználóként való elküldéséhez.

Miután a felhasználó beírja a hitelesítő adatait, a Microsoft Identity platform ellenőrzi a *felhasználói beleegyezési* rekordokat. Ha a felhasználó nem járult hozzá a korábban kért engedélyekhez, és ha a rendszergazda nem járult hozzá ezekhez az engedélyekhez a teljes szervezet nevében, a Microsoft Identity platform arra kéri a felhasználót, hogy adja meg a kért engedélyeket.

Ebben az esetben a ("az Ön számára hozzáférést biztosít az `offline_access` Ön számára biztosított adathozzáféréshez") engedélyt és a `user.read` ("bejelentkezés és olvasás a profilban") engedélyt automatikusan belefoglalja az alkalmazás kezdeti beleegyezéséhez.  Ezek az engedélyek általában az alkalmazások megfelelő működéséhez szükségesek. Az `offline_access` engedély lehetővé teszi az alkalmazás számára a natív alkalmazások és webalkalmazások szempontjából kritikus fontosságú frissítési tokenek frissítését. Az `user.read` engedély hozzáférést biztosít a `sub` jogcímek számára. Lehetővé teszi, hogy az ügyfél vagy az alkalmazás helyesen azonosítsa a felhasználót az idő múlásával, és a felhasználói adatokhoz hozzáférjen.

![Példa a munkahelyi fiók beleegyezikét megjelenítő képernyőképre.](./media/v2-permissions-and-consent/work_account_consent.png)

Ha a felhasználó jóváhagyja az engedély kérését, a rendszer rögzíti a beleegyezést. A felhasználónak nem kell újból megadnia, amikor később bejelentkeznek az alkalmazásba.

## <a name="requesting-consent-for-an-entire-tenant"></a>A teljes bérlőre vonatkozó belefoglalási kérelem

Ha egy szervezet egy alkalmazás licencét vagy előfizetését vásárolja meg, a szervezet gyakran szeretné proaktív módon beállítani az alkalmazást a szervezet összes tagja számára. Ennek a folyamatnak a részeként a rendszergazda beleegyezik abba, hogy az alkalmazás a bérlő bármely felhasználója nevében működjön. Ha a rendszergazda hozzájárulást ad a teljes bérlőhöz, a szervezet felhasználóinak nem jelennek meg az alkalmazás hozzájárulási lapja.

Ha a bérlő összes felhasználója számára szeretne beleegyezni a delegált engedélyekkel, az alkalmazás használhatja a rendszergazdai engedélyezési végpontot.

Emellett az alkalmazásoknak a rendszergazdai engedélyezési végpontot kell használniuk az alkalmazások engedélyeinek igényléséhez.

## <a name="admin-restricted-permissions"></a>Rendszergazdai hozzáférésre korlátozott engedélyek

Bizonyos magas jogosultsági szintű engedélyek a Microsoft erőforrásaiban csak *rendszergazdai* jogosultsággal állíthatók be. Íme néhány példa az ilyen típusú engedélyekre:

* Az összes felhasználó teljes profiljának olvasása a következő használatával: `User.Read.All`
* Adatírás a szervezet könyvtárába a következő használatával: `Directory.ReadWrite.All`
* A szervezet címtárában lévő összes csoport olvasása a következő használatával: `Groups.Read.All`

Bár a felhasználói felhasználók hozzáférést biztosíthatnak az ilyen típusú adatokhoz, a szervezeti felhasználók nem adhatnak hozzáférést ugyanahhoz a bizalmas vállalati adatokhoz. Ha az alkalmazás egy szervezeti felhasználótól kéri a fenti engedélyek egyikének elérését, a felhasználó hibaüzenetet kap, amely szerint nem jogosult beleegyezni az alkalmazás engedélyeivel.

Ha az alkalmazáshoz hatókörök szükségesek a rendszergazdai jogosultságok korlátozásához, a szervezet rendszergazdájának a szervezet felhasználói nevében hozzá kell járulnia a hatókörökhöz. Annak érdekében, hogy ne jelenjen meg olyan felhasználói kérések, akik beleegyeznek a nem engedélyezett engedélyekre, az alkalmazás használhatja a rendszergazdai hozzájárulási végpontot. A rendszergazdai engedélyezési végpontot a következő szakaszban találja.

Ha az alkalmazás magas jogosultságú delegált engedélyeket kér, és a rendszergazda a rendszergazdai hozzájárulási végponton keresztül engedélyezi ezeket az engedélyeket, akkor a bérlő összes felhasználója számára meg kell adni a hozzájárulást.

Ha az alkalmazás az alkalmazásra vonatkozó engedélyeket kér, és a rendszergazda a rendszergazdai hozzájárulási végponton keresztül adja meg ezeket az engedélyeket, akkor az adott felhasználó nevében nem végezhető el. Ehelyett az ügyfélalkalmazás *közvetlenül* kap engedélyeket. Az ilyen típusú engedélyeket csak a Daemon Services és a háttérben futó egyéb nem interaktív alkalmazások használják.

## <a name="using-the-admin-consent-endpoint"></a>A rendszergazdai engedélyezési végpont használata

Miután a rendszergazdai jogosultsággal rendelkező végpontot használja a rendszergazdai engedély megadásához, elkészült. A felhasználóknak semmilyen további műveletet nem kell elvégezniük. A rendszergazdai jogosultság megadását követően a felhasználók egy tipikus hitelesítési folyamaton keresztül kaphatnak hozzáférési jogkivonatot. Az eredményül kapott hozzáférési jogkivonat rendelkezik a beleegyezési engedélyekkel.

Ha egy globális rendszergazda az alkalmazást használja, és az engedélyezés végpontra irányítja, a Microsoft Identity platform észleli a felhasználó szerepkörét. Megkérdezi, hogy a globális rendszergazda szeretne-e hozzájárulni a teljes bérlő nevében a kért engedélyekhez. Ehelyett egy dedikált rendszergazdai engedélyezési végpont használatával proaktív módon kérheti a rendszergazdát, hogy engedélyezze a teljes bérlő nevében engedélyt. Ez a végpont az alkalmazás engedélyeinek kérelmezéséhez is szükséges. Az alkalmazás engedélyei nem kérhetők az engedélyezés végpontjának használatával.

Ha követi ezeket a lépéseket, az alkalmazás engedélyt kérhet a bérlő összes felhasználója számára, beleértve a rendszergazdai korlátozás alá eső hatóköröket is. Ez a művelet magas jogosultsággal rendelkezik. A műveletet csak akkor használja, ha a forgatókönyvhöz szükséges.

Ha meg szeretné tekinteni a lépéseket megvalósító kódrészletet, tekintse meg a [rendszergazda által korlátozott hatókörű mintát](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) a githubon.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az engedélyek igénylése az alkalmazás regisztrációs portálján

Az alkalmazás regisztrációs portálján az alkalmazások felsorolják a szükséges engedélyeket, beleértve a delegált engedélyeket és az alkalmazás engedélyeit is. Ezzel a beállítással engedélyezheti a `/.default` hatókör használatát és a Azure Portal **engedélyezési rendszergazdai engedélyezési** beállítását.  

Általában az engedélyeket statikusan kell definiálni egy adott alkalmazáshoz. Az alkalmazásnak olyan engedélyekkel kell rendelkeznie, amelyeket az alkalmazás dinamikusan vagy fokozatosan kér le.

> [!NOTE]
>Az alkalmazás engedélyei csak a használatával kérhetők le [`/.default`](#the-default-scope) . Tehát ha az alkalmazásnak szüksége van az alkalmazások engedélyeire, győződjön meg róla, hogy szerepelnek az alkalmazás regisztrációs portálján.

A statikusan kért engedélyek listájának konfigurálása egy alkalmazáshoz:

1. Nyissa meg az alkalmazást az <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményben.
1. Válasszon ki egy alkalmazást, vagy [hozzon létre egy alkalmazást](quickstart-register-app.md) , ha még nem tette meg.
1. Az alkalmazás **Áttekintés** lapjának **kezelés** területén válassza az API- **engedélyek**  >  **Hozzáadás engedélyt**.
1. Az elérhető API-k listájából válassza a **Microsoft Graph** lehetőséget. Ezután adja hozzá az alkalmazás által igényelt engedélyeket.
1. Válassza az **engedélyek hozzáadása** lehetőséget.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Ajánlott: a felhasználó aláírása az alkalmazásba

Ha olyan alkalmazást hoz létre, amely a rendszergazdai jogosultságok végpontját használja, akkor az alkalmazásnak szüksége van egy olyan oldalra vagy nézetre, amelyben a rendszergazda jóváhagyhatja az alkalmazás engedélyeit. A lap a következő lehet:

* Az alkalmazás regisztrációs folyamatának része.
* Az alkalmazás beállításainak része.
* Egy dedikált "kapcsolat" folyamat. 

Sok esetben érdemes megmutatni, hogy az alkalmazás csak akkor jelenjen meg ez a "kapcsolódás" nézet, ha egy felhasználó munkahelyi Microsoft-fiók vagy iskolai Microsoft-fiókval jelentkezett be.

Amikor beírja a felhasználót az alkalmazásba, azonosíthatja azt a szervezetet, amelyhez a rendszergazda tartozik, mielőtt a szükséges engedélyek jóváhagyását kéri. Bár ez a lépés nem feltétlenül szükséges, a segítségével könnyebben hozhat létre egy intuitív felhasználói élményt a szervezeti felhasználók számára. 

A felhasználó a alkalmazásban való aláírásához kövesse a [Microsoft Identity platform protokoll oktatóanyagokat](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése egy címtár-rendszergazdától

Ha készen áll arra, hogy engedélyt kérjen a szervezete rendszergazdájától, átirányíthatja a felhasználót a Microsoft Identity platform rendszergazdai engedélyezési végpontján.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Paraméter        | Feltétel        | Leírás                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Kötelező | Az a címtár-bérlő, amelyre engedélyt szeretne kérni. Egy GUID-vagy felhasználóbarát név formátumban is megadható. Vagy általános módon hivatkozhat a szervezetekkel, ahogy az a példában is látható. Ne használja a "Common" kulcsszót, mert a személyes fiókok nem biztosíthatnak rendszergazdai jogosultságot, kivéve a bérlő kontextusát. A bérlőket kezelő személyes fiókokkal való legjobb kompatibilitás érdekében használja a bérlő AZONOSÍTÓját, ha lehetséges. |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület (ügyfél) azonosítója. |
| `redirect_uri` | Kötelező |Az az átirányítási URI, ahová az alkalmazásnak el kell juttatnia a választ a kezelésére. Pontosan meg kell egyeznie az alkalmazás regisztrációs portálján regisztrált átirányítási URI-k egyikével. |
| `state` | Ajánlott | A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. A kívánt tartalom sztringje lehet. Az állapot használatával kódolja a felhasználó állapotára vonatkozó adatokat az alkalmazásban, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
|`scope`        | Kötelező        | Meghatározza az alkalmazás által igényelt engedélyek készletét. A hatókörök lehet statikus (using [`/.default`](#the-default-scope) ) vagy dinamikus.  Ez a készlet tartalmazhat az OpenID Connect-hatóköröket ( `openid` , `profile` , `email` ). Ha az alkalmazásra vonatkozó engedélyekre van szüksége, a használatával kell `/.default` kérnie a statikusan konfigurált engedélyek listáját.  |


Ezen a ponton az Azure AD-nek a bérlői rendszergazdának kell bejelentkeznie a kérelem teljesítéséhez. A rendszer felkéri a rendszergazdát, hogy hagyja jóvá a paraméterben kért összes engedélyt `scope` .  Ha statikus ( `/.default` ) értéket használt, úgy fog működni, mint a v 1.0 rendszergazdai engedélyezési végpont, és az alkalmazáshoz szükséges engedélyekben található összes hatókörre vonatkozó kérelem beleegyezését kéri.

#### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | A címtár-bérlő, amely az alkalmazást a kért engedélyekkel rendelkezik, GUID formátumban megadva. |
| `state` | A kérelemben szereplő érték, amely a jogkivonat-válaszban is szerepelni fog. A kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését. |
| `admin_consent` | A következőre lesz beállítva: `True` . |

#### <a name="error-response"></a>Hiba válasza

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőre hasonlít:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható. Felhasználható a hibákra való reagálásra is. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hibák kiváltó okának azonosításában. |

Miután sikeres választ kapott a rendszergazdai jogosultsági végponttól, az alkalmazás megszerezte a kért engedélyeket. Ezután kérheti a kívánt erőforráshoz tartozó jogkivonatot.

## <a name="using-permissions"></a>Engedélyek használata

Miután a felhasználó beleegyezett az alkalmazás engedélyeibe, az alkalmazás olyan hozzáférési jogkivonatokat tud beszerezni, amelyek az adott erőforráshoz való hozzáféréshez szükséges engedélyeket képviselik bizonyos kapacitásban. Hozzáférési jogkivonat csak egyetlen erőforráshoz használható. A hozzáférési jogkivonaton belül azonban minden olyan engedély, amelyet az alkalmazás az adott erőforráshoz adott meg. Hozzáférési jogkivonat beszerzéséhez az alkalmazás kérheti a Microsoft Identity platform jogkivonat-végpontját, amely a következőképpen néz ki:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Az eredményül kapott hozzáférési jogkivonatot a HTTP-kérelmekben az erőforráshoz is használhatja. Ez megbízhatóan jelzi az erőforrásnak, hogy az alkalmazás megfelelő engedéllyel rendelkezik egy adott feladat végrehajtásához.

A OAuth 2,0 protokollról és a hozzáférési jogkivonatok beszerzéséről a [Microsoft Identity platform Endpoint Protocol-referenciájában](active-directory-v2-protocols.md)talál további információt.

## <a name="the-default-scope"></a>A/.default hatóköre

A `/.default` hatókör segítségével áttelepítheti az alkalmazásokat a 1.0-s végpontról a Microsoft Identity platform-végpontra. A `/.default` hatókör minden olyan alkalmazáshoz be van építve, amely az alkalmazás regisztrációján konfigurált engedélyek statikus listájára hivatkozik. 

A (z `scope` ) értéke a `https://graph.microsoft.com/.default` `resource=https://graph.microsoft.com` v 1.0 végponton megegyező módon működik. A `https://graph.microsoft.com/.default` hatókörnek a kérelemben való megadásával az alkalmazás olyan hozzáférési jogkivonatot kér, amely az alkalmazás regisztrációs portálon az alkalmazáshoz kiválasztott minden Microsoft Graph engedély hatókörét tartalmazza. A hatókört az erőforrás URI-ja és a alapján kell kiépíteni `/.default` . Tehát ha az erőforrás URI `https://contosoApp.com` -ja, a kért hatókör `https://contosoApp.com/.default` .  Azokban az esetekben, amikor a token megfelelő kéréséhez egy második perjelre van szükség, tekintse meg a [záró perjelet ismertető szakaszt](#trailing-slash-and-default).

A `/.default` hatókör bármely OAuth 2,0-flow-ban használható. Azonban szükség van a folyamaton belüli és ügyfél [-](v2-oauth2-on-behalf-of-flow.md) [hitelesítő adatok folyamatára](v2-oauth2-client-creds-grant-flow.md). Akkor is szüksége lesz rá, ha a v2 rendszergazdai engedélyezési végpontot használja az alkalmazás engedélyeinek igényléséhez.

Az ügyfelek egyetlen kérelemben nem kombinálhatók a statikus ( `/.default` ) és a dinamikus beleegyező engedélyekkel. Ezért `scope=https://graph.microsoft.com/.default+mail.read` hibát eredményez, mert egyesíti a hatókör-típusokat.

### <a name="default-and-consent"></a>/.default és beleegyezett

A `/.default` hatókör a v 1.0 végpont viselkedését `prompt=consent` is elindítja. Az adott erőforrástól függetlenül minden, az alkalmazás által regisztrált engedélyhez beleegyezik. Ha a kérelem részeként szerepel, a `/.default` hatókör olyan tokent ad vissza, amely tartalmazza a kért erőforrás hatóköreit.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, ha a felhasználó már megadott egy engedélyt

A `/.default` hatókör a `resource` -centrikus v 1.0 végpont működésével egyenértékű. A v 1.0 végpont engedélyezési viselkedését is magában hordozza. Ez a művelet `/.default` csak akkor indítja el a jóváhagyást, ha a felhasználó nem kapott engedélyt az ügyfél és az erőforrás között. 

Ha ilyen beleegyezik, a visszaadott jogkivonat az adott erőforráshoz megadott felhasználó összes hatókörét tartalmazza. Ha azonban nem adta meg az engedélyt, vagy ha a paraméter meg van adva `prompt=consent` , a rendszer egy beleegyezési kérést jelenít meg az ügyfélalkalmazás által regisztrált összes hatókörhöz.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>1. példa: a felhasználó vagy a bérlő rendszergazdája engedélyt kapott

Ebben a példában a felhasználó vagy egy bérlői rendszergazda engedélyezte a `mail.read` és a `user.read` Microsoft Graph engedélyt az ügyfélnek. 

Ha az ügyfél kéri `scope=https://graph.microsoft.com/.default` , a rendszer nem jelenít meg beleegyező kérést, függetlenül az ügyfélalkalmazás regisztrált engedélyeinek Microsoft Graph. A visszaadott jogkivonat tartalmazza a hatóköröket `mail.read` és a-t `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>2. példa: a felhasználó nem adott meg engedélyeket az ügyfél és az erőforrás között

Ebben a példában a felhasználó nem kapott beleegyezést az ügyfél és a Microsoft Graph között. Az ügyfél regisztrálva van az engedélyekhez `user.read` és a `contacts.read` . A Azure Key Vault hatókörében is regisztrálva van `https://vault.azure.net/user_impersonation` . 

Amikor az ügyfél jogkivonatot kér a `scope=https://graph.microsoft.com/.default` alkalmazáshoz, a felhasználó a `user.read` hatókör, a `contacts.read` hatókör és a Key Vault `user_impersonation` hatókörökhöz tartozó beleegyező lapot láthat. A visszaadott jogkivonat csak a `user.read` és a `contacts.read` hatóköröket tartalmazza. Csak Microsoft Graph esetén használható.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>3. példa: a felhasználó beleegyezett, és az ügyfél több hatókört kér

Ebben a példában a felhasználó már beleegyezett az `mail.read` ügyfélhez. Az ügyfél regisztrálva van a `contacts.read` hatókörben. 

Ha az ügyfél egy jogkivonatot kér a használatával `scope=https://graph.microsoft.com/.default` , és a kérést kéri `prompt=consent` , a felhasználó az alkalmazás által regisztrált engedélyekhez (és csak) egy beleegyező lapot lát. A `contacts.read` hatókör a beleegyező oldalon található, de `mail.read` nem. A visszaadott jogkivonat Microsoft Graph. Tartalmaz `mail.read` és `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>A/.default hatókör használata az ügyféllel

Bizonyos esetekben előfordulhat, hogy az ügyfél kérheti a saját `/.default` hatókörét. A következő példa bemutatja ezt a forgatókönyvet.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Ez a kódrészlet egy beleegyező oldalt hoz létre az összes regisztrált engedélyhez, ha az előzőekben ismertetett beleegyezikés és `/.default` a forgatókönyvre vonatkozik. Ezt követően a kód egy `id_token` hozzáférési jogkivonat helyett egy értéket ad vissza.  

Ez a viselkedés az Azure AD Authentication Library (ADAL) és a Microsoft Authentication Library (MSAL) között áthelyezett örökölt ügyfeleket is tartalmazza. Ezt a *beállítást nem szabad olyan* új ügyfelek használni, amelyek a Microsoft Identity platformot célozzák meg.

### <a name="client-credentials-grant-flow-and-default"></a>Az ügyfél hitelesítő adatai biztosítják a folyamatot és a/.default  

A egy másik használata az `/.default` alkalmazás engedélyeinek (vagy *szerepköreinek*) kérése egy nem interaktív alkalmazásban, például egy olyan Daemon-alkalmazás, amely az [ügyfél hitelesítő adatait](v2-oauth2-client-creds-grant-flow.md) használja, és meghívja a webes API-t.

Ha alkalmazás-engedélyeket (szerepköröket) szeretne létrehozni egy webes API-hoz, tekintse [meg az alkalmazás szerepköreinek hozzáadása az alkalmazásban](howto-add-app-roles-in-azure-ad-apps.md)című témakört.

Az ügyfélhez tartozó hitelesítő adatokra vonatkozó kérelmeknek szerepelniük *kell* a alkalmazásban `scope={resource}/.default` . Itt `{resource}` látható az alkalmazás által hívni kívánt webes API. Az ügyfél-hitelesítő adatokra vonatkozó kérelem egyedi alkalmazás-engedélyek (szerepkörök) használatával történő kiállítása *nem* támogatott. Az adott webes API-hoz megadott összes alkalmazás-engedély (szerepkör) a visszaadott hozzáférési jogkivonat részét képezi.

Ha hozzáférést szeretne adni a megadott alkalmazás-engedélyekhez, beleértve az alkalmazáshoz való rendszergazdai jóváhagyást is, tekintse meg az [ügyfélalkalmazás konfigurálása webes API eléréséhez](quickstart-configure-app-access-web-apis.md)című témakört.

### <a name="trailing-slash-and-default"></a>Záró perjel és/.default

Egyes erőforrás-URI-k záró perjeltel rendelkeznek, például a `https://contoso.com/` helyett `https://contoso.com` . A záró perjel problémákhoz vezethet a jogkivonat-ellenőrzés során. A probléma elsősorban akkor fordul elő, ha Azure Resource Manager () jogkivonatot kér `https://management.azure.com/` . Ebben az esetben az erőforrás URI azonosítójának záró perjele azt jelenti, hogy a perjelnek jelen kell lennie a jogkivonat kérése esetén.  Tehát amikor jogkivonatot kér `https://management.azure.com/` és használ `/.default` , meg kell kérnie `https://management.azure.com//.default` (figyelje meg a dupla perjelet!). Általánosságban elmondható, hogy ha ellenőrzi, hogy a jogkivonat ki van-e állítva, és ha a jogkivonatot el szeretné utasítani az API-nak, hogy el kellene fogadnia, vegye fontolóra egy második perjel hozzáadását, és próbálkozzon újra. 

## <a name="troubleshooting-permissions-and-consent"></a>Hibaelhárítási engedélyek és beleegyezett

Hibaelhárítási lépések: [váratlan hiba történt az alkalmazáshoz való belelépéskor](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Következő lépések

* [AZONOSÍTÓ tokenek a Microsoft Identity platformon](id-tokens.md)
* [Hozzáférési tokenek a Microsoft Identity platformon](access-tokens.md)
