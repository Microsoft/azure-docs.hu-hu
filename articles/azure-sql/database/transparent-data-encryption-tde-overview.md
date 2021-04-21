---
title: Transzparens adattitkosítás
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: A transzparens adattitkosítás áttekintése a Azure SQL Database, Azure SQL Managed Instance és Azure Synapse Analytics. A dokumentum a szolgáltatás által felügyelt transzparens adattitkosítást és adattitkosítást, valamint a konfigurálási lehetőségeket Bring Your Own Key.
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
ms.openlocfilehash: f93d65b4d10c1a8454a8e24b5cb081dae4d6943e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812803"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transzparens adattitkosítás SQL Database, SQL Managed Instance és Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A transzparens adattitkosítás [(TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) Azure SQL Database, Azure SQL Managed Instance és Azure Synapse Analytics az offline tevékenységek fenyegetése elleni védelemhez az aktív adatok titkosítása révén. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE engedélyezve van az összes újonnan üzembe helyezett SQL Database-adatbázishoz, és manuálisan kell engedélyezni a régebbi, Azure SQL Database Azure SQL Managed Instance. A TDE-t manuálisan kell engedélyezni a Azure Synapse Analytics.

A TDE valós idejű I/O-titkosítást és -visszafejtést végez az adatokon az oldal szintjén. Az egyes lapok visszafejtése a memóriába történő beolvasáskor történik, a titkosítás pedig a lemezre írás előtt. A TDE egy teljes adatbázis tárolását titkosítja az Adatbázis-titkosítási kulcs (DEK) nevű szimmetrikus kulccsal. Az adatbázis indításakor a titkosított adattitkosítási kulcsot visszafejtik, majd az adatbázismotor SQL Server visszafejtéséhez és újratitkosításához használják. A DEK-t a TDE-védő védi. A TDE-védő egy szolgáltatás által felügyelt tanúsítvány (szolgáltatás által felügyelt transzparens adattitkosítás) [vagy](../../key-vault/general/security-features.md) a Azure Key Vault-ban tárolt aszimmetrikus kulcs (ügyfél által felügyelt transzparens adattitkosítás).

A Azure SQL Database és Azure Synapse A TDE-védő a kiszolgáló szintjén [](logical-servers.md) van beállítva, és a kiszolgálóhoz társított összes adatbázis örökli. A Azure SQL Managed Instance A TDE-védő a példány szintjén van beállítva, és a példány összes titkosított adatbázisa örökli. A *kiszolgáló kifejezés a* dokumentumban a kiszolgálóra és a példányra is vonatkozik, kivéve, ha másként van megszabadulva.

> [!IMPORTANT]
> A szolgáltatás által felügyelt transzparens adattitkosítás alapértelmezés szerint SQL Database összes újonnan létrehozott adatbázist titkosítja. A 2017 májusa előtt létrehozott meglévő SQL-adatbázisok és a visszaállítással, georeplikációval és adatbázis-másolással létrehozott SQL-adatbázisok alapértelmezés szerint nincsenek titkosítva. A SQL Managed Instance 2019 februárja előtt létrehozott meglévő adatbázisokat a rendszer alapértelmezés szerint nem titkosítja. SQL Managed Instance visszaállítással létrehozott adatbázisok titkosítási állapotát a forrástól öröklik.

> [!NOTE]
> A TDE nem használható rendszeradatbázisok, például a **master** adatbázis titkosítására a Azure SQL Database és Azure SQL Managed Instance. A **master** adatbázis olyan objektumokat tartalmaz, amelyek a TDE-műveletek felhasználói adatbázisokon való végrehajtásához szükségesek. Javasoljuk, hogy a rendszeradatbázisok ne tároljanak bizalmas adatokat. [Folyamatban van az](transparent-data-encryption-byok-overview.md#doubleencryption) infrastruktúra-titkosítás, amely titkosítja a rendszeradatbázisokat, beleértve a főkiszolgálót is. 

## <a name="service-managed-transparent-data-encryption"></a>Szolgáltatás által felügyelt transzparens adattitkosítás

Az Azure-ban a TDE alapértelmezett beállítása az, hogy a DEK-t egy beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány minden kiszolgálóhoz egyedi, a használt titkosítási algoritmus pedig az AES 256. Ha egy adatbázis georeplikációs kapcsolatban van, az elsődleges és a földrajzi másodlagos adatbázist is az elsődleges adatbázis szülőkiszolgálói kulcsa védi. Ha két adatbázis csatlakozik ugyanerhez a kiszolgálóhoz, akkor ugyanazon a beépített tanúsítványon osztoznak. A Microsoft a belső biztonsági házirendnek megfelelően automatikusan váltja fel ezeket a tanúsítványokat, és a legfelső szintű kulcsot egy belső Microsoft titkos kulcstároló védi. Az ügyfelek a Microsoft biztonsági SQL Database SQL Managed Instance elérhető független külső naplózási jelentésekben ellenőrizhetik, hogy megfelelnek-e a belső biztonsági [szabályzatnak, és megfelelnek-e a szabályzatnak.](https://servicetrust.microsoft.com/)

A Microsoft emellett zökkenőmentesen áthelyezi és kezeli a kulcsokat a georeplikációhoz és a visszaállításhoz.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Ügyfél által felügyelt transzparens adattitkosítás – Bring Your Own Key

Az ügyfél által felügyelt TDE más néven Bring Your Own Key (BYOK) támogatása a TDE-hez. Ebben a forgatókönyvben a DEK-t titkosító TDE-védő egy ügyfél által felügyelt aszimmetrikus kulcs, amely egy ügyfél tulajdonában lévő és felügyelt Azure Key Vault-ban (az Azure felhőalapú külső kulcskezelő rendszerében) van tárolva, és soha nem hagyja el a kulcstartót. A TDE-védőt a [kulcstartó](../../key-vault/keys/hsm-protected-keys.md) generálhatja, vagy átviheti a kulcstartóba egy helyszíni hardveres biztonsági modul (HSM) eszközéről. SQL Database, SQL Managed Instance és Azure Synapse engedélyeket kell adni az ügyfél tulajdonában lévő kulcstartónak az ADATTITK visszafejtéséhez és titkosításához. Ha visszavonják a kiszolgáló kulcstartó-engedélyét, az adatbázis nem lesz elérhető, és minden adat titkosítva lesz

Az Azure Key Vault-integrációval rendelkező TDE segítségével a felhasználók szabályozni tudja a kulcskezelési feladatokat, beleértve a kulcsrotálásokat, a kulcstartó-engedélyeket és a kulcsmentéseket, valamint engedélyezhetik az összes TDE-védő naplózását és jelentéskészítését Azure Key Vault funkciók használatával. Key Vault központi kulcskezelést biztosít, kihasználja a szorosan figyelt HSM-eket, és lehetővé teszi a kulcsok és adatok kezelése közötti feladatok elkülönítését a biztonsági házirendek betartásának biztosítása érdekében.
További információ a byOK-Azure SQL Database és Azure Synapse: Transzparens adattitkosítás [Azure Key Vault integrációval.](transparent-data-encryption-byok-overview.md)

A TDE és a Azure Key Vault használatának első Azure Key Vault [útmutatója A](transparent-data-encryption-byok-configure.md)transzparens adattitkosítás bekapcsolása saját kulccsal a Key Vault.

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transzparens adattitkosítással védett adatbázis áthelyezése

Az Azure-beli műveletekhez nem kell visszafejtenie az adatbázisokat. A forrásadatbázis vagy az elsődleges adatbázis TDE-beállításai transzparens módon öröklődik a célon. A benne foglalt műveletek a következő műveleteket foglalják magukban:

- Georedundáns visszaállítás
- Önkiszolgáló időponthoz időben való visszaállítás
- Törölt adatbázis visszaállítása
- Aktív georeplikáció
- Adatbázis-másolat létrehozása
- A biztonsági mentési fájl visszaállítása a Azure SQL Managed Instance

> [!IMPORTANT]
> A szolgáltatás által felügyelt TDE által titkosított adatbázis manuális COPY-ONLY biztonsági mentését a Azure SQL Managed Instance nem támogatja, mivel a titkosításhoz használt tanúsítvány nem érhető el. Az időponthoz időben való visszaállítás funkcióval áthelyezi az ilyen típusú adatbázist egy másik SQL Managed Instance, vagy átválthat az ügyfél által felügyelt kulcsra.

A TDE által védett adatbázisok exportálhatók, az adatbázis exportált tartalma nem lesz titkosítva. Az exportált tartalmat a rendszer titkosítatlan BACPAC-fájlokban tárolja. Az új adatbázis importálásának befejezése után győződjön meg arról, hogy megfelelően védi a BACPAC-fájlokat, és engedélyezi a TDE-t.

Ha például a BACPAC-fájlt egy SQL Server-példányból exportálják, az új adatbázis importált tartalma nem lesz automatikusan titkosítva. Hasonlóképpen, ha a BACPAC-fájlt egy SQL Server-példányba importálják, az új adatbázis sem lesz automatikusan titkosítva.

Az egyetlen kivétel az, amikor adatbázist exportál a SQL Database. A TDE engedélyezve van az új adatbázisban, de maga a BACPAC-fájl még mindig nincs titkosítva.

## <a name="manage-transparent-data-encryption"></a>Transzparens adattitkosítás kezelése

# <a name="the-azure-portal"></a>[Az Azure Portal](#tab/azure-portal)

A TDE kezelése a Azure Portal.

Ha a TDE-t a Azure Portal konfigurálni, Azure-tulajdonosként, közreműködőként vagy SQL Security Managerként kell csatlakoztatnia.

A TDE engedélyezése és letiltása az adatbázis szintjén. A Azure SQL Managed Instance Transact-SQL (T-SQL) használatával kapcsolhatja be és ki a TDE-t egy adatbázison. A Azure SQL Database és Azure Synapse a TDE-t a Azure Portal kezelheti, [](https://portal.azure.com) miután bejelentkezett az Azure-rendszergazdai vagy közreműködői fiókkal. Keresse meg a TDE-beállításokat a felhasználói adatbázisban. Alapértelmezés szerint a szolgáltatás által felügyelt transzparens adattitkosítás van használatban. A rendszer automatikusan létrehoz egy TDE-tanúsítványt az adatbázist tartalmazó kiszolgálóhoz.

![Szolgáltatás által felügyelt transzparens adattitkosítás](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

A TDE-főkulcsot (más néven TDE-védőt) a kiszolgáló vagy a példány szintjén állíthatja be. Ha a TDE-t BYOK-támogatással és kulccsal Key Vault, nyissa meg a TDE-beállításokat a kiszolgálón.

![Transzparens adattitkosítás Bring Your Own Key támogatással](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A TDE kezelése a PowerShell használatával.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de minden jövőbeli fejlesztés az Az.Sql modulra fog kifejlesztésére. A parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/) Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek.

Ahhoz, hogy a TDE-t a PowerShellen keresztül konfigurálja, Azure-tulajdonosként, közreműködőként vagy SQL Security Managerként kell összekapcsolnia.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Parancsmagok Azure SQL Database és Azure Synapse

Használja a következő parancsmagokat a Azure SQL Database és Azure Synapse:

| Parancsmag | Leírás |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Engedélyezi vagy letiltja egy adatbázis transzparens adattitkosítását.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Lekérte egy adatbázis transzparens adattitkosítási állapotát. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Ellenőrzi egy adatbázis titkosítási folyamatának előrehaladását. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Hozzáad egy Key Vault kulcsot egy kiszolgálóhoz. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Lekérte Key Vault kiszolgálóhoz a következő kulcsokat:  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Beállítja a transzparens adattitkosítási védőt egy kiszolgálóhoz. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Lekérte a transzparens adattitkosítási védőt |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Eltávolít egy Key Vault kulcsot a kiszolgálóról. |
|  | |

> [!IMPORTANT]
> A Azure SQL Managed Instance A T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) paranccsal kapcsolhatja be és ki a TDE-t egy adatbázisszinten, és a PowerShell-példaszkprogramban példányszinten kezelheti a TDE-t. [](transparent-data-encryption-byok-configure.md)

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

A TDE kezelése a Transact-SQL használatával.

Csatlakozzon az adatbázishoz egy olyan bejelentkezéssel, amely rendszergazda vagy a **dbmanager** szerepkör tagja a master adatbázisban.

| Parancs | Leírás |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | A TITKOSÍTÁS BE/KI beállítása titkosítja vagy visszafejti az adatbázist |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Információkat ad vissza egy adatbázis és a hozzá tartozó adatbázis-titkosítási kulcsok titkosítási állapotáról |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Az egyes csomópontok titkosítási állapotáról és Azure Synapse adatbázistitkosítási kulcsokról ad vissza adatokat. |
|  | |

A TDE-védő nem váltható kulcsra a Key Vault Transact-SQL használatával. Használja a PowerShellt vagy a Azure Portal.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

A TDE kezelése a REST API.

Ha a TDE-t a REST API konfigurálni, Azure-tulajdonosként, közreműködőként vagy SQL Security Managerként kell csatlakoztatnia.
Használja a következő parancsokat a Azure SQL Database és Azure Synapse:

| Parancs | Leírás |
| --- | --- |
|[Kiszolgáló létrehozása vagy frissítése](/rest/api/sql/servers/createorupdate)|Hozzáad egy Azure Active Directory identitást egy kiszolgálóhoz. (az alkalmazáshoz való hozzáférés Key Vault)|
|[Kiszolgálókulcs létrehozása vagy frissítése](/rest/api/sql/serverkeys/createorupdate)|Hozzáad egy Key Vault kulcsot egy kiszolgálóhoz.|
|[Kiszolgálókulcs törlése](/rest/api/sql/serverkeys/delete)|Eltávolít egy Key Vault kulcsot a kiszolgálóról. |
|[Kiszolgálókulcsok lekérte](/rest/api/sql/serverkeys/get)|Lekért egy Key Vault kulcsot egy kiszolgálóról.|
|[Kiszolgálókulcsok listából kiszolgáló szerint](/rest/api/sql/serverkeys/listbyserver)|Lekérte Key Vault kiszolgáló összes kulcsát. |
|[Titkosítási védő létrehozása vagy frissítése](/rest/api/sql/encryptionprotectors/createorupdate)|Beállítja a TDE-védőt egy kiszolgálóhoz.|
|[Titkosítási védő lekérte](/rest/api/sql/encryptionprotectors/get)|Lekérte egy kiszolgáló TDE-védőjéhez.|
|[Titkosítási védők listosítása kiszolgáló szerint](/rest/api/sql/encryptionprotectors/listbyserver)|Lekérte egy kiszolgáló TDE-védőit. |
|[Konfiguráció létrehozása vagy transzparens adattitkosítás frissítése](/rest/api/sql/transparentdataencryptions/createorupdate)|Engedélyezi vagy letiltja a TDE-t egy adatbázishoz.|
|[A transzparens adattitkosítás lekért konfigurációja](/rest/api/sql/transparentdataencryptions/get)|Lekérte egy adatbázis TDE-konfigurációját.|
|[A transzparens adattitkosítás találatok listája](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Lekérte egy adatbázis titkosítási eredményét.|

## <a name="see-also"></a>Lásd még:

- SQL Server Azure-beli virtuális gépen futó virtuális gépek szintén használhatnak aszimmetrikus kulcsot a Key Vault. A konfigurációs lépések eltérnek az aszimmetrikus kulcs használatának lépéseitől a SQL Database és SQL Managed Instance. További információ: [Extensible key management by using using Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- A TDE általános leírását lásd: [Transzparens adattitkosítás.](/sql/relational-databases/security/encryption/transparent-data-encryption)
- További információ a TDE-ről a Azure SQL Database, Azure SQL Managed Instance és Azure Synapse BYOK-támogatással: Transzparens adattitkosítás [Bring Your Own Key támogatással.](transparent-data-encryption-byok-overview.md)
- Ha a TDE-t Bring Your Own Key használni, tekintse meg a transzparens adattitkosítás bekapcsolása a saját kulcsával a [Key Vault.](transparent-data-encryption-byok-configure.md)
- További információ a kulcstartók Key Vault: Biztonságos hozzáférés [kulcstartóhoz.](../../key-vault/general/security-features.md)