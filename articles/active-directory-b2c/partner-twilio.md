---
title: Az alkalmazás Twilio ellenőrzése Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálhat egy Azure AD B2Cos online fizetési alkalmazást a Twilio-ellenőrzési API-val. Meg kell felelnie a PSD2 (Payment Services-irányelv 2) tranzakciós követelményeinek dinamikus összekapcsolással és erős ügyfél-hitelesítéssel.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994043"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Twilio-ellenőrzési alkalmazás integrálása Azure Active Directory B2C

Ebből az útmutatóból megtudhatja, hogyan integrálhat egy Azure Active Directory B2C (Azure AD B2C) minta online fizetési alkalmazást a Twilio-ellenőrzési API-val. A Twilio ellenőrzése alkalmazás használatával Azure AD B2C ügyfelek a PSD2 (Payment Services-irányelv 2) tranzakciós követelményeit dinamikus összekapcsolással és erős ügyfél-hitelesítéssel is be tudják tartani.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* [Egy Azure ad B2C bérlő](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.
* Egy [próbaverziós fiók](https://www.twilio.com/try-twilio) a Twilio címen.

## <a name="scenario-description"></a>Forgatókönyv leírása

A következő összetevők alkotják a Twilio megoldást:

- .NET [PSD2 bemutató webalkalmazás](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App), amely lehetővé teszi a bejelentkezést, illetve a regisztrációt, valamint egy nagy kockázatú, magas kockázatú tranzakció elvégzését.
- Azure AD B2C [a kombinált bejelentkezést és a regisztrálási szabályzatot](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy).
- Azure AD B2C a Twilio-sel integrált szabályzat az API-t használva `id_token_hint` .
- .NET-webalkalmazás, amely egy `.well-known` OpenIdConnect-végpontot üzemeltet, amely lehetővé teszi az érvényesítését `id_token_hint` .


    ![twilio folyamat](media/partner-twilio/twilio-flow.png)

| Lépés | Leírás |
|------|------|
| 1     | A felhasználó kezdeményezi a bejelentkezést, vagy bejelentkezik a PSD2 bemutató alkalmazásba. A felhasználó hitelesítése a Azure AD B2C összevont bejelentkezés és a regisztrálási szabályzat használatával történik. A rendszer tokent ad vissza az alkalmazásnak. A regisztráláskor a rendszer az SMS/telefon használatával ellenőrzi a felhasználó telefonszámát, és rögzíti őket a Azure AD B2C-fiókjában.     |
| 2     | A felhasználó magas kockázatú tranzakciót kezdeményez, például $50,00-es átvitelt. A rendszer kiértékeli a felhasználó aktuális hozzáférési jogkivonatát, hogy a PolicyId meg tudja határozni, hogy a felhasználó már hitelesített Step-Up egyéni házirenden keresztül.     |
| 3     | Az alkalmazás rögzíti a tranzakció értékét és a kedvezményezettet, $50,00 és John Doe-t, és létrehoz egy aláírt jogkivonatot. Ennek a tokennek `id_token_hint` a neve, és tartalmazza a jogcímet `amount:$500, payee:john doe` . A `id_token_hint` küldi el a kérést a Azure ad B2C egyéni szabályzattal együtt, amely integrálva van a Twilio-mel.     |
| 4     | Azure AD B2C ellenőrzi a id_token_hint aláírását az alkalmazások `/.well-known` OpenId Connect végpontjának ellenőrzésével. Az ellenőrzés után kicsomagolja a jogcímeket a jogkivonatból, különösen a `amount` és a `payee` . A felhasználó megtekint egy oldalt, hogy ellenőrizze a mobiltelefonjuk számát SMS-üzeneten keresztül.     |
| 5     | A felhasználó SMS-üzenetben kéri a telefonszámát, és Azure AD B2C REST API kérelmet küld a Twilio ellenőrzése API-végpontnak. Emellett elküldi a tranzakciót `amount` és a `payee` PSD2 folyamat részeként az egyszeri jelszó (OTP) létrehozásához. A Twilio SMS-üzenetet küld a felhasználó regisztrált telefonszámára.     |
| 6     |  A felhasználónak be kell írnia az egyszeri jelszavas küldést az SMS-üzenetbe, és beküldi Azure AD B2C. Azure AD B2C API-kérést küld ezzel az egyszeri jelszavas hitelesítéssel, hogy az Twilio ellenőrizze, hogy az OTP helyes-e. Végül egy jogkivonatot ad ki az alkalmazásnak, és egy új PolicyId, amely azt jelzi, hogy a felhasználó fokozta a hitelesítést.    |
|      |      |

## <a name="onboard-with-twilio"></a>Twilio

1. Szerezze be a [próbaverziós fiókot](https://www.twilio.com/try-twilio) a Twilio címen.

2. Az [ebben a cikkben](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console) leírtak szerint vásároljon telefonszámot a Twilio címen.

3. Navigáljon az [API ellenőrzéséhez](https://www.twilio.com/console/verify/services) a Twilio-konzolon, és kövesse az [utasításokat](https://www.twilio.com/docs/verify/verifying-transactions-psd2) a szolgáltatás létrehozásához és a PSD2 beállítás engedélyezéséhez.  

## <a name="configure-the-psd2-demo-app"></a>A PSD2 bemutató alkalmazás konfigurálása

1. Nyissa meg a B2C-WebAPI-DotNet megoldást, és cserélje le a következő értékeket a saját bérlő-specifikus értékeire a web.configban:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. A [webalkalmazás](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) emellett az id jogkivonat-generátort és a metaadat-végpontot is üzemelteti.
   - Hozza létre az aláíró tanúsítványt a [minta leírásában](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)leírtak szerint.
   - Frissítse a következő sorokat a tanúsítványa alapján a web.configban:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Töltse fel a bemutató alkalmazást az Ön által választott üzemeltetési szolgáltatóba. A Azure App Service útmutatása ebben a [minta leírásban](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)található, beleértve a tanúsítvány feltöltésére vonatkozó utasításokat is.

4. Frissítse a Azure AD B2C alkalmazás regisztrációját úgy, hogy a válasz URL-címét adja meg, amely egyenértékű az alkalmazás által üzemeltetett URL-címmel.

5. Nyissa meg a [házirend-fájlokat](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) , és cserélje le az összes példányát a  `contoso` bérlő nevére.

6. Keresse meg a Twilio REST API a technikai profil **Egyéni-SMS-regisztrációját**. Frissítse a  `ServiceURL`   Twilio-AccountSID és a számot a megvásárolt telefonszámra.

7. Keresse meg a Twilio REST API a technikai profilokat, a **TwilioRestAPI-1. lépés**   és a **TwilioRestAPI-ellenőrzés-2. lépés**, és frissítse a Twilio-AccountSID  `ServiceURL`   .

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

Adja hozzá a Azure AD B2C a házirend-fájlokat:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.

2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.

4. Navigáljon **Azure ad B2C**  >  **Identity Experience Framework**-  >  **szabályzat kulcsaihoz**.

5. Adjon hozzá egy új, **B2cRestTwilioClientId** nevű kulcsot. Válassza a **Manual (manuális**) lehetőséget, és adja meg a Twilio AccountSID értékét.

6. Adjon hozzá egy új, **B2cRestTwilioClientSecret** nevű kulcsot. Válassza a **Manual (manuális**) lehetőséget, és adja meg a TWILIO Auth token értékét.

7. Töltse fel az összes házirend-fájlt a bérlőbe.

8. Szabja testre a karakterláncot a GenerateOTPMessageEnrol jogcím-átalakításban a regisztrációs SMS-szövegben.

## <a name="test-the-solution"></a>A megoldás tesztelése

* Keresse meg az alkalmazást, és tesztelje a bejelentkezést, a regisztrációt és a pénz küldése műveletet.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- Tekintse meg a GitHubot a [Twilio Integration Code-mintákhoz](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)  

- [Egyéni házirendek a AAD B2Cban](custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal AAD B2C](custom-policy-get-started.md?tabs=applications)
