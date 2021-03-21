---
title: 'Azure AD Connect: a LocalDB 10 GB-os korlátjának helyreállítása | Microsoft Docs'
description: Ez a témakör azt ismerteti, hogyan lehet helyreállítani Azure AD Connect szinkronizációs szolgáltatást, ha LocalDB 10GB-korláttal kapcsolatos problémát tapasztal.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e10aa5d96722b414d7384ceb81f393575d57e2a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688773"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Helyreállítás 10 GB-ra korlátozott LocalDB-adatbázisból
Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Használhatja az Azure AD Connecttel együtt telepített alapértelmezett SQL Server 2012 Express LocalDB-t, vagy használhatja saját teljes SQL-kiszolgálóját. Az SQL Server Express 10 GB-os méretkorláttal rendelkezik. Ha a LocalDB-t használja és eléri a korlátot, az Azure AD Connect szinkronizálási szolgáltatása többé nem indul majd el, vagy nem végzi el megfelelően a szinkronizálást. Ez a cikk a helyreállítási lépéseket ismerteti.

## <a name="symptoms"></a>Hibajelenségek
Két gyakori tünet:

* Azure AD Connect szinkronizációs szolgáltatás **fut,** de nem tud szinkronizálni a *"leállított-adatbázis-lemez-teljes"* hibával.

* Azure AD Connect szinkronizálási szolgáltatás **nem indítható el**. Amikor megkísérli elindítani a szolgáltatást, az a 6323-as és a következő hibaüzenettel meghiúsul: *"a kiszolgáló hibát észlelt, mert a SQL Server nincs elegendő lemezterület."*

## <a name="short-term-recovery-steps"></a>Rövid távú helyreállítási lépések
Ez a szakasz azokat a lépéseket ismerteti, amelyekkel megadható a Azure AD Connect szinkronizálási szolgáltatáshoz szükséges adatbázis-terület a működés folytatásához. A lépések a következők:
1. [A szinkronizálási szolgáltatás állapotának meghatározása](#determine-the-synchronization-service-status)
2. [Az adatbázis zsugorítása](#shrink-the-database)
3. [Futtatási előzmények törlése](#delete-run-history-data)
4. [A futtatási előzmények adatmegőrzési időszakának csökkentése](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>A szinkronizálási szolgáltatás állapotának meghatározása
Először is állapítsa meg, hogy a szinkronizálási szolgáltatás továbbra is fut-e:

1. Jelentkezzen be a Azure AD Connect-kiszolgálóra rendszergazdaként.

2. Lépjen a **szolgáltatásvezérlő kezelőjéhez**.

3. **Microsoft Azure ad szinkronizálás** állapotának bejelölése.


4. Ha fut, ne állítsa le vagy indítsa újra a szolgáltatást. Hagyja ki [az adatbázis lekicsinyítése](#shrink-the-database) lépést, és válassza a [futtatási előzmények törlése](#delete-run-history-data) lépést.

5. Ha nem fut, próbálja meg elindítani a szolgáltatást. Ha a szolgáltatás sikeresen elindul, hagyja ki [az adatbázis lekicsinyítése](#shrink-the-database) lépést, és válassza a [futtatási előzmények törlése](#delete-run-history-data) lépést. Ellenkező esetben folytassa [az adatbázis lépésének Lekicsinyítésével](#shrink-the-database) .

### <a name="shrink-the-database"></a>Az adatbázis zsugorítása
A Shrink művelettel szabadítson fel elegendő adatbázis-területet a szinkronizálási szolgáltatás elindításához. Üresen szabadít fel adatbázis-területet a szóközök eltávolításával az adatbázisban. Ez a lépés a legjobb megoldás, mivel nem garantált, hogy mindig helyre tudja állítani a helyet. Ha többet szeretne megtudni a zsugorodó műveletről, olvassa el ezt a cikket [egy adatbázis zsugorítása](/sql/relational-databases/databases/shrink-a-database)című cikkben.

> [!IMPORTANT]
> Hagyja ki ezt a lépést, ha le szeretné kérni a szinkronizálási szolgáltatást. Az SQL-adatbázis zsugorodása nem ajánlott, mivel a töredezettség megnövekedése miatt gyenge teljesítményt eredményezhet.

A Azure AD Connecthoz létrehozott adatbázis neve **AdSync**. A Shrink művelet végrehajtásához az adatbázis sysadmin vagy DBO kell bejelentkeznie. Azure AD Connect telepítésekor a következő fiókok rendszergazdai jogosultságokat kapnak:
* Helyi rendszergazdák
* A Azure AD Connect telepítésének futtatásához használt felhasználói fiók.
* A szinkronizálási szolgáltatásfiók, amelyet Azure AD Connect szinkronizációs szolgáltatás működési környezete használ.
* A telepítés során létrehozott helyi csoport ADSyncAdmins.

1. Az adatbázis biztonsági mentéséhez másolja a **AdSync. MDF** és **ADSync_log. ldf** fájlt `%ProgramFiles%\Microsoft Azure AD Sync\Data` , amely egy biztonságos helyen található.

2. Indítson el egy új PowerShell-munkamenetet.

3. Navigáljon a mappához `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn` .

4. Indítsa el a **Sqlcmd** segédprogramot a parancs futtatásával a `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>` sysadmin (rendszergazda) vagy az adatbázis dbo hitelesítő adatainak használatával.

5. Az adatbázis összezsugorodása érdekében a Sqlcmd prompton ( `1>` ) adja meg a `DBCC Shrinkdatabase(ADSync,1);` következő sort, majd utána: `GO` .

6. Ha a művelet sikeres, próbálja meg újból elindítani a szinkronizálási szolgáltatást. Ha el tudja indítani a szinkronizálási szolgáltatást, ugorjon a [futtatási előzmények adatainak törlése](#delete-run-history-data) lépésre. Ha nem, forduljon az ügyfélszolgálathoz.

### <a name="delete-run-history-data"></a>Futtatási előzmények törlése
Alapértelmezés szerint a Azure AD Connect akár hét napig is megőrzi a futtatási előzményeket. Ebben a lépésben a futtatási előzmények adatainak törlésével visszaállítjuk az adatbázis területét, hogy Azure AD Connect szinkronizációs szolgáltatás újra tudja indítani a szinkronizálást.

1. Indítsa el **synchronization Service Manager** a Start → synchronization Service.

2. Lépjen az **Operations (műveletek** ) lapra.

3. A **műveletek** területen válassza a **futtatások törlése**... lehetőséget.

4. Választhatja **a minden Futtatás törlése** vagy a **futtatások törlése előtt \<date>** lehetőséget is. Javasoljuk, hogy először törölje a két napnál régebbi futtatási előzményeket. Ha továbbra is adatbázis-mérettel kapcsolatos problémát tapasztal, válassza a **minden Futtatás törlése** lehetőséget.

### <a name="shorten-retention-period-for-run-history-data"></a>A futtatási előzmények adatmegőrzési időszakának csökkentése
Ennek a lépésnek a célja, hogy csökkentse a 10 GB-os korláttal való futás valószínűségét több szinkronizálási ciklus után.

1. Nyisson meg egy új PowerShell-munkamenetet.

2. Futtassa `Get-ADSyncScheduler` és jegyezze fel a PurgeRunHistoryInterval tulajdonságot, amely megadja az aktuális megőrzési időtartamot.

3. Futtassa `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` a parancsot a megőrzési időtartam két napra történő beállításához. Szükség szerint módosítsa a megőrzési időszakot.

## <a name="long-term-solution--migrate-to-full-sql"></a>Hosszú távú megoldás – Migrálás teljes SQL-re
Általánosságban a probléma általában azt jelzi, hogy a 10 GB-os adatbázis mérete már nem elegendő ahhoz, hogy Azure AD Connect a helyszíni Active Directory az Azure AD-vel való szinkronizálásához. Javasoljuk, hogy az SQL Server teljes verziójának használatára váltson. Az Azure AD Connect üzemelő példányának LocalDB adatbázisát nem cserélheti le közvetlenül az SQL teljes verziójának adatbázisára. Ehelyett egy új Azure AD Connect-kiszolgálót kell üzembe helyeznie az SQL teljes verziójával. Ajánlott a párhuzamos migrálás, amely során az (SQL-adatbázist tartalmazó) új Azure AD Connect-kiszolgálót átmeneti kiszolgálóként helyezi üzembe a (LocalDB-adatbázist tartalmazó) meglévő Azure AD Connect-kiszolgáló mellett. 
* A távoli SQL az Azure AD Connecttel történő konfigurálásáról tekintse meg a [Az Azure AD Connect egyéni telepítése](./how-to-connect-install-custom.md) című cikket.
* Az Azure AD Connect frissítésének párhuzamos migrálásáról tekintse meg az [Azure AD Connect: Frissítés egy előző verzióról a legújabbra](./how-to-upgrade-previous-version.md#swing-migration) című cikket.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
