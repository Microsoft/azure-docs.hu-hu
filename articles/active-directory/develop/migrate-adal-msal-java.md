---
title: ADAL a MSAL áttelepítési útmutatóhoz (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan telepítheti át Azure Active Directory Authentication Library (ADAL) Java-alkalmazását a Microsoft Authentication Library-be (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 620f77655f8281919ba0831a7e53af8cd28bc5f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583993"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>ADAL a MSAL áttelepítési útmutató Javához

Ez a cikk azokat a módosításokat mutatja be, amelyeket a Azure Active Directory hitelesítési függvénytárat (ADAL) használó alkalmazások áttelepíteni kell a Microsoft Authentication Library (MSAL) használatára.

A Javához készült Microsoft Authentication Library (MSAL4J) és az Azure AD Authentication Library for Java (ADAL4J) egyaránt az Azure AD-entitások hitelesítésére és az Azure AD-jogkivonatok igénylésére szolgál. Eddig a legtöbb fejlesztő dolgozott együtt az Azure ad for Developers platformmal (v 1.0) az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítéséhez az Azure AD Authentication Library (ADAL) használatával.

A MSAL a következő előnyöket kínálja:

- Mivel az újabb Microsoft Identity platformot használja, a Microsoft-identitások, például az Azure AD-identitások, a Microsoft-fiókok és a közösségi és helyi fiókok szélesebb körét hitelesítheti az Azure AD Business a Consumer (B2C) szolgáltatáson keresztül.
- A felhasználók a legjobb egyszeri bejelentkezési élményt kapják meg.
- Az alkalmazás lehetővé teszi a növekményes hozzáférést, és megkönnyíti a feltételes hozzáférés támogatását.

A Javához készült MSAL a Microsoft Identity platform használatával javasolt hitelesítési függvénytár. A ADAL4J-on nem lesznek új funkciók implementálva. Minden, a jövőre irányuló erőfeszítés a MSAL javítására koncentrál.

## <a name="differences"></a>Eltérések

Ha az Azure AD for Developers (1.0) végpont (és a ADAL4J) használatával dolgozik, érdemes elolvasnia, hogy [Mi a különbség a Microsoft Identity platformmal kapcsolatban?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="scopes-not-resources"></a>Hatókörök nem erőforrásai

A ADAL4J az erőforrások jogkivonatait szerzi be, míg a Java-MSAL a hatókörökhöz tartozó jogkivonatokat vásárol. A Java-osztályokhoz számos MSAL szükséges. Ez a paraméter a szükséges engedélyeket és erőforrásokat deklaráló karakterláncok listája. Tekintse [meg a Microsoft Graph hatókörét](/graph/permissions-reference) a példa hatókörök megjelenítéséhez.

A `/.default` hatókör utótagját hozzáadhatja az erőforráshoz, hogy az alkalmazások áttelepíthetők legyenek a ADAL a MSAL. A (z) erőforrás értékének `https://graph.microsoft.com` megfelelő hatókör értéke például a következő: `https://graph.microsoft.com/.default` .  Ha az erőforrás nem szerepel az URL-címben, de az űrlap erőforrás-azonosítója `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , továbbra is használhatja a hatókör értékét `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

A különböző típusú hatókörökkel kapcsolatos további részletekért tekintse [meg a Microsoft Identity platform engedélyeit és](./v2-permissions-and-consent.md) a hozzájuk tartozó jogosultságokat, valamint a webes API-k 1.0-s verzióinak [elfogadására vonatkozó](./msal-v1-app-scopes.md) cikkeket.

## <a name="core-classes"></a>Alapvető osztályok

A ADAL4J-ben az `AuthenticationContext` osztály a biztonsági jogkivonat szolgáltatással (STS) vagy az engedélyezési kiszolgálóval létesített kapcsolatát jelenti a szolgáltatón keresztül. A Java-hoz készült MSAL azonban ügyfélalkalmazások köré épülnek. Két különálló osztályt biztosít: `PublicClientApplication` `ConfidentialClientApplication` az ügyfélalkalmazások képviseletére.  Ez utóbbi `ConfidentialClientApplication` egy olyan alkalmazást jelent, amely biztonságos karbantartást, például egy Daemon-alkalmazás alkalmazás-azonosítóját jelöli.

A következő táblázat azt mutatja be, hogy a ADAL4J functions hogyan képezhető le a Java functions új MSAL:

| ADAL4J metódus| MSAL4J metódus|
|------|-------|
|acquireToken (karakterlánc-erőforrás, ClientCredential hitelesítő adat, AuthenticationCallback visszahívás) | acquireToken(ClientCredentialParameters)|
|acquireToken (karakterlánc-erőforrás, ClientAssertion-érvényesítés, AuthenticationCallback visszahívás)|acquireToken(ClientCredentialParameters)|
|acquireToken (karakterlánc-erőforrás, AsymmetricKeyCredential hitelesítő adat, AuthenticationCallback visszahívás)|acquireToken(ClientCredentialParameters)|
|acquireToken (karakterlánc-erőforrás, karakterlánc clientId, karakterlánc felhasználóneve, karakterlánc jelszava, AuthenticationCallback visszahívás)| acquireToken(UsernamePasswordParameters)|
|acquireToken (karakterlánc-erőforrás, karakterlánc clientId, karakterlánc felhasználóneve, karakterlánc jelszava = null, AuthenticationCallback visszahívás)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken (karakterlánc-erőforrás, UserAssertion userAssertion, ClientCredential hitelesítő adat, AuthenticationCallback visszahívás)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode () és acquireTokenByDeviceCode ()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount helyett IUser

ADAL4J manipulált felhasználók. Bár a felhasználók egyetlen emberi vagy szoftveres ügynököt jelölnek, a Microsoft Identity rendszer egy vagy több fiókja is lehet. Előfordulhat például, hogy egy felhasználó több Azure AD-, Azure AD B2C-vagy személyes Microsoft-fiókkal rendelkezik.

A Javához készült MSAL a felhasználói felületen keresztül határozza meg a fiók fogalmát `IAccount` . Ez egy ADAL4J-változás, de ez egy jó megoldás, mivel ez azt a tényt rögzíti, hogy ugyanaz a felhasználó több fiókkal is rendelkezhet, és talán akár különböző Azure AD-címtárakban is. A MSAL for Java jobb információkat biztosít a vendég forgatókönyvekhez, mert a rendszer megadja az otthoni fiók adatait.

## <a name="cache-persistence"></a>Gyorsítótár-megőrzés

A ADAL4J nem támogatta a jogkivonat-gyorsítótárat.
A MSAL for Java egy [jogkivonat-gyorsítótárat](msal-acquire-cache-tokens.md) hoz létre a jogkivonat-élettartamok kezelésének egyszerűsítése érdekében, ha lehetséges, automatikusan frissíti a lejárt jogkivonatokat, és megakadályozza, hogy a felhasználó a hitelesítő adatok megadását, amikor lehetséges

## <a name="common-authority"></a>Általános szolgáltató

A v 1.0-s verzióban, ha a `https://login.microsoftonline.com/common` hatóságot használja, a felhasználók bármilyen Azure Active Directory-(HRE-) fiókkal bejelentkezhetnek (bármely szervezet esetében).

Ha a szolgáltatót a 2.0-s `https://login.microsoftonline.com/common` verzióban használja, a felhasználók bármely HRE-szervezettel, vagy akár egy személyes Microsoft-fiókkal (MSA) is bejelentkezhetnek. Ha a MSAL for Java esetében szeretné korlátozni a bejelentkezést bármely HRE-fiókra, használja a szolgáltatót `https://login.microsoftonline.com/organizations` (ez ugyanaz, mint a ADAL4J esetében). A szolgáltató megadásához állítsa a `authority` paramétert a [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) metódusban az osztály létrehozásakor `PublicClientApplication` .

## <a name="v10-and-v20-tokens"></a>1.0-s és v 2.0-tokenek

A ADAL által használt v 1.0 végpont csak v 1.0 jogkivonatokat bocsát ki.

A v 2.0-s végpont (a MSAL által használt) 1.0-s és v 2.0-tokeneket bocsát ki. A webes API alkalmazási jegyzékfájljának egyik tulajdonsága lehetővé teszi a fejlesztők számára, hogy a jogkivonat melyik verzióját fogadják el. Tekintse `accessTokenAcceptedVersion` meg az [alkalmazás jegyzékfájljának](./reference-app-manifest.md) dokumentációját.

A 1.0-s és a 2.0-s verziókkal kapcsolatos további információkért lásd: [Azure Active Directory hozzáférési tokenek](./access-tokens.md).

## <a name="adal-to-msal-migration"></a>ADAL a MSAL áttelepítéséhez

A ADAL4J a frissítési jogkivonatok elérhetők – ami lehetővé tette a fejlesztők számára a gyorsítótárazást. Ezeket a megoldásokat olyan megoldások engedélyezésére használják, mint például a `AcquireTokenByRefreshToken()` hosszan futó szolgáltatások megvalósítása, amelyek az irányítópultokat a felhasználó nevében frissítik, ha a felhasználó már nem csatlakozik.

A MSAL for Java nem teszi lehetővé biztonsági okokból a frissítési jogkivonatokat. Ehelyett a MSAL kezeli a frissítő tokeneket.

A MSAL for Java egy API-val rendelkezik, amellyel áttelepítheti a ADAL4j-ben szerzett frissítési jogkivonatokat a ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)értékre. Ezzel a módszerrel megadhatja a korábban használt frissítési jogkivonatot a kívánt hatókörökkel (erőforrásokkal) együtt. A frissítési tokent egy újat cseréli a rendszer, és az alkalmazás által használt gyorsítótárazza.

Az alábbi kódrészlet egy bizalmas ügyfélalkalmazás áttelepítési kódját mutatja be:

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

A `IAuthenticationResult` visszaadja a hozzáférési jogkivonatot és az azonosító tokent, míg az új frissítési jogkivonat a gyorsítótárban tárolódik.
Az alkalmazás ekkor is tartalmaz egy IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

A gyorsítótárban lévő tokenek használatához hívja a következőt:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
