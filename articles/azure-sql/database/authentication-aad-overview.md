---
title: Hitelesítés Azure Active Directory-fiókkal
description: Ismerje meg, hogyan használhatja a Azure Active Directoryt a hitelesítéshez a Azure SQL Database, az Azure SQL felügyelt példányaival és a szinapszis SQL az Azure szinapszis Analytics szolgáltatásban való használatával
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454289"
---
# <a name="use-azure-active-directory-authentication"></a>Azure Active Directory hitelesítés használata

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A Azure Active Directory (Azure AD) hitelesítés a [Azure SQL Database](sql-database-paas-overview.md), az [Azure SQL felügyelt példányának](../managed-instance/sql-managed-instance-paas-overview.md)és a [szinapszis SQL az Azure szinapszis Analyticsben](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) való csatlakozásának mechanizmusa az Azure ad-ben az identitások használatával.

> [!NOTE]
> Ez a cikk a Azure SQL Database, az SQL felügyelt példányára és az Azure szinapszis Analyticsre vonatkozik.

Az Azure Active Directory-hitelesítéssel lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. A központi azonosítófelügyelettel egyetlen helyen kezelheti az adatbázis-felhasználókat, így leegyszerűsítheti az engedélykezelést. Ez az alábbi előnyökkel jár:

- Alternatívát biztosít a SQL Server hitelesítéshez.
- Segít leállítani a felhasználói identitások elterjedését a kiszolgálók között.
- Lehetővé teszi a jelszó-elforgatást egyetlen helyen.
- Az ügyfelek külső (Azure AD-) csoportok használatával kezelhetik az adatbázis-engedélyeket.
- Az integrált Windows-hitelesítés és a Azure Active Directory által támogatott hitelesítés más formáinak engedélyezésével megtörölheti a jelszavak tárolását.
- Az Azure AD-hitelesítés a tárolt adatbázis-felhasználók használatával hitelesíti az identitásokat az adatbázis szintjén.
- Az Azure AD a SQL Database és az SQL felügyelt példányához csatlakozó alkalmazások jogkivonat-alapú hitelesítését támogatja.
- Az Azure AD-hitelesítés a következőket támogatja:
  - Azure AD-beli kizárólag Felhőbeli identitások.
  - Az Azure AD hibrid identitások, amelyek támogatják a következőket:
    - A felhőalapú hitelesítés két lehetőséggel párosul a zökkenőmentes egyszeri bejelentkezés (SSO) átmenő hitelesítés és a **jelszó-kivonatoló** hitelesítés **használatával** .
    - Összevont hitelesítés.
  - Az Azure AD-hitelesítési módszerekről és azok közül az egyikről a következő cikkben talál további információt:
    - [Válassza ki a megfelelő hitelesítési módszert a Azure Active Directory Hybrid Identity megoldáshoz](../../active-directory/hybrid/choose-ad-authn.md)

- Az Azure AD olyan SQL Server Management Studio kapcsolatokat támogat, amelyek Active Directory univerzális hitelesítést használnak, beleértve a Multi-Factor Authenticationt. Multi-Factor Authentication erős hitelesítést tartalmaz számos egyszerű ellenőrzési lehetőséggel – telefonhívást, szöveges üzenetet, intelligens kártyákat PIN-kóddal vagy Mobile apps-értesítéssel. További információ: SSMS- [támogatás az Azure AD multi-Factor Authenticationhoz Azure SQL Database, SQL felügyelt példánnyal és Azure szinapszis](authentication-mfa-ssms-overview.md)

- Az Azure AD támogatja az SQL Server Data Toolsból (SSDT) érkező, az Active Directory interaktív hitelesítést használó hasonló kapcsolatokat. További információ: [Azure Active Directory támogatás a SQL Server Data Toolsban (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Az Azure-beli virtuális gépen futó SQL Server-példányhoz való csatlakozás nem támogatott Azure Active Directory-fiókkal. Helyette használjon egy tartományi Active Directory-fiókot.  

A konfigurációs lépések a következő eljárásokat ismertetik Azure Active Directory hitelesítés konfigurálásához és használatához.

1. Hozza létre és töltse fel az Azure AD-t.
2. Nem kötelező: társítsa vagy módosítsa az Azure-előfizetéshez jelenleg társított Active Directoryt.
3. Hozzon létre egy Azure Active Directory rendszergazdát.
4. Konfigurálja az ügyfélszámítógépeket.
5. Hozzon létre tárolt adatbázis-felhasználókat az adatbázisban az Azure AD-identitásokhoz rendelve.
6. Kapcsolódjon az adatbázishoz az Azure AD-identitások használatával.

> [!NOTE]
> További információ az Azure ad létrehozásáról és feltöltéséről, majd az Azure AD és a Azure SQL Database, az SQL felügyelt példányának és a szinapszis SQL az Azure szinapszis Analyticsben való konfigurálásáról: az [Azure ad konfigurálása a Azure SQL Database](authentication-aad-configure.md)használatával.

## <a name="trust-architecture"></a>Megbízhatósági architektúra

- Az Azure AD natív felhasználói jelszavainak támogatásához csak az Azure AD, SQL Database, SQL felügyelt példány és az Azure szinapszis Felhőbeli része minősül.
- A Windows egyszeri bejelentkezéshez használt hitelesítő adatok (vagy a Windows hitelesítő adatok felhasználói/jelszavának) támogatásához használjon Azure Active Directory hitelesítő adatokat egy összevont vagy felügyelt tartományból, amely zökkenőmentes egyszeri bejelentkezésre van konfigurálva a továbbított és a jelszó kivonatos hitelesítéshez. További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](../../active-directory/hybrid/how-to-connect-sso.md).
- Az összevont hitelesítés (vagy a Windows rendszerbeli hitelesítő adatok felhasználó/jelszó) támogatásához az ADFS-blokktal folytatott kommunikáció szükséges.

Az Azure AD hibrid identitásokkal, a telepítéssel és a szinkronizálással kapcsolatos további információkért tekintse meg a következő cikkeket:

- Jelszó-kivonatolási hitelesítés – [jelszó-kivonatolási szinkronizálás megvalósítása Azure ad Connect szinkronizálással](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Átmenő hitelesítés – Azure Active Directory átmenő [hitelesítés](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Összevont hitelesítés – [Active Directory összevonási szolgáltatások (AD FS) üzembe helyezése az Azure-ban és a](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) [Azure ad Connect és az összevonás](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Az ADFS-infrastruktúrával (vagy a Windows rendszerbeli hitelesítő adatokhoz tartozó felhasználó/jelszó) összevont hitelesítésnél tekintse meg az alábbi ábrát. A nyilak a kommunikációs útvonalakat jelzik.

![HRE-hitelesítési diagram][1]

A következő ábra azokat az összevonási, megbízhatósági és üzemeltetési kapcsolatokat mutatja be, amelyek lehetővé teszik az ügyfél számára, hogy jogkivonat elküldésével csatlakozhasson egy adatbázishoz. A tokent egy Azure AD hitelesíti, és az adatbázis megbízhatónak tartja. Az 1. ügyfél a natív felhasználókkal vagy egy összevont felhasználókkal rendelkező Azure AD-val rendelkező Azure Active Directoryt jelenthet. A 2. ügyfél egy lehetséges megoldást jelent, beleértve az importált felhasználókat is, ebben a példában egy összevont Azure Active Directory származik, és az ADFS-sel szinkronizálva van Azure Active Directory. Fontos tisztában lenni azzal, hogy az Azure AD-hitelesítést használó adatbázisokhoz való hozzáféréshez szükséges, hogy az üzemeltetési előfizetés az Azure AD-hez legyen társítva. Ugyanezt az előfizetést kell használni a Azure SQL Database, az SQL felügyelt példány vagy az Azure szinapszis-erőforrások létrehozásához.

![előfizetés kapcsolata][2]

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor két rendszergazdai fiók létezik: az eredeti Azure SQL Database rendszergazdája és az Azure AD-rendszergazda. Ugyanezek a fogalmak az Azure Szinapszisra is érvényesek. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-beli adatbázis-felhasználót egy felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés egy Azure AD-felhasználó vagy egy Azure AD-csoport lehet. Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, így több Azure AD-rendszergazda is engedélyezhető a kiszolgálóhoz. A csoportfiókok rendszergazdaként való használata javítja a kezelhetőséget azáltal, hogy lehetővé teszi a csoporttagok központi hozzáadását és eltávolítását az Azure AD-ben anélkül, hogy módosítani kellene a felhasználókat vagy az engedélyeket a SQL Database vagy az Azure Szinapszisban. Egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható.

![felügyeleti struktúra][3]

## <a name="permissions"></a>Engedélyek

Új felhasználók létrehozásához engedéllyel kell rendelkeznie az `ALTER ANY USER` adatbázisban. Az `ALTER ANY USER` engedélyek bármelyik adatbázis-felhasználó számára megadhatók. Az `ALTER ANY USER` engedélyt a kiszolgálói rendszergazdai fiókok, valamint az adatbázis-felhasználók vagy az adatbázis `CONTROL ON DATABASE` `ALTER ON DATABASE` -szerepkör tagjai is megtartják `db_owner` .

A Azure SQL Database, SQL felügyelt példányban vagy Azure Szinapszisban tárolt adatbázis-felhasználó létrehozásához az Azure AD-identitás használatával csatlakoznia kell az adatbázishoz vagy a példányhoz. Az első tárolt adatbázis-felhasználó létrehozásához az adatbázishoz kell csatlakoznia egy Azure AD-rendszergazda használatával (aki az adatbázis tulajdonosa). Ez a [SQL Database vagy az Azure szinapszis Azure Active Directory hitelesítésének konfigurálása és kezelése](authentication-aad-configure.md)című cikkben látható. Az Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda Azure SQL Database, SQL felügyelt példányhoz vagy Azure Szinapszishoz lett létrehozva. Ha a Azure Active Directory-rendszergazda el lett távolítva a kiszolgálóról, a SQL Server korábban létrehozott Azure Active Directory felhasználók már nem tudnak csatlakozni az adatbázishoz a Azure Active Directory hitelesítő adataik használatával.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-funkciók és korlátozások

- A következő Azure AD-tagokat lehet kiépíteni a Azure SQL Databasehoz:

  - Natív tagok: az Azure AD-ben létrehozott tag a felügyelt tartományban vagy az ügyfél tartományában. További információ: [saját tartománynév hozzáadása az Azure ad](../../active-directory/fundamentals/add-custom-domain.md)-hez.
  - Egy Active Directory tartomány tagjai, amelyekkel összevonták a felügyelt tartományhoz tartozó Azure Active Directoryokat, és amelyek áteresztő vagy jelszó-kivonatoló hitelesítéssel történő zökkenőmentes egyszeri bejelentkezésre vannak konfigurálva. További információ: [Microsoft Azure mostantól támogatja az összevonást a Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) és az [Azure Active Directory zökkenőmentes egyszeri bejelentkezést](../../active-directory/hybrid/how-to-connect-sso.md).
  - A többi Azure AD-ből származó, natív vagy összevont tartományi tagokból importált tagok.
  - Active Directory biztonsági csoportként létrehozott csoportokat.

- A kiszolgálói szerepkörrel rendelkező csoportok részét képező Azure AD-felhasználók `db_owner` nem használhatják az **[adatbázis-hatókörrel rendelkező hitelesítő adatok létrehozása](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** Azure SQL Database és az Azure szinapszis szolgáltatáshoz. A következő hibaüzenet jelenik meg:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Adja meg a `db_owner` szerepkört közvetlenül az egyes Azure ad-felhasználók számára az **adatbázis-HATÓKÖRREL rendelkező hitelesítő adatok létrehozásakor** felmerülő problémák enyhítése érdekében.

- Ezek a rendszerfunkciók NULL értéket adnak vissza, amikor az Azure AD-rendszerbiztonsági tag alatt hajtja végre:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>SQL Managed Instance

- A [felügyelt SQL-példányok](../managed-instance/sql-managed-instance-paas-overview.md)támogatják az Azure ad-kiszolgálói rendszerbiztonsági tag (bejelentkezések) és a felhasználók támogatását.
- Az Azure ad-kiszolgáló rendszerbiztonsági tagjai (bejelentkezések) adatbázis-tulajdonosként való beállítása nem támogatott az [SQL felügyelt példányain](../managed-instance/sql-managed-instance-paas-overview.md).
  - Ennek az az előnye, hogy ha egy csoportot a kiszolgálói szerepkör részeként vesznek fel `dbcreator` , a csoportba tartozó felhasználók csatlakozhatnak az SQL felügyelt példányához, és új adatbázisokat hozhatnak létre, de nem fogják tudni elérni az adatbázist. Ennek az az oka, hogy az új adatbázis-tulajdonos a SA, nem pedig az Azure AD-felhasználó. Ez a probléma nem jelenik meg, ha az adott felhasználó hozzá van adva a `dbcreator` kiszolgálói szerepkörhöz.
- Az SQL-ügynök kezelése és a feladatok végrehajtása az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) esetében támogatott.
- Az adatbázis biztonsági mentési és visszaállítási műveleteit az Azure AD-kiszolgáló résztvevői (bejelentkezések) hajtják végre.
- Az Azure AD-kiszolgáló rendszertagjaihoz (bejelentkezésekhez) és a hitelesítési eseményekhez kapcsolódó összes utasítás naplózása támogatott.
- A rendszergazdai szerepkörrel rendelkező Azure AD-kiszolgálói rendszerbiztonsági tagok (bejelentkezések) dedikált rendszergazdai kapcsolatai támogatottak.
  - Támogatott a SQLCMD segédprogram és a SQL Server Management Studio használatával.
- A bejelentkezési eseményindítók az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) felől érkező bejelentkezési események esetén támogatottak.
- Az Service Broker-és adatbázis-levelezést egy Azure AD-kiszolgáló rendszerbiztonsági tag (login) használatával lehet beállítani.

## <a name="connect-by-using-azure-ad-identities"></a>Kapcsolódjon az Azure AD-identitások használatával

Azure Active Directory hitelesítés a következő módszereket támogatja az adatbázishoz való kapcsolódáshoz az Azure AD-identitások használatával:

- Azure Active Directory jelszó
- Integrált Azure Active Directory
- Univerzális Azure Active Directory Multi-Factor Authentication
- Alkalmazás-jogkivonat hitelesítésének használata

Az Azure AD Server-rendszerbiztonsági tag (Logins) esetében az alábbi hitelesítési módszerek támogatottak:

- Azure Active Directory jelszó
- Integrált Azure Active Directory
- Univerzális Azure Active Directory Multi-Factor Authentication

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

- A kezelhetőség növelése érdekében javasoljuk, hogy hozzon létre egy dedikált Azure AD-csoportot rendszergazdaként.
- Egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható SQL Database vagy Azure-beli szinapszis-kiszolgálóhoz.
  - Az SQL felügyelt példányaihoz tartozó Azure AD-kiszolgálói rendszerbiztonsági tag (login) hozzáadása lehetővé teszi több Azure AD-kiszolgáló rendszerbiztonsági tag (bejelentkezés) létrehozását, amelyek hozzáadhatók a `sysadmin` szerepkörhöz.
- Először csak egy Azure AD-rendszergazda csatlakozhat a kiszolgálóhoz vagy a felügyelt példányhoz egy Azure Active Directory fiók használatával. A Active Directory rendszergazda konfigurálhatja a következő Azure AD-adatbázis felhasználóit.
- Javasoljuk, hogy a kapcsolat időtúllépését 30 másodpercre állítsa be.
- SQL Server 2016 Management Studio és SQL Server Data Tools a Visual Studio 2015 (14.0.60311.1 április 2016-ös vagy újabb verziója) támogatja a Azure Active Directory hitelesítést. (Az Azure AD-hitelesítést a **.NET-keretrendszer SQLServer-adatszolgáltatója** támogatja; legalább a .NET-keretrendszer 4,6-es verziója). Ezért az eszközök és az adatrétegbeli alkalmazások (DAC és BACPAC) legújabb verziói az Azure AD-hitelesítést használhatják.
- A 15.0.1 verziótól kezdődően a [Sqlcmd segédprogram](/sql/tools/sqlcmd-utility) és a [bcp segédprogram](/sql/tools/bcp-utility) támogatja Active Directory interaktív hitelesítést multi-Factor Authentication használatával.
- A Visual Studio 2015 SQL Server Data Tools használatához legalább az Adateszközök (14.0.60311.1-verzió) április 2016-es verziója szükséges. Az Azure AD-felhasználók jelenleg nem jelennek meg a SSDT Object Explorer. Áthidaló megoldásként tekintse meg [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)felhasználóit.
- [A SQL Server rendszerhez készült Microsoft JDBC-6,0 illesztőprogram támogatja az](https://www.microsoft.com/download/details.aspx?id=11774) Azure ad-hitelesítést. Lásd még: [a kapcsolatok tulajdonságainak beállítása](/sql/connect/jdbc/setting-the-connection-properties).
- Az Azure AD-hitelesítéssel nem végezhető el a hitelesítés.
- Az Azure AD-hitelesítés a Azure SQL Database és az Azure szinapszis számára is támogatott a Azure Portal **importálási adatbázis** és az **adatbázis exportálása** lapok használatával. Az Azure AD-hitelesítéssel történő Importálás és exportálás a PowerShell-parancsokból is támogatott.
- Az Azure AD-hitelesítés támogatott a SQL Database, az SQL felügyelt példányok és az Azure szinapszis használatával a parancssori felülettel. További információ: [Azure ad-hitelesítés konfigurálása és kezelése SQL Database vagy Azure szinapszis](authentication-aad-configure.md) és [SQL Server – az SQL Server](/cli/azure/sql/server)segítségével.

## <a name="next-steps"></a>Következő lépések

- Az Azure AD-példányok létrehozásával és feltöltésével, valamint a Azure SQL Database, SQL felügyelt példánnyal vagy az Azure szinapszis szolgáltatással való konfigurálásával kapcsolatban tekintse meg a [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database, SQL felügyelt példány vagy Azure szinapszis](authentication-aad-configure.md)használatával című témakört.
- Az Azure AD Server rendszerbiztonsági példányok (bejelentkezések) SQL felügyelt példánnyal való használatával kapcsolatos oktatóanyagért lásd: [Azure ad Server rendszerbiztonsági tag (bejelentkezések) az SQL felügyelt példányával](../managed-instance/aad-security-configure-tutorial.md)
- A bejelentkezések, a felhasználók, az adatbázis-szerepkörök és a SQL Databaseban lévő engedélyek áttekintését lásd: [bejelentkezések, felhasználók, adatbázis-szerepkörök és engedélyek](logins-create-manage.md).
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](/sql/relational-databases/security/authentication-access/principals-database-engine).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](/sql/relational-databases/security/authentication-access/database-level-roles).
- Az SQL felügyelt példányához tartozó Azure AD-kiszolgálói rendszerbiztonsági tag (login) létrehozásával kapcsolatos szintaxisért lásd:  [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
