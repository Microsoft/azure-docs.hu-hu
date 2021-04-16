---
title: A Microsoft identitásplatform-hatókörei, engedélyei és & hozzájárulás
description: Ismerje meg az engedélyezést a Microsoft identitásplatformjának végpontján, beleértve a hatókörök, engedélyek és jóváhagyások használatát.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2a975a0aba06ecfd010fe328ef6c8cda75290f2b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515583"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Engedélyek és jóváhagyás a Microsoft identitásplatformján

A Microsoft identitásplatformmal integrált alkalmazások engedélyezési modellt követnek, amely lehetővé teszi a felhasználók és a rendszergazdák számára az adatokhoz való hozzáférés szabályozását. Az engedélyezési modell implementációja frissült a Microsoft identitásplatformján. Megváltoztatja, hogy az alkalmazások hogyan kommunikálhatnak a Microsoft identitásplatformmal. Ez a cikk az engedélyezési modell alapvető fogalmait, beleértve a hatókörök, az engedélyek és a hozzájárulás fogalmát is.

## <a name="scopes-and-permissions"></a>Hatókörök és engedélyek

A Microsoft identitásplatformja az [OAuth 2.0 engedélyezési protokollt](active-directory-v2-protocols.md) valósítja meg. Az OAuth 2.0 egy olyan módszer, amellyel egy külső alkalmazás hozzáférhet a webes erőforrásokhoz egy felhasználó nevében. Minden, a Microsoft identitásplatformjával integrálható, webes erőforrás rendelkezik erőforrás-azonosítóval vagy alkalmazásazonosító *URI-val.* 

Íme néhány példa a Microsoft által üzemeltetett webes erőforrásokra:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 Mail API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Ugyanez vonatkozik a Microsoft identitásplatformmal integrált, harmadik féltől származó erőforrásokra is. Ezen erőforrások bármelyike meghatározhat engedélyeket, amelyek segítségével az erőforrás funkciói kisebb adattömbökre oszthatóak. Például a [Microsoft Graph](https://graph.microsoft.com) többek között a következő feladatok elvégzésére vonatkozó engedélyekkel rendelkezik:

* Felhasználó naptárának olvasása
* Írás egy felhasználó naptárába
* E-mail küldése felhasználóként

Az ilyen típusú engedélydefiníciók miatt az erőforrás az adatok és az API-funkciók elérhetővé tevésének finomhangolt szabályozásával rendelkezik. Egy külső alkalmazás kérheti ezeket az engedélyeket a felhasználóktól és a rendszergazdáktól, akiknek jóvá kell hagyniuk a kérést, mielőtt az alkalmazás hozzáférhetne az adatokhoz, vagy a felhasználó nevében cselekedhet. 

Ha egy erőforrás funkcióit kis engedélycsoportokra darablik, külső alkalmazások építhetőek ki úgy, hogy csak a funkciójuk elvégzéséhez szükséges engedélyeket kérjenek. A felhasználók és a rendszergazdák tudják, hogy milyen adatokhoz férhetnek hozzá az alkalmazás. Emellett biztosabb lehet abban, hogy az alkalmazás nem rosszindulatú szándékkal viselkedik. A fejlesztőknek mindig meg kell felelniük a legkisebb jogosultság elvének, és csak az alkalmazások működéséhez szükséges engedélyeket kell kérniük.

Az OAuth 2.0-ban ezeket az engedélykészleteket *hatóköröknek nevezzük.* Ezeket gyakran engedélyeknek *is nevezik.* A Microsoft identitásplatformján az engedélyek sztringértékként vannak ábrázolva. Az alkalmazás a lekérdezési paraméterben megadott engedélyekkel kéri le a `scope` szükséges engedélyeket. Az identitásplatform [](#openid-connect-scopes) számos jól meghatározott OpenID Connect hatókört és erőforrás-alapú engedélyt támogat (az egyes engedélyeket az engedély értékének az erőforrás azonosítójának vagy alkalmazásazonosító URI-jának hozzáfűzése jelzi). Az engedélysring például arra használható, hogy engedélyt kérjen a felhasználói naptárak `https://graph.microsoft.com/Calendars.Read` olvasásához a Microsoft Graph.

Az alkalmazások leggyakrabban úgy kérik ezeket az engedélyeket, hogy meghatározzák a hatókörök körét a Microsoft identitásplatformjának jogosultsági végpontjára vonatkozó kérelmekben. Bizonyos magas jogosultsági szintű engedélyek azonban csak rendszergazdai jóváhagyással adhatók meg. Ezek a rendszergazdai jóváhagyás végpontjával [kérhetőek vagy adhatók meg.](#admin-restricted-permissions) További információért olvass tovább.

## <a name="permission-types"></a>Engedélytípusok

A Microsoft identitásplatformja kétféle engedélyt támogat: *delegált* engedélyeket és *alkalmazásengedélyeket.*

* **A delegált engedélyeket** olyan alkalmazások használják, amelyek bejelentkezett felhasználóval vannak jelen. Ezekben az alkalmazásokban a felhasználó vagy egy rendszergazda elfogadja az alkalmazás által kért engedélyeket. Az alkalmazás delegált engedély arra, hogy bejelentkezett felhasználóként viselkedjen, amikor hívásokat kezdeményez a célerőforráshoz. 

    Egyes delegált engedélyeket nem rendszergazdák is hagyhatnak. Néhány magas jogosultsági szintű engedélyhez azonban rendszergazdai [jóváhagyás szükséges.](#admin-restricted-permissions) Ha meg szeretne tudni, hogy mely rendszergazdai szerepkörök járulnak hozzá a delegált engedélyekhez, tekintse meg a rendszergazdai szerepkör engedélyeit a Azure Active Directory [(Azure AD) szolgáltatásban.](../roles/permissions-reference.md)

* **Az alkalmazásengedélyeket** olyan alkalmazások használják, amelyek bejelentkezett felhasználó nélkül futnak, például háttérszolgáltatásként vagy démonként futó alkalmazások. Csak [rendszergazda járulhat hozzá az](#requesting-consent-for-an-entire-tenant) alkalmazásengedélyekhoz.

_A hatályos engedélyek_ azok az engedélyek, amelyekre az alkalmazásnak akkor van engedélye, amikor kéréseket ad a célerőforrásnak. Fontos megérteni, hogy mi a különbség a delegált engedélyek és az alkalmazásengedélyek között, amelyek meg vannak adni az alkalmazásnak, valamint hogy az alkalmazás milyen hatályos engedélyeket kap, amikor hívásokat kezdeményez a célerőforráshoz.

- Delegált engedélyeknél  az alkalmazás hatályos engedélyei az alkalmazás által (hozzájárulással) megadott delegált engedélyek és az aktuálisan bejelentkezett felhasználó jogosultságai legkevesebb jogosultsággal rendelkező metszetét metszete. Az alkalmazásnak soha nem lehet több jogosultsága, mint a bejelentkezett felhasználónak. 

   A szervezeteken belül a bejelentkezett felhasználó jogosultságait szabályzat vagy egy vagy több rendszergazdai szerepkör tagja határozza meg. Ha meg szeretne tudni, hogy mely rendszergazdai szerepkörök járulnak hozzá a delegált engedélyekhez, tekintse meg az Azure AD rendszergazdai szerepköreinek [engedélyeit.](../roles/permissions-reference.md)

   Tegyük fel például, hogy az alkalmazás _User.ReadWrite.All_ delegált engedélyt kapott. Ez az engedély névlegesen ad engedélyt az alkalmazás számára egy cégben lévő összes felhasználó profiljának olvasásához és frissítéséhez. Ha a bejelentkezett felhasználó globális rendszergazda, az alkalmazás a szervezet minden felhasználójának profilját frissítheti. Ha azonban a bejelentkezett felhasználó nem rendszergazdai szerepkört tölt be, az alkalmazás csak a bejelentkezett felhasználó profilját tudja frissíteni. Nem tudja frissíteni a szervezet más felhasználóinak profiljait, mert az a felhasználó, aki nevében jár el, nem rendelkezik ilyen jogosultságokkal.

- Alkalmazásengedélyek használata  során az alkalmazás hatályos engedélyei az engedély által hallgatólagos jogosultságok teljes szintjei. Például egy Olyan alkalmazás, amely _User.ReadWrite.All_ alkalmazásengedélysel rendelkezik, frissítheti a szervezet minden felhasználója profilját.

## <a name="openid-connect-scopes"></a>OpenID Connect hatókörök

A Microsoft identitásplatform-OpenID Connect néhány jól meghatározott hatókörrel rendelkezik, amelyek szintén a következő Microsoft Graph: `openid` , `email` , és `profile` `offline_access` . A `address` `phone` és OpenID Connect hatókörök nem támogatottak.

Ha a OpenID Connect és egy jogkivonatot kér, egy jogkivonatot fog kapni a [UserInfo végpont híváshoz.](userinfo.md)

### <a name="openid"></a>Openid

Ha egy alkalmazás a következővel [jelentkezik OpenID Connect,](active-directory-v2-protocols.md)akkor le kell kérnie a `openid` hatókört. A hatókör bejelentkezési engedélyként jelenik meg a munkahelyi fiók `openid` **hozzájárulási oldalán.** A személyes Microsoft-fiók jóváhagyás lapján megjelenik a Profil megtekintése, és csatlakozás alkalmazásokhoz és **szolgáltatásokhoz** a saját Microsoft-fiók használatával. 

Ezzel az engedéllyel az alkalmazás egyedi azonosítót kaphat a felhasználóhoz a jogcím `sub` formájában. Az engedély a UserInfo végponthoz is hozzáférést biztosít az alkalmazásnak. A `openid` hatókör a Microsoft identitásplatformjának jogkivonatvégpontján használható azonosító jogkivonatok lekért szerzéshez. Az alkalmazás használhatja ezeket a jogkivonatokat a hitelesítéshez.

### <a name="email"></a>e-mail

A `email` hatókör használható a hatókörrel és bármely más `openid` hatókörrel is. Hozzáférést biztosít az alkalmazásnak a felhasználó elsődleges e-mail-címéhez a jogcím `email` formájában. 

A `email` jogcím csak akkor szerepel a jogkivonatban, ha egy e-mail-cím van társítva a felhasználói fiókkal, ami nem mindig történik meg. Ha az alkalmazás a hatókört használja, az alkalmazásnak képesnek kell lennie kezelni egy olyan esetet, amelyben nincs jogcím a `email` `email` jogkivonatban.

### <a name="profile"></a>profil

A `profile` hatókör használható a hatókörrel és bármely más `openid` hatókörrel is. Hozzáférést biztosít az alkalmazás számára a felhasználóval kapcsolatos nagy mennyiségű információhoz. Az adatok, amelyekhez hozzáférhetnek, többek között a felhasználó utónevét, vezetéknevét, előnyben részesített felhasználónevét és objektumazonosítóját is tartalmazzák. 

Az adott felhasználó paraméterében elérhető jogcímek teljes listájáért tekintse meg `profile` `id_tokens` a hivatkozást. [ `id_tokens` ](id-tokens.md)

### <a name="offline_access"></a>offline_access

A [ `offline_access` hatókör](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) hosszabb ideig biztosít hozzáférést az alkalmazás számára az erőforrásokhoz a felhasználó nevében. A hozzájárulási lapon ez a hatókör a Hozzáférés fenntartása olyan adatokhoz, amelyekhez hozzáférést adott **neki.** 

Amikor egy felhasználó jóváhagyja a hatókört, az alkalmazás frissítési jogkivonatokat fogadhat a `offline_access` Microsoft identitásplatformjának jogkivonatvégponttól. A frissítési jogkivonatok hosszú életűek. Az alkalmazás új hozzáférési jogkivonatokat kap, amint a régebbiek lejárnak.

> [!NOTE]
> Ez az engedély jelenleg minden hozzájárulási oldalon megjelenik, még olyan folyamatoknál is, amelyek nem biztosítanak frissítési jogkivonatot (például [az implicit folyamatot).](v2-oauth2-implicit-grant-flow.md) Ez a beállítás olyan helyzetekkel foglalkozik, ahol az ügyfél az implicit folyamaton belül kezdődhet, majd továbbléphet arra a kódfolyamra, ahol frissítési jogkivonat várható.

A Microsoft identitásplatformján (a 2.0-s végpontra vonatkozó kérések) az alkalmazásnak explicit módon le kell kérnie a hatókört a frissítési `offline_access` jogkivonatok fogadásához. Így amikor az [OAuth 2.0](active-directory-v2-protocols.md)hitelesítési kódfolyamában bevált egy engedélyezési kódot, csak egy hozzáférési jogkivonatot kap a `/token` végponttól. 

A hozzáférési jogkivonat rövid ideig érvényes. Általában egy óra múlva lejár. Ezen a ponton az alkalmazásnak vissza kell irányítania a felhasználót a végpontra egy új engedélyezési `/authorize` kód le kéréséhez. Az átirányítás során az alkalmazás típusától függően előfordulhat, hogy a felhasználónak újra meg kell adnia a hitelesítő adatait, vagy ismét meg kell adnia az engedélyeket.

A frissítési jogkivonatok lekért és használatával kapcsolatos további információkért tekintse meg a [Microsoft identitásplatform-protokoll referenciáját.](active-directory-v2-protocols.md)

## <a name="requesting-individual-user-consent"></a>Egyéni felhasználói hozzájárulás kérése

Egy OpenID Connect [OAuth 2.0](active-directory-v2-protocols.md) hitelesítési kérelemben az alkalmazás a lekérdezési paraméter használatával kérheti a számára `scope` szükséges engedélyeket. Amikor például egy felhasználó bejelentkezik egy alkalmazásba, az alkalmazás az alábbi példához hasonló kérést küld. (Az olvashatóság érdekében sortörések vannak hozzáadva.)

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

A paraméter az alkalmazás által kért delegált engedélyek szóközrel `scope` elválasztott listája. Az egyes engedélyeket az erőforrás azonosítójának (az alkalmazásazonosító URI-jának) az engedélyértékhez való hozzáfűzése jelzi. A kérés példájában az alkalmazásnak engedéllyel kell rendelkeznie a felhasználó naptárának olvasására és e-mailek felhasználóként való elküldére.

Miután a felhasználó megadotta a hitelesítő adatait, a Microsoft identitásplatformja ellenőrzi a felhasználói hozzájárulás egyező *rekordját.* Ha a felhasználó korábban nem hagyta jóvá a kért engedélyeket, és a rendszergazda nem hagyta jóvá ezeket az engedélyeket a teljes szervezet nevében, a Microsoft identitásplatformja megkéri a felhasználót, hogy adja meg a kért engedélyeket.

Jelenleg a ("Hozzáférés fenntartása az adatokhoz, amelyekhez hozzáférést adott neki") engedély és a ("Bejelentkezés és a profil olvasása") engedély automatikusan szerepel az alkalmazás kezdeti `offline_access` `user.read` hozzájárulásában.  Ezek az engedélyek általában szükségesek a megfelelő alkalmazásfunkciókhoz. Az engedély hozzáférést biztosít az alkalmazásnak a natív alkalmazások és webalkalmazások számára kritikus jogkivonatok `offline_access` frissítéséhez. Az `user.read` engedély hozzáférést biztosít a `sub` jogcímhez. Lehetővé teszi az ügyfél vagy alkalmazás számára, hogy idővel helyesen azonosítsa a felhasználót, és hozzáfér a felhasználói adatokhoz.

![Példa képernyőkép a munkahelyi fiók hozzájárulásával.](./media/v2-permissions-and-consent/work_account_consent.png)

Amikor a felhasználó jóváhagyja az engedélykérést, a hozzájárulás rögzítve lesz. A felhasználónak nem kell újra hozzájárulni, amikor később bejelentkezik az alkalmazásba.

## <a name="requesting-consent-for-an-entire-tenant"></a>Hozzájárulás kérése egy teljes bérlőhöz

Amikor egy szervezet licencet vagy előfizetést vásárol egy alkalmazáshoz, a szervezet gyakran proaktív módon szeretné beállítani az alkalmazást a szervezet összes tagja számára. A folyamat részeként a rendszergazda engedélyt adhat arra, hogy az alkalmazás a bérlő bármely felhasználója nevében jár el. Ha a rendszergazda jóváhagyást ad a teljes bérlőhöz, a szervezet felhasználói nem látják az alkalmazáshoz a hozzájárulási oldalt.

Ha a bérlő összes felhasználója számára delegált engedélyekre vonatkozó jóváhagyást kér, az alkalmazás használhatja a rendszergazdai hozzájárulási végpontot.

Emellett az alkalmazásoknak a rendszergazdai hozzájárulási végpontot kell használniuk az alkalmazásengedélyek igényléséhez.

## <a name="admin-restricted-permissions"></a>Rendszergazda által korlátozott engedélyek

A Microsoft-erőforrások egyes magas szintű jogosultsági engedélyei beállíthatók rendszergazda *által korlátozottra.* Íme néhány példa az ilyen típusú engedélyekre:

* Az összes felhasználó teljes profiljának olvasása a használatával `User.Read.All`
* Adatok írása egy szervezet címtárába a használatával `Directory.ReadWrite.All`
* Egy szervezet címtárában található összes csoport olvasása a használatával `Groups.Read.All`

Bár a fogyasztói felhasználók hozzáférést adhatnak egy alkalmazásnak az ilyen típusú adatokhoz, a szervezeti felhasználók nem adhatnak hozzáférést ugyanannak a bizalmas vállalati adathalmaznak. Ha az alkalmazás hozzáférést kér ezen engedélyek egyikéhez egy szervezeti felhasználótól, a felhasználó egy hibaüzenetet kap, amely szerint nem jogosult az alkalmazás engedélyeinek igénylésére.

Ha az alkalmazásnak korlátozott rendszergazdai engedélyek hatókörére van szüksége, a szervezet rendszergazdájának jóvá kell azt mondania a szervezet felhasználói nevében. Annak érdekében, hogy az alkalmazás ne jelenítsen meg olyan engedélyekre vonatkozó kéréseket a felhasználók számára, akik nem adhatnak meg jóváhagyást, az alkalmazás használhatja a rendszergazdai hozzájárulási végpontot. A rendszergazdai jóváhagyás végpontját a következő szakasz tartalmazza.

Ha az alkalmazás magas jogosultságú delegált engedélyeket kér, és egy rendszergazda a rendszergazdai hozzájárulási végponton keresztül adja meg ezeket az engedélyeket, a rendszer a bérlő összes felhasználója számára jóváhagyást ad.

Ha az alkalmazás alkalmazásengedélyeket kér, és egy rendszergazda a rendszergazdai hozzájárulási végponton keresztül adja meg ezeket az engedélyeket, ez a jogosultság nem egy adott felhasználó nevében történik. Ehelyett az ügyfélalkalmazás közvetlenül kap *engedélyeket.* Az ilyen típusú engedélyeket csak démonszolgáltatások és más, a háttérben futó nem interaktív alkalmazások használják.

## <a name="using-the-admin-consent-endpoint"></a>A rendszergazdai hozzájárulási végpont használata

Miután a rendszergazdai hozzájárulási végpont használatával adta meg a rendszergazdai jóváhagyást, már készen is van. A felhasználóknak nincs szükségük további beavatkozásra. A rendszergazdai jóváhagyást követően a felhasználók egy tipikus hitelesítési folyamaton keresztül kaphatnak hozzáférési jogkivonatot. Az eredményül kapott hozzáférési jogkivonat rendelkezik a szükséges engedélyekkel.

Ha egy globális rendszergazda használja az alkalmazást, és a rendszer a jogosultsági végpontra irányítja, a Microsoft identitásplatformja észleli a felhasználó szerepkörét. Megkérdezi, hogy a globális rendszergazda a teljes bérlő nevében szeretne-e jóváhagyást adni a kért engedélyekhez. Ehelyett egy dedikált rendszergazdai hozzájárulási végponttal proaktív módon kérheti a rendszergazdát, hogy adja meg az engedélyt a teljes bérlő nevében. Ez a végpont az alkalmazásengedélyek igénylése során is szükséges. Az alkalmazásengedélyek nem kérhetőek az engedélyvégpont használatával.

Ha követi ezeket a lépéseket, az alkalmazás a bérlő összes felhasználója számára engedélyeket kérhet, beleértve a rendszergazda által korlátozott hatókörökét is. Ez a művelet magas szintű jogosultsággal rendelkezik. A műveletet csak akkor használja, ha szükséges a forgatókönyvhöz.

A lépéseket megvalósító kódminta a GitHub rendszergazda által korlátozott [hatókörök](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) mintáját tartalmazza.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Engedélyek kérése az alkalmazásregisztrációs portálon

Az alkalmazásregisztrációs portálon az alkalmazások listába sorolják a szükséges engedélyeket, beleértve a delegált és az alkalmazásengedélyeket is. Ez a beállítás lehetővé teszi a hatókör és a Azure Portal rendszergazdai hozzájárulás `/.default` **megadása beállítását.**  

Az engedélyeket általában statikusan kell meghatározni egy adott alkalmazáshoz. Az alkalmazás által dinamikusan vagy növekményesen lekért engedélyeket kell átfedni.

> [!NOTE]
>Az alkalmazásengedélyek csak a használatával [`/.default`](#the-default-scope) kérhetőek. Ha tehát az alkalmazásnak alkalmazásengedélyre van szüksége, ellenőrizze, hogy szerepel-e az alkalmazásregisztrációs portálon.

Egy alkalmazás statikusan kért engedélyeinek listájának konfigurálása:

1. Az alkalmazáshoz a Azure Portal <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">– Alkalmazásregisztrációk</a> útmutatóban.
1. Válasszon ki egy alkalmazást, vagy [hozzon létre egy alkalmazást,](quickstart-register-app.md) ha még nem.
1. Az alkalmazás Áttekintés **lapján,** a **Kezelés alatt** válassza az **API-engedélyek**  >  **Engedély hozzáadása lehetőséget.**
1. Az **Microsoft Graph** API-k listájából válassza az Alkalmazásokat. Ezután adja hozzá az alkalmazáshoz szükséges engedélyeket.
1. Válassza **az Engedélyek hozzáadása lehetőséget.**

### <a name="recommended-sign-the-user-in-to-your-app"></a>Ajánlott: A felhasználó bejelentkeztetése az alkalmazásba

Amikor olyan alkalmazást hoz létre, amely a rendszergazdai hozzájárulási végpontot használja, az alkalmazásnak általában szüksége van egy lapra vagy nézetre, amelyen a rendszergazda jóváhagyhatja az alkalmazás engedélyét. Ez az oldal a következő lehet:

* Az alkalmazás bejelentkezési folyamatának része.
* Az alkalmazás beállításainak egy része.
* Egy dedikált "csatlakozás" folyamat. 

Sok esetben logikus, hogy az alkalmazás ezt a "kapcsolódási" nézetet csak akkor mutassa, ha egy felhasználó munkahelyi vagy iskolai Microsoft-fiók bejelentkezett Microsoft-fiók.

Amikor bejelentkezik a felhasználóval az alkalmazásba, azonosíthatja a szervezetet, amelyhez a rendszergazda tartozik, mielőtt megkéri, hogy hagyja jóvá a szükséges engedélyeket. Bár ez a lépés nem feltétlenül szükséges, segít intuitívabb felhasználói élményt létrehozni a szervezeti felhasználók számára. 

A felhasználó bejelentkezéshez kövesse a [Microsoft identitásplatform-protokoll oktatóanyagát.](active-directory-v2-protocols.md)

### <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése egy címtár-rendszergazdától

Ha készen áll arra, hogy engedélyeket kérjen a szervezet rendszergazdájatól, átirányíthatja a felhasználót a Microsoft identitásplatformjának rendszergazdai hozzájárulási végpontjára.

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
| `tenant` | Kötelező | Az a címtárbérlő, amelytől engedélyeket szeretne kérni. Guid vagy rövid név formátumban is meg lehet adni. Vagy általánosan hivatkozhat a szervezetekre, ahogy az a példában is látható. Ne használja a "common" (gyakori) lehetőséget, mert a személyes fiókok csak a bérlők kontextusában tudnak rendszergazdai jóváhagyást adni. A bérlőket kezelő személyes fiókokkal való lehető legjobb kompatibilitás érdekében használja a bérlőazonosítót, ha lehetséges. |
| `client_id` | Kötelező | Az alkalmazás (ügyfél) azonosítója, Azure Portal [– Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) alkalmazáshoz rendelt felhasználói élményt. |
| `redirect_uri` | Kötelező |Az átirányítási URI, ahová el szeretné küldeni a választ, hogy az alkalmazás kezelni tudja. Pontosan meg kell egyeznie az alkalmazásregisztrációs portálon regisztrált átirányítási URI-k egyikének. |
| `state` | Ajánlott | A kérésben szereplő érték, amely szintén a jogkivonat válaszában lesz visszaadva. Ez bármilyen tartalom sztringe lehet. Az állapot használatával kódoljuk a felhasználó alkalmazásbeli állapotára vonatkozó információkat a hitelesítési kérelem előtt, például azt az oldalt vagy nézetet, ahol a felhasználó jelen volt. |
|`scope`        | Kötelező        | Meghatározza az alkalmazás által kért engedélyeket. A hatókörök statikusak (a használatával) vagy [`/.default`](#the-default-scope) dinamikusak is lehetnek.  Ez a készlet tartalmazhatja a OpenID Connect ( `openid` , `profile` , `email` ). Ha alkalmazásengedélyre van szüksége, a használatával kell lekérte a statikusan konfigurált engedélyek `/.default` listáját.  |


Az Azure AD-nek ezen a ponton be kell jelentkeznie egy bérlői rendszergazdára a kérés befejezéséhez. A rendszer megkéri a rendszergazdát, hogy hagyja jóvá a paraméterben kért összes `scope` engedélyt.  Ha statikus ( ) értéket használt, az az 1.0-s verziójának rendszergazdai hozzájárulási végpontjaként fog működni, és jóváhagyást kér az alkalmazáshoz szükséges engedélyekben található összes `/.default` hatókörre.

#### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyét, a sikeres válasz a következő lesz:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | Az a címtárbérlő, amely GUID formátumban megadta az alkalmazásnak a kért engedélyeket. |
| `state` | A kérésben szereplő érték, amely szintén a jogkivonat válaszában lesz visszaadva. Ez bármilyen tartalom sztringe lehet. A rendszer az állapot használatával kódolja a felhasználó alkalmazásbeli állapotára vonatkozó információkat a hitelesítési kérelem beeste előtt, például az oldalt vagy a megtekintést, ahol voltak. |
| `admin_consent` | A beállítás a `True` lesz. |

#### <a name="error-response"></a>Hibaválasz

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyét, a sikertelen válasz a következő lesz:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-sztring, amely a előforduló hibák típusainak besorolására használható. Arra is használható, hogy reagáljon a hibákra. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a fejlesztőnek azonosítani a hiba kiváltó okát. |

Miután sikeresen kapott választ a rendszergazdai hozzájárulási végponttól, az alkalmazás megkapta a kért engedélyeket. Ezután kérhet egy jogkivonatot a kívánt erőforráshoz.

## <a name="using-permissions"></a>Engedélyek használata

Miután a felhasználó hozzájárul az alkalmazás engedélyeinek megszerzéséhez, az alkalmazás hozzáférési jogkivonatokat szerezhet be, amelyek az alkalmazásnak egy adott kapacitásban az erőforrások elérésére vonatkozó engedélyét képviselik. A hozzáférési jogkivonatok csak egyetlen erőforráshoz használhatók. A hozzáférési jogkivonatban kódolt minden olyan engedély, amely az alkalmazás számára az adott erőforráshoz hozzá lett adták. Hozzáférési jogkivonat lekéréséhez az alkalmazás az alábbihoz hasonló kérést tud tenni a Microsoft identitásplatform jogkivonatának végpontjára:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Az eredményül kapott hozzáférési jogkivonatot használhatja az erőforrásnak adott HTTP-kérésben. Megbízhatóan jelzi az erőforrásnak, hogy az alkalmazás megfelelő engedéllyel rendelkezik egy adott feladat elvégzéséhez.

Az OAuth 2.0 protokollról és a hozzáférési jogkivonatok lekért verziójáról további információt a [Microsoft identitásplatformjának végponti protokolljának referenciája tartalmaz.](active-directory-v2-protocols.md)

## <a name="the-default-scope"></a>A /.default hatókör

A hatókör használatával alkalmazásait az 1.0-s verziójának végpontról a `/.default` Microsoft identitásplatform-végpontjára miggálhatja. A hatókör minden olyan alkalmazáshoz be van építve, amely az alkalmazásregisztrációban konfigurált engedélyek statikus `/.default` listájára hivatkozik. 

A `scope` értéke `https://graph.microsoft.com/.default` funkcionálisan megegyezik az `resource=https://graph.microsoft.com` 1.0-s végpont értékével. A hatókör kérésben való megadásával az alkalmazás olyan hozzáférési jogkivonatot kér, amely az alkalmazáshoz az alkalmazásregisztrációs portálon kiválasztott Microsoft Graph-engedélyek hatókörét `https://graph.microsoft.com/.default` tartalmazza. A hatókör az erőforrás URI-ját és a használatával áll `/.default` össze. Tehát ha az erőforrás URI-ja `https://contosoApp.com` , akkor a kért hatókör a `https://contosoApp.com/.default` következő: .  Ha a jogkivonat helyes lekérése érdekében egy második perjelet is be kell foglalnia, tekintse meg a záró perjelekkel kapcsolatos [szakaszt.](#trailing-slash-and-default)

A `/.default` hatókör bármely OAuth 2.0-folyamatban használható. Erre azonban szükség van [](v2-oauth2-on-behalf-of-flow.md) a Meghatalmazásos folyamat és az ügyfél-hitelesítő [adatok folyamatában.](v2-oauth2-client-creds-grant-flow.md) Akkor is szüksége lesz rá, ha a v2 rendszergazdai hozzájárulási végpontot használja az alkalmazásengedélyek igénylésére.

Az ügyfelek nem kombinálják a statikus ( ) hozzájárulásokat és `/.default` a dinamikus hozzájárulásokat egyetlen kérésben. Ezért ez hibát jelez, mert a `scope=https://graph.microsoft.com/.default+mail.read` hatókörtípusokat kombinálja.

### <a name="default-and-consent"></a>/.default és hozzájárulás

A `/.default` hatókör aktiválja az 1.0-s végpont viselkedését `prompt=consent` is. Az alkalmazás által regisztrált összes engedélyhez jóváhagyást kér, az erőforrástól függetlenül. Ha a kérelem része, a hatókör egy jogkivonatot ad vissza, amely tartalmazza a kért erőforrás `/.default` hatókörét.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, ha a felhasználó már hozzájárulását adta

A `/.default` hatókör funkcionálisan megegyezik a `resource` -központú v1.0-végpont viselkedésével. A v1.0-végpont hozzájárulási viselkedését is tartalmazza. Ez azt jelenti, hogy csak akkor aktivál egy hozzájárulási kérést, ha a felhasználó nem adott engedélyt az ügyfél és az `/.default` erőforrás között. 

Ha létezik ilyen jóváhagyás, a visszaadott jogkivonat tartalmazza az adott erőforráshoz a felhasználó által megadott összes hatókört. Ha azonban nem kapott engedélyt, vagy ha a paraméter meg lett adva, az ügyfélalkalmazás által regisztrált összes hatókörre jóváhagyást kér `prompt=consent` a rendszer.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>1. példa: A felhasználó vagy bérlői rendszergazda engedélyeket adott

Ebben a példában a felhasználó vagy egy bérlői rendszergazda megadta a és Microsoft Graph `mail.read` `user.read` engedélyeket az ügyfélnek. 

Ha az ügyfél a kérést kéri, a rendszer nem kér jóváhagyást, függetlenül attól, hogy az ügyfélalkalmazás milyen tartalmakkal rendelkezik a `scope=https://graph.microsoft.com/.default` Microsoft Graph. A visszaadott jogkivonat tartalmazza a és a `mail.read` `user.read` hatókört.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>2. példa: A felhasználó nem adott engedélyeket az ügyfél és az erőforrás között

Ebben a példában a felhasználó nem adott jóváhagyást az ügyfél és a Microsoft Graph. Az ügyfél regisztrált a és a `user.read` `contacts.read` engedélyre. Emellett regisztrálva van a Azure Key Vault `https://vault.azure.net/user_impersonation` hatókörben. 

Amikor az ügyfél jogkivonatot kér a számára, a felhasználó egy hozzájárulási oldalt lát a hatókörre, a hatókörre és a Key Vault `scope=https://graph.microsoft.com/.default` `user.read` `contacts.read` `user_impersonation` hatókörre. A visszaadott jogkivonat csak a és a `user.read` `contacts.read` hatókört tartalmazza. Csak a Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>3. példa: A felhasználó hozzájárult, és az ügyfél több hatókört kér

Ebben a példában a felhasználó már hozzájárult az `mail.read` ügyfélhez. Az ügyfél regisztrált a `contacts.read` hatókörre. 

Amikor az ügyfél jogkivonatot kér a használatával, és a használatával kér jóváhagyást, a felhasználó az alkalmazás által regisztrált összes (és csak) engedélyhez egy hozzájárulási `scope=https://graph.microsoft.com/.default` `prompt=consent` oldalt lát. A `contacts.read` hatókör a hozzájárulási oldalon található, `mail.read` de nem az. A visszaadott jogkivonat a Microsoft Graph. A és a `mail.read` adatokat `contacts.read` tartalmazza.

### <a name="using-the-default-scope-with-the-client"></a>A /.default hatókör használata az ügyféllel

Bizonyos esetekben az ügyfelek saját hatókört `/.default` kérhetnek. Az alábbi példa ezt a forgatókönyvet mutatja be.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Ez a példakód létrehoz egy jóváhagyási oldalt az összes regisztrált engedélyhez, ha a hozzájárulás fenti leírásai és `/.default` a forgatókönyvre vonatkoznak. Ezután a kód egy `id_token` , és nem egy hozzáférési jogkivonatot ad vissza.  

Ez a viselkedés az Azure AD Authentication Libraryről (ADAL) a Microsoft Authentication Libraryre (MSAL) való áthelyezésre használt régebbi ügyfeleket is befogadja. Ezt *a beállítást nem szabad* használni a Microsoft identitásplatformját megcélzott új ügyfelek számára.

### <a name="client-credentials-grant-flow-and-default"></a>Ügyfél-hitelesítő adatok megadása folyamat és /.default  

Egy másik felhasználási lehetőség az alkalmazásengedélyek (vagy szerepkörök) kérése egy nem interaktív alkalmazásban, például egy démonalkalmazásban, amely az ügyfél hitelesítő adatait használó folyamat segítségével hív meg egy `/.default` webes API-t.  [](v2-oauth2-client-creds-grant-flow.md)

A webes API-k alkalmazásengedélyeinek (szerepköreinek) létrehozásához lásd: [Alkalmazásszerepk szerepkörök](howto-add-app-roles-in-azure-ad-apps.md)hozzáadása az alkalmazásban.

Az ügyfél-hitelesítő adatokra vonatkozó kérések az ügyfélalkalmazásban tartalmazzák *a* következőt: `scope={resource}/.default` . Itt az a webes API, amit az alkalmazás `{resource}` meg kíván hívni. Az ügyfél-hitelesítő adatok kérésének egyéni alkalmazásengedélyekkel (szerepkörökkel) történő kiadása *nem* támogatott. A visszaadott hozzáférési jogkivonat tartalmazza az adott webes API-hoz megadott összes alkalmazásengedélyt (szerepkört).

Ha hozzáférést ad a megadott alkalmazásengedélyek számára, beleértve a rendszergazdai hozzájárulás megadását az alkalmazáshoz, lásd: Ügyfélalkalmazás konfigurálása webes [API-k elérésére.](quickstart-configure-app-access-web-apis.md)

### <a name="trailing-slash-and-default"></a>Záró perjel és /.default

Egyes erőforrás-URI-k záró perjellel( ) vannak, nem pedig `https://contoso.com/` a `https://contoso.com` következővel: . A záró perjel problémákat okozhat a jogkivonat érvényesítésével kapcsolatban. A problémák elsősorban akkor fordulnak elő, ha jogkivonatot Azure Resource Manager ( `https://management.azure.com/` ). Ebben az esetben az erőforrás URI-ján záró perjel azt jelenti, hogy a perjelnek jelen kell lennie a jogkivonat lekérésekor.  Így amikor jogkivonatot kér a számára, és a használatát használja, le kell kérnie `https://management.azure.com/` `/.default` (figyelje meg a kettős `https://management.azure.com//.default` perjelet!). Ha általában ellenőrzi, hogy a jogkivonat ki van-e bocsátva, és az API elutasítja a jogkivonatot, amely elfogadja azt, érdemes lehet hozzáadni egy újabb perjelet, és újra próbálkozni. 

## <a name="troubleshooting-permissions-and-consent"></a>Engedélyek és hozzájárulás hibaelhárítása

Hibaelhárítási lépésekért lásd: Váratlan hiba egy alkalmazás [hozzájárulásának végrehajtásakor.](../manage-apps/application-sign-in-unexpected-user-consent-error.md)

## <a name="next-steps"></a>Következő lépések

* [Azonosító jogkivonatok a Microsoft identitásplatformján](id-tokens.md)
* [Hozzáférési jogkivonatok a Microsoft identitásplatformján](access-tokens.md)
