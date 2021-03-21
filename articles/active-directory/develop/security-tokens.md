---
title: Biztonsági jogkivonatok | Azure
titleSuffix: Microsoft identity platform
description: Tudnivalók a biztonsági jogkivonatok alapjairól a Microsoft Identity platformon.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795645"
---
# <a name="security-tokens"></a>Biztonsági jogkivonatok

A központosított identitás-szolgáltató különösen olyan alkalmazások esetében hasznos, amelyeken a világ bármely pontján tartózkodó felhasználó található, akik nem feltétlenül jelentkeznek be a vállalati hálózatról. A Microsoft Identity platform hitelesíti a felhasználókat, és biztonsági jogkivonatokat biztosít, például [hozzáférési](developer-glossary.md#access-token)jogkivonatokat, [frissítési](developer-glossary.md#refresh-token)jogkivonatokat és [azonosító](developer-glossary.md#id-token)jogkivonatokat. A biztonsági jogkivonatok lehetővé teszik az [ügyfélalkalmazás](developer-glossary.md#client-application) számára a védett erőforrások elérését egy [erőforrás-kiszolgálón](developer-glossary.md#resource-server).

**Hozzáférési jogkivonat**: a hozzáférési jogkivonat olyan biztonsági jogkivonat, amelyet egy [engedélyezési kiszolgáló](developer-glossary.md#authorization-server) állít ki egy [OAuth 2,0](active-directory-v2-protocols.md) -as folyamat részeként. A felhasználóval és az erőforrással kapcsolatos információkat tartalmaz, amelyekhez a jogkivonat készült. Az információk a webes API-k és más védett erőforrások elérésére használhatók. A hozzáférési jogkivonatokat az erőforrások érvényesítik, hogy hozzáférést biztosítanak egy ügyfélalkalmazás számára. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan bocsát ki hozzáférési jogkivonatokat, tekintse meg a [hozzáférési jogkivonatokat](access-tokens.md).

**Frissítési jogkivonat**: mivel a hozzáférési jogkivonatok csak rövid ideig érvényesek, az engedélyezési kiszolgálók időnként kiállítanak egy frissítési jogkivonatot, amely a hozzáférési jogkivonatot is megadja. Az ügyfélalkalmazás ezután szükség esetén átadhatja ezt a frissítési tokent egy új hozzáférési jogkivonathoz. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan használja a frissítési tokeneket az engedélyek visszavonására, tekintse meg a [jogkivonat-visszavonás](access-tokens.md#token-revocation)című témakört.

**Azonosító jogkivonat**: az id-tokenek az [OpenID Connect](v2-protocols-oidc.md) folyamat részeként továbbítódnak az ügyfélalkalmazás számára. A hozzáférési tokenek mellett vagy ahelyett is elküldhetők. Az ügyfél azonosító jogkivonatokat használ a felhasználó hitelesítéséhez. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan okoz problémát azonosító jogkivonatokat, tekintse meg az [azonosító jogkivonatokat](id-tokens.md).

> [!NOTE]
> Ez a cikk a OAuth2 és az OpenID Connect protokollok által használt biztonsági jogkivonatokat ismerteti. Számos vállalati alkalmazás SAML-t használ a felhasználók hitelesítéséhez. Az SAML-kijelentésekkel kapcsolatos információkért lásd: [Azure Active Directory SAML-jogkivonat referenciája](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Biztonsági jogkivonatok ellenőrzése

Ez az alkalmazás, amelyhez a jogkivonat létrejött, a felhasználó által bejelentkezett webalkalmazás, vagy a jogkivonat érvényesítéséhez hívott webes API. A jogkivonat aláírása titkos kulccsal történik az engedélyezési kiszolgáló aláírásával. Az engedélyezési kiszolgáló közzéteszi a megfelelő nyilvános kulcsot. A jogkivonat érvényesítéséhez az alkalmazás ellenőrzi az aláírást az engedélyezési kiszolgáló nyilvános kulcsával annak ellenőrzéséhez, hogy az aláírás a titkos kulccsal lett-e létrehozva.

A tokenek csak korlátozott időtartamra érvényesek. Az engedélyezési kiszolgáló általában egy pár tokent biztosít, például:

* Egy hozzáférési jogkivonat, amely hozzáfér az alkalmazáshoz vagy a védett erőforráshoz.
* Egy frissítési jogkivonat, amely a hozzáférési jogkivonat frissítéséhez használatos, amikor a hozzáférési jogkivonat le van zárva.

Hozzáférési jogkivonatok átadása egy webes API-nak a fejlécben szereplő tulajdonosi jogkivonatként `Authorization` . Az alkalmazások frissítési jogkivonatot adhatnak az engedélyezési kiszolgálónak. Ha az alkalmazáshoz való hozzáférés nem lett visszavonva, egy új hozzáférési tokent és egy új frissítési jogkivonatot fog kapni. Így történik a vállalatot elhagyó személy forgatókönyvének kezelése. Ha az engedélyezési kiszolgáló megkapja a frissítési jogkivonatot, nem ad ki másik érvényes hozzáférési jogkivonatot, ha a felhasználó már nem engedélyezett.

## <a name="json-web-tokens-and-claims"></a>JSON webes jogkivonatok és jogcímek

A Microsoft Identity platform a *jogcímeket* tartalmazó JSON webes tokenként (JWTs) implementálja a biztonsági jogkivonatokat. Mivel a JWTs biztonsági jogkivonatként használják, ezt a hitelesítési űrlapot néha JWT- *hitelesítésnek* is nevezik.

A [jogcím](developer-glossary.md#claim) egy entitásra, például egy ügyfélalkalmazás vagy [erőforrás-tulajdonosra](developer-glossary.md#resource-owner)vonatkozó kijelentéseket biztosít egy másik entitáshoz, például egy erőforrás-kiszolgálóhoz. A jogcímek JWT jogcímként vagy JSON Web Token jogcímként is szerepelhetnek.

A jogcímek név vagy érték párok, amelyek a token tárgyával kapcsolatos adatokat továbbítanak. Előfordulhat például, hogy egy jogcím az engedélyezési kiszolgáló által hitelesített rendszerbiztonsági tag tényeit tartalmazza. Egy adott jogkivonatban található jogcímek számos dologtól függenek, például a jogkivonat típusától, a tulajdonos hitelesítéséhez használt hitelesítő adatoktól és az alkalmazás konfigurációjával.

Az alkalmazások különféle feladatokhoz használhatnak jogcímeket, például:

* Érvényesítse a jogkivonatot.
* Azonosítsa a jogkivonat tulajdonosának [bérlőjét](developer-glossary.md#tenant).
* Jelenítse meg a felhasználói adatokat.
* Határozza meg a tárgy engedélyét.

A jogcím kulcs-érték párokból áll, amelyek olyan információkat biztosítanak, mint például a:

* A jogkivonatot létrehozó biztonsági jogkivonat-kiszolgáló.
* A jogkivonat létrehozásának dátuma.
* Tárgy (például a felhasználó – a démonok kivételével).
* A célközönség, amely az az alkalmazás, amelyhez a jogkivonat létrejött.
* Az alkalmazás (a-ügyfél), amely a tokent kéri. A webalkalmazások esetében ez az alkalmazás megegyeznek a célközönséggel.

Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan valósítja meg a jogkivonatokat és a jogcímeket, tekintse meg a [hozzáférési tokenek](access-tokens.md) és [azonosító tokenek](id-tokens.md)című témakört

## <a name="how-each-flow-emits-tokens-and-codes"></a>Az egyes folyamatok tokeneket és kódokat bocsátanak ki

Az ügyfél felépítésének módjától függően a Microsoft Identity platform által támogatott hitelesítési folyamatok közül egy (vagy több) is használható. Ezek a folyamatok különböző tokeneket (azonosító jogkivonatokat, frissítési jogkivonatokat, hozzáférési jogkivonatokat) és engedélyezési kódokat hozhatnak létre. A működésük érdekében különböző tokenek szükségesek. Ez a táblázat áttekintést nyújt.

|Folyamat | Igényel | AZONOSÍTÓ token | Hozzáférési jogkivonat | Jogkivonat frissítése | Engedélyezési kód |
|-----|----------|----------|--------------|---------------|--------------------|
|[Engedélyezési kód folyamatábrája](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Implicit folyamat](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hibrid OIDC folyamat](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Jogkivonat-beváltások frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Jogkivonat frissítése | x | x | x| |
|[Meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md) | Hozzáférési jogkivonat| x| x| x| |
|[Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) | | | x (csak alkalmazás)| | |

Az implicit módban kiállított tokenek hossza korlátozott, mert az URL-címen keresztül adják vissza a böngészőnek, ahol a `response_mode` a `query` vagy a `fragment` . Egyes böngészőkben korlátozva van a böngészőablakban elhelyezhető URL-cím mérete, és a művelet nem hajtható végre, ha túl hosszú. Ennek eredményeképpen ezek a jogkivonatok nem rendelkeznek `groups` vagy `wids` jogcímek.

## <a name="next-steps"></a>Következő lépések

A Microsoft Identity platform hitelesítésével és engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

* A hitelesítés és az engedélyezés alapvető fogalmait a [hitelesítés és engedélyezés](authentication-vs-authorization.md)című témakör ismerteti.
* Az alkalmazás integrációs regisztrációjának megismeréséhez lásd: [Application Model](application-model.md).
* A webes, asztali és mobil alkalmazások bejelentkezési folyamatával kapcsolatos információkért lásd: [az alkalmazás bejelentkezési folyamata](app-sign-in-flow.md).
