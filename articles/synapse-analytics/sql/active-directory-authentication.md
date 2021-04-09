---
title: Azure Active Directory
description: Ismerje meg, hogyan használható a Azure Active Directory a hitelesítéshez SQL Database, felügyelt példány és szinapszis SQL használatával
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 92d06a95dcd32501a05dfd50e81f768f59742bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674341"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Azure Active Directory hitelesítés használata a szinapszis SQL-sel való hitelesítéshez

Azure Active Directory hitelesítés olyan mechanizmus, amely az [Azure szinapszis analyticshez](../overview-faq.md) kapcsolódik az Azure Active Directory (Azure ad) identitások használatával.

Az Azure AD-hitelesítés segítségével központilag kezelheti az Azure Szinapszishoz hozzáférő felhasználói identitásokat az engedélyek kezelésének egyszerűsítése érdekében. Ez az alábbi előnyökkel jár:

- Alternatívát biztosít a normál Felhasználónév és jelszó hitelesítéshez.
- Segít leállítani a felhasználói identitások elterjedését a kiszolgálók között.
- Lehetővé teszi a jelszó elforgatását egyetlen helyen.
- Az ügyfelek külső (Azure AD-) csoportokkal kezelhetik az engedélyeket.
- Az integrált Windows-hitelesítés és a Azure Active Directory által támogatott hitelesítés más formáinak engedélyezésével megtörölheti a jelszavak tárolását.
- Az Azure AD az Azure Szinapszishoz csatlakozó alkalmazások jogkivonat-alapú hitelesítését támogatja.
- Az Azure AD-hitelesítés támogatja az ADFS (tartományi összevonás) vagy a natív felhasználói/jelszavas hitelesítést egy helyi Azure Active Directory tartományi szinkronizálás nélkül.
- Az Azure AD támogatja az SQL Server Management Studióból érkező azon kapcsolatokat, amelyek az Active Directory univerzális hitelesítést használják, amelynek része a többtényezős hitelesítés (MFA) is.  Az MFA erős hitelesítést tartalmaz számos egyszerű ellenőrzési lehetőséggel, beleértve a telefonhívást, a szöveges üzenetet, a PIN-kóddal ellátott intelligens kártyákat vagy a Mobile apps-értesítéseket. További információ: SSMS- [támogatás az Azure ad MFA-hoz a SZINAPSZIS SQL-szel](mfa-authentication.md).
- Az Azure AD támogatja az SQL Server Data Toolsból (SSDT) érkező, az Active Directory interaktív hitelesítést használó hasonló kapcsolatokat. További információért tekintse meg [az Azure Active Directory SQL Server Data Tools (SSDT) általi támogatását](/sql/ssdt/azure-active-directory?view=azure-sqldw-latest&preserve-view=true) ismertető cikket.

A konfigurációs lépések a következő eljárásokat ismertetik Azure Active Directory hitelesítés konfigurálásához és használatához.

1. Hozza létre és töltse fel az Azure AD-t.
2. Azure Active Directory identitás létrehozása
3. Szerepkör társítása létrehozott Azure Active Directory identitáshoz a szinapszis munkaterületen
4. Kapcsolódjon a szinapszis studióhoz az Azure AD-identitások használatával.

## <a name="azure-ad-pass-through-in-azure-synapse-analytics"></a>Azure AD-továbbítás az Azure szinapszis Analytics szolgáltatásban

Az Azure szinapszis Analytics lehetővé teszi, hogy a Azure Active Directory identitásával hozzáférhessen az adatokhoz a Lake-ben.

A különböző adatmotorokban tiszteletben lévő fájlokra és adatforrásokra vonatkozó hozzáférési jogosultságok meghatározása lehetővé teszi, hogy a rendszer egy olyan helyet helyezzen el az adatkezelési megoldások számára, amely az engedélyek meghatározását választja ahelyett, hogy több helyen kellene meghatározni őket.

## <a name="trust-architecture"></a>Megbízhatósági architektúra

A következő magas szintű diagram összefoglalja az Azure AD-hitelesítés és a szinapszis SQL használatával történő használatának megoldási architektúráját. Az Azure AD natív felhasználói jelszavának támogatásához csak a felhő részét és az Azure AD/szinapszis szinapszis SQL-t kell figyelembe venni. Az összevont hitelesítés (vagy a Windows rendszerbeli hitelesítő adatok felhasználó/jelszó) támogatásához az ADFS-blokktal folytatott kommunikáció szükséges. A nyilak a kommunikációs útvonalakat jelzik.

![Azure ad-hitelesítési diagram](./media/aad-authentication/1-active-directory-authentication-diagram.png)

A következő ábra azokat az összevonási, megbízhatósági és üzemeltetési kapcsolatokat mutatja be, amelyek lehetővé teszik az ügyfél számára, hogy jogkivonat elküldésével csatlakozhasson egy adatbázishoz. A tokent egy Azure AD hitelesíti, és az adatbázis megbízhatónak tartja. 

Az 1. ügyfél a natív felhasználókkal vagy egy összevont felhasználókkal rendelkező Azure AD-val rendelkező Azure Active Directoryt jelenthet. A 2. ügyfél egy lehetséges megoldást jelent, beleértve az importált felhasználókat is; Ebben a példában egy összevont Azure Active Directory, amellyel az ADFS szinkronizálva van Azure Active Directoryval. 

Fontos tisztában lenni azzal, hogy az Azure AD-hitelesítést használó adatbázisokhoz való hozzáféréshez szükséges, hogy az üzemeltetési előfizetés az Azure AD-hez legyen társítva. Ugyanezt az előfizetést kell használni a Azure SQL Database vagy dedikált SQL-készletet üzemeltető SQL Server létrehozásához.

![előfizetés kapcsolata](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor két rendszergazdai fiók létezik a szinapszis SQL-hez; az eredeti SQL Server rendszergazdája és az Azure AD rendszergazdája. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-beli adatbázis-felhasználót egy felhasználói adatbázisban. 

Az Azure AD rendszergazdai bejelentkezés egy Azure AD-felhasználó vagy egy Azure AD-csoport lehet. Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, így több Azure AD-rendszergazda is engedélyezhető a szinapszis SQL-példányhoz. 

A csoportfiókok rendszergazdaként való használata javítja a kezelhetőséget azáltal, hogy lehetővé teszi a csoporttagok központi hozzáadását és eltávolítását az Azure AD-ben anélkül, hogy módosítani kellene a felhasználókat vagy az engedélyeket az Azure szinapszis Analytics-munkaterületen. Egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható.

![felügyeleti struktúra](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Engedélyek

Új felhasználók létrehozásához engedéllyel kell rendelkeznie az `ALTER ANY USER` adatbázisban. Az `ALTER ANY USER` engedélyek bármelyik adatbázis-felhasználó számára megadhatók. Az `ALTER ANY USER` engedélyt a kiszolgálói rendszergazdai fiókok, valamint az adatbázis-felhasználók vagy az adatbázis `CONTROL ON DATABASE` `ALTER ON DATABASE` -szerepkör tagjai is megtartják `db_owner` .

Ha egy tárolt adatbázis-felhasználót szeretne létrehozni a szinapszis SQL-ben, akkor Azure AD-identitás használatával kell csatlakoznia az adatbázishoz vagy a példányhoz. Az első tárolt adatbázis-felhasználó létrehozásához az adatbázishoz kell csatlakoznia egy Azure AD-rendszergazda használatával (aki az adatbázis tulajdonosa). 

Minden Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda létre lett hozva a szinapszis SQL-hez. Ha a Azure Active Directory-rendszergazda el lett távolítva a kiszolgálóról, a szinapszis SQL-ben korábban létrehozott meglévő Azure Active Directory-felhasználók már nem tudnak csatlakozni az adatbázishoz Azure Active Directory hitelesítő adataik használatával.
 
## <a name="azure-ad-features-and-limitations"></a>Azure AD-funkciók és korlátozások

- A következő Azure AD-tagokat lehet kiépíteni a szinapszis SQL-ben:

  - Natív tagok: az Azure AD-ben létrehozott tag a felügyelt tartományban vagy az ügyfél tartományában. További információ: [saját tartománynév hozzáadása az Azure ad](../../active-directory/fundamentals/add-custom-domain.md)-hez.
  - Összevont tartomány tagjai: az Azure AD-ben egy összevont tartománnyal létrehozott tag. További információ: [Microsoft Azure mostantól támogatja a Windows Server Active Directory való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Más Azure-hirdetésekből származó, natív vagy összevont tartományi tagokból importált tagok.
  - Active Directory biztonsági csoportként létrehozott csoportokat.

- A kiszolgálói szerepkörrel rendelkező csoport részét képező Azure AD-felhasználók `db_owner` nem használhatják az **[ADATBÁZISHOZ tartozó hatókörrel rendelkező hitelesítő adatok létrehozása](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)** a szinapszis SQL-re. A következő hibaüzenet jelenik meg:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Adja meg a `db_owner` szerepkört közvetlenül az egyes Azure ad-felhasználók számára az **adatbázis-HATÓKÖRREL rendelkező hitelesítő adatok létrehozásakor** felmerülő problémák enyhítése érdekében.

- Ezek a rendszerfunkciók NULL értéket adnak vissza, amikor az Azure AD-rendszerbiztonsági tag alatt hajtja végre:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connect-using-azure-ad-identities"></a>Összekapcsolás az Azure AD-identitások használatával

Azure Active Directory hitelesítés a következő módszereket támogatja az adatbázishoz való kapcsolódáshoz az Azure AD-identitások használatával:

- Azure Active Directory jelszó
- Integrált Azure Active Directory
- Univerzális Azure Active Directory MFA-val
- Alkalmazás-jogkivonat hitelesítésének használata

Az Azure AD Server-rendszerbiztonsági tag (Logins) esetében az alábbi hitelesítési módszerek támogatottak:

- Azure Active Directory jelszó
- Integrált Azure Active Directory
- Univerzális Azure Active Directory MFA-val

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

- A kezelhetőség növelése érdekében javasoljuk, hogy hozzon létre egy dedikált Azure AD-csoportot rendszergazdaként.
- Egyszerre csak egy Azure AD-rendszergazda (egy felhasználó vagy csoport) konfigurálható a szinapszis SQL-készlethez.
  - A szinapszis SQL-hez készült Azure AD Server-rendszerbiztonsági tag (login) hozzáadása lehetővé teszi több Azure AD-kiszolgáló rendszerbiztonsági tag (bejelentkezés) létrehozását, amelyek hozzáadhatók a `sysadmin` szerepkörhöz.
- Csak a szinapszis SQL Azure AD-rendszergazdája csatlakozhat a szinapszis SQL-hez egy Azure Active Directory fiók használatával. A Active Directory rendszergazda konfigurálhatja a következő Azure AD-adatbázis felhasználóit.
- Javasoljuk, hogy a kapcsolat időtúllépését 30 másodpercre állítsa be.
- SQL Server 2016 Management Studio és SQL Server Data Tools a Visual Studio 2015 (14.0.60311.1 április 2016-ös vagy újabb verziója) támogatja a Azure Active Directory hitelesítést. (Az Azure AD-hitelesítést a **.NET-keretrendszer SQLServer-adatszolgáltatója** támogatja; legalább a .NET-keretrendszer 4,6-es verziója). Így az eszközök és adatrétegbeli alkalmazások legújabb verziói (DAC és. A BACPAC) az Azure AD-hitelesítést is használhatja.
- A 15.0.1 verziótól kezdődően a [Sqlcmd segédprogram](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) és a [BCP segédprogram](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) támogatja a többtényezős hitelesítés (MFA) Active Directoryét.
- A Visual Studio 2015 SQL Server Data Tools használatához legalább az Adateszközök (14.0.60311.1-verzió) április 2016-es verziója szükséges. Az Azure AD-felhasználók jelenleg nem jelennek meg a SSDT Object Explorerban. Áthidaló megoldásként tekintse meg [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=azure-sqldw-latest&preserve-view=true)felhasználóit.
- [A SQL Server rendszerhez készült Microsoft JDBC-6,0 illesztőprogram támogatja az](https://www.microsoft.com/download/details.aspx?id=11774) Azure ad-hitelesítést. Lásd még: [a kapcsolatok tulajdonságainak beállítása](/sql/connect/jdbc/setting-the-connection-properties?view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Következő lépések

- A szinapszis SQL hozzáférésének és vezérlésének áttekintését lásd: [SZINAPSZIS SQL-hozzáférés-vezérlés](../security/synapse-workspace-access-control-overview.md).
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](/sql/relational-databases/security/authentication-access/principals-database-engine?view=azure-sqldw-latest&preserve-view=true).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true).

