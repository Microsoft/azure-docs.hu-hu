---
title: Microsoft 365 védelme a helyszíni támadásoktól
description: Útmutató arról, hogyan biztosítható, hogy egy helyszíni támadás ne befolyásolja a Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609906"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Microsoft 365 védelme a helyszíni támadásoktól

Számos ügyfél a saját vállalati hálózatait úgy kapcsolja össze, hogy Microsoft 365 a felhasználók, eszközök és alkalmazások előnyeit. Ezek a magánhálózatok azonban számos jól dokumentált módon sérülhet. Mivel Microsoft 365 sokféle idegrendszeri rendszerként működik számos szervezet számára, elengedhetetlen, hogy megvédje a feltört helyszíni infrastruktúrától.

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a rendszereket a Microsoft 365 felhőalapú környezetének a helyszíni támadásokkal szembeni védeleméhez. Elsősorban a következőkre koncentrálunk: 

- Azure Active Directory (Azure AD) bérlői konfigurációs beállításai.
- Hogyan lehet biztonságosan csatlakoztatni az Azure AD-bérlőket a helyszíni rendszerekhez.
- A rendszerek olyan módon történő üzemeltetéséhez szükséges kompromisszumok, amelyek a felhőalapú rendszereknek a helyszíni kompromisszummal való ellátását védik.

Javasoljuk, hogy ezt az útmutatót a Microsoft 365 felhőalapú környezetének biztonságossá tételéhez implementálja.
> [!NOTE]
> Ez a cikk eredetileg blogbejegyzésként jelent meg. Az élettartam és a karbantartás jelenlegi helyén lett áthelyezve.
>
> A jelen cikk offline verziójának létrehozásához használja a böngésző PDF-fájlját. Látogasson vissza ide gyakran a frissítésekhez.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Elsődleges fenyegetési vektorok a feltört helyszíni környezetekben


A Microsoft 365 felhőalapú környezete széles körű megfigyelési és biztonsági infrastruktúrát kínál. A gépi tanulás és az emberi intelligencia használatával Microsoft 365 az egész világra kiterjedő forgalomra hasonlít. Gyorsan képes felderíteni a támadásokat, és lehetővé teszi a közel valós idejű újrakonfigurálást. 

A helyszíni infrastruktúrát Microsoft 365hoz csatlakozó hibrid környezetekben számos szervezet delegálja a megbízható hitelesítéshez és a címtár-objektumok állapotának kezelésére vonatkozó döntéseket a helyszíni összetevőkhöz.
Sajnos, ha a helyszíni környezet biztonsága sérül, ezek a megbízhatósági kapcsolatok a támadók számára a Microsoft 365-környezet sérülését fenyegetik.

A két elsődleges veszélyforrás vektor az *összevonási megbízhatósági kapcsolatok* és a *fiók szinkronizálása.* Mindkét vektor megadhatja a támadónak a rendszergazdai hozzáférést a felhőhöz.

* Az **összevont megbízhatósági kapcsolatok**, például az SAML-hitelesítés a helyszíni identitás-infrastruktúrán keresztül történő Microsoft 365 hitelesítésére szolgálnak. Ha egy SAML-jogkivonat-aláíró tanúsítvány biztonsága sérül, az összevonás lehetővé teszi, hogy bárki, aki rendelkezik ezzel a tanúsítvánnyal, hogy megszemélyesítse a felhőben lévő felhasználókat. *Javasoljuk, hogy ha lehetséges, tiltsa le az összevonási megbízhatósági kapcsolatokat a hitelesítéshez Microsoft 365.*

* A **fiók-szinkronizálás** segítségével módosíthatja a Kiemelt felhasználók (beleértve a hitelesítő adataikat is), illetve a Microsoft 365 felügyeleti jogosultságokkal rendelkező csoportjaikat. *Javasoljuk, hogy a szinkronizált objektumok ne tartsanak jogosultságot a Microsoft 365 felhasználóján kívül, közvetlenül,* vagy a megbízható szerepkörök vagy csoportok belefoglalásával. Győződjön meg arról, hogy ezek az objektumok nem rendelkeznek közvetlen vagy beágyazott hozzárendeléssel a megbízható Felhőbeli szerepkörökben vagy csoportokban.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Microsoft 365 védelme a helyszíni támadásokkal szemben


A korábban ismertetett veszélyforrás-vektorok kezeléséhez javasoljuk, hogy kövesse az alábbi ábrán bemutatott elveket:

![Microsoft 365 védelmére szolgáló hivatkozási architektúra.](media/protect-m365/protect-m365-principles.png)

1. **A Microsoft 365-rendszergazdai fiókok teljes elkülönítése.** A következőket kell tenniük:

    * Az Azure AD-ben elsajátítva.

     * Többtényezős hitelesítéssel hitelesítve.

     *  Az Azure AD feltételes hozzáférése biztosítja.

     *  Csak az Azure által felügyelt munkaállomások használatával érhető el.

    Ezek a rendszergazdai fiókok korlátozott használatú fiókok. *A helyi fiókok nem rendelkezhetnek rendszergazdai jogosultságokkal Microsoft 365.* 

    További információ: [Microsoft 365 rendszergazdai szerepkörök áttekintése](/microsoft-365/admin/add-users/about-admin-roles). Lásd még: [Szerepkörök az Azure ad-ben Microsoft 365hoz](../roles/m365-workload-docs.md).

1. **Eszközök kezelése Microsoft 365ról.** Az Azure AD JOIN és a felhőalapú mobileszköz-kezelés (MDM) használatával elkerülhető a helyszíni eszközkezelés infrastruktúrájának függőségei. Ezek a függőségek veszélyeztethetik az eszköz-és biztonsági vezérlőket.

1. **Győződjön meg arról, hogy a helyi fiók nem rendelkezik emelt szintű jogosultságokkal a Microsoft 365.**
    Egyes fiókok olyan helyszíni alkalmazásokhoz férnek hozzá, amelyek NTLM-, LDAP-vagy Kerberos-hitelesítést igényelnek. Ezeknek a fiókoknak a szervezet helyi identitás-infrastruktúrájában kell lenniük. Győződjön meg arról, hogy ezek a fiókok, beleértve a szolgáltatásfiókok, nem szerepelnek a Kiemelt felhőalapú szerepkörökben vagy csoportokban. Győződjön meg arról is, hogy a fiókok módosításai nem befolyásolhatják a felhőalapú környezet integritását. A Kiemelt jogosultságú helyszíni szoftverek nem lehetnek képesek befolyásolni Microsoft 365 emelt szintű fiókokat vagy szerepköröket.

1. Az **Azure ad Felhőbeli hitelesítéssel** távolítsa el a helyszíni hitelesítő adataival kapcsolatos függőségeket. Mindig erős hitelesítést használjon, például a Windows Hello, a vagy a Microsoft Authenticator vagy az Azure AD többtényezős hitelesítést.

## <a name="specific-security-recommendations"></a>Konkrét biztonsági javaslatok


A következő szakaszokban konkrét útmutatást talál a korábban ismertetett alapelvek megvalósításával kapcsolatban.

### <a name="isolate-privileged-identities"></a>Emelt szintű identitások elkülönítése


Az Azure AD-ben a Kiemelt szerepkörrel rendelkező felhasználók, például a rendszergazdák a megbízhatóság gyökerét használják a környezet többi részének kiépítéséhez és felügyeletéhez. A következő eljárások megvalósításával csökkentheti a kompromisszum hatásait.

* Felhőalapú fiókok használata az Azure AD-hez és Microsoft 365 Kiemelt szerepkörökhöz.

* Az Microsoft 365 és az Azure AD felügyeletéhez emelt szintű jogosultságú hozzáférési [eszközök](/security/compass/privileged-access-devices#device-roles-and-profiles) helyezhetők üzembe.

*  [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) üzembe helyezése az igény szerinti (JIT) hozzáféréshez minden Kiemelt szerepkörrel rendelkező emberi fiókhoz. Erős hitelesítés szükséges a szerepkörök aktiválásához.

* Olyan rendszergazdai szerepköröket adjon meg, amelyek lehetővé teszik a [szükséges feladatok végrehajtásához szükséges legalacsonyabb jogosultságot](../roles/delegate-by-task.md).

* Egy olyan sokoldalú szerepkör-hozzárendelési élmény engedélyezéséhez, amely egyszerre több delegálást és több szerepkört is tartalmaz, érdemes lehet Azure AD-beli biztonsági csoportokat vagy Microsoft 365 csoportokat használni. Ezek a csoportok együttesen *Felhőbeli csoportok* néven szerepelnek. [Engedélyezze a szerepköralapú hozzáférés-vezérlést](../roles/groups-assign-role.md)is. A [felügyeleti egységekkel](../roles/administrative-units.md) korlátozhatja a szerepkörök hatókörét a szervezet egy részére.

* [Vészhelyzeti hozzáférési fiókok](../roles/security-emergency-access.md)üzembe helyezése. A hitelesítő adatok tárolásához *ne* használjon helyszíni jelszavas tárolókat.

További információ: a [privilegizált hozzáférés biztonságossá tétele](/security/compass/overview). Lásd még: [biztonságos hozzáférési eljárások a rendszergazdák számára az Azure ad-ben](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Felhőalapú hitelesítés használata 

A hitelesítő adatok elsődleges támadási vektorok. A hitelesítő adatok biztonságosabbá tételéhez hajtsa végre az alábbi eljárásokat:

* A [jelszóval nem rendelkező hitelesítés üzembe helyezése](../authentication/howto-authentication-passwordless-deployment.md). A jelszóval nem rendelkező hitelesítő adatok üzembe helyezésével csökkentheti a jelszavak használatát a lehető legnagyobb mértékben. Ezek a hitelesítő adatok a felhőben natív módon kezelhetők és ellenőrizhetők. Válasszon a következő hitelesítési módszerek közül:

   * [Vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [A Microsoft Authenticator alkalmazás](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 biztonsági kulcsok](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Többtényezős hitelesítés üzembe helyezése](../authentication/howto-mfa-getstarted.md). [Több erős hitelesítő adat kiépítése az Azure ad többtényezős hitelesítés használatával](../fundamentals/resilience-in-credentials.md). Ily módon a felhőalapú erőforrásokhoz való hozzáféréshez az Azure AD-ben felügyelt hitelesítő adatokra is szükség van, valamint egy helyi jelszó, amelyet kezelhet. További információ: [rugalmas hozzáférés-vezérlési kezelési stratégia létrehozása az Azure ad használatával](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Korlátozások és kompromisszumok

* A hibrid fiók jelszavas kezelése olyan hibrid összetevőket igényel, mint például a jelszavas védelem ügynökei és a jelszó-visszaírási ügynökök. Ha a helyszíni infrastruktúra biztonsága sérül, a támadók vezérelhetik azokat a gépeket, amelyeken ezek az ügynökök találhatók. Ez a biztonsági rés nem veszélyezteti a felhőalapú infrastruktúrát. A felhőalapú fiókok azonban nem védik ezeket az összetevőket a helyszíni kompromisszumból.

*  A Active Directoryról szinkronizált helyszíni fiókok soha nem járnak le az Azure AD-ben. Ezt a beállítást általában a helyszíni Active Directory jelszavának beállításai csökkentik. Ha azonban a Active Directory helyszíni példánya sérült, és a szinkronizálás le van tiltva, be kell állítania a [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) beállítást a jelszó módosításának kényszerítéséhez.

## <a name="provision-user-access-from-the-cloud"></a>Felhasználói hozzáférés kiépítése a felhőből

A *kiépítés* a felhasználói fiókok és csoportok alkalmazásokban vagy identitás-szolgáltatókban való létrehozására utal.

![A kiépítési architektúra ábrája.](media/protect-m365/protect-m365-provision.png)

A következő üzembe helyezési módszereket javasoljuk:

* Üzembe helyezés a **Felhőbeli HR-alkalmazásokból az Azure ad**-be: Ez a kiépítés lehetővé teszi a helyszíni kompromisszum elkülönítését anélkül, hogy a FELHŐbeli HR-alkalmazásokból az Azure ad-be nem zavarja az összekötő-mozgatói ciklust.

* **Felhőalapú alkalmazások**: ha lehetséges, az [Azure ad-alkalmazás](../app-provisioning/user-provisioning.md) üzembe helyezését a helyszíni kiépítési megoldásokkal szemben kell telepíteni. Ez a módszer védi a szoftveres (SaaS) alkalmazások némelyikét a helyszíni támadásokban lévő rosszindulatú hacker-profilok által érintettek számára. 

* **Külső identitások**: az [Azure ad B2B együttműködés](../external-identities/what-is-b2b.md)használata.
    Ez a módszer csökkenti a helyszíni fiókoktól való függőséget a partnerekkel, ügyfelekkel és szállítókkal való külső együttműködés érdekében. Alaposan értékelje ki a közvetlen összevonást más identitás-szolgáltatókkal. Javasoljuk, hogy az alábbi módokon korlátozzák a B2B vendég fiókok használatát:

   *  A címtárban lévő tallózási csoportok és egyéb tulajdonságok elérésének korlátozása. A külső együttműködési beállítások használatával korlátozhatja a vendégek azon csoportok olvasását, amelyek nem tagjai a csoportoknak. 

    *   A Azure Portal elérésének letiltása. Ritkán szükséges kivételeket hozhat.  Hozzon létre egy feltételes hozzáférési szabályzatot, amely tartalmazza az összes vendéget és a külső felhasználókat. Ezután [hozzon létre egy szabályzatot a hozzáférés blokkolásához](../../role-based-access-control/conditional-access-azure-management.md). 

* **Leválasztott erdők**: az [Azure ad Cloud kiépítés](../cloud-sync/what-is-cloud-sync.md)használata. Ez a módszer lehetővé teszi a leválasztott erdőkhöz való csatlakozást, így nem kell erdők közötti kapcsolatot vagy megbízhatóságot létesíteni, ami kibővítheti a helyszíni szabálysértés hatását. 
 
### <a name="limitations-and-tradeoffs"></a>Korlátozások és kompromisszumok

Hibrid fiókok kiépítésének használatakor az Azure-AD-from-Cloud-HR rendszer helyszíni szinkronizálást használ a Active Directoryról az Azure AD-be történő adatforgalom befejezéséhez. Ha a szinkronizálás megszakad, az új alkalmazotti rekordok nem lesznek elérhetők az Azure AD-ben.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Felhőalapú csoportok használata együttműködéshez és hozzáféréshez

A felhőalapú csoportok lehetővé teszik az együttműködés és a helyszíni infrastruktúrák elérését.

* **Együttműködés**: Microsoft 365 csoportok és Microsoft Teams használata a modern együttműködéshez. Kiszerelheti a helyszíni terjesztési listát, és [frissítheti a terjesztési listát az outlookban Microsoft 365 csoportokba](/office365/admin/manage/upgrade-distribution-lists).

* **Hozzáférés**: az Azure ad biztonsági csoportjaival vagy Microsoft 365 csoportokkal engedélyezheti az alkalmazásokhoz való hozzáférést az Azure ad-ben.
* **Office 365 licencelés**: csoportos licencelés használatával kiépíthető az Office 365-be a csak felhőalapú csoportok használatával. Ez a módszer leválasztja a csoporttagság vezérlését a helyszíni infrastruktúrából.

A hozzáféréshez használt csoportok tulajdonosait emelt szintű identitásnak kell tekinteni, hogy elkerülje a tagságot a helyszíni kompromisszumban.
Az átvétel magában foglalja a csoporttagság közvetlen kezelését a helyszínen, vagy olyan helyszíni attribútumok kezelését, amelyek hatással lehetnek a dinamikus csoporttagság Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Eszközök kezelése a felhőből


Az Azure AD-funkciókkal biztonságosan felügyelheti az eszközöket.

-   **Windows 10 rendszerű munkaállomások használata**: az [Azure ad-hez csatlakoztatott eszközök üzembe helyezése](../devices/azureadjoin-plan.md) Mdm-házirendekkel. A [Windows Autopilot](/mem/autopilot/windows-autopilot) engedélyezése teljesen automatizált üzembe helyezési élményhez.

    -   A Windows 8,1-es vagy korábbi verzióját futtató gépek elavultak.

    -   Ne telepítse a kiszolgáló operációs rendszerű gépeket munkaállomásként.

    -   Az összes eszközkezelés munkaterheléshez használja a [Microsoft Intunet](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) a szolgáltató forrásaként.

-   Emelt [**szintű hozzáférési eszközök üzembe helyezése**](/security/compass/privileged-access-devices#device-roles-and-profiles): a Microsoft 365 és az Azure ad felügyeletéhez emelt szintű hozzáférés használata.

## <a name="workloads-applications-and-resources"></a>Munkaterhelések, alkalmazások és erőforrások 

-   **Helyszíni egyszeri bejelentkezéses (SSO) rendszerek** 

    A helyszíni összevonási és a webes hozzáférés-kezelési infrastruktúra elavult. Alkalmazások konfigurálása az Azure AD használatára.  

-   **A modern hitelesítési protokollokat támogató SaaS-és üzletági (LOB) alkalmazások** 

    [Az Azure ad használata egyszeri bejelentkezéshez](../manage-apps/what-is-single-sign-on.md). Minél több alkalmazás van konfigurálva az Azure AD hitelesítéshez való használatára, annál kevesebb kockázatot jelent a helyszíni biztonság.


* **Örökölt alkalmazások** 

   * Engedélyezheti a hitelesítést, az engedélyezést és a távoli hozzáférést a modern hitelesítést nem támogató örökölt alkalmazásokhoz. Az [Azure ad Application proxy](../manage-apps/application-proxy.md)használata. A [biztonságos hibrid hozzáférési partner-integrációk](../manage-apps/secure-hybrid-access.md)használatával hálózati vagy Application Delivery Controller-megoldáson keresztül is engedélyezheti azokat.   

   * Válasszon olyan VPN-gyártót, amely támogatja a modern hitelesítést. A hitelesítés integrálása az Azure AD-vel. Helyszíni kompromisszum esetén az Azure AD használatával letilthatja vagy letilthatja a hozzáférést a VPN letiltásával.

*  **Alkalmazás-és munkaterhelés-kiszolgálók**

   * Azok az alkalmazások vagy erőforrások, amelyeket a szükséges kiszolgálók áttelepíthetnek az Azure-infrastruktúra szolgáltatásként (IaaS). A [Azure ad Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) használatával elválaszthatja a megbízhatóságot és a függőséget Active Directory helyszíni példányain. A leválasztás eléréséhez győződjön meg arról, hogy az Azure AD DS használt virtuális hálózatok nem rendelkeznek a vállalati hálózatokhoz való csatlakozással.

   * Kövesse a [hitelesítő adatokra vonatkozó rétegek](/security/compass/privileged-access-access-model#ADATM_BM)útmutatását. Az alkalmazás-kiszolgálók általában 1. rétegbeli eszközöknek tekintendők.

## <a name="conditional-access-policies"></a>Feltételes hozzáférési szabályzatok

Az Azure AD feltételes hozzáféréssel a jelek értelmezésére és a hitelesítési döntések elvégzésére használható. További információkért lásd a [feltételes hozzáférés telepítési tervét](../conditional-access/plan-conditional-access.md).

* Ha lehetséges, használja a feltételes hozzáférést az [örökölt hitelesítési protokollok blokkolásához](../conditional-access/howto-conditional-access-policy-block-legacy.md) . Emellett az alkalmazás szintjén tiltsa le az örökölt hitelesítési protokollokat egy alkalmazásspecifikus konfiguráció használatával.

   További információ: [örökölt hitelesítési protokollok](../fundamentals/auth-sync-overview.md). Vagy tekintse meg az [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) és a [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant)adott részleteit.

* A javasolt [identitás-és eszköz-hozzáférési konfigurációk](/microsoft-365/security/office-365-security/identity-access-policies)implementálása.

* Ha olyan Azure AD-verziót használ, amely nem tartalmaz feltételes hozzáférést, győződjön meg arról, hogy az [Azure ad biztonsági alapértékeit](../fundamentals/concept-fundamentals-security-defaults.md)használja.

   Az Azure AD szolgáltatás licencelésével kapcsolatos további információkért tekintse meg az [Azure ad díjszabási útmutatóját](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>Monitor 

Miután úgy konfigurálta a környezetet, hogy az Microsoft 365t a helyszíni támadásokkal szemben, [proaktívan figyelje](../reports-monitoring/overview-monitoring.md) a környezetet.
### <a name="scenarios-to-monitor"></a>Monitorozandó forgatókönyvek

Figyelje meg a következő, a szervezetre vonatkozó forgatókönyvek melletti forgatókönyveket. Például proaktívan figyelnie kell az üzleti szempontból kritikus fontosságú alkalmazásaihoz és erőforrásaihoz való hozzáférést.

* **Gyanús tevékenység** 

    A gyanús tevékenységekre vonatkozó összes [Azure ad kockázati esemény](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) figyelése. A [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md) natív módon van integrálva a Azure Security Centerval.

    A hely alapú jelek zajos észlelésének elkerülése érdekében adja meg a [helyek nevű](../reports-monitoring/quickstart-configure-named-locations.md) hálózatot. 
*  **Felhasználói és entitások viselkedésének elemzéséhez (UEBA) kapcsolódó riasztások** 

    A UEBA használatával bepillantást nyerhet a anomáliák észlelésére.
    * Microsoft Cloud App Security (MCAS) [UEBA biztosít a felhőben](/cloud-app-security/tutorial-ueba).

    * A helyszíni [UEBA integrálható az Azure komplex veszélyforrások elleni védelem (ATP)](/defender-for-identity/install-step2)használatával. A MCAS beolvassa a jeleket a Azure AD Identity Protectionból. 

* **Vészhelyzeti hozzáférésű fiókok tevékenység** 

    A [vészhelyzeti hozzáférési fiókokat](../roles/security-emergency-access.md)használó összes hozzáférés figyelése. Riasztások létrehozása a vizsgálatokhoz. A figyelésnek a következőket kell tartalmaznia: 

   * Bejelentkezések. 

   * Hitelesítőadat-kezelés. 

   * A csoporttagságok frissítései. 

   * Alkalmazás-hozzárendelések. 
* **Kiemelt szerepköri tevékenység**

    [Azure ad Privileged Identity Management (PIM) által generált biztonsági riasztások](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts)konfigurálása és áttekintése.
    A rendszerjogosultságú szerepkörök a PIM-n kívüli közvetlen hozzárendelésének figyelése riasztások létrehozásával, amikor egy felhasználó közvetlenül van hozzárendelve.

* **Azure AD-bérlőre kiterjedő konfigurációk**

    A bérlői szintű konfigurációk bármilyen módosítása esetén riasztásokat kell készíteni a rendszeren. Ezek a változások többek között a következők:

  *  Frissített egyéni tartományok.  

  * Az Azure AD B2B a allowlists-és címek listáit-módosításokat hajtja végre.

  * Az Azure AD B2B az engedélyezett identitás-szolgáltatók (SAML-azonosítók, közvetlen összevonás vagy közösségi bejelentkezések) alapján történő módosítását teszi lehetővé.  

  * A feltételes hozzáférés vagy a kockázatkezelési szabályok változásai. 

* **Alkalmazás- és szolgáltatásnév objektumok**
    
   * Új alkalmazások vagy egyszerű szolgáltatások, amelyek feltételes hozzáférési szabályzatokat igényelhetnek. 

   * Az egyszerű szolgáltatásokhoz hozzáadott hitelesítő adatok.
   * Alkalmazás-engedélyezési tevékenység. 

* **Egyéni szerepkörök**
   * Az egyéni szerepkör-definíciók frissítései. 

   * Újonnan létrehozott egyéni szerepkörök. 

### <a name="log-management"></a>Naplózás kezelése

Meghatározhatja a naplózási tárolási és adatmegőrzési stratégiát, a tervezést és a megvalósítást a konzisztens eszközkészlet megkönnyítése érdekében. Például megtekintheti a biztonsági információkat és az eseménykezelő (SIEM) rendszereket, például az Azure Sentinel, a gyakori lekérdezések és a nyomozás és a kriminalisztika forgatókönyveit.

* **Azure ad-naplók**: a generált naplók és jelek betöltése az ajánlott eljárások, például a diagnosztika, a naplózási megőrzés és az Siem-adatfeldolgozás során. 

    A naplózási stratégiának tartalmaznia kell a következő Azure AD-naplókat:
   * Bejelentkezési tevékenység 

   * Naplók 

   * Kockázati események 

    Az Azure AD [Azure monitor integrációt](../reports-monitoring/concept-activity-logs-azure-monitor.md) biztosít a bejelentkezési tevékenység naplójának és a naplóinak. A kockázati események a [Microsoft Graph API](/graph/api/resources/identityriskevent)-n keresztül tölthetők be. [Az Azure ad-naplókat Azure monitor naplókba is továbbíthatja](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* A **hibrid infrastruktúra operációs rendszerének biztonsági naplói**: az összes hibrid identitás-infrastruktúra operációs rendszerének naplóit archiválni kell, és egy 0. rétegbeli rendszernek kell figyelnie, a felszíni terület következményei miatt. A következő elemek belefoglalása: 

   *  Azure AD Connect. Az identitás-szinkronizálás figyeléséhez [Azure ad Connect Health](../hybrid/whatis-azure-ad-connect.md) kell telepíteni.

   *  Alkalmazásproxy-ügynökök 


   * Visszaírási-ügynökök jelszava 

   * Jelszavas védelem átjárójának gépei  

   * Hálózati házirend-kiszolgálók (NPSs), amelyek rendelkeznek az Azure AD többtényezős hitelesítési RADIUS-bővítménnyel 

## <a name="next-steps"></a>Következő lépések
* [Hozzon létre rugalmasságot az identitás-és hozzáférés-kezeléshez az Azure AD használatával](resilience-overview.md)

* [Biztonságos külső hozzáférés az erőforrásokhoz](secure-external-access-resources.md) 
* [Az összes alkalmazás integrálása az Azure AD-vel](five-steps-to-full-application-integration-with-azure-ad.md)