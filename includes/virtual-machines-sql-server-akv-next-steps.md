---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 6195b949cc71043dfa7a12bdece7a311dbde5e21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95558695"
---
## <a name="next-steps"></a>Következő lépések

Azure Key Vault integráció engedélyezése után engedélyezheti SQL Server titkosítását az SQL-alapú virtuális gépen. Először létre kell hoznia egy aszimmetrikus kulcsot a kulcstartóban és egy szimmetrikus kulcsot a virtuális gépen SQL Serveron belül. Ezt követően a T-SQL-utasítások végrehajtásával engedélyezheti az adatbázisok és a biztonsági másolatok titkosítását.

A titkosítás többféle formában is kihasználható:

* [Transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Titkosított biztonsági másolatok](/sql/relational-databases/backup-restore/backup-encryption)
* [Oszlop szintű titkosítás (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)

A következő Transact-SQL-szkriptek példákat biztosítanak ezekre a területekre vonatkozóan.

### <a name="prerequisites-for-examples"></a>Példák az előfeltételekre

Mindegyik példa a két előfeltételen alapul: egy **CONTOSO_KEY** nevű kulcstartó aszimmetrikus kulcsára és egy **Azure_EKM_cred** nevű AKV-integrációs szolgáltatás által létrehozott hitelesítő adatra. A következő Transact-SQL-parancsok a példák futtatásához szükséges előfeltételeket állítják be.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL Azure_EKM_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL Azure_EKM_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

1. Hozzon létre egy SQL Server bejelentkezési azonosítót, amelyet a TDE adatbázismotor használ, majd adja hozzá a hitelesítő adatokat.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Hozza létre az adatbázis-titkosítási kulcsot, amelyet a rendszer a TDE fog használni.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Titkosított biztonsági másolatok

1. Hozzon létre egy SQL Server bejelentkezési azonosítót, amelyet az adatbázismotor használ a biztonsági másolatok titkosításához, és adja hozzá a hitelesítő adatokat.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Biztonsági másolat készítése az adatbázisról a Key vaultban tárolt aszimmetrikus kulccsal történő titkosítás megadásával.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Oszlop szintű titkosítás (CLE)

Ez a szkript létrehoz egy szimmetrikus kulcsot, amelyet az aszimmetrikus kulcs véd a kulcstartóban, majd a szimmetrikus kulcs használatával titkosítja az adatbázist.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>További források

A titkosítási funkciók használatáról további információt a [EKM használata SQL Server titkosítási funkciókkal](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server#UsesOfEKM)című témakörben talál.

Vegye figyelembe, hogy a cikkben ismertetett lépések azt feltételezik, hogy már fut SQL Server Azure-beli virtuális gépen. Ha nem, tekintse [meg a SQL Server virtuális gép üzembe helyezése az Azure-ban](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md)című témakört. A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további útmutatásért lásd: [SQL Server az azure Virtual Machines áttekintése](../articles/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).