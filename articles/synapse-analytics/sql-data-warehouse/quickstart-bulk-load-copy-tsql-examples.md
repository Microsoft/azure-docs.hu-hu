---
title: Hitelesítési mechanizmusok a COPY utasítással
description: Az adatok tömeges betöltésének hitelesítési mechanizmusait ismerteti
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 510f2556fba42176817b782fe48d01d76eaa3fd7
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568454"
---
# <a name="securely-load-data-using-synapse-sql"></a>Adatok biztonságos betöltése a Synapse SQL

Ez a cikk a COPY utasítás biztonságos hitelesítési mechanizmusait emeli ki és mutat [be példákkal.](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) A COPY utasítás az adatok tömeges betöltésének legrugalmasabb és legbiztonságosabb módja a Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Támogatott hitelesítési mechanizmusok

Az alábbi mátrix az egyes fájltípusokat és tárfiókokat támogató hitelesítési módszereket ismerteti. Ez a forrástárhelyre és a hibafájl helyére vonatkozik.

|                          |                CSV                |                      Parquet                       |                        Ork                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure Blob Storage**  | SAS/MSI/SZOLGÁLTATÁSNÉV/KULCS/AAD |                      SAS/KULCS                       |                      SAS/KULCS                       |
| **2. generációs Azure Data Lake** | SAS/MSI/SZOLGÁLTATÁSNÉV/KULCS/AAD | SAS (blob<sup>1</sup>)/MSI (dfs<sup>2</sup>)/SZOLGÁLTATÁSNÉV/KULCS/AAD | SAS (blob<sup>1</sup>)/MSI (dfs<sup>2</sup>)/SZOLGÁLTATÁSNÉV/KULCS/AAD |

1: A külső hely elérési útjának .blob végpontjára (**.blob**.core.windows.net) van szükség ehhez a hitelesítési módszerhez.

2: Ehhez a hitelesítési módszerhez a külső elérési úton található .dfs végpontra (**.dfs**.core.windows.net) van szükség.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Tárfiókkulcs, sorválasztóként LF (Unix-stílusú új sor)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - A hexadecimális érték (0x0A) használatával adja meg a Sorcsatorna/Sorsor karaktert. Vegye figyelembe, hogy a COPY utasítás az \n sztringet \r\n formátumban értelmezi (kocsivissza sor sor).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Közös hozzáférésű jogosultságok (SAS) a CRLF-fel sorválasztóként (Windows stílusú új sor)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Ne adja meg a ROWTERMINATOR értéket \r\n értékként, amelyet a rendszer \r\r\nként értelmez, és elemzési problémákat okozhat

## <a name="c-managed-identity"></a>C. Felügyelt identitás

Felügyelt identitás hitelesítése akkor szükséges, ha a tárfiók egy virtuális hálózathoz van csatlakoztatva. 

### <a name="prerequisites"></a>Előfeltételek

1. Telepítse az Azure PowerShellt a kapcsolódó [útmutató](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) alapján.
2. Ha általános célú v1 vagy Blob Storage-fiókja van, először frissítenie kell az általános célú v2 fiókra az [itt található útmutatások](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) szerint.
3. Az Azure Storage-fiók Tűzfalak és virtuális hálózatok **beállításai menüjében** be kell Microsoft-szolgáltatások a Tárfiók elérésének engedélyezése megbízható fiókok **számára** beállítást. További információt ebben az [útmutatóban](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) talál.

#### <a name="steps"></a>Lépések

1. Ha önálló dedikált SQL-készlete van, regisztrálja az SQL Servert az Azure Active Directory (AAD) szolgáltatásban a PowerShell használatával: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Ez a lépés nem szükséges a Synapse-munkaterületen belüli dedikált SQL-készletekhez.

1. Ha Synapse-munkaterülettel dolgozik, regisztrálja a munkaterület rendszer által felügyelt identitását:

   1. A synapse-munkaterület megnyitása a Azure Portal
   2. Ugrás a Felügyelt identitások panelre 
   3. Győződjön meg arról, hogy az "Allow Pipelines" (Folyamatok engedélyezése) beállítás engedélyezve van
   
   ![Munkaterület-rendszer regisztrálása msi](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. Hozzon **létre egy általános célú v2-tárfiókot** az útmutató [segítségével.](../../storage/common/storage-account-create.md)

   > [!NOTE]
   >
   > - Ha általános célú v1- vagy Blob Storage-fiókja van, először frissítenie kell **a v2-re** az útmutató [használatával.](../../storage/common/storage-account-upgrade.md)
   > - A probléma ismert Azure Data Lake Storage Gen2 tekintse meg ezt az [útmutatót.](../../storage/blobs/data-lake-storage-known-issues.md)

1. A tárfiók alatt lépjen a Access Control **(IAM) pontra,** és válassza a **Szerepkör-hozzárendelés hozzáadása lehetőséget.** Rendeljen **Storage-blobadatok közreműködője** Azure-szerepkört a dedikált SQL-készletet üzemeltető kiszolgálóhoz vagy munkaterülethez, amelyet az Azure Active Directory (AAD) regisztrált.

   > [!NOTE]
   > Ezt a lépést csak a Tulajdonos jogosultsággal rendelkező tagok hajthatja végre. Különböző beépített Azure-szerepkörökért tekintse meg ezt az [útmutatót.](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
   
    > [!IMPORTANT]
    > Adja meg a **Storage-blobadatok** tulajdonosa, közreműködője vagy olvasó Azure-szerepkörét.  Ezek a szerepkörök eltérnek az Azure beépített Tulajdonos, Közreműködő és Olvasó szerepköreitől. 

    ![Azure RBAC-engedély megadása a betöltéshez](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Most már futtathatja a "Managed Identity" (Felügyelt identitás) megadásával rendelkező COPY utasítást:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication"></a>D. Azure Active Directory-hitelesítés
#### <a name="steps"></a>Lépések

1. A tárfiókja alatt lépjen a Access Control **(IAM) pontra,** és válassza a **Szerepkör-hozzárendelés hozzáadása lehetőséget.** Rendeljen **Storage-blobadatok tulajdonosi,** közreműködői vagy olvasói Azure-szerepkört az Azure AD-felhasználóhoz. 

    > [!IMPORTANT]
    > Adja meg a Storage **Blob-adatok** tulajdonosa, Közreműködő vagy Olvasó Azure-szerepkört.  Ezek a szerepkörök eltérnek az Azure beépített Tulajdonos, Közreműködő és Olvasó szerepköreitől.

    ![Azure RBAC-engedély megadása a betöltéshez](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Konfigurálja az Azure AD-hitelesítést az alábbi [dokumentációban.](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell) 

3. Csatlakozzon az SQL-készlethez a Active Directory, ahol a HITELESÍTŐ adatok megadása nélkül futtathatja a COPY utasítást:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Szolgáltatásnév-hitelesítés
#### <a name="steps"></a>Lépések

1. [Új Azure Active Directory létrehozása](../..//active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Alkalmazásazonosító lekérte](../..//active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
3. [A hitelesítési kulcs lekérte](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [A V1 OAuth 2.0 jogkivonatvégpont beszerzése](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. Olvasási, írási és végrehajtási engedélyek hozzárendelése az [Azure AD-alkalmazáshoz](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) a tárfiókban
6. Most már futtathatja a COPY utasítást:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Az OAuth 2.0-jogkivonatvégpont **V1** verziójának használata

## <a name="next-steps"></a>Következő lépések

- A részletes [szintaxist a COPY utasítás](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) cikkben talál
- Az ajánlott [betöltési eljárásokért](./design-elt-data-loading.md#what-is-elt) tekintse meg az adatbetöltés áttekintését ismertető cikket
