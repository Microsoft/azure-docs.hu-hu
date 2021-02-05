---
title: Átirányítási URI (válasz URL-cím) korlátozásai | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Identity platform által kényszerített átirányítási URI (válasz URL) formátumra vonatkozó korlátozások és korlátozások leírása.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 11/23/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: 91df89a69368056c1967e641562cf8515f44ade0
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582808"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Átirányítási URI (válasz URL-cím) korlátozásai és korlátozásai

Az átirányítási URI-vagy válasz-URL-cím az a hely, ahol az engedélyezési kiszolgáló elküldi a felhasználót, ha az alkalmazás sikeresen engedélyezte az alkalmazást, és engedélyezte az engedélyezési kódot vagy a hozzáférési jogkivonatot. Az engedélyezési kiszolgáló elküldi a kódot vagy a tokent az átirányítási URI-nak, ezért fontos, hogy regisztrálja a megfelelő helyet az alkalmazás regisztrációs folyamatának részeként.

 Az átirányítási URI-k esetében a következő korlátozások érvényesek:

* Az átirányítási URI-nak a sémával kell kezdődnie `https` . A localhost átirányítási URI-k néhány [kivételt jelentenek](#localhost-exceptions) .

* Az átirányítási URI megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út részeként szerepel `.../abc/response-oidc` , ne adja meg `.../ABC/response-oidc` az átirányítási URI-t. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az elérési utakat, a hozzájuk társított cookie-k `.../abc/response-oidc` kizárható, ha a rendszer átirányítja a kis-és nagybetűket `.../ABC/response-oidc` .

## <a name="maximum-number-of-redirect-uris"></a>Átirányítási URI-k maximális száma

Ez a táblázat a Microsoft Identity platformon az alkalmazások regisztrálásához felvehető átirányítási URI-k maximális számát jeleníti meg.

| Bejelentkezett fiókok | Átirányítási URI-k maximális száma | Description |
|--------------------------|---------------------------------|-------------|
| Microsoft munkahelyi vagy iskolai fiókok bármely szervezet Azure Active Directory (Azure AD) bérlőben | 256 | `signInAudience` az alkalmazás jegyzékfájljában lévő mező beállítása *AzureADMyOrg* vagy *AzureADMultipleOrgs* |
| Személyes Microsoft-fiókok és munkahelyi és iskolai fiókok | 100 | `signInAudience`az alkalmazás jegyzékfájljának mezője *AzureADandPersonalMicrosoftAccount* értékre van állítva |

## <a name="maximum-uri-length"></a>URI maximális hossza

Az alkalmazások regisztrálásához hozzáadott átirányítási URI-azonosítóhoz legfeljebb 256 karaktert használhat.

## <a name="supported-schemes"></a>Támogatott sémák

A Azure Active Directory (Azure AD) alkalmazás-modell jelenleg a HTTP-és HTTPS-sémákat is támogatja olyan alkalmazásokhoz, amelyek bármely szervezet Azure AD-bérlője munkahelyi vagy iskolai fiókjait bejelentkeznek. Ezeket a fiókokat az `AzureADMyOrg` `AzureADMultipleOrgs` `signInAudience` alkalmazás jegyzékfájl mezőjében szereplő és értékek határozzák meg. A személyes Microsoft-fiókokat (MSA) *és* munkahelyi és iskolai fiókokat (azaz a értéket) használó alkalmazások esetében `signInAudience` `AzureADandPersonalMicrosoftAccount` csak a https-séma engedélyezett.

Ha olyan HTTP-sémával rendelkező átirányítási URI-t szeretne hozzáadni, amely munkahelyi vagy iskolai fiókkal jelentkezik be, használja a Azure Portal [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) alkalmazás jegyzékfájl-szerkesztőjét. Azonban előfordulhat, hogy a manifest Editor használatával beállítható a HTTP-alapú átirányítási URI *, ezért javasoljuk* , hogy használja a https-sémát az átirányítási URI-k számára.

## <a name="localhost-exceptions"></a>Localhost kivételek

A 8,3 és [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), valamint a "loopback" vagy "localhost" átirányítási URI-k által [8252-es szakaszban](https://tools.ietf.org/html/rfc8252#section-8.3) két speciális szempontot kell figyelembe venni:

1. `http` Az URI-sémák elfogadhatók, mert az átirányítás soha nem hagyja el az eszközt. Ezért mindkét URI elfogadható:
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. A natív alkalmazások által gyakran igényelt ideiglenes porttartomány miatt a rendszer figyelmen kívül hagyja a port összetevőjét (például `:5001` vagy `:443` ), mert az ÁTirányítási URI-t egyezteti. Ennek eredményeképpen az összes URI-azonosító egyenértékűnek tekintendő:
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Fejlesztési szempontból ez néhány dolgot jelent:

* Ne regisztráljon több átirányítási URI-t, ahol csak a port különbözik. A bejelentkezési kiszolgáló egy tetszőlegesen kiválasztható, és az átirányítási URI-hoz társított viselkedést (például az a `web` -, `native` -vagy a `spa` -Type átirányítás) használja.

    Ez különösen akkor fontos, ha ugyanazt az alkalmazás-regisztrációban eltérő hitelesítési folyamatokat kíván használni, például az engedélyezési kód engedélyezését és az implicit folyamatot. Ahhoz, hogy az egyes átirányítási URI-k megfelelő válaszait társítsa, a bejelentkezési kiszolgálónak képesnek kell lennie az átirányítási URI-k megkülönböztetésére, és nem teheti meg, ha csak a port különbözik.
* Ha több átirányítási URI-t szeretne regisztrálni a localhost-on a különböző folyamatok teszteléséhez a fejlesztés során, akkor az URI *elérési útja* összetevő használatával tegye különbséget azokat. A nem egyezik például a következővel: `http://localhost/MyWebApp` `http://localhost/MyNativeApp` .
* Az IPv6-visszacsatolási cím ( `[::1]` ) jelenleg nem támogatott.

#### <a name="prefer-127001-over-localhost"></a>A 127.0.0.1 előnyben részesítette a localhost felett

Ha meg szeretné akadályozni, hogy az alkalmazás hibás módon konfigurált tűzfalakkal vagy átnevezett hálózati adapterekkel megszakadjon, a helyett használja az `127.0.0.1` átirányítási URI-ban található IP-szövegkonstans-visszacsatolási címet `localhost` . Például: `https://127.0.0.1`.

A Azure Portal **átirányítási URI** -k szövegmezője azonban nem használható olyan visszacsatolási alapú átirányítási URI hozzáadásához, amely a `http` sémát használja:

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="A nem engedélyezett HTTP-alapú visszacsatolási átirányítási URI-t megjelenítő Azure Portal hiba párbeszédpanele":::

Ha a sémát a visszacsatolási címhez használó átirányítási URI-t szeretne hozzáadni `http` `127.0.0.1` , akkor a [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) attribútumot jelenleg módosítania kell az [alkalmazás jegyzékfájljában](reference-app-manifest.md).

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Helyettesítő karakterek korlátozásai az átirányítási URI-k között

Az olyan helyettesítő URI-k, mint például `https://*.contoso.com` kényelmesek, de a biztonsági következmények miatt el kell kerülni őket. Az OAuth 2,0 specifikációnak megfelelően (az[RFC 6749 3.1.2. szakasza](https://tools.ietf.org/html/rfc6749#section-3.1.2)) az átirányítási VÉGPONT URI azonosítójának abszolút URI-nak kell lennie.

A helyettesítő URI-k jelenleg nem támogatottak a személyes Microsoft-fiókokhoz és munkahelyi vagy iskolai fiókokhoz való bejelentkezésre konfigurált alkalmazás-regisztrációkban. A helyettesítő URI-k használata engedélyezett, azonban olyan alkalmazások esetében, amelyek csak munkahelyi vagy iskolai fiókba való bejelentkezésre vannak konfigurálva a szervezet Azure AD-bérlője számára.

A munkahelyi vagy iskolai fiókba bejelentkező alkalmazás-regisztrációhoz helyettesítő URI-azonosítókkal ellátott átirányítási URI-k hozzáadásához használja a Azure Portal [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) alkalmazás jegyzékfájl-szerkesztőjét. Bár lehetséges, hogy egy helyettesítő URI-t a manifest Editor használatával is be kell állítani egy helyettesítő karakterrel *, javasoljuk,* hogy tartsa be az [RFC 6749 3.1.2. szakaszát](https://tools.ietf.org/html/rfc6749#section-3.1.2) , és csak abszolút URI-kat használjon.

Ha a forgatókönyvben a maximálisan megengedettnél több átirányítási URI-t igényel, akkor a helyettesítő karakteres átirányítási URI-k hozzáadása helyett vegye figyelembe a következő [State paramétert](#use-a-state-parameter) .

#### <a name="use-a-state-parameter"></a>Állapot paraméterének használata

Ha több altartománnyal rendelkezik, és a forgatókönyv megköveteli, hogy a sikeres hitelesítés során a felhasználókat ugyanarra az oldalra irányítsa át, amelyről elindították, az állapot paraméterének használata hasznos lehet.

Ebben a megközelítésben:

1. Hozzon létre egy "Shared" átirányítási URI-t az alkalmazásban az engedélyezési végponttól kapott biztonsági jogkivonatok feldolgozásához.
1. Az alkalmazás elküldheti az alkalmazásspecifikus paramétereket (például altartomány URL-címét, ahol a felhasználó származik, vagy bármi más, mint a márkaépítési információ) az állapot paraméterben. A State paraméter használatakor az CSRF elleni védelem az [RFC 6749 10,12. szakaszának](https://tools.ietf.org/html/rfc6749#section-10.12)megfelelően van megadva.
1. Az alkalmazásspecifikus paraméterek tartalmazzák az alkalmazás számára a megfelelő felhasználói élmény megjelenítéséhez szükséges összes információt, azaz a megfelelő alkalmazás-állapotot. Az Azure AD engedélyezési végpontja a HTML-kódot az állapot paraméterből adja meg, ezért ügyeljen arra, hogy ne legyenek átadva a HTML-tartalom ebben a paraméterben.
1. Ha az Azure AD választ küld a "Shared" átirányítási URI-nak, az állapot-paraméter visszakerül az alkalmazásnak.
1. Az alkalmazás ezután használhatja az állapot paraméter értékét annak meghatározásához, hogy melyik URL-címet szeretné elküldeni a felhasználónak. Győződjön meg arról, hogy a CSRF-védelem érvényesítése megtörtént.

> [!WARNING]
> Ez a módszer lehetővé teszi a feltört ügyfél számára, hogy módosítsa az állapot paraméterében eljuttatott további paramétereket, így átirányítja a felhasználót egy másik URL-címre, amely az RFC 6819-ben leírt [nyílt átirányító fenyegetés](https://tools.ietf.org/html/rfc6819#section-4.2.4) . Ezért az ügyfélnek meg kell védenie ezeket a paramétereket az állapot titkosításával vagy más módon történő ellenőrzésével, például az átirányítási URI-n lévő tartománynév érvényesítésével a tokenen.

## <a name="next-steps"></a>Következő lépések

További információ az alkalmazás-regisztrációs [alkalmazás jegyzékéről](reference-app-manifest.md).
