---
title: Telefonos regisztráció és bejelentkezés egyéni szabályzatokkal
titleSuffix: Azure AD B2C
description: Egyszeri jelszó (OTP) küldése szöveges üzenetekben az alkalmazás felhasználói telefonokra egyéni szabályzatokkal Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2600ea3488c643bcf215b058425de42cd439dcff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660267"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>A telefonos regisztráció és a bejelentkezés beállítása egyéni szabályzatokkal Azure AD B2C

A telefonos regisztráció és bejelentkezés Azure Active Directory B2C (Azure AD B2C) lehetővé teszi a felhasználók számára, hogy egy szöveges üzenetben SMS-ben küldött egyszeri jelszó (OTP) használatával regisztráljanak és jelentkezzenek be az alkalmazásaiba. Az egyszeri jelszavak segítségével csökkentheti a felhasználók felejtésének kockázatát, vagy megsérült a jelszavuk.

A cikk lépéseit követve az egyéni szabályzatok használatával engedélyezheti ügyfelei számára, hogy regisztráljanak, és bejelentkezzenek az alkalmazásaiba a telefonjára eljuttatott egyszeri jelszó használatával.

## <a name="pricing"></a>Díjszabás

Az egyszeri jelszavakat SMS szöveges üzenetek formájában küldi el a felhasználók számára, és minden egyes elküldött üzenet után díjat számítunk fel. A díjszabással kapcsolatos információkért tekintse meg a [Azure Active Directory B2C díjszabásának](https://azure.microsoft.com/pricing/details/active-directory-b2c/) **külön** díjszabását ismertető szakaszt.

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Felhasználói élmény a telefonos regisztrációhoz és bejelentkezéshez

A telefonos regisztrációt és bejelentkezést követően a felhasználó az elsődleges azonosítóként megadott telefonszám használatával regisztrálhat az alkalmazásra. A regisztráció és a bejelentkezés során a végfelhasználói élmény az alábbiakban olvasható.

> [!NOTE]
> Nyomatékosan javasoljuk, hogy a regisztrációs és a bejelentkezéshez hasonló módon adja meg a belefoglalási adatokat, mint az alábbi minta szövege. Ez a mintaszöveg csak tájékoztató jellegű. A megfelelőségi igények kielégítéséhez tekintse meg a [CTIA webhelyén](https://www.ctia.org/programs) található rövid kód-figyelési kézikönyvet, és kérjen segítséget a saját jogi vagy megfelelőségi szakértőitől a végső szöveg és a szolgáltatás konfigurálásához:
>
> *A telefonszámának megadásával beleegyezik abba, hogy SMS-ben küldött egyszeri jelszót kapjon, amely segítséget nyújt a *&lt; beszúráshoz: az alkalmazás neve &gt;*. A standard üzenet és az adatforgalmi díj is érvényes.*
>
> *&lt;INSERT: az adatvédelmi nyilatkozatra mutató hivatkozás&gt;*<br/>*&lt;INSERT: a szolgáltatási feltételekre mutató hivatkozás&gt;*

Saját engedélyezési adatok hozzáadásához szabja testre a következő mintát. Vegye fel a (z)-t a `LocalizedResources` (z) által a ContentDefinition (a [telefonos regisztrációs és bejelentkezési indító csomag][starter-pack-phone] *Phone_Email_Base.xml* fájlja) által használt,

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard message and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Telefonos regisztrációs élmény

Ha a felhasználó még nem rendelkezik fiókkal az alkalmazáshoz, létrehozhat egyet a **regisztráció most** hivatkozásra kattintva. Megjelenik egy regisztrációs oldal, ahol a felhasználó kiválasztja az **országot**, beírja a telefonszámát, és kiválasztja a **kód küldése** lehetőséget.

![A felhasználó elindítja a telefonos regisztrációt](media/phone-authentication/phone-signup-start.png)

A rendszer egy egyszeri ellenőrző kódot továbbít a felhasználó telefonszámára. A felhasználó beírja az **ellenőrző kódot** a regisztrációs oldalra, majd kiválasztja a **kód ellenőrzése** lehetőséget. (Ha a felhasználó nem tudta beolvasni a kódot, akkor válassza az **új kód küldése** lehetőséget.)

![A felhasználó a telefonos regisztráció során ellenőrzi a kódot.](media/phone-authentication/phone-signup-verify-code.png)

A felhasználó más, a regisztrációs oldalon kért adatokat is megad. Például a **megjelenítendő név**, az **Utónév** és a **vezetéknév** (ország és telefonszám továbbra is kitöltve). Ha a felhasználó egy másik telefonszámot szeretne használni, akkor a regisztráció újraindításához válassza a **szám módosítása** lehetőséget. Ha elkészült, a felhasználó a **Folytatás** gombra kattint.

![A felhasználó további információkat is tartalmaz](media/phone-authentication/phone-signup-additional-info.png)

Ezután a rendszer megkéri a felhasználót, hogy adjon meg egy helyreállítási e-mailt. A felhasználó megadja az e-mail-címét, majd kiválasztja az **ellenőrző kód küldése** lehetőséget. A rendszer elküld egy kódot a felhasználó e-mail-fiókjába, amelyet az **ellenőrző kód** mezőbe beolvashat és megadhat. Ezután a felhasználó kiválasztja a **kód ellenőrzése** lehetőséget. 

A kód ellenőrzése után a felhasználó a **Létrehozás** gombra kattint a fiók létrehozásához. Ha a felhasználó más e-mail-címet szeretne használni, válassza az **E-mail módosítása** lehetőséget.

![A felhasználó létrehoz egy fiókot](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Telefonos bejelentkezési élmény

Ha a felhasználó rendelkezik egy olyan fiókkal, amely azonosítóként rendelkezik telefonszámmal, a felhasználó beírja a telefonszámát, és kiválasztja a **Continue (folytatás**) értéket. A **Folytatás** gombra kattintva megerősítik az országot és a telefonszámot, és egy egyszeri ellenőrző kódot kapnak a telefonjára. A felhasználó beírja az ellenőrző kódot, és kiválasztja a **tovább** lehetőséget a bejelentkezéshez.

![Telefonos bejelentkezési felhasználói élmény](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Felhasználói fiók törlése

Bizonyos esetekben előfordulhat, hogy törölnie kell egy felhasználót és a hozzá tartozó adatait a Azure AD B2C könyvtárából. A felhasználói fiókok Azure Portalon keresztüli törlésével kapcsolatos részletekért tekintse meg [ezeket az utasításokat](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete). 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Előfeltételek

Az OTP beállítása előtt a következő erőforrásokra van szükség.

* [Azure AD B2C bérlő](tutorial-create-tenant.md)
* A bérlőben [regisztrált webalkalmazás](tutorial-register-applications.md)
* A bérlőre feltöltött [Egyéni szabályzatok](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Telefonos regisztráció & bejelentkezési indító csomag beszerzése

Először frissítse a telefonos regisztrációt és a bejelentkezési egyéni házirend-fájlokat a Azure AD B2C Bérlővel való együttműködéshez.

1. Keresse meg a [telefonos regisztrációs és bejelentkezési egyéni házirend-fájlokat][starter-pack-phone] az alapszintű csomag tárházának helyi klónjában, vagy töltse le őket közvetlenül. Az XML-házirend fájljai a következő könyvtárban találhatók:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Minden fájlban cserélje le a karakterláncot a `yourtenant` Azure ad B2C bérlő nevére. Ha például a B2C-bérlő neve *contosob2c*, az összes példánya `yourtenant.onmicrosoft.com` lesz `contosob2c.onmicrosoft.com` .

1. Hajtsa végre az [alkalmazás-azonosítók hozzáadása az egyéni házirendhez](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) című szakasz lépéseit az [Egyéni szabályzatok beszerzése Azure Active Directory B2Cban](custom-policy-get-started.md)című témakörben. Ebben az esetben az `/phone-number-passwordless/` **`Phone_Email_Base.xml`** Előfeltételek, a *IdentityExperienceFramework* és a *ProxyIdentityExperienceFramework* végrehajtásakor regisztrált két alkalmazás **alkalmazás-(ügyfél-) azonosítóit** kell frissíteni.

## <a name="upload-the-policy-files"></a>A szabályzat fájljainak feltöltése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a Azure ad B2C bérlőhöz.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza az **egyéni házirend feltöltése** lehetőséget.
1. Töltse fel a házirend-fájlokat a következő sorrendben:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Az egyes fájlok feltöltésekor az Azure hozzáadja az előtagot `B2C_1A_` .

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Az **Egyéni házirendek** területen válassza a **B2C_1A_SignUpOrSignInWithPhone** lehetőséget.
1. Az **alkalmazás kiválasztása** területen válassza ki azt a *webapp1* -alkalmazást, amelyet az előfeltételek végrehajtásakor regisztrált.
1. Válassza a **Válasz URL-cím kiválasztása** lehetőséget `https://jwt.ms` .
1. Válassza a **Futtatás most** lehetőséget, és regisztráljon e-mail-cím vagy telefonszám használatával.
1. Kattintson ismét a **Futtatás** gombra, és jelentkezzen be ugyanazzal a fiókkal, és ellenőrizze, hogy megfelelő-e a konfigurációja.

## <a name="get-user-account-by-phone-number"></a>Felhasználói fiók beolvasása telefonszám alapján

Az a felhasználó, aki regisztrál egy telefonszámra, egy helyreállítási e-mail-cím nélkül rögzíti a Azure AD B2C könyvtárban a bejelentkezési nevükben megadott telefonszámot. A telefonszám módosításához az ügyfélszolgálatnak vagy a támogatási csapatnak először meg kell keresnie a fiókját, majd frissítenie kell a telefonszámát.

A felhasználó telefonszáma alapján (bejelentkezési név) a [Microsoft Graph](microsoft-graph-operations.md)használatával kereshet:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Például:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Következő lépések

Megtalálhatja a telefonos regisztrációt és az egyéni házirend-előindítási csomagot (és más alapszintű csomagokat) a GitHubon: [Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/forgatókönyvek/telefon-szám][starter-pack-phone]
* [Azure AD Multi-Factor Authentication technikai profil megadása](multi-factor-auth-technical-profile.md)
* [Telefonszám-jogcímek átalakításának meghatározása](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
