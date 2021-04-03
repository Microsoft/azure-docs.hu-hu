---
title: Bejelentkezés az Apple szolgáltatással (előzetes verzió)
description: Megtudhatja, hogyan konfigurálhatja az Apple-be való bejelentkezést az App Service vagy Azure Functions alkalmazás identitás-szolgáltatójának használatával.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603227"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>App Service vagy Azure Functions alkalmazás konfigurálása az Apple Provider szolgáltatással való bejelentkezéshez (előzetes verzió)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a cikkből megtudhatja, hogyan konfigurálhatja Azure App Service vagy Azure Functions a bejelentkezés az Apple-ben hitelesítési szolgáltatóként való használatára. 

A cikkben ismertetett eljárás végrehajtásához regisztrálnia kell az Apple Developer programban. Az Apple fejlesztői programban való regisztráláshoz nyissa meg a [Developer.Apple.com/Programs/Enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> Ha engedélyezi a bejelentkezést az Apple szolgáltatással, akkor letilthatja az alkalmazás App Service hitelesítési/engedélyezési funkciójának kezelését bizonyos ügyfeleken, például a Azure Portalon, az Azure CLI-n és a Azure PowerShellon keresztül. A funkció egy új API-felületre támaszkodik, amely az előzetes verzióban még nem szerepel a felügyeleti élményekben.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Alkalmazás létrehozása az Apple fejlesztői portálon
Az Apple fejlesztői portálon létre kell hoznia egy alkalmazás-azonosítót és egy szolgáltatási azonosítót.

1. Az Apple fejlesztői portálon nyissa meg a **tanúsítványok, azonosítók, & profilok** lehetőséget.
2. Az **azonosítók** lapon kattintson a **(+)** gombra.
3. Az **új azonosító regisztrálása** lapon válassza az **alkalmazás-azonosítók** lehetőséget, majd válassza a **Folytatás** lehetőséget. (Az alkalmazás-azonosítók egy vagy több szolgáltatási azonosítót tartalmaznak.) ![Új alkalmazás-azonosító regisztrálása az Apple fejlesztői portálon](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. Az **alkalmazás-azonosító regisztrálása** lapon adja meg a leírást és a köteg azonosítóját, majd válassza a **Bejelentkezés az Apple-szel** lehetőséget a képességek listából. Ezután válassza a **Folytatás** elemet. Jegyezze fel az **alkalmazás azonosítójának előtagját (a csoport azonosítóját)** ebből a lépésből, majd később szüksége lesz rá.
![Új alkalmazás azonosítójának konfigurálása az Apple fejlesztői portálon](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Tekintse át az alkalmazás regisztrációs adatait, és válassza a **regisztráció** lehetőséget.
6. Az **azonosítók** lapon kattintson az **(+)** gombra.
![Új szolgáltatás azonosítójának létrehozása az Apple fejlesztői portálon](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. Az **új azonosító regisztrálása** lapon válassza a **szolgáltatások azonosítók** lehetőséget, majd kattintson a **Folytatás** gombra.
![Új szolgáltatás azonosítójának regisztrálása az Apple fejlesztői portálon](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. A **szolgáltatások azonosítójának regisztrálása** lapon adjon meg egy leírást és egy azonosítót. A leírás az, amit a felhasználó a beleegyezési képernyőn fog megjelenni. Az azonosító az Apple Provider az App Service-ben való konfigurálásához használt ügyfél-azonosító lesz. Ezután válassza a **Konfigurálás** lehetőséget.
![Leírás és azonosító megadása](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. Az előugró ablakban állítsa be az elsődleges alkalmazás azonosítóját a korábban létrehozott alkalmazás-azonosítóra. Adja meg az alkalmazás tartományát a tartomány szakaszban. A visszatérési URL-címhez használja az URL-címet `<app-url>/.auth/login/apple/callback` . Például: `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Ezután válassza a **Hozzáadás** és **Mentés** lehetőséget.
![A tartomány és a regisztrációs URL-cím megadása a regisztrációhoz](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Tekintse át a szolgáltatás regisztrációs adatait, majd válassza a **Mentés** lehetőséget.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Az ügyfél titkos kulcsának előállítása
Az Apple megköveteli, hogy az alkalmazás fejlesztői JWT-tokent hozzanak létre és írjanak alá az ügyfél titkos értékeként. A titkos kód létrehozásához először egy elliptikus görbe titkos kulcsot kell előállítania és letöltenie az Apple fejlesztői portálról. Ezt követően használja ezt a kulcsot egy [adott adattartalommal](#structure-the-client-secret-jwt)rendelkező [JWT aláírásához](#sign-the-client-secret-jwt) .

### <a name="create-and-download-the-private-key"></a>A titkos kulcs létrehozása és letöltése
1. Az Apple fejlesztői portál **kulcsok** lapján válassza a **kulcs létrehozása** lehetőséget, vagy kattintson a **(+)** gombra.
2. Az **új kulcs regisztrálása** lapon adja meg a kulcs nevét, jelölje be az **Apple-bejelentkezés** melletti jelölőnégyzetet, majd válassza a **Konfigurálás** lehetőséget.
3. A **kulcs konfigurálása** lapon kapcsolja össze a kulcsot a korábban létrehozott elsődleges alkalmazás-azonosítóval, majd válassza a **Mentés** lehetőséget.
4. Fejezze be a kulcs létrehozását úgy, hogy megerősíti az adatokat, és kiválasztja a **Folytatás** lehetőséget, majd az információk áttekintését és a **regisztráció** lehetőséget.
5. A **kulcs letöltése** oldalon töltse le a kulcsot. Ez a fájl `.p8` (PKCS # 8) fájlként lesz letöltve – az ügyfél titkos JWT aláírásához használja a fájl tartalmát.

### <a name="structure-the-client-secret-jwt"></a>Az ügyfél titkos JWT felépítése
Az Apple megköveteli, hogy az ügyfél titka egy JWT token Base64 kódolása legyen. A dekódolású JWT tokennek a következő példához hasonló adattartalommal kell rendelkeznie:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **Sub**: az Apple ügyfél-azonosító (a szolgáltatás azonosítója is)
- **ISS**: az Apple fejlesztői CSAPATának azonosítója
- **AUD**: az Apple megkapja a jogkivonatot, így azok a célközönségek
- **exp**: legfeljebb hat hónappal a **NBF** után

A fenti hasznos adat Base64 kódolású verziója így néz ki: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Megjegyzés: az Apple nem fogad el olyan ügyfél titkos JWTs, amelynek lejárati dátuma a létrehozás (vagy NBF) dátum utáni hat hónapnál nagyobb. Ez azt jelenti, hogy az ügyfél titkát legalább hat havonta el kell forgatnia._

A jogkivonatok létrehozásával és ellenőrzésével kapcsolatos további információkért tekintse [meg az Apple fejlesztői dokumentációját](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>Az ügyfél titkos JWT aláírása
A `.p8` korábban letöltött fájlt fogja használni az ügyfél titkos JWT aláírásához. Ez a fájl egy [PCKS # 8 fájl](https://en.wikipedia.org/wiki/PKCS_8) , amely a privát aláíró kulcsot a PEM formátumban tartalmazza. A JWT létrehozásához és aláírásához számos kódtárat használhat. 

A JWT-tokenek létrehozásához és aláírásához különböző nyílt forráskódú könyvtárak érhetők el online. Az JWT-tokenek létrehozásával kapcsolatos további információkért lásd: jwt.io. Például az ügyfél titkos kódjának generálásának egyik módja a [Microsoft. IdentityModel. tokens NuGet-csomag](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) importálása és az alább látható, kis mennyiségű C# kód futtatása.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: az Apple fejlesztői CSAPATának azonosítója
- **clientId**: az Apple ügyfél-azonosító (a szolgáltatás azonosítója is)
- **p8key**: a PEM formátumú kulcs – a kulcs beszerzéséhez nyissa meg a `.p8` fájlt egy szövegszerkesztőben, és másoljon mindent `-----BEGIN PRIVATE KEY-----` a `-----END PRIVATE KEY-----` sortörések között, és anélkül.
- **keyId**: a letöltött kulcs azonosítója

Ez a jogkivonat az Apple Provider konfigurálásához használni kívánt ügyfél-titkos értéket adja vissza.

> [!IMPORTANT]
> Az ügyfél titkos kulcsa fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.
>

Adja hozzá az ügyfél titkát [alkalmazási beállításként](./configure-common.md#configure-app-settings) az alkalmazáshoz, a kívánt beállítás neve alapján. Később jegyezze fel ezt a nevet.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Szolgáltatói adatok hozzáadása az alkalmazáshoz

> [!NOTE]
> A szükséges konfiguráció új API-formátumban van, jelenleg csak a [fájl alapú konfiguráció (előzetes verzió)](.\app-service-authentication-how-to.md#config-file)támogatja. Az alábbi lépéseket kell követnie egy ilyen fájl használatával.

Ez a szakasz végigvezeti a konfiguráció frissítésén, hogy tartalmazza az új IDENTITÁSSZOLGÁLTATÓ. Egy példa a konfigurációra.

1. Az `identityProviders` objektumon belül adjon hozzá egy `apple` objektumot, ha még nem létezik ilyen.
2. Rendeljen hozzá egy objektumot a kulcshoz egy `registration` objektumon belül, és opcionálisan egy `login` objektumot:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. Az `registration` objektumon belül állítsa be a `clientId` t az összegyűjtött ügyfél-azonosítóra.
    
    b. Az `registration` objektumon belül állítsa be annak az `clientSecretSettingName` alkalmazás-beállításnak a nevét, ahol az ügyfél titkos kulcsát tárolta.
    
    c. Az objektumon belül dönthet úgy `login` , hogy a tömböt úgy állítja be, `scopes` hogy tartalmazza az Apple-hitelesítéshez használt hatókörök listáját (például "név" és "e-mail"). Ha a hatókörök konfigurálva vannak, a rendszer explicit módon kéri őket a beleegyezési képernyőn, amikor a felhasználók első alkalommal jelentkeznek be.

Miután beállította ezt a konfigurációt, készen áll az Apple Provider használatára az alkalmazásban való hitelesítéshez.

A teljes konfiguráció a következő példához hasonlóan néz ki (ahol a APPLE_GENERATED_CLIENT_SECRET beállítás egy generált JWT tartalmazó alkalmazás-beállításra mutat):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
