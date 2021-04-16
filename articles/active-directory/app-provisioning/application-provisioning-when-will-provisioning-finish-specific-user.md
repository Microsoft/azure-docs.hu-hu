---
title: Annak kideríte, hogy egy adott felhasználó mikor férhet hozzá egy alkalmazáshoz
description: Hogyan deríti ki, hogy egy kritikus fontosságú felhasználó mikor férhet hozzá az Azure AD-val való felhasználóátépítéshez konfigurált alkalmazáshoz?
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9835ba2b6db2d71d0ff5825f2eb1996133e75537
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530830"
---
# <a name="check-the-status-of-user-provisioning"></a>A felhasználóátépítés állapotának ellenőrzése

Az Azure AD kiépítési szolgáltatás egy kezdeti kiépítési ciklust futtat a forrásrendszeren és a célrendszeren, majd időszakos növekményes ciklusokat követ. Amikor konfigurálja egy alkalmazás kiépítését, ellenőrizheti a kiépítési szolgáltatás aktuális állapotát, és láthatja, hogy a felhasználók mikor férhetnek hozzá az alkalmazásokhoz.

## <a name="view-the-provisioning-progress-bar"></a>A kiépítési folyamatjelző sáv megtekintése

 Az alkalmazás **Kiépítés** lapján megtekintheti az Azure AD kiépítési szolgáltatás állapotát. A **lap alján** található Aktuális állapot szakasz azt mutatja, hogy megkezdődött-e egy kiépítési ciklus felhasználói fiókok létesítése. Figyelheti a ciklus előrehaladását, láthatja, hogy hány felhasználó és csoport lett kiépítve, és láthatja, hány szerepkör lett létrehozva.

Az automatikus kiépítés első  konfigurálásakor a lap alján található Aktuális állapot szakasz a kezdeti kiépítési ciklus állapotát jeleníti meg. Ez a szakasz a növekményes ciklusok minden futtatásakor frissül. Az alábbi részletek jelennek meg:
- Az aktuálisan futó vagy utoljára befejezett kiépítési ciklus (kezdeti vagy növekményes) típusa.
- Egy **folyamatjelző sáv,** amely a kiépítési ciklus befejezett százalékos arányát mutatja. A százalékos érték a kiépített oldalak számát tükrözi. Vegye figyelembe, hogy minden oldal több felhasználót vagy csoportot is tartalmazhat, így a százalékos arány nem kapcsolódik közvetlenül a kiépített felhasználók, csoportok vagy szerepkörök számhoz.
- Egy **Frissítés** gomb, amely a nézet frissítését tartja szem előtt.
- Az összekötő **adattárában** **található** felhasználók és csoportok száma. A szám minden olyankor nő, amikor egy objektumot hozzáadnak a kiépítés hatóköre alá. A szám nem fog leállni, ha egy felhasználó nem törölhető vagy nem törölhető, mivel ezzel nem távolítja el az objektumot az összekötő adattárból. A rendszer újraszámolja az első szinkronizálást a CDS alaphelyzetbe állítása [után](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) 
- Az **Auditnaplók** megtekintése hivatkozás megnyitja az Azure AD kiépítési naplóit a felhasználóátépítési szolgáltatás által futtatott összes művelet részleteiért, beleértve az egyes felhasználók kiépítési állapotát (lásd az alábbi Kiépítési naplók használata című szakaszt). [](#use-provisioning-logs-to-check-a-users-provisioning-status)

A kiépítési ciklus befejezése után a **Statisztika** eddig szakasz megjeleníti az aktuálisan kiépített felhasználók és csoportok összesített számát, valamint az utolsó ciklus befejezési dátumát és időtartamát. A **tevékenységazonosító** egyedileg azonosítja a legutóbbi kiépítési ciklust. A **feladatazonosító** a kiépítési feladat egyedi azonosítója, és a bérlőben az alkalmazásra jellemző.

A kiépítési folyamat a vállalati alkalmazások Azure Portal lapon Azure Active Directory **&gt; látható. &gt; \[ \] &gt;**

![Kiépítési oldal folyamatjelző sávja](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Kiépítési naplók használata egy felhasználó kiépítési állapotának ellenőrzéshez

A kiválasztott felhasználó kiépítési állapotának megtekintéséhez tekintse meg az Azure AD-beli [kiépítési naplókat (előzetes](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) verzió). A felhasználóátépítési szolgáltatás által futtatott összes művelet rögzítve van az Azure AD kiépítési naplóiban. Ez magában foglalja a forrás- és célrendszereken az összes olvasási és írási műveletet, valamint az egyes műveletek során olvasott vagy írt felhasználói adatokat.

A kiépítési naplókat a Azure Portal a  Tevékenység szakaszban Azure Active Directory &gt; **Enterprise Apps** &gt; **provisioning logs (előzetes verzió)** lehetőség kiválasztásával.  A kiépítési adatok között kereshet a felhasználó neve vagy a forrásrendszerben vagy a célrendszerben megadott azonosító alapján. Részletekért lásd: [Kiépítési naplók (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

A kiépítési naplók a kiépítési szolgáltatás által végrehajtott összes műveletet rögzítik, beleértve a következőket:

* Az Azure AD lekérdezése a kiépítés hatókörében kijelölt felhasználókról
* A célalkalmazás lekérdezése a felhasználók meglétéről
* A felhasználói objektumok összehasonlítása a rendszer között
* Felhasználói fiók hozzáadása, frissítése vagy letiltása a célrendszerben az összehasonlítás alapján

További információ a kiépítési naplók beolvassa a Azure Portal a [kiépítési jelentéskészítési útmutatót.](check-status-user-account-provisioning.md)

## <a name="how-long-will-it-take-to-provision-users"></a>Mennyi ideig tart a felhasználók kiépítése?
Ha automatikus felhasználóátépítést használ egy alkalmazással, az Azure AD automatikusan [](../manage-apps/assign-user-or-group-access-portal.md) kiépíti és frissíti a felhasználói fiókokat egy alkalmazásban olyan dolgok alapján, mint a felhasználók és csoportok rendszeres időközönkénti, általában 40 percenkénti hozzárendelése.

Egy adott felhasználó üzembe állásának időtartama elsősorban attól függ, hogy az üzembe állási feladat kezdeti vagy növekményes ciklust futtat-e.

- A **kezdeti ciklus** esetében a feladatidő számos tényezőtől függ, beleértve a kiépítés hatókörébe tartozó felhasználók és csoportok számát, valamint a forrásrendszerben található felhasználók és csoportok teljes számát. Az Azure AD és az alkalmazás közötti első szinkronizálás 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében található felhasználók számától függően. A kezdeti ciklus teljesítményét befolyásoló tényezők átfogó listáját a szakasz későbbi részében foglaljuk össze.

- A  kezdeti ciklust követő növekményes ciklusok esetében a feladatidők általában gyorsabbak (például 10 percen belül), mivel a kiépítési szolgáltatás olyan vízjeleket tárol, amelyek mindkét rendszer állapotát képviselik a kezdeti ciklus után, ami javítja a későbbi szinkronizálások teljesítményét. A feladatidő az adott kiépítési ciklusban észlelt módosítások számától függ. Ha 5000-esnél kevesebb felhasználó- vagy csoporttagság-módosítás van, a feladat egyetlen növekményes kiépítési cikluson belül befejeződhet. 

Az alábbi táblázat a gyakori kiépítési forgatókönyvek szinkronizálási időit foglalja össze. Ezekben a forgatókönyvekben a forrásrendszer az Azure AD, a célrendszer pedig egy SaaS-alkalmazás. A szinkronizálási idők a ServiceNow, a Workplace, a Salesforce és a G Suite SaaS-alkalmazások szinkronizálási feladatának statisztikai elemzésén alapulnak.


| Hatókör-konfiguráció | Felhasználók, csoportok és tagok a hatókörben | Kezdeti ciklusidő | Növekményes ciklusidő |
| -------- | -------- | -------- | -------- |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |  < 1000 |  < 30 perc | < 30 perc |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |  1,000 - 10,000 | 142–708 perc | < 30 perc |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |   10,000 - 100,000 | 1170 – 2340 perc | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ban |  < 1000 | < 30 perc  | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ban |  1,000 - 10,000 | < 30–120 perc | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ban |  10,000 - 100,000  | 713 – 1425 perc | < 30 perc |
| Az összes felhasználó szinkronizálása az Azure AD-ban|  < 1000  | < 30 perc | < 30 perc |
| Az összes felhasználó szinkronizálása az Azure AD-ban | 1,000 - 10,000  | 43–86 perc | < 30 perc |

Csak a **Synchez hozzárendelt** felhasználók és csoportok esetében a következő képletekkel határozható meg a várható kezdeti ciklusok hozzávetőleges minimális és maximális **ideje:**

- Minimális perc = 0,01 x [Hozzárendelt felhasználók, csoportok és csoporttagok száma]
- Maximális perc = 0,08 x [Hozzárendelt felhasználók, csoportok és csoporttagok száma]

A kezdeti ciklus befejezéséhez szükséges időt befolyásoló **tényezők összegzése:**

- A kiépítés hatókörében található felhasználók és csoportok teljes száma.

- A forrásrendszerben (Azure AD) jelen található felhasználók, csoportok és csoporttagok teljes száma.

- Azt határozza meg, hogy a kiépítés hatókörében lévő felhasználók egyeznek-e a célalkalmazás meglévő felhasználóival, vagy első alkalommal kell létrehozni őket. Azok a szinkronizálási feladatok, amelyekhez az  összes felhasználó először jött létre, körülbelül kétszer annyi ideig tart, amíg azok a szinkronizálási feladatok, amelyekhez az összes felhasználó megfelel a meglévő felhasználóknak.

- A kiépítési [naplókban előforduló hibák száma.](check-status-user-account-provisioning.md) A teljesítmény lassabb, ha sok hiba történik, és a kiépítési szolgáltatás karanténba került. 

- A célrendszer által megvalósított kérelmek sebességkorlátozása és szabályozása. Egyes célrendszerek a kérelmek sebességének korlátozását és szabályozását valósítják meg, ami hatással lehet a nagy szinkronizálási műveletek teljesítményére. Ilyen körülmények között a túl sok kérést túl gyorsan megkapó alkalmazás lelassíthatja a válaszarányt, vagy bezárhatja a kapcsolatot. A teljesítmény javítása érdekében az összekötőnek úgy kell módosítania az adatokat, hogy nem küldi el gyorsabban az alkalmazáskéréseket, mint amennyit az alkalmazás fel tud őket feldolgozni. Ezt a módosítást a Microsoft által készült kiépítési összekötők teszik lehetővé. 

- A hozzárendelt csoportok száma és mérete. A hozzárendelt csoportok szinkronizálása több időt vesz igénybe, mint a felhasználók szinkronizálása. A hozzárendelt csoportok száma és mérete is hatással van a teljesítményre. Ha egy alkalmazáson [engedélyezve](customize-application-attributes.md#editing-group-attribute-mappings)vannak a csoportobjektum-szinkronizálás leképezései, a rendszer a csoporttulajdonságokat, például a csoportneveket és a tagságokat is szinkronizálja a felhasználók mellett. Ezek a további szinkronizálások több időt fognak igénybe venni, mint a felhasználói objektumok szinkronizálása.

- Ha a teljesítmény problémává válik, és megpróbálja kiépítni a bérlőben a felhasználók és csoportok többségét, használjon hatókörszűrőket. A hatókörszűrőkkel finomhangolhatja a kiépítési szolgáltatás által az Azure AD-ból kinyert adatokat a felhasználók adott attribútumértékek alapján való szűrésével. A hatókörszűrőkkel kapcsolatos további információkért lásd: Attribútumalapú alkalmazás kiépítése [hatókörszűrőkkel.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

## <a name="next-steps"></a>Következő lépések
[Felhasználók átadásának és megszüntetésének automatizálása az SaaS-alkalmazásokban az Azure Active Directoryval](user-provisioning.md)