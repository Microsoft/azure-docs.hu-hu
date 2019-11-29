---
title: Központi telepítési tervek – Azure Active Directory | Microsoft Docs
description: A számos Azure Active Directory funkció üzembe helyezésének teljes körű útmutatója.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e6708225262349d56c6e261895882e9c31677f
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558531"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-üzembehelyezési tervek
Az Azure Active Directory (Azure AD) képességeinek üzembe helyezésével kapcsolatos teljes körű útmutatást keres? Az Azure AD üzembehelyezési csomagjai végigvezetik a közös Azure AD-képességek sikeres üzembe helyezéséhez szükséges üzleti értékeken, tervezési szempontokon és üzemeltetési eljárásokon.

A csomag bármelyik lapján a böngészőben a PDF-fájl nyomtatása lehetőséggel létrehozhat egy naprakész offline verziót a dokumentációban.
## <a name="include-the-right-stakeholders"></a>A megfelelő résztvevők belefoglalása

Amikor megkezdi az üzembe helyezést egy új képesség megtervezése során, fontos, hogy a szervezeten belül kulcsfontosságú érdekelteket vegyen fel. Javasoljuk, hogy azonosítsa és dokumentálja azokat a személyeket vagy személyeket, akik a következő szerepköröket teljesítik, és működjenek együtt velük a projektben való részvételük meghatározásához.  

A szerepkörök a következők lehetnek: 

|Szerepkör |Leírás |
|-|-|
|Végfelhasználó|Azoknak a felhasználóknak a reprezentatív csoportja, amelyekhez a képességet alkalmazni kívánja. Gyakran megtekinti a kísérleti program módosításait.
|INFORMATIKAI támogatás kezelője|Támogatja a szervezeti képviselőt, aki megadhatja a változás támogatását a helpdesk szemszögéből.  
|Identity Architect vagy Azure globális rendszergazda|Az Identitáskezelés csapatának képviselője, amely meghatározza, hogy a változás hogyan igazodik a szervezet alapvető Identity Management infrastruktúrához.|
|Alkalmazás üzleti tulajdonosa |Az érintett alkalmazás (ok) általános üzleti tulajdonosa, amely magában foglalhatja a hozzáférés kezelését is.  A felhasználói élmény és a változás hasznosságát is megadhatja a végfelhasználó szemszögéből.
|Biztonsági tulajdonos|A biztonsági csapat képviselője, amely kijelentkezhet, hogy a terv megfelel a szervezete biztonsági követelményeinek.|
|Megfelelőség-kezelő|A szervezeten belül a vállalat, az iparág vagy a kormányzati követelmények teljesítésének biztosításáért felelős személy.|

**A részvételi szintek a következők lehetnek:**

- Az **R**esponsible a projekt tervének és eredményének megvalósításához 

- **Pproval és**-eredmény 

- **C**ontributor a projekt tervéhez és eredményéhez 

- **Nformed és**végeredmény


## <a name="best-practices-for-a-pilot"></a>Ajánlott eljárások a pilóták számára
Egy próba lehetővé teszi egy kis csoport tesztelését, mielőtt mindenki számára bekapcsolja a funkciót. Győződjön meg arról, hogy a tesztelés részeként a szervezeten belül minden használati esetet alaposan teszteltek. A legjobb megoldás, ha a kísérleti felhasználók egy adott csoportját szeretné megcélozni, mielőtt a szervezet egészét bevezeti.

Az első hullámban célozza meg, a használhatóságot és az egyéb megfelelő felhasználókat, akik kipróbálhatják és elküldhetik a visszajelzést. Ezzel a visszajelzéssel tovább fejlesztheti a felhasználóknak küldött kommunikációt és útmutatást, és betekintést nyerhet a támogatási munkatársak által látható problémák típusaiba. 

A bevezetést nagyobb felhasználói csoportokra kell kiterjeszteni a megcélzott csoport (ok) hatókörének növelésével. Ez a [dinamikus csoporttagság](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)használatával végezhető el, vagy manuálisan is hozzáadhatja a felhasználókat a megcélozott csoport (ok) hoz.


## <a name="deploy-authentication"></a>Hitelesítés telepítése

| Szolgáltatás | Leírás|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Az Azure Multi-Factor Authentication (MFA) a Microsoft kétlépéses hitelesítési megoldása. A rendszergazda által jóváhagyott hitelesítési módszerek használatával az Azure MFA segít megőrizni az adataihoz és alkalmazásaihoz való hozzáférést, miközben az egyszerű bejelentkezési folyamat iránti igényt is kielégíti. |
| [Feltételes hozzáférés](https://aka.ms/deploymentplans/ca)| A feltételes hozzáféréssel olyan automatizált hozzáférés-vezérlési döntéseket hozhat létre, amelyek a feltételek alapján hozzáférhetnek a felhőalapú alkalmazásokhoz. |
| [Új jelszó önkiszolgáló kérése](https://aka.ms/deploymentplans/sspr)| Az önkiszolgáló jelszó-visszaállítás segítségével a felhasználók rendszergazdai beavatkozás nélkül állíthatják vissza a jelszavukat, amikor és ahol szükségesek. |
| [Passwordless](https://aka.ms/deploymentplans/passwordless) | Jelszó-alapú hitelesítés implementálása a szervezet Microsoft Authenticator alkalmazás-vagy FIDO2 biztonsági kulcsaival |

## <a name="deploy-application-management"></a>Az alkalmazások felügyeletének központi telepítése

| Szolgáltatás | Leírás|
| -| - |
| [Egyszeri bejelentkezés](https://aka.ms/deploymentplans/sso)| Az egyszeri bejelentkezés lehetővé teszi, hogy a felhasználók csak egyszer jelentkezzenek be az üzleti életbe lépésekhez szükséges alkalmazásokhoz és erőforrásokhoz. Miután bejelentkezett, a Microsoft Office SalesForce a belső alkalmazásokhoz, anélkül, hogy másodszor is meg kellene adniuk a hitelesítő adatokat. |
| [Hozzáférési panel](https://aka.ms/deploymentplans/accesspanel)| Egy egyszerű központot biztosít a felhasználóknak az összes alkalmazás felderítéséhez és eléréséhez. Hatékonyabbá teheti őket az önkiszolgáló képességekkel, például az alkalmazásokhoz és csoportokhoz való hozzáférés kérelmezéséhez, illetve mások nevében az erőforrásokhoz való hozzáférés kezeléséhez. |


## <a name="deploy-hybrid-scenarios"></a>Hibrid forgatókönyvek üzembe helyezése

| Szolgáltatás | Leírás|
| -| -|
| [ADFS a jelszókivonat-szinkronizáláshoz](https://aka.ms/deploymentplans/adfs2phs)| A jelszó-kivonatolási szinkronizálással a felhasználói jelszavak kivonatait a helyszíni Active Directoryról az Azure AD-be szinkronizálja, így az Azure AD hitelesíti a felhasználókat a helyszíni Active Directoryekkel való interakció nélkül. |
| [ADFS az átmenő hitelesítéshez](https://aka.ms/deploymentplans/adfs2pta)| Az Azure AD átmenő hitelesítéssel a felhasználók ugyanazzal a jelszóval jelentkezhetnek be mind a helyszíni, mind a felhőalapú alkalmazásokba. Ez a funkció jobb felhasználói élményt nyújt a felhasználóknak – eggyel kevesebb jelszót kell megjegyeznie – és csökkenti az informatikai támogatási szolgálat költségeit, mivel a felhasználók kevésbé valószínű, hogy bejelentkeznek. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat. |
| [Azure AD-alkalmazásproxy](https://aka.ms/deploymentplans/appproxy)| Napjainkban a munkavállalók a legkülönfélébb helyeken, időpontokban és eszközökön szeretnek dolgozni. Az SaaS-alkalmazásokhoz a helyszíni felhőben és a vállalati alkalmazásokban is hozzá kell férniük. Az Azure AD-alkalmazásproxy lehetővé teszi, hogy ez a robusztus hozzáférés költséges és összetett virtuális magánhálózatok (VPN) vagy vagy demilitarizált zónák (DMZ) nélkül legyen elérhető. |
| [Közvetlen egyszeri bejelentkezés](../hybrid/how-to-connect-sso-quick-start.md)| Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. Ezzel a funkcióval a felhasználóknak nem kell beírniuk a jelszavukat, hogy bejelentkezzenek az Azure AD-be, és általában nem kell megadniuk a felhasználónevét. Ez a funkció lehetővé teszi, hogy a jogosult felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz anélkül, hogy további helyszíni összetevőket kellene megadniuk. |

## <a name="deploy-user-provisioning"></a>Felhasználói kiépítés központi telepítése

| Szolgáltatás | Leírás|
| -| -|
| [Felhasználók regisztrálása](https://aka.ms/deploymentplans/userprovisioning)| Az Azure AD-vel automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását a felhőalapú (SaaS-) alkalmazásokban, például a Dropboxban, a Salesforce-ban vagy a ServiceNow-ban. |
| [Felhőbeli HR-felhasználó kiépítés](https://aka.ms/deploymentplans/cloudhr)| A Felhőbeli HR-felhasználók kiépítése a Active Directory létrehoz egy alapot a folyamatos identitások irányításához, és javítja a mérvadó személyazonossági adatokra támaszkodó üzleti folyamatok minőségét. Ha ezt a szolgáltatást a Felhőbeli HR-termékkel, például a munkanapokkal vagy a SuccessFactors együtt használja, zökkenőmentesen kezelheti az alkalmazottak és a függőben lévő munkavégzők identitás-életciklusát, Átvitel) az IT-létesítési műveletekhez (például Létrehozás, engedélyezés, Letiltás) |

## <a name="deploy-governance-and-reporting"></a>Irányítás és jelentéskészítés üzembe helyezése

| Szolgáltatás | Leírás|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) segítségével felügyelheti az Azure AD, az Azure-erőforrások és más Microsoft Online Services Kiemelt felügyeleti szerepköreit. A PIM olyan megoldásokat kínál, mint az igény szerinti hozzáférés, a jóváhagyási munkafolyamatok kérése és a teljes körűen integrált hozzáférési felülvizsgálatok, amelyek segítségével valós időben azonosíthatja, feltárhatja és megakadályozhatja a Kiemelt szerepkörök rosszindulatú tevékenységeit. |
| [Jelentéskészítés és figyelés](https://aka.ms/deploymentplans/reporting)| Az Azure AD jelentéskészítési és figyelési megoldásának kialakítása a jogi, biztonsági és üzemeltetési követelményektől, valamint a meglévő környezettől és folyamattól függ. Ez a cikk bemutatja a különböző kialakítási lehetőségeket, és végigvezeti Önt a megfelelő üzembe helyezési stratégiában. |
