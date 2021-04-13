---
title: Transzparens adattitkosítás
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: A Azure SQL Database, az Azure SQL felügyelt példányok és az Azure szinapszis Analytics transzparens adattitkosításának áttekintése. A dokumentum a szolgáltatás által felügyelt transzparens adattitkosítást és Bring Your Own Keyt is magában foglaló előnyökkel és beállításokkal foglalkozik.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 4c2c27fb196b222b8094c33eee60a32ccf5a028f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307503"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transzparens adattitkosítás a SQL Database, az SQL felügyelt példánya és az Azure szinapszis Analytics számára
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A [transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) segít megvédeni Azure SQL Database, az Azure SQL felügyelt példányát és az Azure szinapszis Analytics szolgáltatást a rosszindulatú offline tevékenységek fenyegetésével szemben az inaktív adatok titkosításával. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE minden újonnan telepített SQL-adatbázishoz engedélyezve van, és manuálisan kell engedélyezni a Azure SQL Database, Azure SQL felügyelt példányának régebbi adatbázisaihoz. Az Azure szinapszis Analytics TDE manuálisan kell engedélyezni.

A TDE az adatok valós idejű I/O-titkosítását és visszafejtését hajtja végre az oldal szintjén. Az egyes lapok visszafejtése a memóriába történő beolvasáskor történik, a titkosítás pedig a lemezre írás előtt. A TDE titkosítja egy teljes adatbázis tárterületét az adatbázis-titkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) nevű szimmetrikus kulcs használatával. Az adatbázis indításakor a rendszer visszafejti a titkosított ADATTITKOSÍTÁSI kulcsot, majd felhasználja az adatbázisfájlok visszafejtésére és újratitkosítására a SQL Server adatbázismotor folyamatában. A ADATTITKOSÍTÁSI kulcsot a TDE Protector védi. A TDE-védő vagy egy szolgáltatás által felügyelt tanúsítvány (szolgáltatás által felügyelt transzparens adattitkosítás) vagy [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) tárolt aszimmetrikus kulcs (ügyfél által felügyelt transzparens adattitkosítás).

A Azure SQL Database és az Azure szinapszis esetében a TDE-védő a [kiszolgáló](logical-servers.md) szintjén van beállítva, és az adott kiszolgálóhoz társított összes adatbázis örökli. Az Azure SQL felügyelt példányai esetében a TDE-védő a példány szintjén van beállítva, és az adott példányon található összes titkosított adatbázis örökli. A *kiszolgáló* kifejezés a jelen dokumentumon belül a kiszolgáló és a példányra is vonatkozik, kivéve, ha másként van megadva.

> [!IMPORTANT]
> A SQL Database összes újonnan létrehozott adatbázisa alapértelmezés szerint titkosítva van a szolgáltatás által felügyelt transzparens adattitkosítás használatával. A 2017-as és a Restore, Geo-replikáció és adatbázis-másolat használatával létrehozott meglévő SQL Database-adatbázisok nincsenek titkosítva alapértelmezés szerint. A 2019 februárjában létrehozott meglévő SQL felügyelt példány-adatbázisok alapértelmezés szerint nincsenek titkosítva. A visszaállítással létrehozott SQL felügyelt példány-adatbázisok a forrástól öröklik a titkosítási állapotot.

> [!NOTE]
> A TDE nem használható a rendszeradatbázisok, például a **főadatbázis Azure SQL Database** és az Azure SQL felügyelt példányának titkosítására. A **főadatbázis olyan** objektumokat tartalmaz, amelyek szükségesek a TDE műveletek végrehajtásához a felhasználói adatbázisokon. Azt javasoljuk, hogy ne tároljon bizalmas adatokat a rendszeradatbázisokban. Az [infrastruktúra titkosítása](transparent-data-encryption-byok-overview.md#doubleencryption) most már folyamatban van, amely titkosítja a rendszeradatbázisokat, beleértve a főkiszolgálót is. 

## <a name="service-managed-transparent-data-encryption"></a>Szolgáltatás által kezelt transzparens adattitkosítás

Az Azure-ban a TDE alapértelmezett beállítása az, hogy a ADATTITKOSÍTÁSI kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgálói tanúsítvány minden kiszolgáló esetében egyedi, és a használt titkosítási algoritmus az AES 256. Ha egy adatbázis földrajzi replikálási kapcsolatban van, az elsődleges és a Geo-másodlagos adatbázist is az elsődleges adatbázis szülő kiszolgáló kulcsa védi. Ha két adatbázis csatlakozik ugyanahhoz a kiszolgálóhoz, akkor ugyanazt a beépített tanúsítványt is megosztják. A Microsoft automatikusan elforgatja ezeket a tanúsítványokat a belső biztonsági házirendnek megfelelően, és a legfelső szintű kulcsot egy Microsoft belső titkos tárolója védi. Az ügyfelek ellenőrizhetik, hogy az SQL Database és az SQL felügyelt példányának megfelelősége belső biztonsági házirendekkel történik-e a [Microsoft adatvédelmi központban](https://servicetrust.microsoft.com/)elérhető független, harmadik féltől származó auditálási jelentésekben.

A Microsoft emellett zökkenőmentesen helyezi át és felügyeli a kulcsokat a földrajzi replikálás és a visszaállítások igény szerint.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Az ügyfél által felügyelt transzparens adattitkosítás – Bring Your Own Key

Az ügyfél által felügyelt TDE más néven Bring Your Own Key (BYOK) támogatás a TDE-hez. Ebben az esetben a ADATTITKOSÍTÁSI kulcsot titkosító TDE-védő egy ügyfél által felügyelt aszimmetrikus kulcs, amelyet az ügyfél és a felügyelt Azure Key Vault (az Azure felhőalapú külső kulcs-felügyeleti rendszere) tárol, és soha nem hagyja el a kulcstartót. A TDE-védőt [a Key Vault generálhatja, vagy](../../key-vault/keys/hsm-protected-keys.md) egy helyszíni hardveres biztonsági modul (HSM) eszközről továbbíthatja a kulcstartóba. Az SQL Database, az SQL felügyelt példányának és az Azure szinapszisnak engedélyeket kell adni az ügyfél által birtokolt kulcstartó számára a ADATTITKOSÍTÁSI kulcsot visszafejtéséhez és titkosításához. Ha a kiszolgáló és a kulcstartó engedélyeit visszavonják, az adatbázisok nem lesznek elérhetők, és minden adattitkosítás

A Azure Key Vault-integrációval rendelkező TDE segítségével a felhasználók vezérelhetik a kulcsfontosságú felügyeleti feladatokat, beleértve a kulcsok elforgatását, a Key Vault engedélyeit és a biztonsági mentéseket, valamint lehetővé teszik a naplózást/jelentéskészítést az összes TDE-védelemmel Azure Key Vault funkcióval. Key Vault biztosít a központi kulcskezelő szolgáltatáshoz, a kihasználja a szigorúan figyelt HSM, és lehetővé teszi a feladatok elkülönítését a kulcsok és az adatok felügyelete között a biztonsági szabályzatoknak való megfelelés elősegítése érdekében.
Ha többet szeretne megtudni a Azure SQL Database és az Azure szinapszis BYOK, tekintse meg az [átlátható adattitkosítás Azure Key Vault integrációs](transparent-data-encryption-byok-overview.md)szolgáltatással című témakört.

A TDE Azure Key Vault-integrációval való használatának megkezdéséhez tekintse meg a következő témakört: útmutató az [transzparens adattitkosítás bekapcsolásához a saját kulcsát használva Key Vault](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transzparens adattitkosítással védett adatbázis áthelyezése

Nem kell visszafejtenie az adatbázisokat az Azure-ban végzett műveletekhez. A forrás-vagy az elsődleges adatbázis TDE-beállításai transzparens módon öröklik a célt. A benne foglalt műveletek a következők:

- Georedundáns visszaállítás
- Önkiszolgáló időpontra történő visszaállítás
- Törölt adatbázis visszaállítása
- Aktív georeplikáció
- Adatbázis-másolat létrehozása
- Biztonságimásolat-fájl visszaállítása az Azure SQL felügyelt példányára

> [!IMPORTANT]
> A szolgáltatás által felügyelt TDE által titkosított adatbázisok manuális MÁSOLÁSi biztonsági mentése nem támogatott az Azure SQL felügyelt példányain, mivel a titkosításhoz használt tanúsítvány nem érhető el. Az időponthoz való visszaállítás funkció használatával áthelyezheti az ilyen típusú adatbázist egy másik SQL-felügyelt példányra, vagy átválthat az ügyfél által felügyelt kulcsra.

TDE-védelemmel ellátott adatbázis exportálásakor az adatbázis exportált tartalma nincs titkosítva. Az exportált tartalmat titkosítatlan BACPAC-fájlok tárolják. Ügyeljen arra, hogy a megfelelő módon megvédje a BACPAC-fájlokat, és engedélyezze a TDE az új adatbázis importálása után.

Ha például a BACPAC-fájlt egy SQL Server példányból exportálja, az új adatbázis importált tartalma nem lesz automatikusan titkosítva. Hasonlóképpen, ha a BACPAC-fájlt egy SQL Server-példányba importálták, az új adatbázis szintén nem lesz automatikusan titkosítva.

Az egyetlen kivétel az, amikor adatbázist exportál a SQL Databaseba és onnan. A TDE engedélyezve van az új adatbázison, de maga a BACPAC-fájl még nem titkosított.

## <a name="manage-transparent-data-encryption"></a>Transzparens adattitkosítás kezelése

# <a name="the-azure-portal"></a>[Az Azure Portal](#tab/azure-portal)

A Azure Portal TDE kezelése.

A TDE a Azure Portalon keresztüli konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security Managerrel kell csatlakoznia.

A TDE engedélyezése és letiltása az adatbázis szintjén. Az Azure SQL felügyelt példánya a Transact-SQL (T-SQL) használatával kapcsolja be és ki a TDE egy adatbázison. A Azure SQL Database és az Azure szinapszis esetében az adatbázis TDE az Azure-rendszergazda vagy a közreműködői fiókkal való bejelentkezés után kezelheti a [Azure Portal](https://portal.azure.com) . Keresse meg a felhasználói adatbázis TDE beállításait. Alapértelmezés szerint a rendszer a szolgáltatás által kezelt transzparens adattitkosítást használja. A rendszer automatikusan létrehoz egy TDE-tanúsítványt az adatbázist tartalmazó kiszolgálóhoz.

![Szolgáltatás által kezelt transzparens adattitkosítás](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Beállíthatja a TDE főkulcsát, azaz a TDE-védőt a kiszolgáló vagy a példány szintjén. Ha a TDE-t a BYOK-támogatással szeretné használni, és az adatbázisait Key Vault-kulccsal szeretné védelemmel ellátni, nyissa meg a TDE beállításait a kiszolgálón.

![Transzparens adattitkosítás Bring Your Own Key-támogatással](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

TDE kezelése a PowerShell használatával.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A TDE PowerShellen keresztüli konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security managerként kell csatlakoznia.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>A Azure SQL Database és az Azure szinapszis-parancsmagjai

A következő parancsmagokat használja a Azure SQL Database és az Azure Szinapszishoz:

| Parancsmag | Leírás |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Engedélyezheti vagy letilthatja az adatbázisok transzparens adattitkosítását.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Egy adatbázis transzparens adattitkosítási állapotának beolvasása. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Ellenőrzi egy adatbázis titkosítási folyamatát. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Egy Key Vault kulcs hozzáadására szolgál. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Lekéri egy kiszolgáló Key Vault kulcsait  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Beállítja a-kiszolgáló transzparens adattitkosítási védelmezőjét. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Az átlátszó adattitkosítási védő beolvasása |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Eltávolít egy Key Vault kulcsot egy kiszolgálóról. |
|  | |

> [!IMPORTANT]
> Az Azure SQL felügyelt példányai esetében használja a T-SQL [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-database) parancsot a TDE be-és kikapcsolásához az adatbázis szintjén, valamint a [minta PowerShell-parancsfájlt](transparent-data-encryption-byok-configure.md) a TDE kezeléséhez a példány szintjén.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

TDE kezelése a Transact-SQL használatával.

Kapcsolódjon az adatbázishoz egy olyan bejelentkezéssel, amely a Master adatbázisban a **DBManager** szerepkör rendszergazdája vagy tagja.

| Parancs | Leírás |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | A TITKOSÍTÁS be-és kikapcsolása titkosítja vagy visszafejti az adatbázist |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Az adatbázis és a hozzá tartozó adatbázis titkosítási kulcsainak titkosítási állapotával kapcsolatos információkat ad vissza. |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Az egyes Azure szinapszis-csomópontok és a hozzájuk társított adatbázis-titkosítási kulcsok titkosítási állapotával kapcsolatos információkat adja vissza. |
|  | |

A TDE-védőt nem lehet a kulcsra váltani Key Vault a Transact-SQL használatával. Használja a PowerShellt vagy a Azure Portal.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

A TDE kezelése a REST API használatával.

A TDE a REST APIon keresztüli konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security Managerrel kell csatlakoznia.
A következő parancsokat használja a Azure SQL Database és az Azure szinapszis-hoz:

| Parancs | Leírás |
| --- | --- |
|[Kiszolgáló létrehozása vagy frissítése](/rest/api/sql/servers/createorupdate)|Egy Azure Active Directory identitás hozzáadására szolgál. (a Key Vaulthoz való hozzáférés biztosításához használatos)|
|[Kiszolgáló kulcsának létrehozása vagy frissítése](/rest/api/sql/serverkeys/createorupdate)|Egy Key Vault kulcs hozzáadására szolgál.|
|[Kiszolgáló kulcsának törlése](/rest/api/sql/serverkeys/delete)|Eltávolít egy Key Vault kulcsot egy kiszolgálóról. |
|[Kiszolgálói kulcsok beolvasása](/rest/api/sql/serverkeys/get)|Egy adott Key Vault kulcs beolvasása egy kiszolgálóról.|
|[Kiszolgálói kulcsok listázása kiszolgáló alapján](/rest/api/sql/serverkeys/listbyserver)|Lekéri egy kiszolgáló Key Vault kulcsait. |
|[Titkosítási védő létrehozása vagy frissítése](/rest/api/sql/encryptionprotectors/createorupdate)|Beállítja a kiszolgáló TDE-védőjét.|
|[Titkosítási védő beolvasása](/rest/api/sql/encryptionprotectors/get)|Lekéri egy kiszolgáló TDE-védőjét.|
|[Titkosítási védők listázása kiszolgáló szerint](/rest/api/sql/encryptionprotectors/listbyserver)|Lekéri egy kiszolgáló TDE-védőit. |
|[transzparens adattitkosítás konfiguráció létrehozása vagy frissítése](/rest/api/sql/transparentdataencryptions/createorupdate)|Engedélyezheti vagy letilthatja a TDE egy adatbázishoz.|
|[transzparens adattitkosítás konfiguráció beolvasása](/rest/api/sql/transparentdataencryptions/get)|Lekéri egy adatbázis TDE-konfigurációját.|
|[A transzparens adattitkosítás konfigurációs eredményeinek listázása](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Lekéri egy adatbázis titkosítási eredményét.|

## <a name="see-also"></a>Lásd még:

- Az Azure-beli virtuális gépeken futó SQL Server a Key Vault aszimmetrikus kulcsát is használhatja. A konfigurációs lépések eltérnek a SQL Database és az SQL felügyelt példányának aszimmetrikus kulcsának használatával. További információ: [bővíthető kulcskezelő Azure Key Vault (SQL Server) használatával](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- A TDE általános ismertetését lásd: [transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Ha többet szeretne megtudni a Azure SQL Database, az Azure SQL felügyelt példányának és az Azure szinapszisnak a BYOK-támogatásával kapcsolatos TDE, tekintse meg [a bring your own Key-támogatással rendelkező transzparens adattitkosítás](transparent-data-encryption-byok-overview.md)
- A TDE Bring Your Own Key-támogatással való használatának megkezdéséhez tekintse meg a útmutató című témakört, és [kapcsolja be a transzparens adattitkosítást a saját kulcsának használatával Key Vaultból](transparent-data-encryption-byok-configure.md).
- További információ a Key Vaultről: [biztonságos hozzáférés a kulcstartóhoz](../../key-vault/general/secure-your-key-vault.md).