---
title: Automatikus felhasználói fiók kiépítés az SaaS-alkalmazásokba
description: Megtudhatja, hogyan ellenőrizhető a felhasználói fiókok automatikus kiépítési feladatainak állapota, és hogyan lehet elhárítani az egyes felhasználók kiépítési feladatait.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 289347474189d1fb57d95a2f424cf381e1e37875
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522662"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Oktatóanyag: jelentéskészítés a felhasználói fiókok automatikus üzembe helyezéséhez

Azure Active Directory (Azure AD) olyan [felhasználói fiók létesítési szolgáltatását](user-provisioning.md) tartalmazza, amely segít automatizálni az SaaS-alkalmazásokban és más rendszerekben lévő felhasználói fiókok üzembe helyezését a teljes körű identitások életciklusának kezelése céljából. Az Azure AD az [Azure ad Application Gallery](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)"Kiemelt" szakaszában található összes alkalmazáshoz és rendszerhez támogatja az előre integrált felhasználó-kiépítési összekötőket.

Ez a cikk azt ismerteti, hogyan ellenőrizhető a kiépítési feladatok állapota a beállításuk után, valamint az egyes felhasználók és csoportok üzembe helyezésének hibaelhárítása.

## <a name="overview"></a>Áttekintés

A kiépítési összekötők a [Azure Portal](https://portal.azure.com)használatával állíthatók be és konfigurálhatók, a támogatott alkalmazáshoz mellékelt [dokumentáció](../saas-apps/tutorial-list.md) alapján. A konfigurálás és a Futtatás után a kiépítési feladatok a következő két módszer egyikének használatával adhatók meg:

* **Azure Portal** – ez a cikk elsősorban azt ismerteti, hogyan kell beolvasni a jelentési adatokat a [Azure Portalból](https://portal.azure.com), amely egyszerre egy kiépítési összegző jelentést is biztosít, valamint egy adott alkalmazás részletes kiépítési naplóit.
* A **audit API** -Azure Active Directory egy olyan naplózási API-t is biztosít, amely lehetővé teszi a részletes kiépítési naplók programozott lekérését. Tekintse meg az API használatára vonatkozó dokumentáció [Azure Active Directory naplózási API-referenciát](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) . Habár ez a cikk nem fedi le az API használatát, részletesen ismerteti a naplóban rögzített kiépítési események típusait.

### <a name="definitions"></a>Meghatározások

Ez a cikk az alábbi, az alábbiakban meghatározott kifejezéseket használja:

* **Forrásoldali rendszer** – az Azure ad kiépítési szolgáltatás által szinkronizált felhasználók tárháza. A Azure Active Directory az előre integrált létesítési összekötők többségének a forrása, azonban bizonyos kivételek (például: munkaidőn belüli bejövő szinkronizálás).
* **Célrendszer** – az Azure ad-kiépítési szolgáltatás által szinkronizált felhasználók tárháza. Ez általában egy SaaS-alkalmazás (például: Salesforce, ServiceNow, G Suite, Dropbox for Business), de bizonyos esetekben olyan helyszíni rendszer is lehet, mint például a Active Directory (például: munkanapokon bejövő szinkronizálás Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Kiépítési jelentések beolvasása a Azure Portalból

Ha egy adott alkalmazáshoz szeretne kiépíteni egy jelentést, először indítsa el a [Azure Portal](https://portal.azure.com) és **Azure Active Directory** &gt; **vállalati alkalmazásokat** &gt; **kiépítési naplókat (előzetes verzió)** a **tevékenység** szakaszban. Azt is megteheti, hogy megkeresi azt a vállalati alkalmazást, amelyhez a kiépítés konfigurálva van. Ha például a felhasználókat a LinkedIn jogosultságszint-emeléssel kívánja kiépíteni, az alkalmazás adatainak navigációs útvonala a következő:

**Azure Active Directory > vállalati alkalmazások > minden alkalmazás > LinkedIn emelt szintű**

Innen elérheti a kiépítési folyamatjelző sávot és a kiépítési naplókat is, amelyeket az alábbiakban ismertetünk.

## <a name="provisioning-progress-bar"></a>Kiépítés folyamatjelző sáv

A [kiépítési folyamatjelző sáv](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) az adott alkalmazás **létesítés** lapján látható. Az aktuális **állapot** szakaszban a **Beállítások**alatt található, és az aktuális kezdeti vagy növekményes ciklus állapotát jeleníti meg. Ez a szakasz a következőket is tartalmazza:

* A szinkronizált felhasználók és/csoportok teljes száma, amelyek jelenleg a forrásrendszer és a célként megadott rendszer közötti kiépítés hatókörében vannak.
* A szinkronizálás legutóbbi futtatásakor. A szinkronizálások jellemzően 20-40 percenként történnek, a [kezdeti ciklus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) befejeződése után.
* Azt határozza meg, hogy befejeződött-e a [kezdeti ciklus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) .
* Azt határozza meg, hogy a kiépítési folyamat karanténba helyezése megtörtént-e, és hogy a karantén milyen okból áll fenn (például érvénytelen rendszergazdai hitelesítő adatok miatt nem lehet kommunikálni a megcélzott rendszerrel).

Az **aktuális állapotnak** az első hely rendszergazdáinak kell megkeresnie a kiépítési feladatok működési állapotát.

 ![Összegző jelentés](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Kiépítési naplók (előzetes verzió)

A kiépítési szolgáltatás által végrehajtott összes tevékenységet az Azure AD- [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)rögzítik. A Azure Portal kiépítési naplóit a **tevékenység** szakaszban **Azure Active Directory** &gt; **vállalati alkalmazások** &gt; **kiépítési naplók (előzetes verzió)** lehetőség kiválasztásával érheti el. A kiépítési adat a felhasználó neve vagy a forrásrendszer vagy a célként megadott rendszer alapján is megkereshető. Részletekért lásd: [kiépítési naplók (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Naplózott tevékenység típusú események a következők:

## <a name="troubleshooting"></a>Hibakeresés

A kiépítési összefoglalás és a kiépítési naplók kulcsszerepet játszanak a rendszergazdáknak a különböző felhasználói fiókok üzembe helyezésével kapcsolatos problémák elhárításában.

Az automatikus felhasználó-kiépítés hibaelhárításával kapcsolatos forgatókönyv-alapú útmutatásért tekintse meg a [felhasználók egy alkalmazáshoz való konfigurálásával és](../app-provisioning/application-provisioning-config-problem.md)kikapcsolásával kapcsolatos problémákat.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
