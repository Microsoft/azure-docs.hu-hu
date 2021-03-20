---
title: TDE-tanúsítvány által felügyelt példány migrálása
description: Egy adatbázis adatbázis-titkosítási kulcsát védő tanúsítvány migrálása az Azure SQL felügyelt példányának transzparens adattitkosítás
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: c465da3d5d812ea7e811cbe59318122700c6e786
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97824662"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>TDE-védelemmel ellátott adatbázis tanúsítványának migrálása az Azure SQL felügyelt példányaira
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ha [transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) által védett adatbázist telepít át az Azure SQL felügyelt példányára a natív visszaállítási lehetőséggel, a SQL Server példány megfelelő tanúsítványát át kell telepíteni az adatbázis-visszaállítás előtt. Ez a cikk végigvezeti a tanúsítvány manuális áttelepítésének folyamatán a felügyelt Azure SQL-példányon:

> [!div class="checklist"]
>
> * A tanúsítvány exportálása személyes információcsere (. pfx) fájlba
> * A tanúsítvány kinyerése fájlból Base-64 sztringre
> * Feltöltés egy PowerShell-parancsmag használatával

Ha egy teljes körűen felügyelt szolgáltatást használ a TDE-védelemmel ellátott adatbázis és a hozzá tartozó tanúsítvány zökkenőmentes áttelepítéséhez, tekintse [meg a helyszíni adatbázis áttelepítése az Azure SQL felügyelt példányra Azure Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md)című témakört.

> [!IMPORTANT]
> Az áttelepített tanúsítvány csak a TDE által védett adatbázis visszaállítására szolgál. A visszaállítást követően a rendszer a példányon beállított TDE típusától függően egy másik oltalmazót, vagy egy szolgáltatás által felügyelt tanúsítványt vagy egy aszimmetrikus kulcsot cserél le a Key vaultból.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

* Telepített [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx) parancssori eszköz egy helyszíni kiszolgálón vagy egy olyan számítógépen, amely hozzáfér a fájlként exportált tanúsítványhoz. A Pvk2Pfx eszköz a [vállalati Windows illesztőprogram-csomag](/windows-hardware/drivers/download-the-wdk)része, amely egy önálló parancssori környezet.
* Telepített [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell), 5.0-s vagy újabb verzió.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Azure PowerShell modul [telepítve és frissítve](/powershell/azure/install-az-ps).
* [Az az. SQL modul](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Az Azure SQL felügyelt példánya továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRM modulok parancsainak argumentumai lényegében azonosak.

Futtassa a következő parancsokat a PowerShellben a modul telepítéséhez/frissítéséhez:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Az TDE-tanúsítvány exportálása. pfx-fájlba

A tanúsítvány közvetlenül a forrás SQL Server-példányból vagy a tanúsítványtárolóból exportálható, ha az ott tart.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>A tanúsítvány exportálása a forrás SQL Server példányból

A következő lépésekkel exportálhatja a tanúsítványt SQL Server Management Studio és konvertálhatja. pfx formátumúra. A rendszer az általános neveket *TDE_Cert* és *full_path* használja a tanúsítvány-és fájlnevek és elérési utak számára a lépéseken keresztül. Ezeket a gyakorlatban a tényleges nevekre kell cserélni.

1. A SSMS-ben nyisson meg egy új lekérdezési ablakot, és kapcsolódjon a forrás SQL Server-példányhoz.

1. Az alábbi szkripttel listázhatja az TDE által védett adatbázisokat, és lekérheti az áttelepítendő adatbázis titkosítását védő tanúsítvány nevét:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![TDE-tanúsítványok listája](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. A következő szkriptet futtatva a tanúsítványt két fájlba (.cer és .pvk) exportálhatja, megőrizve a nyilvános és a titkos kulcs adatait:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![TDE-tanúsítvány biztonsági mentése](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. A PowerShell-konzollal másolja a tanúsítvány információit egy pár újonnan létrehozott fájlból egy. pfx-fájlba a Pvk2Pfx eszköz használatával:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Tanúsítvány exportálása tanúsítványtárolóból

Ha a tanúsítványt a SQL Server helyi számítógép tanúsítványtárolójában tárolja, a következő lépésekkel exportálhatja:

1. Nyissa meg a PowerShell-konzolt, és hajtsa végre a következő parancsot a Microsoft Management Console Tanúsítványkezelő beépülő moduljának megnyitásához:

   ```cmd
   certlm
   ```

2. A tanúsítványok MMC beépülő modulban bontsa ki az elérési út személyes > tanúsítványok elemet a tanúsítványok listájának megtekintéséhez.

3. Kattintson a jobb gombbal a tanúsítványra, majd kattintson az **Exportálás** parancsra.

4. Kövesse a varázslót a tanúsítvány és a titkos kulcs. pfx formátumba való exportálásához.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>A tanúsítvány feltöltése az Azure SQL felügyelt példányára egy Azure PowerShell-parancsmag használatával

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Előkészítő lépések a PowerShellben:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Az előkészítési lépések elvégzése után futtassa a következő parancsokat a Base-64 kódolású tanúsítvány feltöltéséhez a cél felügyelt példányra:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először létre kell hoznia [egy Azure Key vaultot](../../key-vault/general/manage-with-cli2.md) a *. pfx* fájllal.

1. Előkészítő lépések a PowerShellben:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Az előkészítési lépések elvégzése után futtassa a következő parancsokat, hogy feltöltse a Base-64 kódolású tanúsítványt a cél felügyelt példányra:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

A tanúsítvány mostantól elérhető a megadott felügyelt példányon, és a megfelelő TDE-védelemmel ellátott adatbázis biztonsági mentése sikeresen visszaállítható.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan telepíthet át egy olyan tanúsítványt, amely egy adatbázis titkosítási kulcsát védi transzparens adattitkosítás, a helyszíni vagy IaaS SQL Server példányról az Azure SQL felügyelt példányára.

Lásd: [adatbázis biztonsági másolatának visszaállítása egy Azure SQL felügyelt példányra](restore-sample-database-quickstart.md) , amelyből megtudhatja, hogyan állíthatja vissza az adatbázisok biztonsági mentését az Azure SQL felügyelt példányaira.