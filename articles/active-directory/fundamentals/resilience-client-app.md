---
title: Növelje a hitelesítés és az engedélyezés rugalmasságát a fejleszthető ügyfélalkalmazások esetében
titleSuffix: Microsoft identity platform
description: Útmutató a hitelesítés és az engedélyezés rugalmasságának növeléséhez az ügyfélalkalmazás számára a Microsoft Identity platform használatával
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: bc3b041e44fad66a4edc6ff34c0e534dc423de86
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226590"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Növelje a hitelesítés és az engedélyezés rugalmasságát a fejleszthető ügyfélalkalmazások esetében

Ez a szakasz útmutatást nyújt a rugalmasság létrehozásához a Microsoft Identity platformot használó ügyfélalkalmazások számára, és Azure Active Directory a felhasználók bejelentkezésére és a műveletek elvégzésére a felhasználók nevében.

## <a name="use-the-microsoft-authentication-library-msal"></a>A Microsoft Authentication Library (MSAL) használata

A Microsoft [-hitelesítési tár (MSAL)](../develop/msal-overview.md) a [Microsoft Identity platform](../develop/index.yml)egyik kulcsfontosságú része. Leegyszerűsíti és kezeli a tokenek beszerzését, kezelését, gyorsítótárazását és frissítését, valamint ajánlott eljárásokat használ a rugalmassághoz. A MSAL úgy lett kialakítva, hogy a fejlesztők nem kell aggódniuk a megvalósítás részleteivel.

A MSAL gyorsítótárazza a tokeneket, és egy csendes jogkivonat-beszerzési mintát használ. Emellett automatikusan szerializálja a jogkivonat-gyorsítótárat olyan platformokon, amelyek natív módon biztosítják a biztonságos tárolást, például a Windows UWP, az iOS és az Android rendszert. A fejlesztők testre szabhatják a szerializálási viselkedést a [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), a [MSAL.net](../develop/msal-net-token-cache-serialization.md), [a MSAL for Java](../develop/msal-java-token-cache-serialization.md)és a [Python MSAL](../develop/msal-python-token-cache-serialization.md)használata esetén.

![Az eszköz és az alkalmazás képe a MSAL használatával a Microsoft Identity hívásához](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

A MSAL használatakor a jogkivonat-gyorsítótárazás, a frissítés és a csendes beszerzések automatikusan támogatottak. A modern hitelesítéshez szükséges jogkivonatok megszerzéséhez egyszerű mintákat használhat. Számos nyelvet támogatunk, és olyan mintát talál, amely megfelel az Ön nyelvének és forgatókönyvének a [minták](../develop/sample-v2-code.md) oldalon.

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

A MSAL bizonyos esetekben proaktív módon frissíthetik a jogkivonatokat. Ha a Microsoft Identity hosszú élettartamú jogkivonatot ad ki, akkor a jogkivonat frissítéséhez ("frissítés a következőben") az ügyfélnek küldheti el az adatokat \_ . A MSAL ezen információk alapján proaktív módon frissíti a jogkivonatot. Az alkalmazás továbbra is futni fog, amíg a régi jogkivonat érvényes, de már van olyan időkerete, amely során egy másik sikeres jogkivonat-beszerzést fog végezni.

### <a name="stay-up-to-date"></a>Maradjon naprakész

A fejlesztőknek folyamattal kell rendelkezniük a legújabb MSAL-kiadásra való frissítéshez. A hitelesítés az alkalmazás biztonságának része, és az alkalmazásnak naprakésznek kell lennie az új MSAL-kiadásokban található biztonsági újításokkal. Ez általában helyes gyakorlat a folyamatos fejlesztés alatt álló könyvtárak esetében, és így biztosíthatja, hogy a legnaprakészebb kód legyen az alkalmazás rugalmassága szempontjából. Mivel a Microsoft Identity továbbra is innovációt biztosít az alkalmazások rugalmassága érdekében, a legújabb MSAL használó alkalmazások lesznek a legjobban felkészülve ezekre az újításokra.

[A legújabb MSAL.js verzió és kibocsátási megjegyzések megtekintése](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[A .NET-es MSAL legújabb verziójának és kibocsátási megjegyzésének megkeresése](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[A legújabb MSAL Python-verzió és kibocsátási megjegyzések megtekintése](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[A Java-verzió legújabb MSAL és kibocsátási megjegyzései](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[Az iOS-és macOS-verziók legújabb MSAL és kibocsátási megjegyzései](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[A MSAL legújabb Android-verziójának és kibocsátási megjegyzései](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[A legújabb MSAL a szögletes változat és a kibocsátási megjegyzések megtekintése](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[A legújabb Microsoft. Identity. Web verzió és kibocsátási megjegyzések megtekintése](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>Rugalmas mintázatok használata a tokenek kezelésére

Ha nem használ MSAL-t, ezeket a rugalmas mintákat használhatja a tokenek kezelésére. Ezeket az ajánlott eljárásokat a MSAL-könyvtár automatikusan alkalmazza. 

Általánosságban elmondható, hogy egy modern hitelesítést használó alkalmazás egy végpontot hív le a felhasználót hitelesítő tokenek lekéréséhez, vagy engedélyezi az alkalmazásnak a védett API-k meghívását. A MSAL célja, hogy kezelni tudja a hitelesítés részleteit, és több mintát implementáljon a folyamat rugalmasságának növelése érdekében. Az ebben a szakaszban ismertetett útmutatás segítségével megvalósíthatja az ajánlott eljárásokat, ha a MSAL eltérő könyvtárat szeretne használni. Ha a MSAL-t használja, az összes ajánlott eljárás ingyenes, mivel a MSAL automatikusan implementálja őket.

### <a name="cache-tokens"></a>Gyorsítótár-tokenek

Az alkalmazásoknak megfelelően kell gyorsítótárazni a Microsoft-identitástól kapott jogkivonatokat. Ha az alkalmazás jogkivonatokat kap, a jogkivonatokat tartalmazó HTTP-válasz egy "expires_in" tulajdonságot is tartalmaz, amely megadja az alkalmazásnak, hogy mennyi ideig kell gyorsítótárazni, és újra kell használni a tokent. Fontos, hogy az alkalmazások a "expires_in" tulajdonságot használják a jogkivonat élettartamának megállapításához. Az alkalmazásnak soha nem kell megkísérelni egy API-hozzáférési jogkivonat dekódolását.

![Egy olyan alkalmazás, amely meghívja a Microsoft Identity-t, de a hívás az alkalmazást futtató eszköz jogkivonat-gyorsítótárán keresztül halad át.](media/resilience-client-app/token-cache.png)

A gyorsítótárazott jogkivonat használatával megelőzhető az alkalmazás és a Microsoft identitása közötti felesleges forgalom, és az alkalmazás a jogkivonat-beszerzési hívások számának csökkentésével kevésbé érzékeny a jogkivonat-beszerzési hibákra. A gyorsítótárazott tokenek is javítják az alkalmazás teljesítményét, mivel az alkalmazásnak le kell tiltania a tokenek kisebb beszerzését. A felhasználó bejelentkezhet az alkalmazásba a jogkivonat élettartamának hosszára.

### <a name="serialize-and-persist-tokens"></a>Tokenek szerializálása és megőrzése

Az alkalmazások biztonságosan szerializálják a jogkivonat-gyorsítótárat, hogy megmaradjanak a jogkivonatok az alkalmazás példányai között. A tokenek újra felhasználhatók, feltéve, hogy érvényes élettartamuk alatt vannak. A [jogkivonatok frissítése](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token), és egyre több [hozzáférési tokent](../develop/access-tokens.md)is kiállítanak. Ez az érvényes idő többször is elindíthat egy felhasználót az alkalmazástól. Az alkalmazás indításakor ellenőrizze, hogy van-e érvényes hozzáférési vagy frissítési jogkivonat. Ez növeli az alkalmazás rugalmasságát és teljesítményét, mivel elkerüli a szükségtelen hívásokat a Microsoft Identity szolgáltatásba.

![Egy olyan alkalmazás, amely meghívja a Microsoft Identity-t, de a hívás egy jogkivonat-gyorsítótárban, valamint egy jogkivonat-tárolón halad át az alkalmazást futtató eszközön.](media/resilience-client-app/token-store.png)

Az állandó jogkivonat-tárolónak hozzáférés-vezérléssel kell rendelkeznie, és titkosítottnak kell lennie a tulajdonos felhasználó vagy folyamat identitásával. A mobil, a Windows és a Mac platformokon a fejlesztőnek ki kell használnia a hitelesítő adatok tárolásához szükséges beépített funkciókat.

### <a name="acquire-tokens-silently"></a>Tokenek csendes beszerzése

A felhasználók hitelesítésének vagy az engedélyek egy API hívására való beolvasásának folyamata több lépést is igényelhet a Microsoft-identitásban. Ha például a felhasználó első alkalommal jelentkezik be, előfordulhat, hogy meg kell adnia a hitelesítő adatokat, és egy szöveges üzenettel kell elvégeznie a többtényezős hitelesítést. Minden lépés egy függőséget ad hozzá a szolgáltatást biztosító erőforráshoz. A felhasználó számára a legjobb megoldás, a legkevesebb függőséggel rendelkezők esetében pedig a tokenek csendes beszerzésének megkísérlése a felhasználói beavatkozás kérelmezése előtt.

![A Microsoft-identitáson belüli különböző szolgáltatásokat bemutató diagram, amely a felhasználó hitelesítésének vagy engedélyezésének befejezéséhez szükséges lehet](media/resilience-client-app/external-dependencies.png)

A jogkivonat beszerzése csendesen kezdődik az alkalmazás jogkivonat-gyorsítótárából érvényes jogkivonat használatával. Ha nem áll rendelkezésre érvényes jogkivonat, az alkalmazásnak egy frissítési jogkivonat használatával kell próbálkoznia a jogkivonat beszerzéséhez, ha az elérhető, és a jogkivonat-végpontot. Ha ezek közül egyik lehetőség sem érhető el, az alkalmazásnak a "kérdés = None" paraméter használatával kell jogkivonatot bekérnie. Ez az engedélyezési végpontot fogja használni, de nem jeleníti meg a felhasználó felhasználói FELÜLETét. Ha a Microsoft-identitás a felhasználóval való interakció nélkül képes jogkivonatot biztosítani az alkalmazásnak, akkor az a következő lesz:. Ha a metódusok egyike sem eredményez tokent, akkor a felhasználónak újra kell hitelesítenie az interaktív hitelesítést.

> [!NOTE]
> Általánosságban elmondható, hogy az alkalmazásoknak kerülniük kell a "Bejelentkezés" és a "hozzájárulás" utasítás használatát, mivel a felhasználói beavatkozást akkor is kényszerítik, ha nincs szükség beavatkozásra.

## <a name="handle-service-responses-properly"></a>A szolgáltatás megfelelő válaszait kezeli

Habár az alkalmazásoknak az összes hibaüzenetet kezelnie kell, vannak olyan válaszok, amelyek hatással lehetnek a rugalmasságra. Ha az alkalmazás HTTP 429-hibakódot kap, túl sok kérést, a Microsoft Identity szabályozza a kérelmeket. Ha az alkalmazás továbbra is túl sok kérést hajt végre, továbbra is érvényben marad, hogy megakadályozza az alkalmazásnak a jogkivonatok fogadását. Az alkalmazásnak nem kell újra megadnia a tokent addig, amíg a Retry-After válasz mezőjében szereplő idő elteltével nem próbálkozott. Az 429-es válasz fogadása gyakran arra utal, hogy az alkalmazás nem gyorsítótárazza és nem használja helyesen a jogkivonatokat. A fejlesztőknek át kell tekinteniük a tokenek gyorsítótárazásának és az alkalmazásban való újrafelhasználásának módját.

Ha egy alkalmazás HTTP-5xx választ kap, akkor az alkalmazás nem adhat meg gyors újrapróbálkozási ciklust. Ha van ilyen, az alkalmazásnak ugyanazt a Retry-After kezelését kell figyelembe vennie, mint a 429-es válasz esetében. Ha a válasz nem biztosít Retry-After fejlécet, javasoljuk, hogy az első újrapróbálkozás után legalább 5 másodperccel próbálkozzon újra az Újrapróbálkozással.

Ha egy kérelem időtúllépése esetén az alkalmazások nem tudnak azonnal újrapróbálkozni. Hozzon létre egy exponenciális visszalépési újrapróbálkozást az első újrapróbálkozás után legalább 5 másodperccel a válasz után.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Az engedélyezéssel kapcsolatos információk beolvasására vonatkozó beállítások kiértékelése

Számos alkalmazásnak és API-nak konkrét információra van szüksége a felhasználóról az engedélyezési döntések elvégzéséhez. Az alkalmazások számára többféleképpen is megtudhatja ezt az információt. Minden módszernek megvan az előnyei és hátrányai. A fejlesztőknek meg kell mérniük, hogy melyik stratégia a legmegfelelőbb a rugalmassághoz az alkalmazásban.

### <a name="tokens"></a>Tokenek

Az Identity (ID) tokenek és a hozzáférési tokenek standard jogcímeket tartalmaznak, amelyek a tárgyra vonatkozó információkat biztosítanak. Ezek a [Microsoft Identity platform azonosító jogkivonatokban](../develop/id-tokens.md) és a [Microsoft Identity platform hozzáférési jogkivonatokban](../develop/access-tokens.md)vannak dokumentálva. Ha az alkalmazás által igényelt információk már szerepelnek a jogkivonatban, akkor az adatok lekérésének leghatékonyabb módszere a jogkivonat-jogcímek használata, mivel a rendszer a további hálózati hívásokat a további adatok lekérésére fogja menteni. Kevesebb hálózati hívás az alkalmazás számára nagyobb mértékű teljes rugalmasságot jelent.

> [!NOTE]
> Egyes alkalmazások meghívja a UserInfo-végpontot a hitelesített felhasználó jogcímeinek lekéréséhez. Az alkalmazás által fogadott azonosító jogkivonatban elérhető információk a UserInfo-végpontból beolvasható információk egy részét képezik. Az alkalmazásnak az azonosító jogkivonatot kell használnia, hogy az UserInfo-végpont meghívása helyett adatokat kérjen a felhasználótól.

Egy alkalmazás fejlesztője a standard jogkivonatok jogcímeit a [választható jogcímek](../develop/active-directory-optional-claims.md)használatával bővítheti. Egy gyakori választható jogcím a [csoportok](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims). A csoportos jogcímek hozzáadásának számos módja van. Az "Application Group" beállítás csak az alkalmazáshoz rendelt csoportokat tartalmazza. Az "összes" vagy "biztonsági csoport" lehetőség az ugyanabban a bérlőben lévő összes alkalmazásból származó csoportokat is tartalmazza, így számos csoportot adhat hozzá a tokenhez. Fontos, hogy kiértékelje az adott eset hatását, mivel az a jogkivonat-közelítést okozó csoportok bekérésével, valamint a csoportok teljes listájának lekéréséhez szükséges további hívásokkal is megtagadhatja a jogkivonatban rejlő hatékonyságot.

A jogkivonatban lévő csoportok használata helyett használhatja az alkalmazáshoz tartozó szerepköröket is. A fejlesztők meghatározhatják az alkalmazásaikat és az API- [kat, amelyeket](../develop/howto-add-app-roles-in-azure-ad-apps.md) az ügyfél a címtárból a portál vagy API-k használatával kezelhet. Az informatikai szakemberek ezután különböző felhasználókhoz és csoportokhoz rendelhetnek szerepköröket annak szabályozásához, hogy ki férhet hozzá a tartalomhoz és a funkciókhoz. Ha jogkivonatot ad ki az alkalmazáshoz vagy az API-hoz, a felhasználóhoz rendelt szerepkörök elérhetők lesznek a jogkivonat roles jogcímében. Ezek az információk közvetlenül a jogkivonatokban való beszerzése további API-hívásokat menthet.

Végezetül a rendszergazdák hozzáadhatnak jogcímeket a bérlő adott információi alapján is. Egy vállalat például rendelkezhet egy adott vállalati felhasználói AZONOSÍTÓval rendelkező bővítménnyel.

Az adatoknak a címtárból közvetlenül a jogkivonatba való hozzáadásának minden esetben hatékony lehet, és növelheti az alkalmazások rugalmasságát azáltal, hogy csökkenti az alkalmazáshoz tartozó függőségek számát. Másfelől azonban nem foglalkozik semmilyen, a tokenek beszerzését nem okozó rugalmassági problémával. Az alkalmazás főbb forgatókönyvei esetében csak opcionális jogcímeket adjon hozzá. Ha az alkalmazás csak a rendszergazdai funkciókra vonatkozó információkat igényel, akkor a legjobb megoldás, ha az alkalmazás csak szükség szerint szerzi be ezeket az információkat.

### <a name="microsoft-graph"></a>Microsoft Graph

A Microsoft Graph egy egységes API-végpontot biztosít a munkahelyen a termelékenység, az identitás és a biztonság mintáit leíró Microsoft 365 adatok eléréséhez. Az Microsoft Grapht használó alkalmazások a Microsoft 365 az engedélyezési döntésekhez bármelyik információt használhatják.

Az alkalmazások csak egyetlen tokent igényelnek az összes Microsoft 365 eléréséhez. Ez rugalmasabb, mint a régebbi API-k használata, amelyek olyan Microsoft 365-összetevőkhöz tartoznak, mint a Microsoft Exchange vagy a Microsoft SharePoint, ahol több tokenre van szükség.

Microsoft Graph API-k használatakor javasoljuk, hogy használjon egy [Microsoft Graph SDK](/graph/sdks/sdks-overview)-t. A Microsoft Graph SDK-k úgy lettek kialakítva, hogy leegyszerűsítsék a Microsoft Graphhoz hozzáférő, magas színvonalú, hatékony és rugalmas alkalmazásokat.

Az engedélyezési döntések meghozatalához a fejlesztőknek érdemes megfontolniuk, hogy mikor használják a jogkivonatokban elérhető jogcímeket egy Microsoft Graph-hívás alternatívájaként. A fentiekben említettek szerint a fejlesztők csoportokat, alkalmazás-szerepköröket és opcionális jogcímeket igényelhetnek a jogkivonatokban. A rugalmasság szempontjából a Microsoft Graph for Authorization használata olyan további hálózati hívásokat igényel, amelyek Microsoft-identitásra támaszkodnak (a tokennek a Microsoft Graph eléréséhez), valamint Microsoft Graph magát. Ha azonban az alkalmazás az adatrétege már Microsoft Graphra támaszkodik, akkor az engedélyezéshez használt gráfra való támaszkodás nem jelent további kockázatot.

## <a name="use-broker-authentication-on-mobile-devices"></a>A Broker-hitelesítés használata mobileszközökön

A mobileszközök esetében a hitelesítési közvetítő (például Microsoft Authenticator) használatával javul a rugalmasság. A közvetítő előnyt biztosít a többi lehetőséggel (például a rendszerböngészővel vagy egy beágyazott webnézettel) kapcsolatban. A hitelesítési közvetítő olyan [elsődleges frissítési tokent](../devices/concept-primary-refresh-token.md) (PRT) is használhat, amely a felhasználóval és az eszközzel kapcsolatos jogcímeket tartalmaz, és a hitelesítési jogkivonatok lekérésére használható a más alkalmazásokhoz való hozzáféréshez az eszközről. Ha egy PRT-t használ az alkalmazáshoz való hozzáférés kérelmezéséhez, az eszközét és az MFA jogcímeit az Azure AD megbízhatónak tekinti. Ez növeli a rugalmasságot azáltal, hogy elkerüli az eszköz ismételt hitelesítésének további lépéseit. A felhasználóknak nem kell több MFA-kéréssel foglalkoznia ugyanazon az eszközön, így a rugalmasságot a külső szolgáltatásokkal kapcsolatos függőségek csökkentésével és a felhasználói élmény fokozásával.

![Egy olyan alkalmazás, amely meghívja a Microsoft Identity-t, de a hívás egy jogkivonat-gyorsítótáron, valamint egy jogkivonat-tárolón és egy, az alkalmazást futtató eszközön található hitelesítési közvetítőn keresztül halad át.](media/resilience-client-app/authentication-broker.png)

A MSAL automatikusan támogatja a közvetítői hitelesítést. A következő lapokon talál további információt a felügyelt hitelesítés használatával kapcsolatban:

- [SSO konfigurálása macOS és iOS rendszeren](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [Alkalmazások közötti egyszeri bejelentkezés engedélyezése Androidon a MSAL használatával](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>Folyamatos hozzáférés kiértékelésének elfogadása

A [folyamatos hozzáférés kiértékelése (CAE)](../conditional-access/concept-continuous-access-evaluation.md) egy közelmúltbeli fejlesztés, amely az alkalmazások biztonságának és rugalmasságának növelését is képes a hosszú élettartamú jogkivonatokkal. A CAE egy új iparági szabvány, amely az OpenID Foundation közös jelek és események munkacsoportjában lett kifejlesztve. A CAE-mel a hozzáférési tokenek a [kritikus események](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) és a [házirendek kiértékelése](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview)alapján vonhatók vissza, nem pedig egy rövid jogkivonat élettartama. Egyes erőforrás-API-k esetében, mivel a kockázat és a házirend valós időben lett kiértékelve, a CAE jelentősen növelheti a token élettartamát akár 28 óráig is. Ha az erőforrás-API-k és-alkalmazások a CAE-t fogadják el, a Microsoft identitása visszavonhatja a visszavont hozzáférési jogkivonatokat, és hosszabb ideig érvényesek lesznek. Ezek a hosszú élettartamú tokenek proaktív módon frissülnek a MSAL.

Habár a CAE a korai fázisokban van, olyan ügyfélalkalmazások fejlesztésére is lehetőség van, amelyek az alkalmazás által használt erőforrások (API-k) alkalmazása által [igénybe vehető](../develop/app-resilience-continuous-access-evaluation.md) CAE-t használják. Ahogy egyre több erőforrás kerül be a CAE-be, az alkalmazása a CAE-kompatibilis tokeneket is megvásárolhatja az adott erőforrásokhoz. A Microsoft Graph API és a [Microsoft Graph SDK](/graph/sdks/sdks-overview)-k előnézete az CAE-képesség elején 2021. Ha szeretne részt venni a Microsoft Graph a CAE-vel való nyilvános előzetesében, a következőket teheti: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview)

Ha erőforrás-API-kat fejleszt, javasoljuk, hogy vegyen részt a [megosztott jelek és események WG](https://openid.net/wg/sse/)-ben. Ezzel a csoporttal együttműködve engedélyezheti a Microsoft identitás-és erőforrás-szolgáltatói közötti biztonsági események megosztását.

## <a name="next-steps"></a>Következő lépések

- [A folyamatos hozzáférés kiértékelését engedélyező API-k használata az alkalmazásokban](../develop/app-resilience-continuous-access-evaluation.md)
- [Rugalmasság létrehozása Daemon-alkalmazásokban](resilience-daemon-app.md)
- [Hozzon létre rugalmasságot az identitás-és hozzáférés-kezelési infrastruktúrában](resilience-in-infrastructure.md)
- [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)
