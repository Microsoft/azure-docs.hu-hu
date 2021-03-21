---
title: Hitelesítési mechanizmusok a COPY utasítással
description: Az adatmennyiség tömeges betöltésére szolgáló hitelesítési mechanizmusok körvonalazása
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: 70e8f15b2b02008f24c87cfe70372fccbf0506fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600122"
---
# <a name="securely-load-data-using-synapse-sql"></a>Adattárolás biztonságos betöltése a szinapszis SQL használatával

Ez a cikk a [copy utasítás](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)biztonságos hitelesítési mechanizmusaival kapcsolatos példákat mutatja be és ismerteti. A MÁSOLÁSi utasítás az adatok tömeges betöltésének legrugalmasabb és biztonságos módja a szinapszis SQL-ben.
## <a name="supported-authentication-mechanisms"></a>Támogatott hitelesítési mechanizmusok

A következő mátrix ismerteti az egyes fájltípusok és a Storage-fiókok támogatott hitelesítési módszereit. Ez a forrás tárolási helyére és a hiba fájljának helyére vonatkozik.

|                          |                CSV                |                      Parquet                       |                        ORC                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure Blob Storage**  | SAS/MSI/EGYSZERŰ SZOLGÁLTATÁSNÉV/KULCS/HRE |                      SAS/KULCS                       |                      SAS/KULCS                       |
| **Azure Data Lake Gen2** | SAS/MSI/EGYSZERŰ SZOLGÁLTATÁSNÉV/KULCS/HRE | SAS (blob<sup>1</sup>)/MSI (DFS<sup>2</sup>)/Service elsődleges/kulcs/HRE | SAS (blob<sup>1</sup>)/MSI (DFS<sup>2</sup>)/Service elsődleges/kulcs/HRE |

1: a hitelesítési módszerhez a külső hely elérési útjában lévő. blob-végpontot (**. blob**. Core.Windows.net) kell megadni.

2: a hitelesítési módszerhez a külső hely elérési útjában található. DFS-végpontot (**. DFS**. Core.Windows.net) kötelező megadni.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Storage-fiók kulcsa a LF-rel a lezáró sor (Unix stílusú új sor)


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
> - Használja a hexadecimális értéket (0x0A) a soremelés/sortörési karakter megadásához. Megjegyzés: a COPY utasítás a "\n" karakterláncot "\r\n"-ként értelmezi (a kocsivissza karakter).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Közös hozzáférésű aláírások (SAS) a CRLF (Windows Style New line)
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
> - Ne adja meg a ROWTERMINATOR "\r\n" néven, amely "\r\r\n" lesz értelmezve, és elemzési problémákhoz vezethet

## <a name="c-managed-identity"></a>C. Felügyelt identitás

A felügyelt identitás hitelesítésére akkor van szükség, ha a Storage-fiók VNet van csatolva. 

### <a name="prerequisites"></a>Előfeltételek

1. Telepítse az Azure PowerShellt a kapcsolódó [útmutató](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) alapján.
2. Ha általános célú v1 vagy Blob Storage-fiókja van, először frissítenie kell az általános célú v2 fiókra az [itt található útmutatások](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) szerint.
3. Engedélyeznie kell, **hogy a megbízható Microsoft-szolgáltatások hozzáférjenek ehhez a Storage-fiókhoz** az Azure Storage **-fiók tűzfala és a virtuális hálózatok** beállítások menüjében. További információt ebben az [útmutatóban](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) talál.

#### <a name="steps"></a>Lépések

1. Ha önálló dedikált SQL-készlettel rendelkezik, regisztrálja az SQL Servert Azure Active Directory (HRE) a PowerShell használatával: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Ez a lépés nem szükséges egy szinapszis-munkaterületen található dedikált SQL-készletekhez.

1. Ha van egy szinapszis-munkaterülete, regisztrálja a munkaterület rendszer által felügyelt identitását:

   1. Nyissa meg a szinapszis munkaterületet a Azure Portal
   2. Ugrás a felügyelt identitások panelre 
   3. Győződjön meg arról, hogy a "folyamatok engedélyezése" beállítás engedélyezve van
   
   ![Munkaterület-rendszermsi-fájl regisztrálása](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. Hozzon létre egy **általános célú v2 Storage-fiókot** az [útmutató](../../storage/common/storage-account-create.md)segítségével.

   > [!NOTE]
   >
   > - Ha rendelkezik általános célú v1-vagy blob Storage-fiókkal, először a **v2-re kell frissítenie** az [útmutató](../../storage/common/storage-account-upgrade.md)segítségével.
   > - Azure Data Lake Storage Gen2 kapcsolatos ismert problémák esetén tekintse meg ezt az [útmutatót](../../storage/blobs/data-lake-storage-known-issues.md).

1. A Storage-fiók területen navigáljon a **Access Control (iam)** elemre, és válassza a **szerepkör-hozzárendelés hozzáadása** elemet. Rendeljen **Storage blob-adatközreműködő** Azure-szerepkört a dedikált SQL-készletet futtató kiszolgálóhoz vagy munkaterülethez, amelyet a Azure Active Directory (HRE) regisztrált.

   > [!NOTE]
   > Ezt a lépést csak a tulajdonosi jogosultsággal rendelkező tagok hajthatják végre. A különböző Azure-beli beépített szerepkörökhöz tekintse meg ezt az [útmutatót](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
   
    > [!IMPORTANT]
    > A **Storage** **blob** -adattulajdonos, közreműködő vagy olvasó Azure-szerepkör meghatározása. Ezek a szerepkörök eltérnek a tulajdonos, közreműködő és olvasó Azure beépített szerepköreitől. 

    ![Azure RBAC-engedély betöltésének engedélyezése](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Most már futtathatja a "felügyelt identitás" megadását megadó MÁSOLÁSi utasítást:

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

1. A Storage-fiók területen navigáljon a **Access Control (iam)** elemre, és válassza a **szerepkör-hozzárendelés hozzáadása** elemet. Rendeljen **Storage blob-Adattulajdonost, közreműködőt vagy olvasó** Azure-szerepkört az Azure ad-felhasználóhoz. 

    > [!IMPORTANT]
    > A **Storage** **blob** -adattulajdonos, közreműködő vagy olvasó Azure-szerepkör meghatározása. Ezek a szerepkörök eltérnek a tulajdonos, közreműködő és olvasó Azure beépített szerepköreitől.

    ![Azure RBAC-engedély betöltésének engedélyezése](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Konfigurálja az Azure AD-hitelesítést az alábbi [dokumentáción](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)keresztül. 

3. Kapcsolódjon az SQL-készlethez Active Directory használatával, ahol a MÁSOLÁSi utasítást a hitelesítő adatok megadása nélkül is futtathatja:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Szolgáltatásnév-hitelesítés
#### <a name="steps"></a>Lépések

1. [Azure Active Directory-alkalmazás létrehozása](../..//active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Alkalmazás AZONOSÍTÓjának beolvasása](../..//active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
3. [A hitelesítési kulcs beszerzése](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [A v1 OAuth 2,0 token végpontjának beolvasása](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Olvasási, írási és végrehajtási engedélyek kiosztása az Azure ad-alkalmazáshoz](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) a Storage-fiókban
6. Ezután futtathatja a COPY utasítást:

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
> - Az OAuth 2,0 token végpont **v1** -es verziójának használata

## <a name="next-steps"></a>Következő lépések

- A részletes szintaxissal kapcsolatban olvassa el a [copy utasítással](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) kapcsolatos cikket.
- Az ajánlott eljárások betöltéséhez tekintse meg az [adatgyűjtés áttekintése című](./design-elt-data-loading.md#what-is-elt) cikket
