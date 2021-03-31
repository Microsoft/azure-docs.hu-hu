---
title: Identitás-szabályozás – Azure Active Directory | Microsoft Docs
description: A Azure Active Directory Identity Governance lehetővé teszi, hogy a megfelelő folyamatokkal és láthatósággal egyensúlyt biztosítson a szervezete számára a biztonság és az alkalmazottak hatékonysága iránt.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e02df83d4b7874a1d158aae45f1619eb543e0aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92362450"
---
# <a name="what-is-azure-ad-identity-governance"></a>Mi az Azure AD Identity Governance?

A Azure Active Directory (Azure AD) Identity-szabályozás lehetővé teszi, hogy a megfelelő folyamatokkal és láthatósággal egyensúlyt biztosítson a szervezete számára a biztonság és az alkalmazottak hatékonysága szempontjából. Olyan képességeket biztosít, amelyekkel biztosíthatja, hogy a megfelelő személyek rendelkezzenek a megfelelő erőforrásokhoz való megfelelő hozzáféréssel. Ezek és a kapcsolódó Azure AD-és Enterprise Mobility + Security-funkciók lehetővé teszik a hozzáférés kockázatának enyhítését a kritikus fontosságú eszközökhöz való hozzáférés védelme, monitorozása és naplózása révén, ugyanakkor biztosítva az alkalmazottak és az üzleti partnerek termelékenységét.  

Az Identitáskezelés lehetővé teszi a szervezetek számára, hogy a következő feladatokat hajtsák végre az alkalmazottak, az üzleti partnerek és a szállítók, valamint a helyszíni és a Felhőbeli szolgáltatások és alkalmazások között:

- Az identitás életciklusának szabályozása
- Hozzáférési életciklus szabályozása
- Biztonságos privilegizált hozzáférés az adminisztrációhoz

Konkrétan a következő négy fő kérdés megválaszolására szolgál a szervezetek számára:

- Mely felhasználók férhetnek hozzá az erőforrásokhoz?
- Mik azok a felhasználók, akik ezt a hozzáférést használják?
- Vannak hatékony szervezeti szabályozások a hozzáférés kezeléséhez?
- Ellenőrizhető, hogy a vezérlők működnek-e?

## <a name="identity-lifecycle"></a>Identitás életciklusa

Az identitások szabályozása révén a szervezetek a *termelékenység* egyensúlyát érhetik el – milyen gyorsan férhet hozzá egy személy a szükséges erőforrásokhoz, például ha a szervezethez csatlakoznak? És *Biztonság* – Hogyan változnak a hozzáférésük az idő múlásával, például az adott személy foglalkoztatási állapotának változásai miatt?  Az identitás-életciklus kezelése az identitások irányításának alapja, és a hatékony irányítás érdekében az alkalmazásokhoz szükséges az identitás-életciklus-kezelési infrastruktúra korszerűsítése.

![Identitás életciklusa](./media/identity-governance-overview/identity-lifecycle.png)

Számos szervezet esetében az alkalmazottak identitás-életciklusa az adott felhasználó egy HCM (Human Capital Management) rendszerbeli ábrázolásához van kötve.  A prémium szintű Azure AD a  [Felhőbeli HR-alkalmazásban az Azure Active Directory felhasználó-létesítési tervezési útmutatóban](../app-provisioning/plan-cloud-hr-provision.md)leírtak szerint automatikusan megtartja a felhasználói identitásokat a munkanapokon és a SuccessFactors Active Directory és Azure Active Directoryben szereplő személyek számára.  A prémium szintű Azure AD [Microsoft Identity Manager](/microsoft-identity-manager/)is tartalmaz, amely a helyszíni HCM-rendszerekből, például az SAP HCM-ből, az Oracle-től és az Oracle PeopleSoft-ból tud adatokat importálni.

A forgatókönyvek egyre nagyobb mértékben igénylik a szervezeten kívüli személyekkel való együttműködést. Az [Azure ad B2B](/azure/active-directory/b2b/) együttműködés lehetővé teszi, hogy biztonságosan ossza meg szervezete alkalmazásait és szolgáltatásait a vendég felhasználókkal és a külső partnerekkel bármely szervezettől, miközben a saját vállalati adatok felett tartja a felügyeletet.  Az [Azure ad-jogosultságok kezelése](entitlement-management-overview.md) lehetővé teszi annak kiválasztását, hogy mely szervezet felhasználói férhetnek hozzá a hozzáféréshez, és hogyan vehetők fel B2B-vendégként a szervezeti címtárba, és biztosíthatja, hogy ezek a vendégek el legyenek távolítva, ha már nem férnek hozzá.

## <a name="access-lifecycle"></a>Hozzáférési életciklus

A szervezeteknek olyan folyamatra van szükségük, amely túllépi az adott felhasználó identitásának létrehozásakor az eredetileg kiépített hozzáférést.  Emellett a vállalati szervezeteknek képesnek kell lenniük a hatékony méretezésre, hogy folyamatosan képesek legyenek a hozzáférési szabályzatok és ellenőrzések fejlesztésére és betartatására.

![Hozzáférési életciklus](./media/identity-governance-overview/access-lifecycle.png)

Ez általában a hozzáférés-jóváhagyási döntéseket delegálja az üzleti döntéshozóknak.  Emellett maguk is bejárhatják a felhasználókat.  A vállalati marketing alkalmazásban található bizalmas ügyféladatokat használó felhasználóknak például ismerniük kell a vállalat szabályzatait. Előfordulhat, hogy a vendég felhasználói nem tudnak az olyan szervezeten belüli adatkezelési követelményekkel kapcsolatban, amelyeknek meghívásra kerültek.

A szervezetek az olyan technológiákon keresztül automatizálják a hozzáférési életciklus folyamatát, mint a [dinamikus csoportok](../enterprise-users/groups-dynamic-membership.md), valamint a scim integrált [SaaS-alkalmazásokhoz](../saas-apps/tutorial-list.md) vagy [alkalmazásokhoz](../app-provisioning/use-scim-to-provision-users-and-groups.md)való felhasználói kiépítés.  A szervezetek azt is megadhatják [, hogy mely vendég felhasználók férhetnek hozzá a helyszíni alkalmazásokhoz](../external-identities/hybrid-cloud-to-on-premises.md).  Ezek a hozzáférési jogosultságok rendszeresen áttekinthetők az ismétlődő [Azure ad hozzáférési felülvizsgálatok](access-reviews-overview.md)használatával.   Az [Azure ad-jogosultságok kezelése](entitlement-management-overview.md) lehetővé teszi annak meghatározását is, hogy a felhasználók hogyan kérnek hozzáférést a csoport-és csoporttagság, az alkalmazási szerepkörök és a SharePoint Online szerepköreinek csomagjaihoz.

Amikor egy felhasználó megpróbál hozzáférni az alkalmazásokhoz, az Azure AD kikényszeríti a [feltételes hozzáférési](../conditional-access/index.yml) szabályzatokat. A feltételes hozzáférési szabályzatok tartalmazhatják például a használati [feltételek](../conditional-access/terms-of-use.md) megjelenítését, és biztosítják, hogy a felhasználó az alkalmazáshoz való hozzáférés megkezdése előtt [jóváhagyja ezeket a feltételeket](../conditional-access/require-tou.md) .

## <a name="privileged-access-lifecycle"></a>Emelt szintű hozzáférés életciklusa

A rendszerjogosultságú hozzáférést a többi szállító a személyazonosság szabályozásának külön funkciójaként írja le. A Microsoft azonban úgy gondolja, hogy a privilegizált hozzáférés szabályozása kulcsfontosságú része az identitás szabályozásának – különösen, ha az ilyen rendszergazdai jogokkal való visszaélés lehetséges, a szervezet számára is okozhat. A rendszergazdai jogosultságokat igénybe vevő alkalmazottakat, szállítókat és alvállalkozókat szabályozni kell.

![Emelt szintű hozzáférés életciklusa](./media/identity-governance-overview/privileged-access-lifecycle.png)

A [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) további vezérlőket biztosít a hozzáférési jogosultságok biztosításához az erőforrásokhoz, az Azure ad-ben, az Azure-ban és más Microsoft Online-szolgáltatásokban.  A többtényezős hitelesítés és a feltételes hozzáférés mellett az Azure AD PIM által biztosított igény szerinti hozzáférés-és szerepkör-változási riasztási képességek széles körű irányítási vezérlőket biztosítanak a vállalati erőforrások biztonságossá tételéhez (címtár, Microsoft 365 és Azure erőforrás-szerepkörök). A más hozzáférési formákhoz hasonlóan a szervezetek hozzáférési felülvizsgálatokat is használhatnak a rendszergazdai szerepkörökben lévő összes felhasználóra vonatkozó ismétlődő hozzáférés-visszaigazolások konfigurálásához.

## <a name="governance-capabilities-in-other-azure-ad-features"></a>Irányítási képességek más Azure AD-funkciókban

A fent felsorolt funkciók mellett további Azure AD-funkciók is gyakran használatosak az identitás-irányítási forgatókönyvek biztosításához:

| Képesség | Eset |Szolgáltatás
| ------- | --------------------- |-----|
|Identitás életciklusa (alkalmazottak)|A rendszergazdák engedélyezhetik a felhasználói fiókok üzembe helyezését a munkanap vagy a SuccessFactors Cloud HR vagy a helyszíni HR használatával.|[Felhőbeli HR – Azure AD-felhasználók üzembe helyezése](../app-provisioning/plan-cloud-hr-provision.md)|
|Identitás életciklusa (vendég)|A rendszergazdák engedélyezhetik az önkiszolgáló vendég felhasználói számára egy másik Azure AD-bérlőből, közvetlen összevonásból, egyszeri jelszóból (OTP) vagy Google-fiókokból való bevezetést.  A vendég felhasználókat a rendszer automatikusan kiépíti és kiépíti életciklus-szabályzatok alapján.|[Jogosultságok kezelése](entitlement-management-overview.md) [B2B](../external-identities/what-is-b2b.md) használatával|
|Jogosultságkezelés|Az erőforrás-tulajdonosok létrehozhatnak alkalmazásokat, csoportokat, Azure AD-és Microsoft 365-csoportokat és SharePoint Online-webhelyeket tartalmazó hozzáférési csomagokat.|[Jogosultságkezelés](entitlement-management-overview.md)|
|Hozzáférési kérelmek|A végfelhasználók csoporttagság vagy alkalmazás-hozzáférés igénylésére is jogosultak. A végfelhasználók, beleértve a más szervezetek vendégeit is, hozzáférési csomagokat igényelhetnek.|[Jogosultságkezelés](entitlement-management-overview.md)|
|Munkafolyamat|Az erőforrás-tulajdonosok a szerepkör-aktiválási kérelmekhez hozzáférési kérelmeket és jóváhagyókat is meghatározhatnak a jóváhagyók és a eszkalációs jóváhagyóik számára.  |[Jogosultságok kezelése](entitlement-management-overview.md) és [PIM](../privileged-identity-management/pim-configure.md)|
|Házirend és szerepkör-kezelés|A rendszergazda feltételes hozzáférési szabályzatokat adhat meg az alkalmazásokhoz való futásidejű hozzáféréshez.  Az erőforrás-tulajdonosok házirendeket adhatnak meg a felhasználói hozzáféréshez a hozzáférési csomagokon keresztül.|[Feltételes hozzáférés](../conditional-access/overview.md) és [jogosultság-kezelési](entitlement-management-overview.md) szabályzatok|
|Hozzáférés minősítése|A rendszergazdák a következőhöz engedélyezhetik az ismétlődő hozzáférés-újraminősítést: SaaS-alkalmazások vagy felhőalapú csoporttagság, Azure AD vagy Azure-erőforrás szerepkör-hozzárendelések. Automatikusan távolítsa el az erőforrás-hozzáférést, tiltsa le a vendég hozzáférését, és törölje a vendég fiókokat.|[Hozzáférési felülvizsgálatok](access-reviews-overview.md)a [PIM](../privileged-identity-management/pim-how-to-start-security-review.md) felületén is|
|Teljesítés és kiépítés|Automatikus kiépítés és megszüntetés az Azure AD-hez csatlakoztatott alkalmazásokban, beleértve a SCIM-t és a SharePoint Online-webhelyeket. |[felhasználó kiépítés](../app-provisioning/user-provisioning.md)|
|Jelentéskészítés és elemzés|A rendszergazdák lekérhetik a legutóbbi felhasználó-kiépítési és bejelentkezési tevékenység naplóit. Integráció a Azure Monitor és a "ki férhet hozzá" hozzáférési csomagokon keresztül.|[Azure ad-jelentések](../reports-monitoring/overview-reports.md) és- [figyelés](../reports-monitoring/overview-monitoring.md)|
|Emelt szintű hozzáférés|Igény szerinti és ütemezett hozzáférés, riasztás, jóváhagyási munkafolyamatok Azure AD-szerepkörökhöz (beleértve az egyéni szerepköröket) és az Azure-erőforrás-szerepköröket.|[Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|Naplózás|A rendszergazdák riasztást kaphatnak a rendszergazdai fiókok létrehozásáról.|[Azure AD PIM-riasztások](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>Első lépések

Tekintse meg a Azure Portal identitás- **irányításának** első lépések lapját a jogosultsági felügyelet, a hozzáférési felülvizsgálatok, a Privileged Identity Management és a használati feltételek használatának megkezdéséhez.

![A személyazonosság szabályozása – első lépések](./media/identity-governance-overview/getting-started.png)


Ha visszajelzést szeretne küldeni a személyazonosság-irányítási funkciókról, kattintson a **visszajelzés** küldése lehetőségre a Azure Portal a visszajelzés elküldéséhez. A csapat rendszeresen felülvizsgálja visszajelzéseit.

Habár nincs tökéletes megoldás vagy javaslat minden ügyfélhez, a következő konfigurációs útmutatók biztosítják a Microsoft által a biztonságosabb és produktív munkaerő biztosításához javasolt alapelveket.

- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Emelt szintű hozzáférés biztonsága](../roles/security-planning.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Függelék – az identitás-irányítási funkciókban való felügyelethez szükséges legkevésbé Kiemelt szerepkörök

Ajánlott eljárás a legkevésbé Kiemelt szerepkör használata a rendszergazdai feladatok elvégzéséhez az Identitáskezelés irányításában. Javasoljuk, hogy az Azure AD PIM használatával aktiválja a szerepköröket a feladatok elvégzéséhez szükséges módon. Az identitás-irányítási funkciók konfigurálásához az alábbi legkevesebb jogosultsággal rendelkező címtár-szerepkört kell megadni:

| Szolgáltatás | Legkevésbé Kiemelt szerepkör |
| ------- | --------------------- |
| Jogosultságkezelés | Felhasználói rendszergazda (a SharePoint Online-helyek katalógusba való felvételének kivételével, amely globális rendszergazdai jogosultságot igényel) |
| Hozzáférési felülvizsgálatok | Felhasználói rendszergazda (az Azure-beli vagy az Azure AD-szerepkörök hozzáférési felülvizsgálatának kivételével, amelyhez Kiemelt szerepkörű rendszergazda szükséges) |
|Privileged Identity Management | Kiemelt szerepkörű rendszergazda |
| Használati feltételek | Biztonsági rendszergazda vagy feltételes hozzáférésű rendszergazda |

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD-jogosultságkezelés?](entitlement-management-overview.md)
- [Mik azok az Azure AD-hozzáférési felülvizsgálatok?](access-reviews-overview.md)
- [Mi az az Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Mire használhatom a Használati feltételek funkciót?](../conditional-access/terms-of-use.md)