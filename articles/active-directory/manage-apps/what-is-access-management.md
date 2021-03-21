---
title: Az alkalmazásokhoz való hozzáférés kezelése az Azure AD-vel
description: Leírja, hogy Azure Active Directory lehetővé teszi a szervezetek számára, hogy megadják azokat az alkalmazásokat, amelyekhez az egyes felhasználók hozzáférnek.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: 5afc6aa8f52011eba6d7cfdfaa09b0ab995183e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257423"
---
# <a name="managing-access-to-apps"></a>Alkalmazásokhoz való hozzáférés kezelése

A folyamatos hozzáférés-kezelés, a használat kiértékelése és a jelentéskészítés továbbra is kihívást jelent, miután az alkalmazás integrálva van a szervezet identitási rendszerébe. Sok esetben a rendszergazdáknak vagy az ügyfélszolgálatnak folyamatosan aktív szerepkört kell betartania az alkalmazásaihoz való hozzáférés kezelésében. Időnként a hozzárendelést egy általános vagy egy részlegi informatikai csapat végzi. A hozzárendelési döntés gyakran arra szolgál, hogy az üzleti döntéshozónak legyen delegálva, mielőtt jóváhagyja a hozzárendelést.  Más szervezetek egy meglévő automatizált identitás-és hozzáférés-kezelési rendszerrel (például Role-Based Access Control (RBAC) vagy Attribute-Based Access Control (ABAC) való integrációt fektetnek be. Az integráció és a szabályok fejlesztése is általában specializált és költséges. A felügyeleti megközelítés figyelése vagy jelentése a saját külön, költséges és összetett befektetés.

## <a name="how-does-azure-active-directory-help"></a>Hogyan segít a Azure Active Directory?

Az Azure AD széleskörű hozzáférés-vezérlést biztosít a konfigurált alkalmazásokhoz, így lehetővé teszi a szervezetek számára, hogy az automatikus, attribútum-alapú hozzárendeléstől (ABAC vagy RBAC forgatókönyvek) kezdve egyszerűen elérjék a megfelelő hozzáférési házirendeket a delegáláson keresztül, beleértve a rendszergazdák felügyeletét. Az Azure AD segítségével könnyedén, összetett házirendeket hozhat létre, több felügyeleti modellt is használhat egyetlen alkalmazáshoz, és a felügyeleti szabályokat is újra felhasználhatja az alkalmazások között ugyanazzal a célközönséggel.

Az Azure AD-vel a használati és a hozzárendelési jelentéskészítés teljes mértékben integrálva van, így a rendszergazdák egyszerűen jelenthetik be a hozzárendelési állapotot, a hozzárendelési hibákat és a használatot is.

### <a name="assigning-users-and-groups-to-an-app"></a>Felhasználók és csoportok társítása egy alkalmazáshoz

Az Azure AD alkalmazás-hozzárendelése két elsődleges hozzárendelési módra összpontosít:

* **Egyéni hozzárendelés** A globális rendszergazdai jogosultságokkal rendelkező informatikai rendszergazdák kijelölhetik az egyes felhasználói fiókokat, és hozzáférést biztosíthatnak az alkalmazáshoz.

* **Csoport alapú hozzárendelés (prémium szintű Azure ad P1 vagy P2 szükséges)** A globális rendszergazdai jogosultságokkal rendelkező informatikai rendszergazdák hozzárendelhet egy csoportot az alkalmazáshoz. Az adott felhasználók hozzáférését az határozza meg, hogy a csoport tagjai-e, amikor megpróbálják elérni az alkalmazást. Más szóval a rendszergazda gyakorlatilag létrehozhat egy hozzárendelési szabályt, amely "a hozzárendelt csoport bármely aktuális tagja hozzáfér az alkalmazáshoz". Ezzel a hozzárendelési lehetőséggel a rendszergazdák az Azure AD csoportos felügyeleti lehetőségei közül bármelyiket kihasználhatják, beleértve az [attribútum-alapú dinamikus csoportokat](../fundamentals/active-directory-groups-create-azure-portal.md), a külső rendszercsoportokat (például a helyszíni Active Directory vagy a munkanapokat), vagy a rendszergazda által felügyelt vagy önkiszolgáló által felügyelt csoportokat. Egyetlen csoport egyszerűen hozzárendelhető több alkalmazáshoz, így biztosíthatja, hogy a hozzárendelési affinitással rendelkező alkalmazások megosszák a hozzárendelési szabályokat, így csökkentve az általános felügyelet bonyolultságát. Vegye figyelembe, hogy a beágyazott csoporttagságok nem támogatottak a csoport alapú hozzárendelések esetében.

A két hozzárendelési mód használatával a rendszergazdák bármilyen kívánt hozzárendelés-kezelési módszert érhetnek el.

### <a name="requiring-user-assignment-for-an-app"></a>Felhasználók hozzárendelésének megkövetelése egy alkalmazáshoz

Bizonyos típusú alkalmazások esetében lehetősége van arra, hogy az [alkalmazáshoz rendelje a felhasználókat](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Ezzel megakadályozza, hogy mindenki bejelentkezzen, kivéve azokat a felhasználókat, akiket kifejezetten az alkalmazáshoz rendeltek. A következő típusú alkalmazások támogatják ezt a lehetőséget:

* Az összevont egyszeri bejelentkezéshez (SSO) SAML-alapú hitelesítéssel konfigurált alkalmazások
* Azure Active Directory előhitelesítést használó alkalmazásproxy-alkalmazások
* Az Azure AD Application platformra épülő, OAuth 2,0/OpenID Connect hitelesítést használó alkalmazások, miután egy felhasználó vagy rendszergazda beleegyezett az adott alkalmazásba. Bizonyos vállalati alkalmazások további ellenőrzés alatt állnak, akik számára engedélyezett a bejelentkezés.

Ha a felhasználó-hozzárendelés *nem szükséges*, a nem hozzárendelt felhasználók nem látják az alkalmazást a saját alkalmazásaikban, de továbbra is bejelentkezhetnek az alkalmazásba (más néven az SP által kezdeményezett bejelentkezés), vagy használhatják a **felhasználói hozzáférési URL-címet** az alkalmazás **Tulajdonságok** lapján (más néven identitásszolgáltató-alapú bejelentkezés).

Egyes alkalmazások esetében a felhasználói hozzárendelés megkövetelésének lehetősége nem érhető el az alkalmazás tulajdonságaiban. Ezekben az esetekben a PowerShell használatával állíthatja be a appRoleAssignmentRequired tulajdonságot az egyszerű szolgáltatásnév számára.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Az alkalmazások elérésének felhasználói élményének meghatározása

Az Azure AD [számos testreszabható módszert](end-user-experiences.md) biztosít az alkalmazások végfelhasználók számára történő központi telepítéséhez a szervezeten belül:

* Azure AD-alkalmazások
* Microsoft 365 Application Launcher
* Közvetlen bejelentkezés az összevont alkalmazásokba (Service-PR)
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Megtudhatja, hogy a vállalati alkalmazásokhoz rendelt felhasználók láthatják-e az alkalmazásokban és Microsoft 365 az Application launcherben.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Példa: összetett alkalmazás-hozzárendelés az Azure AD-vel
Vegyünk egy olyan alkalmazást, mint például a Salesforce. Számos szervezetben a Salesforce elsődlegesen a marketing-és értékesítési csapatok használják. Gyakran a marketing csapat tagjai magas jogosultsági szintű hozzáféréssel rendelkeznek a Salesforce, míg az értékesítési csapat tagjai korlátozott hozzáféréssel rendelkeznek. Sok esetben az információkkal dolgozó szakemberek széles köre korlátozott hozzáféréssel rendelkezik az alkalmazáshoz. Az ezekre a szabályokra vonatkozó kivételek bonyolítják az ügyet. Gyakran a marketing-vagy értékesítési vezető csapatok előjoga, hogy felhasználói hozzáférést biztosítson, vagy a szerepköröket az általános szabályoktól függetlenül módosítsa.

Az Azure AD-ben az olyan alkalmazások, mint a Salesforce, előre konfigurálhatók az egyszeri bejelentkezés (SSO) és az automatikus kiépítés számára. Az alkalmazás konfigurálása után a rendszergazda a megfelelő csoportok létrehozásához és hozzárendeléséhez igénybe vehet egy egyszeri műveletet. Ebben a példában a rendszergazda a következő hozzárendeléseket hajthatja végre:

* A [dinamikus csoportok](../fundamentals/active-directory-groups-create-azure-portal.md) úgy határozhatók meg, hogy automatikusan képviseljék a marketing-és értékesítési csapatok összes tagját olyan attribútumok használatával, mint például az osztály vagy a szerepkör:
  
  * A marketing-csoportok összes tagja hozzá lesz rendelve a "marketing" szerepkörhöz a Salesforce-ben
  * Az értékesítési csoportok összes tagja hozzá lesz rendelve az "értékesítés" szerepkörhöz a Salesforce-ben. A további finomítások több olyan csoportot is használhatnak, amelyek a különböző Salesforce-szerepkörökhöz rendelt regionális értékesítési csapatokat képviselik.

* A kivételi mechanizmus engedélyezéséhez létrehozható egy önkiszolgáló csoport az egyes szerepkörökhöz. Például a "Salesforce marketing Exception" csoport önkiszolgáló csoportként hozható létre. A csoport hozzárendelhető a Salesforce marketing szerepkörhöz, és a marketing Leadership csapata tulajdonosként is elvégezhető. A marketing Leadership csapat tagjai hozzáadhatnak vagy eltávolíthatnak felhasználókat, beállíthat egy csatlakozási szabályzatot, vagy akár jóváhagyhatja vagy megtagadhatja az egyes felhasználói kérések összekapcsolását. Ezt a mechanizmust olyan információkkal dolgozó szakemberek támogatják, akik nem igényelnek speciális képzést a tulajdonosok vagy a tagok számára.

Ebben az esetben az összes hozzárendelt felhasználó automatikusan a Salesforce lesz kiépítve, mivel azokat a különböző csoportokhoz adja hozzá a szerepkör-hozzárendelésük a Salesforce-ben való frissítésekor. A felhasználók felfedezhetik és érhetik el a Salesforce a saját alkalmazások, az Office-webügyfelek vagy akár a szervezeti Salesforce bejelentkezési oldalán. A rendszergazdák könnyedén megtekinthetik a használati és hozzárendelési állapotot az Azure AD jelentéskészítési funkciójával.

A rendszergazdák az [Azure ad feltételes hozzáférést](../conditional-access/concept-conditional-access-users-groups.md) alkalmazhatják a hozzáférési szabályzatok meghatározott szerepkörökhöz való beállításához. Ezek a szabályzatok magukban foglalhatják, hogy a hozzáférés engedélyezett-e a vállalati környezeten kívül, és még Multi-Factor Authentication vagy az eszközre vonatkozó követelmények a hozzáférés különböző esetekben való eléréséhez

## <a name="access-to-microsoft-applications"></a>Hozzáférés a Microsoft-alkalmazásokhoz

A Microsoft-alkalmazások (például az Exchange, a SharePoint, a Yammer stb.) a harmadik féltől származó SaaS-alkalmazásokkal vagy más, az Azure AD-vel integrált egyszeri bejelentkezéshez használt alkalmazásokkal vannak társítva és kezelve.

A felhasználók három fő módon érhetik el a Microsoft által közzétett alkalmazásokat.

- A Microsoft 365 vagy más fizetős csomag alkalmazásaiban a felhasználók közvetlenül a felhasználói fiókjához vagy a csoport alapú licenc-hozzárendelési képességgel rendelkező csoporton keresztül kapnak hozzáférést a **licenc-hozzárendelésen** keresztül.
- Azon alkalmazások esetében, amelyeket a Microsoft vagy harmadik fél szabadon tesz közzé mindenki számára, a felhasználók a [felhasználói engedélyeken](configure-user-consent.md)keresztül kaphatnak hozzáférést. Ez azt jelenti, hogy az alkalmazásba bejelentkeznek az Azure AD munkahelyi vagy iskolai fiókjával, és lehetővé teszik, hogy a fiókja korlátozott mennyiségű adathalmazhoz hozzáférjen.
- Az olyan alkalmazások esetében, amelyeket a Microsoft vagy harmadik fél szabadon tesz közzé mindenki számára, a felhasználók [rendszergazdai engedélyen](manage-consent-requests.md)keresztül is kaphatnak hozzáférést. Ez azt jelenti, hogy a rendszergazda azt állapította meg, hogy az alkalmazást mindenki használhatja a szervezeten belül, így egy globális rendszergazdai fiókkal jelentkezik be az alkalmazásba, és hozzáférést biztosít mindenki számára a szervezeten belül.

Egyes alkalmazások ezeket a metódusokat kombinálják. Bizonyos Microsoft-alkalmazások például egy Microsoft 365-előfizetés részét képezik, de továbbra is beleegyezik.

A felhasználók Office 365-portálon keresztül férhetnek hozzá Microsoft 365 alkalmazásokhoz. Megtekintheti vagy elrejtheti Microsoft 365 alkalmazásait a saját alkalmazások között az [Office 365 láthatósági kapcsolóval](hide-application-from-user-portal.md) a címtár **felhasználói beállításaiban**. 

A vállalati alkalmazásokhoz hasonlóan a felhasználók bizonyos Microsoft-alkalmazásokhoz is [hozzárendelhetők](assign-user-or-group-access-portal.md) a Azure Portalon keresztül, vagy ha a portál lehetőség nem érhető el, a PowerShell használatával.

## <a name="next-steps"></a>Következő lépések
* [Alkalmazások védelme feltételes hozzáféréssel](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Önkiszolgáló csoport kezelése/SSAA](../enterprise-users/groups-self-service-management.md)
