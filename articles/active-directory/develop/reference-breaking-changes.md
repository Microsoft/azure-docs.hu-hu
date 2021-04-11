---
title: Azure Active Directory feltörési változások referenciája
description: Ismerje meg az Azure AD protokollok változásait, amelyek hatással lehetnek az alkalmazásra.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 3/30/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: eb75450527fc31d6ea4a9f9d60d676718ad79bda
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167583"
---
# <a name="whats-new-for-authentication"></a>A hitelesítés újdonságai

> Az alábbi URL-cím RSS-hírcsatorna-olvasóba való beillesztésével értesítést kaphat az oldal frissítéseiről:<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

A hitelesítési rendszer folyamatosan módosítja és hozzáadja a szolgáltatásokat a biztonság és a szabványok megfelelőségének javítása érdekében. A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő részletekkel kapcsolatos információkat tartalmazza:

- A legújabb funkciók
- Ismert problémák
- Protokoll módosításai
- Elavult funkciók

> [!TIP]
> Ez az oldal rendszeresen frissül, ezért gyakran látogatnak. Hacsak másként nincs jelezve, ezek a módosítások csak az újonnan regisztrált alkalmazások esetében kerülnek érvénybe.

## <a name="upcoming-changes"></a>Közelgő változások

### <a name="bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice"></a>Hibajavítás: az Azure AD már nem fogja kétszer kódolni az állapot paramétert.

Hatálybalépés **dátuma**: május 2021

Érintett **végpontok**: v 1.0 és v 2.0 

Érintett **protokoll**: minden olyan folyamat, amely felkeresi a `/authorize` végpontot (implicit flow és engedélyezési kód flow)

A rendszer hibát talált az Azure AD-engedélyezési válaszban. A `/authorize` hitelesítés során a `state` kérésben szereplő paraméter szerepel a válaszban, hogy megőrizze az alkalmazás állapotát, és segít megelőzni a CSRF támadásokat. Az Azure AD helytelenül kódolt URL-címet `state` kapott a paraméternek a válaszba való behelyezése előtt, ahol még egyszer kódolták.  Ez azt eredményezi, hogy az alkalmazások helytelenül elutasítják az Azure AD válaszát. 

Az Azure AD többé nem kódolja ezt a paramétert, így az alkalmazások helyesen tudják elemezni az eredményt. Ezt a módosítást minden alkalmazás esetében el kell végezni. 

### <a name="conditional-access-will-only-trigger-for-explicitly-requested-scopes"></a>A feltételes hozzáférés csak explicit módon kért hatókörök esetén aktiválódik

**Hatályba lépés**: május 2021., fokozatos bevezetéssel, áprilistól kezdődően. 

Érintett **végpontok**: v 2.0

Érintett **protokoll**: a [dinamikus beleegyezett](v2-permissions-and-consent.md#requesting-individual-user-consent)

A dinamikus megadást használó alkalmazások az összes engedélyt megkapják, még akkor is, ha a `scope` paraméter név szerint nem kérték őket.  Ennek hatására előfordulhat, hogy egy alkalmazás például csak a-t `user.read` , de beleegyezéssel kéri `files.read` az engedélyhez rendelt feltételes hozzáférés továbbítására `files.read` . 

A szükségtelen feltételes hozzáférési kérések számának csökkentése érdekében az Azure AD megváltoztatja a nem kérelmezett hatóköröket az alkalmazásoknak, így csak a explicit módon kért hatókörök váltanak ki feltételes hozzáférést. Ez a változás azt eredményezheti, hogy az alkalmazások az Azure AD korábbi viselkedésére támaszkodnak (azaz ha nem kérték fel az összes engedélyt), mert a kért jogkivonatok hiányoznak az engedélyek.

Az alkalmazások mostantól megkapják a hozzáférési jogkivonatokat, amelyek a kért, valamint a feltételes hozzáférési kérésekhez nem szükséges engedélyekkel rendelkeznek.  A hozzáférési jogkivonat hatóköre megjelenik a jogkivonat-válasz `scope` paraméterében. 

Ez a módosítás az összes alkalmazásra vonatkozik, kivéve azokat, amelyek megfigyelt függőséggel rendelkeznek.  A fejlesztők a jelen változás alól mentesülnek, mivel a további feltételes hozzáférési kérések függőségi viszonyba kerülhetnek. 

**Példák**

Az alkalmazáshoz a, `user.read` a `files.readwrite` és a `tasks.read` . `files.readwrite` alkalmazza a feltételes hozzáférési szabályzatokat, míg a másik kettő nem. Ha egy alkalmazás jogkivonat-kérelmet küld a számára `scope=user.read` , és a jelenleg bejelentkezett felhasználó nem adott meg semmilyen feltételes hozzáférési szabályzatot, akkor az eredményül kapott jogkivonat lesz a `user.read` és az `tasks.read` engedély. `tasks.read` belefoglalja, mert az alkalmazás beleegyezik a hozzá, és nem igényli feltételes hozzáférési szabályzat érvényesítését. 

Ha az alkalmazás ezután kéri `scope=files.readwrite` , a bérlő által igényelt feltételes hozzáférés aktiválódik, és arra kényszeríti az alkalmazást, hogy megjelenítse az interaktív hitelesítési kérést, ahol a feltételes hozzáférési házirend teljesül.  A visszaadott token mindhárom hatókört tartalmazni fog. 

Ha az alkalmazás ezt követően egy utolsó kérelmet hajt végre a három hatókör bármelyikéhez (például `scope=tasks.read` :), az Azure ad látni fogja, hogy a felhasználó már végrehajtotta a szükséges feltételes hozzáférési szabályzatokat `files.readwrite` , és ismét kiállít egy jogkivonatot, amely mindhárom engedélyekkel rendelkezik. 


## <a name="may-2020"></a>2020. május

### <a name="azure-government-endpoints-are-changing"></a>Azure Government végpontok változnak

Hatálybalépés **dátuma**: május 5. (Befejezés 2020. június) 

Érintett **végpontok**: az összes

Érintett **protokoll**: minden folyamat

2018. június 1-jén a Azure Government hivatalos Azure Active Directory (HRE) hatósága a verzióról a verzióra változott `https://login-us.microsoftonline.com` `https://login.microsoftonline.us` . Ez a változás a Microsoft 365 GCC High és a DoD szolgáltatásra is vonatkozik, amely Azure Government HRE is. Ha egy Egyesült államokbeli kormányzati bérlőn belül egy alkalmazás tulajdonosa, akkor frissítenie kell az alkalmazást, hogy aláírja a felhasználókat a `.us` végponton.  

Május 5. után az Azure AD megkezdi a végpont változásának érvényesítését, és blokkolja a kormányzati felhasználókat a nyilvános végpont () használatával az USA kormányzati bérlői által üzemeltetett alkalmazásokba való bejelentkezéskor `microsoftonline.com` .  Az érintett alkalmazások megkezdik a hibát `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` . Ez a hiba azt jelzi, hogy az alkalmazás egy Egyesült államokbeli kormányzati felhasználót próbál bejelentkezni a nyilvános Felhőbeli végponton. Ha az alkalmazás egy nyilvános Felhőbeli bérlőn van, és az USA kormányzati felhasználóinak támogatására szolgál, [frissítenie kell az alkalmazást, hogy explicit módon támogassa őket](./authentication-national-cloud.md). Ehhez szükség lehet új alkalmazás-regisztráció létrehozására az USA kormányzati felhőben. 

Ennek a változásnak a kényszerítése egy fokozatos bevezetéssel történik, attól függően, hogy az Egyesült államokbeli kormányzati Felhőbeli felhasználók milyen gyakran jelentkeznek be az USA kormányzati felhasználóinak az alkalmazásba való bejelentkezéshez. A kényszerítést arra várjuk, hogy az összes alkalmazásban az 2020-es verzióban befejeződjön. 

További részletekért tekintse meg az [áttelepítés Azure Government blogbejegyzését](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/). 

## <a name="march-2020"></a>2020. március

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>A felhasználói jelszavak 256 karakterre lesznek korlátozva.

Hatálybalépés **dátuma**: március 13., 2020

Érintett **végpontok**: az összes

Érintett **protokoll**: minden felhasználói folyamat.

Az 256 karakternél hosszabb jelszavakkal rendelkező felhasználók, amelyek közvetlenül az Azure AD-be jelentkeznek be (ellentétben az összevont IDENTITÁSSZOLGÁLTATÓ, például az ADFS-vel), nem tudnak bejelentkezni a 2020. március 13-án kezdődően, és a rendszer kéri a jelszó alaphelyzetbe állítását.  Előfordulhat, hogy a rendszergazdák a felhasználók jelszavának visszaállítására vonatkozó kéréseket kapnak.

A bejelentkezési naplók hibája a következő lesz: AADSTS 50052: InvalidPasswordExceedsMaxLength

Üzenetet `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Szervizkiszolgáló

A felhasználó nem tud bejelentkezni, mert a jelszava meghaladja a megengedett maximális hosszt. A jelszó alaphelyzetbe állításához forduljon a rendszergazdához. Ha a SSPR engedélyezve van a bérlője számára, a jelszót a "Elfelejtett jelszó" hivatkozásra kattintva állíthatja alaphelyzetbe.



## <a name="february-2020"></a>2020. február

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>A rendszer az összes HTTP-átirányításhoz hozzáfűzi az üres töredékeket a bejelentkezési végpontról.

Hatálybalépés **dátuma**: február 8., 2020

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: response_type = lekérdezést használó OAuth-és OIDC-folyamatok, amelyek bizonyos esetekben az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) , valamint az [implicit folyamatot](v2-oauth2-implicit-grant-flow.md)fedik le.

Ha a login.microsoftonline.com egy alkalmazáshoz HTTP-átirányítás útján küldenek hitelesítési választ, a szolgáltatás hozzáfűz egy üres töredéket a válasz URL-címéhez.  Ezzel megakadályozhatja, hogy a böngésző kitörölje a hitelesítési kérelemben szereplő meglévő töredékeket.  Ehhez a viselkedéshez egyetlen alkalmazásnak sincs függősége.


## <a name="august-2019"></a>2019. augusztus

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>A POST Form szemantikai érvényesítése szigorúbb lesz, és a rendszer figyelmen kívül hagyja az idézőjeleket

Hatálybalépés **dátuma**: szeptember 2., 2019

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: bárhol a post használatban van ([ügyfél-hitelesítő adatok](./v2-oauth2-client-creds-grant-flow.md), [engedélyezési kód beváltása](./v2-oauth2-auth-code-flow.md), [ROPC](./v2-oauth-ropc.md), [OBO](./v2-oauth2-on-behalf-of-flow.md)és [frissítési jogkivonat beváltása](./v2-oauth2-auth-code-flow.md#refresh-the-access-token))

A 9/2-as hét elindításával a POST metódust használó hitelesítési kérelmek szigorúbb HTTP-szabványokkal lesznek érvényesítve.  A szóközök és a kettős idézőjelek (") nem lesznek eltávolítva a kérelmek űrlapjának értékeiből. Ezeknek a változásoknak nem kell megszüntetniük a meglévő ügyfeleket, és biztosítaniuk kell, hogy az Azure AD-nek küldött kérelmeket minden alkalommal megbízhatóan kezelje a rendszer. A jövőben (lásd fent) Azt tervezzük, hogy az ismétlődő paramétereket is elutasítjuk, és figyelmen kívül hagyják az ANYAGJEGYZÉKet a kérések között.

Példa:

Napjainkban a `?e=    "f"&g=h` értelmezése azonos módon történik `?e=f&g=h` `e`  ==  `f` .  Ennek a változásnak az értelmezése most már nem valószínű, hogy `e`  ==  `    "f"` Ez egy érvényes argumentum, és a kérés sikertelen lesz.


## <a name="july-2019"></a>2019. július

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Csak az egybérlős alkalmazásokhoz tartozó jogkivonatok jelennek meg, ha az ügyfélalkalmazás létezik az erőforrás-bérlőben

**Hatálybalépés dátuma**: július 26., 2019

Érintett **végpontok**: [v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) és [v 2.0](./v2-oauth2-client-creds-grant-flow.md)

Érintett **protokoll**: [ügyfél-hitelesítő adatok (csak alkalmazásbeli tokenek)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

A biztonsági változások élőben július 26-án mentek át, és az alkalmazáshoz tartozó jogkivonatok (az ügyfél hitelesítő adatainak megadása révén) megváltoznak. Korábban az alkalmazások jogkivonatokat kérhetnek a más alkalmazások meghívására, függetlenül attól, hogy az adott alkalmazáshoz hozzájárultak-e a bérlő vagy a szerepkörök.  Ez a viselkedés úgy frissült, hogy az erőforrások (más néven webes API-k) egybérlős (alapértelmezett) legyenek, az ügyfélalkalmazás léteznie kell az erőforrás-bérlőn belül.  Vegye figyelembe, hogy az ügyfél és az API közötti meglévő hozzájárulás még nem szükséges, és az alkalmazásoknak továbbra is saját engedélyezési ellenőrzéseket kell végezniük annak biztosításához, hogy a `roles` jogcímek jelen legyenek, és tartalmazzák az API várt értékét.

A forgatókönyvhöz tartozó hibaüzenet jelenleg a következő állapotokat jelzi:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

A probléma megoldásához a rendszergazdai engedélyekkel hozza létre az ügyfélalkalmazás szolgáltatásnevet a bérlőben, vagy hozza létre manuálisan.  Ez a követelmény biztosítja, hogy a bérlő a bérlőn belüli működésre engedélyt kapjon az alkalmazás számára.

#### <a name="example-request"></a>Példakérelem

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` Ebben a példában az erőforrás-bérlő (szolgáltató) a contoso.com, az erőforrás-alkalmazás egy, a contoso-bérlőhöz nevű egybérlős alkalmazás `gateway.contoso.com/api` , és az ügyfélalkalmazás `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` .  Ha az ügyfélalkalmazás a Contoso.com-n belül rendelkezik egyszerű szolgáltatással, akkor a kérelem folytatódhat.  Ha azonban ez nem így van, a kérelem sikertelen lesz a fenti hibával.

Ha a contoso-átjáró alkalmazás több-bérlős alkalmazás volt, akkor a kérést a rendszer a Contoso.com-on belüli egyszerű szolgáltatástól függetlenül folytatja.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Az átirányítási URI-k mostantól tartalmazhatnak lekérdezési karakterlánc-paramétereket

**Hatálybalépés dátuma**: július 22., 2019

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: minden folyamat

Az [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)-ben az Azure ad-alkalmazások mostantól regisztrálhatják és használhatják az átirányítási (válasz) URI-ket statikus lekérdezési paraméterekkel (például `https://contoso.com/oauth2?idp=microsoft` ) a OAuth 2,0-kérelmek esetében.  A dinamikus átirányítási URI-k továbbra is tiltottak, mivel biztonsági kockázatot jelentenek, és ez nem használható az állapotadatok megtartására a hitelesítési kérések között – Ehhez használja a `state` paramétert.

A statikus lekérdezési paraméter az átirányítási URI-k (például az átirányítási URI bármely más része) karakterlánc-megfeleltetésének felel meg – ha nincs regisztrálva karakterlánc, amely megfelel az URI-dekódolású redirect_urinak, a rendszer elutasítja a kérelmet.  Ha az URI megtalálható az alkalmazás regisztrációjában, a rendszer a teljes karakterláncot használja a felhasználó átirányításához, beleértve a statikus lekérdezési paramétert is.

Vegye figyelembe, hogy jelenleg (2019. július végén) az alkalmazás regisztrációja UX Azure Portal továbbra is letiltják a lekérdezési paramétereket.  Az alkalmazás-jegyzékfájlt azonban manuálisan is szerkesztheti a lekérdezési paraméterek hozzáadásához és a teszteléshez az alkalmazásban.


## <a name="march-2019"></a>2019. március

### <a name="looping-clients-will-be-interrupted"></a>A hurok-ügyfelek megszakadnak

**Hatálybalépés dátuma**: március 25., 2019

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: minden folyamat

Előfordulhat, hogy az ügyfélalkalmazások időnként nem működnek, és több száz ugyanazzal a bejelentkezési kérelemmel rendelkeznek.  Előfordulhat, hogy ezek a kérések nem sikeresek, de mind hozzájárulnak a gyenge felhasználói élményhez és a megnövekedett munkaterhelésekhez a IDENTITÁSSZOLGÁLTATÓ, az összes felhasználó késésének növeléséhez és a IDENTITÁSSZOLGÁLTATÓ rendelkezésre állásának csökkentéséhez.  Ezek az alkalmazások a normál használat határain kívül működnek, és a megfelelő működés érdekében frissíteni kell őket.

A duplikált kérelmeket többször kibocsátó ügyfelek `invalid_grant` hibaüzenetet kapnak: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` .

A hiba elkerüléséhez a legtöbb ügyfélnek nem kell módosítania a viselkedést.  Ez a hiba csak a helytelenül konfigurált ügyfelek (a jogkivonat-gyorsítótárazás nélküli, illetve a hozzájuk tartozó prompt-hurkok nélkül) hatással lesz rá.  Az ügyfeleket az alábbi tényezők alapján, helyileg (cookie-n keresztül) követik nyomon.

* Felhasználói célzás, ha van ilyen

* Igényelt hatókörök vagy erőforrások

* Ügyfél-azonosító

* Átirányítási URI

* Válasz típusa és módja

A több kérést (15 +) használó alkalmazások rövid időn belül (5 perc) hibaüzenetet kapnak arról `invalid_grant` , hogy a rendszer hurkokat küld.  A kért jogkivonatok kellően hosszú élettartammal rendelkeznek (alapértelmezés szerint 10 perc, 60 perc), ezért az adott időszakra vonatkozó ismétlődő kérelmek szükségtelenek.

A `invalid_grant` tokenek csendes kérelmezése helyett minden alkalmazásnak egy interaktív kérést kell kezelnie.  A hiba elkerüléséhez az ügyfeleknek biztosítaniuk kell, hogy megfelelően gyorsítótárazzák a kapott jogkivonatokat.


## <a name="october-2018"></a>2018. október

### <a name="authorization-codes-can-no-longer-be-reused"></a>Az engedélyezési kódokat már nem lehet újra felhasználni

Hatálybalépés **dátuma**: november 15., 2018

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: [kód flow](v2-oauth2-auth-code-flow.md)

A 2018. november 15-én kezdődően az Azure AD nem fogadja el a korábban használt hitelesítési kódokat az alkalmazásokhoz. Ez a biztonsági változás segíti az Azure AD-t az OAuth-specifikációnak megfelelően bevezetni, és a v1 és v2 végpontokon is érvényben lesz.

Ha az alkalmazás az engedélyezési kódokat több erőforráshoz tartozó jogkivonatok lekérésére használja, javasoljuk, hogy a kód segítségével szerezzen be egy frissítési tokent, majd a frissítési token használatával további jogkivonatokat szerezzen be más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési tokenek többször is használhatók több erőforrásban. Minden olyan új alkalmazás, amely a OAuth-kód folyamata során a hitelesítési kódot próbálja újra felhasználni, invalid_grant hibát fog kapni.

További információ a frissítési tokenekről: [a hozzáférési tokenek frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  A ADAL vagy a MSAL használata esetén ezt a függvénytár kezeli – a "AcquireTokenByAuthorizationCodeAsync" második példányát cserélje le a "AcquireTokenSilentAsync" értékre.

## <a name="may-2018"></a>2018. május

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Az OBO-folyamathoz nem használhatók azonosító jogkivonatok

**Dátum**: 2018. május 1.

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokollok**: implicit folyamat és meghatalmazott [folyamat](v2-oauth2-on-behalf-of-flow.md)

2018. május 1-től a id_tokens nem használható az új alkalmazások esetében az OBO-flow-ban. A hozzáférési jogkivonatokat inkább az API-k védelmére kell használni, még az ügyfél és a középső rétegek között is. Az 2018. május 1. előtt regisztrált alkalmazások továbbra is működőképesek lesznek, és a hozzáférési tokenek id_tokens Exchange-re lesznek képesek. Ez a minta azonban nem tekinthető az ajánlott eljárásnak.

A módosítás megkerüléséhez a következőket teheti:

1. Hozzon létre egy webes API-t az alkalmazáshoz egy vagy több hatókörrel. Ez a explicit belépési pont lehetővé teszi a finomabb, részletes szabályozást és biztonságot.
1. Az alkalmazás jegyzékfájljában a [Azure Portal](https://portal.azure.com) vagy az [alkalmazás regisztrációs portálján](https://apps.dev.microsoft.com)ellenőrizze, hogy az alkalmazás jogosult-e hozzáférési tokeneket kiadni az implicit folyamaton keresztül. Ezt a kulcs vezérli `oauth2AllowImplicitFlow` .
1. Amikor az ügyfélalkalmazás egy id_tokent kér `response_type=id_token` a-on keresztül, `response_type=token` a fent létrehozott webes API-hoz is igényel hozzáférési tokent (). Így ha a v 2.0-s végpontot használja, a paraméternek a következőhöz `scope` hasonlóan kell kinéznie: `api://GUID/SCOPE` . A v 1.0 végponton a `resource` paraméternek a webes API alkalmazás-URI-ja kell lennie.
1. Adja át ezt a hozzáférési jogkivonatot a középső szintjére a id_token helyett.
