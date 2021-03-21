---
title: Automatikus felhasználói fiók kiépítés az SaaS-alkalmazásokba
description: Megtudhatja, hogyan ellenőrizhető a felhasználói fiókok automatikus kiépítési feladatainak állapota, és hogyan lehet elhárítani az egyes felhasználók kiépítési feladatait.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5d93cec201feeb43700fe849f2fddc8eaf22488a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579484"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Oktatóanyag: jelentéskészítés a felhasználói fiókok automatikus üzembe helyezéséhez

Azure Active Directory (Azure AD) olyan [felhasználói fiók létesítési szolgáltatását](user-provisioning.md) tartalmazza, amely segít automatizálni az SaaS-alkalmazásokban és más rendszerekben lévő felhasználói fiókok üzembe helyezését a teljes körű identitások életciklusának kezelése céljából. Az Azure AD az összes olyan alkalmazás és rendszer számára támogatja az előre integrált felhasználó-kiépítési összekötőket, amelyeken a felhasználók üzembe helyezésével [kapcsolatos oktatóanyagok találhatók](../saas-apps/tutorial-list.md).

Ez a cikk azt ismerteti, hogyan ellenőrizhető a kiépítési feladatok állapota a beállításuk után, valamint az egyes felhasználók és csoportok üzembe helyezésének hibaelhárítása.

## <a name="overview"></a>Áttekintés

A kiépítési összekötők a [Azure Portal](https://portal.azure.com)használatával állíthatók be és konfigurálhatók, a támogatott alkalmazáshoz mellékelt [dokumentáció](../saas-apps/tutorial-list.md) alapján. A konfigurálás és a Futtatás után a kiépítési feladatok a következő két módszer egyikének használatával adhatók meg:

* **Azure Portal** – ez a cikk elsősorban azt ismerteti, hogyan kell beolvasni a jelentési adatokat a [Azure Portalból](https://portal.azure.com), amely egyszerre egy kiépítési összegző jelentést is biztosít, valamint egy adott alkalmazás részletes kiépítési naplóit.
* A **audit API** -Azure Active Directory egy olyan naplózási API-t is biztosít, amely lehetővé teszi a részletes kiépítési naplók programozott lekérését. Tekintse meg az API használatára vonatkozó dokumentáció [Azure Active Directory naplózási API-referenciát](/graph/api/resources/directoryaudit) . Habár ez a cikk nem fedi le az API használatát, részletesen ismerteti a naplóban rögzített kiépítési események típusait.

### <a name="definitions"></a>Definíciók

Ez a cikk az alábbi, az alábbiakban meghatározott kifejezéseket használja:

* **Forrásoldali rendszer** – az Azure ad kiépítési szolgáltatás által szinkronizált felhasználók tárháza. A Azure Active Directory az előre integrált létesítési összekötők többségének a forrása, azonban bizonyos kivételek (például: munkaidőn belüli bejövő szinkronizálás).
* **Célrendszer** – az Azure ad-kiépítési szolgáltatás által szinkronizált felhasználók tárháza. Ez általában egy SaaS-alkalmazás (például: Salesforce, ServiceNow, G Suite, Dropbox for Business), de bizonyos esetekben olyan helyszíni rendszer is lehet, mint például a Active Directory (például: munkanapokon bejövő szinkronizálás Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Kiépítési jelentések beolvasása a Azure Portalból

Ha egy adott alkalmazáshoz szeretne kiépíteni egy jelentést, először indítsa el a [Azure Portal](https://portal.azure.com) és **Azure Active Directory** &gt; **vállalati alkalmazások** &gt; **kiépítési naplóit (előzetes verzió)** a **tevékenység** szakaszban. Azt is megteheti, hogy megkeresi azt a vállalati alkalmazást, amelyhez a kiépítés konfigurálva van. Ha például a felhasználókat a LinkedIn jogosultságszint-emeléssel kívánja kiépíteni, az alkalmazás adatainak navigációs útvonala a következő:

**Azure Active Directory > vállalati alkalmazások > minden alkalmazás > LinkedIn emelt szintű**

Innen elérheti a kiépítési folyamatjelző sávot és a kiépítési naplókat is, amelyeket az alábbiakban ismertetünk.

## <a name="provisioning-progress-bar"></a>Kiépítés folyamatjelző sáv

A [kiépítési folyamatjelző sáv](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) az adott alkalmazás **létesítés** lapján látható. A **jelenlegi állapot** szakaszban található, és az aktuális kezdeti vagy növekményes ciklus állapotát jeleníti meg. Ez a szakasz a következőket is tartalmazza:

* A szinkronizált felhasználók és/csoportok teljes száma, amelyek jelenleg a forrásrendszer és a célként megadott rendszer közötti kiépítés hatókörében vannak.
* A szinkronizálás legutóbbi futtatásakor. A szinkronizálások jellemzően 20-40 percenként történnek, a [kezdeti ciklus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) befejeződése után.
* Azt határozza meg, hogy befejeződött-e a [kezdeti ciklus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) .
* Azt határozza meg, hogy a kiépítési folyamat karanténba helyezése megtörtént-e, és hogy a karantén milyen okból áll fenn (például érvénytelen rendszergazdai hitelesítő adatok miatt nem lehet kommunikálni a megcélzott rendszerrel).

Az **aktuális állapotnak** az első hely rendszergazdáinak kell megkeresnie a kiépítési feladatok működési állapotát.

 ![Összegző jelentés](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Kiépítési naplók (előzetes verzió)

A kiépítési szolgáltatás által végrehajtott összes tevékenységet az Azure AD- [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)rögzítik. A Azure Portal kiépítési naplóit a  &gt; tevékenység szakaszban Azure Active Directory **vállalati alkalmazások** &gt; **kiépítési naplói (előzetes verzió)**  lehetőség kiválasztásával érheti el. A kiépítési adat a felhasználó neve vagy a forrásrendszer vagy a célként megadott rendszer alapján is megkereshető. Részletekért lásd: [kiépítési naplók (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Naplózott tevékenység típusú események a következők:

## <a name="troubleshooting"></a>Hibaelhárítás

A kiépítési összefoglalás és a kiépítési naplók kulcsszerepet játszanak a rendszergazdáknak a különböző felhasználói fiókok üzembe helyezésével kapcsolatos problémák elhárításában.

Az automatikus felhasználó-kiépítés hibaelhárításával kapcsolatos forgatókönyv-alapú útmutatásért tekintse meg a [felhasználók egy alkalmazáshoz való konfigurálásával és](../app-provisioning/application-provisioning-config-problem.md)kikapcsolásával kapcsolatos problémákat.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)