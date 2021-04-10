---
title: Azure Active Directory minősítésű AppSource beszerzése | Microsoft Docs
description: Megtudhatja, hogyan szerezheti be az alkalmazás AppSource minősítését Azure Active Directory számára.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7731aa2eb1a962d4674ed382911ba0058e1ebdaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647506"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Azure Active Directory AppSource minősítésének beolvasása

[Microsoft AppSource](https://appsource.microsoft.com/) az üzleti felhasználók számára az üzletági SaaS-alkalmazások (önálló SaaS és a meglévő Microsoft SaaS-termékekhez való bővítmény) felderítésére, kipróbálására és kezelésére szolgáló cél.

Ha önálló SaaS-alkalmazást szeretne listázni a AppSource-on, az alkalmazásnak el kell fogadnia az egyszeri bejelentkezést a munkahelyi fiókokból bármely olyan vállalattól vagy szervezettől, amely Azure Active Directory (Azure AD). A bejelentkezési folyamatnak az [OpenID Connect](v2-protocols-oidc.md) vagy a [OAuth 2,0](v2-oauth2-auth-code-flow.md) protokollt kell használnia. Az SAML-integráció nem fogadható el a AppSource-hitelesítéshez.

## <a name="multi-tenant-applications"></a>Több-bérlős alkalmazások

A *több-bérlős alkalmazások* olyan alkalmazások, amelyek bármely olyan cég vagy szervezet felhasználóitól fogadnak bejelentkezést, amely külön példány, konfiguráció vagy központi telepítés nélkül rendelkezik az Azure ad-vel. A AppSource javasolja, hogy az alkalmazások több-bérlőt alkalmazzanak *az ingyenes* próbaidőszakos felhasználói élmény érdekében.

Az alkalmazáson belüli több-bérlő engedélyezéséhez kövesse az alábbi lépéseket:
1. Állítsa be a `Multi-Tenanted` tulajdonságot az `Yes` alkalmazás regisztrációjának adataira a [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Alapértelmezés szerint a Azure Portalban létrehozott alkalmazások *[egybérlős](#single-tenant-applications)* konfigurációként vannak konfigurálva.
1. Frissítse a kódot a kérelmeknek a végpontba való küldéséhez `common` . Ehhez frissítse a végpontot a verzióról a verzióra `https://login.microsoftonline.com/{yourtenant}` `https://login.microsoftonline.com/common*` .
1. Egyes platformokhoz, például a ASP.NET-hoz, frissítenie kell a kódot, hogy több kiállító is fogadja el.

A több-bérlős alkalmazással kapcsolatos további információkért lásd: [Hogyan lehet bejelentkezni bármelyik Azure Active Directory (Azure ad) felhasználót a több-bérlős alkalmazás mintájának használatával](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Egybérlős alkalmazások

Az *egybérlős alkalmazások* olyan alkalmazások, amelyek csak egy meghatározott Azure ad-példány felhasználóitól fogadnak bejelentkezéseket. A külső felhasználók (beleértve a más szervezetektől származó munkahelyi vagy iskolai fiókokat, vagy a személyes fiókok) bejelentkezhetnek egy egybérlős alkalmazásba az egyes felhasználók vendég fiókként való hozzáadása után az Azure AD-példányhoz, amelyet az alkalmazás regisztrál. 

A felhasználókat az Azure ad [B2B-együttműködésen](../external-identities/what-is-b2b.md) keresztül is hozzáadhatja vendég fiókként az Azure ad-hez, és ezt [programozott](../../active-directory-b2c/code-samples.md)módon is elvégezheti. A B2B használatakor a felhasználók létrehozhatnak olyan önkiszolgáló portált, amelyhez nem szükséges meghívót bejelentkezni. További információ: [önkiszolgáló portál az Azure ad B2B Collaboration-regisztrációhoz](../external-identities/self-service-portal.md).

Az egybérlős alkalmazások lehetővé teszik a *kapcsolatfelvételt* , de ha engedélyezni szeretné a AppSource által javasolt egyszeres vagy ingyenes próbaverziót, engedélyezze a többszörös kihelyezés használatát az alkalmazásban.

## <a name="appsource-trial-experiences"></a>AppSource próbaverziók

### <a name="free-trial-customer-led-trial-experience"></a>Ingyenes próbaverzió (ügyfél által vezetett próbaverzió)

Az ügyfél által vezetett próbaverzió az a tapasztalat, amellyel a AppSource azt javasolja, hogy egyetlen kattintással hozzáférhessen az alkalmazáshoz. Az alábbi példa bemutatja, hogyan néz ki ez a felület:

1.  A felhasználó megkeresi az alkalmazást a AppSource webhelyén, majd kiválasztja az **ingyenes próbaverzió** lehetőséget.

    ![Az ügyfél által vezetett próbaverzió ingyenes próbaverzióját jeleníti meg](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  A AppSource átirányítja a felhasználót a webhely egyik URL-címére. A webhely automatikusan elindítja az *egyszeri bejelentkezés* folyamatát (az oldal betöltésekor).

    ![Azt mutatja, hogy a rendszer hogyan irányítja át a felhasználót a webhelyén lévő URL-címre](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  A rendszer átirányítja a felhasználót a Microsoft bejelentkezési oldalára, és a felhasználó hitelesítő adatokat biztosít a bejelentkezéshez.

    ![A Microsoft bejelentkezési oldalát jeleníti meg](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. A felhasználó beleegyezik az alkalmazásba.

    ![Példa: az alkalmazáshoz tartozó beleegyező lap](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  A bejelentkezés befejeződött, és a rendszer visszairányítja a felhasználót a webhelyre.  A felhasználó elindítja az ingyenes próbaverziót.

    ![Megjeleníti a felhasználó által a webhelyre való átirányításkor megjelenő élményt.](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Kapcsolatfelvétel (partner által vezetett próbaverziós felület)

A partneri próbaidőszakot akkor használhatja, ha egy manuális vagy hosszú távú műveletnek a felhasználó/vállalat kiépítéséhez kell történnie – például az alkalmazásnak olyan virtuális gépeket, adatbázis-példányokat vagy műveleteket kell kiépítenie, amelyek végrehajtása sok időt is igénybe vehet. Ebben az esetben, miután a felhasználó kiválasztja a **kérelem próbaverziója** gombot, és kitölti az űrlapot, a AppSource elküldi a felhasználó kapcsolattartási adatait. Amikor megkapja ezeket az információkat, kiépítheti a környezetet, és elküldheti az utasításokat a felhasználónak a próbaverziós felület elérésének módjával kapcsolatban:<br/><br/>

1. A felhasználó megkeresi az alkalmazást a AppSource webhelyen, majd kiválasztja a **kapcsolat** lehetőséget.

    ![Kapcsolatfelvétel a partner által vezetett próbaverziós felhasználói felülettel](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. A felhasználó a kapcsolattartási adatokkal kitölt egy űrlapot.

    ![Példa a kapcsolattartási adatokat tartalmazó űrlapra](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Megkapja a felhasználó adatait, állítson be egy próbaverziós példányt, és küldje el a hiperhivatkozást, hogy hozzáférjen az alkalmazáshoz a felhasználóhoz.

    ![A felhasználói adatok helyőrzőjét jeleníti meg](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. A felhasználó hozzáfér az alkalmazáshoz, és befejezi az egyszeri bejelentkezés folyamatát.

    ![Megjeleníti az alkalmazás bejelentkezési képernyőjét.](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. A felhasználó beleegyezik az alkalmazásba.

    ![Az alkalmazás egy példaként szolgáló beleegyező lapját jeleníti meg](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. A bejelentkezés befejeződött, és a rendszer visszairányítja a felhasználót a webhelyre. A felhasználó elindítja az ingyenes próbaverziót.

    ![Megjeleníti a felhasználó által a webhelyre való átirányításkor megjelenő élményt.](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>További információ

A AppSource-próbaverzióval kapcsolatos további információkért tekintse meg [ezt a videót](https://aka.ms/trialexperienceforwebapps). 

## <a name="get-support"></a>Támogatás kérése

Az Azure AD-integrációhoz a [Microsoft Q&](/answers/topics/azure-active-directory.html) a-t használjuk a Közösségben, hogy támogatást nyújtsanak.

Javasoljuk, hogy Kérdezzen rá kérdéseket a [Microsoft Q&](/answers/topics/azure-active-directory.html) először, és Böngésszen a meglévő problémák között, és ellenőrizze, hogy valaki megkérdezte-e a kérdést. Győződjön meg arról, hogy a kérdései vagy megjegyzései szerepelnek a címkével [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

A következő Megjegyzések szakaszban visszajelzéseket adhat meg, és segítheti a tartalom pontosítását és kialakítását.

## <a name="next-steps"></a>Következő lépések

- Az Azure AD-bejelentkezéseket támogató alkalmazások létrehozásával kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-hez](authentication-flows-app-scenarios.md).
- A SaaS-alkalmazás AppSource való listázásával kapcsolatos információkért lásd: AppSource- [partner adatai](https://appsource.microsoft.com/partners)