---
title: Öt lépés az összes alkalmazás integrálásához az Azure AD-vel
description: Ez az útmutató ismerteti, hogyan integrálható az összes alkalmazás az Azure AD-vel. Az egyes lépésekben leírjuk az értéket, és olyan erőforrásokra mutató hivatkozásokat adunk meg, amelyek leírják a technikai részleteket.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7fd9e504448d55b4a2ef8c10b4ba1176cb2e3402
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172635"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Öt lépés az összes alkalmazás integrálásához az Azure AD-vel

Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Az Azure AD biztonságos hitelesítési és engedélyezési megoldásokat biztosít ahhoz, hogy az ügyfelek, partnerek és alkalmazottak hozzáférhessenek a szükséges alkalmazásokhoz. Az Azure AD-vel, a [feltételes hozzáféréssel](../conditional-access/overview.md), a [többtényezős hitelesítéssel](../authentication/concept-mfa-howitworks.md), az [egyszeri bejelentkezéssel](../hybrid/how-to-connect-sso.md)és az [automatikus felhasználó-kiépítési](../app-provisioning/user-provisioning.md) szolgáltatással egyszerűen és biztonságossá teheti az identitás-és hozzáférés-kezelést.

Ha a vállalata Microsoft 365-előfizetéssel rendelkezik, akkor valószínűleg [már használja](/office365/enterprise/about-office-365-identity) az Azure ad-t. Az Azure AD azonban az összes alkalmazásához használható, és az [alkalmazások kezelésének központosításával](../manage-apps/common-scenarios.md) a teljes alkalmazás-portfólióban ugyanazokat az Identitáskezelés-felügyeleti funkciókat, eszközöket és házirendeket használhatja. Ez egy egységes megoldást biztosít, amely javítja a biztonságot, csökkenti a költségeket, növeli a termelékenységet, és lehetővé teszi a megfelelőség biztosítását. A helyszíni alkalmazások távoli elérését is elérhetővé teszi.

Ez az útmutató ismerteti, hogyan integrálható az összes alkalmazás az Azure AD-vel. Az egyes lépésekben leírjuk az értéket, és olyan erőforrásokra mutató hivatkozásokat adunk meg, amelyek leírják a technikai részleteket. Ezeket a lépéseket a javasolt sorrendben mutatjuk be. Megkezdheti azonban a folyamat bármely részének megkezdését, ami a legértékesebb értéket adja meg Önnek.

A témakör további erőforrásai, beleértve az üzleti folyamatok részletes tanulmányait is, amelyek az [alkalmazások Azure Active Directory lapra való áttelepítésének forrásaiban](../manage-apps/migration-resources.md) találhatók.

## <a name="1-use-azure-ad-for-new-applications"></a>1. az Azure AD használata új alkalmazásokhoz

Először is koncentráljon az újonnan beszerzett alkalmazásokra. Ha a vállalat új alkalmazást kezd használni, azonnal [adja hozzá az Azure ad-bérlőhöz](../manage-apps/add-application-portal.md) . Állítson be egy vállalati szabályzatot, hogy az új alkalmazások az Azure AD-be való hozzáadásával a szervezete szokásos gyakorlata legyen. Ez minimálisan zavaró a meglévő üzleti folyamatok esetében, és lehetővé teszi az alkalmazások integrálásának megvizsgálása és bizonyítása anélkül, hogy az emberek a környezetben használják az üzleti tevékenységet.

Azure Active Directory (Azure AD) gyűjteménye több ezer előre integrált alkalmazást tartalmaz, amelyek megkönnyítik az első lépéseket. Az [Azure ad-szervezethez hozzáadhat egy Gallery-alkalmazást](../manage-apps/add-application-portal.md) , amely részletes [oktatóanyagokat](../saas-apps/tutorial-list.md) biztosít a népszerű alkalmazások integrálásához, például:

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

Emellett a katalógusban [nem szereplő alkalmazásokat is integrálhat](../manage-apps/view-applications-portal.md), beleértve a szervezetében már létező alkalmazásait, vagy bármely olyan gyártótól származó alkalmazást, amely még nem része az Azure ad-katalógusnak. Azt is megteheti, [hogy hozzáadta az alkalmazást a](../develop/v2-howto-app-gallery-listing.md) katalógushoz, ha az nem létezik.

Végül integrálhatja a házon belül fejlesztett alkalmazásokat is. Ez a jelen útmutató ötödik lépésében szerepel.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. a meglévő alkalmazás-használat és a feladatok rangsorolásának meghatározása

Ezután Fedezze fel az alkalmazások alkalmazottai gyakran használják, és rangsorolja a munkáját, hogy integrálja őket az Azure AD-vel.

A Microsoft Cloud App Security [Cloud Discovery-eszközeivel](/cloud-app-security/tutorial-shadow-it) elkezdheti felderíteni és kezelni az "árnyékot" a hálózatban (azaz az IT-részleg által nem kezelt alkalmazásokat). A felderítési folyamat egyszerűsítéséhez és bővítéséhez [használhatja a Microsoft Defender Advanced Threat Protection (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) eszközt.

Emellett a Azure Portal [AD FS alkalmazás tevékenység jelentésével](../manage-apps/migrate-adfs-application-activity.md) felderítheti a szervezetben lévő összes AD FS alkalmazást, a számukra bejelentkezett egyedi felhasználók számát, és az Azure ad-vel való integráláshoz való kompatibilitást is.

Miután felfedezte a meglévő tájat, [létre kell hoznia egy tervet](../manage-apps/migration-resources.md) , és rangsorolnia kell a legmagasabb prioritású alkalmazásokat az integráláshoz. Néhány példa arra, hogy a folyamat útmutatója a következő:

- Mely alkalmazásokat használják leginkább?
- Melyek a legkockázatosabb?
- Mely alkalmazások lesznek leszerelve a jövőben, így szükségtelen a költözés?
- Mely alkalmazásoknak kell a helyszínen maradni, és nem helyezhetők át a felhőbe?

Az alkalmazások integrálása után a legnagyobb előnyök és költségmegtakarítások jelennek meg, és már nem támaszkodhat több Identity megoldásra. Ugyanakkor könnyebb az Identitáskezelés és a fokozott biztonság, ahogy az a cél irányába mozdul el. Ezt az időt szeretné használni, hogy rangsorolja a munkáját, és döntse el, hogy milyen értelme van a helyzetnek.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. a más identitás-szolgáltatókra támaszkodó alkalmazások integrálása

A felderítési folyamat során előfordulhat, hogy az informatikai részleg által nyomon követett alkalmazásokat talált, így az adatai és erőforrásai sebezhetőek maradnak. Olyan alkalmazásokat is használhat, amelyek alternatív identitási megoldásokat használnak, beleértve a Active Directory összevonási szolgáltatások (AD FS) (ADFS) vagy más identitás-szolgáltatókat. Gondolja át, hogyan tudja összevonni az identitás-és hozzáférés-kezelést, hogy pénzt takarítson meg, és növelje a biztonságot. Az Ön által birtokolt személyazonossági megoldások számának csökkentése:

- A helyszíni felhasználók kiépítésének és hitelesítésének, valamint az ugyanazon szolgáltatáshoz tartozó más felhőalapú identitás-szolgáltatóknak fizetett licencelési díjaknak a megtakarításával pénzt takaríthat meg.
- Csökkentse az adminisztrációs terhelést, és engedélyezze a szigorúbb biztonságot az identitás-és hozzáférés-kezelési folyamat kevesebb redundanciával.
- Lehetővé teheti az alkalmazottak számára, hogy az [MyApps-portálon](../manage-apps/access-panel-collections.md)keresztül biztonságosan egyszeri bejelentkezéses hozzáférést kapjanak az összes szükséges alkalmazáshoz.
- Az Azure AD [Identity Protection](../identity-protection/overview-identity-protection.md) szolgáltatáshoz kapcsolódó szolgáltatások (például a feltételes hozzáférés) javítása az alkalmazás használatából származó adatok mennyiségének növelésével, valamint az újonnan hozzáadott alkalmazások előnyeinek kibővítésével.

Tettünk közzé útmutatást az alkalmazások Azure AD-vel való integrálásának üzleti folyamatának kezeléséhez, beleértve a [plakátot](https://aka.ms/AppOnePager) és a [bemutatót](https://aka.ms/AppGuideline) , amellyel az üzleti és az alkalmazás-tulajdonosok tudatában és érdeklik. Ezeket a mintákat saját arculatával módosíthatja, és közzéteheti azokat a céges portálon, a hírlevélen vagy más médiumon keresztül, amikor befejezi a folyamat befejezését.

A kezdéshez jó kiindulópont a Active Directory összevonási szolgáltatások (AD FS) (ADFS) használatának kiértékelése. Számos szervezet használja az ADFS-t az SaaS-alkalmazásokkal, az egyéni üzletági alkalmazásokkal és a Microsoft 365 és az Azure AD-alapú alkalmazásokkal való hitelesítéshez:

![A diagramon a helyszíni alkalmazások, az üzletági alkalmazások, az SaaS-alkalmazások, valamint az Azure AD-n keresztül az Office 365 minden pontokkal összekapcsolható Active Directory és AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Ezt a konfigurációt úgy frissítheti, ha [lecseréli az ADFS](../manage-apps/migrate-adfs-apps-to-azure.md) -t az Azure ad-be az identitáskezelési megoldás központjaként. Ez lehetővé teszi a bejelentkezést minden olyan alkalmazáshoz, amelyet az alkalmazottak szeretne elérni, és megkönnyíti az alkalmazottak számára a [MyApps-portálon](../user-help/my-apps-portal-end-user-access.md)keresztül igénybe venni kívánt üzleti alkalmazások megtalálását a fent említett egyéb előnyök mellett.

![A diagram a helyi alkalmazásokat Active Directory és AD FS, üzletági alkalmazások, SaaS-alkalmazások és az Office 365 használatával jeleníti meg, amelyek mindegyike pontozott vonallal csatlakozik Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Ha az Azure AD a központi identitás-szolgáltató lesz, akkor lehetséges, hogy összevont megoldás helyett teljesen át tud váltani az ADFS-ből. Az olyan alkalmazások, amelyek korábban az ADFS-t használják a hitelesítéshez, mostantól használhatják az Azure AD-t.

![A diagram a helyszínen, az üzletági alkalmazásokban, az SaaS-alkalmazásokban és az Office 365-ben jeleníti meg az összes pontozott vonallal összekapcsolt Azure Active Directory. Active Directory és AD FS nincs jelen.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Áttelepíthet olyan alkalmazásokat is, amelyek eltérő felhőalapú identitás-szolgáltatót használnak az Azure AD-ben. Előfordulhat, hogy a szervezete több Identity Access-felügyeleti (IAM) megoldással rendelkezik. Az egyik Azure AD-infrastruktúrába való Migrálás lehetősége van arra, hogy csökkentse az IAM-licencek (helyszíni vagy Felhőbeli) és az infrastrukturális költségek függőségeit. Azokban az esetekben, amikor az Azure AD-t a M365-licenceken keresztül már kifizette, nem kell fizetnie egy másik IAM megoldás hozzáadott költségeinek.

## <a name="4-integrate-on-premises-applications"></a>4. helyszíni alkalmazások integrálása

Az alkalmazások védelme hagyományosan a vállalati hálózathoz való csatlakozáshoz való hozzáférés engedélyezésével történt. Az egyre szorosabban összekapcsolt világban azonban az ügyfelek, partnerek és/vagy alkalmazottak számára elérhetővé szeretnénk tenni az alkalmazásokhoz való hozzáférést, függetlenül attól, hogy hol vannak a világon. Az Azure [ad Application proxy](../manage-apps/what-is-application-proxy.md) (AppProxy) az Azure ad egyik funkciója, amely összekapcsolja a meglévő helyszíni alkalmazásokat az Azure ad-vel, és nem igényli az Edge-kiszolgálók vagy más további infrastruktúra fenntartását.

![A diagram az alkalmazásproxy szolgáltatást mutatja működés közben. A felhasználó a (z) "", és a hozzá https://sales.contoso.com tartozó kérést átirányítja a (z) Azure Active Directory a helyszíni címen található " https://sales-contoso.msappproxy.net http://sales " helyre.](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Használhatja az [oktatóanyagot: helyi alkalmazás hozzáadása a távoli eléréshez](../manage-apps/application-proxy-add-on-premises-application.md) az alkalmazásproxy használatával Azure Active Directory az alkalmazásproxy engedélyezéséhez és egy helyszíni alkalmazás Azure ad-bérlőhöz való hozzáadásához.

Emellett integrálhatja az alkalmazások kézbesítési vezérlőit, például az F5 Big-IP APM vagy a Zscaler privát elérését. Ezeknek az Azure AD-val való integrálásával az Azure AD modern hitelesítését és identitás-kezelését a partner termék Traffic Management és Security funkciói mellett érheti el. Ezt a megoldást [biztonságos hibrid hozzáférésnek](../manage-apps/secure-hybrid-access.md)nevezzük. Ha jelenleg a következő szolgáltatások valamelyikét használja, oktatóanyagokat talál, amelyek végigvezetik az Azure AD-vel való integrálás lépésein.

- [Akamai Enterprise Application Access (EAA)](../saas-apps/akamai-tutorial.md)
- [Citrix Application Deliver Controller (ADC)](../saas-apps/citrix-netscaler-tutorial.md) (korábbi nevén Citrix NetScaler)
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. a fejlesztők által felépített alkalmazások integrálása

A vállalaton belül létrehozott alkalmazások esetében a fejlesztők a [Microsoft Identity platform](../develop/index.yml) segítségével implementálják a hitelesítést és az engedélyezést. A platformmal integrált alkalmazások [regisztrálva lesznek az Azure ad](../develop/quickstart-register-app.md) -ben, és ugyanúgy kezelhetők, mint bármely más alkalmazás a portfólióban.

A fejlesztők a platformot használhatják a belső használatra szánt alkalmazások és az ügyfelek felé irányuló alkalmazások számára is, és a platform használatának más előnyei is vannak. A platform részét képező [Microsoft Authentication librarys (MSAL)](../develop/msal-overview.md)lehetővé teszi a fejlesztők számára, hogy a többtényezős hitelesítéssel és a biztonsági kulcsok használatával hozzáférjenek az alkalmazásaihoz anélkül, hogy azt maguknak kellene implementálni. Emellett a Microsoft Identity platformmal integrált alkalmazások is hozzáférhetnek [Microsoft Graph](../develop/microsoft-graph-intro.md) – egy egységes API-végpontot, amely a munkahelyen a termelékenység, az identitás és a biztonság mintáit leíró Microsoft 365-adatokkal rendelkezik. A fejlesztők ezeket az információkat olyan funkciók megvalósítására használhatják, amelyek növelhetik a hatékonyságot a felhasználók számára. Például azonosíthatja azokat a személyeket, akikkel a felhasználó a közelmúltban kommunikált, és az alkalmazás felhasználói felületén felszínre került.

Egy videó- [sorozatot](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) biztosítunk, amely átfogó bevezetést nyújt a platformhoz, valamint számos, a támogatott nyelveken és platformokon elérhető [kódrészletet](../develop/sample-v2-code.md) .

## <a name="next-steps"></a>Következő lépések

- [Forrásanyagok az alkalmazások Azure Active Directoryba való migrálásához](../manage-apps/migration-resources.md)