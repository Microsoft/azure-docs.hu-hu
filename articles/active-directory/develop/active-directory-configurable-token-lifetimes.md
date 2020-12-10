---
title: Konfigurálható jogkivonat élettartama
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan állíthatja be a Microsoft Identity platform által kiállított jogkivonatok élettartamát.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 12d74e2f21678fdbb02719c29a31c0fcdc4fa132
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030145"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Konfigurálható jogkivonat-élettartamok a Microsoft Identity platformban (előzetes verzió)

> [!IMPORTANT]
> 2021. január 30-ig a bérlők többé nem tudják konfigurálni a frissítési és a munkamenet-tokenek élettartamát, és Azure Active Directory a házirendek után leállítja a frissítési és a munkamenet-jogkivonat konfigurációját.
>
> Ha továbbra is meg kell határoznia azt az időtartamot, ameddig a felhasználónak újra be kell jelentkeznie, konfigurálnia kell a bejelentkezési gyakoriságot a feltételes hozzáférésben. Ha többet szeretne megtudni a feltételes hozzáférésről, látogasson el az [Azure ad díjszabási oldalára](https://azure.microsoft.com/en-us/pricing/details/active-directory/).
>
> Azok a bérlők, akik nem szeretnék feltételes hozzáférést használni a lejárati dátum után, számíthatnak arra, hogy az Azure AD tiszteletben tartja a következő szakaszban ismertetett alapértelmezett konfigurációt.

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>Konfigurálható jogkivonat élettartamának tulajdonságai a kivonulás után
A frissítési és a munkamenet-jogkivonat konfigurációját a következő tulajdonságok és azok beállított értékei érintik. A frissítési és a munkamenet-jogkivonat konfigurációjának kivonása után az Azure AD csak az alább ismertetett alapértelmezett értéket fogja figyelembe venni, függetlenül attól, hogy a szabályzatok egyéni értékeit konfigurálta-e egyéni értékekkel. A hozzáférési jogkivonat élettartamát a nyugdíjazás után is konfigurálhatja. 

|Tulajdonság   |Házirend tulajdonságának karakterlánca    |Befolyásolja |Alapértelmezett |
|----------|-----------|------------|------------|
|Frissítési jogkivonat maximális inaktív ideje |MaxInactiveTime  |Tokenek frissítése |90 nap  |
|Single-Factor frissítési token Max Age  |MaxAgeSingleFactor  |Tokenek frissítése (bármely felhasználó esetében)  |Visszavonásig  |
|Multi-Factor refresh token Max Age  |MaxAgeMultiFactor  |Tokenek frissítése (bármely felhasználó esetében) |180 nap  |
|Single-Factor munkamenet-token maximális kora  |MaxAgeSessionSingleFactor |Munkamenet-tokenek (állandó és nem állandó)  |Visszavonásig |
|Többtényezős munkamenet-token maximális kora  |MaxAgeSessionMultiFactor  |Munkamenet-tokenek (állandó és nem állandó)  |180 nap |

## <a name="identify-configuration-in-scope-of-retirement"></a>A konfiguráció azonosítása a nyugdíjazás hatókörében

A kezdéshez hajtsa végre a következő lépéseket:

1. Töltse le a legújabb [Azure ad PowerShell-modul nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview).
1. A parancs futtatásával `Connect` Jelentkezzen be az Azure ad-rendszergazdai fiókjába. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít el.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. A szervezetben létrehozott összes házirend megtekintéséhez futtassa a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot.  A fent felsorolt alapértelmezett értékektől eltérő, meghatározott tulajdonságértékeket használó eredmények a nyugdíjazás hatókörében vannak.

    ```powershell
    Get-AzureADPolicy -All
    ```

1. Ha szeretné megtekinteni, hogy mely alkalmazások és szolgáltatások vannak összekapcsolva egy adott házirenddel, akkor a következő [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) parancsmagot kell lecserélnie a **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** bármely házirend-azonosítóval való lecserélésével. Ezután eldöntheti, hogy konfigurálja-e a feltételes hozzáférés bejelentkezési gyakoriságát, vagy az Azure AD alapértelmezett értékeivel marad-e.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Ha a bérlő rendelkezik olyan házirendekkel, amelyek egyéni értékeket határoznak meg a frissítési és a munkamenet-jogkivonat konfigurációs tulajdonságaihoz, a Microsoft javasolja, hogy frissítse ezeket a házirendeket a fent ismertetett alapértékeket tükröző értékekre. Ha nem végez módosítást, az Azure AD automatikusan tiszteletben tartja az alapértelmezett értékeket.  

## <a name="overview"></a>Áttekintés

Megadhatja a Microsoft Identity platform által kiadott jogkivonatok élettartamát. Beállíthatja a cégen belüli összes alkalmazás jogkivonatának élettartamát több-bérlős alkalmazások (több cég) vagy munkahelyen belüli adott szolgáltatásnév esetén. Jelenleg azonban a [felügyelt identitás-szolgáltatási rendszerbiztonsági tag](../managed-identities-azure-resources/overview.md)esetében nem támogatott a jogkivonat élettartamának konfigurálása.

Az Azure AD-ben a házirend-objektum az egyes alkalmazásokra vagy a szervezet összes alkalmazására kikényszerített szabályok halmazát jelöli. Minden egyes házirend-típushoz egyedi struktúra tartozik, amely a hozzájuk rendelt objektumokra érvényes tulajdonságokkal rendelkezik.

Megadhat egy házirendet a szervezet alapértelmezett szabályzatának megfelelően. A szabályzatot a rendszer a szervezet bármely alkalmazására alkalmazza, amennyiben azt nem bírálja felül magasabb prioritású házirend. Egy szabályzatot adott alkalmazásokhoz is hozzárendelhet. A prioritás sorrendje a házirend típusa szerint változhat.

Példákat olvashat a [jogkivonat-élettartamok konfigurálásának módjáról](configure-token-lifetimes.md).

> [!NOTE]
> A konfigurálható jogkivonat élettartama házirend csak a SharePoint Online-hoz és az üzleti erőforrásokhoz hozzáférő mobil-és asztali ügyfelekre vonatkozik, és nem vonatkozik a webböngésző-OneDrive.
> A SharePoint Online-hoz és a OneDrive for Businesshez készült webböngésző-munkamenetek élettartamának kezeléséhez használja a [feltételes hozzáférési munkamenet élettartama](../conditional-access/howto-conditional-access-session-lifetime.md) funkciót. Ha többet szeretne megtudni az üresjárati munkamenetek időtúllépésének konfigurálásáról, tekintse meg a [SharePoint Online blogját](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) .

## <a name="token-types"></a>Token-típusok

Megadhatja a jogkivonat élettartamára vonatkozó szabályzatokat a frissítési tokenekhez, a hozzáférési jogkivonatokhoz, az SAML-tokenekhez, a munkamenet-jogkivonatokhoz és az azonosító jogkivonatokhoz.

### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Az ügyfelek hozzáférési jogkivonatokkal férnek hozzá egy védett erőforráshoz. Hozzáférési jogkivonat csak a felhasználó, az ügyfél és az erőforrás adott kombinációjára használható. A hozzáférési jogkivonatok nem vonhatók vissza, és a lejárat előtt érvényesek. Egy rosszindulatú színész, amely hozzáférési tokent kapott, az élettartama mértékének megfelelően használhatja. A hozzáférési jogkivonat élettartamának módosítása a rendszer teljesítményének javítása és a felhasználó fiókjának letiltása után az ügyfél által megőrzött idő növelése közötti kompromisszum. A rendszer jobb teljesítményét úgy érheti el, hogy csökkenti az ügyfelek által a friss hozzáférési token beszerzéséhez szükséges számú időt.  Az alapértelmezett érték 1 óra – 1 óra elteltével az ügyfélnek a frissítési tokent kell használnia (általában csendesen) új frissítési jogkivonat és hozzáférési jogkivonat beszerzése. 

### <a name="saml-tokens"></a>SAML-jogkivonatok

Az SAML-jogkivonatokat számos webalapú SAAS-alkalmazás használja, és a Azure Active Directory egy SAML2 protokoll-végpontján keresztül szerezhetők be. Ezeket a WS-Federationt használó alkalmazások is használják. A token alapértelmezett élettartama 1 óra. Egy alkalmazás szemszögéből a jogkivonat érvényességi idejét a `<conditions …>` jogkivonat elemének NotOnOrAfter értéke adja meg. A jogkivonat érvényességi időtartamának lejárta után az ügyfélnek új hitelesítési kérelmet kell kezdeményeznie, amely az egyszeri bejelentkezés (SSO) munkamenet-jogkivonatának eredményeképpen gyakran elégedett lesz az interaktív bejelentkezés nélkül.

A NotOnOrAfter értéke az a paraméter használatával módosítható `AccessTokenLifetime` `TokenLifetimePolicy` . A házirendben megadott élettartamra lesz beállítva, ha van ilyen, valamint egy óra, amely öt percet vesz igénybe.

Az elemben megadott tulajdonos megerősítő NotOnOrAfter `<SubjectConfirmationData>` nem érinti a jogkivonat élettartamának konfigurációja. 

### <a name="refresh-tokens"></a>Tokenek frissítése

Amikor egy ügyfél hozzáférési jogkivonatot kap egy védett erőforrás eléréséhez, az ügyfél frissítési jogkivonatot is kap. A frissítési jogkivonat az új hozzáférési/frissítési jogkivonat-párok beszerzésére szolgál, ha az aktuális hozzáférési jogkivonat lejár. A frissítési jogkivonat a felhasználó és az ügyfél kombinációjával van kötve. A frissítési tokenek bármikor [visszavonhatók](access-tokens.md#token-revocation), és a jogkivonat érvényességét a rendszer minden alkalommal ellenőrzi, amikor a jogkivonat használatban van.  A frissítési tokenek nem vonhatók vissza, amikor új hozzáférési jogkivonatok beolvasására használják – ez a legjobb megoldás, ha azonban a régi jogkivonatot biztonságosan törli, amikor egy újat kap.

Fontos, hogy különbséget tegyen a bizalmas ügyfelek és a nyilvános ügyfelek között, mivel ez azt befolyásolja, hogy mennyi ideig használhatók a frissítési tokenek. További információ a különböző típusú ügyfelekről: [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Jogkivonat-élettartamok bizalmas ügyfél-frissítési jogkivonatokkal
A bizalmas ügyfelek olyan alkalmazások, amelyek biztonságos módon tárolhatják az ügyfél jelszavát (titkos kulcs). Bizonyítani tudják, hogy a kérések a biztonságos ügyfélalkalmazás felől érkeznek, nem rosszindulatú színésztől. Egy webalkalmazás például egy bizalmas ügyfél, mert a webkiszolgálón tárolhatja az ügyfél titkos kulcsát. Nincs kitéve. Mivel ezek a folyamatok biztonságosabbak, az ezekre a folyamatokra kiállított frissítési jogkivonatok alapértelmezett élettartama a `until-revoked` házirend használatával nem módosítható, és nem vonható vissza az önkéntes jelszó-visszaállítási művelet.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tokenek élettartama nyilvános ügyfél-frissítési jogkivonatokkal

A nyilvános ügyfelek nem tudják biztonságosan tárolni az ügyfél jelszavát (titkos kulcs). Egy iOS-/Android-alkalmazás például nem tud titkos kulcsot kialakítani az erőforrás-tulajdonostól, ezért nyilvános ügyfélnek számít. Az erőforrásokra vonatkozó szabályzatok megadásával megakadályozhatja, hogy a frissítési tokenek a megadott időszaknál régebbi nyilvános ügyfelekről szerezzenek új hozzáférési vagy frissítési jogkivonat-párokat. Ehhez használja a [refresh token Max inaktív idő tulajdonságát](#refresh-token-max-inactive-time) ( `MaxInactiveTime` ). Házirendeket is használhat egy olyan időszak megadására, amelyen túl a frissítési tokenek már nem lesznek elfogadva. Ehhez használja az [egytényezős frissítési token Max Age](#single-factor-session-token-max-age) vagy a [multi-Factor munkamenet-token Max Age](#multi-factor-refresh-token-max-age) tulajdonságát. A frissítési token élettartama beállítható annak szabályozására, hogy a felhasználó mikor és milyen gyakran írja elő a hitelesítő adatok újbóli hitelesítését, ahelyett, hogy a rendszer egy nyilvános ügyfélalkalmazás használatával visszaadja a hitelesítő adatokat.

> [!NOTE]
> A Max Age tulajdonság azt az időtartamot használja, ameddig egyetlen jogkivonat használható. 

### <a name="id-tokens"></a>Azonosító jogkivonatok
Az azonosító jogkivonatok átadása a webhelyeknek és a natív ügyfeleknek. Az azonosító jogkivonatok egy felhasználó profiljára vonatkozó adatokat tartalmaznak. Az azonosító jogkivonat a felhasználó és az ügyfél adott kombinációjára van kötve. Az azonosító jogkivonatok érvényessége csak a lejárat után tekinthető érvényesnek. A webalkalmazások általában a felhasználó munkamenetének élettartamát tükrözik az alkalmazásban a felhasználó számára kiállított azonosító jogkivonat élettartama alapján. Az azonosító token élettartama beállítható annak szabályozására, hogy a webalkalmazás milyen gyakran járjon le az alkalmazás-munkamenetben, és hogy milyen gyakran szükséges, hogy a felhasználó újra legyen hitelesítve a Microsoft Identity platformmal (csendes vagy interaktív módon).

### <a name="single-sign-on-session-tokens"></a>Egyszeri bejelentkezés munkamenet-jogkivonatai
Amikor egy felhasználó a Microsoft Identity platformmal végzi a hitelesítést, az egyszeri bejelentkezési munkamenet (SSO) a felhasználó böngészőjével és a Microsoft Identity platformmal lesz létrehozva. A cookie-t tartalmazó SSO-jogkivonat ezt a munkamenetet jelöli. Az SSO-munkamenet tokenje nem kötődik egy adott erőforráshoz/ügyfélalkalmazás-alkalmazáshoz. Az egyszeri bejelentkezéses munkamenet-tokenek visszavonhatók, és az érvényességük minden használatkor be van jelölve.

A Microsoft Identity platform kétféle egyszeri bejelentkezéses munkamenet-jogkivonatot használ: állandó és nem állandó. Az állandó munkamenet-tokeneket a böngésző állandó cookie-ként tárolja. A nem állandó munkamenet-tokenek munkamenet-cookie-ként vannak tárolva. (A munkamenet-cookie-k megsemmisülnek a böngésző bezárásakor.) Általában nem állandó munkamenet-token van tárolva. Ha azonban a **felhasználó a bejelentkezéskor jelölőnégyzet** bejelölését választja, a rendszer állandó munkamenet-tokent tárol.

A nem állandó munkamenet-tokenek élettartama 24 óra. Az állandó tokenek élettartama 90 nap. Az egyszeri bejelentkezési munkamenet tokenjét az érvényességi időtartamon belül kell használni, az érvényességi időtartam a jogkivonat típusától függően egy másik 24 órán vagy 90 nap múlva meghosszabbodik. Ha a rendszer nem használja az egyszeri bejelentkezési munkamenet tokenjét az érvényességi időtartamon belül, akkor a rendszer lejártnak tekinti, és már nem fogadja el.

A szabályzattal megadhatja azt az időpontot, ameddig az első munkamenet-jogkivonat ki lett állítva, amely után a munkamenet-jogkivonat már nem fogadható el. (Ehhez használja a munkamenet-jogkivonat Max Age tulajdonságát.) A munkamenet-token élettartamát beállíthatja annak szabályozására, hogy a rendszer mikor és milyen gyakran írja elő a felhasználótól a hitelesítő adatok újbóli megadását a nem hitelesített hitelesítés helyett, ha webalkalmazást használ.

### <a name="token-lifetime-policy-properties"></a>Jogkivonat élettartama házirend tulajdonságai
A jogkivonat élettartama házirend olyan házirend-objektum, amely a jogkivonat élettartamának szabályait tartalmazza. A szabályzat tulajdonságai segítségével szabályozhatja a megadott jogkivonat-élettartamot. Ha nincs beállítva házirend, a rendszer kényszeríti az alapértelmezett élettartam értékét.

### <a name="configurable-token-lifetime-properties"></a>Konfigurálható jogkivonat élettartamának tulajdonságai
| Tulajdonság | Házirend tulajdonságának karakterlánca | Befolyásolja | Alapértelmezett | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Hozzáférési jogkivonat élettartama |<sup>2</sup> . AccessTokenLifetime |Hozzáférési tokenek, azonosító tokenek, egy SAML2 tokenek |1 óra |10 perc |1 nap |
| Frissítési jogkivonat maximális inaktív ideje |MaxInactiveTime |Tokenek frissítése |90 nap |10 perc |90 nap |
| Single-Factor frissítési token Max Age |MaxAgeSingleFactor |Tokenek frissítése (bármely felhasználó esetében) |Visszavonásig |10 perc |Visszavonás:<sup>1</sup> |
| Multi-Factor refresh token Max Age |MaxAgeMultiFactor |Tokenek frissítése (bármely felhasználó esetében) | 180 nap |10 perc |180 nap<sup>1</sup> |
| Single-Factor munkamenet-token maximális kora |MaxAgeSessionSingleFactor |Munkamenet-tokenek (állandó és nem állandó) |Visszavonásig |10 perc |Visszavonás:<sup>1</sup> |
| Többtényezős munkamenet-token maximális kora |MaxAgeSessionMultiFactor |Munkamenet-tokenek (állandó és nem állandó) | 180 nap |10 perc | 180 nap<sup>1</sup> |

* <sup>1</sup>365 nappal az attribútumok maximális explicit hosszúsága adható meg.
* <sup>2</sup> A Microsoft Teams Web Client működésének biztosítása érdekében ajánlott a AccessTokenLifetime 15 percnél hosszabb ideig megőrizni a Microsoft Teams szolgáltatásban.

### <a name="exceptions"></a>Kivételek
| Tulajdonság | Befolyásolja | Alapértelmezett |
| --- | --- | --- |
| Frissítési jogkivonat maximális kora (a nem elegendő visszavonási<sup>információval</sup>rendelkező összevont felhasználók számára kiállított) |Frissítési tokenek (olyan összevont felhasználók számára, akik nem rendelkeznek elegendő visszavonási információval<sup>1</sup>) |12 óra |
| Frissítési jogkivonat maximális inaktív ideje (bizalmas ügyfelek számára kiállítva) |Frissítési tokenek (bizalmas ügyfelek számára kiállítva) |90 nap |
| Frissítési token maximális kora (bizalmas ügyfelek számára kiállítva) |Frissítési tokenek (bizalmas ügyfelek számára kiállítva) |Visszavonásig |

* <sup>1</sup> a nem elegendő visszavonási információval rendelkező összevont felhasználók között szerepelnek azok a felhasználók, akik nem rendelkeznek szinkronizált "LastPasswordChangeTimestamp" attribútummal. Ezek a felhasználók ezt a rövid maximális kort kapják meg, mert Azure Active Directory nem tudja ellenőrizni, hogy mikor kell visszavonni a régi hitelesítő adatokhoz kötött jogkivonatokat (például a jelszót, amelyet módosítottak), és gyakrabban kell ismét bejelentkeznie ahhoz, hogy a felhasználó és a társított jogkivonatok továbbra is jó helyzetben legyenek. A környezet javítása érdekében a bérlői rendszergazdáknak biztosítaniuk kell, hogy szinkronizálják a "LastPasswordChangeTimestamp" attribútumot (ezt a felhasználói objektumhoz a PowerShell vagy a AADSync használatával lehet beállítani).

### <a name="policy-evaluation-and-prioritization"></a>Szabályzat kiértékelése és rangsorolása
Létrehozhat és hozzárendelhet egy jogkivonat-élettartam-szabályzatot egy adott alkalmazáshoz, a szervezetéhez és az egyszerű szolgáltatásokhoz. Egy adott alkalmazásra több házirend is alkalmazható. A jogkivonat élettartamára vonatkozó szabályzat a következő szabályokat követi:

* Ha a szabályzatot explicit módon rendeli hozzá a szolgáltatáshoz, azt a rendszer kényszeríti.
* Ha nincs kifejezetten hozzárendelve házirend az egyszerű szolgáltatáshoz, a rendszer kikényszeríti az egyszerű szolgáltatásnév fölérendelt szervezetéhez hozzárendelt szabályzatot.
* Ha a szolgáltatáshoz vagy a szervezethez explicit módon nem rendel hozzá szabályzatot, a rendszer kikényszeríti az alkalmazáshoz rendelt szabályzatot.
* Ha nem rendelt hozzá szabályzatot a szolgáltatáshoz, a szervezethez vagy az alkalmazás-objektumhoz, a rendszer az alapértelmezett értékeket kényszeríti ki. (Lásd a következő táblázatot a [konfigurálható jogkivonat élettartamának tulajdonságaiban](#configurable-token-lifetime-properties).)

További információ az alkalmazásobjektumok és a szolgáltatás-objektumok közötti kapcsolatról: [alkalmazás-és szolgáltatásnév-objektumok Azure Active Directoryban](app-objects-and-service-principals.md).

A jogkivonat érvényességét a rendszer a jogkivonat használatának időpontjában értékeli ki. Az elérni kívánt alkalmazás legmagasabb prioritású szabályzata érvénybe lép.

Az itt használt összes időtávok a C# [TimeSpan](/dotnet/api/system.timespan) objektum-D. HH: PP: mm szerint van formázva.  Tehát 80 nap és 30 perc lenne `80.00:30:00` .  A vezető D-t nullára lehet dobni, így 90 perc lenne `00:90:00` .  

> [!NOTE]
> Példa erre a forgatókönyvre.
>
> A felhasználók két webalkalmazáshoz szeretnének hozzáférni: webalkalmazás A és A (z) B webalkalmazás.
> 
> Tényezők
> * Mindkét webalkalmazás ugyanahhoz a szülő szervezethez tartozik.
> * A jogkivonat élettartamának szabályzata 1. a munkamenet tokenje legfeljebb nyolc óra, a szülő szervezet alapértelmezett értékeként van beállítva.
> * Az A webalkalmazás egy normál használatú webalkalmazás, és nincs hozzárendelve szabályzatokhoz.
> * A "B" webalkalmazás a szigorúan bizalmas folyamatokhoz használatos. Az egyszerű szolgáltatás a jogkivonat-élettartamra vonatkozó szabályzat 2., amelynek a munkamenet-tokenje legfeljebb 30 percet vesz igénybe.
>
> A felhasználó 12:00 ÓRAKOR elindítja az új böngésző-munkamenetet, és megpróbál hozzáférni A webalkalmazáshoz. A rendszer átirányítja a felhasználót a Microsoft Identity platformra, és megkéri, hogy jelentkezzen be. Ez létrehoz egy cookie-t, amely egy munkamenet-tokent tartalmaz a böngészőben. A rendszer visszairányítja a felhasználót az A webalkalmazásba egy azonosító jogkivonattal, amely lehetővé teszi, hogy a felhasználó hozzáférjen az alkalmazáshoz.
>
> A felhasználó 12:15 ÓRAKOR megpróbál hozzáférni a (z) B webalkalmazáshoz. A böngésző átirányítja a Microsoft Identity platformra, amely észleli a munkamenet cookie-t. A webalkalmazás B szolgáltatásának egyszerű szolgáltatása a jogkivonat élettartama (2), de a szülő szervezet része is, és az alapértelmezett jogkivonat élettartama házirend 1. A jogkivonat élettartamára vonatkozó házirend 2 érvénybe lép, mert az egyszerű szolgáltatásokhoz kapcsolódó házirendek magasabb prioritással rendelkeznek, mint a szervezet alapértelmezett házirendjei. A munkamenet jogkivonatát eredetileg az elmúlt 30 percben adták ki, ezért érvényesnek számít. A rendszer visszairányítja a felhasználót a (B) webalkalmazásba egy olyan azonosító jogkivonattal, amely hozzáférést biztosít számukra.
>
> A felhasználó 1:00 ÓRAKOR megpróbál hozzáférni a (z) A webalkalmazáshoz. A rendszer átirányítja a felhasználót a Microsoft Identity platformra. Az A webalkalmazás nincs hozzárendelve egyetlen házirendhez sem, azonban mivel az alapértelmezett jogkivonat-élettartamot biztosító szervezeten belül van, a házirend érvénybe lép. A rendszer az utolsó nyolc órában eredetileg kiállított munkamenet-cookie-t észleli. A felhasználó csendesen át lesz irányítva a webalkalmazásba egy új azonosító jogkivonattal. A felhasználónak nincs szüksége a hitelesítésre.
>
> Ezt követően a felhasználó megpróbál hozzáférni a B webalkalmazáshoz. A rendszer átirányítja a felhasználót a Microsoft Identity platformra. Ahogy korábban is, a jogkivonat élettartamának szabályzata 2 érvénybe lép. Mivel a jogkivonat több mint 30 perccel ezelőtt lett kiállítva, a rendszer felszólítja a felhasználót, hogy adja meg újra a bejelentkezési hitelesítő adatait. A rendszer új munkamenet-tokent és azonosító jogkivonatot állít ki. A felhasználó Ezután elérheti a B webalkalmazást.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurálható házirend-Tulajdonságok részletei
### <a name="access-token-lifetime"></a>Hozzáférési jogkivonat élettartama
**Karakterlánc:** AccessTokenLifetime

A **következőket érinti:** Hozzáférési tokenek, azonosító tokenek, SAML-tokenek

**Összefoglalás:** Ez a házirend azt szabályozza, hogy az erőforráshoz tartozó hozzáférési és azonosító jogkivonatok érvényesek legyenek. A hozzáférési jogkivonat élettartama tulajdonságának csökkentése csökkenti annak kockázatát, hogy egy rosszindulatú színész egy hozzáférési jogkivonatot vagy egy azonosító jogkivonatot hosszabb ideig használ. (Ezek a tokenek nem vonhatók vissza.) A kompromisszum az, hogy a teljesítmény negatív hatással van, mivel a jogkivonatokat gyakrabban kell cserélni.

Példaként tekintse meg [a házirend létrehozása webes bejelentkezéshez](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)című témakört.

### <a name="refresh-token-max-inactive-time"></a>Frissítési jogkivonat maximális inaktív ideje
**Karakterlánc:** MaxInactiveTime

A **következőket érinti:** Tokenek frissítése

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a frissítési jogkivonat hány korábbi lehet, hogy az ügyfél többé nem tudja lekérni az új hozzáférési/frissítési jogkivonat-párokat, amikor megpróbál hozzáférni ehhez az erőforráshoz. Mivel a rendszer általában új frissítési jogkivonatot ad vissza a frissítési token használatakor, ez a házirend megakadályozza a hozzáférést, ha az ügyfél az aktuális frissítési jogkivonat használatával megpróbál hozzáférni az erőforrásokhoz a megadott időszakban.

Ez a házirend azokat a felhasználókat kényszeríti, akik nem voltak aktívak az ügyfélen az új frissítési jogkivonat lekéréséhez.

A frissítési token maximális inaktív idő tulajdonságának alacsonyabb értékre kell állítani, mint az Single-Factor token Max Age és a multi-Factor refresh token Max Age tulajdonságai.

Példaként tekintse meg a szabályzat [létrehozása egy olyan natív alkalmazáshoz, amely webes API-t hív meg](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-refresh-token-max-age"></a>Single-Factor frissítési token Max Age
**Karakterlánc:** MaxAgeSingleFactor

A **következőket érinti:** Tokenek frissítése

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhat frissítési jogkivonatot új hozzáférési/frissítési jogkivonat-párok beszerzésére, miután a rendszer csak egyetlen tényező használatával sikeresen hitelesítette a hitelesítést. Miután egy felhasználó hitelesíti és új frissítési tokent kapott, a felhasználó a megadott időtartamra használhatja a frissítési jogkivonat folyamatát. (Ez akkor igaz, ha az aktuális frissítési jogkivonat nincs visszavonva, és az inaktív időpontnál hosszabb ideig nem használatban marad.) Ezen a ponton a felhasználónak újra hitelesítenie kell magát, hogy új frissítési tokent kapjon.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egytényezős hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, javasoljuk, hogy ezt a tulajdonságot olyan értékre állítsa be, amely egyenlő vagy kisebb, mint a multi-Factor refresh token Max Age tulajdonsága.

Példaként tekintse meg a szabályzat [létrehozása egy olyan natív alkalmazáshoz, amely webes API-t hív meg](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor refresh token Max Age
**Karakterlánc:** MaxAgeMultiFactor

A **következőket érinti:** Tokenek frissítése

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhat frissítési jogkivonatot új hozzáférési/frissítési jogkivonat-párok beszerzéséhez, miután több tényezővel sikeresen elvégezte a hitelesítést. Miután egy felhasználó hitelesíti és új frissítési tokent kapott, a felhasználó a megadott időtartamra használhatja a frissítési jogkivonat folyamatát. (Ez akkor igaz, ha az aktuális frissítési jogkivonat nincs visszavonva, és az inaktívnál hosszabb ideig nem használható fel.) Ezen a ponton a felhasználóknak újra kell hitelesíteniük magukat, hogy új frissítési tokent kapjanak.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egytényezős hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, javasoljuk, hogy ezt a tulajdonságot olyan értékre állítsa be, amely egyenlő vagy nagyobb, mint az Single-Factor frissítési token Max Age tulajdonsága.

Példaként tekintse meg a szabályzat [létrehozása egy olyan natív alkalmazáshoz, amely webes API-t hív meg](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-session-token-max-age"></a>Single-Factor munkamenet-token maximális kora
**Karakterlánc:** MaxAgeSessionSingleFactor

A **következőket érinti:** Munkamenet-tokenek (állandó és nem állandó)

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhatja a munkamenet-tokent új azonosító és munkamenet-jogkivonat beszerzésére, miután a rendszer csak egyetlen tényezővel sikeresen hitelesítette a hitelesítést. Miután egy felhasználó egy új munkamenet-tokent hitelesített és kap, a felhasználó a munkamenet-jogkivonat folyamatát a megadott ideig használhatja. (Ez akkor igaz, ha az aktuális munkamenet-jogkivonat nincs visszavonva, és nem járt le.) A megadott időtartam elteltével a felhasználónak újra hitelesítenie kell magát, hogy új munkamenet-tokent kapjon.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egytényezős hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, javasoljuk, hogy ezt a tulajdonságot olyan értékre állítsa be, amely a multi-Factor munkamenet-token Max Age tulajdonságával egyenlő vagy annál kisebb.

Példaként tekintse meg [a házirend létrehozása webes bejelentkezéshez](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)című témakört.

### <a name="multi-factor-session-token-max-age"></a>Többtényezős munkamenet-token maximális kora
**Karakterlánc:** MaxAgeSessionMultiFactor

A **következőket érinti:** Munkamenet-tokenek (állandó és nem állandó)

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhatja a munkamenet-tokent új azonosító és munkamenet-jogkivonat beszerzésére, miután a rendszer a legutóbbi sikeres hitelesítés után több tényezőt is sikeresen hitelesített. Miután egy felhasználó egy új munkamenet-tokent hitelesített és kap, a felhasználó a munkamenet-jogkivonat folyamatát a megadott ideig használhatja. (Ez akkor igaz, ha az aktuális munkamenet-jogkivonat nincs visszavonva, és nem járt le.) A megadott időtartam elteltével a felhasználónak újra hitelesítenie kell magát, hogy új munkamenet-tokent kapjon.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egytényezős hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, javasoljuk, hogy ezt a tulajdonságot olyan értékre állítsa be, amely egyenlő vagy nagyobb, mint az Single-Factor munkamenet-token Max Age tulajdonsága.

## <a name="cmdlet-reference"></a>Parancsmag-referencia

Ezek a parancsmagok a [Azure Active Directory PowerShell a Graph Preview modulhoz](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true).

### <a name="manage-policies"></a>A szabályzatok kezelése

A szabályzatok kezeléséhez a következő parancsmagokat használhatja.

| Parancsmag | Leírás | 
| --- | --- |
| [Új – AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Létrehoz egy új szabályzatot. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Lekéri az összes Azure AD-házirendet vagy egy megadott szabályzatot. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | A Szabályzathoz kapcsolódó összes alkalmazás és szolgáltatásnév beolvasása. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Frissíti a meglévő szabályzatokat. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | A megadott házirend törlése. |

### <a name="application-policies"></a>Tanúsítványhasználati házirend
Az alkalmazás-házirendekhez a következő parancsmagokat használhatja.</br></br>

| Parancsmag | Leírás | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | A megadott szabályzat csatolása egy alkalmazáshoz. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Az alkalmazáshoz rendelt szabályzat beolvasása. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Eltávolít egy szabályzatot egy alkalmazásból. |

### <a name="service-principal-policies"></a>Egyszerű szolgáltatásnév házirendek
A következő parancsmagokat használhatja az egyszerű szolgáltatásnév házirendjeihez.

| Parancsmag | Leírás | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | A megadott házirend csatolása egy egyszerű szolgáltatáshoz. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Lekéri a megadott egyszerű szolgáltatáshoz kapcsolódó házirendet.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Eltávolítja a szabályzatot a megadott egyszerű szolgáltatásból.|

## <a name="license-requirements"></a>Licenckövetelmények

A szolgáltatás használatához prémium szintű Azure AD P1 licenc szükséges. A követelmények megfelelő licencének megkereséséhez tekintse meg [az ingyenes és Prémium kiadások általánosan elérhető funkcióinak összehasonlítását](https://azure.microsoft.com/pricing/details/active-directory/)ismertető témakört.

A [Microsoft 365 vállalati verzió licenccel](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek hozzáférhetnek a feltételes hozzáférési funkciókhoz is.

## <a name="next-steps"></a>Következő lépések

További információért olvassa el [a jogkivonat-élettartamok konfigurálásának példáit](configure-token-lifetimes.md).