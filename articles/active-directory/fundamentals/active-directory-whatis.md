---
title: Mi az az Azure Active Directory? – Azure Active Directory | Microsoft Docs
description: Áttekintő és elméleti információk a Azure Active Directoryről, beleértve a terminológiát és a rendelkezésre álló licenceket, valamint a kapcsolódó szolgáltatások listáját, amelyekben további információk találhatók.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 06/05/2020
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9e85960f3efc074eca18475c4a3e6c422c1990b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99090841"
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory?

Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása, amely segít az alkalmazottaknak bejelentkezni, és hozzáférni a következő erőforrásokhoz:

- Külső erőforrások, például Microsoft 365, a Azure Portal és több ezer más SaaS-alkalmazás.

- Belső erőforrások, például a vállalati hálózaton és az intraneten lévő alkalmazások, valamint a saját szervezete által fejlesztett felhőalapú alkalmazások. További információ a bérlők létrehozásáról a szervezet számára: gyors útmutató [: új bérlő létrehozása Azure Active Directoryban](active-directory-access-create-new-tenant.md).

Az Azure AD és a Active Directory Domain Services közötti különbség megismeréséhez tekintse meg a [Active Directory összevetése a](active-directory-compare-azure-ad-to-ad.md)következővel: Azure Active Directory. Az Azure-ban, az Azure AD-ben és a Microsoft 365ban az alapvető identitási szolgáltatások jobb megismeréséhez használhatja a [vállalati építészek sorozatának](/microsoft-365/solutions/cloud-architecture-models) plakátjának különböző Microsoft Cloudeit is.

## <a name="who-uses-azure-ad"></a>Ki használja az Azure AD-t?

Az Azure AD a következő célokra szolgál:

- **IT rendszergazdák.** Rendszergazdaként az Azure AD használatával szabályozhatja az alkalmazásaihoz és az alkalmazás-erőforrásaihoz való hozzáférést az üzleti követelmények alapján. Használhatja például az Azure AD-t a többtényezős hitelesítés megköveteléséhez a fontos szervezeti erőforrások elérésekor. Emellett az Azure AD segítségével automatizálhatja a felhasználók üzembe helyezését a meglévő Windows Server AD és a Felhőbeli alkalmazások között, beleértve a Microsoft 365t is. Végül az Azure AD hatékony eszközöket biztosít a felhasználói identitások és hitelesítő adatok védelméhez, valamint a hozzáférési irányítási követelmények teljesítéséhez. Első lépésként regisztráljon egy [30 napos ingyenes prémium szintű Azure Active Directory próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Alkalmazások fejlesztői.** Alkalmazás-fejlesztőként használhatja az Azure AD-t szabvány-alapú megközelítésként az egyszeri bejelentkezés (SSO) az alkalmazáshoz való hozzáadásához, amely lehetővé teszi, hogy a felhasználó már meglévő hitelesítő adataival működjön. Az Azure AD olyan API-kat is biztosít, amelyek segítségével személyre szabott alkalmazásokat hozhat létre meglévő szervezeti adatai alapján. Első lépésként regisztráljon egy [30 napos ingyenes prémium szintű Azure Active Directory próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/). További információt a [fejlesztők Azure Active Directory](../develop/index.yml)is talál.

- **Microsoft 365, Office 365, Azure vagy Dynamics CRM Online-előfizetők.** Előfizetőként már használja az Azure AD-t. Minden egyes Microsoft 365-, Office 365-, Azure- és Dynamics CRM Online-bérlő automatikusan Azure AD-bérlő is. Azonnal elkezdheti az integrált felhőalapú alkalmazásokhoz való hozzáférés kezelését.

## <a name="what-are-the-azure-ad-licenses"></a>Mik az Azure AD-licencek?

A Microsoft Online Business Services, például Microsoft 365 vagy Microsoft Azure esetén az Azure AD-t kell bejelentkeznie, és segítenie kell az Identity Protection szolgáltatásban. Ha előfizet bármely Microsoft Online Business Service szolgáltatásra, automatikusan megkapja az Azure AD-t az összes ingyenes szolgáltatás eléréséhez.

Az Azure AD megvalósításának javítása érdekében a prémium szintű Azure Active Directory P1 vagy prémium P2 licencekre való frissítéssel is hozzáadhat fizetős funkciókat. Az Azure AD fizetős licencek a meglévő ingyenes címtárra épülnek, így önkiszolgáló, továbbfejlesztett monitorozási, biztonsági jelentéskészítési és biztonságos hozzáférést biztosíthat a mobil felhasználói számára.

>[!Note]
>A licencek díjszabási lehetőségeiért tekintse meg a [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/).
>
>A P1 és a prémium P2 prémium szintű Azure Active Directory Kínában jelenleg nem támogatott. Az Azure AD díjszabásával kapcsolatos további információkért forduljon a [Azure Active Directory fórumhoz](https://azure.microsoft.com/support/community/?product=active-directory).

- **ingyenes Azure Active Directory.** Lehetővé teszi a felhasználók és a csoportok felügyeletét, a helyszíni címtár-szinkronizálást, az alapszintű jelentéseket, az önkiszolgáló jelszó-módosítást a felhőalapú felhasználók számára, valamint az egyszeri bejelentkezést az Azure-ban, Microsoft 365 és számos népszerű SaaS-alkalmazást.

- **Prémium szintű Azure Active Directory P1.** Az ingyenes funkciók mellett a P1 lehetővé teszi a hibrid felhasználók számára a helyszíni és a Felhőbeli erőforrások elérését is. Emellett támogatja a speciális felügyeletet, például a dinamikus csoportokat, az önkiszolgáló csoportok felügyeletét, a Microsoft Identity Managert (helyszíni identitás-és hozzáférés-kezelési csomagot) és a felhőalapú írási visszalépési képességeket, amelyek lehetővé teszik az önkiszolgáló jelszó-visszaállítást a helyszíni felhasználók számára.

- **Prémium szintű Azure Active Directory P2.** Az ingyenes és a P1 funkciókon kívül a P2 olyan [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) is biztosít, amelyek segítenek az alkalmazásokhoz és a kritikus fontosságú vállalati adatokhoz való kockázatalapú feltételes hozzáférés biztosításában, valamint [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) a rendszergazdák felderítésére, korlátozására és figyelésére, valamint az erőforrásokhoz való hozzáférésre, valamint az igény szerinti hozzáférés biztosítására.

- **"Pay as go" szolgáltatás-licencek.** További szolgáltatás-licenceket is kaphat, például Azure Active Directory üzleti – ügyfél (B2C) szolgáltatást. A B2C segítségével identitás-és hozzáférés-kezelési megoldásokat biztosíthat az ügyfelek felé irányuló alkalmazásaihoz. További információ: [Azure Active Directory B2C dokumentáció](../../active-directory-b2c/index.yml).

Az Azure-előfizetések Azure AD-hez való társításával kapcsolatos további információkért lásd: [Az Azure-előfizetés társítása és hozzáadása a Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md) , valamint a licencek felhasználókhoz való hozzárendelésével kapcsolatos további információk [: útmutató: Azure Active Directory-licencek hozzárendelése vagy eltávolítása](license-users-groups.md).

## <a name="which-features-work-in-azure-ad"></a>Mely funkciók működnek az Azure AD-ben?

Miután kiválasztotta az Azure AD-licencét, hozzáférhet a szervezete következő szolgáltatásainak egy részéhez vagy mindegyikéhez:

|Kategória|Leírás|
|-------|-----------|
|Alkalmazáskezelés|A Felhőbeli és helyszíni alkalmazásokat az alkalmazásproxy, az egyszeri bejelentkezés, a saját alkalmazások portál (más néven hozzáférési panel) és a szolgáltatott szoftverek (SaaS) használatával kezelheti. További információkért lásd: [biztonságos távelérés biztosítása a helyszíni alkalmazásokhoz](../manage-apps/application-proxy.md) és az [alkalmazás-felügyeleti dokumentációhoz](../manage-apps/index.yml).|
|Hitelesítés|Felügyelheti Azure Active Directory önkiszolgáló jelszó-visszaállítást, Multi-Factor Authentication, egyéni tiltott jelszavak listáját és az intelligens zárolást. További információ: [Azure ad-hitelesítés dokumentációja](../authentication/index.yml).|
|Azure Active Directory fejlesztők számára|Olyan alkalmazásokat hozhat létre, amelyek az összes Microsoft-identitást bejelentkeznek, és a Microsoft Graph, más Microsoft API-k vagy egyéni API-k meghívására kapnak tokeneket. További információ: [Microsoft Identity platform (Azure Active Directory for Developers)](../develop/index.yml).|
|Vállalatközi (B2B)|Kezelheti vendégeit és külső partnereit, miközben felügyelheti a saját céges adatait. További információ: [Azure Active Directory B2B-dokumentáció](../external-identities/index.yml).|
|Vállalat és ügyfél közötti (B2C)|Testreszabhatja és szabályozhatja, hogy a felhasználók hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik profiljaikat az alkalmazások használatakor. További információ: [Azure Active Directory B2C dokumentáció](../../active-directory-b2c/index.yml).|
|Feltételes hozzáférés|A felhőalapú alkalmazásokhoz való hozzáférés kezelése. További információt az [Azure ad feltételes hozzáférési dokumentációjában](../conditional-access/index.yml)talál.|
|Eszközkezelés|Kezelheti, hogyan férnek hozzá vállalati adataihoz a felhőbeli vagy helyszíni eszközei. További információ: az [Azure ad-eszközkezelés dokumentációja](../devices/index.yml).|
|Tartományi szolgáltatások|Az Azure-beli virtuális gépeket tartományvezérlők használata nélkül csatlakoztathatja egy tartományhoz. További információ: [Azure ad Domain Services dokumentáció](../../active-directory-domain-services/index.yml).|
|Vállalati felhasználók|A licenc-hozzárendelések kezelése, az alkalmazások elérése és a delegált csoportok és rendszergazdai szerepkörök használatával történő beállítása. További információ: [Azure Active Directory felhasználói felügyeleti dokumentáció](../enterprise-users/index.yml).|
|Hibrid identitás|A Azure Active Directory Connect és a kapcsolódási állapot használatával egyetlen felhasználói identitást biztosíthat a hitelesítéshez és az engedélyezéshez minden erőforráshoz, a helytől (Felhőbeli vagy helyszíni) függetlenül. További információ: [hibrid identitás dokumentációja](../hybrid/index.yml).|
|Identitáskezelés|Szervezete személyazonosságának kezelése az alkalmazottak, az üzleti partner, a szállító, a szolgáltatás és az alkalmazás-hozzáférés-vezérlés használatával. A hozzáférési felülvizsgálatokat is elvégezheti. További információ: [Azure ad Identity irányítási dokumentáció](../governance/identity-governance-overview.md) és [Azure ad hozzáférési felülvizsgálatok](../governance/access-reviews-overview.md).|
|Identity protection|A szervezet identitásait érintő lehetséges sebezhetőségek észlelése, a szabályzatok konfigurálása a gyanús műveletekre való válaszadásra, majd a szükséges műveletek elvégzése a megoldáshoz. További információ: [Azure ad Identity Protection](../identity-protection/index.yml).|
|Azure-erőforrások felügyelt identitásai|Az Azure AD-ben automatikusan felügyelt identitással biztosítja az Azure-szolgáltatásokat, amely képes hitelesíteni bármely Azure AD által támogatott hitelesítési szolgáltatást, beleértve a Key Vault is. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md).|
|Privileged Identity Management (PIM)|A szervezeten belüli hozzáférés kezelése, szabályozása és figyelése. Ez a szolgáltatás magában foglalja az Azure AD-ben és az Azure-ban, valamint a Microsoft egyéb online szolgáltatásaiban (például Microsoft 365 vagy Intune) elérhető erőforrásokat További információ: [Azure ad Privileged Identity Management](../privileged-identity-management/index.yml).|
|Jelentések és monitorozás|Betekintést nyerhet a környezet biztonsági és használati szokásaiba. További információ: [Azure Active Directory jelentések és figyelés](../reports-monitoring/index.yml).|

## <a name="terminology"></a>Terminológia

Az Azure AD és a hozzá tartozó dokumentáció jobb megismerése érdekében javasoljuk, hogy tekintse át az alábbi feltételeket.

|Kifejezés vagy fogalom|Leírás|
|---------------|-----------|
|Identitás| Olyan dolog, amely hitelesíthető lehet. Az identitás lehet felhasználónévvel és jelszóval rendelkező felhasználó. Az identitások olyan alkalmazásokat vagy más kiszolgálókat is tartalmaznak, amelyekhez titkos kulcsokon vagy tanúsítványokon keresztül lehet hitelesítést igényelni.|
|Fiók| A hozzá társított adatokkal rendelkező identitás. Identitás nélkül nem lehet fiókja.|
|Azure AD-fiók| Az Azure AD-n vagy egy másik Microsoft Cloud Service-en keresztül létrehozott identitás, például Microsoft 365. Az identitások az Azure AD-ben tárolódnak, és elérhetők a szervezet Cloud Service-előfizetései számára. Ezt a fiókot más néven munkahelyi vagy iskolai fióknak is nevezik.|
|Fiókadminisztrátor|Ez a klasszikus előfizetés-rendszergazdai szerepkör fogalmilag egy előfizetés számlázási tulajdonosa. Ez a szerepkör hozzáfér a [Azure Fiókközponthoz](https://account.azure.com/Subscriptions) , és lehetővé teszi a fiókban lévő összes előfizetés kezelését. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Szolgáltatás-rendszergazda|Ez a klasszikus előfizetés-rendszergazdai szerepkör lehetővé teszi az összes Azure-erőforrás kezelését, beleértve a hozzáférést is. Ez a szerepkör egyenértékű hozzáféréssel rendelkezik egy olyan felhasználóhoz, aki az előfizetés hatókörében tulajdonosi szerepkört rendelt hozzá. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Tulajdonos|Ez a szerepkör segít az összes Azure-erőforrás kezelésében, beleértve a hozzáférést is. Ez a szerepkör egy Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) nevű újabb engedélyezési rendszerre épül, amely részletes hozzáférés-kezelést biztosít az Azure-erőforrásokhoz. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globális Azure AD-rendszergazda|Ez a rendszergazdai szerepkör automatikusan hozzá lett rendelve az Azure AD-bérlőhöz. A globális rendszergazdák az Azure AD összes felügyeleti funkcióját és az Azure AD-be összevonása szolgáltatásokat (például Exchange Online, SharePoint Online és Skype vállalati online) is elvégezhetik. Több globális rendszergazda is lehet, de csak a globális rendszergazdák rendelhetnek rendszergazdai szerepköröket (beleértve más globális rendszergazdák hozzárendelését is) a felhasználók számára. A különböző rendszergazdai szerepkörökkel kapcsolatos további információkért lásd: [rendszergazdai szerepkör engedélyei Azure Active Directory](../roles/permissions-reference.md).|
|Azure-előfizetés| Az Azure Cloud Services fizetésére használatos. Több előfizetéssel is rendelkezhet, és ezek egy hitelkártyához vannak társítva.|
|Azure-bérlő| Az Azure AD dedikált és megbízható példánya, amely akkor jön létre automatikusan, amikor a szervezet regisztrál egy Microsoft Cloud Service-előfizetésre, például Microsoft Azure, Microsoft Intune vagy Microsoft 365. Az Azure-bérlő egyetlen szervezetet jelöl.|
|Egybérlős alkalmazás| A dedikált környezetben más szolgáltatásokhoz hozzáférő Azure-bérlők egyetlen bérlőnek tekintendők.|
|Több-bérlős| Azok az Azure-bérlők, amelyek más szolgáltatásokhoz férnek hozzá egy megosztott környezetben, több szervezeten belül több-bérlőnek számítanak.|
|Azure AD-címtár|Minden egyes Azure-bérlő dedikált és megbízható Azure AD-címtárral rendelkezik. Az Azure AD-címtár tartalmazza a bérlő felhasználóit, csoportjait és alkalmazásait, és a bérlői erőforrások identitás-és hozzáférés-kezelési funkcióinak végrehajtásához használatos.|
|Egyéni tartomány|Minden új Azure AD-címtárhoz tartozik egy kezdeti tartománynév, a domainname.onmicrosoft.com. A kezdeti név mellett felveheti a szervezet tartományneveit is, amelyek tartalmazzák az üzleti és a felhasználók által a szervezet erőforrásaihoz való hozzáféréshez használt neveket a listához. Az egyéni tartománynevek hozzáadásával a felhasználók számára ismerős felhasználóneveket hozhat létre, például: alain@contoso.com .|
|Microsoft-fiók (más néven, MSA)|Személyes fiókok, amelyek hozzáférést biztosítanak a Microsoft-termékekhez és a felhőalapú szolgáltatásokhoz, például az Outlookhoz, a OneDrive, az Xbox LIVE-hoz vagy a Microsoft 365hoz. A Microsoft-fiók a Microsoft által futtatott Microsoft fogyasztói identitás-fiók rendszerében jön létre és tárolja.|

## <a name="next-steps"></a>Következő lépések

- [Prémium szintű Azure Active Directory – Regisztráció](active-directory-get-started-premium.md)

- [Azure-előfizetés hozzárendelése a Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)

- [prémium szintű Azure Active Directory P2 szolgáltatás üzembe helyezési ellenőrzőlistája](active-directory-deployment-checklist-p2.md)
