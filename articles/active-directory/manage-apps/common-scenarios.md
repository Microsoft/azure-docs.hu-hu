---
title: Gyakori alkalmazás-felügyeleti forgatókönyvek a Azure Active Directoryhoz | Microsoft Docs
description: Az alkalmazások kezelésének központosítása az Azure AD-vel
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 882770fefbb48524ad5f11a56089132b319470aa
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053346"
---
# <a name="centralize-application-management-with-azure-ad"></a>Az alkalmazások kezelésének központosítása az Azure AD-vel

Jelszavak, mind az IT-rémálom, mind a fájdalom a világ különböző alkalmazottai számára. Ezért egyre több vállalat fordul Azure Active Directory, a Microsoft identitás-és hozzáférés-kezelési megoldása a felhőhöz és az összes többi erőforráshoz. Ugorjon az alkalmazásból az alkalmazásba anélkül, hogy jelszót kellene megadnia mindegyikhez. Ugorjon az Outlookból a munkanapokra, és válassza az ADP-t a gyors megnyitáshoz, gyorsan és biztonságosan. Ezután a partnerekkel együttműködve még a szervezeten kívül is dolgozhat, anélkül, hogy meg kellene hívnia. További információ: az Azure AD segít a kockázat kezelésében azáltal, hogy a többtényezős hitelesítéshez hasonló módon biztosítja a használatban lévő alkalmazásokat, hogy megbizonyosodjon róla, hogy ki, folyamatosan adaptív gépi tanulási és biztonsági intelligenciával azonosítsa a gyanús bejelentkezéseket, így biztonságos hozzáférést biztosít a szükséges alkalmazásokhoz, bárhol is legyenek. Ez nem csak a felhasználók számára hasznos, hanem azért is. Az igény szerinti hozzáférési felülvizsgálatok és a teljes körű irányítási csomag segítségével az Azure AD lehetővé teszi a megfelelőség és a szabályzatok betartatását. Így akár automatizálhatja a felhasználói fiókok üzembe helyezését is, így a hozzáférés-kezelés gyerekjáték. Tekintse meg a gyakori forgatókönyveket, amelyeket az ügyfél a Azure Active Directory alkalmazás-felügyeleti képességeinek használatára használ.

**Gyakori forgatókönyvek**


> [!div class="checklist"]
> * Egyszeri bejelentkezés az összes alkalmazáshoz
> * A kiépítés és a megszüntetés automatizálása 
> * Alkalmazások biztonságossá tétele
> * Az alkalmazásokhoz való hozzáférés szabályozása
> * Hibrid biztonságos hozzáférés

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>1. forgatókönyv: egyszeri bejelentkezés beállítása az összes alkalmazáshoz

Nincs több felügyelet jelszava. Biztonságosan férhet hozzá az összes szükséges erőforráshoz a vállalati hitelesítő adataival. 

|Szolgáltatás  | Leírás | Ajánlás |
|---------|---------|---------|
|SSO|Szabványokon alapuló összevont egyszeri bejelentkezés a megbízható iparági szabványokkal.|Mindig az [SAML/OIDC](../develop/v2-howto-app-gallery-listing.md) használatával engedélyezze az egyszeri bejelentkezést, ha az alkalmazás támogatja azt.|
|Saját alkalmazások|Egy egyszerű központot biztosít a felhasználóknak az összes alkalmazás felderítéséhez és eléréséhez. Hatékonyabbá teheti őket az önkiszolgáló képességekkel, például az alkalmazásokhoz és csoportokhoz való hozzáférés kérelmezéséhez, illetve mások nevében az erőforrásokhoz való hozzáférés kezeléséhez.| Alkalmazások [üzembe](my-apps-deployment-plan.md) helyezése a szervezetben, ha az Azure ad-vel integrálta az alkalmazásokat az egyszeri bejelentkezéshez.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>2. forgatókönyv: a kiépítés és a megszüntetés automatizálása 


A legtöbb alkalmazásnak szüksége van egy felhasználónak az alkalmazásba való bevonására, mielőtt hozzáfér a szükséges erőforrásokhoz. A CSV-fájlok vagy az összetett parancsfájlok használata költséges és nehezen kezelhető. Emellett az ügyfeleknek biztosítaniuk kell, hogy a fiókok el legyenek távolítva, ha valakinek többé nem lenne hozzáférése. Az alábbi eszközöket kihasználva automatizálhatja az üzembe helyezést és megszüntetést. 


|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------|---------|
|SCIM kiépítés|A [scim](https://aka.ms/SCIMOverview) egy iparági ajánlott eljárás a felhasználók üzembe helyezésének automatizálásához. Minden SCIM-kompatibilis alkalmazás integrálható az Azure AD-vel. Felhasználói fiókok automatikus létrehozása, frissítése és törlése anélkül, hogy CSV-fájlokat, egyéni parancsfájlokat vagy helyszíni megoldásokat kellene fenntartania.|Tekintse meg az [előre integrált](../saas-apps/tutorial-list.md) alkalmazások növekvő listáját az Azure ad app Galleryben|
|Microsoft Graph|Kihasználhatja az Azure AD által az általa igényelt adatmennyiséggel gazdagított alkalmazások kilégzését és mélységét.|Használja ki a [Microsoft Graphot](https://developer.microsoft.com/graph/) a Microsoft-ökoszisztéma adatainak beszerzéséhez. |


## <a name="scenario-3-secure-your-applications"></a>3. forgatókönyv: az alkalmazások biztonságossá tétele
Az identitás a biztonság sarokköve. Ha egy identitás biztonsága sérül, hihetetlenül nehéz leállítani a dominó-hatást, mielőtt túl késő lenne. Átlagosan 100 nappal azelőtt, hogy a szervezetek észlelték, hogy fennáll a veszélye. Az Azure AD által biztosított eszközök segítségével javíthatja az alkalmazások biztonsági állapotát. 

|Szolgáltatás  |Leírás| Ajánlás |
|---------|---------| ---------|
|Azure AD MFA|Az Azure AD Multi-Factor Authentication (MFA) a Microsoft kétlépéses ellenőrzési megoldása. A rendszergazda által jóváhagyott hitelesítési módszerek használatával az Azure AD MFA segít megőrizni az adataihoz és alkalmazásaihoz való hozzáférést, miközben az egyszerű bejelentkezési folyamat iránti igényt is kielégíti.| Az [MFA engedélyezése](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) a felhasználók számára.  |
|Feltételes hozzáférés|A feltételes hozzáféréssel olyan automatizált hozzáférés-vezérlési döntéseket hozhat létre, amelyek a feltételek alapján hozzáférhetnek a felhőalapú alkalmazásokhoz.| Tekintse át az ügyfelek által használt [biztonsági alapértékeket](../fundamentals/concept-fundamentals-security-defaults.md) és [általános házirendeket](../conditional-access/concept-conditional-access-policy-common.md) . | 
|Identity Protection|Az Identity Protection a Microsoft által az Azure AD-vel, a Microsoft-fiókokkal, a Microsoft-fiókokkal és az Xbox-szal a felhasználók védelme érdekében a Microsoft által beszerzett tapasztalatokat használja. A Microsoft naponta 6 500 000 000 000-as jeleket elemez, hogy azonosítsa és megvédje az ügyfeleket a fenyegetésektől.|Engedélyezze a szolgáltatás által biztosított [alapértelmezett Identity Protection-szabályzatokat](../identity-protection/concept-identity-protection-policies.md) . | 

## <a name="scenario-4-govern-access-to-your-applications"></a>4. forgatókönyv: az alkalmazásokhoz való hozzáférés szabályozása
Az identitások szabályozása révén a szervezetek a termelékenység egyensúlyát érhetik el – milyen gyorsan férhet hozzá egy személy a szükséges alkalmazásokhoz, például ha a szervezethez csatlakoznak? És biztonság – Hogyan változnak a hozzáférésük az idő múlásával, például az adott személy foglalkoztatási állapotának változásai miatt? 

|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------| ---------|
|ELM|Az Azure AD-jogosultságok kezelése lehetővé teheti a szervezeten belüli és kívüli felhasználók számára, hogy az alkalmazásokhoz való hozzáférést hatékonyabban kezeljék.| Lehetővé teszi, hogy a nem rendszergazdák kezelhesse az alkalmazásaikat [hozzáférési csomagokkal](../governance/entitlement-management-access-package-first.md).|
|Hozzáférési felülvizsgálatok|A felhasználók alkalmazásokhoz való hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő személyek férhessenek hozzájuk.| [Tekintse át](../governance/access-reviews-overview.md) a legérzékenyebb alkalmazásokhoz való hozzáférést. |
|Log Analytics|Jelentéseket készíthet arról, hogy ki fér hozzá az alkalmazásokhoz, és hogyan tárolhatja azokat a SIEM-eszközben, hogy az adatforrások és az adatok között összekapcsolja az információkat.| Engedélyezze a [log Analytics szolgáltatást](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) , és állítson be riasztásokat az alkalmazásokkal kapcsolatos kritikus eseményekhez. |


## <a name="scenario-5-hybrid-secure-access"></a>5. forgatókönyv: hibrid biztonságos hozzáférés
Az identitás csak akkor lehet a vezérlési síkon, ha mindent a felhőben és a helyszíni alkalmazásokban is képes összekapcsolni. Kihasználhatja az Azure AD és partnerei által biztosított eszközöket az örökölt hitelesítésen alapuló alkalmazásokhoz való hozzáférés biztosításához.

|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------|---------|
|Alkalmazásproxy|Napjainkban a munkavállalók a legkülönfélébb helyeken, időpontokban és eszközökön szeretnek dolgozni. Az SaaS-alkalmazásokhoz a helyszíni felhőben és a vállalati alkalmazásokban is hozzá kell férniük. Az Azure AD-alkalmazásproxy lehetővé teszi, hogy ez a robusztus hozzáférés költséges és összetett virtuális magánhálózatok (VPN) vagy vagy demilitarizált zónák (DMZ) nélkül legyen elérhető.|[Távoli hozzáférés](./application-proxy.md) beállítása helyszíni alkalmazásokhoz. |
|F5, Akamai, Zscaler|A meglévő hálózatkezelési és kézbesítési vezérlő használatával könnyedén biztosíthatja az üzleti folyamatok számára kritikus fontosságú, de az Azure AD-vel még nem védett korábbi alkalmazásokat. Valószínűleg már mindent megtalál, amire szüksége lehet az alkalmazások védelmének megkezdéséhez.| Akamai, Citrix, F5 vagy Zscaler használatával? Tekintse meg az [előre elkészített megoldásokat](./secure-hybrid-access.md). | 

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Alkalmazáskezelés](./index.yml)
- [Alkalmazások üzembe helyezése](../app-provisioning/user-provisioning.md)
- [Hibrid biztonságos hozzáférés](./secure-hybrid-access.md)
- [Identitáskezelés](../governance/identity-governance-overview.md)
- [Microsoft-identitásplatform](../develop/v2-overview.md)
- [Identitás biztonsága](../conditional-access/index.yml)