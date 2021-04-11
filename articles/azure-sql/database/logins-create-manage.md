---
title: Kiszolgáló-és adatbázis-hozzáférés engedélyezése bejelentkezések és felhasználói fiókok használatával
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Ismerje meg, hogy a Azure SQL Database, az SQL felügyelt példánya és az Azure szinapszis Hogyan hitelesíti a felhasználókat a bejelentkezések és a felhasználói fiókok használatával való hozzáféréshez. Azt is megtudhatja, hogyan adhat meg adatbázis-szerepköröket és explicit engedélyeket a bejelentkezések és a felhasználók számára a műveletek és a lekérdezési adatok elvégzéséhez.
keywords: sql database biztonság,adatbázis biztonságának felügyelete,bejelentkezési biztonság,adatbázis biztonsága,adatbázis-hozzáférés
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: a0e9cc9967509eba05bd26f3bad86eefeeb6ca3e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639469"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Adatbázis-hozzáférés engedélyezése az SQL Database-hez, a felügyelt SQL-példányhoz és az Azure Synapse Analyticshez
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a cikk a következőket ismerteti:

- A Azure SQL Database, az Azure SQL felügyelt példányának és az Azure szinapszis Analytics konfigurálásának lehetőségei lehetővé teszik a felhasználók számára a rendszergazdai feladatok elvégzését és az ezekben az adatbázisokban tárolt adatok elérését.
- A hozzáférési és engedélyezési konfiguráció a kezdeti új kiszolgáló létrehozása után.
- Bejelentkezések és felhasználói fiókok hozzáadása a főadatbázisban és a felhasználói fiókokban, majd a fiókok rendszergazdai engedélyeinek megadása.
- Felhasználói fiókok hozzáadása felhasználói adatbázisokban a bejelentkezésekhez vagy a benne foglalt felhasználói fiókokhoz társítva.
- Az adatbázis-szerepkörök és a explicit engedélyek használatával konfigurálja a felhasználói fiókokat az engedélyekkel a felhasználói adatbázisokban.

> [!IMPORTANT]
> A Azure SQL Databaseban, az Azure SQL felügyelt példányában és az Azure Szinapszisban található adatbázisokat a cikk további részében adatbázisként hivatkoznak, és a kiszolgáló arra a [kiszolgálóra](logical-servers.md) hivatkozik, amely a Azure SQL Database és az Azure szinapszis adatbázisait kezeli.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

A [**hitelesítés**](security-overview.md#authentication) az a folyamat, amellyel a felhasználó igazolni kívánja. A felhasználó felhasználói fiókkal kapcsolódik az adatbázishoz.
Amikor egy felhasználó megpróbál csatlakozni egy adatbázishoz, felhasználói fiókot és hitelesítési adatokat biztosít. A felhasználó hitelesítése a következő két hitelesítési módszer egyikével történik:

- [SQL-hitelesítés](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Ezzel a hitelesítési módszerrel a felhasználó egy felhasználói fiók nevét és a hozzá tartozó jelszót küld a kapcsolat létrehozásához. Ezt a jelszót a rendszer a főadatbázisban tárolja a bejelentkezéshez csatolt felhasználói fiókokhoz, vagy a bejelentkezéshez *nem* csatolt felhasználói fiókokat tartalmazó adatbázisban tárolja.
- [Azure Active Directory-hitelesítés](authentication-aad-overview.md)

  Ezzel a hitelesítési módszerrel a felhasználó elküld egy felhasználói fióknevet és kéréseket, hogy a szolgáltatás a Azure Active Directory (Azure AD) által tárolt hitelesítő adatokat használja.

**Bejelentkezések és felhasználók**: egy adatbázisban lévő felhasználói fiók társítható a főadatbázisban tárolt bejelentkezési azonosítóval, vagy lehet egy különálló adatbázisban tárolt Felhasználónév.

- A **Bejelentkezés** a főadatbázis egy különálló fiókja, amelyhez egy vagy több adatbázisban lévő felhasználói fiók csatolható. Ha rendelkezik bejelentkezési azonosítóval, a felhasználói fiókhoz tartozó hitelesítő adatokat a rendszer a bejelentkezési azonosítóval együtt tárolja.
- A **felhasználói fiók** minden olyan adatbázishoz tartozik, amely lehet, de nem feltétlenül szükséges egy bejelentkezési azonosítóhoz csatolva. Ha a felhasználói fiók nincs bejelentkezési azonosítóhoz rendelve, a rendszer a felhasználói fiókkal együtt tárolja a hitelesítő adatokat.

Az adatokhoz való hozzáférés [**engedélyezése**](security-overview.md#authorization) és a különféle műveletek végrehajtása adatbázis-szerepkörök és explicit engedélyek használatával történik. Az engedélyezés a felhasználóhoz rendelt engedélyekre utal, és meghatározza, hogy a felhasználó mit tehet. Az engedélyezést a felhasználói fiók adatbázis- [szerepköri tagsága](/sql/relational-databases/security/authentication-access/database-level-roles) és az [objektum szintű engedélyek](/sql/relational-databases/security/permissions-database-engine)vezérlik. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Meglévő bejelentkezések és felhasználói fiókok új adatbázis létrehozása után

Amikor először telepíti az Azure SQL-t, a bejelentkezéshez meg kell adnia egy rendszergazdai bejelentkezési azonosítót és egy hozzá tartozó jelszót. A rendszergazdai fiók neve **kiszolgáló-rendszergazda**. Az üzembe helyezés során a rendszer a következő konfigurációkat és felhasználókat a fő és a felhasználói adatbázisokban is megtörténik:

- A rendszergazdai jogosultságokkal rendelkező SQL-bejelentkezés a megadott bejelentkezési névvel hozható létre. A [Bejelentkezés](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) egy egyéni felhasználói fiók, amellyel bejelentkezhet a SQL Databaseba, az SQL felügyelt példányaiba és az Azure szinapszisba.
- Ez a bejelentkezési azonosító teljes rendszergazdai jogosultságokkal rendelkezik minden adatbázishoz [kiszolgálói szintű rendszerbiztonsági tagként](/sql/relational-databases/security/authentication-access/principals-database-engine). A bejelentkezés minden rendelkezésre álló engedéllyel rendelkezik, és nem korlátozható. A felügyelt SQL-példányokban ez a bejelentkezés a [sysadmin (rendszergazda) rögzített kiszolgálói szerepkörbe](/sql/relational-databases/security/authentication-access/server-level-roles) kerül (ez a szerepkör nem létezik a Azure SQL Database).
- A rendszer létrehoz egy nevű [felhasználói fiókot](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) `dbo` ehhez a bejelentkezéshez az egyes felhasználói adatbázisokban. A [dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) -felhasználó rendelkezik az adatbázis összes engedélyével, és a `db_owner` rögzített adatbázis-szerepkörre van leképezve. A cikk későbbi részében további rögzített adatbázis-szerepköröket is ismertetünk.

Az adatbázishoz tartozó rendszergazdai fiókok azonosításához nyissa meg a Azure Portal, és keresse meg a kiszolgáló vagy a felügyelt példány **Tulajdonságok** lapját.

![SQL Server-rendszergazdák](./media/logins-create-manage/sql-admins.png)

![Képernyőfelvétel a Tulajdonságok menüpontra.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> A rendszergazdai bejelentkezési név nem módosítható a létrehozása után. A kiszolgáló-rendszergazda jelszavának alaphelyzetbe állításához lépjen a [Azure Portalra](https://portal.azure.com), kattintson az **SQL-kiszolgálók** elemre, válassza ki a kiszolgálót a listából, majd kattintson a **jelszó alaphelyzetbe állítása** elemre. A felügyelt SQL-példány jelszavának alaphelyzetbe állításához lépjen a Azure Portalre, kattintson a példányra, majd a **jelszó alaphelyzetbe állítása** elemre. Használhatja a PowerShellt vagy az Azure CLI-t is.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Rendszergazdai jogosultságokkal rendelkező további bejelentkezések és felhasználók létrehozása

Ezen a ponton a kiszolgáló vagy a felügyelt példány csak egyetlen SQL-bejelentkezéssel és felhasználói fiókkal történő hozzáférésre van konfigurálva. Teljes vagy részleges rendszergazdai engedélyekkel rendelkező további bejelentkezések létrehozásához a következő beállítások érhetők el (a telepítési módtól függően):

- **Teljes rendszergazdai engedélyekkel rendelkező Azure Active Directory rendszergazdai fiók létrehozása**

  Engedélyezze Azure Active Directory hitelesítést, és hozzon létre egy Azure AD-rendszergazdai bejelentkezési azonosítót. Az egyik Azure Active Directory fiók az Azure SQL-telepítés rendszergazdájaként konfigurálható teljes rendszergazdai engedélyekkel. Ez a fiók lehet egy egyéni vagy egy biztonsági csoport fiók. Az Azure AD- **rendszergazdát úgy kell** konfigurálni, hogy az Azure ad-fiókokkal kapcsolódjon SQL Databasehoz, az SQL felügyelt példányához vagy az Azure szinapszishoz. Az Azure AD-hitelesítés az összes Azure SQL-alapú központi telepítési típusra való engedélyezésével kapcsolatos részletes információkért tekintse meg a következő cikkeket:

  - [Azure Active Directory hitelesítés használata SQL-alapú hitelesítéshez](authentication-aad-overview.md)
  - [Azure Active Directory hitelesítés konfigurálása és kezelése SQL-sel](authentication-aad-configure.md)

- **SQL felügyelt példányban hozzon létre teljes körű rendszergazdai engedélyekkel rendelkező SQL-bejelentkezéseket**

  - Hozzon létre egy további SQL-bejelentkezést a Master adatbázisban.
  - Adja hozzá a bejelentkezést a [sysadmin rögzített kiszolgálói szerepkörhöz](/sql/relational-databases/security/authentication-access/server-level-roles) az [Alter Server role](/sql/t-sql/statements/alter-server-role-transact-sql) utasítás használatával. Ez a bejelentkezés teljes rendszergazdai jogosultságokkal fog rendelkezni.
  - Másik lehetőségként hozzon létre egy [Azure ad-bejelentkezést](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) a [bejelentkezési szintaxis létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) paranccsal.

- **A SQL Database-ban hozzon létre SQL-bejelentkezéseket korlátozott rendszergazdai engedélyekkel**

  - Hozzon létre egy további SQL-bejelentkezést a Master adatbázisban.
  - Hozzon létre egy felhasználói fiókot az új bejelentkezéshez társított Master adatbázisban.
  - Adja hozzá a felhasználói fiókot a `dbmanager` , a `loginmanager` szerepkörhöz vagy mindkettőhöz az `master` adatbázisban az [Alter role](/sql/t-sql/statements/alter-role-transact-sql) utasítás használatával (az Azure szinapszis esetében használja a [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) utasítást).

  > [!NOTE]
  > `dbmanager``loginmanager`a és a szerepkörök **nem** vonatkoznak az SQL felügyelt példányainak központi telepítésére.

  Ezeknek a [speciális főadatbázis-szerepköröknek](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) a tagjai Azure SQL Database rendelkeznek az adatbázisok létrehozásához és kezeléséhez, illetve a bejelentkezések létrehozásához és kezeléséhez. A szerepkörbe tartozó felhasználó által létrehozott adatbázisokban `dbmanager` a tag a `db_owner` rögzített adatbázis-szerepkörre van leképezve, és a felhasználói fiók használatával tud bejelentkezni és felügyelni az adatbázist `dbo` . Ezek a szerepkörök nem rendelkeznek explicit engedélyekkel a Master adatbázison kívül.

  > [!IMPORTANT]
  > Nem hozhat létre további SQL-bejelentkezést teljes körű rendszergazdai engedélyekkel a SQL Databaseban.

## <a name="create-accounts-for-non-administrator-users"></a>Fiókok létrehozása nem rendszergazda felhasználók számára

A nem rendszergazda felhasználók számára a következő két módszer egyikét hozhatja létre:

- **Bejelentkezési azonosító létrehozása**

  Hozzon létre egy SQL-bejelentkezést a Master adatbázisban. Ezután hozzon létre egy felhasználói fiókot minden adatbázisban, amelyhez a felhasználónak hozzáférésre van szüksége, és társítsa a felhasználói fiókot a bejelentkezéshez. Ez a megközelítés akkor javasolt, ha a felhasználónak több adatbázishoz is hozzá kell férnie, és meg szeretné tartani a jelszavak szinkronizálását. Azonban ez a megközelítés összetett, ha a földrajzi replikálást használja, mivel a bejelentkezést az elsődleges kiszolgálón és a másodlagos kiszolgálón is létre kell hozni. További információt a [geo-visszaállítási vagy feladatátvételi Azure SQL Database biztonságának konfigurálása és kezelése](active-geo-replication-security-configure.md)című témakörben talál.
- **Felhasználói fiók létrehozása**

  Hozzon létre egy felhasználói fiókot abban az adatbázisban, amelyhez a felhasználónak hozzáférésre van szüksége (más néven [tárolt felhasználó](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - A SQL Database használatával mindig létrehozhat ilyen típusú felhasználói fiókot.
  - Az [Azure ad Server-rendszerbiztonsági tagokat](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)támogató SQL felügyelt példányok esetében felhasználói fiókokat hozhat létre az SQL felügyelt példányának hitelesítéséhez anélkül, hogy az adatbázis-felhasználók befoglalt adatbázis-felhasználóként jöjjenek létre.

  Ezzel a módszerrel a rendszer az egyes adatbázisokban tárolja a felhasználói hitelesítési adatokat, és automatikusan replikálja a Geo-replikált adatbázisokat. Ha azonban ugyanaz a fiók több adatbázisban is létezik, és Azure SQL-hitelesítést használ, akkor manuálisan kell megtartania a jelszavak szinkronizálását. Emellett, ha egy felhasználó különböző jelszavakkal rendelkező fiókkal rendelkezik, akkor a jelszó megemlékezése problémát jelenthet.

> [!IMPORTANT]
> Az Azure AD-identitásokhoz hozzárendelt, tárolt felhasználók létrehozásához olyan Azure AD-fiókkal kell bejelentkeznie, amely a Azure SQL Database adatbázisban található adatbázis rendszergazdája. Az SQL felügyelt példányában az engedélyekkel rendelkező SQL-bejelentkezések `sysadmin` Azure ad-bejelentkezést vagy-felhasználót is létrehozhatnak.

A bejelentkezések és a felhasználók létrehozásának módját bemutató Példákért lásd:

- [Azure SQL Database-bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)
- [Bejelentkezés létrehozása az Azure SQL felügyelt példányához](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true#examples-2)
- [Azure Szinapszishoz való bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-3)
- [Felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Azure AD-beli felhasználók létrehozása](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> A Azure SQL Database felhasználók létrehozását bemutató biztonsági oktatóanyagért lásd [: oktatóanyag: Secure Azure SQL Database](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Rögzített és egyéni adatbázis-szerepkörök használata

Miután létrehozott egy felhasználói fiókot egy adatbázisban, vagy egy bejelentkezés vagy egy tárolt felhasználó alapján, engedélyezheti, hogy a felhasználó különböző műveleteket hajtson végre, és hozzáférhessen az adatokhoz egy adott adatbázisban. A hozzáférés engedélyezéséhez a következő módszereket használhatja:

- **Rögzített adatbázis-szerepkörök**

  Adja hozzá a felhasználói fiókot egy [rögzített adatbázis-szerepkörhöz](/sql/relational-databases/security/authentication-access/database-level-roles). 9 rögzített adatbázis-szerepkör van, amelyek mindegyike meghatározott engedélyekkel rendelkezik. A leggyakoribb rögzített adatbázis-szerepkörök a következők: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** és **db_denydatareader**. A **db_owner** általában teljes körű engedélyek biztosítására szolgál néhány felhasználó számára. A többi rögzített adatbázis-szerepkör hasznos az egyszerű adatbázisok fejlesztésének gyors elkezdéséhez, de a legtöbb éles környezetbeli adatbázishoz nem ajánlott. A **db_datareader** rögzített adatbázis-szerepkör például olvasási hozzáférést biztosít az adatbázisban található összes táblához, ami nagyobb, mint a szigorúan szükséges.

  - Felhasználó hozzáadása rögzített adatbázis-szerepkörhöz:

    - Azure SQL Database használja az [Alter role](/sql/t-sql/statements/alter-role-transact-sql) utasítást. Példák: a [szerepkör-példák módosítása](/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure szinapszis, használja a [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) utasítást. Példák: [sp_addrolemember példák](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql#examples).

- **Egyéni adatbázis-szerepkör**

  Hozzon létre egy egyéni adatbázis-szerepkört a [create role](/sql/t-sql/statements/create-role-transact-sql) utasítás használatával. Az egyéni szerepkör lehetővé teszi saját, felhasználó által definiált adatbázis-szerepkörök létrehozását, és az üzleti igényeknek leginkább megfelelő jogosultságok megadását az egyes szerepkörök számára. Ezután felhasználókat adhat hozzá az egyéni szerepkörhöz. Ha a felhasználó egyszerre több szerepkörnek is tagja, akkor a rendszer összesíti az engedélyeket.
- **Engedélyek közvetlen megadása**

  Közvetlenül adja meg a felhasználói fiók [engedélyeit](/sql/relational-databases/security/permissions-database-engine) . Az SQL Database-ben több mint 100 engedély adható vagy tagadható meg külön-külön. Ezek közül számos engedély beágyazott. Egy sémában található `UPDATE` engedély például a séma minden táblájára vonatkozó `UPDATE` engedélyt tartalmazza. A legtöbb engedélyrendszerhez hasonlóan az engedély megtagadása felülírja a megadását. Az engedélyek beágyazott jellege és száma miatt lehetséges, hogy alapos tervezés szükséges az adatbázis megfelelő védelmét biztosító engedélyrendszer kialakításához. Kezdje az [Engedélyek (Adatbázismotor)](/sql/relational-databases/security/permissions-database-engine) szakaszban felsorolt engedélyek listájával, majd tekintse át az engedélyek [poszterméretű ábráját](/sql/relational-databases/security/media/database-engine-permissions.png).

## <a name="using-groups"></a>Csoportok használata

A hatékony hozzáférés-vezérlés Active Directory biztonsági csoportokhoz, illetve rögzített vagy egyéni szerepkörökhöz hozzárendelt engedélyeket használ az egyes felhasználók helyett.

- Azure Active Directory hitelesítés használatakor Azure Active Directory felhasználókat Azure Active Directory biztonsági csoportba helyezheti. Hozzon létre a csoportban egy adatbázis-felhasználót. Adjon hozzá egy vagy több adatbázis-felhasználót tagként az egyéni vagy beépített adatbázis-szerepkörökhöz az adott felhasználói csoportnak megfelelő engedélyekkel.

- SQL-hitelesítés használatakor hozzon létre tárolt adatbázis-felhasználókat az adatbázisban. Helyezzen egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe az adott felhasználói csoportnak megfelelő engedélyekkel.

  > [!NOTE]
  > Csoportokat is használhat a nem tárolt adatbázis-felhasználók számára.

Érdemes megismerkednie az alábbi funkciókkal, amelyek az engedélyek korlátozására vagy a szint emelésére is használhatók:

- A [megszemélyesítés](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) és a [modulaláírás](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) az engedélyek biztonságos átmeneti növelésére használható.
- A [sorszintű biztonság](/sql/relational-databases/security/row-level-security) használatával korlátozhatja, hogy a felhasználó mely sorokhoz férhessen hozzá.
- Az [adatmaszkolás](dynamic-data-masking-overview.md) használatával korlátozhatja a bizalmas adatok megjelenítését.
- A [tárolt eljárások](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

## <a name="next-steps"></a>Következő lépések

Az összes Azure SQL Database és SQL felügyelt példány biztonsági funkciójának áttekintését lásd: [biztonsági áttekintés](security-overview.md).
