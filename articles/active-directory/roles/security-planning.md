---
title: Biztonságos hozzáférési eljárások a rendszergazdák számára az Azure AD-ben | Microsoft Docs
description: Győződjön meg arról, hogy a szervezet rendszergazdai hozzáférési és rendszergazdai fiókjai biztonságosak. Olyan rendszerfejlesztők és informatikai szakemberek számára, akik az Azure AD-t, az Azure-t és a Microsoft Online Servicest konfigurálják.
services: active-directory
keywords: ''
author: rolyon
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f2fe29ede2bf0f92049d1ae82bae87326057a63
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594294"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez

Az üzleti eszközök biztonsága az informatikai rendszereket felügyelő Kiemelt fiókok integritásának függvénye. A Cyber-támadók a hitelesítő adatok ellopására irányuló támadásokkal megcélozják a rendszergazdai fiókokat és más emelt szintű hozzáférést, hogy megpróbálják elérni a bizalmas adatokat.

A Cloud Services esetében a megelőzés és a reagálás a felhőalapú szolgáltató és az ügyfél együttes feladatai közé tartozik. A végpontokra és a felhőre vonatkozó legújabb fenyegetésekkel kapcsolatos további információkért tekintse meg a [Microsoft biztonsági intelligencia jelentését](https://www.microsoft.com/security/operations/security-intelligence-report). Ez a cikk segítséget nyújt egy olyan ütemterv kidolgozásához, amely a jelenlegi csomagok és az itt ismertetett útmutató közötti hézagok lezárását célozza.

> [!NOTE]
> A Microsoft elkötelezte magát a megbízhatóság, az átláthatóság, a szabványok és a jogszabályi megfelelőség legmagasabb szintjére. Tudjon meg többet arról, hogy a Microsoft globális incidensek csapatának milyen hatása van a Cloud Services elleni támadások következményeire, és hogy a Microsoft adatvédelmi központ – a Microsoft adatvédelmi [központjában –](https://www.microsoft.com/trustcenter/compliance) [biztonsági](https://www.microsoft.com/trustcenter/security) és Microsoft-megfelelőségi célokból hogyan építhető be a biztonság a Microsoft üzleti termékeibe és a felhőalapú szolgáltatásokba.

A szervezeti biztonság hagyományosan a hálózat belépési és kilépési pontjaira összpontosított biztonsági területet. Az SaaS-alkalmazások és az interneten található személyes eszközök azonban kevésbé hatékonyak. Az Azure AD-ben a hálózati biztonsági területet a szervezet identitási rétegében lévő hitelesítéssel cseréljük le, és a felhasználók a vezérlés jogosultsággal rendelkező rendszergazdai szerepkörökhöz vannak rendelve. A hozzáférést védeni kell, függetlenül attól, hogy a környezet helyszíni, Felhőbeli vagy hibrid.

Az emelt szintű hozzáférés biztonságossá tételéhez a következő módosításokat kell megadnia:

* Folyamatok, felügyeleti gyakorlatok és tudásmenedzsment
* Technikai összetevők, mint például a gazdagépek védelme, a fiókok védelme és az Identitáskezelés

Az emelt szintű hozzáférést a Microsoft szolgáltatásokban felügyelt és jelentett módon biztosíthatja. Ha helyszíni rendszergazdai fiókokkal rendelkezik, tekintse meg a helyszíni és a hibrid rendszerjogosultságú hozzáférésre vonatkozó útmutatást Active Directory a [privilegizált hozzáférés biztosításához](/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> A jelen cikkben található útmutatás elsősorban a P1 és P2 csomagokban található Azure Active Directory prémium szintű Azure Active Directory-funkciókra vonatkozik. A P2 prémium szintű Azure Active Directory az EMS E5 Suite és a Microsoft 365 E5 csomag része. Ez az útmutató feltételezi, hogy a szervezete már rendelkezik a felhasználók számára megvásárolt prémium szintű Azure AD P2-licencekkel. Ha nem rendelkezik ezekkel a licencekkel, előfordulhat, hogy néhány útmutató nem vonatkozik a szervezetére. Emellett a jelen cikkben a globális rendszergazda kifejezés a "vállalati rendszergazda" vagy a "bérlői rendszergazda" kifejezést is jelenti.

## <a name="develop-a-roadmap"></a>Ütemterv kidolgozása

A Microsoft azt javasolja, hogy fejlesszen és kövessen el egy ütemtervet, amely biztonságos hozzáférést biztosít a Cyber-támadók ellen. Az ütemtervet bármikor módosíthatja, hogy megfeleljen a meglévő képességeinek és a szervezeten belül meghatározott követelményeknek. Az ütemterv minden szakaszának meg kell növelnie a költségeket és a nehézségeket, hogy a támadók a helyi, a Felhőbeli és a hibrid eszközökre emelt szintű hozzáférést kapjanak. A Microsoft a következő négy ütemtervi szakaszt javasolja. Először ütemezze a leghatékonyabb és a leggyorsabb megvalósítást. Ez a cikk lehet az Ön útmutatója, amely a Microsoft által a Cyber-támadás incidensekkel és a válaszadások megvalósításával kapcsolatos tapasztalatain alapul. Az ütemterv Ütemtervei a közelítések.

![Az ütemterv szakaszai a Time Lines szolgáltatással](./media/security-planning/roadmap-timeline.png)

* 1. fázis (24-48 óra): a kritikus fontosságú elemek, amelyekről javasoljuk, hogy azonnal elvégezze

* 2. fázis (2-4 hét): a leggyakrabban használt támadási technikák enyhítése

* 3. fázis (1-3 hónap): a felügyeleti tevékenység kiépítése és teljes körű vezérlése

* 4. fázis (hat hónap és újabb): a védelmi platform további megerősítése érdekében folytassa a fejlesztést.

Ez az ütemterv-keretrendszer úgy lett kialakítva, hogy maximalizálja a már üzembe helyezett Microsoft-technológiák használatát. Érdemes lehet a más gyártóktól származó, már üzembe helyezett vagy üzembe helyezést fontolgató biztonsági eszközöket összekapcsolni.

## <a name="stage-1-critical-items-to-do-right-now"></a>1. lépés: a kritikus fontosságú elemek most

![1. lépés a kritikus elemek első lépése](./media/security-planning/stage-one.png)

Az ütemterv 1. fázisa a gyors és könnyen megvalósítható kritikus fontosságú feladatokra összpontosít. Azt javasoljuk, hogy ezeket a néhány elemet az első 24-48 órán belül el kell végeznie, hogy biztosítsa az alapszintű biztonságos jogosultságok elérését. A védett privilegizált hozzáférési ütemterv ezen szakasza a következő műveleteket tartalmazza:

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management bekapcsolása

Javasoljuk, hogy kapcsolja be a Azure AD Privileged Identity Management (PIM) szolgáltatást az Azure AD éles környezetben. A PIM bekapcsolását követően értesítő e-mail-üzeneteket fog kapni az emelt szintű hozzáférési szerepkör változásairól. Az értesítések korai figyelmeztetést nyújtanak, ha további felhasználókat adnak hozzá a magas jogosultsági szintű szerepkörökhöz.

Azure AD Privileged Identity Management a P2 vagy az EMS E5 prémium szintű Azure AD része. Ha segítségre van szüksége a helyszíni és a Felhőbeli alkalmazásokhoz és erőforrásokhoz való hozzáférés védelméhez, regisztráljon a [Enterprise Mobility + Security ingyenes 90 napos próbaverzióra](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). A biztonsági tevékenységek Azure AD Privileged Identity Management és Azure AD Identity Protection figyelése az Azure AD jelentéskészítési, naplózási és riasztásokkal.

A Azure AD Privileged Identity Management bekapcsolását követően:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely az Azure ad éles szervezet globális rendszergazdája.

2. Válassza ki azt az Azure AD-szervezetet, ahol a Privileged Identity Management szeretné használni, válassza ki a felhasználónevét a Azure Portal jobb felső sarkában.

3. A Azure Portal menüben válassza a **minden szolgáltatás** lehetőséget, és szűrje a **Azure ad Privileged Identity Management** listáját.

4. Nyissa meg Privileged Identity Management a **minden szolgáltatás** listából, és rögzítse az irányítópulton.

Győződjön meg arról, hogy az első személy, aki a PIM-t használja a szervezetben, hozzá van rendelve a **biztonsági rendszergazda** és a **Kiemelt szerepkörű rendszergazda** szerepkörhöz. Csak a Kiemelt szerepkörű rendszergazdák kezelhetik a felhasználók Azure AD-címtárbeli szerepkör-hozzárendeléseit. A PIM biztonsági varázsló végigvezeti a kezdeti felderítési és hozzárendelési élményen. A varázslóból a további módosítások végrehajtása nélkül is kiléphet.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Magas jogosultsági szintű szerepkörökben lévő fiókok azonosítása és kategorizálása

Azure AD Privileged Identity Management bekapcsolását követően tekintse meg a következő Azure AD-szerepkörökkel rendelkező felhasználókat:

* Globális rendszergazda
* Kiemelt szerepkörű rendszergazda
* Exchange-rendszergazda
* SharePoint-rendszergazda

Ha nem rendelkezik Azure AD Privileged Identity Management a szervezetében, használhatja a [POWERSHELL API](/powershell/module/azuread/get-azureaddirectoryrolemember)-t. Kezdje a globális rendszergazdai szerepkörrel, mert a globális rendszergazda ugyanazokkal az engedélyekkel rendelkezik az összes olyan felhőalapú szolgáltatáshoz, amelyhez a szervezet előfizetett. Ezek az engedélyek a hozzárendelésük helyétől függetlenül megadhatók: a Microsoft 365 felügyeleti központban, a Azure Portalban vagy a Microsoft PowerShellhez készült Azure AD-modulban.

Távolítsa el azokat a fiókokat, amelyekre már nincs szükség ezekben a szerepkörökben. Ezután kategorizálja a rendszergazdai szerepkörökhöz rendelt többi fiókot:

* Rendszergazda felhasználókhoz rendelve, de nem rendszergazdai célokra is használják (például személyes e-mail-cím)
* Rendszergazda felhasználókhoz rendelhető hozzá, és csak felügyeleti célokra használható
* Több felhasználó között megosztott
* Az eseti vészhelyzetek eléréséhez
* Automatikus parancsfájlok esetén
* Külső felhasználók számára

#### <a name="define-at-least-two-emergency-access-accounts"></a>Adjon meg legalább két vészhelyzeti hozzáférési fiókot

Lehetséges, hogy a felhasználó véletlenül kizárja a szerepkörét. Ha például egy összevont helyszíni identitás-szolgáltató nem érhető el, a felhasználók nem jelentkezhetnek be, és nem aktiválhatók a meglévő rendszergazdai fiókok. A két vagy több vészhelyzeti hozzáférési fiók tárolásával előkészítheti a véletlen hiány elérését.

A sürgősségi hozzáférési fiókok segítenek a rendszerjogosultságú hozzáférés korlátozásában egy Azure AD-szervezeten belül. Ezek a fiókok magas jogosultsággal rendelkeznek, és nincs hozzárendelve bizonyos személyekhez. A vészhelyzeti hozzáférési fiókok a "break Glass" forgatókönyvekre korlátozódnak, ahol a normál rendszergazdai fiókok nem használhatók. Győződjön meg arról, hogy a vészhelyzeti fiók használatának szabályozása és csökkentése csak erre az időre van szükség.

Értékelje ki azokat a fiókokat, amelyek a globális rendszergazdai szerepkörhöz vannak rendelve vagy jogosultak. Ha nem lát csak felhőalapú fiókokat a \* . onmicrosoft.com tartománnyal (a "break Glass" vészhelyzeti hozzáféréshez), hozza létre őket. További információ: [a sürgősségi hozzáférés felügyeleti fiókjainak kezelése az Azure ad-ben](security-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>A többtényezős hitelesítés bekapcsolása és az összes többi magas jogosultsági szintű, egyetlen felhasználótól eltérő, nem összevont rendszergazdai fiók regisztrálása

Az Azure AD Multi-Factor Authentication (MFA) megkövetelése a bejelentkezéskor minden olyan felhasználó számára, aki véglegesen hozzá van rendelve egy vagy több Azure AD-rendszergazdai szerepkörhöz: globális rendszergazda, Kiemelt szerepkörű rendszergazda, Exchange-rendszergazda és SharePoint-rendszergazda. Az útmutató segítségével engedélyezheti a [többtényezős hitelesítés (MFA) használatát a rendszergazdai fiókokhoz](../authentication/howto-mfa-userstates.md) , és gondoskodhat arról, hogy minden felhasználó regisztrálva legyen a következő helyen: [https://aka.ms/mfasetup](https://aka.ms/mfasetup) . További információt a 2. lépés és a útmutató 3. lépése tartalmaz a [Microsoft 365ban található adatokhoz és szolgáltatásokhoz való hozzáférés védelme](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)című témakörben. 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>2. fázis: a gyakran használt támadások enyhítése

![2. fázis – a gyakran használt támadások enyhítése](./media/security-planning/stage-two.png)

Az ütemterv 2. szakasza arra összpontosít, hogy enyhítse a hitelesítő adatok ellopásának és visszaélésének leggyakrabban használt támadási módszereit, és körülbelül 2-4 hétig valósítható meg. A védett privilegizált hozzáférési ütemterv ezen szakasza a következő műveleteket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Szolgáltatások, tulajdonosok és rendszergazdák leltárának elvégzése

A "saját eszközök használata" és a otthoni házirendek működésének növekedése, valamint a vezeték nélküli kapcsolatok növekedése kritikus fontosságú a hálózathoz csatlakozó hálózat figyeléséhez. A biztonsági naplózás felfedi a hálózaton lévő olyan eszközöket, alkalmazásokat és programokat, amelyeket a szervezet nem támogat, és amelyek nagy kockázatot jelentenek. További információ: az [Azure biztonsági felügyelete és monitorozása – áttekintés](../../security/fundamentals/management-monitoring-overview.md). Győződjön meg arról, hogy a leltári folyamat az alábbi feladatokat tartalmazza.

* Azonosítsa azokat a felhasználókat, akik rendszergazdai szerepkörökkel rendelkeznek, és azokat a szolgáltatásokat, amelyeken kezelhető.
* Az Azure AD PIM segítségével megtudhatja, hogy a szervezete mely felhasználói rendelkeznek rendszergazdai hozzáféréssel az Azure AD-hez.
* Az Azure AD-ben definiált szerepkörökön túl Microsoft 365 olyan rendszergazdai szerepköröket tartalmaz, amelyeket a szervezet felhasználói számára hozzárendelhet. Az egyes rendszergazdai szerepkörök közös üzleti funkciókra mutatnak, és lehetővé teszi, hogy a szervezeten belül a [Microsoft 365 felügyeleti központban](https://admin.microsoft.com)meghatározott feladatokat végezzenek. A Microsoft 365 felügyeleti központ segítségével megtudhatja, hogy a szervezete mely felhasználói rendelkeznek rendszergazdai hozzáféréssel a Microsoft 365hoz, beleértve az Azure AD-ben nem felügyelt szerepkörökön keresztül. További információ: Microsoft 365 tudnivalók az Office 365 [rendszergazdai szerepköreiről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) és [biztonsági eljárásairól](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* A szervezet által használt szolgáltatások leltározása, például az Azure, az Intune vagy a Dynamics 365.
* Győződjön meg arról, hogy a felügyeleti célokra használt fiókok:

  * Munkavégzéshez csatolt e-mail-címek
  * Regisztrálva van az Azure AD Multi-Factor Authentication vagy a helyszíni MFA használata
* Kérje meg a felhasználókat, hogy üzleti szempontból indokolják meg a rendszergazdai hozzáférést.
* Távolítsa el a rendszergazdai hozzáférést azon személyek és szolgáltatások számára, akiknek nincs rá szükségük.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Azonosítsa a Microsoft-fiókokat olyan rendszergazdai szerepkörökben, amelyeket munkahelyi vagy iskolai fiókokra kell váltani

Ha a kezdeti globális rendszergazdák felhasználják a meglévő Microsoft-fiók hitelesítő adataikat, amikor elkezdték használni az Azure AD-t, cserélje le a Microsoft-fiókokat egyedi felhőalapú vagy szinkronizált fiókokra.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Különálló felhasználói fiókok és a levelezés továbbítása globális rendszergazdai fiókokhoz

A személyes e-mail-fiókokat rendszeresen a Cyber-támadók végzik, a személyes e-mail-címeket nem fogadja el a globális rendszergazdai fiókok számára. Az internetes kockázatok rendszergazdai jogosultságokkal való elkülönítéséhez hozzon létre dedikált fiókokat minden rendszergazda jogosultságokkal rendelkező felhasználó számára.

* Ügyeljen arra, hogy a felhasználók számára a globális rendszergazdai feladatok elvégzéséhez külön fiókokat hozzon létre.
* Győződjön meg arról, hogy a globális rendszergazdák nem véletlenül nyitnak meg e-maileket, vagy futtassanak programokat a rendszergazdai fiókjaikkal.
* Győződjön meg arról, hogy ezek a fiókok rendelkeznek e-mail-címmel egy működő postaládába.
* A globális rendszergazda (és más kiemelt csoportok) fiókjai csak felhőalapú fiókok lehetnek, amelyek nem kapcsolódnak a helyszíni Active Directoryhoz.

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Győződjön meg arról, hogy a rendszergazdai fiókok jelszavai nemrég módosultak

Győződjön meg arról, hogy minden felhasználó bejelentkezett a rendszergazdai fiókjaiba, és az elmúlt 90 napban legalább egyszer módosította a jelszavukat. Győződjön meg arról is, hogy a megosztott fiókok jelszavának módosítása a közelmúltban megtörtént.

#### <a name="turn-on-password-hash-synchronization"></a>Jelszó-kivonat szinkronizálásának bekapcsolása

Azure AD Connect szinkronizálja egy felhasználó jelszavának kivonatát a helyszíni Active Directoryból egy felhőalapú Azure AD-szervezetbe. Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS) összevonást használ, a jelszó-kivonatoló szinkronizálást biztonsági másolatként is használhatja. Ez a biztonsági mentés akkor lehet hasznos, ha a helyszíni Active Directory vagy AD FS kiszolgáló átmenetileg nem érhető el.

A jelszó-kivonatolási szinkronizálás lehetővé teszi a felhasználók számára, hogy ugyanazzal a jelszóval jelentkezzenek be a szolgáltatásba, amelyet a helyszíni Active Directory-példányba való bejelentkezéshez használnak. A jelszó-kivonatolási szinkronizálás lehetővé teszi az Identity Protection számára a feltört hitelesítő adatok észlelését a jelszó-kivonatok és a biztonsági rések által ismert jelszavak összehasonlításával. További információ: jelszó- [kivonatolási szinkronizálás implementálása Azure ad Connect szinkronizálással](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Többtényezős hitelesítés megkövetelése a Kiemelt szerepkörökben és a felhasználók számára elérhetővé tett felhasználók számára

Az Azure AD azt javasolja, hogy a többtényezős hitelesítés (MFA) megkövetelése minden felhasználó számára. Ügyeljen arra, hogy azok a felhasználók, akik jelentős hatással lennének a fiókjuk biztonságára (például pénzügyi tisztviselők). Az MFA feltört jelszó miatt csökkenti a támadás kockázatát.

Bekapcsolás:

* [MFA feltételes hozzáférési szabályzatok használatával](../authentication/howto-mfa-getstarted.md) a szervezet összes felhasználója számára.

Ha a vállalati Windows Hello-t használja, akkor az MFA-követelmény a Windows Hello bejelentkezési felületének használatával teljesíthető. További információ: [Windows Hello](/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Identity Protection konfigurálása

A Azure AD Identity Protection egy algoritmus-alapú figyelési és jelentéskészítési eszköz, amely a szervezet identitásait érintő lehetséges biztonsági réseket észleli. A gyanús tevékenységekre vonatkozó automatizált válaszokat is konfigurálhat, és megteheti a szükséges lépéseket a megoldásához. További információkért lásd: [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>A Microsoft 365 biztonságos pontszámának beszerzése (ha Microsoft 365 használ)

A biztonságos pontszám a használt Microsoft 365-szolgáltatások beállításait és tevékenységeit vizsgálja, és összehasonlítja őket a Microsoft által létrehozott alaptervvel. Az Ön által összehangolt biztonsági eljárások alapján kapsz egy pontszámot. Bárki, aki rendszergazdai jogosultságokkal rendelkezik egy Microsoft 365 Vállalati verzió standard vagy nagyvállalati előfizetéshez, elérheti a biztonságos pontszámot a következő címen: [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Tekintse át a Microsoft 365 biztonsági és megfelelőségi útmutatóját (ha Microsoft 365 használ)

A [biztonsági és megfelelőségi terv](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) felvázolja az Office 365-ügyfél az Office 365 konfigurálására és más EMS-funkciók engedélyezésére vonatkozó megközelítését. Ezt követően tekintse át a 3-6-es lépéseket a [Microsoft 365 adataihoz és szolgáltatásaihoz való hozzáférés védelméről](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) , valamint az útmutató a [Biztonság és a megfelelőség figyeléséhez Microsoft 365ban](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Microsoft 365 tevékenység figyelésének konfigurálása (ha Microsoft 365 használ)

Figyelje meg a szervezetét olyan felhasználók számára, akik Microsoft 365 használnak a rendszergazdai fiókkal rendelkező munkatársak azonosításához, de előfordulhat, hogy nincs szükségük Microsoft 365 hozzáférésre, mert nem jelentkeznek be a portálokra. További információ: [tevékenységi jelentések a Microsoft 365 felügyeleti központban](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Incidens/vészhelyzeti válasz terv tulajdonosainak létrehozása

A sikeres incidens-reagálási képesség létrehozásához jelentős tervezés és erőforrások szükségesek. Folyamatosan figyelnie kell a Cyber-támadásokat, és prioritásokat kell létrehoznia az incidensek kezeléséhez. Az incidensek adatainak összegyűjtése, elemzése és jelentése a kapcsolatok létrehozásához és más belső csoportokkal való kommunikáció létrehozásához, valamint a tulajdonosok megtervezéséhez. További információ: [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Biztonságos helyszíni privilegizált rendszergazdai fiókok, ha még nem tette meg

Ha a Azure Active Directory-szervezet szinkronizálva van a helyszíni Active Directoryval, kövesse a következő témakör útmutatását: a [biztonsági privilegizált hozzáférési menetrend](/windows-server/identity/securing-privileged-access/securing-privileged-access): ebben a szakaszban a következők szerepelnek:

* Különálló rendszergazdai fiókok létrehozása azon felhasználók számára, akiknek helyszíni felügyeleti feladatokat kell elvégezniük
* Rendszerjogosultságú hozzáférési munkaállomások üzembe helyezése Active Directory rendszergazdák számára
* Egyedi helyi rendszergazdai jelszavak létrehozása munkaállomásokhoz és kiszolgálókhoz

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára

#### <a name="complete-an-inventory-of-subscriptions"></a>Előfizetések leltárának befejezése

Az Enterprise Portal és a Azure Portal segítségével azonosíthatja a szervezetében az üzemi alkalmazásokat futtató előfizetéseket.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Microsoft-fiókok eltávolítása rendszergazdai szerepkörökből

A más programokból (például Xbox, Live és Outlook) származó Microsoft-fiókok nem használhatók rendszergazdai fiókként a szervezet előfizetéséhez. Távolítsa el a rendszergazdai állapotot az összes Microsoft-fiókból, és cserélje le az Azure AD-t (például: chris@contoso.com ) a munkahelyi vagy iskolai fiókokra. Rendszergazdai célokra az Azure AD-ben hitelesített és nem más szolgáltatásokban lévő fiókoktól függ.

#### <a name="monitor-azure-activity"></a>Azure-tevékenység figyelése

Az Azure-tevékenységnapló az Azure előfizetés-szintű eseményeit tartalmazza. Információt nyújt arról, hogy ki hozta létre, frissítette és törölte azokat az erőforrásokat, és hogy mikor történtek ezek az események. További információt az [Azure-előfizetésében található fontos műveletekkel kapcsolatos értesítések naplózása és fogadása](../../azure-monitor/alerts/alerts-activity-log.md)című témakörben talál.

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépések a más felhőalapú alkalmazásokhoz való hozzáférést kezelő szervezetek számára az Azure AD-n keresztül

#### <a name="configure-conditional-access-policies"></a>Feltételes hozzáférési szabályzatok konfigurálása

Feltételes hozzáférési szabályzatok előkészítése a helyszíni és a felhőben üzemeltetett alkalmazásokhoz. Ha a felhasználók munkahelyhez csatlakoztatott eszközöket használnak, további információkat kaphat a helyszíni [feltételes hozzáférés beállításáról Azure Active Directory eszköz regisztrációjának használatával](../../active-directory-b2c/overview.md).

## <a name="stage-3-take-control-of-admin-activity"></a>3. fázis: a rendszergazdai tevékenység felügyeletének átvétele

![3. fázis: a rendszergazdai tevékenység felügyeletének átvétele](./media/security-planning/stage-three.png)

A 3. fázis a 2. fázis enyhítésére épül, és körülbelül 1-3 hónapon belül implementálható. A védett privilegizált hozzáférési ütemterv ezen szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Rendszergazdai szerepkörrel rendelkező felhasználók hozzáférési felülvizsgálatának befejezése

A vállalati felhasználók egyre magasabb szintű hozzáférést kapnak a Cloud Serviceshez, ami nem felügyelt hozzáférést eredményezhet. Napjainkban a felhasználók globális rendszergazdák lehetnek Microsoft 365, az Azure-előfizetés rendszergazdái számára, vagy rendszergazdai hozzáférésük van a virtuális gépekhez vagy SaaS-alkalmazásokon keresztül.

A szervezetnek minden alkalmazottnak rendelkeznie kell a szokásos üzleti tranzakciókkal a nem megfelelő jogosultságú felhasználóként, és csak szükség esetén adhat rendszergazdai jogosultságokat. A hozzáférési felülvizsgálatok végrehajtásával azonosíthatja és megerősítheti azokat a felhasználókat, akik jogosultak a rendszergazdai jogosultságok aktiválásához.

A következő megoldást javasoljuk:

1. Határozza meg, hogy mely felhasználók az Azure AD-rendszergazdák, hogyan engedélyezheti az igény szerinti rendszergazdai hozzáférést, valamint a szerepkör alapú biztonsági vezérlőket.
2. Olyan felhasználók konvertálása, akik nem rendelkeznek egyértelmű indoklással a rendszergazdai jogosultsági szintű hozzáféréshez egy másik szerepkörhöz (ha nincs erre jogosult szerepkör, távolítsa el őket).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Az erősebb hitelesítés bevezetésének folytatása minden felhasználó számára

Magas rendelkezésre állású felhasználóknak kell megkövetelni a modern, erős hitelesítést, például az Azure AD MFA vagy a Windows Hello használatát. A Kiemelt felhasználók többek között a következők:

* C-Suite vezetők
* Magas szintű vezetők
* Kritikus informatikai és biztonsági személyzet

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Dedikált munkaállomások használata az Azure AD felügyeletéhez

A támadók megpróbálhatják a Kiemelt fiókok megcélzását, hogy megzavarják az adatok integritását és hitelességét. Gyakran olyan kártékony kódot használnak, amely megváltoztatja a program logikáját, vagy a rendszergazda hitelesítő adatait adja meg. Az emelt hozzáférési szintű munkaállomások (Privileged Access Workstation, PAW) az internetről érkező támadások és fenyegetések ellen védett dedikált operációs rendszert biztosítanak a bizalmas feladatokhoz. Ezek a bizalmas feladatok és fiókok a napi használatú munkaállomásokon és eszközökön való elkülönítése erős védelmet biztosít a következőtől:

* Adathalászat elleni támadások
* Az alkalmazás és az operációs rendszer biztonsági rései
* Megszemélyesítési támadások
* A hitelesítő adatok ellopása elleni támadások, például a billentyűleütések naplózása, a pass-The-hash és a pass-The-Ticket

Az emelt szintű hozzáférésű munkaállomások üzembe helyezésével csökkentheti annak kockázatát, hogy a rendszergazdák a nem megerősített asztali környezetben adják meg a hitelesítő adataikat. További információ: [privilegizált hozzáférésű munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>A National Institute of Standards and Technology ajánlásainak áttekintése az incidensek kezeléséhez

A National Institute of Standards and Technology (NIST) útmutatást nyújt az incidensek kezeléséhez, különösen az incidensekkel kapcsolatos adatok elemzéséhez és az egyes incidensekre vonatkozó megfelelő válaszok meghatározásához. További információ: [(NIST) számítógép biztonsági incidensek kezelési útmutatója (SP 800-61, 2. változat)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Privileged Identity Management (PIM) megvalósítása a JIT számára a további rendszergazdai szerepkörökhöz

Azure Active Directory esetén használja a [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) képességet. A Kiemelt szerepkörök időkorlátos aktiválása a következőket teszi lehetővé:

* Rendszergazdai jogosultságok aktiválása adott feladat végrehajtásához
* Az MFA betartatása az aktiválási folyamat során
* Értesítéseket használhat a rendszergazdáknak a sávon kívüli változásokról
* Lehetővé teheti a felhasználók számára, hogy az előre konfigurált időtartamig megtartsa a privilegizált hozzáférését
* A biztonsági rendszergazdák számára a következőket teheti:

  * Az összes privilegizált identitás felderítése
  * Naplózási jelentések megtekintése
  * Hozzáférési felülvizsgálatok létrehozása a rendszergazdai jogosultságok aktiválásához jogosult összes felhasználó azonosításához

Ha már használja a Azure AD Privileged Identity Management-t, módosítsa a szükséges időkereteket (például karbantartási időszakok).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Jelszó alapú bejelentkezési protokollok (az Exchange Online használata esetén) való kitettség meghatározása

Javasoljuk, hogy azonosítsa az összes lehetséges felhasználót, aki katasztrofális lehet a szervezet számára, ha a hitelesítő adatai biztonságban vannak. Ezekhez a felhasználókhoz erős hitelesítési követelmények helyezhetők el, és az Azure AD feltételes hozzáférés használatával megtarthatja, hogy a felhasználónévvel és jelszóval jelentkezzen be az e-mailbe. A [feltételes hozzáférés használatával blokkolhatja az örökölt hitelesítést](../conditional-access/block-legacy-authentication.md), és [letilthatja az alapszintű hitelesítést](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) az Exchange Online-on keresztül.

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Szerepkörök felülvizsgálati értékelésének elvégzése Microsoft 365 szerepkörökhöz (ha Microsoft 365 használ)

Értékelje ki, hogy az összes rendszergazda felhasználó a megfelelő szerepkörökben van-e (törölje és rendelje hozzá újra az értékelés alapján).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Tekintse át a Microsoft 365 használt biztonsági incidensek kezelési megközelítését, és hasonlítsa össze a saját szervezetével

Ezt a jelentést a [Microsoft 365 biztonsági incidensek felügyeletéről](https://www.microsoft.com/download/details.aspx?id=54302)töltheti le.

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Folytassa a helyszíni privilegizált rendszergazdai fiókok biztonságossá tételét

Ha a Azure Active Directory a helyszíni Active Directoryhoz csatlakozik, kövesse a [biztonsági privilegizált hozzáférési ütemterv](/windows-server/identity/securing-privileged-access/securing-privileged-access)útmutatását: 2. fázis. Ebben a szakaszban a következőket végezheti el:

* Emelt szintű hozzáférési munkaállomások üzembe helyezése az összes rendszergazda számára
* MFA megkövetelése
* A tartományvezérlő-karbantartáshoz elég rendszergazda, a tartományok támadási felületének csökkentése
* Komplex veszélyforrások felmérésének bevezetése a támadás észleléséhez

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára

#### <a name="establish-integrated-monitoring"></a>Integrált figyelés létrehozása

A [Azure Security Center](../../security-center/security-center-introduction.md):

* Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között
* Segít észlelni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek
* A biztonsági megoldások széles skálájával működik

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Kiemelt jogosultságú fiókok leltárba vétele az üzemeltetett Virtual Machines

Általában nincs szükség arra, hogy az összes Azure-előfizetéshez vagy erőforráshoz korlátlan hozzáférést adjon a felhasználóknak. Az Azure AD rendszergazdai szerepköreivel csak azt a hozzáférést adja meg, amelyhez a felhasználóknak szükségük van. Az Azure AD rendszergazdai szerepköreivel engedélyezheti, hogy egy rendszergazda csak az előfizetésben lévő virtuális gépeket kezelhesse, míg a másik az SQL-adatbázisokat ugyanazon az előfizetésen belül tudja kezelni. További információ: [Mi az az Azure szerepköralapú hozzáférés-vezérlés](../../active-directory-b2c/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>A PIM megvalósítása az Azure AD rendszergazdai szerepköreihez

Az Azure AD rendszergazdai szerepköreivel felügyelheti, felügyelheti és figyelheti az Azure-erőforrásokhoz való hozzáférést. A PIM védi a jogosultságok expozíciós idejének csökkentését, és a jelentéseken és riasztásokon keresztül növeli a használat láthatóságát. További információ: [What is Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Az Azure log-integrációk használata a kapcsolódó Azure-naplók a SIEM-rendszerbe való küldéséhez

Az Azure log-integráció lehetővé teszi, hogy az Azure-erőforrásokból származó nyers naplókat integrálja a szervezet meglévő biztonsági információi és eseménykezelő (SIEM) rendszereibe. Az [Azure log-integráció](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) Windows-eseményeket gyűjt a Windows Eseménynapló-naplókból és az Azure-erőforrásokból a következő helyekről:

* Azure-tevékenység naplói
* Azure Security Center-riasztások
* Azure-erőforrásnaplók

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépések a más felhőalapú alkalmazásokhoz való hozzáférést kezelő szervezetek számára az Azure AD-n keresztül

#### <a name="implement-user-provisioning-for-connected-apps"></a>Felhasználók üzembe helyezésének megvalósítása a csatlakoztatott alkalmazásokhoz

Az Azure AD segítségével automatizálhatja a felhasználói identitások létrehozását és karbantartását a felhőalapú alkalmazásokban (például a Dropbox, a Salesforce és a ServiceNow). További információ: a [felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások kiépítése az Azure ad-vel](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Adatvédelem integrálása

Microsoft Cloud App Security lehetővé teszi a fájlok vizsgálatát és a szabályzatok Azure Information Protection besorolási címkék alapján történő beállítását, amely lehetővé teszi a Felhőbeli adatmennyiség jobb láthatóságát és felügyeletét. Megvizsgálhatja és osztályozhatja a fájlokat a felhőben, és alkalmazhatja az Azure Information Protection feliratait. További információ: [Azure Information Protection integráció](/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

Konfigurálhatja a feltételes hozzáférést a csoport, a hely és az alkalmazás érzékenysége alapján az [SaaS-alkalmazások](https://azure.microsoft.com/overview/what-is-saas/) és az Azure ad-hez csatlakoztatott alkalmazások számára. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Tevékenységek figyelése a csatlakoztatott felhőalapú alkalmazásokban

Javasoljuk, hogy a [Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) használatával gondoskodjon arról, hogy a felhasználók hozzáférése is védve legyen a csatlakoztatott alkalmazásokban. Ez a szolgáltatás biztosítja a vállalati hozzáférést a felhőalapú alkalmazásokhoz, és védi a rendszergazdai fiókokat, így lehetővé teszi a következőket:

* A Felhőbeli alkalmazások láthatóságának és vezérlésének kiterjesztése
* Szabályzatok létrehozása a hozzáféréshez, a tevékenységekhez és az adatmegosztáshoz
* A kockázatos tevékenységek, a rendellenes viselkedések és a fenyegetések automatikus azonosítása
* Az adatszivárgás megakadályozása
* Csökkentse a kockázatkezelést és az automatizált fenyegetések megelőzését és a házirendek betartatását

A Cloud App Security SIEM-ügynök a SIEM-kiszolgálóval integrálja az Cloud App Securityt, hogy lehetővé tegye Microsoft 365 riasztások és tevékenységek központosított figyelését. Ez a kiszolgálón fut, és lekéri a riasztásokat és tevékenységeket a Cloud App Securityről, és továbbítja azokat az SIEM-kiszolgálóra. További információ: Siem- [integráció](/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>4. fázis: a védelem folytatása

![4. fázis: aktív biztonsági testhelyzet elfogadása](./media/security-planning/stage-four.png)

Az ütemterv 4. fázisát hat hónapon belül és azt meghaladóan kell megvalósítani. Fejezze be az ütemtervet, hogy megerősítse a jelenleg ismert, lehetséges támadásokkal kapcsolatos privilegizált hozzáférési védelmet. A holnapi biztonsági fenyegetések esetében javasoljuk, hogy a biztonság megtekinthető legyen folyamatban lévő folyamatként, hogy növelje a költségeket, és csökkentse a környezetét célzó ellenfelek sikerességi arányát.

Az emelt szintű hozzáférés biztonságossá tétele fontos, hogy biztonsági garanciákat hozzon létre üzleti eszközei számára. Azonban egy teljes biztonsági program részeként kell szerepelnie, amely folyamatos biztonsági garanciákat biztosít. A programnak olyan elemeket kell tartalmaznia, mint például a következők:

* Szabályzat
* Üzemeltetés
* Információbiztonság
* Kiszolgálók
* Alkalmazások
* Számítógépek
* Eszközök
* Felhőbeli háló

A Kiemelt jogosultságú hozzáférési fiókok kezelésekor a következő gyakorlatokat javasoljuk:

* Győződjön meg arról, hogy a rendszergazdák a nap és a nap közötti tevékenységet használják a nem Kiemelt felhasználók számára
* Csak szükség esetén adja meg a Kiemelt jogosultságú hozzáférést, és távolítsa el később (igény szerint)
* A rendszerjogosultságú fiókokhoz kapcsolódó naplózási tevékenység naplóinak megőrzése

A teljes biztonsági ütemterv létrehozásával kapcsolatos további információkért lásd: [Microsoft Cloud it Architecture Resources](https://almbok.com/office365/microsoft_cloud_it_architecture_resources). Ha a Microsoft-szolgáltatásokkal szeretne részt venni az ütemterv bármely részének megvalósításában, forduljon a Microsoft képviselőjéhez, vagy tekintse meg a [vállalata védelme érdekében a kritikus számítógépes védelem kiépítésével](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)foglalkozó témakört.

A védett privilegizált hozzáférési ütemterv ezen végső szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="review-admin-roles-in-azure-ad"></a>Rendszergazdai szerepkörök áttekintése az Azure AD-ben

Állapítsa meg, hogy a jelenlegi beépített Azure AD-rendszergazdai szerepkörök naprakészek-e, és hogy a felhasználók csak a szükséges szerepkörökkel rendelkeznek-e. Az Azure AD-vel külön rendszergazdákat rendelhet hozzá a különböző funkciók kiszolgálásához. További információ: [rendszergazdai szerepkörök kiosztása a Azure Active Directoryban](permissions-reference.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Az Azure AD-hez csatlakoztatott eszközök felügyeletével rendelkező felhasználók áttekintése

További információ: [hibrid Azure Active Directory csatlakoztatott eszközök konfigurálása](../devices/hybrid-azuread-join-plan.md).

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>A [beépített Microsoft 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) tagjainak áttekintése
Hagyja ki ezt a lépést, ha nem használja a Microsoft 365.
‎
#### <a name="validate-incident-response-plan"></a>Incidens-válasz tervének ellenőrzése

A terv tökéletesítése érdekében a Microsoft azt javasolja, hogy rendszeresen ellenőrizze, hogy a csomag a várt módon működik-e:

* A meglévő útiterv segítségével megtekintheti, hogy mi volt a kihagyva
* A futási elemzéstől függően módosítsa a meglévőket, vagy adjon meg új eljárásokat.
* Győződjön meg arról, hogy a frissített incidensekre adott válasz terve és gyakorlata a szervezeten belül van elosztva


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára 

Állapítsa meg, hogy [egy Azure-előfizetés tulajdonjogát át kell-e vinni egy másik fiókba](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break Glass": mi a teendő vészhelyzet esetén

![Fiókok a sürgősségi szüneti üvegek eléréséhez](./media/security-planning/emergency.jpeg)

1. A legfontosabb vezetők és biztonsági tisztviselők értesítése az incidenssel kapcsolatos információkkal.

2. Tekintse át a támadás forgatókönyvét.

3. Az Azure AD-be való bejelentkezéshez nyissa meg az "break Glass" fiókhoz tartozó felhasználónevet és a jelszó kombinációját.

4. Kérjen segítséget a Microsofttól [egy Azure-támogatási kérelem megnyitásával](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Tekintse meg az [Azure ad bejelentkezési jelentéseit](../reports-monitoring/overview-reports.md). Előfordulhat, hogy egy adott esemény és a jelentésbe belefoglalt események között némi idő van.

6. Hibrid környezetek esetén, ha a helyszíni infrastruktúra összevont és a AD FS-kiszolgáló nem érhető el, átmenetileg átválthat az összevont hitelesítésből a jelszó-kivonatoló szinkronizálás használatára. Ezzel a kapcsolóval visszaállíthatja a tartomány-összevonást a felügyelt hitelesítésre, amíg az AD FS-kiszolgáló elérhetővé nem válik.

7. A Kiemelt fiókokra vonatkozó e-mailek figyelése.

8. Győződjön meg arról, hogy biztonsági másolatot készít a kapcsolódó naplókról a lehetséges törvényszéki és jogi vizsgálatokhoz.

További információ arról, hogyan kezeli a Microsoft Office 365 a biztonsági incidenseket: [a biztonsági incidensek kezelése Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>Gyakori kérdések: válaszok a privilegizált hozzáférés biztonságossá tételéhez  

**K:** Mi a teendő, ha még nem hajtottam végre biztonságos hozzáférési összetevőket?

**Válasz:** Adjon meg legalább két, a többtényezős fiókokat, rendeljen MFA-t a Kiemelt rendszergazdai fiókokhoz, és válassza el a felhasználói fiókokat a globális rendszergazdai fiókokból.

**K:** Mi az a leggyakoribb probléma, amelyet először kell megoldani?

**Válasz:** Győződjön meg arról, hogy a legerősebb hitelesítést igényli a kiemelten elérhető személyek számára.

**K:** Mi történik, ha a Kiemelt rendszergazdák inaktiválva vannak?

**Válasz:** Hozzon létre egy globális rendszergazdai fiókot, amely mindig naprakész marad.

**K:** Mi történik, ha csak egy globális rendszergazda van hátra, és nem érhető el?

**Válasz:** Az egyik, az Ön által használt fiókkal azonnali jogosultsági szintű hozzáférést nyerhet.

**K:** Hogyan biztosíthatom a rendszergazdákat a szervezeten belül?

**Válasz:** A rendszergazdáknak mindig a szokásos "nem privilegizált" felhasználóként kell elvégezniük a mindennapi tevékenységet.

**K:** Mik az ajánlott eljárások az Azure AD-ben rendszergazdai fiókok létrehozásához?

**Válasz:** Emelt szintű hozzáférés az adott rendszergazdai feladatokhoz.

**K:** Milyen eszközök léteznek az állandó rendszergazdai hozzáférés csökkentése érdekében?

**Válasz:** Privileged Identity Management (PIM) és az Azure AD rendszergazdai szerepkörei.

**K:** Mi a Microsoft pozíciója a rendszergazdai fiókok Azure AD-vel való szinkronizálásához?

**Válasz:** A 0. rétegbeli rendszergazdai fiókok csak helyszíni AD-fiókokhoz használatosak. Ezek a fiókok általában nem szinkronizálhatók az Azure AD-vel a felhőben. A 0. rétegbeli rendszergazdai fiókok közé tartoznak azok a fiókok, csoportok és egyéb eszközök, amelyek közvetlen vagy közvetett felügyeleti felügyelettel rendelkeznek a helyszíni Active Directory erdőben, tartományokban, tartományvezérlőkön és eszközökön.

**K:** Hogyan lehet megőrizni a rendszergazdákat, hogy véletlenszerű rendszergazdai hozzáférést rendeljenek a portálon?

**Válasz:** A nem Kiemelt fiókok használata az összes felhasználó és a legtöbb rendszergazda számára. Kezdje azzal, hogy kifejleszti a szervezeti lábnyomot, és meghatározza, hogy mely néhány rendszergazdai fióknak kell jogosultságot biztosítania. És figyelés az újonnan létrehozott rendszergazda felhasználók számára.

## <a name="next-steps"></a>Következő lépések

* [Microsoft adatvédelmi központ a termékbiztonság számára](https://www.microsoft.com/trustcenter/security) – a Microsoft Cloud Products és Services biztonsági funkciói

* [Microsoft adatvédelmi központ – megfelelőség](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – a Microsoft átfogó megfelelőségi kínálata a Cloud Serviceshez

* [Útmutató a kockázatértékeléshez](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – a Microsoft Cloud Services biztonsági és megfelelőségi követelményeinek kezelése

### <a name="other-microsoft-online-services"></a>Egyéb Microsoft Online Services

* [Microsoft Intune biztonság](https://www.microsoft.com/trustcenter/security/intune-security) – az Intune mobileszköz-felügyeletet, mobileszköz-kezelést és számítógép-felügyeleti funkciókat biztosít a felhőből.

* [Microsoft dynamics 365 biztonság](https://www.microsoft.com/trustcenter/security/dynamics365-security) – a Dynamics 365 a Microsoft felhőalapú megoldás, amely egységesíti az Ügyfélkapcsolat-kezelési (CRM) és a vállalati erőforrás-tervezési (ERP) képességeket.
