---
title: Mi az az Azure AD-ben az automatizált SaaS-alkalmazások felhasználói üzembe helyezése
description: Bevezetés az Azure AD használatával a felhasználói fiókok automatikus kiosztására, kiépítésére és folyamatos frissítésére több külső SaaS-alkalmazás között.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 02/08/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: f899c4d67321482dab62729632fe031bb23f71ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99830668"
---
# <a name="what-is-automated-saas-app-user-provisioning-in-azure-ad"></a>Mi az az Azure AD-ben az automatizált SaaS-alkalmazások felhasználói üzembe helyezése?

Azure Active Directory (Azure AD) esetében az **alkalmazás üzembe** helyezésének kifejezése arra a felhasználói identitások és szerepkörök automatikus létrehozására utal, amelyekhez [](https://azure.microsoft.com/overview/what-is-saas/)a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett az automatikus kiépítés a felhasználói identitások karbantartását és eltávolítását is magában foglalja az állapot vagy a szerepkörök módosításakor. Gyakori forgatókönyvek például az Azure AD-felhasználók üzembe helyezése olyan alkalmazásokban, mint a [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), a [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), a [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)és még sok más.

Csak az App Management és az egyszeri bejelentkezés (SSO) első lépései az Azure AD-ben? Tekintse meg a gyors üzembe helyezési [sorozatot](../manage-apps/view-applications-portal.md).

Ha többet szeretne megtudni a SCIM és a technikai közösségi beszélgetéshez való csatlakozásról, tekintse meg a [kiépítés a scim technikai Közösséggel](https://aka.ms/scimoverview)című témakört.

![Kiépítés – áttekintés diagram](./media/user-provisioning/provisioning-overview.png)

Ez a funkció lehetővé teszi a következőket:

- A **kiépítés automatizálása**: automatikusan hozzon létre új fiókokat a megfelelő rendszerekben az új személyekhez, amikor csatlakoznak a csapathoz vagy szervezethez.
- **Kiépítés automatizálása:** Automatikusan inaktiválja a fiókokat a megfelelő rendszerekben, amikor a felhasználók elhagyják a csapatot vagy a szervezetet.
- **Az adatszinkronizálás a rendszerek között:** Győződjön meg arról, hogy az alkalmazásokban és rendszerekben lévő identitások naprakészek maradnak a címtár vagy az emberi erőforrások rendszerének változásai alapján.
- **Csoportok kiépítése:** Csoportok kiépítése az azokat támogató alkalmazásokba.
- **Hozzáférés szabályozása:** Az alkalmazásokban kiépített figyelés és naplózás.
- **Zökkenőmentes üzembe helyezés a barna mezőkben:** A meglévő identitások egyeztetése a rendszerek között, és az egyszerű integráció lehetővé tétele, még akkor is, ha a felhasználók már léteznek a megcélzott rendszerben.
- **Gazdag Testreszabás használata:** Kihasználhatja a testreszabható attribútum-hozzárendeléseket, amelyek meghatározzák, hogy a felhasználói adatok milyen mértékben áramlanak a forrásoldali rendszerből a célként megadott rendszerbe.
- **Riasztások beolvasása kritikus eseményekre:** A kiépítési szolgáltatás riasztásokat biztosít a kritikus fontosságú eseményekhez, és lehetővé teszi Log Analytics integrációt, ahol egyéni riasztásokat adhat meg az üzleti igényeknek megfelelően.

## <a name="benefits-of-automatic-provisioning"></a>Az automatikus kiépítés előnyei

Mivel a modern szervezeteknél használt alkalmazások száma folyamatosan nő, a rendszergazdák a nagy léptékű hozzáférés-kezeléssel rendelkeznek. Az olyan szabványok, mint például a biztonsági kikötések Markup Language (SAML) vagy a Open ID kapcsolódás (OIDC) lehetővé teszik a rendszergazdáknak, hogy gyorsan állítsanak be egyszeri bejelentkezést (SSO), de a hozzáféréshez az is szükséges, hogy a felhasználók beépítsék az alkalmazást. Számos rendszergazda számára a kiépítés azt jelenti, hogy manuálisan hozza létre az összes felhasználói fiókot, vagy a CSV-fájlokat minden héten feltölti, de ezek a folyamatok időigényesek, költségesek és hibákra hajlamosak. Az SAML-alapú (például JIT) megoldásokat a kiépítés automatizálására is elfogadták, de a vállalatoknak olyan megoldásra is szükségük van, amely a felhasználók kiépítését igényli, amikor a szervezet elhagyja a munkahelyet, vagy már nem igényelnek hozzáférést bizonyos alkalmazásokhoz a szerepkör módosítása alapján.

Az automatikus kiépítés használatának gyakori indítékai a következők:

- A kiépítési folyamatok hatékonyságának és pontosságának maximalizálása.
- Az egyedi fejlesztésű üzembe helyezési megoldások és szkriptek üzemeltetésével és karbantartásával kapcsolatos költségek mentése.
- A szervezet védelme azáltal, hogy azonnal eltávolítja a felhasználók identitását a Key SaaS-alkalmazásokból, amikor elhagyja a szervezetet.
- Számos felhasználót egyszerűen importálhat egy adott SaaS-alkalmazásba vagy-rendszerbe.
- Egyetlen szabályzattal meghatározhatja, hogy ki kiépített és ki tud jelentkezni egy alkalmazásba.

Az Azure AD-felhasználók üzembe helyezése segíthet a problémák megoldásában. Ha többet szeretne megtudni arról, hogy az ügyfelek hogyan használják az Azure AD-t, akkor olvassa el a [Asos-esettanulmányt](https://aka.ms/asoscasestudy). Az alábbi videó áttekintést nyújt a felhasználók üzembe helyezéséről az Azure AD-ben:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Milyen alkalmazásokat és rendszereket használhatok az Azure AD automatikus felhasználói üzembe helyezéséhez?

Az Azure AD számos népszerű SaaS-alkalmazás és emberi erőforrásrendszer előre integrált támogatását, valamint általános támogatást nyújt a [SCIM 2,0 standard](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)részét képező alkalmazások számára.

* **Előre integrált alkalmazások (Gallery SaaS-alkalmazások)**. Megtalálhatja az összes olyan alkalmazást, amelyhez az Azure AD támogatja a [felhasználó által kiépített alkalmazás-oktatóanyagokat](../saas-apps/tutorial-list.md)tartalmazó, előre integrált létesítési összekötőt. A katalógusban felsorolt előre integrált alkalmazások általában a SCIM 2,0-alapú felhasználói felügyeleti API-kat használják a kiépítés számára. 

   ![Salesforce embléma](./media/user-provisioning/gallery-app-logos.png)

   Ha új alkalmazást szeretne igényelni az üzembe helyezéshez, [kérheti, hogy az alkalmazás integrálva legyen](../develop/v2-howto-app-gallery-listing.md)az alkalmazás-galériával. A felhasználó kiépítési kérelméhez az alkalmazásnak SCIM-kompatibilis végponttal kell rendelkeznie. Kérje meg, hogy az alkalmazás gyártója kövesse a SCIM standardot, így gyorsan üzembe helyezhetjük az alkalmazást a platformon.

* **Az SCIM 2,0-et támogató alkalmazások**. Az SCIM 2,0-alapú felhasználói felügyeleti API-kat megvalósító alkalmazások általános összekapcsolásával kapcsolatos információkért lásd: [scim-végpont létrehozása és a felhasználók üzembe](use-scim-to-provision-users-and-groups.md)helyezésének konfigurálása.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Mi a rendszer a tartományok közötti Identitáskezelés kezelésére (SCIM)?

A kiépítés és a megszüntetés automatizálása érdekében az alkalmazások tulajdonosi felhasználói és csoportos API-kat tesznek elérhetővé. Azonban bárki, aki több alkalmazásban próbálta felügyelni a felhasználókat, azt fogja tudni, hogy minden alkalmazás ugyanazt az egyszerű műveletet próbálja végrehajtani, például a felhasználók létrehozását és frissítését, a felhasználók csoportokhoz való hozzáadását vagy a felhasználók megszüntetését. Az ilyen egyszerű műveletek azonban a különböző végponti elérési utakkal, a felhasználói adatok megadására szolgáló különböző módszerekkel, valamint egy másik, az egyes elemek ábrázolására szolgáló sémával eltérő módon valósíthatók meg.

A problémák megoldása érdekében a SCIM-specifikáció egy általános felhasználói sémát biztosít, amellyel a felhasználók beléphetnek az alkalmazásba, és onnan is megtalálhatják őket. A SCIM a kiépítés de facto szabványa, és az összevonási szabványok, például az SAML vagy az OpenID Connect együttes használata esetén a rendszergazdák teljes körű, a hozzáférés-vezérlésre vonatkozó szabványokon alapuló megoldást biztosítanak.

A SCIM-végpontok alkalmazáshoz való kiépítésének és megszüntetésének automatizálására vonatkozó részletes útmutatásért lásd: [scim-végpont létrehozása és](use-scim-to-provision-users-and-groups.md)a felhasználók üzembe helyezésének konfigurálása. A katalógusban található előre integrált alkalmazások (Slack, Azure Databricks, hópehely stb.) esetében kihagyhatja a fejlesztői dokumentációt, és használhatja az [itt](../saas-apps/tutorial-list.md)elérhető oktatóanyagokat.

## <a name="manual-vs-automatic-provisioning"></a>Manuális és automatikus átadás

Az Azure AD-katalógusban található alkalmazások támogatják a két üzembe helyezési mód egyikét:

* A **manuális** kiépítés azt jelenti, hogy még nincs automatikus Azure ad-létesítési összekötő az alkalmazáshoz. A felhasználói fiókokat manuálisan kell létrehozni, például a felhasználókat közvetlenül az alkalmazás felügyeleti portálján, vagy a felhasználói fiók részleteit tartalmazó számolótábla feltöltésével. Tekintse át az alkalmazás által biztosított dokumentációt, vagy lépjen kapcsolatba az alkalmazás fejlesztővel, és határozza meg, hogy milyen mechanizmusok érhetők el.

* Az **automatikus** beállítás azt jelenti, hogy az alkalmazáshoz egy Azure ad-létesítési összekötő lett kifejlesztve. Kövesse az alkalmazás üzembe helyezésének beállítására vonatkozó telepítési oktatóanyagot. Az alkalmazás-oktatóanyagok az [SaaS-alkalmazások Azure Active Directory használatával történő integrálását ismertető oktatóanyagban](../saas-apps/tutorial-list.md)találhatók.

Az Azure AD-katalógusban az automatikus kiépítés támogatására szolgáló alkalmazásokat **kiépítési** ikon jelöli. Váltson az új katalógus előzetes verziójára, és tekintse meg ezeket az ikonokat (az **alkalmazás hozzáadása lap** tetején található szalagcímben, majd válassza ki azt a hivatkozást, amely az **új és továbbfejlesztett alkalmazás-gyűjtemény kipróbálásához kattintson ide**).

![Kiépítési ikon az alkalmazás-gyűjteményben](./media/user-provisioning/browse-gallery.png)

Az alkalmazás által támogatott kiépítési mód a **kiépítés** lapon is látható, miután hozzáadta az alkalmazást a **vállalati** alkalmazásokhoz.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hogyan automatikus kiépítés beállítása egy alkalmazáshoz?

A katalógusban felsorolt előre integrált alkalmazások esetében az automatikus kiépítés beállításához lépésenkénti útmutató áll rendelkezésre. Tekintse [meg az integrált katalógus-alkalmazásokhoz tartozó oktatóanyagok listáját](../saas-apps/tutorial-list.md). A következő videó bemutatja, hogyan állíthatja be az automatikus felhasználó-kiépítés SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Az SCIM 2,0-et támogató egyéb alkalmazások esetében kövesse a [scim-végpont létrehozása és a felhasználók kiépítésének konfigurálása](use-scim-to-provision-users-and-groups.md)című cikkben ismertetett lépéseket.


## <a name="next-steps"></a>Következő lépések

- [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
- [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](customize-application-attributes.md)
- [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
