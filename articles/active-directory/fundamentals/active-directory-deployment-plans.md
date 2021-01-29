---
title: Központi telepítési tervek – Azure Active Directory | Microsoft Docs
description: A számos Azure Active Directory funkció üzembe helyezésének teljes körű útmutatója.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4185ffd644d54c419f42c78326ca10bf100443c3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051430"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-üzembehelyezési tervek
Az Azure Active Directory (Azure AD) képességeinek üzembe helyezésével kapcsolatos teljes körű útmutatást keres? Az Azure AD üzembehelyezési csomagjai végigvezetik a közös Azure AD-képességek sikeres üzembe helyezéséhez szükséges üzleti értékeken, tervezési szempontokon és üzemeltetési eljárásokon.

A csomag bármelyik lapján a böngészőben a PDF-fájl nyomtatása lehetőséggel létrehozhat egy naprakész offline verziót a dokumentációban.


## <a name="deploy-authentication"></a>Hitelesítés telepítése

| Képesség | Leírás|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Az Azure AD Multi-Factor Authentication (MFA) a Microsoft kétlépéses ellenőrzési megoldása. A rendszergazda által jóváhagyott hitelesítési módszerek használatával az Azure AD MFA segít megőrizni az adataihoz és alkalmazásaihoz való hozzáférést, miközben az egyszerű bejelentkezési folyamat iránti igényt is kielégíti. Tekintse meg ezt a videót arról, [hogyan konfigurálhatja és kényszerítheti a többtényezős hitelesítés használatát a bérlőben](https://www.youtube.com/watch?v=qNndxl7gqVM)|
| [Feltételes hozzáférés](../conditional-access/plan-conditional-access.md)| A feltételes hozzáféréssel olyan automatizált hozzáférés-vezérlési döntéseket hozhat létre, amelyek a feltételek alapján hozzáférhetnek a felhőalapú alkalmazásokhoz. |
| [Új jelszó önkiszolgáló kérése](../authentication/howto-sspr-deployment.md)| Az önkiszolgáló jelszó-visszaállítás segítségével a felhasználók rendszergazdai beavatkozás nélkül állíthatják vissza a jelszavukat, amikor és ahol szükségesek. |
| [Jelszó nélküli](../authentication/howto-authentication-passwordless-deployment.md) | Jelszó-alapú hitelesítés implementálása a szervezet Microsoft Authenticator alkalmazás-vagy FIDO2 biztonsági kulcsaival |

## <a name="deploy-application-and-device-management"></a>Alkalmazások és eszközök felügyeletének központi telepítése

| Képesség | Leírás|
| -| - |
| [Egyszeri bejelentkezés](../manage-apps/plan-sso-deployment.md)| Az egyszeri bejelentkezés lehetővé teszi, hogy a felhasználók csak egyszer jelentkezzenek be az üzleti életbe lépésekhez szükséges alkalmazásokhoz és erőforrásokhoz. Miután bejelentkezett, a Microsoft Office SalesForce a belső alkalmazásokhoz, anélkül, hogy másodszor is meg kellene adniuk a hitelesítő adatokat. |
| [Saját alkalmazások](../manage-apps/my-apps-deployment-plan.md)| Egy egyszerű központot biztosít a felhasználóknak az összes alkalmazás felderítéséhez és eléréséhez. Hatékonyabbá teheti őket az önkiszolgáló képességekkel, például az alkalmazásokhoz és csoportokhoz való hozzáférés kérelmezéséhez, illetve mások nevében az erőforrásokhoz való hozzáférés kezeléséhez. |
| [Eszközök](../devices/plan-device-deployment.md) | Ez a cikk segítséget nyújt az eszköz Azure AD-vel való integrálásának módszereinek kiértékeléséhez, a megvalósítási terv kiválasztásához, valamint a támogatott Eszközkezelő eszközökre mutató legfontosabb hivatkozásokat. |


## <a name="deploy-hybrid-scenarios"></a>Hibrid forgatókönyvek üzembe helyezése

| Képesség | Leírás|
| -| -|
| [ADFS a jelszókivonat-szinkronizáláshoz](../hybrid/plan-migrate-adfs-password-hash-sync.md)| A jelszó-kivonatolási szinkronizálással a felhasználói jelszavak kivonatait a helyszíni Active Directoryról az Azure AD-be szinkronizálja, így az Azure AD hitelesíti a felhasználókat a helyszíni Active Directoryekkel való interakció nélkül. |
| [ADFS az átmenő hitelesítéshez](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Az Azure AD átmenő hitelesítéssel a felhasználók ugyanazzal a jelszóval jelentkezhetnek be mind a helyszíni, mind a felhőalapú alkalmazásokba. Ez a funkció jobb felhasználói élményt nyújt a felhasználóknak – eggyel kevesebb jelszót kell megjegyeznie – és csökkenti az informatikai támogatási szolgálat költségeit, mivel a felhasználók kevésbé valószínű, hogy bejelentkeznek. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat. |
| [Azure-AD Application Proxy](../manage-apps/application-proxy-deployment-plan.md) |Napjainkban a munkavállalók a legkülönfélébb helyeken, időpontokban és eszközökön szeretnek dolgozni. Az SaaS-alkalmazásokhoz a helyszíni felhőben és a vállalati alkalmazásokban is hozzá kell férniük. Az Azure AD-alkalmazásproxy lehetővé teszi, hogy ez a robusztus hozzáférés költséges és összetett virtuális magánhálózatok (VPN) vagy vagy demilitarizált zónák (DMZ) nélkül legyen elérhető. |
| [Közvetlen egyszeri bejelentkezés](../hybrid/how-to-connect-sso-quick-start.md)| Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. Ezzel a funkcióval a felhasználóknak nem kell beírniuk a jelszavukat, hogy bejelentkezzenek az Azure AD-be, és általában nem kell megadniuk a felhasználónevét. Ez a funkció lehetővé teszi, hogy a jogosult felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz anélkül, hogy további helyszíni összetevőket kellene megadniuk. |

## <a name="deploy-user-provisioning"></a>Felhasználói kiépítés központi telepítése

| Képesség | Leírás|
| -| -|
| [Felhasználók regisztrálása](../app-provisioning/plan-auto-user-provisioning.md)| Az Azure AD-vel automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását a felhőalapú (SaaS-) alkalmazásokban, például a Dropboxban, a Salesforce-ban vagy a ServiceNow-ban. |
| [Felhőbeli HR-felhasználó kiépítés](../app-provisioning/plan-cloud-hr-provision.md)| A Felhőbeli HR-felhasználók kiépítése a Active Directory létrehoz egy alapot a folyamatos identitások irányításához, és javítja a mérvadó személyazonossági adatokra támaszkodó üzleti folyamatok minőségét. Ha ezt a szolgáltatást a Felhőbeli HR-termékkel, például a munkanapokkal vagy a SuccessFactors együtt használja, zökkenőmentesen kezelheti az alkalmazottak és a függőben lévők identitásának életciklusát úgy, hogy olyan szabályokat konfigurál, amelyekkel az asztalos küldési műveletek (például a létrehozás, az engedélyezés, a Letiltás) |

## <a name="deploy-governance-and-reporting"></a>Irányítás és jelentéskészítés üzembe helyezése

| Képesség | Leírás|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) segítségével felügyelheti az Azure AD, az Azure-erőforrások és más Microsoft Online Services Kiemelt felügyeleti szerepköreit. A PIM olyan megoldásokat kínál, mint az igény szerinti hozzáférés, a jóváhagyási munkafolyamatok kérése és a teljes körűen integrált hozzáférési felülvizsgálatok, amelyek segítségével valós időben azonosíthatja, feltárhatja és megakadályozhatja a Kiemelt szerepkörök rosszindulatú tevékenységeit. |
| [Jelentéskészítés és figyelés](../reports-monitoring/plan-monitoring-and-reporting.md)| Az Azure AD jelentéskészítési és figyelési megoldásának kialakítása a jogi, biztonsági és üzemeltetési követelményektől, valamint a meglévő környezettől és folyamattól függ. Ez a cikk bemutatja a különböző kialakítási lehetőségeket, és végigvezeti Önt a megfelelő üzembe helyezési stratégiában. |
| [Hozzáférési felülvizsgálatok](../governance/deploy-access-reviews.md) | A hozzáférési felülvizsgálatok fontos részét képezik az irányítási stratégiának, amely lehetővé teszi, hogy megismerje és felügyelje, ki férhet hozzá, és hogy mire van hozzáférése. Ez a cikk segítséget nyújt a hozzáférési felülvizsgálatok megtervezésében és üzembe helyezésében a kívánt biztonsági és együttműködési testhelyzetek eléréséhez. |

## <a name="include-the-right-stakeholders"></a>A megfelelő résztvevők belefoglalása

Amikor megkezdi az üzembe helyezést egy új képesség megtervezése során, fontos, hogy a szervezeten belül kulcsfontosságú érdekelteket vegyen fel. Javasoljuk, hogy azonosítsa és dokumentálja azokat a személyeket vagy személyeket, akik a következő szerepköröket teljesítik, és működjenek együtt velük a projektben való részvételük meghatározásához.  

A szerepkörök a következők lehetnek: 

|Szerepkör |Leírás |
|-|-|
|Végfelhasználó|Azoknak a felhasználóknak a reprezentatív csoportja, amelyekhez a képességet alkalmazni kívánja. Gyakran megtekinti a kísérleti program módosításait.
|INFORMATIKAI támogatás kezelője|Támogatja a szervezeti képviselőt, aki megadhatja a változás támogatását a helpdesk szemszögéből.  
|Identity Architect vagy Azure globális rendszergazda|Az Identitáskezelés csapatának képviselője, amely meghatározza, hogy a változás hogyan igazodik a szervezet alapvető Identity Management infrastruktúrához.|
|Alkalmazás üzleti tulajdonosa |Az érintett alkalmazás (ok) általános üzleti tulajdonosa, amely magában foglalhatja a hozzáférés kezelését is.A felhasználói élmény és a változás hasznosságát is megadhatja a végfelhasználó szemszögéből.
|Biztonsági tulajdonos|A biztonsági csapat képviselője, amely kijelentkezhet, hogy a terv megfelel a szervezete biztonsági követelményeinek.|
|Compliance Manager|A szervezeten belül a vállalat, az iparág vagy a kormányzati követelmények teljesítésének biztosításáért felelős személy.|

**A részvételi szintek a következők lehetnek:**

- Az **R** esponsible a projekt tervének és eredményének megvalósításához 

- **Pproval és**-eredmény 

- **C** ontributor a projekt tervéhez és eredményéhez 

- **Nformed és** végeredmény


## <a name="best-practices-for-a-pilot"></a>Ajánlott eljárások a pilóták számára
Egy próba lehetővé teszi egy kis csoport tesztelését, mielőtt mindenki számára bekapcsolja a funkciót. Győződjön meg arról, hogy a tesztelés részeként a szervezeten belül minden használati esetet alaposan teszteltek. A legjobb megoldás, ha a kísérleti felhasználók egy adott csoportját szeretné megcélozni, mielőtt a szervezet egészét bevezeti.

Az első hullámban célozza meg, a használhatóságot és az egyéb megfelelő felhasználókat, akik kipróbálhatják és elküldhetik a visszajelzést. Ezzel a visszajelzéssel tovább fejlesztheti a felhasználóknak küldött kommunikációt és útmutatást, és betekintést nyerhet a támogatási munkatársak által látható problémák típusaiba. 

A bevezetést nagyobb felhasználói csoportokra kell kiterjeszteni a megcélzott csoport (ok) hatókörének növelésével. Ez a [dinamikus csoporttagság](../enterprise-users/groups-dynamic-membership.md)használatával végezhető el, vagy manuálisan is hozzáadhatja a felhasználókat a megcélozott csoport (ok) hoz.
