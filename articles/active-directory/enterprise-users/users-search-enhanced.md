---
title: Felhasználói felügyeleti fejlesztések (előzetes verzió) – Azure Active Directory | Microsoft Docs
description: Leírja, hogy Azure Active Directory hogyan teszi lehetővé a felhasználók keresését, szűrését és további információit.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5038bde01a6b183a25a47f3b4e206c1ce80e6b6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98127838"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Felhasználói felügyeleti fejlesztések (előzetes verzió) Azure Active Directory

Ez a cikk azt ismerteti, hogyan használható a Azure Active Directory (Azure AD) portálon a felhasználó-felügyeleti fejlesztések előzetes verziója. A **minden felhasználó** és a **törölt felhasználók** oldal frissítve lett, hogy további információkat szolgáltasson, és megkönnyítse a felhasználók megtalálását. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az előzetes verzió változásai a következők:

- További látható felhasználói tulajdonságok, például az objektumazonosító, a címtár-szinkronizálás állapota, a létrehozás típusa és az identitás kiállítója
- A keresés lehetővé teszi az alsztringek keresését és a nevek, e-mailek és objektumazonosítók keresését
- Továbbfejlesztett szűrés felhasználói típus szerint (tag, vendég, nincs), címtár-szinkronizálási állapot, létrehozás típusa, cégnév és tartománynév
- Új rendezési képességek olyan tulajdonságokhoz, mint a név és az egyszerű Felhasználónév
- Új felhasználók száma, amelyek keresések vagy szűrők használatával frissülnek

> [!NOTE]
> Ez az előzetes verzió jelenleg nem érhető el Azure AD B2C bérlők számára.

## <a name="find-the-preview"></a>Az előnézet megkeresése

Az előzetes verzió alapértelmezés szerint be van kapcsolva, így azonnal használhatja. A legújabb funkciókat és tökéletesítéseket a **minden felhasználó** lapon, az **előzetes verziójú szolgáltatások** lehetőség kiválasztásával tekintheti meg. Az előzetes verzió részeként frissített összes oldal megjeleníti az előnézet címkét. Ha problémák merülnek fel, visszaválthat a régi felhasználói élményre:

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) , és válassza a **felhasználók** lehetőséget.
1. A **felhasználók – minden felhasználó** lapon válassza ki a szalagcímet az oldal tetején.
1. Az **előzetes verziójú szolgáltatások** ablaktáblán kapcsolja ki a **kibővített felhasználók felügyeletét** .

   ![A fokozott felhasználói felügyelet be-és kikapcsolása](./media/users-search-enhanced/enable-preview.png)

Nagyra értékeljük visszajelzését, hogy fejlesszük a tapasztalatainkat.

## <a name="more-user-properties"></a>További felhasználói tulajdonságok

Módosítottuk a **minden felhasználó** és a **törölt felhasználók** lapokon elérhető oszlopokat. A felhasználók listájának kezeléséhez megadott meglévő oszlopok mellett még néhány oszlopot is felvettünk.

### <a name="all-users-page"></a>Minden felhasználó lap

A **minden felhasználó** lapon látható felhasználói tulajdonságok a következők:

- Name (név): a felhasználó megjelenített neve.
- Egyszerű Felhasználónév: a felhasználó egyszerű felhasználóneve (UPN).
- Felhasználó típusa: tag, vendég, nincs.
- Létrehozás időpontja: a felhasználó létrehozásának dátuma és időpontja.
- Beosztás: a felhasználó beosztása.
- Részleg: az a részleg, amelyben a felhasználó működik.
- Könyvtár szinkronizálva: azt jelzi, hogy a felhasználó szinkronizálva van-e egy helyszíni címtárból.
- Identity kiállító: a felhasználói fiókba való bejelentkezéshez használt identitás kibocsátói.
- Objektum azonosítója: a felhasználó objektumazonosító.
- Létrehozás típusa: azt jelzi, hogy a felhasználói fiók hogyan lett létrehozva.
- Cégnév: annak a vállalatnak a neve, amelyhez a felhasználó társítva van.
- Meghívási állapot: a vendég felhasználó meghívásának állapota.
- Mail: a felhasználó e-mail-címe.
- Utolsó bejelentkezés: az a dátum, ameddig a felhasználó utoljára bejelentkezett. Ez a tulajdonság csak a naplók olvasására jogosult felhasználók számára látható (Reporting_ApplicationAuditLogs_Read)

![az összes felhasználó és a törölt felhasználók lapokon megjelenő új felhasználói tulajdonságok](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Törölt felhasználók lap

A **törölt felhasználók** lap tartalmazza az összes **felhasználó** lapon elérhető összes oszlopot, valamint néhány további oszlopot, nevezetesen:

- Törlés dátuma: az a dátum, amikor a felhasználót először törölték a szervezetből (a felhasználó helyreállítható).
- Végleges törlés dátuma: az a dátum, amely után a rendszer automatikusan törli a felhasználót a szervezettől.
- Eredeti egyszerű Felhasználónév: a felhasználó eredeti UPN-azonosítója, mielőtt a rendszer hozzáadja az objektum azonosítóját a törölt UPN-hez.

> [!NOTE]
> A törlési dátumok az egyezményes világidő (UTC) szerint jelennek meg.

A rendszer alapértelmezés szerint egyes oszlopokat jelenít meg. További oszlopok hozzáadásához jelölje ki az **oszlopok** elemet az oldalon, válassza ki a hozzáadni kívánt oszlopokat, majd kattintson **az OK** gombra a beállítások mentéséhez.

### <a name="identity-issuers"></a>Identitás-kibocsátók

Válasszon egy bejegyzést az **Identity kiállító** oszlopban bármely felhasználó számára a kibocsátó további részleteinek megtekintéséhez, beleértve a bejelentkezési típust és a kiállítóhoz rendelt azonosítót. Az **Identity kiállító** oszlop bejegyzéseinek értéke többértékű lehet. Ha a felhasználó identitásának több kiállítója is van, akkor az **összes felhasználó** és a **törölt felhasználók** lapokon az **Identity kiállító** oszlopban több szó jelenik meg, a részleteket tartalmazó ablaktábla pedig felsorolja az összes kiállítót.

> [!NOTE]
> A **forrás** oszlopot több oszlop váltja fel, többek között a **Létrehozás típusa**, a **címtár-szinkronizálás** és az **identitás kiállítója** részletesebb szűrés céljából.

## <a name="user-list-search"></a>Felhasználói lista keresése

Ha keresési karakterláncot ad meg, a keresés mostantól a "Start with" és az alsztring keresés használatával egyezteti a neveket, e-maileket vagy objektumazonosítók egyetlen keresésben. Ezen attribútumok bármelyikét megadhatja a keresőmezőbe, és a keresés automatikusan megkeresi az összes tulajdonságot, hogy visszaadja a megfelelő eredményeket. Az alkarakterlánc-keresés csak egész szavakon hajtható végre. Ugyanezt a keresést a **minden felhasználó** és a **törölt felhasználók** lapokon is elvégezheti.

## <a name="user-list-filtering"></a>Felhasználói lista szűrése

A szűrési képességek továbbfejlesztettek, így több szűrési lehetőség biztosítható a **minden felhasználó** és a **törölt felhasználók** oldalára. Mostantól egyszerre több tulajdonság is szűrhető, és további tulajdonságok alapján szűrhet.

### <a name="filtering-all-users-list"></a>Az összes felhasználó listájának szűrése

A **minden felhasználó** lapon található szűrhető tulajdonságok a következők:

- Felhasználó típusa: tag, vendég, nincs
- Címtárral szinkronizált állapot: igen, nem
- Létrehozás típusa: meghívás, e-mailben ellenőrzött, helyi fiók
- Létrehozás időpontja: utolsó 7, 14, 30, 90, 360 vagy >360 nappal ezelőtt
- Beosztás: adja meg a beosztás nevét
- Részleg: adja meg a részleg nevét
- Csoport: csoport keresése
- Meghívás állapota – elfogadás függőben, elfogadva
- Tartománynév: adjon meg egy tartománynevet
- Cég neve: adja meg a vállalat nevét
- Felügyeleti egység: ezzel a beállítással korlátozhatja a megtekintett felhasználók hatókörét egyetlen felügyeleti egységre. További információ: [felügyeleti egységek kezelése előzetes](../roles/administrative-units.md)verzió.

### <a name="filtering-deleted-users-list"></a>Törölt felhasználók listájának szűrése

A **törölt felhasználók** lapon további szűrők nem szerepelnek a **minden felhasználó** lapon. A **törölt felhasználók** lapon található szűrhető tulajdonságok a következők:

- Felhasználó típusa: tag, vendég, nincs
- Címtárral szinkronizált állapot: igen, nem
- Létrehozás típusa: meghívás, e-mailben ellenőrzött, helyi fiók
- Létrehozás időpontja: utolsó 7, 14, 30, 90, 360 vagy > 360 nappal ezelőtt
- Beosztás: adja meg a beosztás nevét
- Részleg: adja meg a részleg nevét
- Meghívás állapota: elfogadás függőben, elfogadva
- Törlés dátuma: utolsó 7, 14 vagy 30 nap
- Tartománynév: adjon meg egy tartománynevet
- Cég neve: adja meg a vállalat nevét
- Végleges törlés dátuma: utolsó 7, 14 vagy 30 nap

## <a name="user-list-sorting"></a>Felhasználói lista rendezése

Mostantól név és egyszerű felhasználónév alapján rendezheti a **minden felhasználó** és a **törölt felhasználók** lapokat. A **törölt felhasználók** listán a törlés dátuma is megadható.

## <a name="user-list-counts"></a>Felhasználói lista száma

A felhasználók teljes számát a **minden felhasználó** és a **törölt felhasználók** lapokon tekintheti meg. A listák keresésekor vagy szűrése során a rendszer frissíti a darabszámot, hogy tükrözze a talált felhasználók teljes számát.

![A felhasználók listájának a minden felhasználó lapján látható ábrája](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Kérdés | Válasz
-------- | ------
Miért jelenik meg a törölt felhasználó, ha az állandó törlési dátum át lett adva? | Az állandó törlés dátuma az UTC időzónában jelenik meg, így előfordulhat, hogy ez nem egyezik meg a jelenlegi időzónával. Emellett ez a dátum a legkorábbi dátum, amely után a felhasználó véglegesen törlődik a szervezetből, így továbbra is feldolgozható. A véglegesen törölt felhasználók automatikusan el lesznek távolítva a listából.
Mi történik a felhasználók és a vendégek tömeges képességeivel? | A tömeges műveletek mind elérhetők a felhasználók és a vendégek számára, beleértve a tömeges létrehozását, a tömeges meghívást, a tömeges törlést és a felhasználók letöltését. Most egyesítjük őket egy **tömeges műveletek** nevű menübe. A **tömeges műveletek** beállításai a **minden felhasználó** lap tetején találhatók.
Mi történt a forrás oszloppal? | A **forrás** oszlopot lecserélték más oszlopokra, amelyek hasonló információt biztosítanak, miközben lehetővé teszi az értékek egymástól független szűrését. Ilyenek például a **Létrehozás típusa**, a **címtárral szinkronizált** és az **identitás kiállítója**.
Mi történt a Felhasználónév oszloppal? | A **Felhasználónév** oszlop még mindig létezik, de az **egyszerű felhasználónévre** lett átnevezve. Ez jobban megfelel az adott oszlopban található információknak. Azt is láthatja, hogy a teljes egyszerű felhasználónév mostantól megjelenik a B2B vendégek számára. Ez megegyezik azzal, amit az MS Graph-ban fog kapni.  

## <a name="next-steps"></a>Következő lépések

Felhasználói műveletek

- [Profil adatainak hozzáadása vagy módosítása](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Felhasználók hozzáadása vagy törlése](../fundamentals/add-users-azure-active-directory.md)

Tömeges műveletek

- [Felhasználók listájának letöltése](users-bulk-download.md)
- [Felhasználók tömeges hozzáadása](users-bulk-add.md)
- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
