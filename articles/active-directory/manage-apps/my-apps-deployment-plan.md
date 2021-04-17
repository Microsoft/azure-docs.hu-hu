---
title: A Azure Active Directory Saját alkalmazások megterve
description: Tervezési útmutató a szervezeti Saját alkalmazások hatékony alkalmazáshoz.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: baselden
ms.openlocfilehash: 777daecc119a158f11d865489e4eb497c3bc7899
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376596"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>A Azure Active Directory Saját alkalmazások megterve

> [!NOTE]
> Ez a cikk olyan informatikai szakemberek számára készült, akiknek meg kell tervezni a céges portál Saját alkalmazások konfigurálását. 
>
> **A végfelhasználói dokumentációt [lásd: Bejelentkezés](../user-help/my-apps-portal-end-user-access.md)** és alkalmazások Saját alkalmazások portálon.

Azure Active Directory (Azure AD) Saját alkalmazások egy webalapú portál az alkalmazások indításához és kezeléséhez. Az Saját alkalmazások oldalon a felhasználók egyetlen helyen kezdhetik el a munkájukat, és megkeresik az összes alkalmazást, amelyhez hozzáféréssel rendelkezik. A felhasználók a Saját alkalmazások [https://myapps.microsoft.com](https://myapps.microsoft.com/) eléréséhez.

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Miért érdemes konfigurálni Saját alkalmazások

A Saját alkalmazások portál alapértelmezés szerint elérhető a felhasználók számára, és nem lehet kikapcsolni. Fontos, hogy úgy konfigurálja, hogy a lehető legjobb felhasználói élményben legyen része, és a portál mindig hasznos maradjon. 

A vállalati alkalmazások listájában Azure Active Directory alkalmazás akkor jelenik meg, ha mindkét alábbi feltétel teljesül:

* Az alkalmazás láthatósági tulajdonsága igazra van állítva. 

* Az alkalmazás bármely felhasználóhoz vagy csoporthoz hozzá van rendelve. Megjelenik a hozzárendelt felhasználók számára.

A portál konfigurálása biztosítja, hogy a megfelelő személyek könnyen megtalálják a megfelelő alkalmazásokat.

 
### <a name="how-is-the-my-apps-portal-used"></a>Hogyan használható Saját alkalmazások portál?

A felhasználók a következő Saját alkalmazások férnek hozzá az Saját alkalmazások portálhoz:

* A szervezet összes olyan Azure AD-hez csatlakoztatott alkalmazásának felderítése és elérése, amelyhez hozzáféréssel rendelkezik.

   * A legjobb, ha az alkalmazások egyszeri bejelentkezésre (SSO) vannak konfigurálva, hogy a lehető legjobb felhasználói élményt nyújtják.

* Kérjen hozzáférést az önkiszolgáló konfigurált új alkalmazásokhoz.

* Személyes alkalmazásgyűjtemények létrehozása.

* Kezelheti mások alkalmazás-hozzáférését, ha csoporttulajdonosi szerepkörhöz vagy delegált vezérléshez van hozzárendelve az alkalmazás(nak) hozzáférésének megadásához használt csoport.

A rendszergazdák a következő beállításokat konfigurálhatják:

* [Hozzájárulási élmények,](../manage-apps/configure-user-consent.md)  beleértve a szolgáltatási feltételeket is.

* [Önkiszolgáló alkalmazásfelderítési és -hozzáférési kérések.](../manage-apps/access-panel-manage-self-service-access.md)

* [Alkalmazásgyűjtemények.](../manage-apps/access-panel-collections.md)

* Ikonok hozzárendelése alkalmazásokhoz

* Alkalmazások felhasználóbarát nevei

* A vállalati arculat Saját alkalmazások

 

## <a name="plan-consent-configuration"></a>Hozzájárulási konfiguráció megterve

### <a name="user-consent-for-applications"></a>Felhasználói jóváhagyás az alkalmazásokhoz 

Ahhoz, hogy egy felhasználó bejelentkezhet egy alkalmazásba, és az alkalmazás hozzáfér a szervezet adataihoz, a felhasználónak vagy a rendszergazdának meg kell adnunk az alkalmazásnak az engedélyeket. Megadhatja, hogy a felhasználói jóváhagyás engedélyezve legyen-e, és milyen feltételek mellett. **A Microsoft azt javasolja, hogy csak az ellenőrzött közzétevőktől származó alkalmazásokhoz engedélyezze a felhasználói jóváhagyást.**

További információ: Annak konfigurálása, hogy a [végfelhasználók hogyan járulnak hozzá az alkalmazásokhoz.](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Csoporttulajdonosi jóváhagyás az adatokhoz hozzáférő alkalmazásokhoz

A csoport- és csoporttulajdonosok engedélyezhetik az alkalmazások , például a külső szállítók által közzétett alkalmazások számára a szervezet egy csoporthoz társított adatainak hozzáférését. További [információ: Erőforrás-specifikus hozzájárulás a Microsoft Teamsben.](https://docs.microsoft.com/microsoftteams/resource-specific-consent) 

Beállíthatja, hogy szeretné-e engedélyezni vagy letiltani ezt a funkciót.

További információ: [Csoport-hozzájárulási engedélyek konfigurálása.](../manage-apps/configure-user-consent-groups.md)

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció kritikus fontosságú az új szolgáltatások sikerességéhez. Proaktív módon tájékoztassa a felhasználókat arról, hogy a felhasználói élmény hogyan és mikor változik meg, és szükség esetén hogyan lehet támogatást szerezni.

Bár Saját alkalmazások nem hoz létre általában felhasználói problémákat, fontos felkészülni. Az indítás előtt hozzon létre útmutatókat és a támogatási munkatársak összes erőforrásának listáját.

#### <a name="communications-templates"></a>Kommunikációs sablonok

A Microsoft testre [szabható sablonokat biztosít az e-mailekhez](https://aka.ms/APTemplates) és más kommunikációs Saját alkalmazások. Ezeket az eszközöket a vállalati kultúra szerint más kommunikációs csatornákon való használatra is adaptálhatja.

 

## <a name="plan-your-sso-configuration"></a>Az SSO-konfiguráció megterve

A legjobb, ha az SSO engedélyezve van az Saját alkalmazások portálon elérhető összes alkalmazáshoz, hogy a felhasználók zökkenőmentes felhasználói élményt tapasztalnak anélkül, hogy meg kellene adnia a hitelesítő adataikat.

Az Azure AD több SSO-lehetőséget is támogat. 

* További információ: Egyszeri bejelentkezési lehetőségek az [Azure AD-ban.](sso-options.md)

* Az Azure AD alkalmazásidentitás-szolgáltatóként való használatával kapcsolatos további információkért lásd az alkalmazáskezelés rövid [útmutatóját.](../manage-apps/view-applications-portal.md)

### <a name="use-federated-sso-if-possible"></a>Összevont SSO használata, ha lehetséges

A legjobb felhasználói élmény érdekében a Saját alkalmazások az összevont SSO -hez (OpenID Connect VAGY SAML-hez) elérhető felhőalkalmazások integrációjával kezdje. Az összevont SSO lehetővé teszi, hogy a felhasználók egységes, egyetlen kattintással indítható felületet használjanak az alkalmazás indításához, és általában robusztusabb konfiguráció-vezérléssel rendelkeznek.

A szolgáltatott szoftver (SaaS) alkalmazások SSO-hoz való konfigurálásával kapcsolatos további információkért lásd: [SaaS SSO deployment plan]. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Speciális SSO-körülmények esetén megfontolandó szempontok

> [!TIP]
> A jobb felhasználói élmény érdekében használjon összevont SSO-t az Azure AD-val (OpenID Connect/SAML), ha egy alkalmazás támogatja azt a jelszóalapú SSO és az ADFS helyett.

A jelszóalapú SSO-alkalmazásokba vagy az Azure AD alkalmazásproxy által elért alkalmazásokba való bejelentkezéshez a felhasználóknak telepítenie és használnia kell a Saját alkalmazások biztonságos bejelentkezési bővítményt. Amikor először indítják el a jelszóalapú SSO-t vagy az alkalmazást, a rendszer felkéri a alkalmazásproxy telepítésére. 

![Képernyőkép a következőről:](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

A bővítővel kapcsolatos részletes információkért lásd: [Installing Saját alkalmazások browser extension (Böngészőbővítmény telepítése).](../user-help/my-apps-portal-end-user-access.md)

Ha integrálni kell ezeket az alkalmazásokat, meg kell határoznia egy mechanizmust, amely lehetővé teszi a bővítmény nagy léptékű üzembe helyezését a [támogatott böngészőkkel.](../user-help/my-apps-portal-end-user-access.md) A lehetőségek a következők:

* [Felhasználó által vezérelt letöltés és konfiguráció Chrome, Firefox, Microsoft Edge vagy IE böngészőhöz](../user-help/my-apps-portal-end-user-access.md)

* [Konfigurációkezelő Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

A bővítmény lehetővé teszi a felhasználók számára, hogy a keresősávból indítsanak el alkalmazásokat, hozzáférést találjanak a legutóbb használt alkalmazásokhoz, és Saját alkalmazások lapra.

![Képernyőkép az alkalmazásbővítmények keresésről](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>A mobil hozzáférés megterve

A jelszóalapú SSO-t használó vagy [](../manage-apps/application-proxy.md)a Microsoft Azure AD alkalmazásproxy használatával elért alkalmazásokhoz mobileszközökön Microsoft Edge kell. Más alkalmazásokhoz bármilyen mobilböngésző használható. 

### <a name="linked-sso"></a>Összekapcsolt SSO

Az alkalmazások a Csatolt SSO beállítással adhatók hozzá. Konfigurálhat egy alkalmazáscsempét, amely a meglévő webalkalmazás URL-címére mutat. A csatolt SSO lehetővé teszi, hogy anélkül indítsa el a felhasználók Saját alkalmazások portálra, hogy az összes alkalmazást átküldi az Azure AD SSO-ba. Fokozatosan áttérhet az Azure AD SSO-val konfigurált alkalmazásokra a felhasználói élmény megzavarása nélkül.

## <a name="plan-the-user-experience"></a>A felhasználói élmény megterve

Alapértelmezés szerint minden alkalmazás, amelyhez a felhasználó hozzáfér, és az önkiszolgáló felderítéshez konfigurált összes alkalmazás megjelenik a felhasználó Saját alkalmazások panelen. Sok szervezet számára ez egy nagyon nagy lista lehet, amely nehézkessé válhat, ha nincs szervezve

### <a name="plan-my-apps-collections"></a>A Saját alkalmazások megterve

Minden olyan Azure AD-alkalmazás, amelyhez a felhasználó hozzáféréssel rendelkezik, megjelenik Saját alkalmazások Minden alkalmazás gyűjteményben. Gyűjtemények használatával csoportosítja a kapcsolódó alkalmazásokat, és egy külön lapon mutatja be őket, így könnyebben megtalálja őket. Gyűjtemények használatával például alkalmazások logikai csoportosítását hozhatja létre adott feladatszerepk számára, tevékenységekhez, projektekhez stb. 

A végfelhasználók a következővel is testre szabhatják a felhasználói élményt:

* Saját alkalmazásgyűjtemények létrehozása.

* [Alkalmazásgyűjtemények elrejtése és átrendezésük.](access-panel-collections.md)

![Képernyőkép az önkiszolgáló konfigurációról](./media/my-apps-deployment-plan/collections.png)

Lehetőség van arra, hogy elrejtse az alkalmazásokat a Saját alkalmazások portálról, miközben továbbra is engedélyezi a hozzáférést más helyekről, például a Microsoft 365 portálról. További információ: Alkalmazás elrejtése a felhasználói élmény elől a [Azure Active Directory.](hide-application-from-user-portal.md)

> [!IMPORTANT]
> A felhasználók csak 950 alkalmazáshoz férhetnek hozzá a Saját alkalmazások. Ide tartoznak a felhasználó vagy a rendszergazda által rejtett alkalmazások is. 

### <a name="plan-self-service-group-management-membership"></a>Önkiszolgáló csoportkezelési tagság megterve

Engedélyezheti a felhasználóknak, hogy saját biztonsági csoportokat vagy Microsoft 365 azure AD-ban hozzanak létre és kezeljenek. A csoport tulajdonosa jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság vezérlését. Az önkiszolgáló csoportkezelési funkciók nem érhetők el levelezési biztonsági csoportokhoz vagy terjesztési listákhoz.

Az önkiszolgáló csoporttagság tervezéséhez határozza meg, hogy engedélyezi-e, hogy a szervezetben minden felhasználó csoportokat hozzon létre és kezeljen, vagy csak a felhasználók egy részcsoportját. Ha a felhasználók egy részhalmazát engedélyezi, be kell állítania egy csoportot, amelyhez hozzáadja ezeket a személyeket. 

Az ilyen forgatókönyvek engedélyezésével kapcsolatos részletekért tekintse meg az önkiszolgáló csoportkezelés beállítását a Azure Active Directory a következőben: Set [up self-service group management in Azure Active Directory.](../enterprise-users/groups-self-service-management.md)

### <a name="plan-self-service-application-access"></a>Önkiszolgáló alkalmazás-hozzáférés megterve

A felhasználók a panelen engedélyezhetik, hogy a felhasználók alkalmazásokat derítsnek fel és Saját alkalmazások hozzáférést. Ezt először önnek kell megtennie 

* önkiszolgáló csoportkezelés engedélyezése

* alkalmazás engedélyezése az SSO számára

* csoport létrehozása az alkalmazás-hozzáféréshez

![Képernyőfelvétel az Saját alkalmazások konfigurálásról](./media/my-apps-deployment-plan/my-apps-self-service.png)

Amikor a felhasználók hozzáférést kérnek, hozzáférést kérnek a mögöttes csoporthoz, és a csoporttulajdonosok engedélyt kaphatnak a csoporttagság és így az alkalmazás-hozzáférés kezelésére. Az alkalmazásokhoz való hozzáféréshez jóváhagyási munkafolyamatok érhetők el explicit jóváhagyásra. A jóváhagyó felhasználók értesítést kapnak a Saját alkalmazások portálon, ha az alkalmazáshoz való hozzáférésre vonatkozó kérelem van függőben.

## <a name="plan-reporting-and-auditing"></a>Jelentéskészítés és naplózás megtervezése

Az Azure AD [műszaki és üzleti elemzéseket lehetővé tő jelentéseket] biztosít. /reports-monitoring/overview-reports.md). Működjön együtt az üzleti és technikai alkalmazások tulajdonosaival, hogy felvezék a jelentések tulajdonjogát, és rendszeresen használják őket. Az alábbi táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre.

| Példa| A kockázatok kezelése| A hatékonyság növelése| Cégirányítás és megfelelőség |
| - | - | - | -|
| Jelentéstípusok| Alkalmazásengedélyek és használat| Fiók-kiépítési tevékenység| Az alkalmazásokhoz hozzáférő felhasználók áttekintése |
| Lehetséges műveletek| Hozzáférés naplózása; engedélyek visszavonása| Kiépítési hibák javítása| Hozzáférés visszavonása |


Az Azure AD 30 napig őrzi meg a legtöbb naplózási adatot. Az adatok az Azure felügyeleti portálon vagy API-n keresztül érhetők el, és letölthetők az elemzési rendszerekbe.

#### <a name="auditing"></a>Naplózás

Az alkalmazás-hozzáférés auditnaplói 30 napig érhetők el. Ha a szervezet hosszabb megőrzést igényel, exportálja a naplókat egy security information event and management (SIEM) eszközbe, például a Splunkba vagy az ArcSightba.

A naplózási, jelentéskészítési és vészhelyreállítási biztonsági mentések esetén dokumentálja a letöltés szükséges gyakoriságát, a célrendszert, és hogy ki felelős az egyes biztonsági mentések kezeléséért. Előfordulhat, hogy nincs szükség külön naplózásra és biztonsági mentések jelentésére. A vészhelyreállítási biztonsági mentésnek egy különálló entitásnak kell lennie.

## <a name="validate-your-deployment"></a>Az üzemelő példány ellenőrzése

Győződjön meg Saját alkalmazások, hogy az üzembe helyezés alapos tesztelésen és visszaállítási terven van.

Végezze el a következő teszteket a vállalati és a személyes eszközökkel is. Ezeknek a tesztelési eseteknek az üzleti felhasználást is tükrözniük kell. Az alábbiakban néhány, a tipikus technikai forgatókönyveken alapuló esetet követünk. Adjon hozzá az igényeinek megfelelőket.

#### <a name="application-sso-access-test-case-examples"></a>Példák az alkalmazás-SSO-hozzáférés tesztesetére:


| Üzleti eset| Várt eredmény |
| - | - |
| A felhasználó bejelentkezik a Saját alkalmazások portálra| A felhasználó bejelentkezhet, és láthatja az alkalmazásait |
| A felhasználó elindít egy összevont SSO-alkalmazást| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó először indít el egy jelszó-SSO-alkalmazást| A felhasználónak telepítenie kell a Saját alkalmazások bővítményt |
| A felhasználó a következő alkalommal indít el egy jelszó-SSO-alkalmazást| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó elindít egy alkalmazást a Microsoft 365 portálról| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó elindít egy alkalmazást a Managed Browser| A felhasználó automatikusan bejelentkezik az alkalmazásba |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Példák az alkalmazás önkiszolgáló képességeinek tesztelésére


| Üzleti eset| Várt eredmény |
| - | - |
| A felhasználó kezelheti az alkalmazás tagságát| A felhasználó hozzáadhat vagy eltávolíthat olyan tagokat, akik hozzáféréssel rendelkezik az alkalmazáshoz |
| A felhasználó szerkesztheti az alkalmazást| A felhasználó szerkesztheti az alkalmazás leírását és hitelesítő adatait a jelszó-SSO-alkalmazásokhoz |


### <a name="rollback-steps"></a>Visszaállítási lépések

Fontos, hogy megtervezze, mi a feladat, ha az üzembe helyezés nem a terv szerint működik. Ha az SSO konfigurálása sikertelen az üzembe helyezés során, tisztában kell lennie az [SSO-problémák](../hybrid/tshoot-connect-sso.md) elhárításának és a felhasználókra gyakorolt hatásának csökkentésével. Szélsőséges körülmények között szükség lehet az [SSO visszaállításra.](plan-sso-deployment.md)

## <a name="manage-your-implementation"></a>A megvalósítás kezelése

A legkevesebb kiemelt szerepkör használatával elvégez egy szükséges feladatot a Azure Active Directory. [Tekintse át a rendelkezésre álló szerepköröket,](../roles/permissions-reference.md) és válassza ki a megfelelőt az alkalmazás minden egyes személyre vonatkozó igényeinek megoldásához. Előfordulhat, hogy egyes szerepköröket ideiglenesen kell alkalmazni, és az üzembe helyezés befejezése után el kell távolítani őket.

| Szerepek| Szerepkörök| Azure AD-szerepkör |
| - | - | - |
| Helpdesk admin| 1. rétegbeli támogatás| Nincsenek |
| Identitás-rendszergazda| Konfigurálás és hibakeresés, ha problémák befolyásolják az Azure AD-t| Globális rendszergazda |
| Alkalmazás-rendszergazda| Felhasználói igazolás az alkalmazásban, konfigurálás engedélyekkel rendelkező felhasználókon| Nincsenek |
| Infrastruktúra-rendszergazdák| Tanúsítványváltás tulajdonosa| Globális rendszergazda |
| Üzlettulajdonos/érintett| Felhasználói igazolás az alkalmazásban, konfigurálás engedélyekkel rendelkező felhasználókon| Nincsenek |


A [címtár-Privileged Identity Management](../privileged-identity-management/pim-configure.md) kezelheti a szerepköröket, hogy további naplózási, vezérlési és hozzáférési felülvizsgálatot biztosítson a címtárengedélyekkel rendelkező felhasználók számára.

## <a name="next-steps"></a>Következő lépések

[Az Azure AD Multi-Factor Authentication üzembe helyezésének megterve](../authentication/howto-mfa-getstarted.md)

[Alkalmazásproxy üzembe helyezésének tervezése](application-proxy-deployment-plan.md)

