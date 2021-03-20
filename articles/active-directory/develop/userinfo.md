---
title: Microsoft Identity platform UserInfo-végpont | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a UserInfo-végpontot a Microsoft Identity platformon.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752731"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft Identity platform UserInfo-végpont

Az UserInfo végpont az [OpenID Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) része, amely a hitelesített felhasználó jogcímeinek visszaküldésére szolgál.  A Microsoft Identity platform esetében az UserInfo-végpontot a Microsoft Graph () üzemelteti https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>A. well-known konfigurációs végpont megkeresése

A UserInfo végpontot programozott módon derítheti fel az OpenID Connect Discovery-dokumentum használatával `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Ez a mező szerepel a `userinfo_endpoint` mezőben, és ez a minta használható a felhők között, hogy a jobb oldali végpontra mutasson.  Nem javasoljuk, hogy a UserInfo-végpontot az alkalmazásban használja – a OIDC-felderítési dokumentum használatával megkeresheti a végpontot futásidőben.

Az OpenID Connect specifikációjának részeként a UserInfo-végpontot gyakran automatikusan meghívja a [OIDC-kompatibilis kódtárak](https://openid.net/developers/certified/)  a felhasználó adatainak lekéréséhez.  Az ilyen végpontok üzemeltetése nélkül a Microsoft Identity platform nem lenne szabványoknak megfelelő, és egyes könyvtárak sikertelenek lesznek.  A [OIDC standardban azonosított jogcímek listájáról](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) a jogcímek, a tulajdonosi jogcímek és az e-mailek, ha elérhetők, és a hozzájárulása is elő van állítva.  

## <a name="consider-use-an-id-token-instead"></a>Megfontolandó: azonosító jogkivonat használata

Az alkalmazás által fogadott azonosító jogkivonatban elérhető információk a UserInfo-végpontból beolvasható információk egy részét képezik.  Mivel a UserInfo-végpont meghívásához egy tokent kap, az azonosító jogkivonat beszerzése esetén azt javasoljuk, hogy a UserInfo-végpont meghívása helyett az azonosító tokent használja a felhasználó adatainak lekérésére.  Az azonosító jogkivonat használatával az alkalmazás indításával egy-két hálózati kérelem törlődik, ami csökkenti az alkalmazás késését.

Ha további részletekre van szüksége a felhasználóról, hívja meg a [Microsoft Graph `/user` API](/graph/api/user-get) -t, hogy olyan információkat kapjon, mint az Office Number vagy a Job title.   A [választható jogcímek](active-directory-optional-claims.md) használatával további felhasználói adatokat is HOZZÁADHAT az azonosítóhoz és a hozzáférési jogkivonatokhoz.

## <a name="calling-the-userinfo-endpoint"></a>Az UserInfo-végpont meghívása

A UserInfo egy standard OAuth-tulajdonosi jogkivonat API, mint bármely más Microsoft Graph API-hoz, amely az Microsoft Graph token beszerzése során kapott hozzáférési jogkivonatot használja. Egy JSON-választ ad vissza, amely a felhasználóval kapcsolatos jogcímeket tartalmaz.

### <a name="permissions"></a>Engedélyek

A UserInfo API meghívásához használja az alábbi [OIDC-engedélyeket](v2-permissions-and-consent.md#openid-connect-scopes) . `openid` kötelező, és a `profile` és a `email` hatókörök biztosítják, hogy a válaszban további információk is elérhetők legyenek.

|Engedély típusa      | Engedélyek    |
|:--------------------|:---------------------------------------------------------|
|Delegált (munkahelyi vagy iskolai fiók) | OpenID (kötelező), profil, e-mail |
|Delegált (személyes Microsoft-fiók) | OpenID (kötelező), profil, e-mail |
|Alkalmazás | Nem alkalmazható |

> [!TIP]
> Másolja ezt az URL-címet a böngészőjében, és szerezzen be egy tokent a UserInfo-végponthoz, valamint egy [azonosító jogkivonatot](id-tokens.md) , és cserélje le az ügyfél-azonosítót és az ÁTirányítási URI-t. Vegye figyelembe, hogy csak az OpenID vagy Graph hatókörökhöz kér hatókört, és semmi mást sem.  Erre azért van szükség, mert az ugyanazon jogkivonat-kérelemben szereplő két különböző erőforráshoz nem kérhet engedélyeket.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> Ezt a hozzáférési jogkivonatot a következő szakaszban használhatja.

Más Microsoft Graph-tokenekhez hasonlóan előfordulhat, hogy az itt kapott token nem JWT. Ha Microsoft-fiók felhasználóhoz jelentkezett be, akkor titkosított jogkivonat-formátum lesz. Ennek az az oka, hogy Microsoft Graph speciális jogkivonat-kiállítási mintát tartalmaz. Ez nem befolyásolja a hozzáférési jogkivonat használatát a UserInfo végpont meghívásához.

### <a name="calling-the-api"></a>Az API meghívása

A UserInfo API a GET és a POST kombinációt is támogatja a OIDC specifikáció alapján.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>UserInfo válasz

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

Az itt felsorolt jogcímek tartalmazzák az összes olyan jogcímet, amelyet az UserInfo-végpont vissza tud adni.  Ezek ugyanazok az értékek, amelyeket az alkalmazás az alkalmazás számára kiállított [azonosító jogkivonatban](id-tokens.md) láthat.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>Megjegyzések és kikötések a UserInfo-végponton

* Ha meg szeretné hívni ezt a UserInfo-végpontot, a v 2.0-s végpontot kell használnia.  Ha a 1.0-s végpontot használja, a rendszer a login.microsoftonline.com-on üzemeltetett v 1.0 UserInfo-végpont tokenjét kapja meg.  Javasoljuk, hogy az összes OIDC-kompatibilis alkalmazás és könyvtár a v 2.0-s végpontot használja a kompatibilitás biztosításához.
* A UserInfo végpont válasza nem szabható testre.  Ha testre szeretné szabni a jogcímeket, használja a [jogcímek leképezését]( active-directory-claims-mapping.md) a jogkivonatokban visszaadott információk szerkesztéséhez.
* A UserInfo-végpont válasza nem adható hozzá a következőhöz:.  Ha további jogcímeket szeretne kapni a felhasználóval kapcsolatban, használja a [választható jogcímeket]( active-directory-optional-claims.md) , hogy új jogcímeket adjon hozzá a jogkivonatokhoz.

## <a name="next-steps"></a>Következő lépések

* [Az azonosító tokenek tartalmának áttekintése](id-tokens.md)
* [AZONOSÍTÓ jogkivonat tartalmának testreszabása választható jogcímek használatával](active-directory-optional-claims.md)
* [Hozzáférési jogkivonat és azonosító jogkivonat kérése a OAuth2 protokoll használatával](v2-protocols-oidc.md)
