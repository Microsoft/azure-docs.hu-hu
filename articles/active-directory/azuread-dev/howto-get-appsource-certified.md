---
title: A Azure Active Directory minősítésű AppSource beszerzése | Microsoft Docs
description: Az alkalmazás AppSource minősítése Azure Active Directory számára.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: d9a4da6fe65fda07609c7399518fa324017ea44c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649345"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>A Azure Active Directory minősítésének beszerzése a AppSource

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) az üzleti felhasználók számára az üzletági SaaS-alkalmazások (önálló SaaS és a meglévő Microsoft SaaS-termékekhez való bővítmény) felderítésére, kipróbálására és kezelésére szolgáló cél.

Ha önálló SaaS-alkalmazást szeretne listázni a AppSource-on, az alkalmazásnak el kell fogadnia az egyszeri bejelentkezést a munkahelyi fiókokból bármely olyan vállalattól vagy szervezettől, amely Azure Active Directory (Azure AD). A bejelentkezési folyamatnak az [OpenID Connect](v1-protocols-openid-connect-code.md) vagy a [OAuth 2,0](v1-protocols-oauth-code.md) protokollt kell használnia. Az SAML-integráció nem fogadható el a AppSource-hitelesítéshez.

## <a name="guides-and-code-samples"></a>Útmutatók és mintakód-minták

Ha szeretné megtudni, hogyan integrálhatja alkalmazásait az Azure AD-vel az Open ID-kapcsolat használatával, kövesse a [Azure Active Directory fejlesztői útmutatójában](v1-overview.md#get-started "Ismerkedés az Azure AD fejlesztői szolgáltatásokkal való használatába")található útmutatók és kódok mintáit.

## <a name="multi-tenant-applications"></a>Több-bérlős alkalmazások

A *több-bérlős alkalmazások* olyan alkalmazások, amelyek bármely olyan cég vagy szervezet felhasználóitól fogadnak bejelentkezést, amely külön példány, konfiguráció vagy központi telepítés nélkül rendelkezik az Azure ad-vel. A AppSource javasolja, hogy az alkalmazások több-bérlőt alkalmazzanak *az ingyenes* próbaidőszakos felhasználói élmény érdekében.

Az alkalmazáson belüli több-bérlő engedélyezéséhez kövesse az alábbi lépéseket:
1. Állítsa be a `Multi-Tenanted` tulajdonságot az `Yes` alkalmazás regisztrációjának adataira a [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Alapértelmezés szerint a Azure Portalban létrehozott alkalmazások *[egybérlős](#single-tenant-applications)* konfigurációként vannak konfigurálva.
1. Frissítse a kódot a kérelmeknek a végpontba való küldéséhez `common` . Ehhez frissítse a végpontot a verzióról a verzióra `https://login.microsoftonline.com/{yourtenant}` `https://login.microsoftonline.com/common*` .
1. Egyes platformok, például az ASP .NET esetében frissíteni kell a kódot, hogy több kiállító is el legyen fogadva.

A több-bérlős alkalmazással kapcsolatos további információkért lásd: [Hogyan lehet bejelentkezni bármelyik Azure Active Directory (Azure ad) felhasználót a több-bérlős alkalmazás mintájának használatával](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Egybérlős alkalmazások

Az *egybérlős alkalmazások* olyan alkalmazások, amelyek csak egy meghatározott Azure ad-példány felhasználóitól fogadnak bejelentkezéseket. A külső felhasználók (beleértve a más szervezetektől származó munkahelyi vagy iskolai fiókokat, vagy a személyes fiókok) bejelentkezhetnek egy egybérlős alkalmazásba az egyes felhasználók vendég fiókként való hozzáadása után az Azure AD-példányhoz, amelyet az alkalmazás regisztrál. 

A felhasználókat az Azure ad [B2B-együttműködésen](../external-identities/what-is-b2b.md) keresztül is hozzáadhatja vendég fiókként az Azure ad-hez, és ezt [programozott](../../active-directory-b2c/code-samples.md)módon is elvégezheti. A B2B használatakor a felhasználók létrehozhatnak olyan önkiszolgáló portált, amelyhez nem szükséges meghívót bejelentkezni. További információ: [önkiszolgáló portál az Azure ad B2B Collaboration-regisztrációhoz](../external-identities/self-service-portal.md).

Az egybérlős alkalmazások lehetővé teszik a *kapcsolatfelvételt* , de ha engedélyezni szeretné a AppSource által javasolt egyszeres vagy ingyenes próbaverziót, engedélyezze a többszörös kihelyezés használatát az alkalmazásban.

## <a name="appsource-trial-experiences"></a>AppSource próbaverziók

### <a name="free-trial-customer-led-trial-experience"></a>Ingyenes próbaverzió (ügyfél által vezetett próbaverzió)

Az ügyfél által vezetett próbaverzió az a tapasztalat, amellyel a AppSource azt javasolja, hogy egyetlen kattintással hozzáférhessen az alkalmazáshoz. Az alábbi példa bemutatja, hogyan néz ki ez a felület:

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>A felhasználó megkeresi az alkalmazást a AppSource webhelyen</li><li>Az "ingyenes próbaverzió" lehetőség kiválasztása</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>A AppSource átirányítja a felhasználót a webhelyén lévő URL-címre</li><li>A webhely automatikusan elindítja az <i>egyszeri bejelentkezés</i> folyamatát (az oldal betöltésekor)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>A rendszer átirányítja a felhasználót a Microsoft bejelentkezési oldalára</li><li>A felhasználó hitelesítő adatokat biztosít a bejelentkezéshez</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>A felhasználó beleegyezik az alkalmazásba</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>A bejelentkezés befejeződik, és a rendszer visszairányítja a felhasználót a webhelyre</li><li>A felhasználó elindítja az ingyenes próbaverziót</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kapcsolatfelvétel (partner által vezetett próbaverziós felület)

A partneri próbaidőszakot akkor használhatja, ha egy manuális vagy hosszú távú műveletnek a felhasználó/vállalat kiépítéséhez kell történnie – például az alkalmazásnak olyan virtuális gépeket, adatbázis-példányokat vagy műveleteket kell kiépítenie, amelyek végrehajtása sok időt is igénybe vehet. Ebben az esetben, miután a felhasználó kiválasztja a **kérelem próbaverziója** gombot, és kitölti az űrlapot, a AppSource elküldi a felhasználó kapcsolattartási adatait. Amikor megkapja ezeket az információkat, kiépítheti a környezetet, és elküldheti az utasításokat a felhasználónak a próbaverziós felület elérésének módjával kapcsolatban:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>A felhasználó megkeresi az alkalmazást a AppSource webhelyen</li><li>A kapcsolatfelvételi lehetőség kiválasztása</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>Űrlap kitöltése kapcsolattartási adatokkal</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>Felhasználói adatokat kap</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>A környezet beállítása</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>Forduljon a felhasználóhoz a próbaverziós adatokkal</td>
        </tr>
        </table><br/><br/>
        <ul><li>A felhasználó információi és beállítási próbaverziós példánya jelenik meg</li><li>Elküldi a hiperhivatkozást, hogy hozzáférjen az alkalmazáshoz a felhasználóhoz</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>A felhasználó hozzáfér az alkalmazáshoz, és elvégzi az egyszeri bejelentkezés folyamatát.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>A felhasználó beleegyezik az alkalmazásba</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>A bejelentkezés befejeződik, és a rendszer visszairányítja a felhasználót a webhelyre</li><li>A felhasználó elindítja az ingyenes próbaverziót</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>További információ

A AppSource-próbaverzióval kapcsolatos további információkért tekintse meg [ezt a videót](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Következő lépések

- Az Azure AD-bejelentkezéseket támogató alkalmazások létrehozásával kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-hez](./v1-authentication-scenarios.md).
- A SaaS-alkalmazás AppSource való listázásával kapcsolatos információkért lásd: AppSource- [partner adatai](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Támogatás kérése

Az Azure AD-integrációhoz a [Microsoft Q&](/answers/products/) a-t használjuk a Közösségben, hogy támogatást nyújtsanak.

Javasoljuk, hogy Kérdezzen rá kérdéseket a Microsoft Q&először, és Böngésszen a meglévő problémák között, és ellenőrizze, hogy valaki megkérdezte-e a kérdést. Győződjön meg arról, hogy a kérdései vagy megjegyzései szerepelnek a címkével [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

A következő Megjegyzések szakaszban visszajelzéseket adhat meg, és segítheti a tartalom pontosítását és kialakítását.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->