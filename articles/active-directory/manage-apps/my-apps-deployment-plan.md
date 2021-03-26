---
title: Az alkalmazások konfigurációjának megtervezése Azure Active Directory
description: Tervezési útmutató a saját alkalmazások hatékony használatához a szervezetben.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 5184639d8c34be705aeeb691f1cf38486f850673
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543958"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Az alkalmazások konfigurációjának megtervezése Azure Active Directory

> [!NOTE]
> Ez a cikk olyan informatikai szakemberek számára készült, akiknek meg kell tervezniük a szervezetem saját alkalmazások portáljának konfigurációját. 
>
> **A végfelhasználói dokumentációért lásd: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md)**.

Azure Active Directory (Azure AD) saját alkalmazások webalapú portál alkalmazások indításához és kezeléséhez. A saját alkalmazások lap egyetlen helyet biztosít a felhasználóknak a munkájuk elindításához és az összes olyan alkalmazás megkereséséhez, amelyhez hozzáféréssel rendelkeznek. A felhasználók a következő címen férhetnek hozzá az alkalmazásokhoz: [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>A saját alkalmazások konfigurálása

A My apps portál alapértelmezés szerint elérhető a felhasználók számára, és nem kapcsolható ki. Fontos, hogy úgy konfigurálja, hogy a lehető legjobb felhasználói élményt használják, és a portál hasznos maradjon. 

Ha a következő feltételek mindegyike teljesül, a Azure Active Directory vállalati alkalmazások listájában szereplő bármely alkalmazás megjelenik:

* Az alkalmazás láthatósági tulajdonsága igaz értékre van állítva. 

* Az alkalmazás minden felhasználóhoz vagy csoporthoz hozzá van rendelve. Megjelenik a hozzárendelt felhasználók számára.

A portál konfigurálása biztosítja, hogy a megfelelő személyek könnyen megtalálják a megfelelő alkalmazásokat.

 
### <a name="how-is-the-my-apps-portal-used"></a>Hogyan használják a My apps portált?

A felhasználók a saját alkalmazások portálon érhetik el a következőket:

* Felderítheti és elérheti a szervezete Azure AD-hez csatlakoztatott alkalmazásait, amelyhez hozzáféréssel rendelkezik.

   * Javasoljuk, hogy az alkalmazások egyszeri bejelentkezésre (SSO) legyenek konfigurálva, hogy a felhasználók a lehető legjobb élményt nyújtsanak.

* Hozzáférés kérése az önkiszolgáló szolgáltatáshoz konfigurált új alkalmazásokhoz.

* Személyes gyűjteményeket hozhat létre.

* Kezelheti mások számára az alkalmazásokhoz való hozzáférést, ha a csoport tulajdonosának vagy delegált vezérlőnek a szerepkörét rendeli hozzá az alkalmazás (ok) hoz való hozzáférés biztosításához.

A rendszergazdák a következőket konfigurálhatják:

* Belefoglalási feltételek [, beleértve a](../manage-apps/configure-user-consent.md) szolgáltatási feltételeket.

* [Önkiszolgáló alkalmazás-felderítési és hozzáférési kérelmek](../manage-apps/access-panel-manage-self-service-access.md).

* [Alkalmazások gyűjteményei](../manage-apps/access-panel-collections.md).

* Ikonok hozzárendelése alkalmazásokhoz

* Alkalmazások felhasználóbarát nevei

* Az alkalmazásokban látható vállalati arculat

 

## <a name="plan-consent-configuration"></a>A engedélyezési konfiguráció megtervezése

### <a name="user-consent-for-applications"></a>Felhasználói beleegyező alkalmazások 

Ahhoz, hogy egy felhasználó be tudja jelentkezni egy alkalmazásba, és az alkalmazás hozzáférhessen a szervezet adataihoz, egy felhasználónak vagy rendszergazdának meg kell adnia az alkalmazás engedélyeit. Megadhatja, hogy engedélyezett-e a felhasználói beleegyezettség, és hogy milyen feltételek mellett. **A Microsoft azt javasolja, hogy csak az ellenőrzött közzétevők alkalmazásai számára engedélyezze a felhasználói jóváhagyást.**

További információ: a [végfelhasználók beleegyezett az alkalmazásokkal való megadásának beállítása](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Az adatokhoz hozzáférő alkalmazások csoportjának tulajdonosi engedélye

A csoport-és csapat tulajdonosai engedélyezhetik az alkalmazások, például a külső gyártók által közzétett alkalmazások számára, hogy hozzáférjenek a szervezet adataihoz a csoporthoz társítva. További információ: [erőforrás-specifikus beleegyezett a Microsoft Teams-ben](https://docs.microsoft.com/microsoftteams/resource-specific-consent) . 

Beállíthatja, hogy engedélyezi vagy letiltja ezt a funkciót.

További információ: csoporttagság- [engedélyezési engedélyek konfigurálása](../manage-apps/configure-user-consent-groups.md).

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon tájékoztathatja a felhasználókat arról, hogy hogyan és Mikor változnak, és hogyan szerezhet támogatást, ha szükséges.

Bár az alkalmazások általában nem hoznak létre felhasználói problémákat, fontos, hogy előkészítse őket. Hozzon létre útmutatókat és a támogatási személyzet összes erőforrását az elindítása előtt.

#### <a name="communications-templates"></a>Kommunikációs sablonok

A Microsoft [testreszabható sablonokat biztosít az e-mailekhez és](https://aka.ms/APTemplates) az alkalmazásokhoz kapcsolódó egyéb kommunikációhoz. Ezeket az eszközöket más kommunikációs csatornákon is használhatja a vállalati kulturális környezetnek megfelelően.

 

## <a name="plan-your-sso-configuration"></a>Az egyszeri bejelentkezés konfigurációjának megtervezése

A legjobb megoldás, ha az egyszeri bejelentkezés engedélyezve van a saját alkalmazások portálon található összes alkalmazáshoz, így a felhasználók zökkenőmentesen használhatják a hitelesítő adatok megadása nélkül.

Az Azure AD több SSO-beállítást is támogat. 

* További információ: [az egyszeri bejelentkezés lehetőségei az Azure ad-ben](sso-options.md).

* Ha többet szeretne megtudni az Azure AD-alkalmazás identitás-szolgáltatóként való használatáról az alkalmazáshoz, tekintse meg a gyors üzembe helyezési [sorozatot az alkalmazások kezelésében](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Összevont egyszeri bejelentkezés használata, ha lehetséges

A saját alkalmazások oldal legjobb felhasználói élményéhez kezdje az összevont egyszeri bejelentkezés (OpenID Connect vagy SAML) által elérhető felhőalapú alkalmazások integrálásával. Az összevont egyszeri bejelentkezés lehetővé teszi a felhasználók számára, hogy egységes, egykattintásos élményt biztosítsanak az alkalmazások indítási felületei között, és a konfiguráció-ellenőrzés során egyre erőteljesebbek.

További információ a szolgáltatott szoftverek (SaaS) egyszeri bejelentkezéshez való konfigurálásáról: [SaaS SSO üzembehelyezési terv].. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Speciális egyszeri bejelentkezési körülményekkel kapcsolatos megfontolások

> [!TIP]
> A jobb felhasználói élmény érdekében használja az összevont egyszeri bejelentkezést az Azure AD-vel (OpenID Connect/SAML), ha egy alkalmazás támogatja azt a jelszó-alapú egyszeri bejelentkezés és az ADFS helyett.

A jelszó-alapú egyszeri bejelentkezéses alkalmazásokhoz vagy az Azure AD Application Proxy által elért alkalmazásokhoz való bejelentkezéshez a felhasználóknak telepíteniük és használniuk kell a saját alkalmazások biztonságos bejelentkezési bővítményét. A rendszer felkéri a felhasználókat, hogy telepítse a bővítményt, amikor először elindítja a jelszó-alapú egyszeri bejelentkezés vagy alkalmazásproxy alkalmazást. 

![Képernyőfelvétel a](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

A bővítmény részletes ismertetését lásd: [saját alkalmazások böngésző-bővítmény telepítése](../user-help/my-apps-portal-end-user-access.md).

Ha integrálnia kell ezeket az alkalmazásokat, meg kell határoznia egy mechanizmust, amellyel a bővítményt a [támogatott böngészőkkel](../user-help/my-apps-portal-end-user-access.md)lehet méretezni. A lehetőségek a következők:

* [A Chrome, a Firefox, a Microsoft Edge vagy az IE felhasználó által vezérelt letöltése és konfigurálása](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager az Internet Explorerben](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

A bővítmény lehetővé teszi, hogy a felhasználók bármelyik alkalmazást elindítsák a keresősáv használatával, megkeressék a legutóbb használt alkalmazásokhoz való hozzáférést, és a saját alkalmazások lapra mutató hivatkozást is megkereshetnek.

![Képernyőkép a saját alkalmazások bővítmény kereséséről](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Mobil hozzáférés tervezése

A jelszó-alapú SSO-t használó vagy [Microsoft Azure ad alkalmazásproxy](../manage-apps/application-proxy.md)használatával elért alkalmazások esetén a Microsoft Edge Mobile használatát kell használnia. Más alkalmazások esetén bármilyen mobil böngésző használható. 

### <a name="linked-sso"></a>Csatolt egyszeri bejelentkezés

Az alkalmazások a csatolt egyszeri bejelentkezés lehetőség használatával adhatók hozzá. Beállíthat egy olyan alkalmazás-csempét, amely a meglévő webalkalmazás URL-címére hivatkozik. A csatolt egyszeri bejelentkezés lehetővé teszi, hogy a felhasználókat a saját alkalmazások portálra irányítsa anélkül, hogy az összes alkalmazást áttelepíti az Azure AD SSO-ba. Fokozatosan áthelyezheti az Azure AD SSO-ra konfigurált alkalmazásokat a felhasználói élmény megszakítása nélkül.

## <a name="plan-the-user-experience"></a>A felhasználói élmény megtervezése

Alapértelmezés szerint minden olyan alkalmazás, amelyhez a felhasználó hozzáfér, és az önkiszolgáló felderítéshez konfigurált összes alkalmazás megjelenik a felhasználó saját alkalmazások paneljén. Sok szervezet esetében ez lehet egy nagyon nagy lista, amely nehézkes lehet, ha nincs rendszerezve

### <a name="plan-my-apps-collections"></a>Alkalmazások gyűjteményének megtervezése

Minden Azure AD-alkalmazás, amelyhez a felhasználó hozzáfér, megjelenik az alkalmazásokban a minden alkalmazás gyűjteményben. Gyűjtemények használata a kapcsolódó alkalmazások csoportosításához és egy külön lapon való megjelenítéséhez, így könnyebben megtalálhatja őket. A gyűjtemények segítségével például létrehozhat logikai csoportosításokat az alkalmazások számára adott feladatok, feladatok, projektek és így tovább. 

A végfelhasználók a saját tapasztalataikat is testre szabhatják

* Saját alkalmazás-gyűjtemények létrehozása.

* [Alkalmazás-gyűjtemények elrejtése és átrendezése](access-panel-collections.md).

![Képernyőkép az önkiszolgáló konfigurációról](./media/my-apps-deployment-plan/collections.png)

Lehetőség van az alkalmazások elrejtésére a saját alkalmazások portálján, miközben más helyekről, például a Microsoft 365-portálról is engedélyezheti a hozzáférést. További információ: [alkalmazás elrejtése a felhasználók Azure Active Directoryban való felhasználói felületéről](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Az alkalmazásokban csak azok a 950-alkalmazások érhetők el, amelyekhez a felhasználó hozzáféréssel rendelkezik. Ide tartoznak a felhasználó vagy a rendszergazda által rejtett alkalmazások is. 

### <a name="plan-self-service-group-management-membership"></a>Önkiszolgáló csoport felügyeleti tagságának megtervezése

Lehetővé teheti a felhasználók számára, hogy saját biztonsági csoportokat vagy Microsoft 365 csoportokat hozzanak létre és kezeljenek az Azure AD-ben. A csoport tulajdonosa jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság vezérlését. Az önkiszolgáló csoport felügyeleti funkciói nem érhetők el levelezési biztonsági csoportokhoz vagy terjesztési listához.

Az önkiszolgáló csoporttagság megtervezéséhez döntse el, hogy engedélyezi-e a szervezet összes felhasználója számára, hogy csoportokat hozzon létre és kezeljen, vagy csak a felhasználók egy részhalmazát. Ha engedélyezi a felhasználók egy részhalmazát, be kell állítania egy csoportot, amelyhez hozzá kívánja adni a felhasználókat. 

A forgatókönyvek engedélyezésével kapcsolatban lásd: [az önkiszolgáló csoport felügyeletének beállítása a Azure Active Directoryban](../enterprise-users/groups-self-service-management.md) .

### <a name="plan-self-service-application-access"></a>Önkiszolgáló alkalmazás-hozzáférés megtervezése

Lehetővé teheti a felhasználók számára, hogy a saját alkalmazások paneljén keresztül hozzáférjenek az alkalmazásokhoz. Ehhez először meg kell adnia 

* önkiszolgáló csoportok felügyeletének engedélyezése

* alkalmazás engedélyezése egyszeri bejelentkezéshez

* Csoport létrehozása az alkalmazás-hozzáféréshez

![Képernyőfelvétel a saját alkalmazások önkiszolgáló konfigurációjával](./media/my-apps-deployment-plan/my-apps-self-service.png)

Ha a felhasználó hozzáférést kér, a rendszer hozzáférést kér az alapul szolgáló csoporthoz, és a csoport tulajdonosai engedélyt kaphatnak a csoporttagság kezelésére, és így az alkalmazások elérésére. A jóváhagyási munkafolyamatok az alkalmazások eléréséhez használhatók explicit jóváhagyásra. A jóváhagyó felhasználók a saját alkalmazások portálon kapnak értesítéseket, amikor az alkalmazáshoz való hozzáférésre vonatkozó kérelem van folyamatban.

## <a name="plan-reporting-and-auditing"></a>Jelentéskészítés és naplózás tervezése

Az Azure AD [olyan jelentéseket biztosít, amelyek technikai és üzleti ismereteket kínálnak]. /reports-monitoring/overview-reports.md). Az üzleti és műszaki alkalmazások tulajdonosai együttműködve vállalják a jelentések tulajdonjogát, és rendszeresen használják azokat. A következő táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre.

| Példa| A kockázatok kezelése| Termelékenység növelése| Cégirányítás és megfelelőség |
| - | - | - | -|
| Jelentéstípusok| Alkalmazás-engedélyek és-használat| Fiók létesítési tevékenysége| Tekintse át, ki fér hozzá az alkalmazásokhoz |
| Lehetséges műveletek| Hozzáférés naplózása; engedélyek visszavonása| Kiépítési hibák szervizelése| Hozzáférés visszavonása |


Az Azure AD a legtöbb naplózási adattal 30 napig tart. Az adatai az Azure felügyeleti portálon vagy API-n keresztül érhetők el az elemzési rendszerbe való letöltéshez.

#### <a name="auditing"></a>Naplózás

Az alkalmazás-hozzáférés naplófájljai 30 napig érhetők el. Ha a szervezete nagyobb adatmegőrzést igényel, exportálja a naplókat egy biztonsági információs esemény és felügyelet (SIEM) eszközre, például splunk vagy ArcSight.

Naplózási, jelentéskészítési és vész-helyreállítási biztonsági másolatok esetén dokumentálja a letöltés szükséges gyakoriságát, a megcélzott rendszerét, valamint az egyes biztonsági másolatok kezeléséért felelős személyeket. Előfordulhat, hogy nincs szüksége külön naplózási és jelentéskészítési biztonsági másolatokra. A vész-helyreállítási biztonsági mentésnek különálló entitásnak kell lennie.

## <a name="validate-your-deployment"></a>Az üzemelő példány ellenőrzése

Győződjön meg arról, hogy a saját alkalmazások üzembe helyezését alaposan tesztelték, és a visszaállítási terv van érvényben.

A következő teszteket a vállalati tulajdonú eszközökkel és a személyes eszközökkel is elvégezheti. Ezeknek a tesztelési eseteknek az üzleti használati eseteit is tükröznie kell. A következő néhány eset a tipikus technikai forgatókönyvek alapján. Adja hozzá az igényeinek megfelelő egyéb szolgáltatásokat.

#### <a name="application-sso-access-test-case-examples"></a>Alkalmazás egyszeri bejelentkezéses hozzáférésének tesztelésére vonatkozó példák:


| Üzleti eset| Várt eredmény |
| - | - |
| A felhasználó bejelentkezik a saját alkalmazások portálra| A felhasználó bejelentkezhet, és megtekintheti az alkalmazásaikat |
| A felhasználó egy összevont SSO-alkalmazást indít el| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó első alkalommal indítja el a jelszó-SSO-alkalmazást| A felhasználónak telepítenie kell a saját alkalmazások bővítményt |
| A felhasználó a következő alkalommal indít el egy jelszó-SSO-alkalmazást| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó elindít egy alkalmazást Microsoft 365 portálról| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó elindít egy alkalmazást a Managed Browser| A felhasználó automatikusan bejelentkezik az alkalmazásba |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Alkalmazások önkiszolgáló képességei – tesztelési példák


| Üzleti eset| Várt eredmény |
| - | - |
| A felhasználó kezelheti a tagságot az alkalmazásban| A felhasználó hozzáadhat vagy eltávolíthat olyan tagokat, akik hozzáféréssel rendelkeznek az alkalmazáshoz |
| A felhasználó szerkesztheti az alkalmazást| A felhasználó szerkesztheti az alkalmazás leírását és a jelszó egyszeri bejelentkezéses alkalmazásaihoz tartozó hitelesítő adatokat. |


### <a name="rollback-steps"></a>Visszaállítási lépések

Fontos megtervezni, hogy mi a teendő, ha a központi telepítés nem a tervezett módon történik. Ha az egyszeri bejelentkezés konfigurálása nem sikerül az üzembe helyezés során, meg kell ismernie az [egyszeri bejelentkezés hibáinak elhárítását](../hybrid/tshoot-connect-sso.md) és a felhasználókra gyakorolt hatás csökkentését. Szélsőséges körülmények között előfordulhat, hogy vissza kell [állítania az egyszeri bejelentkezést](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>A megvalósítás kezelése

A legkevesebb Kiemelt szerepkörrel hajtson végre egy szükséges feladatot a Azure Active Directoryon belül. [Tekintse át az elérhető különböző szerepköröket](../roles/permissions-reference.md) , és válassza ki a megfelelőt az alkalmazáshoz tartozó egyes personák igényeinek megoldásához. Előfordulhat, hogy bizonyos szerepköröket átmenetileg kell alkalmazni, és el kell távolítani az üzembe helyezés befejeződése után.

| Szerepek| Szerepkörök| Azure AD-szerepkör |
| - | - | - |
| Segélyszolgálat rendszergazdája| 1. szintű támogatás| Nincsenek |
| Identity admin| Konfigurálás és hibakeresés az Azure AD-t érintő problémák esetén| Globális rendszergazda |
| Alkalmazás-rendszergazda| Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja| Nincsenek |
| Infrastruktúra-rendszergazdák| Tanúsítvány-átváltási tulajdonos| Globális rendszergazda |
| Üzleti tulajdonos/érintett fél| Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja| Nincsenek |


A [Privileged Identity Management](../privileged-identity-management/pim-configure.md) segítségével kezelheti a szerepköröket, hogy további naplózási, vezérlési és hozzáférési felülvizsgálatot biztosítson a címtár-jogosultságokkal rendelkező felhasználók számára.

## <a name="next-steps"></a>Következő lépések

[Az Azure AD-Multi-Factor Authentication üzembe helyezésének megtervezése](../authentication/howto-mfa-getstarted.md)

[Alkalmazásproxy üzembe helyezésének tervezése](application-proxy-deployment-plan.md)

