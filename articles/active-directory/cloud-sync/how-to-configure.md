---
title: Azure AD Connect Cloud Sync új ügynök konfigurációja
description: Ez a cikk a Cloud Sync telepítését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffdd27b67a122f82cc5fdb5568f11c85387955e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660796"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Új konfiguráció létrehozása Azure AD Connect Cloud Sync szolgáltatáshoz

Miután telepítette a Azure AD Connect kiépítési ügynököt, be kell jelentkeznie a Azure Portalba, és konfigurálnia kell. Az ügynök engedélyezéséhez kövesse az alábbi lépéseket.

## <a name="configure-provisioning"></a>Kiépítés konfigurálása
A kiépítés konfigurálásához kövesse az alábbi lépéseket.

 1. A Azure Portal válassza a **Azure Active Directory**
 2. Válassza a **Azure ad Connect** lehetőséget.
 3. Válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.

 ![Kiépítés kezelése](media/how-to-install/install-6.png)
 
 4. Válassza az **Új konfiguráció** lehetőséget.
 5. A konfiguráció képernyőn válassza ki a tartományt, és adja meg, hogy engedélyezi-e a jelszó-kivonat szinkronizálását.  Kattintson a **Létrehozás** gombra.  
 
 ![Új konfiguráció létrehozása](media/how-to-configure/configure-1.png)


 6.  Ekkor megnyílik a kiépítési konfiguráció szerkesztése képernyő.

   ![Konfiguráció szerkesztése](media/how-to-configure/con-1.png)

 7. Adja meg az **értesítő e-mailt**. Az e-mail értesítést kap, ha a kiépítés állapota nem kifogástalan.  Javasoljuk, hogy tartsa meg a **véletlen törlés** engedélyezése beállítást, és állítsa be a **véletlen törlési küszöbértéket** arra a számra, amelyre értesítést szeretne kapni.  További információ: [véletlen törlések](#accidental-deletions) alább.
 8. Helyezze át a választót az engedélyezéshez, majd válassza a mentés lehetőséget.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Hatókör-kiépítés adott felhasználókra és csoportokra
Az ügynök hatókörével a helyi Active Directory csoportok vagy szervezeti egységek használatával szinkronizálhatja az adott felhasználókat és csoportokat. Nem konfigurálhat csoportokat és szervezeti egységeket a konfiguráción belül. 

 1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
 2. Válassza a **Azure ad Connect** lehetőséget.
 3. Válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.
 4. A **konfiguráció** területen válassza ki a konfigurációt.

 ![Konfigurációs szakasz](media/how-to-configure/scope-1.png)
 
 5. A **Konfigurálás** területen válassza a **hatókör-szűrők szerkesztése** lehetőséget a konfigurációs szabály hatókörének módosításához.
 ![Hatókör szerkesztése](media/how-to-configure/scope-3.png)
 7. A jobb oldalon módosíthatja a hatókört.  Ha elkészült, kattintson a **kész**  gombra, és **mentse a mentést** .
 8. A hatókör módosítása után [újra kell indítani a kiépítés](#restart-provisioning) szolgáltatást a módosítások azonnali szinkronizálásának indításához.

## <a name="attribute-mapping"></a>Attribútumleképezés
Azure AD Connect a Cloud Sync segítségével könnyedén leképezheti az attribútumokat a helyszíni felhasználói/csoport objektumai és az Azure AD objektumai között.  Az alapértelmezett attribútum-hozzárendelések testreszabhatók az üzleti igényeknek megfelelően. Így módosíthatja vagy törölheti a meglévő attribútum-hozzárendeléseket, illetve létrehozhat új attribútum-hozzárendeléseket is.  További információ: [attribútumok leképezése](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Igény szerinti üzembe helyezés
Azure AD Connect a felhő-szinkronizálás lehetővé teszi a konfigurációs változások tesztelését, ha ezeket a módosításokat egyetlen felhasználóra vagy csoportra alkalmazza.  Ezzel ellenőrizheti, hogy a konfiguráción végrehajtott módosítások megfelelően lettek-e alkalmazva, és megfelelően szinkronizálva vannak-e az Azure AD-vel.  További információ: [igény szerinti kiépítés](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Véletlen törlések
A véletlen törlés funkció úgy lett kialakítva, hogy megvédje a véletlen konfigurációs változásokat és a helyszíni címtár azon módosításait, amelyek számos felhasználót és csoportot érintenek.  Ez a funkció lehetővé teszi a következőket:

- konfigurálhatja a véletlen törlések automatikus elkerülésének lehetőségét. 
- Állítsa be, hogy a rendszer milyen típusú objektumokat (küszöbértéket) fog érvénybe lépni 
- értesítési e-mail cím beállítása, hogy e-mailben értesítést kapjon, ha a kérdéses szinkronizálási feladatot a rendszer karanténba helyezi az adott forgatókönyv esetében 

További információ: [véletlen törlések](how-to-accidental-deletes.md)

## <a name="quarantines"></a>Karanténba tételekor
A Felhőbeli szinkronizálás figyeli a konfiguráció állapotát, és nem kifogástalan állapotú objektumokat helyez el karantén állapotban. Ha egy hiba miatt a megcélzott rendszerre irányuló hívások többsége vagy mindegyike (például érvénytelen rendszergazdai hitelesítő adatok) nem sikerül, a szinkronizálási feladat karanténként van megjelölve.  További információ: a [karanténba helyezés](how-to-troubleshoot.md#provisioning-quarantined-problems)hibaelhárítási szakasza.

## <a name="restart-provisioning"></a>Kiépítés újraindítása 
Ha nem szeretné megvárni a következő ütemezett futtatást, aktiválja a kiépítési futtatást az **Újraindítás kiépítés** gomb használatával. 
 1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
 2. Válassza a **Azure ad Connect** lehetőséget.
 3.  Válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.
 4. A **konfiguráció** területen válassza ki a konfigurációt.

   ![Konfiguráció kiválasztása a kiépítés újraindításához](media/how-to-configure/scope-1.png)

 5. A felső részen válassza a **kiépítés újraindítása** lehetőséget.

## <a name="remove-a-configuration"></a>Konfiguráció eltávolítása
A konfiguráció törléséhez kövesse az alábbi lépéseket.

 1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
 2. Válassza a **Azure ad Connect** lehetőséget.
 3. Válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.
 4. A **konfiguráció** területen válassza ki a konfigurációt.
   
   ![Konfiguráció eltávolítására szolgáló konfigurációs beállítás](media/how-to-configure/scope-1.png)

 5. A konfiguráció képernyő felső részén válassza a **Törlés** lehetőséget.

>[!IMPORTANT]
>A konfiguráció törlése előtt nincs megerősítés. Győződjön meg arról, hogy ezt a műveletet szeretné végrehajtani a **Törlés** kiválasztása előtt.


## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
