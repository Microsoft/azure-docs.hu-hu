---
title: Miért érdemes frissíteni a Microsoft Identity platformot (v 2.0) | Azure
description: Ismerje meg a Microsoft Identity platform (v 2.0) végpontja és a Azure Active Directory (Azure AD) v 1.0 végpont közötti különbségeket, és ismerkedjen meg a 2.0-s verzióra való frissítés előnyeivel.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5a000d08afb3afba06d82aae4414e87b61e502f
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533056"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Miért érdemes frissíteni a Microsoft Identity platformot (v 2.0)?

Új alkalmazás fejlesztésekor fontos tudni a Microsoft Identity platform (v 2.0) és a Azure Active Directory (v 1.0) végpontok közötti különbségeket. Ez a cikk a végpontok és a Microsoft Identity platform meglévő korlátai közötti fő különbségeket ismerteti.

> [!NOTE]
> A Microsoft Identity platform végpontja nem támogatja az összes Azure AD-forgatókönyvet és-funkciót. Annak megállapításához, hogy érdemes-e a Microsoft Identity platform-végpontot használni, olvassa el a [Microsoft Identity platform korlátozásait](#limitations)ismertetőt.

## <a name="who-can-sign-in"></a>Kik jelentkezhetnek be

![Ki jelentkezhet be az 1.0-s és a 2.0-s végpontokkal](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* A v 1.0 végpont lehetővé teszi, hogy csak a munkahelyi és iskolai fiókok jelentkezzenek be az alkalmazásba (Azure AD)
* A Microsoft Identity platform végpontja lehetővé teszi a munkahelyi és iskolai fiókok Azure AD-és személyes Microsoft-fiókjai (MSA), például a hotmail.com, a outlook.com és a msn.com használatát a bejelentkezéshez.
* Mindkét végpont fogadja az Azure AD-címtár *[vendég felhasználói](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* számára az *[egybérlős](single-and-multi-tenant-apps.md)* vagy a bérlői végpontra (`https://login.microsoftonline.com/{TenantId_or_Name}`) való rámutató alkalmazásokhoz konfigurált *több-bérlős* alkalmazások bejelentkezését is.

A Microsoft Identity platform végpontja lehetővé teszi olyan alkalmazások írását, amelyek elfogadják a személyes Microsoft-fiókokból, valamint a munkahelyi és iskolai fiókokból érkező bejelentkezéseket. Ez lehetővé teszi, hogy az alkalmazást teljes mértékben fiók-agnosztikusként írja. Ha például az alkalmazás meghívja a [Microsoft Graph](https://graph.microsoft.io), néhány további funkció és adat is elérhető lesz a munkahelyi fiókok, például a SharePoint-webhelyek vagy a címtáradatok számára. Azonban számos művelethez, például [egy felhasználó levelezésének olvasásához](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages)ugyanez a kód férhet hozzá az e-mailekhez a személyes és munkahelyi és az iskolai fiókokhoz is.

A Microsoft Identity platform végpontja esetében a Microsoft Authentication Library (MSAL) használatával férhet hozzá a fogyasztó, az oktatási és a vállalati világhoz. Az Azure AD v 1.0 végpont csak a munkahelyi és iskolai fiókokból érkező bejelentkezéseket fogadja el.

## <a name="incremental-and-dynamic-consent"></a>Növekményes és dinamikus beleegyezett

Az Azure AD 1.0-s verziójának végpontját használó alkalmazásoknak előre meg kell adniuk a szükséges OAuth 2,0-engedélyeket, például:

![Az engedélyek regisztrációjának felhasználói felületét bemutató példa](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

A közvetlenül az alkalmazás regisztrálására beállított engedélyek **statikusak**. Míg a Azure Portalban definiált alkalmazás statikus engedélyei megőrzik a kód szép és egyszerű használatát, néhány lehetséges problémát jelent a fejlesztők számára:

* Az alkalmazásnak minden, a felhasználó első bejelentkezéséhez szükséges engedélyt kell kérnie. Ez olyan engedélyek hosszú listáját eredményezheti, amelyek a végfelhasználók számára nem tudják jóváhagyni az alkalmazás hozzáférését a kezdeti bejelentkezéskor.

* Az alkalmazásnak ismernie kell az összes olyan erőforrást, amely bármikor elérhető. Nehéz volt olyan alkalmazásokat létrehozni, amelyek tetszőleges számú erőforráshoz férnek hozzá.

A Microsoft Identity platform-végponttal figyelmen kívül hagyhatja az alkalmazás regisztrációs információjában megadott statikus engedélyeket a Azure Portal és a kérések engedélyeit Növekményesen kéri, ami azt jelenti, hogy a minimálisan szükséges engedélyek nincsenek előre megadva egyre több időt vesz igénybe, mivel az ügyfél további alkalmazás-funkciókat használ. Ehhez bármikor megadhatja az alkalmazás által igényelt hatóköröket, ha a hozzáférési token kérésekor a `scope` paraméter új hatóköreit is beleértve – anélkül, hogy előzetesen definiálni kellene őket az alkalmazás regisztrációs adataiban. Ha a felhasználó még nem járult hozzá a kérelemhez hozzáadott új hatókörökhöz, a rendszer kérni fogja, hogy csak az új engedélyeket fogadja el. További információ: [engedélyek, beleegyezőség és hatókörök](v2-permissions-and-consent.md).

Lehetővé teszi, hogy az alkalmazások a `scope` paraméterrel dinamikusan igényeljenek engedélyeket, így teljes körűen vezérelhetik a felhasználók felhasználói élményét. Emellett betöltheti a belefoglalt felhasználói élményt, és megkérheti az összes engedélyt egy kezdeti engedélyezési kérelemben. Ha az alkalmazásnak nagy számú engedélyre van szüksége, növekményes módon gyűjtheti ezeket az engedélyeket a felhasználótól, mivel az alkalmazás bizonyos funkcióit az idő múlásával próbálja meg használni.

A szervezet nevében végrehajtott rendszergazdai jóváhagyás továbbra is az alkalmazáshoz regisztrált statikus engedélyeket igényli, ezért az alkalmazás regisztrációs portálján meg kell adnia az alkalmazásokra vonatkozó engedélyeket, ha rendszergazdai jogosultsággal kell rendelkeznie a teljes szervezet nevében történő jóváhagyáshoz. Ez csökkenti a szervezeti rendszergazda által az alkalmazás beállításához szükséges ciklusokat.

## <a name="scopes-not-resources"></a>Hatókörök, nem erőforrások

A v 1.0-s végpontot használó alkalmazások esetében az alkalmazás **erőforrásként**vagy tokenek címzettjeiként működhet. Az erőforrások meghatározhatnak számos **hatókört** vagy **oAuth2Permissions** , amelyek megértik, lehetővé téve, hogy az ügyfélalkalmazások az adott erőforrásból származó jogkivonatokat igényeljenek egy adott hatókörhöz. Vegye figyelembe a Microsoft Graph API-t egy erőforrásra példaként:

* Erőforrás-azonosító vagy `AppID URI`: `https://graph.microsoft.com/`
* Hatókörök vagy `oAuth2Permissions`: `Directory.Read`, `Directory.Write`stb.

Ez a Microsoft Identity platform végpontjának igaz értéke. Egy alkalmazás továbbra is erőforrásként működhet, hatóköröket határozhat meg, és egy URI azonosítóval azonosítható. Az ügyfélalkalmazások továbbra is igényelhetnek hozzáférést ezekhez a hatókörökhöz. Az ügyfél által kért engedélyek azonban megváltoztak.

A 1.0-s végpont esetében az OAuth 2,0-es verziójának engedélyezése az Azure AD-nak való kérelme a következőképpen néz ki:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Itt az **erőforrás** -paraméter jelezte, hogy az ügyfélalkalmazás milyen erőforrást kér az engedélyezéshez. Az Azure AD a Azure Portal statikus konfigurációja alapján kiszámítja az alkalmazás által igényelt engedélyeket, és ennek megfelelően kiállított jogkivonatokat.

A Microsoft Identity platform végpontját használó alkalmazások esetében ugyanaz a OAuth 2,0-as engedélyezési kérelem a következőképpen néz ki:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Itt a **hatókör** paraméter jelzi, hogy az alkalmazás mely erőforrásokra és engedélyekre kér engedélyt. A kívánt erőforrás továbbra is szerepel a kérelemben – a hatókör-paraméter mindegyik értékében szerepel. A hatókör-paraméter ily módon történő használata lehetővé teszi, hogy a Microsoft Identity platform végpontja jobban megfeleljen a OAuth 2,0 specifikációjának, és szorosabban illeszkedjen a közös iparági gyakorlatokhoz. Azt is lehetővé teszi, hogy az alkalmazások [növekményes](#incremental-and-dynamic-consent) belefoglalást kérjenek – csak akkor kérnek engedélyeket, ha az alkalmazás előtte szükséges.

## <a name="well-known-scopes"></a>Jól ismert hatókörök

### <a name="offline-access"></a>Offline hozzáférés

Előfordulhat, hogy a Microsoft Identity platform-végpontot használó alkalmazásokhoz új, jól ismert engedélyekre van szükség az alkalmazások számára – a `offline_access` hatókört. Az összes alkalmazásnak ezt az engedélyt kell kérnie, ha egy felhasználó nevében kell hozzáférni az erőforrásokhoz egy hosszabb ideig, még akkor is, ha a felhasználó nem fogja aktívan használni az alkalmazást. A `offline_access` hatókör a felhasználó beleegyezési párbeszédablakában jelenik meg, amikor **bármikor hozzáfér az adataihoz**, amelyet a felhasználónak el kell fogadnia. A `offline_access` engedély kérése lehetővé teszi, hogy a webalkalmazás a Microsoft Identity platform-végpontról fogadja a OAuth 2,0 refresh_tokens. A frissítési tokenek hosszú élettartamú, és az új OAuth 2,0 hozzáférési tokenek esetében kicserélhető a hosszabb hozzáférés érdekében.

Ha az alkalmazás nem kéri a `offline_access` hatókörét, nem kap frissítési jogkivonatokat. Ez azt jelenti, hogy amikor bevált egy engedélyezési kódot a OAuth 2,0-es engedélyezési kód folyamatában, a rendszer csak a hozzáférési jogkivonatot fogja kapni a `/token` végponttól. A hozzáférési jogkivonat rövid ideig érvényes (általában egy óra), de végül lejár. Ebben az időpontban az alkalmazásnak újra kell irányítani a felhasználót a `/authorize` végpontra egy új engedélyezési kód beolvasásához. Az átirányítás során előfordulhat, hogy a felhasználónak újra meg kell adnia a hitelesítő adatait, vagy újból el kell fogadnia az engedélyeket az alkalmazás típusától függően.

Ha többet szeretne megtudni a 2,0-es, `refresh_tokens`-es és `access_tokens`OAuth, tekintse meg a [Microsoft Identity platform protokoll-referenciáját](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil és e-mail

A Microsoft Identity platformmal a legtöbb alapszintű OpenID Connect bejelentkezési folyamat nagy mennyiségű információt nyújt a felhasználóról az eredményül kapott *id_tokenban*. A id_token lévő jogcímek magukban foglalhatják a felhasználó nevét, az előnyben részesített felhasználónevet, az e-mail-címet, az objektumazonosítót és egyebeket.

A `openid` hatókörben lévő információk mostantól korlátozzák az alkalmazáshoz való hozzáférést. A `openid` hatókör csak azt engedélyezi, hogy az alkalmazás bejelentkezzen a felhasználóba, és megkapja az alkalmazásra vonatkozó azonosítót a felhasználó számára. Ha a felhasználó személyes adatait szeretné lekérni az alkalmazásban, az alkalmazásnak további engedélyeket kell kérnie a felhasználótól. Két új hatókör, `email` és `profile`lehetővé teszi további engedélyek kérését.

* A `email` hatókör lehetővé teszi, hogy az alkalmazás a id_token `email` jogcímen keresztül hozzáférjen a felhasználó elsődleges e-mail-címéhez, feltéve, hogy a felhasználó címezhető e-mail-címmel rendelkezik.
* A `profile` hatóköre hozzáférést biztosít az alkalmazásnak a felhasználóval kapcsolatos összes egyéb alapvető információhoz, például a nevüket, az előnyben részesített felhasználónevet, az objektumazonosítót és így tovább a id_token.

Ezek a hatókörök lehetővé teszik az alkalmazás minimális közzétételi módon történő kódolását, így csak a felhasználónak kell megkérnie azon információk körét, amelyekre az alkalmazásnak szüksége van a feladatához. További információ ezekről a hatókörökről: [a Microsoft Identity platform hatókör-referenciája](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Jogkivonatok jogcímei

A Microsoft Identity platform végpontja alapértelmezés szerint kisebb értékű jogcímeket ad ki a jogkivonatokban. Ha olyan alkalmazásokkal és szolgáltatásokkal rendelkezik, amelyek egy, a Microsoft Identity platform jogkivonatában alapértelmezés szerint nem megadott, egy adott jogcímtől függenek, akkor a [választható jogcímek](active-directory-optional-claims.md) funkció használatával vegye fel ezt a jogcímet.

> [!IMPORTANT]
> a 1.0-s és a v 2.0-tokenek a 1.0-s és a v 2.0-s végpontok esetében is kiállíthatók. id_tokens *mindig* egyezik a kért végponttal, és a hozzáférési tokenek *mindig* egyeznek a webes API által várt formátummal, amelyet az ügyfél a token használatával hív meg.  Tehát ha az alkalmazás a v 2.0-s végpontot használja a Microsoft Graph meghívására, amely a v 1.0 formátum hozzáférési jogkivonatait várja, az alkalmazás egy jogkivonatot kap a 1.0-s formátumban.  

## <a name="limitations"></a>Korlátozások

A Microsoft Identity platform használatakor néhány korlátozást kell figyelembe vennie.

Ha a Microsoft Identity platformmal integrált alkalmazásokat készít, el kell döntenie, hogy a Microsoft Identity platform végpontja és a hitelesítési protokollok megfelelnek-e az igényeinek. A v 1.0-s végpont és a platform továbbra is teljes mértékben támogatott, és bizonyos szempontból a Microsoft Identity platformmal meggazdagodik a szolgáltatás. A Microsoft Identity platform azonban [jelentős előnyökkel jár](azure-ad-endpoint-comparison.md) a fejlesztők számára.

Íme egy egyszerűsített javaslat a fejlesztőknek most:

* Ha azt szeretné, hogy támogassa a személyes Microsoft-fiókokat az alkalmazásban, vagy új alkalmazást ír, használja a Microsoft Identity platformot. Mielőtt azonban megkezdené, győződjön meg róla, hogy megértette a cikkben tárgyalt korlátozásokat.
* Ha SAML-re támaszkodó alkalmazást telepít át vagy frissít, nem használhatja a Microsoft Identity platformot. Ehelyett tekintse meg az [Azure ad v 1.0 útmutatóját](v1-overview.md).

A Microsoft Identity platform végpontja kizárja az itt felsorolt korlátozásokat, így csak a Microsoft Identity platform-végpontot kell használnia. Addig is ebből a cikkből megtudhatja, hogy az Ön számára megfelelő-e a Microsoft Identity platform végpontja. A Microsoft Identity platform-végpont aktuális állapotának megfelelően továbbra is frissíti ezt a cikket. Térjen vissza a követelmények újraértékeléséhez a Microsoft Identity platform képességein.

### <a name="restrictions-on-app-registrations"></a>Az alkalmazások regisztrálásának korlátozásai

Minden olyan alkalmazáshoz, amelyet integrálni szeretne a Microsoft Identity platform-végponttal, létrehozhat egy alkalmazást a Azure Portal új [ **Alkalmazásregisztrációk** felhasználói felületén](https://aka.ms/appregistrations) . A meglévő Microsoft-fiók alkalmazások nem kompatibilisek a portálsal, de az összes Azure AD-alkalmazás, függetlenül attól, hogy hol vagy mikor regisztrálták őket.

A munkahelyi és iskolai fiókokat támogató Alkalmazásregisztrációk és a személyes fiókok a következő kikötésekkel rendelkeznek:

* Alkalmazás-AZONOSÍTÓként csak két alkalmazás titka engedélyezett.
* A bérlőben nem regisztrált alkalmazások csak a regisztrált fiókkal kezelhetők. Más fejlesztővel nem osztható meg. Ez a legtöbb olyan alkalmazás esetében, amelyet az alkalmazás regisztrációs portálján személyes Microsoft-fiók regisztráltak. Ha több fejlesztővel is meg szeretné osztani az alkalmazást, regisztrálja az alkalmazást egy bérlőn a Azure Portal új **Alkalmazásregisztrációk** szakasza segítségével.
* Az átirányítási URL-cím formátuma több korlátozást is igénybe vehet. Az átirányítási URL-címmel kapcsolatos további információkért tekintse meg a következő szakaszt.

### <a name="restrictions-on-redirect-urls"></a>Az átirányítási URL-címekre vonatkozó korlátozások

A Microsoft Identity platformhoz regisztrált alkalmazások korlátozott számú átirányítási URL-értékre korlátozódnak. A webalkalmazások és szolgáltatások átirányítási URL-címének a séma `https`kell kezdődnie, és minden átirányítási URL-értéknek egyetlen DNS-tartományhoz kell tartoznia.  A regisztrációs rendszer összehasonlítja a meglévő átirányítási URL-cím teljes DNS-nevét a hozzáadott átirányítási URL-cím DNS-nevével. a `http://localhost` átirányítási URL-címként is támogatott.  

A DNS-név hozzáadására irányuló kérelem sikertelen lesz, ha a következő feltételek egyikének értéke igaz:  

* Az új átirányítási URL-cím teljes DNS-neve nem egyezik meg a meglévő átirányítási URL-cím DNS-nevével.
* Az új átirányítási URL-cím teljes DNS-neve nem a meglévő átirányítási URL-cím altartománya.

#### <a name="example-1"></a>1\. példa

Ha az alkalmazásnak `https://login.contoso.com`átirányítási URL-címe van, akkor hozzáadhat egy átirányítási URL-címet, ahol a DNS-név pontosan megfelel az alábbi példában látható módon:

`https://login.contoso.com/new`

Vagy a login.contoso.com DNS-altartományára is hivatkozhat, ahogy az az alábbi példában is látható:

`https://new.login.contoso.com`

#### <a name="example-2"></a>2\. példa

Ha olyan alkalmazást szeretne használni, amely átirányítási URL-címekkel rendelkezik `login-east.contoso.com` és `login-west.contoso.com`, akkor az átirányítási URL-címeket a következő sorrendben kell felvennie:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Az utóbbi kettőt hozzáadhatja, mert az első átirányítási URL-cím altartományai, a contoso.com.

Egy adott alkalmazáshoz csak 20 Válasz URL-cím tartozhat – ez a korlát minden olyan alkalmazási típusra vonatkozik, amelyet a regisztráció támogat (egyoldalas alkalmazás (SPA), natív ügyfél, webalkalmazás és szolgáltatás).  

Ha szeretné megtudni, hogyan regisztrálhat egy alkalmazást a Microsoft Identity platformmal való használatra, tekintse meg [az alkalmazás regisztrálása az új Alkalmazásregisztrációk felülettel](quickstart-register-app.md)című témakört.

### <a name="restrictions-on-libraries-and-sdks"></a>Tárak és SDK-k korlátozása

Jelenleg a Microsoft Identity platform végpontjának függvénytár-támogatása korlátozott. Ha éles alkalmazásban szeretné használni a Microsoft Identity platform végpontját, a következő lehetőségek közül választhat:

* Ha webalkalmazást hoz létre, az általánosan elérhető kiszolgálóoldali middleware-t biztonságosan használhatja a bejelentkezéshez és a jogkivonatok érvényesítéséhez. Ezek közé tartozik a OWIN OpenID Connect middleware a ASP.NET és a Node. js Passport beépülő modul. A Microsoft middleware-t használó kódrészletek esetében tekintse meg a [Microsoft Identity platform első lépések](v2-overview.md#getting-started) című szakaszát.
* Ha asztali vagy mobil alkalmazást hoz létre, használhatja a Microsoft Authentication librarys (MSAL) egyikét. Ezek a kódtárak általánosan elérhetők, vagy éles környezetben támogatott előzetes verzióban, így biztonságos módon használhatók éles alkalmazásokban. További információt az előzetes verzióról és a [hitelesítő függvénytárak dokumentációjában](reference-v2-libraries.md)elérhető könyvtárakról itt olvashat.
* A Microsoft-kódtárak által nem támogatott platformok esetében az alkalmazás kódjában lévő protokoll üzeneteinek közvetlen küldésével és fogadásával integrálható a Microsoft Identity platform-végpontba. Az OpenID Connect és a OAuth protokollok [explicit módon dokumentálva vannak](active-directory-v2-protocols.md) , így segít az ilyen integrációban.
* Végül használhatja a nyílt forráskódú OpenID Connect és a OAuth kódtárat a Microsoft Identity platform végpontjának integrálásához. A Microsoft Identity platform végpontjának kompatibilisnek kell lennie a számos nyílt forráskódú protokoll-kódtáraval anélkül, hogy módosításokat kellene megadnia. Az ilyen típusú könyvtárak rendelkezésre állása nyelvtől és platformtól függően változik. Az [OpenID Connect](https://openid.net/connect/) és a [OAuth 2,0](https://oauth.net/2/) websites fenntartják a népszerű implementációk listáját. További információ: [Microsoft Identity platform és Authentication librarys](reference-v2-libraries.md), valamint a Microsoft Identity platform-végponttal tesztelt nyílt forráskódú ügyféloldali kódtárak és minták listája.
* Referenciául a Microsoft Identity platform közös végpontjának `.well-known` végpontja `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Cserélje le a `common`t a bérlői AZONOSÍTÓra a bérlőre vonatkozó adatlekérdezéshez.  

### <a name="protocol-changes"></a>Protokoll módosításai

A Microsoft Identity platform végpontja nem támogatja az SAML vagy a WS-Federation használatát; csak az OpenID Connect és a OAuth 2,0 használatát támogatja.  A OAuth 2,0 protokollnak a v 1.0 végpontról történő jelentős módosításai a következők: 

* Az `email` jogcímet akkor adja vissza a rendszer, ha egy opcionális jogcím konfigurálva van, **vagy** hatókör = e-mail-cím lett megadva a kérelemben. 
* A `scope` paraméter mostantól támogatott a `resource` paraméter helyett.  
* Számos válasz lett módosítva, hogy azok megfeleljenek a OAuth 2,0-specifikációnak, például helytelenül ad vissza `expires_in` as int karakterlánc helyett.  

A Microsoft Identity platform végpontjában támogatott protokoll-funkciók jobb megismeréséhez tekintse meg az [OpenID Connect és a OAuth 2,0 protokoll-referenciát](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>SAML-korlátozások

Ha a Windows-alkalmazásokban Active Directory-hitelesítési tár (ADAL) használta, lehetséges, hogy kihasználta a Windows integrált hitelesítés előnyeit, amely a Security Assertion Markup Language (SAML) jogcímet használja. Ezzel a támogatással az összevont Azure AD-bérlők felhasználói a hitelesítő adatok megadása nélkül csendes módon hitelesíthetők a helyszíni Active Directory-példánnyal. Az SAML-jogcímek engedélyezése nem támogatott a Microsoft Identity platform végpontján.
