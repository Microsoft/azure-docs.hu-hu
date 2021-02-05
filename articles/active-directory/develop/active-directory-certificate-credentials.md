---
title: A Microsoft Identity platform tanúsítványának hitelesítő adatai
titleSuffix: Microsoft identity platform
description: Ez a cikk a tanúsítvány hitelesítő adatainak regisztrálását és használatát ismerteti az alkalmazás hitelesítéséhez.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfbcc8523ff1d5858317a3654b58ec7b2d23607a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582026"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft Identity platform-alkalmazás hitelesítési tanúsítványának hitelesítő adatai

A Microsoft Identity platform lehetővé teszi, hogy az alkalmazások a saját hitelesítő adataikat használják hitelesítésre, bárhol is használhatók legyenek, például a OAuth 2,0  [ügyfél-hitelesítő adatok megadási](v2-oauth2-client-creds-grant-flow.md) folyamata és a [befelé irányuló](v2-oauth2-on-behalf-of-flow.md) (OBO) folyamat.

Az egyik hitelesítő adat, amelyet az alkalmazás használhat a hitelesítéshez, egy [JSON web token](./security-tokens.md#json-web-tokens-and-claims) (JWT)-állítás, amely az alkalmazás tulajdonában lévő tanúsítvánnyal van aláírva.

## <a name="assertion-format"></a>Érvényesítési formátum

Az állítás kiszámításához használhatja a számos JWT-függvénytár egyikét az Ön által választott nyelven, a [MSAL támogatja ezt `.WithCertificate()` ](msal-net-client-assertions.md)a lehetőséget. Az adatokat a token a [fejlécben](#header), a [jogcímek](#claims-payload)és az [aláírás](#signature)alapján végzi el.

### <a name="header"></a>Fejléc

| Paraméter |  Megjegyzés |
| --- | --- |
| `alg` | **RS256** kell lennie |
| `typ` | **JWT** kell lennie |
| `x5t` | Az X. 509 tanúsítvány kivonatának (más néven a CERT SHA-1 *ujjlenyomata*) hexadecimális ábrázolása Base64url. Például egy X. 509 tanúsítvány kivonata `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (hexadecimális) esetén a `x5t` jogcím a következő: `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url). |

### <a name="claims-payload"></a>Jogcímek (hasznos adatok)

Jogcím típusa | Érték | Leírás
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Az "AUD" (célközönség) jogcím azonosítja azokat a címzetteket, amelyeket a JWT szánnak (itt az Azure AD-t) lásd: [RFC 7519, 4.1.3. szakasz](https://tools.ietf.org/html/rfc7519#section-4.1.3).  Ebben az esetben ez a címzett a bejelentkezési kiszolgáló (login.microsoftonline.com).
exp | 1601519414 | Az "exp" (lejárati idő) jogcím azt a lejárati időt azonosítja, amely után a JWT nem fogadható el feldolgozásra. Lásd: [RFC 7519, 4.1.4. szakasz](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Ez lehetővé teszi, hogy a rendszer addig használja az állítást, hogy a lehető legrövidebb idő elteltével 5-10 perccel `nbf` .  Az Azure AD nem korlátozza az `exp` aktuális időpontra vonatkozó korlátozásokat. 
ISS | ClientID | Az "ISS" (kibocsátói) jogcím azonosítja a JWT kiállító rendszerbiztonsági tag, ebben az esetben az ügyfélalkalmazás.  Használja a GUID-alkalmazás AZONOSÍTÓját.
JTI | (GUID) | A "kezdeményezés" (JWT ID) jogcím egyedi azonosítót biztosít a JWT számára. Az azonosító értékét olyan módon kell hozzárendelni, amely biztosítja, hogy az adott érték egy másik adatobjektumhoz is legyen véletlenül hozzárendelve. Ha az alkalmazás több kiállítót használ, az ütközéseket meg kell akadályozni a különböző kibocsátók által előállított értékek között. A "kezdeményezés" érték kis-és nagybetűket megkülönböztető karakterlánc. [RFC 7519, 4.1.7 szakasz](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | A "NBF" (nem korábban) jogcím azt az időpontot határozza meg, ameddig a JWT nem fogadható el a feldolgozáshoz. [RFC 7519, 4.1.5 szakasz](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Az aktuális idő használata megfelelő. 
Sub | ClientID | A "Sub" (tárgy) jogcím azonosítja a JWT tárgyát, ebben az esetben az alkalmazást is. Használja ugyanazt az értéket `iss` . 

### <a name="signature"></a>Aláírás

Az aláírás kiszámítása a tanúsítvány [JSON web token RFC7519-specifikációban](https://tools.ietf.org/html/rfc7519)leírt módon történő alkalmazásával történik.

## <a name="example-of-a-decoded-jwt-assertion"></a>Dekódolású JWT-érvényesítési példa

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Példa kódolt JWT-kijelentésre

A következő karakterlánc egy példa a kódolt állításra. Ha alaposan meggondolja, három szakaszt pont () választja el egymástól `.` :

* Az első szakasz kódolja a *fejlécet*
* A második szakasz kódolja a *jogcímeket* (hasznos adatok)
* Az utolsó szakasz az első két szakasz tartalmából származó tanúsítványokkal kiszámított *aláírás* .

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>A tanúsítvány regisztrálása a Microsoft Identity platformmal

A tanúsítvány hitelesítő adatait a Microsoft Identity platformon található ügyfélalkalmazás alapján a következő módszerek bármelyikével társíthatja a Azure Portal használatával:

### <a name="uploading-the-certificate-file"></a>A tanúsítványfájl feltöltése

Az ügyfélalkalmazás Azure-alkalmazásának regisztrációja:
1. Válassza ki a **tanúsítványok & Secrets** elemet.
2. Kattintson a **tanúsítvány feltöltése** elemre, és válassza ki a feltölteni kívánt tanúsítványt.
3. Kattintson a **Hozzáadás** parancsra.
  A tanúsítvány feltöltése után a rendszer megjeleníti az ujjlenyomatot, a kezdési dátumot és a lejárati értékeket.

### <a name="updating-the-application-manifest"></a>Az alkalmazás jegyzékfájljának frissítése

A tanúsítvány beszerzése után a következő értékeket kell kiszámítani:

- `$base64Thumbprint` -A tanúsítvány kivonatának Base64 kódolású értéke
- `$base64Value` -A tanúsítvány nyers adatmennyiségének Base64 kódolású értéke

Adjon meg egy GUID azonosítót a kulcs azonosításához az Application manifest () alkalmazásban `$keyId` .

Az ügyfélalkalmazás Azure-alkalmazásának regisztrációja:
1. Válassza ki a **jegyzékfájlt** az alkalmazás jegyzékfájljának megnyitásához.
2. Cserélje le a " *hitelesítő adatok* " tulajdonságot az új tanúsítvány adataira a következő séma használatával.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Mentse a módosításokat az alkalmazás-jegyzékfájlba, majd töltse fel a jegyzékfájlt a Microsoft Identity platformba.

   A `keyCredentials` tulajdonság többértékű, így több tanúsítvány is feltölthető a gazdagabb kulcsok kezeléséhez.
   
## <a name="using-a-client-assertion"></a>Ügyfél-érvényesítés használata

Az ügyfél-kijelentések bárhol használhatók, ha az ügyfél titkos kulcsát használja.  Így például az [engedélyezési kód folyamatában](v2-oauth2-auth-code-flow.md)átadható a-nek, `client_secret` hogy igazolja, hogy a kérelem az alkalmazástól érkezik. Ezt a és a paramétereket is lecserélheti `client_assertion` `client_assertion_type` . 

| Paraméter | Érték | Leírás|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Ez egy rögzített érték, amely azt jelzi, hogy a tanúsítvány hitelesítő adatait használja. |
|`client_assertion`| JWT |Ez a fent létrehozott JWT. |

## <a name="next-steps"></a>Következő lépések

A [MSAL.net-függvénytár ezt a forgatókönyvet](msal-net-client-assertions.md) egyetlen sorban kezeli.

A Microsoft Identity platform code minta használatával a GitHubon a [.net Core Daemon Console alkalmazás](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) azt mutatja be, hogyan használja az alkalmazás a saját hitelesítő adatait a hitelesítéshez. Azt is bemutatja, hogyan [hozhat létre önaláírt tanúsítványt](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) a `New-SelfSignedCertificate` PowerShell-parancsmag használatával. Az [alkalmazás-létrehozási szkripteket](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) is használhatja a minta tárházban tanúsítványok létrehozásához, az ujjlenyomat kiszámításához stb.
