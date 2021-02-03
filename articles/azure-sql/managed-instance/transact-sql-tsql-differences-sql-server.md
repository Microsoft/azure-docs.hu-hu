---
title: SQL Server & Azure SQL felügyelt példányának T-SQL-különbségei
description: Ez a cikk a Transact-SQL (T-SQL) közötti különbségeket ismerteti az Azure SQL felügyelt példányai és SQL Server között.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 11/10/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 66198291420d48e04df0821d8d110812d6aa01d7
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525805"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>SQL Server & Azure SQL felügyelt példányának T-SQL-különbségei
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk a felügyelt Azure SQL-példány és a SQL Server szintaxisának és viselkedésének különbségeit összegzi és ismerteti. 


Az SQL felügyelt példánya magas kompatibilitást biztosít a SQL Server adatbázis-motorral, és a legtöbb funkció támogatott egy SQL felügyelt példányban.

![Egyszerű áttelepítés SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Néhány, a felügyelt SQL-példányban bevezetett, és a SQL Serverhoz képest valamilyen viselkedési változás következik be. A különbségek a következő kategóriákba vannak osztva: <a name="Differences"></a>

- A [rendelkezésre állás](#availability) magában foglalja az [Always On rendelkezésre állási csoportok](#always-on-availability-groups) és a [biztonsági másolatok](#backup)közötti különbségeket.
- A [Biztonság](#security) magában foglalja a [naplózás](#auditing), [a tanúsítványok](#certificates), a [hitelesítő adatok](#credential), a [kriptográfiai szolgáltatók](#cryptographic-providers), a [bejelentkezések és a felhasználók](#logins-and-users), valamint a [szolgáltatás kulcsa és a szolgáltatás főkulcsa](#service-key-and-service-master-key)közötti különbségeket.
- A [konfiguráció](#configuration) magában foglalja a [puffer-bővítmények](#buffer-pool-extension), a [Rendezés](#collation), a [kompatibilitási szintek](#compatibility-levels), az [adatbázis-tükrözés](#database-mirroring), az adatbázis- [Beállítások](#database-options), a [SQL Server Agent](#sql-server-agent)és a [tábla beállításainak](#tables)különbségeit.
- A [funkciók](#functionalities) közé tartoznak a [bulk INSERT/OpenRowset](#bulk-insert--openrowset), a [CLR](#clr), a [DBCC](#dbcc), az [Elosztott tranzakciók](#distributed-transactions), a [kiterjesztett események](#extended-events), a [külső kódtárak](#external-libraries), a [FileStream és](#filestream-and-filetable)a lefoglalható, a [teljes szöveges szemantikai keresés](#full-text-semantic-search), a [csatolt kiszolgálók](#linked-servers), a [bázisok](#polybase), a [replikálás](#replication), a [visszaállítás](#restore-statement), a [Service Broker](#service-broker), a [tárolt eljárások, a függvények](#stored-procedures-functions-and-triggers)
- [Környezeti beállítások](#Environment) , például virtuális hálózatok és alhálózati konfigurációk.

Ezeknek a funkcióknak a többsége építészeti korlátozás, és a szolgáltatás funkcióit képviseli.

A felügyelt SQL-példányokban felderített ideiglenes ismert problémák, amelyeket később megoldják, a [kiadási megjegyzések lapon](../database/doc-changes-updates-release-notes.md)olvashat.

## <a name="availability"></a>Rendelkezésre állás

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On rendelkezésre állási csoportok

A [magas rendelkezésre állás](../database/high-availability-sla.md) be van építve az SQL felügyelt példányaiba, és a felhasználók nem vezérelhetők. A következő utasítások nem támogatottak:

- [VÉGPONT LÉTREHOZÁSA... DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT LÉTREHOZÁSA](/sql/t-sql/statements/create-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT MÓDOSÍTÁSA](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT ELDOBÁSA](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql) utasítás [set HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) záradéka

### <a name="backup"></a>Backup

A felügyelt SQL-példányok automatikus biztonsági mentéssel rendelkeznek, így a felhasználók teljes adatbázis `COPY_ONLY` biztonsági másolatokat hozhatnak létre. A különbözeti, a napló és a fájl pillanatképének biztonsági mentése nem támogatott.

- A felügyelt SQL-példányok esetében csak egy Azure Blob Storage-fiókra készíthet biztonsági másolatot egy példány-adatbázisról:
  - Csak `BACKUP TO URL` a támogatott.
  - `FILE`, `TAPE` és a biztonsági mentési eszközök nem támogatottak.
- Az általános lehetőségek többsége `WITH` támogatott.
  - `COPY_ONLY` kötelező.
  - `FILE_SNAPSHOT` nem támogatott.
  - A szalag beállításai: `REWIND` , `NOREWIND` ,, `UNLOAD` és `NOUNLOAD` nem támogatottak.
  - A naplózásra jellemző beállítások: `NORECOVERY` , `STANDBY` és `NO_TRUNCATE` nem támogatottak.

Korlátozások: 

- A felügyelt SQL-példányok segítségével biztonsági mentést készíthet egy példány-adatbázisról egy akár 32-es szalagos biztonsági másolatra, amely elegendő a 4 TB-ig terjedő adatbázisokhoz, ha biztonsági mentési tömörítést használ.
- Nem hajtható végre `BACKUP DATABASE ... WITH COPY_ONLY` olyan adatbázis, amely a szolgáltatás által felügyelt transzparens adattitkosítással (TDE) van titkosítva. A szolgáltatás által felügyelt TDE a biztonsági mentések belső TDE-kulccsal lesznek titkosítva. A kulcs nem exportálható, így a biztonsági mentés nem állítható vissza. Használjon automatikus biztonsági mentést és időponthoz való visszaállítást, vagy használja helyette az [ügyfél által felügyelt (BYOK) TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) . Emellett letilthatja a titkosítást az adatbázison.
- Felügyelt példányon végrehajtott natív biztonsági másolatok nem állíthatók vissza SQL Server. Ennek az az oka, hogy a felügyelt példány a SQL Server bármely verziójához képest magasabb belső adatbázis-verzióval rendelkezik.
- A biztonsági mentési sáv maximális mérete az `BACKUP` SQL felügyelt példányának parancsával 195 GB, amely a blob maximális mérete. Növelje meg a sávok számát a Backup parancsban, hogy csökkentse az egyes sávok méretét, és a korláton belül maradjon.

    > [!TIP]
    > Ha ezt a korlátozást szeretné megkerülni, amikor egy helyszíni környezetben vagy egy virtuális gépen SQL Server egy adatbázisról készít biztonsági másolatot, a következőkre van lehetősége:
    >
    > - Biztonsági mentést készíthet a biztonsági mentés `DISK` helyett `URL` .
    > - Töltse fel a biztonságimásolat-fájlokat a blob Storage-ba.
    > - Állítsa vissza az SQL felügyelt példányát.
    >
    > A `Restore` felügyelt SQL-példányon lévő parancs támogatja a nagyobb blob-méreteket a biztonságimásolat-fájlokban, mert egy másik blob-típust használ a feltöltött biztonságimásolat-fájlok tárolásához.

A T-SQL használatával történő biztonsági mentéssel kapcsolatos információkért lásd: [biztonsági mentés](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Biztonság

### <a name="auditing"></a>Naplózás

A Microsoft Azure SQL-ben és a SQL Serverban végzett naplózás közötti fő különbségek a következők:

- A felügyelt SQL-példányok esetében a naplózás a kiszolgáló szintjén működik. A `.xel` naplófájlok tárolása az Azure Blob Storage-ban történik.
- A Azure SQL Database a naplózás az adatbázis szintjén működik. A `.xel` naplófájlok tárolása az Azure Blob Storage-ban történik.
- SQL Server a helyszíni vagy virtuális gépeken a naplózás a kiszolgáló szintjén működik. Az eseményeket fájlrendszer vagy Windows-eseménynaplók tárolják.
 
Az SQL felügyelt példány XEvent-naplózása támogatja az Azure Blob Storage-célokat. A fájl-és Windows-naplók nem támogatottak.

Az `CREATE AUDIT` Azure Blob Storage-ba való naplózás szintaxisának főbb eltérései a következők:

- Új szintaxissal `TO URL` megadható az Azure Blob Storage-tároló URL-címe, ahol a `.xel` fájlok el vannak helyezve.
- A szintaxis `TO FILE` nem támogatott, mert az SQL felügyelt példánya nem fér hozzá a Windows-fájlmegosztást.

További információkért lásd: 

- [KISZOLGÁLÓ NAPLÓZÁSÁNAK LÉTREHOZÁSA](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Naplózás](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Tanúsítványok

A felügyelt SQL-példány nem fér hozzá a fájlmegosztás és a Windows-mappák eléréséhez, így a következő korlátozások érvényesek:

- A `CREATE FROM` / `BACKUP TO` fájl nem támogatott tanúsítványokhoz.
- A `CREATE` / `BACKUP` tanúsítvány `FILE` / `ASSEMBLY` nem támogatott. A titkos kulcs fájljai nem használhatók. 

Lásd: tanúsítvány és [biztonsági mentési tanúsítvány](/sql/t-sql/statements/backup-certificate-transact-sql) [létrehozása](/sql/t-sql/statements/create-certificate-transact-sql) . 
 
**Áthidaló megoldás**: a tanúsítvány biztonsági másolatának létrehozása és a biztonsági mentés visszaállítása helyett [szerezze be a tanúsítvány bináris tartalmát és titkos kulcsát, tárolja. SQL-fájlként, és hozzon létre a bináris fájlból](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Hitelesítő adat

Csak a Azure Key Vault és `SHARED ACCESS SIGNATURE` az identitások támogatottak. A Windows-felhasználók nem támogatottak.

Lásd: [hitelesítő adatok létrehozása](/sql/t-sql/statements/create-credential-transact-sql) és a [hitelesítő adatok módosítása](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kriptográfiai szolgáltatók

A felügyelt SQL-példány nem fér hozzá a fájlokhoz, így a titkosítási szolgáltatók nem hozhatók létre:

- `CREATE CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: [titkosítási szolgáltató létrehozása](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: a [kriptográfiai szolgáltató módosítása](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Bejelentkezések és felhasználók

- A, a és a használatával létrehozott SQL-bejelentkezések `FROM CERTIFICATE` `FROM ASYMMETRIC KEY` `FROM SID` támogatottak. Lásd: [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql)).
- A [bejelentkezési szintaxissal](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) létrehozott Azure Active Directory (Azure ad) kiszolgálói rendszerbiztonsági tag (bejelentkezések) vagy a [felhasználó létrehozása a bejelentkezéshez [Azure ad login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) szintaxis támogatott. Ezek a bejelentkezések a kiszolgálói szinten jönnek létre.

    Az SQL felügyelt példánya a szintaxissal támogatja az Azure AD adatbázis-rendszerbiztonsági tagokat `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Ez a szolgáltatás az Azure AD-ben tárolt adatbázis-felhasználók néven is ismert.

- A szintaxissal létrehozott Windows-bejelentkezések `CREATE LOGIN ... FROM WINDOWS` nem támogatottak. Azure Active Directory bejelentkezések és felhasználók használata.
- Az Azure AD-felhasználó, aki létrehozta a példányt, [korlátlan rendszergazdai jogosultságokkal](../database/logins-create-manage.md)rendelkezik.
- A nem rendszergazda Azure AD adatbázis-szintű felhasználókat a szintaxis használatával lehet létrehozni `CREATE USER ... FROM EXTERNAL PROVIDER` . Lásd: [felhasználó létrehozása... KÜLSŐ SZOLGÁLTATÓtól](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Az Azure AD-kiszolgálói rendszerbiztonsági tag (Logins) csak egyetlen SQL-alapú felügyelt példányon belül támogatják az SQL-funkciókat. Azok a funkciók, amelyek eltérő interakciót igényelnek, függetlenül attól, hogy ugyanazon az Azure AD-bérlőn vagy más bérlőn belül vannak, nem támogatottak az Azure AD-felhasználók számára. Ilyen funkciók például a következők:

  - SQL tranzakciós replikáció.
  - Kiszolgáló csatolása.

- Egy Azure ad-csoportra leképezett Azure AD-bejelentkezés beállítása, mivel az adatbázis tulajdonosa nem támogatott.
- Az Azure AD-kiszolgáló rendszerbiztonsági tagjainak más Azure AD-rendszerbiztonsági tag használatával történő megszemélyesítése támogatott, például a [Execute as](/sql/t-sql/statements/execute-as-transact-sql) záradékkal. A végrehajtás korlátozásként:

  - Az Azure AD-felhasználók nem támogatják a (z) rendszerbeli VÉGREHAJTÁSt, ha a név eltér a bejelentkezési névvel. Ilyen eset például, amikor a felhasználó létrehozása a [myAadUser] szintaxissal történik a LOGIN [] típusból, john@contoso.com és a megszemélyesítést a User = _myAadUser_ exec használatával kísérli meg. Amikor létrehoz egy **felhasználót** egy Azure ad-kiszolgálói rendszerbiztonsági tag (login) alapján, a **bejelentkezéshez** ugyanazt a login_name kell megadnia a user_name.
  - Csak a szerepkör részét képező SQL Server szintű rendszerbiztonsági tag (login) futhat `sysadmin` a következő, az Azure ad-résztvevőket megcélzó műveletek:

    - VÉGREHAJTÁS FELHASZNÁLÓKÉNT
    - VÉGREHAJTÁS BEJELENTKEZÉSKÉNT

  - Felhasználó megszemélyesítése a végrehajtás mint utasítással a felhasználót közvetlenül az Azure AD-kiszolgáló rendszerbiztonsági tagjához (login) kell leképezni. Azok a felhasználók, akik az Azure AD-kiszolgáló résztvevői számára leképezett Azure AD-csoportok tagjai, nem lehetnek hatékonyan megszemélyesítve a EXECUTE AS utasítással, még akkor is, ha a hívó jogosult a megszemélyesítő engedélyekre a megadott felhasználónévn.

- Az adatbázis-Exportálás/Importálás a bacpac-fájlok használatával támogatott az SQL felügyelt példányában található Azure AD-felhasználók számára a [SSMS v 18.4 vagy újabb](/sql/ssms/download-sql-server-management-studio-ssms), vagy a [SQLPackage.exe](/sql/tools/sqlpackage-download)használatával.
  - Az adatbázis-bacpac fájl a következő konfigurációkat támogatja: 
    - Adatbázis exportálása/importálása ugyanazon Azure AD-tartományban lévő különböző felügyeleti példányok között.
    - Exportáljon egy adatbázist a felügyelt példányból, és importálja SQL Database ugyanazon az Azure AD-tartományon belül. 
    - Exportáljon egy adatbázist a SQL Databaseból, és importálja az SQL felügyelt példányba ugyanazon az Azure AD-tartományon belül.
    - Exportáljon egy adatbázist a felügyelt példányból, és importálja SQL Serverre (2012-es vagy újabb verzióra).
      - Ebben a konfigurációban az összes Azure AD-felhasználó SQL Server adatbázis-rendszerbiztonsági tagként (Users) jön létre a bejelentkezések nélkül. A felhasználók típusa a következőként jelenik `SQL` meg, és látható a `SQL_USER` sys.database_principals). Engedélyeik és szerepköreik a SQL Server adatbázis-metaadatokban maradnak, és megszemélyesítésre is használhatók. Azonban nem használhatók a SQL Server való hozzáférésre és bejelentkezésre a hitelesítő adataik használatával.

- Csak az SQL felügyelt példány létesítési folyamata által létrehozott kiszolgálói szintű rendszerbiztonsági tag, a kiszolgálói szerepkörök, például a `securityadmin` vagy a `sysadmin` , illetve más bejelentkezési engedélyekkel rendelkező más bejelentkezések az SQL felügyelt példányának főadatbázisában hozhatnak létre Azure ad-kiszolgálói rendszerbiztonsági tagokat (bejelentkezéseket).
- Ha a bejelentkezés egy SQL-rendszerbiztonsági tag, csak a szerepkör részét képező bejelentkezések `sysadmin` használhatják a Create parancsot az Azure ad-fiókhoz tartozó bejelentkezések létrehozásához.
- Az Azure AD-bejelentkezésnek a felügyelt Azure SQL-példányhoz használt ugyanazon a címtáron belül kell lennie egy Azure AD-tagnak.
- Az Azure AD-kiszolgáló résztvevői (bejelentkezések) a Object Explorer SQL Server Management Studio 18,0 Preview 5 verziótól kezdődően láthatók.
- Az egymást átfedő Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) Azure AD-rendszergazdai fiókkal való használata engedélyezett. Az Azure ad-kiszolgáló résztvevői (bejelentkezések) elsőbbséget élveznek az Azure AD-rendszergazdával a rendszerbiztonsági tag feloldása és az SQL felügyelt példány engedélyeinek alkalmazása során.
- A hitelesítés során a rendszer a következő sorozatot alkalmazza a hitelesítő tag feloldására:

    1. Ha az Azure AD-fiók létezik közvetlenül leképezve az Azure AD-kiszolgáló rendszerbiztonsági tagja (login) számára, amely az "E" típus sys.server_principals szerepel, adjon hozzáférést és alkalmazza az Azure AD-kiszolgáló rendszerbiztonsági tag (login) engedélyeit.
    2. Ha az Azure AD-fiók olyan Azure ad-csoport tagja, amely az Azure ad-kiszolgáló rendszerbiztonsági tagjához (login) van leképezve, amely sys.server_principals "X" típusúként van társítva, engedélyezze a hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezési engedélyeit.
    3. Ha az Azure AD-fiók egy speciális, felügyelt SQL-példányhoz konfigurált Azure AD-rendszergazda, amely nem létezik az SQL felügyelt példány rendszernézeteiben, alkalmazza a felügyelt SQL-példányok Azure AD-rendszergazdájának speciális rögzített engedélyeit (örökölt mód).
    4. Ha az Azure AD-fiók létezik közvetlenül leképezve egy Azure AD-felhasználóhoz egy adatbázisban, amely az "E" típus sys.database_principals szerepel, adjon hozzáférést és alkalmazza az Azure AD-adatbázis felhasználójának engedélyeit.
    5. Ha az Azure AD-fiók tagja egy olyan Azure ad-csoportnak, amely egy olyan Azure AD-felhasználóhoz van hozzárendelve egy adatbázisban, amely sys.database_principals "X" típussal van leképezve, engedélyezze a hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezési engedélyeit.
    6. Ha egy Azure ad-bejelentkezés egy Azure AD-felhasználói fiókhoz vagy egy Azure AD-csoport fiókjához van rendelve, amely a hitelesítést végző felhasználóra van feloldva, az Azure AD-bejelentkezés összes engedélyét alkalmazza a rendszer.

### <a name="service-key-and-service-master-key"></a>Szolgáltatási kulcs és szolgáltatás főkulcsa

- A [főkulcs biztonsági mentése](/sql/t-sql/statements/backup-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [főkulcs visszaállítása](/sql/t-sql/statements/restore-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [szolgáltatás főkulcsának biztonsági mentése](/sql/t-sql/statements/backup-service-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [szolgáltatás főkulcsának visszaállítása](/sql/t-sql/statements/restore-service-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).

## <a name="configuration"></a>Konfiguráció

### <a name="buffer-pool-extension"></a>Puffer-készlet kiterjesztése

- A [puffer-készlet kiterjesztése](/sql/database-engine/configure-windows/buffer-pool-extension) nem támogatott.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nem támogatott. Lásd: a [kiszolgáló konfigurációjának módosítása](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Rendezés

A példányok alapértelmezett rendezése a (z) és a (z `SQL_Latin1_General_CP1_CI_AS` ) létrehozási paraméterként adható meg. Lásd: [rendezések](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Kompatibilitási szintek

- A támogatott kompatibilitási szintek a következők: 100, 110, 120, 130, 140 és 150.
- Az 100 alatti kompatibilitási szintek nem támogatottak.
- Az új adatbázisok alapértelmezett kompatibilitási szintje 140. A visszaállított adatbázisok esetében a kompatibilitási szint változatlan marad, ha 100-s vagy újabb.

Lásd: az [adatbázis kompatibilitási szintjének módosítása](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözés nem támogatott.

- `ALTER DATABASE SET PARTNER` és a `SET WITNESS` beállítások nem támogatottak.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nem támogatott.

További információ: [Alter Database set partner és set tanúsító](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) és [végpont létrehozása... DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Adatbázis-beállítások

- Több naplófájl nem támogatott.
- A memóriában tárolt objektumok nem támogatottak a általános célú szolgáltatási rétegben. 
- A általános célú-példányok száma legfeljebb 280, amely adatbázis-kiszolgálónként legfeljebb 280 fájlt jelent. A általános célú szinten található adatfájlok és naplófájlok is beleszámítanak a korlátba. [Az üzletileg kritikus szinten az adatbázis 32 767-es fájljait támogatja](./resource-limits.md#service-tier-characteristics).
- Az adatbázis nem tartalmazhat FileStream-adatkészletet tartalmazó fájlcsoportok. A visszaállítás sikertelen, ha a. bak tartalmaz egy `FILESTREAM` adatkészletet. 
- Minden fájl az Azure Blob Storage-ba kerül. A fájl i/o-és átviteli sebessége az egyes fájlok méretétől függ.

#### <a name="create-database-statement"></a>ADATBÁZIS-utasítás létrehozása

A következő korlátozások érvényesek `CREATE DATABASE` :

- A fájlok és a Fájlcsoportok nem definiálhatók. 
- A `CONTAINMENT` beállítás nem támogatott. 
- `WITH` a beállítások nem támogatottak. 
   > [!TIP]
   > Áthidaló megoldásként használja a következőt: `ALTER DATABASE` `CREATE DATABASE` az adatbázis-beállítások megadása a fájlok hozzáadásához vagy a tároló beállításához. 

- A `FOR ATTACH` beállítás nem támogatott.
- A `AS SNAPSHOT OF` beállítás nem támogatott.

További információ: [Create Database (adatbázis létrehozása](/sql/t-sql/statements/create-database-sql-server-transact-sql)).

#### <a name="alter-database-statement"></a>ALTER DATABASE utasítás

Bizonyos fájltulajdonságok nem állíthatók be és nem módosíthatók:

- A `ALTER DATABASE ADD FILE (FILENAME='path')` t-SQL-utasításban nem adható meg a fájl elérési útja. Távolítsa el `FILENAME` a parancsfájlt, mert az SQL felügyelt példánya automatikusan elhelyezi a fájlokat. 
- A fájl neve nem módosítható az utasítás használatával `ALTER DATABASE` .

Alapértelmezés szerint a következő beállítások vannak beállítva, és nem módosíthatók:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

A következő beállítások nem módosíthatók:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Egyes `ALTER DATABASE` utasítások (például a [set containering beállítása](https://docs.microsoft.com/sql/relational-databases/databases/migrate-to-a-partially-contained-database?#converting-a-database-to-partially-contained-using-transact-sql)) átmenetileg sikertelenek lehetnek, például az adatbázis automatikus biztonsági mentése vagy közvetlenül az adatbázis létrehozása után. Ebben az esetben újra `ALTER DATABASE` kell próbálkozni a következő utasítással. További információ a kapcsolódó hibaüzenetekről: [Megjegyzések szakasz](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true&tabs=sqlpool#remarks-2).

További információ: [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- A SQL Server Agent engedélyezése és letiltása jelenleg nem támogatott az SQL felügyelt példányain. Az SQL Agent folyamatosan fut.
- SQL Server Agent beállítások csak olvashatók. Az eljárás `sp_set_agent_properties` nem támogatott az SQL felügyelt példányain. 
- Feladatok
  - A T-SQL-feladatok lépései támogatottak.
  - A következő replikációs feladatok támogatottak:
    - Tranzakciónapló-olvasó
    - Pillanatkép
    - Terjesztő
  - A SSIS-feladatok lépései támogatottak.
  - Más típusú feladatok lépései jelenleg nem támogatottak:
    - Az egyesítéses replikálási feladattípus nem támogatott. 
    - A várólista-olvasó nem támogatott. 
    - A parancs rendszerhéja még nem támogatott.
  - Az SQL felügyelt példánya nem fér hozzá a külső erőforrásokhoz, például hálózati megosztások a Robocopy használatával. 
  - A SQL Server Analysis Services nem támogatott.
- Az értesítések részben támogatottak.
- Az e-mailes értesítések támogatottak, de a Database Mail profil konfigurálását igényli. SQL Server Agent csak egy Database Mail profilt használhat, ezért a hívása kötelező `AzureManagedInstance_dbmail_profile` . 
  - A személyhívó nem támogatott.
  - A NetSend nem támogatott.
  - A riasztások még nem támogatottak.
  - A proxyk nem támogatottak.
- Az Eseménynapló nem támogatott.
- Az SQL Agent-feladatok létrehozásához, módosításához vagy végrehajtásához közvetlenül hozzá kell rendelni a felhasználót az Azure AD-kiszolgáló rendszerbiztonsági képviselőjéhez (login). Azok a felhasználók, akik nem közvetlenül vannak leképezve, például olyan Azure AD-csoporthoz tartozó felhasználók, amelyek az SQL Agent-feladatok létrehozásához, módosításához vagy végrehajtásához szükséges jogosultságokkal rendelkeznek, nem tudják hatékonyan végrehajtani ezeket a műveleteket. Ennek oka a felügyelt példány megszemélyesítése és a [végrehajtás korlátozásként](#logins-and-users)történik.

A következő SQL Agent-funkciók jelenleg nem támogatottak:

- Legközelebbi
- Feladatok ütemezése üresjárati PROCESSZORon
- Ügynök engedélyezése vagy letiltása
- Riasztások

További információ a SQL Server Agentről: [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Táblák

A következő táblázat típusok nem támogatottak:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [Külső tábla](/sql/t-sql/statements/create-external-table-transact-sql) (alapszintű)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (csak általános célú szinten támogatott)

A táblázatok létrehozásával és módosításával kapcsolatos további információkért lásd: [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) és [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkciók

### <a name="bulk-insert--openrowset"></a>Tömeges Beszúrás/OPENROWSET

A felügyelt SQL-példány nem fér hozzá a fájlmegosztás és a Windows-mappák eléréséhez, így a fájlokat az Azure Blob Storage-ból kell importálni:

- `DATASOURCE` az `BULK INSERT` Azure Blob Storage-ból származó fájlok importálásakor szükséges a parancsban. Lásd: [bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` az `OPENROWSET` Azure Blob Storage-ból származó fájl tartalmának olvasása során szükséges a függvényben. Lásd: [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` Azure SQL Database, Azure SQL felügyelt példányból vagy SQL Server példányokból származó adatok olvasására használható. Más források, például Oracle-adatbázisok vagy Excel-fájlok nem támogatottak.

### <a name="clr"></a>CLR

A felügyelt SQL-példányok nem férnek hozzá a fájlmegosztás és a Windows-mappák eléréséhez, így a következő korlátozások érvényesek:

- Csak `CREATE ASSEMBLY FROM BINARY` a támogatott. Lásd: [szerelvény létrehozása bináris fájlból](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` nem támogatott. Lásd: [szerelvény létrehozása fájlból](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nem lehet hivatkozni a fájlokra. Lásd: az [Alter Assembly](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` a mellékletek nem küldhetők @file_attachments paraméterrel. A helyi fájlrendszer és a külső megosztások vagy az Azure Blob Storage nem érhetők el ebből az eljárásból.
 - Tekintse meg a `@query` paraméterrel és a hitelesítéssel kapcsolatos ismert problémákat.
 
### <a name="dbcc"></a>DBCC

A SQL Serverban engedélyezett nem dokumentált DBCC utasítások nem támogatottak az SQL felügyelt példányain.

- Csak korlátozott számú globális nyomkövetési jelző támogatott. A munkamenet-szint `Trace flags` nem támogatott. Lásd: [nyomkövetési jelzők](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- A [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) és a [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) korlátozott számú globális nyomkövetési jelzővel működik.
- A [DBCC CHECKDB utasítást](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) , REPAIR_FAST és REPAIR_REBUILD REPAIR_ALLOW_DATA_LOSS lehetőségekkel nem használható, mert az adatbázis nem állítható be a `SINGLE_USER` mode-ban – lásd: [adatbázis-eltérések módosítása](#alter-database-statement). Az adatbázis potenciális sérülését az Azure-támogatási csapat kezeli. Vegye fel a kapcsolatot az Azure támogatási szolgálatával, ha az adatbázis sérülését jelzi.

### <a name="distributed-transactions"></a>Elosztott tranzakciók

Az [Elosztott tranzakciók](../database/elastic-transactions-overview.md) részleges támogatása jelenleg nyilvános előzetes verzióban érhető el. A támogatott forgatókönyvek a következők:
* Azok a tranzakciók, amelyekben a résztvevők csak a [kiszolgálói megbízhatósági csoport](./server-trust-group-overview.md)részét képező Azure SQL felügyelt példányok.
* A .NET (TransactionScope Class) és a Transact-SQL által kezdeményezett tranzakciók.

Az Azure SQL felügyelt példánya jelenleg nem támogatja az MSDTC által a helyszínen vagy az Azure Virtual Machines által rendszeresen támogatott egyéb forgatókönyveket.

### <a name="extended-events"></a>Bővített események

A bővített események (XEvents) egyes Windows-specifikus céljai nem támogatottak:

- A `etw_classic_sync` cél nem támogatott. `.xel`Fájlok tárolása az Azure Blob Storage-ban. Lásd: [etw_classic_sync cél](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- A `event_file` cél nem támogatott. `.xel`Fájlok tárolása az Azure Blob Storage-ban. Lásd: [event_file cél](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Külső kódtárak

Az adatbázisbeli R-és Python-kódtárak korlátozott nyilvános előzetes verzióban támogatottak. Lásd: [Machine learning Services az Azure SQL felügyelt példányában (előzetes verzió)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>FileStream és lefilézett

- A FileStream-adatértékek nem támogatottak.
- Az adatbázis nem tartalmazhat `FILESTREAM` adatforrással rendelkező fájlcsoportok.
- `FILETABLE` nem támogatott.
- A táblák nem rendelkezhetnek `FILESTREAM` típusokkal.
- A következő függvények nem támogatottak:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

További információ: [FileStream](/sql/relational-databases/blob/filestream-sql-server) és [objektumnak](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Teljes szöveges szemantikai keresés

A [szemantikai keresés](/sql/relational-databases/search/semantic-search-sql-server) nem támogatott.

### <a name="linked-servers"></a>Társított kiszolgálók

Az SQL felügyelt példányában lévő csatolt kiszolgálók korlátozott számú célt támogatnak:

- A támogatott célok: SQL felügyelt példány, SQL Database, Azure szinapszis SQL [Server](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) nélküli és dedikált készletek, valamint SQL Server példányok. 
- Az elosztott írható tranzakciók csak felügyelt példányok között lehetségesek. További információ: [Elosztott tranzakciók](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview). Az MS DTC azonban nem támogatott.
- A nem támogatott célok a fájlok, a Analysis Services és az egyéb RDBMS. Próbálja meg natív CSV-importálást használni az Azure Blob Storage használatával `BULK INSERT` vagy a `OPENROWSET` fájlok importálása helyett, vagy töltsön le fájlokat egy [kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/).

Műveletek: 

- A [példányok közötti](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview) írási tranzakciók csak felügyelt példányok esetén támogatottak.
- `sp_dropserver` egy csatolt kiszolgáló eldobása esetén támogatott. Lásd: [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A `OPENROWSET` függvényt csak SQL Server példányokon lehet lekérdezéseket végrehajtani. Lehetnek felügyelt, helyszíni vagy virtuális gépek. Lásd: [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` függvényt csak SQL Server példányokon lehet lekérdezéseket végrehajtani. Lehetnek felügyelt, helyszíni vagy virtuális gépek. `SQLNCLI`Szolgáltatóként csak a, `SQLNCLI11` , és `SQLOLEDB` értékek támogatottak. Például: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Lásd: [index](/sql/t-sql/functions/opendatasource-transact-sql).
- A csatolt kiszolgálók nem használhatók a hálózati megosztásokból származó fájlok (Excel, CSV) olvasásához. Próbáljon meg olyan [bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) , amely CSV-fájlokat olvas az Azure Blob Storageból vagy egy olyan [csatolt kiszolgálóról, amely a szinapszis Analytics kiszolgáló nélküli SQL-készletére hivatkozik](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/). A kérelmek nyomon követése az [SQL felügyelt példányának visszajelzési elemében](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Az egyetlen elérhető külső forrás a RDBMS (nyilvános előzetes verzió) az Azure SQL Database-be, az Azure SQL felügyelt példányára és az Azure szinapszis-készletre. Olyan [külső táblát is használhat, amely a szinapszis Analytics kiszolgáló nélküli SQL-készletére hivatkozik](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) , és megkerülő megoldásként használja az Azure Storage-ból közvetlenül beolvasott albase külső táblákat. Az Azure SQL felügyelt példányában a kapcsolódó kiszolgálókat a [szinapszis Analytics szolgáltatásban található kiszolgáló nélküli SQL-készletbe](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) , illetve az Azure Storage-beli adatSQL serverk olvasásához használhatja.
További információ [a következőről: Base](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikáció

- A pillanatképek és a kétirányú replikációs típusok támogatottak. Az egyesítéses replikáció, a társ-társ replikáció és a frissíthető előfizetések nem támogatottak.
- A [tranzakciós replikáció](replication-transactional-overview.md) a FELÜGYELt SQL-példány nyilvános előzetes verziójához érhető el, néhány korlátozással:
    - A replikációs résztvevők (kiadó, terjesztő, lekéréses előfizető és leküldéses előfizető) minden típusa elhelyezhető az SQL felügyelt példányain, de a közzétevőnek és a terjesztőnek egyaránt a felhőben vagy a helyszínen kell lennie.
    - Az SQL felügyelt példánya képes kommunikálni a SQL Server legújabb verzióival. További információért tekintse meg a [támogatott verziók mátrixot](replication-transactional-overview.md#supportability-matrix) .
    - A tranzakciós replikáció [további hálózati követelményekkel](replication-transactional-overview.md#requirements)rendelkezik.

A tranzakciós replikáció konfigurálásával kapcsolatos további információkért tekintse meg a következő oktatóanyagokat:
- [Replikáció az SQL MI közzétevője és az SQL MI előfizető között](replication-between-two-instances-configure-tutorial.md)
- [Replikáció az SQL MI közzétevője, az SQL MI terjesztője és a SQL Server előfizető között](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>VISSZAÁLLÍTÁSi utasítás 

- Támogatott szintaxis:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nem támogatott szintaxis:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Forrás: 
  - `FROM URL` (Az Azure Blob Storage) az egyetlen támogatott lehetőség.
  - `FROM DISK`/`TAPE`a/Backup-eszköz nem támogatott.
  - A biztonságimásolat-készletek nem támogatottak.
- `WITH` a beállítások nem támogatottak. A visszaállítási kísérletek, például például `WITH` `DIFFERENTIAL` ,, `STATS` `REPLACE` stb., sikertelenek lesznek.
- `ASYNC RESTORE`: A visszaállítás akkor is folytatódik, ha az ügyfél-kapcsolatok megszakadnak. Ha a rendszer eldobta a-kapcsolatokat, megtekintheti a `sys.dm_operation_status` visszaállítási művelet állapotát, valamint egy létrehozási és ELdobási adatbázist. Lásd: [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

A következő adatbázis-beállítások vannak beállítva vagy felülbírálva, és később nem módosíthatók: 

- `NEW_BROKER` Ha a közvetítő nincs engedélyezve a. bak fájlban. 
- `ENABLE_BROKER` Ha a közvetítő nincs engedélyezve a. bak fájlban. 
- `AUTO_CLOSE=OFF` Ha a. bak fájlban található adatbázis `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` Ha a. bak fájlban található adatbázis `SIMPLE` vagy `BULK_LOGGED` helyreállítási mód van.
- A memóriára optimalizált fájlcsoportja hozzáadása és neve XTP, ha nem szerepel a forrás. bak fájlban. 
- A rendszer a meglévő memória-optimalizált fájlcsoportja átnevezi a XTP. 
- `SINGLE_USER` és `RESTRICTED_USER` a beállítások a következőre lesznek konvertálva: `MULTI_USER` .

Korlátozások: 

- Előfordulhat, hogy a sérült adatbázisok biztonsági másolatait a rendszer a sérülés típusától függően visszaállítja, az automatikus biztonsági mentések azonban nem lesznek elvégezve, amíg a sérülés nem lesz kijavítva. Győződjön meg arról, hogy `DBCC CHECKDB` a forrás SQL felügyelt példányán fut, és használja a biztonsági mentést a `WITH CHECKSUM` probléma megelőzése érdekében.
- A `.BAK` jelen dokumentumban ismertetett korlátozásokat (például vagy objektumokat) tartalmazó adatbázis fájljának visszaállítása `FILESTREAM` nem állítható `FILETABLE` vissza az SQL felügyelt példányán.
- `.BAK` a több biztonságimásolat-készletet tartalmazó fájlok nem állíthatók vissza. 
- `.BAK` a több naplófájlt tartalmazó fájlok nem állíthatók vissza.
- A 8 TB-nál nagyobb adatbázisok, a memóriában tárolt OLTP-objektumok, illetve a 280-nál több fájlt meghaladó fájlok száma nem állítható vissza általános célú példányon. 
- A 4 TB-nál nagyobb vagy memóriában lévő OLTP-objektumokba tartozó, az [erőforrás-korlátokban](resource-limits.md) leírt méretnél nagyobb méretű adatbázisokat tartalmazó biztonsági másolatok nem állíthatók vissza üzletileg kritikus példányon.
További információ a visszaállítási utasításokról: [Restore utasítások](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Ugyanezek a korlátozások érvényesek a beépített időpontra történő visszaállítási műveletre. Például általános célú-adatbázis 4 TB-nál nagyobb mértékben nem állítható vissza üzletileg kritikus példányon. Üzletileg kritikus adatbázis memóriában tárolt OLTP-fájlokkal vagy több mint 280 fájllal nem állítható vissza általános célú példányon.

### <a name="service-broker"></a>Service Broker

A több példányban elérhető Service Broker nem támogatott:

- `sys.routes`: Előfeltételként ki kell választania a-címeket a sys. Routes elemből. A címnek minden útvonalon HELYInek kell lennie. Lásd: [sys. Routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: A (z `CREATE ROUTE` `ADDRESS` ) helyett nem használható `LOCAL` . Lásd: [útvonal létrehozása](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: A (z `ALTER ROUTE` `ADDRESS` ) helyett nem használható `LOCAL` . Lásd: [útvonal módosítása](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Tárolt eljárások, függvények és eseményindítók

- `NATIVE_COMPILATION` nem támogatott a általános célú szinten.
- A következő [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) lehetőségek nem támogatottak: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` nem támogatott. Lásd: [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` nem támogatott. Lásd: [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nem támogatottak, köztük `sp_addextendedproc` a és a `sp_dropextendedproc` . Lásd: [kiterjesztett tárolt eljárások](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` és `sp_detach_db` nem támogatott. Lásd: [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)és [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Rendszerfunkciók és változók

A következő változók, függvények és nézetek eltérő eredményeket adnak vissza:

- `SERVERPROPERTY('EngineEdition')` a 8-as értéket adja vissza. Ez a tulajdonság egyedileg azonosítja a felügyelt SQL-példányt. Lásd: [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` NULL értéket ad vissza, mert a példány fogalma a SQL Server esetében nem vonatkozik az SQL felügyelt példányára. Lásd: [SERVERPROPERTY ("példánynév")](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` egy teljes DNS "csatlakoztatható" nevet ad vissza, például my-managed-instance.wcus17662feb9ce98.database.windows.net. Lásd [: @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` egy teljes DNS "csatlakoztatható" nevet ad vissza, például `myinstance.domain.database.windows.net` a "Name" és a "data_source" tulajdonsághoz. Lásd: [sys. KISZOLGÁLÓK](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` NULL értéket ad vissza, mert az SQL Server esetében a szolgáltatás fogalma nem vonatkozik az SQL felügyelt példányára. Lásd [: @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` támogatott. NULL értéket ad vissza, ha az Azure AD-bejelentkezés nem sys.sysbejelentkezésekben. Lásd: [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` nem támogatott. A rendszer a helytelen adatmennyiséget adja vissza, ami egy ideiglenes ismert probléma. Lásd: [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Környezeti korlátozások

### <a name="subnet"></a>Alhálózat
-  Nem helyezhet el más erőforrásokat (például virtuális gépeket) abban az alhálózatban, amelyben az SQL felügyelt példányát telepítette. Ezeket az erőforrásokat egy másik alhálózattal telepítse.
- Az alhálózatnak elegendő számú elérhető [IP-címmel](connectivity-architecture-overview.md#network-requirements)kell rendelkeznie. Legalább 32 IP-címmel kell rendelkeznie az alhálózatban.
- A régiókban üzembe helyezhető virtuális mag és típusok száma bizonyos [korlátozásokkal és korlátozásokkal](resource-limits.md#regional-resource-limitations)rendelkezik.
- Van olyan [hálózati konfiguráció](connectivity-architecture-overview.md#network-requirements) , amelyet alkalmazni kell az alhálózaton.

### <a name="vnet"></a>Virtuális hálózat
- A VNet az Resource Model használatával telepíthető – a VNet klasszikus modellje nem támogatott.
- A felügyelt SQL-példányok létrehozása után az SQL felügyelt példányának vagy VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.
- Az olyan virtuális fürtökben üzemeltetett SQL felügyelt példányok esetében, amelyeket a 9/22/2020 [globális](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) társítás előtt hozott létre, nem támogatott. Ezekhez az erőforrásokhoz a ExpressRoute vagy a VNet – VNet használatával csatlakozhat a VNet-átjárók segítségével.

### <a name="failover-groups"></a>Feladatátvételi csoportok
A rendszeradatbázisokat a rendszer nem replikálja a feladatátvételi csoport másodlagos példányára. Ezért előfordulhat, hogy a rendszeradatbázisok objektumaitól függő forgatókönyvek a másodlagos példányon nem lesznek lehetségesek, kivéve, ha az objektumokat manuálisan hozták létre a másodlagos kiszolgálón.

### <a name="tempdb"></a>TEMPDB
- A maximális fájlméret `tempdb` nem lehet nagyobb, mint 24 GB általános célú szinten. Üzletileg kritikus szinten a maximális `tempdb` méretet az SQL felügyelt példányának tárolási mérete korlátozza. `Tempdb` a naplófájl mérete általános célú szinten legfeljebb 120 GB lehet. Előfordulhat, hogy egyes lekérdezések hibát jeleznek, ha legalább 24 GB-nál több adatra van szükségük, `tempdb` vagy ha több mint 120 GB adatnaplót hoznak létre.
- `Tempdb` a mindig 12 adatfájlra van felosztva: 1 elsődleges, más néven Master, adatfájl és 11 nem elsődleges adatfájl. A fájl szerkezete nem módosítható, és az új fájlok nem adhatók hozzá a következőhöz: `tempdb` . 
- A [memóriára optimalizált `tempdb` metaadatok](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15#memory-optimized-tempdb-metadata), egy új SQL Server 2019 memóriabeli adatbázis-szolgáltatás, nem támogatott.
- A modell adatbázisban létrehozott objektumok nem hozhatók létre automatikusan az `tempdb` újraindítást vagy feladatátvételt követően, mert `tempdb` nem kapják meg a kezdeti objektumok listáját a modell adatbázisából. Az `tempdb` egyes újraindítások vagy feladatátvételek után manuálisan kell létrehoznia az objektumokat.

### <a name="msdb"></a>MSDB

A felügyelt SQL-példányok következő MSDB-sémáinak a megfelelő előre definiált szerepkörökkel kell rendelkezniük:

- Általános szerepkörök
  - TargetServersRole
- [Rögzített adatbázis-szerepkörök](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail szerepkörök](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Integrációs szolgáltatások szerepkörei](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Az előre definiált szerepkör-nevek, a sémák és a séma tulajdonosainak ügyfeleink általi módosítása hatással lesz a szolgáltatás normál működésére. Az ezekkel kapcsolatos módosítások visszaállnak az előre meghatározott értékekre, amint az észlelve lett, vagy a legújabb szolgáltatás frissítése esetén a normál szolgáltatási művelet biztosítása érdekében.

### <a name="error-logs"></a>Hibanaplók

Az SQL felügyelt példánya részletes információkat helyez el a hibák naplóiban. A hibanapló számos belső rendszereseményt naplóz. Egyéni eljárással olvashatja el a nem releváns bejegyzéseket kiszűrő hibákat. További információ: [SQL felügyelt példány – sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) vagy [SQL felügyelt példányok bővítménye (előzetes verzió)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) Azure Data studiohoz.

## <a name="next-steps"></a>Következő lépések

- További információ az SQL felügyelt példányáról: [Mi az SQL felügyelt példány?](sql-managed-instance-paas-overview.md)
- A szolgáltatások és összehasonlítások listájáért lásd: az [Azure SQL felügyelt példány funkcióinak összehasonlítása](../database/features-comparison.md).
- A kiadási frissítések és az ismert problémák állapota: [SQL felügyelt példány kibocsátási megjegyzései](../database/doc-changes-updates-release-notes.md)
- Az új, felügyelt SQL-példányok létrehozásának módját bemutató rövid útmutató: [SQL felügyelt példány létrehozása](instance-create-quickstart.md).
