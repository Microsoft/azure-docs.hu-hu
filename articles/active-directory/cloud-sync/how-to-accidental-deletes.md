---
title: Azure AD Connect Cloud Sync – véletlen törlések
description: Ez a témakör azt ismerteti, hogyan használható a véletlen törlés funkció a törlés megakadályozására.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785185"
---
# <a name="accidental-delete-prevention"></a>Véletlen törlés megakadályozása

A következő dokumentum a Azure AD Connect Cloud Sync véletlen törlési funkcióját ismerteti.  A véletlen törlés funkció úgy lett kialakítva, hogy megvédje a véletlen konfigurációs változásokat és a helyszíni címtár azon módosításait, amelyek számos felhasználót és csoportot érintenek.  Ez a funkció lehetővé teszi a következőket:

- konfigurálhatja a véletlen törlések automatikus elkerülésének lehetőségét. 
- Állítsa be, hogy a rendszer milyen típusú objektumokat (küszöbértéket) fog érvénybe lépni 
- beállíthatja az értesítő e-mail-címet, hogy e-mailben értesítést kapjon, ha a kérdéses szinkronizálási feladatot a rendszer karanténba helyezi ebben a forgatókönyvben 

Ennek a funkciónak a használatához meg kell határoznia a küszöbértéket azon objektumok számára, amelyek törlését követően a szinkronizálás leáll.  Így ha eléri ezt a számot, a szinkronizálás leáll, és a rendszer értesítést küld a megadott e-mail-címre.  Ez az értesítés lehetővé teszi, hogy vizsgálja meg, mi történik.


## <a name="configure-accidental-delete-prevention"></a>Véletlen törlés megelőzésének konfigurálása
Az új funkció használatához kövesse az alábbi lépéseket.


1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
2.  Válassza a **Azure ad Connect** lehetőséget.
3.  Válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.
4. A **konfiguráció** területen válassza ki a konfigurációt.
5. A **Beállítások** területen adja meg a következőket:
    - **Értesítő e-mail** – értesítésekhez használt e-mail
    - **Véletlen törlések megakadályozása** – jelölje be ezt a jelölőnégyzetet a funkció engedélyezéséhez.
    - **Véletlen törlési küszöb** – Itt adhatja meg a szinkronizálás leállítására és az értesítések elküldésére szolgáló objektumok számát

![Véletlen törlések](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Helyreállítás egy véletlen törlési példányból
Ha véletlen törlést tapasztal, a kiépítési ügynök konfigurációjának állapotát fogja látni.  Ez azt jelenti, hogy **túllépte a törlési küszöbértéket**.
 
![Véletlen törlés állapota](media/how-to-accidental-deletes/delete-1.png)

Ha **túllépi a törlési küszöbértéket**, a szinkronizálási állapotra vonatkozó információk jelennek meg.  Ez további részleteket is tartalmaz. 
 
 ![Szinkronizálás állapota](media/how-to-accidental-deletes/delete-2.png)

Ha a jobb gombbal rákattint a három pontra, a következő beállításokat fogja megkapni:
 - Kiépítési napló megtekintése
 - Ügynök megtekintése
 - Törlés engedélyezése

 ![Jobb gombos kattintás](media/how-to-accidental-deletes/delete-3.png)

A **kiépítési napló megtekintése** lehetőséggel megtekintheti a **StagedDelete** -bejegyzéseket, és áttekintheti a törölt felhasználók által megadott információkat.
 
 ![Üzembehelyezési naplók](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Törlés engedélyezése

A törlés **engedélyezése** művelet törli a véletlen törlés küszöbértékét kiváltó objektumokat.  A törlések elfogadásához kövesse az alábbi eljárást.  

1. Kattintson a jobb gombbal az ellipszisekre, és válassza a **Törlés engedélyezése** lehetőséget.
2. A törlés engedélyezéséhez kattintson az **Igen** gombra a megerősítéshez.
 
 ![Igen megerősítéskor](media/how-to-accidental-deletes/delete-4.png)

3. Ekkor a rendszer megerősíti, hogy a törléseket elfogadták, és az állapota Kifogástalan állapotba kerül a következő ciklusban. 
 
 ![Törlések elfogadása](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Törlések elutasítása

Ha nem szeretné engedélyezni a törlést, a következőket kell tennie:
- a törlések forrásának vizsgálata
- javítsa ki a problémát (például a szervezeti egység kikerült a hatókörből véletlenül, és ismét felvette a hatókörbe)
- Az **Újraindítási szinkronizálás** futtatása az ügynök konfigurációjával

## <a name="next-steps"></a>Következő lépések 

- [Azure AD Connect a Cloud Sync hibaelhárítását?](how-to-troubleshoot.md)
- [Azure AD Connect Cloud Sync hibakódok](reference-error-codes.md)
 

