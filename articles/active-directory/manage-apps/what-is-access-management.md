---
title: Alkalmazásokhoz való hozzáférés kezelése az Azure AD használatával
description: Ez a Azure Active Directory teszi lehetővé a szervezetek számára azon alkalmazások megadását, amelyekhez az egyes felhasználók hozzáféréssel rendelkezik.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: iangithinji
ms.openlocfilehash: 6a1ae7dd1da2c7666c007194bf22bd980e41dc22
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376375"
---
# <a name="managing-access-to-apps"></a>Alkalmazásokhoz való hozzáférés kezelése

A folyamatos hozzáférés-kezelés, használatértékelés és jelentéskészítés továbbra is kihívást jelent, miután az alkalmazás integrálva van a szervezet identitásrendszerébe. Sok esetben a rendszergazdáknak vagy a segítő szolgálatnak folyamatosan aktív szerepet kell betöltötte az alkalmazásokhoz való hozzáférés kezelésében. Előfordulhat, hogy a hozzárendelést egy általános vagy egy részlegen dolgozó itatikus végzi. A hozzárendelési döntést gyakran az üzleti döntéshozónak kell delegálni, amelyhez jóváhagyásra van szükség, mielőtt az it-rendszergazdák meghozták volna a hozzárendelést.  Más szervezetek befektetnek egy meglévő automatizált identitás- és hozzáférés-kezelési rendszerbe, például Role-Based Access Control (RBAC) vagy Attribute-Based Access Control (ABAC). Az integráció és a szabályfejlesztés is általában specializált és költséges. A felügyeleti megközelítés monitorozása vagy jelentése saját különálló, költséges és összetett befektetés.

## <a name="how-does-azure-active-directory-help"></a>Hogyan Azure Active Directory ez?

Az Azure AD támogatja a konfigurált alkalmazások széles körű hozzáférés-kezelését, így a szervezetek könnyedén elérhetik a megfelelő hozzáférési szabályzatokat az automatikus, attribútumalapú hozzárendeléstől (ABAC- vagy RBAC-forgatókönyvektől) a delegáláson és a rendszergazdai felügyeleten keresztül. Az Azure AD-val könnyedén érhet el összetett szabályzatokat, több felügyeleti modellt kombinálva egyetlen alkalmazáshoz, és akár újra fel is használhatja a felügyeleti szabályokat az azonos célközönséggel rendelkező alkalmazásokban.

Az Azure AD-val a használati és hozzárendelési jelentések teljes mértékben integrálva vannak, így a rendszergazdák könnyedén jelentik a hozzárendelés állapotát, a hozzárendelési hibákat és akár a használatot is.

### <a name="assigning-users-and-groups-to-an-app"></a>Felhasználók és csoportok hozzárendelése egy alkalmazáshoz

Az Azure AD alkalmazás-hozzárendelése két elsődleges hozzárendelési módra összpontosít:

* **Egyéni hozzárendelés** A globális rendszergazdai engedélyekkel rendelkező rendszergazdák egyéni felhasználói fiókokat választhatnak, és hozzáférést adhatnak nekik az alkalmazáshoz.

* **Csoportalapú hozzárendelés (P1 vagy P2 prémium szintű Azure AD szükséges)** A globális rendszergazdai engedélyekkel rendelkező rendszergazdák csoportokat rendelhetnek az alkalmazáshoz. Az adott felhasználók hozzáférését az határozza meg, hogy tagjai-e a csoportnak az alkalmazás elérésére való próbálkozáskor. Más szóval a rendszergazda hatékonyan létrehozhat egy hozzárendelési szabályt, amely szerint "a hozzárendelt csoport bármely aktuális tagja hozzáfér az alkalmazáshoz". Ezzel a hozzárendelési lehetőséggel a rendszergazdák bármely Azure AD-csoportkezelési lehetőséget kihasználhatják, beleértve az attribútumalapú dinamikus [csoportokat,](../fundamentals/active-directory-groups-create-azure-portal.md)a külső rendszercsoportokat (például helyi Active Directory vagy Workday) vagy a rendszergazda által felügyelt vagy önkiszolgáló felügyelt csoportokat. Egyetlen csoport egyszerűen hozzárendelhető több alkalmazáshoz, így a hozzárendelési affinitással rendelkező alkalmazások megoszthatják a hozzárendelési szabályokat, ami csökkenti a felügyelet összetettségét. Vegye figyelembe, hogy a beágyazott csoporttagságokat az alkalmazásokhoz való csoportalapú hozzárendelés jelenleg nem támogatja.

Ezzel a két hozzárendelési móddal a rendszergazdák bármilyen kívánt hozzárendelés-kezelési megközelítést elérhetnek.

### <a name="requiring-user-assignment-for-an-app"></a>Felhasználó-hozzárendelés igénylése egy alkalmazáshoz

Bizonyos típusú alkalmazások esetén megkövetelhető a felhasználók hozzárendelése [az alkalmazáshoz.](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) Ezzel megakadályozható, hogy mindenki bejelentkezhet, kivéve azokat a felhasználókat, akikhez kifejezetten hozzárendeli az alkalmazást. Ezt a beállítást a következő típusú alkalmazások támogatják:

* SAML-alapú hitelesítéssel rendelkező összevont egyszeri bejelentkezésre (SSO) konfigurált alkalmazások
* alkalmazásproxy előhitelesítést Azure Active Directory alkalmazásokat
* Az Azure AD alkalmazásplatformra épített, OAuth 2.0/OpenID Connect hitelesítést használó alkalmazások, miután egy felhasználó vagy rendszergazda hozzájárulását adta az alkalmazáshoz. Egyes vállalati alkalmazások további ellenőrzést nyújtanak a bejelentkezéshez engedélyezett felhasználók felett.

Ha nincs szükség felhasználó-hozzárendelésre, a nem hozzárendelt felhasználók nem látják az alkalmazást az Saját alkalmazások-ban, de továbbra is bejelentkeznek az alkalmazásba (más néven SP által  kezdeményezett bejelentkezés), vagy használhatja a Felhasználói hozzáférés **URL-címét** az alkalmazás Tulajdonságok lapján (más néven az IDP által kezdeményezett bejelentkezés). 

Egyes alkalmazások esetében a felhasználó-hozzárendelés megkövetelhető beállítás nem érhető el az alkalmazás tulajdonságai között. Ezekben az esetekben a PowerShell használatával beállíthatja az appRoleAssignmentRequired tulajdonságot a szolgáltatásnéven.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Az alkalmazások elérésének felhasználói élményének meghatározása

Az Azure AD [számos testreszabható módszert](end-user-experiences.md) kínál az alkalmazások üzembe helyezésére a szervezet végfelhasználói számára:

* Azure AD Saját alkalmazások
* Microsoft 365 alkalmazásindító használata
* Közvetlen bejelentkezés összevont alkalmazásokba (service-pr)
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Meghatározhatja, hogy a vállalati alkalmazáshoz hozzárendelt felhasználók láthatják-e azt a Saját alkalmazások és Microsoft 365 alkalmazásindítóban.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Példa: Összetett alkalmazás-hozzárendelés az Azure AD-val
Vesszünk egy olyan alkalmazást, mint a Salesforce. Számos szervezetben a Salesforce-t elsősorban a marketing- és értékesítési csapatok használják. A marketingcsapat tagjai gyakran magas jogosultsági szintű hozzáféréssel rendelkezik a Salesforce-hoz, míg az értékesítési csapat tagjai korlátozott hozzáféréssel. Sok esetben az információs munkatársak széles körének korlátozott hozzáférése van az alkalmazáshoz. A szabályok alóli kivételek bonyolítják a dolgokat. Gyakran a marketing- vagy értékesítési vezetők előjoga, hogy hozzáférést adjanak egy felhasználónak, vagy az általános szabályoktól függetlenül módosítják a szerepkörüket.

Az Azure AD-val az olyan alkalmazások, mint a Salesforce, előre konfigurálhatóak egyszeri bejelentkezéshez (SSO) és automatizált kiépítéshez. Az alkalmazás konfigurálása után a rendszergazda egyszer létrehozhatja és hozzárendelheti a megfelelő csoportokat. Ebben a példában a rendszergazda a következő hozzárendeléseket hajthatja végre:

* [Dinamikus csoportok definiálhatók](../fundamentals/active-directory-groups-create-azure-portal.md) úgy, hogy automatikusan képviselje a marketing- és értékesítési csapatok összes tagját olyan attribútumok használatával, mint a részleg vagy a szerepkör:
  
  * A marketingcsoportok minden tagja a Salesforce "marketing" szerepkörét kapná
  * Az értékesítési csoport minden tagja a Salesforce "értékesítési" szerepkörhöz lesz hozzárendelve. Egy további finomítás több csoportot is használhat, amelyek a különböző Salesforce-szerepkörökhöz rendelt regionális értékesítési csapatokat képviselik.

* A kivétel mechanizmusának engedélyezéséhez minden szerepkörhöz önkiszolgáló csoport is létre lehet hozva. A "Salesforce marketingki kivétel" csoport például létrehozható önkiszolgáló csoportként. A csoport hozzárendelhető a Salesforce marketingszerepkedőhöz, a marketingvezető csapat pedig tulajdonos lehet. A marketinges vezetőség tagjai felhasználókat adhatnak hozzá vagy távolítanak el, beléptethet egy szabályzatot, vagy jóváhagyhatják vagy elutasíthatják az egyes felhasználók csatlakozásra vonatkozó kéréseit. Ezt a mechanizmust az információ-feldolgozó megfelelő felhasználói élménye támogatja, amely nem igényel speciális képzést a tulajdonosok vagy tagok számára.

Ebben az esetben minden hozzárendelt felhasználó automatikusan ki lesz osztva a Salesforce-ban, mivel azok különböző csoportokhoz lesznek hozzáadva, a szerepkör-hozzárendelésük frissül a Salesforce-ban. A felhasználók a Salesforce-t az Saját alkalmazások, az Office webes ügyfelein, vagy akár a céges Salesforce bejelentkezési oldalukon is felfedezhetik és elérhetik. A rendszergazdák könnyedén megtekinthetik a használati és hozzárendelési állapotot az Azure AD jelentéskészítési szolgáltatásával.

A rendszergazdák [Azure AD](../conditional-access/concept-conditional-access-users-groups.md) feltételes hozzáféréssel állíthatnak be hozzáférési szabályzatokat adott szerepkörökhöz. Ezek a szabályzatok magukban foglalhatják, hogy a hozzáférés a vállalati környezeten kívül engedélyezett-e, vagy akár többtényezős hitelesítés vagy eszközkövetelmények a hozzáférés különböző esetekben való eléréséhez.

## <a name="access-to-microsoft-applications"></a>Hozzáférés a Microsoft-alkalmazásokhoz

A Microsoft-alkalmazások (például Exchange, SharePoint, Yammer stb.) hozzárendelése és kezelése kissé eltér a harmadik féltől származó SaaS-alkalmazásoktól és az Azure AD-val az egyszeri bejelentkezéshez integrálható egyéb alkalmazásoktól.

A felhasználók három fő módon férhetnek hozzá a Microsoft által közzétett alkalmazásokhoz.

- A Microsoft 365 vagy más fizetős csomagokban található alkalmazásokhoz  a felhasználók közvetlenül a felhasználói fiókjukhoz vagy a csoportalapú licenc-hozzárendelési képességünket használó csoporton keresztül kapnak hozzáférést a licenc-hozzárendeléssel.
- A Microsoft vagy külső fél által bárki számára ingyenesen közzétett alkalmazások esetén a felhasználók a felhasználói hozzájárulással kapnak [hozzáférést.](configure-user-consent.md) Ez azt jelenti, hogy bejelentkeznek az alkalmazásba a munkahelyi vagy iskolai Azure AD-fiókjukkal, és lehetővé teszik, hogy a fiókjukban korlátozott mennyiségű adathoz férnek hozzá.
- A Microsoft vagy külső fél által bárki számára ingyenesen közzétett alkalmazások esetén a felhasználók rendszergazdai jóváhagyással is [hozzáférést](manage-consent-requests.md)kapnak. Ez azt jelenti, hogy egy rendszergazda megállapította, hogy az alkalmazást a szervezet minden tagja hatja, ezért bejelentkezik az alkalmazásba egy globális rendszergazdai fiókkal, és hozzáférést biztosít a szervezet minden tagja számára.

Egyes alkalmazások kombinálják ezeket a metódusokat. Bizonyos Microsoft-alkalmazások például egy Microsoft 365 részét képezi, de továbbra is jóváhagyást igényelnek.

A felhasználók office 365 Microsoft 365 portáljaikon keresztül férhetnek hozzá az alkalmazásokhoz. Az Office 365 láthatósági Microsoft 365 a címtár Felhasználói beállításai között az [Office 365](hide-application-from-user-portal.md) láthatósági kapcsolóval is elrejtheti a Microsoft 365 Saját alkalmazások **alkalmazásokat.** 

Ahogy a vállalati alkalmazások [](assign-user-or-group-access-portal.md) esetében, a felhasználók bizonyos Microsoft-alkalmazásokhoz is hozzárendelhetőek a Azure Portal, vagy ha a portál nem érhető el, a PowerShell használatával.

## <a name="next-steps"></a>Következő lépések
* [Alkalmazások védelme feltételes hozzáféréssel](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Önkiszolgáló csoportkezelés/SSAA](../enterprise-users/groups-self-service-management.md)
