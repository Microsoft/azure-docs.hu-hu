---
title: Igény szerinti kiépítés Azure AD Connect Cloud syncben
description: Ez a cikk azt ismerteti, hogyan használható a Azure AD Connect Cloud Sync funkciója a konfigurációs változások teszteléséhez.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554275"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Igény szerinti kiépítés Azure AD Connect Cloud syncben

Az Azure Active Directory (Azure AD) Cloud Sync szolgáltatásával csatlakozhat a konfigurációs változások teszteléséhez, ha ezeket a módosításokat egyetlen felhasználóra alkalmazza. Az igény szerinti kiépítés segítségével ellenőrizheti, hogy a konfiguráción végrehajtott módosítások megfelelően lettek-e alkalmazva, és megfelelően vannak-e szinkronizálva az Azure AD-vel.  

> [!IMPORTANT] 
> Igény szerinti kiépítés használata esetén a rendszer nem alkalmazza a hatóköri szűrőket a kiválasztott felhasználóra. Igény szerinti kiépítést használhat a megadott szervezeti egységeken kívüli felhasználók számára.

## <a name="validate-a-user"></a>Felhasználó ellenőrzése
Igény szerinti kiépítés használatához kövesse az alábbi lépéseket:

1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
2.  Válassza a **Azure ad Connect** lehetőséget.
3.  Válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.

    ![A Felhőbeli szinkronizálás kezelésére mutató hivatkozást bemutató képernyőkép.](media/how-to-install/install-6.png)
4. A **konfiguráció** területen válassza ki a konfigurációt.
5. Az **Érvényesítés** területen válassza a **felhasználó kiépítése** gombot. 

   ![A felhasználó kiépítési gombját megjelenítő képernyőkép.](media/how-to-on-demand-provision/on-demand-2.png)

6. A **kiépítés igény szerint** képernyőn adja meg a felhasználó megkülönböztető nevét, és válassza a **kiépítés** gombot.  
 
   ![A felhasználónevet és a kiosztási gombot bemutató képernyőkép.](media/how-to-on-demand-provision/on-demand-3.png)
7. A kiépítés befejezését követően egy sikeres képernyő jelenik meg négy zöld pipa jel. A hibák a bal oldalon jelennek meg.

   ![A sikeres üzembe helyezést bemutató képernyőkép.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>A kiépítés részleteinek beolvasása
Most megtekintheti a felhasználói adatokat, és meghatározhatja, hogy alkalmazta-e a konfigurációban végrehajtott módosításokat. A cikk további része a sikeresen szinkronizált felhasználó részleteiben megjelenő egyes szakaszokat ismerteti.

### <a name="import-user"></a>Felhasználó importálása
A **felhasználó importálása** szakasz a Active Directoryból importált felhasználóról nyújt információkat. A felhasználó így néz ki az Azure AD-ba való kiépítés előtt. Az információk megjelenítéséhez kattintson a **részletek megtekintése** hivatkozásra.

![Képernyőfelvétel a gombról az importált felhasználó részleteinek megtekintéséhez.](media/how-to-on-demand-provision/on-demand-5.png)

Ezen információk használatával megtekintheti az importált különböző attribútumokat (és azok értékeit). Ha egyéni attribútum-hozzárendelést hozott létre, Itt láthatja az értéket.

![A felhasználói adatokat megjelenítő képernyőkép.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Annak megállapítása, hogy a felhasználó hatókörben van-e
Annak **megállapítása** , hogy a felhasználó szerepel-e a hatókör szakaszban, információt nyújt arról, hogy az Azure ad-ba importált felhasználó hatókörben van-e. Az információk megjelenítéséhez kattintson a **részletek megtekintése** hivatkozásra.

![A felhasználói hatókör részleteit megjelenítő gomb képernyőképe.](media/how-to-on-demand-provision/on-demand-7.png)

Ezen információk használatával megtekintheti, hogy a felhasználó hatókörben van-e.

![A felhasználói hatókör részleteit megjelenítő képernyőkép.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Felhasználó egyeztetése a forrás-és a célként megadott rendszer között
A **megfelelő felhasználó a forrás-és a célszámítógép között** című szakasz információt nyújt arról, hogy a felhasználó már létezik-e az Azure ad-ben, és hogy van-e kapcsolódás az új felhasználó üzembe helyezése helyett. Az információk megjelenítéséhez kattintson a **részletek megtekintése** hivatkozásra.

![Képernyőfelvétel a gombról az egyeztetett felhasználó részleteinek megtekintéséhez.](media/how-to-on-demand-provision/on-demand-8.png)

Ezen információk használatával megtekintheti, hogy talált-e egyezést, vagy új felhasználót fog-e létrehozni.

![Képernyőkép, amely a felhasználói adatokat jeleníti meg.](media/how-to-on-demand-provision/on-demand-11.png)

A megfeleltetés részletei egy üzenetet jelenítenek meg a következő három művelet egyikével:
- **Létrehozás**: létrejön egy felhasználó az Azure ad-ben.
- **Frissítés**: a rendszer a konfigurációban végrehajtott módosítások alapján frissíti a felhasználót.
- **Törlés**: eltávolít egy felhasználót az Azure ad-ből.

A végrehajtott művelet típusától függően az üzenet változhat.

### <a name="perform-action"></a>Művelet végrehajtása
A **művelet végrehajtása** szakasz a konfiguráció alkalmazása után az Azure ad-ba kiosztott vagy exportált felhasználóról nyújt információkat. A felhasználó így néz ki az Azure AD-ba való kiépítés után. Az információk megjelenítéséhez kattintson a **részletek megtekintése** hivatkozásra.

![Képernyőkép a gombról az elvégezett művelet részleteinek megtekintéséhez.](media/how-to-on-demand-provision/on-demand-9.png)

Ezen információk használatával megtekintheti az attribútumok értékeit a konfiguráció alkalmazása után. Ugyanúgy néznek ki, mint az importált elemek, vagy különbözőek? Sikerült alkalmazni a konfigurációt?  

Ez a folyamat lehetővé teszi, hogy nyomon kövesse az attribútum átalakítását, ahogy az a felhőben és az Azure AD-bérlőn halad át.

![A nyomon követett attribútumok részleteit megjelenítő képernyőkép.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Következő lépések 

- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [Azure AD Connect Cloud Sync telepítése](how-to-install.md)
 