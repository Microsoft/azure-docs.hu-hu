---
title: T-SQL-különbségek a két SQL Server & Azure SQL Managed Instance
description: Ez a cikk a Transact-SQL (T-SQL) különbségeit ismerteti a Azure SQL Managed Instance és SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/16/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: f744b718919a6da75b2064efdc163ef4618b5a7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815899"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>T-SQL-különbségek a két SQL Server & Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk összefoglalja és elmagyarázza a szintaxis és a viselkedés közötti különbségeket Azure SQL Managed Instance és SQL Server. 


SQL Managed Instance magas szintű kompatibilitást biztosít a SQL Server adatbázismotorral, és a legtöbb szolgáltatás támogatott egy SQL Managed Instance.

![Egyszerű migrálás SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

A paaS bizonyos korlátozásokat a SQL Managed Instance, valamint néhány viselkedésbeli változást a SQL Server. A különbségek a következő kategóriákba sorolhatók: <a name="Differences"></a>

- [A rendelkezésre állás](#availability) tartalmazza az [Always On rendelkezésre](#always-on-availability-groups) állási csoportok és biztonsági másolatok [közötti különbségeket.](#backup)
- [A](#security) biztonság magában foglalja [](#certificates)a naplózás, a [tanúsítványok,](#auditing)a hitelesítő [adatok,](#credential)a titkosítási [szolgáltatók,](#cryptographic-providers)a bejelentkezések és a felhasználók, valamint a szolgáltatáskulcs és a szolgáltatás [főkulcsa közötti különbségeket.](#service-key-and-service-master-key) [](#logins-and-users)
- [A](#configuration) konfiguráció tartalmazza [](#buffer-pool-extension)a pufferkészletbővítmények, a [rendezés,](#collation) [a](#compatibility-levels)kompatibilitási [szintek,](#database-mirroring)az adatbázis-tükrözés, [](#database-options)az adatbázis-beállítások, a [SQL Server Agent](#sql-server-agent)és a tábla beállításai közötti [különbségeket.](#tables)
- [](#functionalities) A funkciók közé tartozik [az BULK INSERT/OPENROWSET,](#bulk-insert--openrowset) [CLR,](#clr) [DBCC,](#dbcc) [](#external-libraries)elosztott [tranzakciók,](#distributed-transactions)bővített [események,](#extended-events)külső kódtárak, fájlstream és [FileTable,](#filestream-and-filetable)teljes szöveges [szemantikai](#full-text-semantic-search) [keresés,](#linked-servers)csatolt kiszolgálók, [PolyBase,](#polybase) [Replikáció,](#replication) [RESTORE](#restore-statement), [Service Broker](#service-broker), tárolt eljárások, függvények és [eseményindítók.](#stored-procedures-functions-and-triggers)
- [Környezeti beállítások,](#Environment) például virtuális hálózatok és alhálózat-konfigurációk.

Ezen funkciók nagy része architekturális korlátozás, és szolgáltatási jellemzőket képvisel.

A (későbbi) SQL Managed Instance ismert ideiglenes problémákat a kibocsátási megjegyzések oldalán [ismertetjük.](../database/doc-changes-updates-release-notes.md)

## <a name="availability"></a>Rendelkezésre állás

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On rendelkezésre állási csoportok

[A magas rendelkezésre állás](../database/high-availability-sla.md) a SQL Managed Instance beépített, és nem vezérelheti a felhasználók. A következő utasítások nem támogatottak:

- [VÉGPONT LÉTREHOZÁSA... FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT LÉTREHOZÁSA](/sql/t-sql/statements/create-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT MEGVÁLTOZTATÁSA](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT ELDOBÁS](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Az ALTER DATABASE utasítás [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) [záradéka](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

SQL Managed Instance automatikus biztonsági mentésekkel rendelkezik, így a felhasználók teljes körű biztonsági másolatokat `COPY_ONLY` készíthetnek az adatbázisokról. A különbségi, a napló- és a fájl-pillanatképek biztonsági mentése nem támogatott.

- A SQL Managed Instance biztonsági mentése csak egy Azure Blob Storage-fiókba lehet biztonsági mentése:
  - Csak `BACKUP TO URL` az támogatott.
  - `FILE`, és a biztonsági mentési `TAPE` eszközök nem támogatottak.
- Az általános lehetőségek `WITH` nagy része támogatott.
  - `COPY_ONLY` A kötelező.
  - `FILE_SNAPSHOT` nem támogatott.
  - A szalagbeállítások: `REWIND` , , és nem `NOREWIND` `UNLOAD` `NOUNLOAD` támogatottak.
  - Naplóspecifikus beállítások: `NORECOVERY` , és nem `STANDBY` `NO_TRUNCATE` támogatottak.

Korlátozások: 

- A SQL Managed Instance biztonsági másolatot készít egy példányadatbázisról egy legfeljebb 32 csíkos biztonsági mentésre, ami elegendő a legfeljebb 4 TB-os adatbázisokhoz biztonsági mentési tömörítés használata esetén.
- Nem hajthat végre olyan adatbázison, amely szolgáltatás által felügyelt transzparens adattitkosítás `BACKUP DATABASE ... WITH COPY_ONLY` (TDE) van titkosítva. A szolgáltatás által felügyelt TDE egy belső TDE-kulccsal kényszeríti a biztonsági másolatok titkosítását. A kulcs nem exportálható, így nem tudja visszaállítani a biztonsági mentést. Használjon automatikus biztonsági mentéseket és időponthoz időben való visszaállítást, vagy használjon ehelyett ügyfél által felügyelt [(BYOK) TDE-t.](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) Az adatbázis titkosítását is letilthatja.
- A felügyelt példányon készült natív biztonsági másolatok nem állíthatók vissza SQL Server. Ennek az az oka, hogy a felügyelt példány magasabb belső adatbázisverzióval rendelkezik, mint a SQL Server.
- A biztonsági mentési csíkok maximális mérete a parancs használatával SQL Managed Instance `BACKUP` 195 GB, amely a blobok maximális mérete. Növelje a csíkok számát a biztonsági mentési parancsban az egyes csíkok méretének csökkentéséhez, és maradjon ezen a korláton belül.

    > [!TIP]
    > Ennek a korlátozásnak a ki- és kieről való biztonsági mentésekor a következőt kell SQL Server helyszíni környezetben vagy virtuális gépen található adatbázisokról:
    >
    > - A biztonságimentés-ről `DISK` a -be, és nem a -be. `URL`
    > - Töltse fel a biztonsági mentési fájlokat a Blob Storage-be.
    > - Visszaállítás a SQL Managed Instance.
    >
    > A parancs SQL Managed Instance nagyobb blobméreteket támogat a biztonsági mentési fájlokban, mert a feltöltött biztonsági mentési fájlok tárolásához más `Restore` blobtípust használ a rendszer.

További információ a T-SQL használatával való biztonsági mentésről: [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Biztonság

### <a name="auditing"></a>Naplózás

Az SQL és a Microsoft Azure naplózása közötti legfontosabb különbségek a SQL Server:

- A SQL Managed Instance naplózás kiszolgálószinten működik. A `.xel` naplófájlok az Azure Blob Storage-ban vannak tárolva.
- A Azure SQL Database naplózás az adatbázis szintjén működik. A `.xel` naplófájlok az Azure Blob Storage-ban vannak tárolva.
- A SQL Server, a helyszínen vagy virtuális gépeken a naplózás kiszolgálószinten működik. Az eseményeket a fájlrendszer vagy a Windows eseménynaplói tárolják.
 
Az XEvent naplózása a SQL Managed Instance támogatja az Azure Blob Storage-célokat. A fájl- és Windows-naplók nem támogatottak.

Az Azure Blob Storage naplózási szintaxisának fő különbségei `CREATE AUDIT` a következőek:

- A rendszer egy új szintaxist biztosít, amely segítségével megadhatja annak az Azure Blob Storage-tárolónak az URL-címét, amelyben a `TO URL` fájlok el vannak `.xel` helyezve.
- A szintaxis `TO FILE` nem támogatott, mert SQL Managed Instance nem fér hozzá a Windows-fájlmegosztáshoz.

További információkért lásd: 

- [KISZOLGÁLÓI NAPLÓZÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Naplózás](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Tanúsítványok

SQL Managed Instance fájlmegosztásokat és Windows-mappákat nem lehet elérni, ezért a következő korlátozások érvényesek:

- A `CREATE FROM` / `BACKUP TO` fájl tanúsítványok esetében nem támogatott.
- A `CREATE` / `BACKUP` tanúsítványa `FILE` / `ASSEMBLY` nem támogatott. A titkos kulcsfájlok nem használhatók. 

Lásd: CREATE CERTIFICATE and BACKUP CERTIFICATE [(TANÚSÍTVÁNY](/sql/t-sql/statements/create-certificate-transact-sql) [ÉS BIZTONSÁGI MENTÉSI TANÚSÍTVÁNY LÉTREHOZÁSA).](/sql/t-sql/statements/backup-certificate-transact-sql) 
 
**Megkerülő** megoldás: A tanúsítvány biztonsági mentése és a biztonsági másolat visszaállítása helyett szerezze be a tanúsítvány bináris tartalmát és titkos [kulcsát, tárolja .sql-fájlként,](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)és hozzon létre bináris fájlból:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Hitelesítő adat

Csak Azure Key Vault `SHARED ACCESS SIGNATURE` identitások és identitások támogatottak. A Windows-felhasználók nem támogatottak.

Lásd: [CREATE CREDENTIAL (HITELESÍTŐ ADATOK LÉTREHOZÁSA)](/sql/t-sql/statements/create-credential-transact-sql) [és ALTER CREDENTIAL (HITELESÍTŐ ADATOK MEGVÁLTOZTATÁSA).](/sql/t-sql/statements/alter-credential-transact-sql)

### <a name="cryptographic-providers"></a>Kriptográfiai szolgáltatók

SQL Managed Instance nem férnek hozzá a fájlokhoz, ezért nem lehet kriptográfiai szolgáltatókat létrehozni:

- `CREATE CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: [CREATE CRYPTOGRAPHIC PROVIDER (TITKOSÍTÁSI SZOLGÁLTATÓ LÉTREHOZÁSA).](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)
- `ALTER CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: [ALTER CRYPTOGRAPHIC PROVIDER (TITKOSÍTÁSI SZOLGÁLTATÓ MEGVÁLTOZTATÁSA).](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)

### <a name="logins-and-users"></a>Bejelentkezések és felhasználók

- A , és használatával létrehozott `FROM CERTIFICATE` `FROM ASYMMETRIC KEY` `FROM SID` SQL-bejelentkezések támogatottak. Lásd: [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) szintaxissal vagy a [CREATE USER FROM LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) szintaxissal létrehozott azure AD-kiszolgálóneveket (logins) támogatja. Ezek a bejelentkezések kiszolgálószinten vannak létrehozva.

    SQL Managed Instance a szintaxissal támogatja az Azure AD-adatbázis rendszerbiztonsági `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` tagokat. Ezt a funkciót Azure AD-beli tartalmazottadatbázis-felhasználóknak is nevezik.

- A szintaxissal létrehozott `CREATE LOGIN ... FROM WINDOWS` Windows-bejelentkezések nem támogatottak. A Azure Active Directory és a felhasználókkal.
- A példányt létrehozó Azure AD-felhasználó korlátlan rendszergazdai [jogosultságokkal rendelkezik.](../database/logins-create-manage.md)
- A nem rendszergazdai Szintű Azure AD-adatbázisszintű felhasználók a szintaxis használatával is `CREATE USER ... FROM EXTERNAL PROVIDER` létrehozhatóak. Lásd: [CREATE USER ... KÜLSŐ SZOLGÁLTATÓTÓL:](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Az Azure AD-kiszolgáló rendszerbiztonsági tagokkal (bejelentkezésekkel) az SQL-funkciók egy adott SQL Managed Instance támogatottak. Az Azure AD-felhasználók nem támogatják a példányok közötti interakciót igénylő funkciókat, függetlenül attól, hogy ugyanazon az Azure AD-bérlőn vagy eltérő bérlőn belül vannak-e. Példák ilyen funkciókra:

  - SQL tranzakciós replikáció.
  - Csatolja a kiszolgálót.

- Nem támogatott az Azure AD-csoportra leképezett Azure AD-bejelentkezés beállítása adatbázis-tulajdonosként.
- Az Azure AD kiszolgálószintű rendszerbiztonsági tagokkal más Azure AD-rendszerbiztonsági tagokkal való megszemélyesítése támogatott, például az [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) záradék. Az EXECUTE AS korlátozásai a következőek:

  - Az EXECUTE AS USER nem támogatott az Azure AD-felhasználók számára, ha a név eltér a bejelentkezési névtől. Ilyen például, ha a felhasználó létrehozása a CREATE USER [myAadUser] FROM LOGIN [ ] szintaxissal történik, és a megszemélyesítést az john@contoso.com EXEC AS USER = _myAadUser_ szintaxissal kísérelik meg. Amikor felhasználót **hoz** létre egy Azure AD-kiszolgálónévből (bejelentkezés), adja meg a user_name a LOGIN login_name **meg.**
  - Csak SQL Server szerepkörhöz SQL Server szintű rendszerbiztonsági tagokat (bejelentkezéseket) hajthat végre a következő, Azure AD-rendszerbiztonsági tagokat célzó `sysadmin` műveleteket:

    - VÉGREHAJTÁS FELHASZNÁLÓKÉNT
    - VÉGREHAJTÁS BEJELENTKEZÉSKÉNT

  - A felhasználó megszemélyesítéséhez az EXECUTE AS utasítással a felhasználót közvetlenül az Azure AD-kiszolgálónévre (bejelentkezés) kell leképezni. Az Azure AD-kiszolgálónévre leképezett Azure AD-csoportok tagjai nem személyeskednek hatékonyan az EXECUTE AS utasítással, még akkor sem, ha a hívó rendelkezik megszemélyesítési engedélyekkel a megadott felhasználónévhez.

- Az adatbázis BACPAC-fájlokkal történő exportálása/importálása az [SSMS 18.4-es](/sql/ssms/download-sql-server-management-studio-ssms)vagy újabb SQL Managed Instance, vagy a [SQLPackage.exe. ](/sql/tools/sqlpackage-download)
  - Az adatbázis BACPAC-fájlja a következő konfigurációkat támogatja: 
    - Adatbázis exportálása/importálása ugyanazon Azure AD-tartomány különböző példányai között.
    - Adatbázis exportálása a SQL Managed Instance és importálása SQL Database Azure AD-tartományba. 
    - Adatbázis exportálása a SQL Database és importálása SQL Managed Instance Azure AD-tartományba.
    - Adatbázis exportálása a SQL Managed Instance importálása SQL Server (2012-es vagy újabb verzió).
      - Ebben a konfigurációban minden Azure AD-felhasználó bejelentkezés nélkül SQL Server adatbázis-rendszerbiztonsági tagként (felhasználóként) jön létre. A felhasználók típusa a következőként van listázva, `SQL` és látható, mint `SQL_USER` a sys.database_principals). Az engedélyeik és szerepköreik a SQL Server adatbázis metaadataiban maradnak, és megszemélyesítésre használhatók. Ezek azonban nem használhatók az alkalmazás eléréséhez és SQL Server hitelesítő adataik használatával.

- Csak az SQL Managed Instance kiépítési folyamat által létrehozott kiszolgálószintű fő bejelentkezés, a kiszolgálói szerepkörök (például vagy) tagjai, illetve a kiszolgálói szinten ALTER ANY LOGIN engedéllyel rendelkező egyéb bejelentkezések hozhatnak létre Azure AD-kiszolgálóneveket `securityadmin` `sysadmin` (bejelentkezéseket) a SQL Managed Instance.
- Ha a bejelentkezés egy SQL-rendszerbiztonsági tag, csak a szerepkörhöz való bejelentkezések használhatja a create parancsot egy `sysadmin` Azure AD-fiókhoz való bejelentkezések létrehozásához.
- Az Azure AD-bejelentkezésnek egy Olyan Azure AD-nek kell tagja lennie, amely ugyanabban a címtárban található, mint a Azure SQL Managed Instance.
- Az Azure AD-kiszolgálóneveket (bejelentkezéseket) a 18.0-s Object Explorer 18.0-s előzetes verziótól SQL Server Management Studio kezdve láthatja.
- Az Azure AD-kiszolgálónevekkel (bejelentkezésekkel) átfedésben lévő Azure AD-rendszergazdai fiókok engedélyezettek. Az Azure AD-kiszolgáló rendszerbiztonsági tagokkal (bejelentkezésekkel) elsőbbséget élveznek az Azure AD-rendszergazda felett, amikor feloldja a rendszerbiztonsági adatokat, és engedélyeket alkalmaz a SQL Managed Instance.
- A hitelesítés során a rendszer a következő sorrendet alkalmazza a hitelesítő rendszerbiztonsági tag feloldásához:

    1. Ha az Azure AD-fiók közvetlenül az Azure AD-kiszolgáló egyszerű felhasználójára (bejelentkezésre) van leképezve, amely az sys.server_principals-ban "E" típusúként szerepel, adjon hozzáférést, és alkalmazza az Azure AD-kiszolgáló egyszerű felhasználójának engedélyét (bejelentkezés).
    2. Ha az Azure AD-fiók egy olyan Azure AD-csoport tagja, amely az Azure AD-kiszolgáló egyszerű felhasználójára (login) van leképezve, amely az sys.server_principals-ban "X" típusúként van jelen, adjon hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezéséhez szükséges engedélyeket.
    3. Ha az Azure AD-fiók az SQL Managed Instance-hez speciális portál által konfigurált Azure AD-rendszergazda, amely nem létezik SQL Managed Instance rendszernézetben, alkalmazza az Azure AD-rendszergazda speciális rögzített engedélyét az SQL Managed Instance -hoz (örökölt mód).
    4. Ha az Azure AD-fiók közvetlenül leképezve van egy Azure AD-felhasználóra egy adatbázisban, amely az sys.database_principals-ban "E" típusúként szerepel, adjon hozzáférést és alkalmazza az Azure AD adatbázis-felhasználó engedélyét.
    5. Ha az Azure AD-fiók egy olyan Azure AD-csoport tagja, amely egy adatbázis Azure AD-felhasználójára van leképezve, és amely az sys.database_principals-ban X típusúként van jelen, adjon hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezésének engedélyét.
    6. Ha az Azure AD-bejelentkezés egy Azure AD-felhasználói fiókra vagy egy Azure AD-csoportfiókra van leképezve, amely a hitelesítést elfelhasználó felhasználóhoz van hozzárendelve, a rendszer az Azure AD-bejelentkezés összes engedélyét alkalmazza.

### <a name="service-key-and-service-master-key"></a>Szolgáltatáskulcs és szolgáltatás főkulcsa

- [A főkulcs biztonsági](/sql/t-sql/statements/backup-master-key-transact-sql) mentése nem támogatott (a SQL Database kezeli).
- [A főkulcs-visszaállítás](/sql/t-sql/statements/restore-master-key-transact-sql) nem támogatott (az SQL Database kezeli).
- [A szolgáltatás főkulcsának](/sql/t-sql/statements/backup-service-master-key-transact-sql) biztonsági mentése nem támogatott (a SQL Database kezeli).
- [A szolgáltatás főkulcsának](/sql/t-sql/statements/restore-service-master-key-transact-sql) visszaállítása nem támogatott (a SQL Database kezeli).

## <a name="configuration"></a>Konfiguráció

### <a name="buffer-pool-extension"></a>Pufferkészlet bővítménye

- [A pufferkészlet bővítménye](/sql/database-engine/configure-windows/buffer-pool-extension) nem támogatott.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nem támogatott. Lásd: [ALTER SERVER CONFIGURATION (KISZOLGÁLÓ KONFIGURÁCIÓJÁNAK MEGVÁLTOZTATÁSA).](/sql/t-sql/statements/alter-server-configuration-transact-sql)

### <a name="collation"></a>Rendezés

A példányok alapértelmezett rendezése és `SQL_Latin1_General_CP1_CI_AS` létrehozási paraméterként is meg lehet adni. Lásd: [Rendezések.](/sql/t-sql/statements/collations)

### <a name="compatibility-levels"></a>Kompatibilitási szintek

- A támogatott kompatibilitási szintek: 100, 110, 120, 130, 140 és 150.
- A 100 alatti kompatibilitási szintek nem támogatottak.
- Az új adatbázisok alapértelmezett kompatibilitási szintje 140. A visszaállított adatbázisok kompatibilitási szintje 100-as vagy korábbi verzió esetén változatlan marad.

Lásd: [ALTER DATABASE Compatibility Level (ALTER DATABASE kompatibilitási szint).](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)

### <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözés nem támogatott.

- `ALTER DATABASE SET PARTNER` A `SET WITNESS` és a beállítások nem támogatottak.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nem támogatott.

További információ: [ALTER DATABASE SET PARTNER and SET WITNESS (ALTER DATABASE SET PARTNER](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) és SET WITNESS) és CREATE ENDPOINT ... [(VÉGPONT LÉTREHOZÁSA) FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Adatbázis-beállítások

- Több naplófájl nem támogatott.
- A memóriabeli objektumok nem támogatottak a általános célú szolgáltatási szinten. 
- Példányonként legfeljebb 280 fájl általános célú, ami azt jelenti, hogy adatbázisonként legfeljebb 280 fájl lehet. A rendszer az adat- és naplófájlokat általános célú beleszámít ebbe a korlátba. [A üzletileg kritikus szint adatbázisonként 32 767 fájlt támogat.](./resource-limits.md#service-tier-characteristics)
- Az adatbázis nem tartalmazhat fájlstreamadatokat tartalmazó fájlcsoportokat. A visszaállítás sikertelen, ha a .bak `FILESTREAM` adatokat tartalmaz. 
- Minden fájl az Azure Blob Storage-ban van elhelyezve. Az I/O és az átviteli sebesség fájlonként az egyes fájlok méretétől függ.

#### <a name="create-database-statement"></a>CREATE DATABASE utasítás

A következő korlátozások vonatkoznak a `CREATE DATABASE` következőre:

- Fájlok és fájlcsoportok nem határozhatóak meg. 
- A `CONTAINMENT` beállítás nem támogatott. 
- `WITH` A beállítások nem támogatottak. 
   > [!TIP]
   > Áthidaló megoldásként az after parancs használatával adja meg az adatbázis-beállításokat a fájlok hozzáadásához vagy a `ALTER DATABASE` `CREATE DATABASE` tartalmazottság beállításához. 

- A `FOR ATTACH` beállítás nem támogatott.
- A `AS SNAPSHOT OF` beállítás nem támogatott.

További információ: [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE utasítás

Egyes fájltulajdonságok nem módosíthatók:

- A T-SQL-utasításban nem lehet `ALTER DATABASE ADD FILE (FILENAME='path')` fájlútvonalat megadni. Távolítsa `FILENAME` el a szkriptből, SQL Managed Instance automatikusan elhelyezi a fájlokat. 
- A fájlnév nem módosítható a `ALTER DATABASE` utasítással.

Az alábbi beállítások alapértelmezés szerint be vannak állítva, és nem módosíthatók:

- `MULTI_USER`
- `ENABLE_BROKER`
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

Egyes `ALTER DATABASE` utasítások (például [a SET CONTAINMENT](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)) átmenetien meghiúsulnak, például az adatbázis automatikus biztonsági mentése során vagy közvetlenül az adatbázis létrehozása után. Ebben az esetben a `ALTER DATABASE` utasítást újra kell tenni. A kapcsolódó hibaüzenetekkel kapcsolatos további információkért lásd a [Megjegyzéseket című szakaszt.](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2)

További információ: [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- A SQL Server Agent engedélyezése és letiltása jelenleg nem támogatott a SQL Managed Instance. Az SQL Agent folyamatosan fut.
- A feladatütemező eseményindító üresjárati CPU-alapú aktiválása nem támogatott.
- SQL Server Agent beállítások csak olvashatók. Az eljárás `sp_set_agent_properties` nem támogatott a SQL Managed Instance. 
- Feladatok
  - A T-SQL-feladat lépései támogatottak.
  - A következő replikációs feladatok támogatottak:
    - Tranzakciónapló-olvasó
    - Pillanatkép
    - Forgalmazó
  - Az SSIS-feladat lépései támogatottak.
  - Más típusú feladat lépések jelenleg nem támogatottak:
    - Az egyesítési replikációs feladat lépése nem támogatott. 
    - A Queue Reader nem támogatott. 
    - A parancshéj még nem támogatott.
  - SQL Managed Instance nem fér hozzá külső erőforrásokhoz, például a hálózati megosztáshoz a robocopyval. 
  - SQL Server Analysis Services nem támogatott.
- Az értesítések csak részben támogatottak.
- Az e-mailes értesítés támogatott, bár ehhez konfigurálnia kell egy Database Mail profilt. SQL Server Agent profil csak egy Database Mail használhat, és meg kell `AzureManagedInstance_dbmail_profile` hívnunk. 
  - A Pager nem támogatott.
  - A NetSend nem támogatott.
  - A riasztások még nem támogatottak.
  - A proxyk nem támogatottak.
- Az EventLog nem támogatott.
- Az SQL Agent-feladatok létrehozásához, módosításához vagy végrehajtásához a felhasználót közvetlenül az Azure AD-kiszolgáló rendszerbiztonsági tagra (bejelentkezésre) kell leképezni. Azok a felhasználók, akik nincsenek közvetlenül leképezve, például egy Azure AD-csoporthoz tartozó, SQL Agent-feladatok létrehozására, módosítására vagy végrehajtására vonatkozó jogosultságokkal nem képesek végrehajtani ezeket a műveleteket. Ennek oka a felügyelt példány megszemélyesítése és az [EXECUTE AS korlátozásai.](#logins-and-users)
- A fő-/célfeladatok (MSX/TSX) többkiszolgálós adminisztrációs szolgáltatása nem támogatott.

További információ a SQL Server Agent: [SQL Server Agent.](/sql/ssms/agent/sql-server-agent)

### <a name="tables"></a>Táblázatok

Az alábbi táblázattípusok nem támogatottak:

- [FILESTREAM (FÁJLFOLYAM)](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (nem csak a általános célú támogatott)

További információ a táblák létrehozásáról és megváltoztatásáról: [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) [alter TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkciók

### <a name="bulk-insert--openrowset"></a>Tömeges beszúrás /OPENROWSET

SQL Managed Instance nem fér hozzá a fájlmegosztáshoz és a Windows-mappákhoz, ezért a fájlokat az Azure Blob Storage-ból kell importálni:

- `DATASOURCE` az parancsban `BULK INSERT` szükséges, amikor fájlokat importál az Azure Blob Storage-ból. Lásd: [BULK INSERT.](/sql/t-sql/statements/bulk-insert-transact-sql)
- `DATASOURCE` A kötelező a függvényben, amikor beolvassa egy fájl `OPENROWSET` tartalmát az Azure Blob Storage-ból. Lásd: [OPENROWSET.](/sql/t-sql/functions/openrowset-transact-sql)
- `OPENROWSET` az adatok beolvashatóak a Azure SQL Database, Azure SQL Managed Instance vagy SQL Server példányból. Más források, például Oracle-adatbázisok vagy Excel-fájlok nem támogatottak.

### <a name="clr"></a>Clr

A SQL Managed Instance nem férhetnek hozzá a fájlmegosztáshoz és a Windows-mappákhoz, ezért a következő korlátozások érvényesek:

- Csak `CREATE ASSEMBLY FROM BINARY` az támogatott. Lásd: [CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` nem támogatott. Lásd: [CREATE ASSEMBLY FROM FILE (SZERELVÉNY LÉTREHOZÁSA FÁJLBÓL).](/sql/t-sql/statements/create-assembly-transact-sql)
- `ALTER ASSEMBLY` nem hivatkozhat fájlokra. Lásd: [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` A nem tud mellékleteket küldeni a @file_attachments paraméterrel. A helyi fájlrendszer és a külső megosztások Azure Blob Storage nem érhetők el ebből az eljárásból.
 - Tekintse meg a paraméterekkel és a `@query` hitelesítéssel kapcsolatos ismert problémákat.
 
### <a name="dbcc"></a>Dbcc

A nem dokumentált DBCC-utasítások, amelyek engedélyezve vannak SQL Server nem támogatottak a SQL Managed Instance.

- Csak korlátozott számú globális nyomkövetési jelző támogatott. A munkamenetszintű `Trace flags` nem támogatott. Lásd: [Nyomkövetési jelzők.](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)
- [A DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) és [a DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) a korlátozott számú globális nyomkövetési jelzővel működik.
- [A DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST és REPAIR_REBUILD nem használható, mert az adatbázis nem beállítható módban `SINGLE_USER` – [lásd: ALTER DATABASE differences (ADATBÁZIS-eltérések módosítása).](#alter-database-statement) A lehetséges adatbázis-sérüléseket a biztonsági Azure-támogatás kezeli. Ha Azure-támogatás az adatbázis sérülését jelzi, lépjen kapcsolatba a Azure-támogatás.

### <a name="distributed-transactions"></a>Elosztott tranzakciók

Az elosztott tranzakciók [részleges támogatása jelenleg](../database/elastic-transactions-overview.md) nyilvános előzetes verzióban érhető el. Az elosztott tranzakciók a következő feltételek mellett támogatottak (mindegyiknek teljesülnie kell):
* Minden tranzakció résztvevője felügyelt Azure SQL, amelyek a [kiszolgálói megbízhatósági csoport tagjai.](./server-trust-group-overview.md)
* A tranzakciók kezdeményezése a .NET (TransactionScope osztály) vagy a Transact-SQL használatával történik.

Azure SQL Managed Instance jelenleg nem támogat más forgatókönyveket, amelyeket az MSDTC rendszeresen támogat a helyszínen vagy az Azure Virtual Machines.

### <a name="extended-events"></a>Bővített események

A bővített események (XEvents) egyes Windows-specifikus céljai nem támogatottak:

- A `etw_classic_sync` cél nem támogatott. Fájlok `.xel` tárolása az Azure Blob Storage-ban. Lásd: [etw_classic_sync cél](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- A `event_file` cél nem támogatott. Fájlok `.xel` tárolása az Azure Blob Storage-ban. Lásd: [event_file cél](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Külső kódtárak

Az adatbázison elérhető R- és Python-külső kódtárak korlátozott nyilvános előzetes verzióban támogatottak. Lásd: [Machine Learning Services a Azure SQL Managed Instance (előzetes verzió) .](machine-learning-services-overview.md)

### <a name="filestream-and-filetable"></a>Filestream és FileTable

- A fájlstreamadatok nem támogatottak.
- Az adatbázis nem tartalmazhat adatokat tartalmazó `FILESTREAM` fájlcsoportokat.
- `FILETABLE` nem támogatott.
- A táblák nem tartalmaznak `FILESTREAM` típusokat.
- A következő függvények nem támogatottak:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

További információ: [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) és [FileTables.](/sql/relational-databases/blob/filetables-sql-server)

### <a name="full-text-semantic-search"></a>Teljes szöveges szemantikai keresés

[A szemantikai keresés](/sql/relational-databases/search/semantic-search-sql-server) nem támogatott.

### <a name="linked-servers"></a>Társított kiszolgálók

A SQL Managed Instance kiszolgálók korlátozott számú célt támogatnak:

- A támogatott célok a SQL Managed Instance, SQL Database, Azure Synapse [kiszolgáló](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) nélküli és dedikált SQL-készletek, valamint SQL Server példányok. 
- Az elosztott írható tranzakciók csak a felügyelt példányok között lehetségesek. További információ: [Elosztott tranzakciók.](../database/elastic-transactions-overview.md) A MS DTC azonban nem támogatott.
- A nem támogatott célok a fájlok, a Analysis Services és más RDBMS. Próbálja meg natív CSV-importálást használni a Azure Blob Storage a vagy a használatával, vagy a fájlimportozás alternatívájaként, vagy fájlokat töltsön be kiszolgáló nélküli SQL-készlet használatával `BULK INSERT` `OPENROWSET` a [Azure Synapse Analytics.](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)

Műveletek: 

- [A példányközi írási](../database/elastic-transactions-overview.md) tranzakciók csak a felügyelt példányok esetében támogatottak.
- `sp_dropserver` A támogatja a csatolt kiszolgáló elejtését. Lásd: [sp_dropserver.](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)
- A `OPENROWSET` függvény csak a példányok lekérdezésére SQL Server használható. Ezek felügyelt, helyszíni vagy virtuális gépeken is kezelhetők. Lásd: [OPENROWSET.](/sql/t-sql/functions/openrowset-transact-sql)
- A `OPENDATASOURCE` függvény csak a példányok lekérdezésére SQL Server használható. Ezek felügyelt, helyszíni vagy virtuális gépeken is kezelhetők. Szolgáltatóként csak a , és értékek `SQLNCLI` `SQLNCLI11` `SQLOLEDB` támogatottak. Például: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Lásd: [OPENDATASOURCE.](/sql/t-sql/functions/opendatasource-transact-sql)
- A csatolt kiszolgálók nem használhatók a hálózati megosztások fájljainak (Excel, CSV) olvasásához. Próbálja meg a [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [az OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) függvényt használni, amely CSV-fájlokat olvas a Azure Blob Storage-ból, vagy egy olyan csatolt kiszolgálót, amely kiszolgáló nélküli SQL-készletre hivatkozik [a Synapse Analytics.](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) A kérések nyomon követése a [SQL Managed Instance visszajelzési elemen](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

A külső forrás egyetlen elérhető típusa az RDBMS (nyilvános előzetes verzióban) a Azure SQL adatbázishoz, Azure SQL példányhoz és a Azure Synapse készlethez. Az Azure Storage-ból közvetlenül beolvasott Polybase külső táblák áthidaló megoldásaként használhat olyan külső táblát, amely a Synapse Analytics kiszolgáló nélküli [SQL-készletére](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) hivatkozik. A Azure SQL-példányban az Azure Storage-adatokat egy kiszolgáló nélküli [SQL-készlethez](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) csatolt Synapse Analytics vagy SQL Server is használhatja.
További információ a PolyBase-ről: [PolyBase.](/sql/relational-databases/polybase/polybase-guide)

### <a name="replication"></a>Replikáció

- A pillanatkép- és kétirányú replikációs típusok támogatottak. Az egyesítéses replikáció, a társközi replikáció és a frissíthető előfizetések nem támogatottak.
- [A tranzakciós replikáció](replication-transactional-overview.md) nyilvános előzetes verzióban érhető el a SQL Managed Instance néhány korlátozással:
    - A replikáció minden résztvevőtípusa (közzétevő, terjesztő, leküldéses előfizető és leküldéses előfizető) elhelyezhető az SQL Managed Instance-ban, de a közzétevőnek és a terjesztőnek egyaránt a felhőben vagy mindkét helyszínen kell lennie.
    - SQL Managed Instance az alkalmazás legújabb verzióival is SQL Server. További [információért tekintse meg a támogatott verziók mátrixát.](replication-transactional-overview.md#supportability-matrix)
    - A tranzakciós replikáció további hálózati [követelményekkel is rendelkezik.](replication-transactional-overview.md#requirements)

A tranzakciós replikáció konfigurálásával kapcsolatos további információkért tekintse meg a következő oktatóanyagokat:
- [Replikáció az SQL MI-közzétevő és az SQL MI-előfizető között](replication-between-two-instances-configure-tutorial.md)
- [Replikáció egy SQL MI-közzétevő, az SQL MI-terjesztő és a SQL Server között](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE utasítás 

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
  - `FROM URL` Az (Azure Blob Storage) az egyetlen támogatott lehetőség.
  - `FROM DISK`/`TAPE`A /backup eszköz nem támogatott.
  - A biztonsági mentési készletek nem támogatottak.
- `WITH` A beállítások nem támogatottak. A visszaállítási kísérletek `WITH` ( például , , `DIFFERENTIAL` `STATS` `REPLACE` stb.) sikertelenek lesznek.
- `ASYNC RESTORE`: A visszaállítás akkor is folytatódik, ha az ügyfélkapcsolat megszakad. Ha megszakad a kapcsolat, ellenőrizheti a visszaállítási művelet állapotát a nézetben, valamint a CREATE és `sys.dm_operation_status` DROP adatbázist. Lásd: [sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 

A következő adatbázis-beállítások vannak beállítva vagy felülbírálva, és később nem módosíthatók: 

- `NEW_BROKER` ha a közvetítő nincs engedélyezve a .bak fájlban. 
- `ENABLE_BROKER` ha a közvetítő nincs engedélyezve a .bak fájlban. 
- `AUTO_CLOSE=OFF` ha egy adatbázis a .bak fájlban a következővel `AUTO_CLOSE=ON` rendelkezik: . 
- `RECOVERY FULL` ha egy adatbázis a .bak fájlban vagy `SIMPLE` helyreállítási `BULK_LOGGED` módban van.
- A rendszer hozzáad egy memóriaoptimalált fájlcsoportot, és XTP-nek nevezi, ha az nem található meg a forrás .bak fájlban. 
- Minden meglévő memóriaoptimalált fájlcsoport XTP-re van átnevezve. 
- `SINGLE_USER`A `RESTRICTED_USER` és a beállítás a formátumra lesz konvertálva. `MULTI_USER`

Korlátozások: 

- Előfordulhat, hogy a sérült adatbázisok biztonsági másolatai a sérülés típusától függően visszaállnak, de az automatikus biztonsági mentések csak a sérülés kijavítása után lesznek készítve. A probléma elkerülése érdekében győződjön meg arról, hogy a forrás SQL Managed Instance fut, és használja a biztonsági `DBCC CHECKDB` `WITH CHECKSUM` mentést.
- A jelen dokumentumban (például vagy objektumokban) leírt korlátozásokat tartalmazó adatbázisfájl visszaállítása nem állítható vissza a `.BAK` `FILESTREAM` `FILETABLE` SQL Managed Instance.
- `.BAK` több biztonságimásolat-készletet tartalmazó fájlok nem állíthatók vissza. 
- `.BAK` A több naplófájlt tartalmazó fájlok nem állíthatók vissza.
- A 8 TB-ot meghaladó adatbázisokat, a memóriában aktív OLTP-objektumokat vagy a példányonként 280-at meghaladó fájlok számát tartalmazó biztonsági másolatok nem állíthatók vissza általános célú példányon. 
- A 4 TB-osnál nagyobb méretű adatbázisokat vagy memóriában lévő OLTP-objektumokat tartalmazó biztonsági másolatok nem állíthatók vissza a üzletileg kritikus példányon. [](resource-limits.md)
A visszaállítási utasításokkal kapcsolatos információkért lásd: [RESTORE utasítások.](/sql/t-sql/statements/restore-statements-transact-sql)

 > [!IMPORTANT]
 > Ugyanezek a korlátozások vonatkoznak a beépített időponthoz igaz visszaállítási műveletre is. A 4 TB általános célú nál nagyobb adatbázis például nem állítható vissza a üzletileg kritikus példányon. üzletileg kritikus adatbázis memóriában lévő OLTP-fájlokkal vagy 280-asnál több fájl nem állítható vissza általános célú példányon.

### <a name="service-broker"></a>Szolgáltatásközvetítő

A példányok közötti szolgáltatásközvetítő üzenetcsere csak felügyelt Azure SQL között támogatott:

- `CREATE ROUTE`: A nem használható más kiszolgáló DNS-nevén kívül `CREATE ROUTE` `ADDRESS` `LOCAL` SQL Managed Instance.
- `ALTER ROUTE`: A nem használható más kiszolgáló DNS-nevén kívül `ALTER ROUTE` `ADDRESS` `LOCAL` SQL Managed Instance.

Az átviteli biztonság támogatott, a párbeszédpanel-biztonság nem:
- `CREATE REMOTE SERVICE BINDING`esetében nem támogatott.

A szolgáltatásközvetítő alapértelmezés szerint engedélyezve van, és nem tiltható le. A következő ALTER DATABSE beállítások nem támogatottak:
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>Tárolt eljárások, függvények és eseményindítók

- `NATIVE_COMPILATION` A nem támogatott a általános célú szinten.
- A következő [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) nem támogatottak: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` nem támogatott. Lásd: [sp_execute_external_scripts.](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)
- `xp_cmdshell` nem támogatott. Lásd: [xp_cmdshell.](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)
- `Extended stored procedures` nem támogatottak, és ez a és a osztályt `sp_addextendedproc` is magában `sp_dropextendedproc` foglalja. Ez a funkció nem támogatott, mert a szolgáltatás elajátsítási útvonalon van a SQL Server. További részletekért lásd: [Bővített tárolt eljárások.](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)
- `sp_attach_db`, `sp_attach_single_file_db` és `sp_detach_db` nem támogatottak. Lásd: [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), és [sp_detach_db.](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)

### <a name="system-functions-and-variables"></a>Rendszerfunkciók és -változók

Az alábbi változók, függvények és nézetek különböző eredményeket adnak vissza:

- `SERVERPROPERTY('EngineEdition')` A a 8 értéket adja vissza. Ez a tulajdonság egyedileg azonosít egy SQL Managed Instance. Lásd: [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` A NULL értéket ad vissza, mert a példány fogalma, ahogy az SQL Server, nem vonatkozik a SQL Managed Instance. Lásd: [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` A teljes DNS "connectable" nevet ad vissza, például my-managed-instance.wcus17662feb9ce98.database.windows.net. [Lásd: @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` A teljes DNS "connectable" nevet ad vissza, például a `myinstance.domain.database.windows.net` "name" (név) és a "data_source" data_source. Lásd: [SYS. SERVERS (KISZOLGÁLÓK)](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` A NULL értéket ad vissza, mert a szolgáltatás fogalma a SQL Server nem vonatkozik a SQL Managed Instance. [Lásd: @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` támogatott. NULL értéket ad vissza, ha az Azure AD-bejelentkezés nincs a sys.sysbejelentkezések között. Lásd: [SUSER_ID.](/sql/t-sql/functions/suser-id-transact-sql) 
- `SUSER_SID` nem támogatott. A rendszer nem megfelelő adatokat ad vissza, ami egy ideiglenes ismert probléma. Lásd: [SUSER_SID.](/sql/t-sql/functions/suser-sid-transact-sql) 

## <a name="environment-constraints"></a><a name="Environment"></a>Környezeti korlátozások

### <a name="subnet"></a>Alhálózat
-  Nem helyezhet más erőforrásokat (például virtuális gépeket) arra az alhálózatra, ahol üzembe SQL Managed Instance. Ezeket az erőforrásokat egy másik alhálózat használatával helyezheti üzembe.
- Az alhálózatnak elegendő számú elérhető [IP-címmel kell lennie.](connectivity-architecture-overview.md#network-requirements) A minimális érték legalább 32 IP-cím az alhálózaton.
- A régióban üzembe helyezhető virtuális magok és példánytípusok száma bizonyos korlátozásokkal [és korlátokkal rendelkezik.](resource-limits.md#regional-resource-limitations)
- Az [alhálózaton](connectivity-architecture-overview.md#network-requirements) alkalmazni kell egy hálózati konfigurációt.

### <a name="vnet"></a>Virtuális hálózat
- A VNet üzembe helyezhető az Erőforrásmodell használatával – A virtuális hálózatok klasszikus modellje nem támogatott.
- A SQL Managed Instance létrehozása után a SQL Managed Instance virtuális hálózat másik erőforráscsoportba vagy előfizetésbe való áthelyezése nem támogatott.
- A 2020. 09. 22. előtt létrehozott virtuális [](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) fürtökben üzemeltetett felügyelt SQL-példányok nem támogatottak. Ezekhez az erőforrásokhoz ExpressRoute-on vagy virtuális hálózatok között, virtuális hálózati átjárókon keresztül csatlakozhat.

### <a name="failover-groups"></a>Feladatátvételi csoportok
A rendszeradatbázisok nincsenek replikálva a feladatátvételi csoportban a másodlagos példányra. Ezért a rendszeradatbázisok objektumaitól függő forgatókönyvek lehetetlenek lesznek a másodlagos példányon, kivéve, ha az objektumok manuálisan vannak létrehozva a másodlagos példányon.

### <a name="tempdb"></a>TEMPDB
- A fájl maximális mérete magonként legfeljebb `tempdb` 24 GB lehet általános célú szinten. A tárolási szinten üzletileg kritikus maximális méretet a SQL Managed Instance `tempdb` korlátozza. `Tempdb` A naplófájl mérete legfeljebb 120 GB lehet általános célú szinten. Egyes lekérdezések hibát jeleznek, ha magonként több mint 24 GB-ra van szükségük, vagy ha 120 GB-nál több naplóadatot `tempdb` hoznak létre.
- `Tempdb` A mindig 12 adatfájlra van felosztva: 1 elsődleges, más néven fő, adatfájl és 11 nem elsődleges adatfájl. A fájlstruktúra nem módosítható, és nem lehet új fájlokat hozzáadni a `tempdb` fájlhoz. 
- [A memóriaoptimalált `tempdb` metaadatok](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata), SQL Server 2019-es memória-adatbázis-szolgáltatás nem támogatott.
- A modelladatbázisban létrehozott objektumokat nem lehet automatikusan létrehozni a-ban újraindítás vagy feladatátvétel után, mert nem kapják meg a kezdeti objektumlistát `tempdb` a `tempdb` modelladatbázisból. Az objektumokat manuálisan `tempdb` kell létrehozni minden újraindítás vagy feladatátvétel után.

### <a name="msdb"></a>MSDB

A következő MSDB-SQL Managed Instance a megfelelő előre meghatározott szerepköröknek kell tulajdonában lennie:

- Általános szerepkörök
  - TargetServersRole
- [Adatbázis-szerepkörök kijavítva](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail-szerepkörök:](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile)
  - DatabaseMailUserRole
- [Integrációs szolgáltatási szerepkörök:](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true)
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Az előre meghatározott szerepkörnevek, sémanevek és sématulajdonosok ügyfelek által való módosítása hatással lesz a szolgáltatás normál működésére. Az ezeken végrehajtott módosítások visszaállnak az előre meghatározott értékekre, amint észlelhetők, vagy a szolgáltatás következő frissítésére a normál működés biztosítása érdekében.

### <a name="error-logs"></a>Hibanaplók

SQL Managed Instance részletes információkat a hibanaplókba. A hibanapló számos belső rendszereseményt naplóz. Egyéni eljárással olvashatja be a hibanaplókat, amelyek kiszűrnek néhány irreleváns bejegyzést. További információ: SQL Managed Instance [sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) SQL Managed Instance bővítmény [sp_readmierrorlog (előzetes verzió)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) Azure Data Studio.

## <a name="next-steps"></a>Következő lépések

- További információ a SQL Managed Instance: [Mi a SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- A funkciók és az összehasonlítási lista a Azure SQL Managed Instance [összehasonlítását tartalmazza.](../database/features-comparison.md)
- A kiadási frissítésekről és az ismert problémák állapotáról további SQL Managed Instance [kibocsátási megjegyzésekben.](../database/doc-changes-updates-release-notes.md)
- Az új fiók létrehozásáról rövid útmutatót a SQL Managed Instance [létrehozása](instance-create-quickstart.md)SQL Managed Instance.
