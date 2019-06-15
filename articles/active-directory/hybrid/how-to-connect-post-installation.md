---
title: 'Azure AD Connect: Következő lépések és az Azure AD Connect kezelése |} A Microsoft Docs'
description: Ismerje meg az alapértelmezett konfiguráció és üzemeltetési feladatok bővítése az Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571320"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Következő lépések és az Azure AD Connect kezelése
Ez a cikk az operatív eljárások használatával testre szabhatja az Azure Active Directory (Azure AD) Connect a szervezet igényeinek megfelelően, és a követelmények teljesítéséhez.  

## <a name="add-additional-sync-admins"></a>További Szinkronizáló rendszergazdák hozzáadása
Alapértelmezés szerint csak a telepítés és a helyi rendszergazdák végrehajtó felhasználó képesek kezelni a telepített szinkronizálási motor. További felhasználók is hozzá és felügyelhesse a szinkronizálási motor keresse meg a csoport ADSyncAdmins nevű a helyi kiszolgálón, és ehhez a csoporthoz hozzáadhatja őket.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Licencek hozzárendelése a prémium szintű Azure AD és a nagyvállalati mobilitási csomag felhasználók számára
Most, hogy a felhőbe szinkronizálta a felhasználókat, meg kell licenceket rendel hozzájuk, így is kezdheti a felhőalapú alkalmazások, például az Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Az Azure AD prémium vagy nagyvállalati mobilitási csomag licenc hozzárendelése

1. Jelentkezzen be az Azure Portalra rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az a **Active Directory** lapon, kattintson duplán a könyvtárra, amelyben a felhasználók be szeretne állítani.
4. A könyvtárlap tetején válassza a **Licencek** elemet.
5. Az a **licencek** lapon jelölje be **Active Directory Premium** vagy **nagyvállalati mobilitási csomag**, és kattintson a **hozzárendelése**.
6. A párbeszédpanelen válassza ki azokat a felhasználókat, akikhez licenceket szeretne rendelni, majd kattintson a pipa ikonra a módosítások mentéséhez.

## <a name="verify-the-scheduled-synchronization-task"></a>Ellenőrizze a beütemezett szinkronizálási feladat
Az Azure portal segítségével egy szinkronizálási állapotának ellenőrzéséhez.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Az ütemezett szinkronizálás task ellenőrzése
1. Jelentkezzen be az Azure Portalra rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. A bal oldalon válassza ki a **az Azure AD Connect**
4. A lap tetején vegye figyelembe a legutóbbi szinkronizálás.

![Címtár-szinkronizálás ideje](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Egy beütemezett szinkronizálási feladat indítása
Ha a szinkronizálási feladat futtatása van szüksége, akkor ehhez:

1. Kattintson duplán az Azure AD Connect asztali varázsló elindításához.
2. Kattintson a **Konfigurálás** elemre.
3. A feladatok képernyőn válassza ki a **szinkronizálási beállítások testreszabása** kattintson **tovább**
4. Adja meg Azure AD hitelesítő adatait
5. Kattintson a **tovább**. Kattintson a **tovább**.  Kattintson a **tovább**.
5.  Az a **készen áll a konfigurálás** képernyőn, ügyeljen arra, hogy a **indítsa el a szinkronizálást, amint a konfigurálás befejeződik** be van jelölve.
6.  Kattintson a **Konfigurálás** elemre.

Az Azure AD Connect szinkronizálása: ütemező további információkért lásd: [az Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Az Azure AD Connectben elérhető további feladatok
Az Azure AD Connect a kezdeti telepítés után mindig indíthatja a varázsló újra a az Azure AD Connect start lap vagy az asztali parancsikonjára.  Megfigyelheti, hogy megy végig a varázslón nyújt néhány új lehetőségek további feladatok formájában.  

A következő táblázat ezeket a feladatokat összegzését, és minden tevékenység rövid leírását.

![További feladatok listája](./media/how-to-connect-post-installation/addtasks2.png)

| További tevékenység | Leírás |
| --- | --- |
|**Adatvédelmi beállítások**|Tekintse meg a Microsofttal megosztott milyen telemetriai adatokat.|
|**Aktuális konfiguráció megtekintése**|Tekintse meg az aktuális Azure AD Connect-megoldással.  Ez magában foglalja az általános beállítások, a szinkronizált címtárak, és a szinkronizálási beállítások. |
| **Szinkronizálási beállítások testreszabása** |Módosítsa például további Active Directory-erdők hozzáadása a konfigurációt, vagy a szinkronizálási beállítások, például a felhasználó, csoport, eszköz vagy a jelszóvisszaírás engedélyezése az aktuális konfigurációt. |
|**Eszközbeállítások konfigurálása**|Szinkronizálási eszköz lehetőségeit|
|**Címtárséma frissítése**|Lehetővé teszi, hogy új helyszíni címtárobjektumok szinkronizáláshoz|
|**Átmeneti környezetű üzemmód konfigurálása** |Szakasz információkat, amelyek nem azonnal szinkronizálja, és nem exportálva az Azure AD vagy a helyszíni Active Directoryval.  Ezzel a funkcióval megtekintheti a szinkronizálást, mielőtt bekövetkeznének. |
|**Felhasználói bejelentkezés módosítása**|A felhasználók miként használják a bejelentkezési hitelesítési módszert|
|**Összevonási kezelése**|Az AD FS-infrastruktúra kezeléséhez, megújítja a tanúsítványokat és AD FS-kiszolgálók hozzáadása|
|**Hibaelhárítás**|Segítség az Azure AD Connect kapcsolatos hibák elhárítása|

## <a name="next-steps"></a>További lépések
Tudjon meg többet [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).
