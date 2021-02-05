---
title: A Storage-fiók hozzáférésének szabályozása kiszolgáló nélküli SQL-készlethez
description: Leírja, hogy a kiszolgáló nélküli SQL-készlet hogyan fér hozzá az Azure Storage-hoz, és Hogyan szabályozható a kiszolgáló nélküli SQL-készlet tárolási hozzáférése az Azure szinapszis Analyticsben.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: c9a5be358c40c3411115d8c2ee3f9471c68771b8
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576210"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>A Storage-fiók hozzáférésének szabályozása kiszolgáló nélküli SQL-készlethez az Azure szinapszis Analyticsben

A kiszolgáló nélküli SQL-készlet lekérdezése közvetlenül az Azure Storage-ból olvassa be a fájlokat. Az Azure Storage-beli fájlok eléréséhez szükséges engedélyek két szinten vannak szabályozva:
- **Tárolási szint** – a felhasználónak engedéllyel kell rendelkeznie a mögöttes tárolási fájlok eléréséhez. A tároló rendszergazdájának engedélyeznie kell az Azure AD-rendszerbiztonsági tag számára a fájlok olvasását/írását, vagy a tároló eléréséhez használni kívánt SAS-kulcs létrehozását.
- **SQL-szolgáltatási szint** – a felhasználónak engedélyt kell adni az adat [külső tábla](develop-tables-external-tables.md) használatával történő olvasásához vagy a `OPENROWSET` függvény végrehajtásához. További információ [a szükséges engedélyekről ebben a szakaszban](develop-storage-files-overview.md#permissions).

Ez a cikk ismerteti a használható hitelesítő adatok típusait, valamint azt, hogy az SQL és az Azure AD-felhasználók hogyan használják a hitelesítő adatokat.

## <a name="supported-storage-authorization-types"></a>Támogatott tárterület-engedélyezési típusok

A kiszolgáló nélküli SQL-készletbe bejelentkezett felhasználók számára engedélyezni kell az Azure Storage-ban tárolt fájlok elérését és lekérdezését, ha a fájlok nyilvánosan nem érhetők el. Három engedélyezési típust használhat a nem nyilvános tár – [felhasználói identitás](?tabs=user-identity), [közös hozzáférésű aláírás](?tabs=shared-access-signature)és [felügyelt identitás](?tabs=managed-identity)eléréséhez.

> [!NOTE]
> A munkaterületek létrehozásakor az **Azure ad pass-through** az alapértelmezett viselkedés.

### <a name="user-identity"></a>[Felhasználói identitás](#tab/user-identity)

A **felhasználói identitás**, amely az "Azure ad pass-through" néven is ismert, olyan engedélyezési típus, ahol a kiszolgáló nélküli SQL-készletbe bejelentkezett Azure ad-felhasználó identitása az adatok elérésének engedélyezésére szolgál. Az adatok elérése előtt az Azure Storage rendszergazdájának engedélyeket kell adnia az Azure AD-felhasználónak. Ahogy az alábbi táblázatban is látható, az SQL-felhasználó típusa nem támogatott.

> [!IMPORTANT]
> Az adatok eléréséhez rendelkeznie kell egy Storage blob-adattulajdonosi/közreműködői/olvasói szerepkörrel, amely az Ön identitását használja.
> Még ha Ön is egy Storage-fiók tulajdonosa, akkor is hozzá kell adnia magát a Storage blob adatszerepköreinek egyikéhez.
>
> Ha többet szeretne megtudni a Azure Data Lake Store Gen2 való hozzáférés-vezérlésről, tekintse át a [Azure Data Lake Storage Gen2 cikkben található hozzáférés-vezérlést](../../storage/blobs/data-lake-storage-access-control.md) .
>

### <a name="shared-access-signature"></a>[Közös hozzáférésű jogosultságkód](#tab/shared-access-signature)

A **közös hozzáférésű aláírás (SAS)** delegált hozzáférést biztosít a Storage-fiók erőforrásaihoz. Az SAS használatával az ügyfelek a fiók kulcsainak megosztása nélkül biztosíthatnak hozzáférést a Storage-fiók erőforrásaihoz. Az SAS részletesen szabályozza a hozzáférést az olyan ügyfelek számára, akik SAS-val rendelkeznek, beleértve az érvényességi időt, a megadott engedélyeket, az elfogadható IP-címtartományt és az elfogadható protokollt (https/http).

SAS-token beszerzéséhez lépjen a **Azure Portal-> Storage-fiókhoz – > közös hozzáférésű aláírás – > konfigurálja az engedélyeket – > sas létrehozása és kapcsolati karakterlánc.**

> [!IMPORTANT]
> SAS-token létrehozásakor a jogkivonat elején szerepel egy kérdőjel ("?"). Ha a tokent kiszolgáló nélküli SQL-készletben szeretné használni, el kell távolítania a kérdőjelet ("?") a hitelesítő adatok létrehozásakor. Például:
>
> SAS-jogkivonat:? SV = 2018-03-28&SS = bfqt&SRT = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = HTTPS&SIG = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

Ha SAS-token használatával szeretné engedélyezni a hozzáférést, létre kell hoznia egy adatbázis-hatókörű vagy kiszolgáló-hatókörű hitelesítő adatot. 

### <a name="managed-identity"></a>[Felügyelt identitás](#tab/managed-identity)

A **felügyelt identitást** MSI-ként is nevezzük. Azure Active Directory (Azure AD) szolgáltatása, amely Azure-szolgáltatásokat biztosít a kiszolgáló nélküli SQL-készlethez. Emellett automatikusan felügyelt identitást helyez üzembe az Azure AD-ben. Ez az identitás használható az Azure Storage-beli adatelérési kérelem engedélyezésére.

Az adatok elérése előtt az Azure Storage rendszergazdájának engedélyeket kell adnia a felügyelt identitásnak az adatok eléréséhez. A felügyelt identitásra vonatkozó engedélyek megadásának módja ugyanúgy történik, mint bármely más Azure AD-felhasználó számára.

### <a name="anonymous-access"></a>[Névtelen hozzáférés](#tab/public-access)

A [névtelen hozzáférést engedélyező](../../storage/blobs/anonymous-read-access-configure.md)Azure Storage-fiókokban elhelyezett, nyilvánosan elérhető fájlokat is elérheti.

---

### <a name="supported-authorization-types-for-databases-users"></a>Támogatott engedélyezési típusok az adatbázisok felhasználói számára

Az alábbi táblázatban az elérhető engedélyezési típusok találhatók:

| Engedélyezés típusa                    | *SQL-felhasználó*    | *Azure AD-felhasználó*     |
| ------------------------------------- | ------------- | -----------    |
| [Felhasználói identitás](?tabs=user-identity#supported-storage-authorization-types)       | Nem támogatott | Támogatott      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Támogatott     | Támogatott      |
| [Felügyelt identitás](?tabs=managed-identity#supported-storage-authorization-types) | Nem támogatott | Támogatott      |

### <a name="supported-storages-and-authorization-types"></a>Támogatott tárolók és engedélyezési típusok

Az engedélyezési és az Azure Storage-típusok következő kombinációit használhatja:

| Engedélyezés típusa  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Támogatott\*      | Nem támogatott   | Támogatott\*     |
| [Felügyelt identitás](?tabs=managed-identity#supported-storage-authorization-types) | Támogatott      | Támogatott        | Támogatott     |
| [Felhasználói identitás](?tabs=user-identity#supported-storage-authorization-types)    | Támogatott\*      | Támogatott\*        | Támogatott\*     |

\* Az SAS-token és az Azure AD-identitás használható a tűzfallal védett tárolók eléréséhez.


### <a name="querying-firewall-protected-storage"></a>Tűzfal által védett tárterület lekérdezése

A tűzfallal védett tárolók eléréséhez használhatja a **felhasználói identitást** vagy a **felügyelt identitást**.

> [!NOTE]
> A Storage tűzfal szolgáltatás nyilvános előzetes verzióban érhető el, és minden nyilvános felhőben elérhető. 

#### <a name="user-identity"></a>Felhasználói identitás

A tűzfallal védett tárhely felhasználói identitáson keresztüli eléréséhez az az. Storage PowerShell-modult használhatja.
#### <a name="configuration-via-powershell"></a>Konfigurálás a PowerShell használatával

Az alábbi lépéseket követve konfigurálja a Storage-fiók tűzfalát, és vegyen fel egy kivételt a szinapszis-munkaterületre.

1. A PowerShell megnyitása vagy a [PowerShell telepítése](/powershell/scripting/install/installing-powershell-core-on-windows?preserve-view=true&view=powershell-7.1)
2. Telepítse az az. Storage 3.0.1-modult és az az. szinapszis 0.7.0: 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.0.1-preview -AllowPrerelease
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > Győződjön meg arról, hogy a 3.0.1-es **verziót** használja. Az az. Storage verzióját a következő parancs futtatásával tekintheti meg:  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Kapcsolódjon az Azure-bérlőhöz: 
    ```powershell
    Connect-AzAccount
    ```
4. Változók definiálása a PowerShellben: 
    - Erőforráscsoport neve – a szinapszis munkaterület áttekintésében találhatja meg Azure Portal.
    - Fióknév – a tűzfalszabályok által védett Storage-fiók neve.
    - Bérlő azonosítója – a bérlői adatok Azure Active Directory Azure Portalban találhatja meg.
    - Munkaterület neve – a szinapszis munkaterület neve.

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > Győződjön meg arról, hogy az erőforrás-azonosító megfelel ennek a sablonnak a resourceId változó nyomtatásban.
    >
    > Fontos, hogy a **resourcegroups** -et kisbetűvel írja.
    > Példa egy erőforrás-azonosítóra: 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. Tárolási hálózati szabály hozzáadása: 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. Ellenőrizze, hogy a szabály alkalmazva lett-e a Storage-fiókban: 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

#### <a name="managed-identity"></a>Felügyelt identitás
Engedélyeznie kell a [megbízható Microsoft-szolgáltatásokat... ](../../storage/common/storage-network-security.md#trusted-microsoft-services)az adott erőforrás [-példány rendszerhez rendelt felügyelt identitásának](../../active-directory/managed-identities-azure-resources/overview.md) beállítása és explicit módon történő [hozzárendelése](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) . Ebben az esetben a példányhoz való hozzáférés hatóköre megfelel a felügyelt identitáshoz rendelt Azure-szerepkörnek.

## <a name="credentials"></a>Hitelesítő adatok

Az Azure Storage-ban található fájl lekérdezéséhez a kiszolgáló nélküli SQL-készlet végpontjának a hitelesítési adatokat tartalmazó hitelesítő adatokra van szüksége. A hitelesítő adatok két típusát használják:
- A kiszolgálói szintű HITELESÍTő adatok a függvény használatával végrehajtott ad hoc lekérdezésekhez használatosak `OPENROWSET` . A hitelesítő adatok nevének egyeznie kell a tároló URL-címével.
- Az adatbázis-HATÓKÖRrel rendelkező hitelesítő adatok külső táblákhoz használatosak. A külső tábla `DATA SOURCE` arra a hitelesítő adatokra hivatkozik, amelyet a tároló elérésére kell használni.

Ha engedélyezni szeretné a felhasználó számára a hitelesítő adatok létrehozását vagy eldobását, a rendszergazda ENGEDÉLYEZheti vagy megtagadhatja a hitelesítő adatok MÓDOSÍTÁSát a felhasználó számára:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

A külső tárolóhoz hozzáférő adatbázis-felhasználóknak engedéllyel kell rendelkezniük a hitelesítő adatok használatához.

### <a name="grant-permissions-to-use-credential"></a>Engedélyek megadása a hitelesítő adatok használatához

A hitelesítő adatok használatához a felhasználónak engedéllyel kell rendelkeznie `REFERENCES` egy adott hitelesítő adathoz. Ha engedélyt szeretne adni egy `REFERENCES` specific_user storage_credentialére, hajtsa végre a következőt:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

A zökkenőmentes Azure AD átmenő élmény biztosítása érdekében a felhasználók alapértelmezés szerint jogosultak a `UserIdentity` hitelesítő adatok használatára.

## <a name="server-scoped-credential"></a>Kiszolgáló – hatókörön belüli hitelesítő adatok

A kiszolgáló hatókörű hitelesítő adatait akkor használja a rendszer, ha az SQL-bejelentkezési hívások `OPENROWSET` funkciója nem `DATA_SOURCE` a fájlok egyes Storage-fiókokban való olvasásával történik. A kiszolgáló-hatókörű hitelesítő adatok nevének **meg kell** egyeznie az Azure Storage URL-címével. A hitelesítő adatok a [create hitelesítő adatok létrehozása](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)paranccsal adhatók hozzá. Meg kell adnia a HITELESÍTŐADAT-név argumentumot. A fájlnak meg kell egyeznie az elérési út vagy a tárolóban lévő összes elérési út részével (lásd alább).

> [!NOTE]
> Az `FOR CRYPTOGRAPHIC PROVIDER` argumentum nem támogatott.

A kiszolgáló szintű HITELESÍTő adatok nevének meg kell egyeznie a Storage-fiók (és opcionálisan tároló) teljes elérési útjával a következő formátumban: `<prefix>://<storage_account_path>/<storage_path>` . A Storage-fiók elérési útját a következő táblázat ismerteti:

| Külső adatforrás       | Előtag | Storage-fiók elérési útja                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| 1. generációs Azure Data Lake Storage | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| 2. generációs Azure Data Lake Storage | https  | <storage_account>. dfs.core.windows.net              |

A kiszolgáló-hatókörű hitelesítő adatok lehetővé teszik az Azure Storage elérését a következő hitelesítési típusok használatával:

### <a name="user-identity"></a>[Felhasználói identitás](#tab/user-identity)

Az Azure AD-felhasználók hozzáférhetnek az Azure Storage-ban található összes fájlhoz `Storage Blob Data Owner` , `Storage Blob Data Contributor` vagy `Storage Blob Data Reader` szerepkörrel. Az Azure AD-felhasználók nem igényelnek hitelesítő adatokat a tárolóhoz való hozzáféréshez. 

Az SQL-felhasználók nem használhatják az Azure AD-hitelesítést a tárolóhoz való hozzáféréshez.

### <a name="shared-access-signature"></a>[Közös hozzáférésű jogosultságkód](#tab/shared-access-signature)

A következő parancsfájl egy kiszolgálói szintű hitelesítő adatot hoz létre, amelyet a függvények használhatnak az `OPENROWSET` Azure Storage-beli fájlok sas-token használatával való eléréséhez. Ennek a hitelesítő adatnak a létrehozásával engedélyezheti az olyan SQL-rendszerbiztonsági tag számára, amely végrehajtja `OPENROWSET` a függvényt az SAS-kulccsal védett fájlok olvasásához az Azure Storage-ban a hitelesítő adatok

Az Exchange <*mystorageaccountname*> a tényleges Storage-fiók nevével, és <*mystorageaccountcontainername*> a tároló tényleges nevével:

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Felügyelt identitás](#tab/managed-identity)

A következő szkript létrehoz egy kiszolgálói szintű hitelesítő adatot, amelyet a függvények használhatnak az `OPENROWSET` Azure Storage-ban található összes fájlhoz a munkaterület által felügyelt identitás használatával.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Nyilvános hozzáférés](#tab/public-access)

Az adatbázis-hatókörrel rendelkező hitelesítő adatok nem szükségesek a nyilvánosan elérhető fájlokhoz való hozzáférés engedélyezéséhez. [Adatbázis-hatókörű hitelesítő adatok nélküli adatforrás](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) létrehozása az Azure Storage-ban nyilvánosan elérhető fájlok eléréséhez.

---

## <a name="database-scoped-credential"></a>Adatbázis – hatókörrel rendelkező hitelesítő adatok

Adatbázis-hatókörű hitelesítő adatokat akkor kell használni, ha bármelyik résztvevő meghívja `OPENROWSET` a `DATA_SOURCE` nyilvános fájlokhoz nem hozzáférő [külső táblából](develop-tables-external-tables.md) származó adatokat, vagy kiválasztja azokat. Az adatbázishoz tartozó hatókörrel rendelkező hitelesítő adatoknak nem kell megegyezniük a Storage-fiók nevével. A rendszer explicit módon fogja használni az adatforrásban, amely meghatározza a tárterület helyét.

Az adatbázis-hatókörrel rendelkező hitelesítő adatok lehetővé teszik az Azure Storage elérését a következő hitelesítési típusok használatával:

### <a name="azure-ad-identity"></a>[Azure AD-identitás](#tab/user-identity)

Az Azure AD-felhasználók hozzáférhetnek az Azure Storage-ban található összes fájlhoz, ha legalább `Storage Blob Data Owner` , `Storage Blob Data Contributor` vagy `Storage Blob Data Reader` szerepkörrel rendelkeznek. Az Azure AD-felhasználók nem igényelnek hitelesítő adatokat a tárolóhoz való hozzáféréshez.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Az SQL-felhasználók nem használhatják az Azure AD-hitelesítést a tárolóhoz való hozzáféréshez.

### <a name="shared-access-signature"></a>[Közös hozzáférésű jogosultságkód](#tab/shared-access-signature)

A következő szkript létrehoz egy hitelesítő adatot, amely a hitelesítő adatokban megadott SAS-jogkivonat használatával fér hozzá a fájlokhoz a tárolóban. A szkript létrehoz egy külső adatforrást, amely ezt az SAS-tokent használja a tároló eléréséhez.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Felügyelt identitás](#tab/managed-identity)

Az alábbi szkript egy adatbázis-hatókörű hitelesítő adatot hoz létre, amellyel megszemélyesítheti a jelenlegi Azure AD-felhasználót a szolgáltatás felügyelt identitásával. A szkript létrehoz egy külső adatforrást, amely munkaterület-identitást használ a tároló eléréséhez.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Az adatbázishoz tartozó hatókörrel rendelkező hitelesítő adatoknak nem kell megegyezniük a Storage-fiók nevével, mert explicit módon lesz használva a Storage helyét meghatározó adatforrásban.

### <a name="public-access"></a>[Nyilvános hozzáférés](#tab/public-access)

Az adatbázis-hatókörrel rendelkező hitelesítő adatok nem szükségesek a nyilvánosan elérhető fájlokhoz való hozzáférés engedélyezéséhez. [Adatbázis-hatókörű hitelesítő adatok nélküli adatforrás](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) létrehozása az Azure Storage-ban nyilvánosan elérhető fájlok eléréséhez.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Az adatbázishoz kötődő hitelesítő adatok külső adatforrásokban vannak használatban annak megadásához, hogy milyen hitelesítési módszert fog használni a tároló eléréséhez:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Példák

### <a name="access-a-publicly-available-data-source"></a>**Nyilvánosan elérhető adatforrás elérése**

A következő szkripttel hozhat létre olyan táblázatot, amely hozzáfér a nyilvánosan elérhető adatforráshoz.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

Az adatbázis-felhasználó az adatforrásból származó fájlok tartalmát elolvashatja az adatforrásra hivatkozó külső tábla vagy [OpenRowset](develop-openrowset.md) függvény használatával:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Adatforrás elérése hitelesítő adatok használatával**

Módosítsa a következő parancsfájlt egy olyan külső tábla létrehozásához, amely az Azure Storage-t az SAS-token, a felhasználó Azure AD-identitása vagy a munkaterület felügyelt identitása használatával éri el.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

Az adatbázis-felhasználó az adatforrásból származó fájlok tartalmát elolvashatja az adatforrásra hivatkozó [külső tábla](develop-tables-external-tables.md) vagy [OpenRowset](develop-openrowset.md)  függvény használatával:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Következő lépések

Az alább felsorolt cikkek segítenek megismerni a különböző típusú mappák, fájltípusok és a nézetek létrehozásának és használatának a lekérdezését:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)
- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Adott fájlok lekérdezése](query-specific-files.md)
- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
- [JSON-fájlok lekérdezése](query-json-files.md)
- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)
