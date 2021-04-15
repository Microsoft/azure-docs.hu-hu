---
title: Gyakori alkalmazáskezelési forgatókönyvek Azure Active Directory | Microsoft Docs
description: Alkalmazáskezelés központosította az Azure AD-val
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: overview
ms.date: 03/02/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acfff8a9152c767b61463bed0165d5ae390f649
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374998"
---
# <a name="centralize-application-management-with-azure-ad"></a>Alkalmazáskezelés központosította az Azure AD-val

Jelszavak, amelyek mind az it-mind a világ különböző táján dolgozó alkalmazottak számára fájnak. Ezért egyre több vállalat fordul a Azure Active Directory, a Microsoft felhőalapú identitás- és hozzáférés-kezelési megoldásához és az összes többi erőforráshoz. Ugrás az alkalmazásról az alkalmazásra anélkül, hogy mindegyikhez meg kell adnia a jelszót. Az Outlookról a Workdayre ugorhat az ADP-hez, amilyen gyorsan, gyorsan és biztonságosan megnyithatja őket. Ezután a szervezeten kívüli partnerekkel és másokkal is együttműködhet anélkül, hogy it-it hívatnia kell. Emellett az Azure AD segít a kockázatkezelésben, mivel biztonságossá teszi a használt alkalmazásokat olyan dolgokkal, mint a többtényezős hitelesítés, amely ellenőrzi a felhasználó személyét, és folyamatosan adaptív gépi tanulási és biztonsági intelligenciát használ a gyanús bejelentkezések észleléséhez, így biztonságos hozzáférést biztosít a szükséges alkalmazásokhoz, bárhol is van. Nem csupán a felhasználók számára, hanem az it-felhasználók számára is nagyszerű. Az Azure AD az időben elérhető hozzáférési felülvizsgálatok és a teljes körű irányítási csomag révén segít a megfelelőségben és a szabályzatok betartásában. Így akár automatizálhatja is a felhasználói fiókok létesítését, hogy a hozzáférés-kezelés egyszerű legyen. Tekintsen meg néhány gyakori forgatókönyvet, amelyekben az ügyfél Azure Active Directory alkalmazáskezelési képességeit.

**Gyakori forgatókönyvek**


> [!div class="checklist"]
> * SSO az összes alkalmazáshoz
> * Kiépítés és megszüntetés automatizálása 
> * Az alkalmazások biztonságossá tere
> * Az alkalmazásokhoz való hozzáférés szabályozása
> * Hibrid biztonságos hozzáférés

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>1. forgatókönyv: Az SSO beállítása az összes alkalmazáshoz

Nincs többé jelszókezelés. A vállalati hitelesítő adatokkal biztonságosan elérheti az összes szükséges erőforrást. 

|Szolgáltatás  | Leírás | Ajánlás |
|---------|---------|---------|
|SSO|Megbízható iparági szabványokat használó szabványalapú összevont SSO.|Mindig [saml/OIDC](../develop/v2-howto-app-gallery-listing.md) használatával engedélyezze az SSO-t, ha az alkalmazás támogatja.|
|Saját alkalmazások|Egyszerű központot kínál a felhasználóknak, hogy felfedezzék és hozzáférjenek az összes alkalmazásukhoz. Lehetővé teheti számukra, hogy hatékonyabbak legyenek az önkiszolgáló képességekkel, például alkalmazásokhoz és csoportokhoz való hozzáférés kérésével, vagy az erőforrásokhoz való hozzáférés más felhasználók nevében történő kezelésével.| Miután [integrálta az](my-apps-deployment-plan.md) alkalmazásokat az Azure AD-val az SSO-hoz, üzembe helyezhet Saját alkalmazások vállalati környezetben.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>2. forgatókönyv: Kiépítés és megszüntetés automatizálása 


A legtöbb alkalmazás megköveteli, hogy a felhasználó az alkalmazásba legyen kiépítve, mielőtt hozzáfér a szükséges erőforrásokhoz. A CSV-fájlok vagy összetett szkriptek használata költséges és nehezen kezelhető lehet. Emellett az ügyfeleknek gondoskodniuk kell arról, hogy a fiókok el legyen távolítva, amikor valaki már nem rendelkezik hozzáféréssel. Az alábbi eszközökkel automatizálhatja a kiépítést és megszüntetéseket. 


|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------|---------|
|SCIM-kiépítés|[Az SCIM](https://aka.ms/SCIMOverview) iparági ajánlott eljárás a felhasználókiépítés automatizálására. Bármely SCIM-kompatibilis alkalmazás integrálható az Azure AD-be. Felhasználói fiókok automatikus létrehozása, frissítése és törlése CSV-fájlok, egyéni szkriptek vagy a helyszínre készült megoldások karbantartása nélkül.|Tekintse meg az előre integrált alkalmazások növekvő listáját az Azure AD [alkalmazásgyűjteményében](../saas-apps/tutorial-list.md)|
|Microsoft Graph|Használja ki az Azure AD-hez szükséges részletes adatokat, hogy az alkalmazásokat a szükséges adatokkal egészítse ki.|A [Microsoft-gráf segítségével](https://developer.microsoft.com/graph/) a Microsoft ökoszisztémájában is lekért adatokhoz jut. |


## <a name="scenario-3-secure-your-applications"></a>3. forgatókönyv: Az alkalmazások biztonságossá
Az identitás a biztonság linchpine. Ha egy identitás biztonsága sérül, rendkívül nehéz leállítani a dominóhatást, mielőtt túl késő lenne. Átlagosan több mint 100 napnak kell eltelnie, amíg a szervezetek fel nem derítik, hogy sérült a biztonság. Az Azure AD által biztosított eszközökkel javíthatja az alkalmazások biztonsági biztonságát. 

|Szolgáltatás  |Leírás| Ajánlás |
|---------|---------| ---------|
|Azure AD MFA|Az Azure AD Multi-Factor Authentication (MFA) a Microsoft kétlépéses ellenőrzési megoldása. A rendszergazda által jóváhagyott hitelesítési módszerek használatával a Azure AD MFA az adatokhoz és alkalmazásokhoz való hozzáférés védelmét, miközben egyszerű bejelentkezési folyamatra van igény.| [MFA engedélyezése](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) a felhasználók számára.  |
|Feltételes hozzáférés|A feltételes hozzáféréssel automatizált hozzáférés-vezérlési döntéseket valósíthat meg azzal, hogy feltételek alapján ki férhet hozzá a felhőalkalmazáshoz.| Tekintse át [az ügyfelek által](../fundamentals/concept-fundamentals-security-defaults.md) használt biztonsági alapértelmezéseket és gyakori szabályzatokat. [](../conditional-access/concept-conditional-access-policy-common.md) | 
|Identity Protection|Az Identity Protection a Microsoft által az Azure AD-val rendelkező szervezetekben való pozíciójukból, a Microsoft-fiókokkal rendelkező fogyasztói térből, valamint az Xbox játékaiból szerzett ismereteket használja a felhasználók védelme érdekében. A Microsoft naponta 6,5 billió jelet elemez az ügyfelek azonosítása és a fenyegetésekkel szembeni védelme érdekében.|Engedélyezze [a szolgáltatás által](../identity-protection/concept-identity-protection-policies.md) biztosított alapértelmezett identitásvédelmi szabályzatokat. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>4. forgatókönyv: Az alkalmazásokhoz való hozzáférés szabályozása
Az Identity Governance segít a szervezeteknek abban, hogy egyensúlyt érjenek el a hatékonyságuk között – Milyen gyorsan férhetnek hozzá a szükséges alkalmazásokhoz, például amikor csatlakoznak a szervezethez? Biztonság – Hogyan kell idővel megváltozni a hozzáférésük, például az adott személy munkaállapotának változásai miatt? 

|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------| ---------|
|Elm|Az Azure AD-jogosultságkezelés segítségével a szervezeten belüli és kívüli felhasználók hatékonyabban kezelhetik az alkalmazásaikhoz való hozzáférést.| Engedélyezze a nem rendszergazdáknak, hogy hozzáférési csomagokkal kezel használják [az alkalmazásokat.](../governance/entitlement-management-access-package-first.md)|
|Hozzáférési felülvizsgálatok|A felhasználók alkalmazásokhoz való hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő személyek férnek hozzá a folyamatos hozzáféréshez.| [Tekintse át a](../governance/access-reviews-overview.md) legérzékenyebb alkalmazásokhoz való hozzáférést. |
|Log Analytics|Jelentéseket hozhat létre arról, hogy ki mely alkalmazásokhoz fér hozzá, és azokat egy SIEM-eszközben tárolva korrelálhat adatokat az adatforrások között és az idő múlása során.| Naplóelemzés [engedélyezése](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) és riasztások beállítása az alkalmazásokhoz kapcsolódó kritikus eseményekhez. |


## <a name="scenario-5-hybrid-secure-access"></a>5. forgatókönyv: Hibrid biztonságos hozzáférés
Az identitás csak akkor lehet a vezérlősík, ha mindent képes összekapcsolni a felhőben és a helyszíni alkalmazásokban. Használja ki az Azure AD és partnerei által biztosított eszközöket az örökölt hitelesítési alapú alkalmazásokhoz való hozzáférés biztonságossátételéhez.

|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------|---------|
|Alkalmazásproxy|Napjainkban a munkavállalók a legkülönfélébb helyeken, időpontokban és eszközökön szeretnek dolgozni. Hozzá kell férni a felhőben található SaaS-alkalmazásokhoz és a helyszíni vállalati alkalmazásokhoz. Az Azure AD-alkalmazásproxy költséges és összetett virtuális magánhálózatok (VPN-ek) vagy demilitarizált zónák (DMZ-k) nélkül teszi lehetővé ezt a robusztus hozzáférést.|Távoli [elérés beállítása](./application-proxy.md) a saját alkalmazásához. |
|F5, Akamai, Zscaler|A meglévő hálózat- és kézbesítésvezérlővel könnyedén megvédheti az üzleti folyamatok szempontjából még kritikus fontosságú, de korábban az Azure AD-val nem védett régebbi alkalmazásokat. Valószínű, hogy már rendelkezik mindennel, amire szüksége van ezeknek az alkalmazásoknak a védelméhez.| Akamai, Citrix, F5 vagy Zscaler használatával? Tekintse meg előre [felépített megoldásainkat.](./secure-hybrid-access.md) | 

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Alkalmazáskezelés](./index.yml)
- [Alkalmazások üzembe helyezése](../app-provisioning/user-provisioning.md)
- [Hibrid biztonságos hozzáférés](./secure-hybrid-access.md)
- [Identitáskezelés](../governance/identity-governance-overview.md)
- [Microsoft-identitásplatform](../develop/v2-overview.md)
- [Identitásbiztonság](../conditional-access/index.yml)