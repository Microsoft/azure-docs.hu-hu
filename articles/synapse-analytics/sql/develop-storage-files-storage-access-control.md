---
title: Tárfiók-hozzáférés szabályozása kiszolgáló nélküli SQL-készlethez
description: Leírja, hogyan fér hozzá a kiszolgáló nélküli SQL-készlet az Azure Storage-hoz, és hogyan szabályozhatja a kiszolgáló nélküli SQL-készlet tárolóelérését a Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 266a6c27261107b883fdc0c1cdd274e6345de6db
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483452"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>A kiszolgáló nélküli SQL-készlet tárfiók-hozzáférésének szabályozása a Azure Synapse Analytics

A kiszolgáló nélküli SQL-készlet lekérdezése közvetlenül az Azure Storage-ból olvas be fájlokat. Az Azure Storage-ban található fájlok elérésére vonatkozó engedélyek vezérlése két szinten történik:
- Tárolási szint – A **felhasználónak** engedéllyel kell rendelkeznie a mögöttes tárolófájlokhoz való hozzáféréshez. A tár rendszergazdájának engedélyeznie kell az Azure AD-rendszerbiztonsági tag számára a fájlok olvasását/írását, vagy létre kell hoznia a tároló eléréséhez használt SAS-kulcsot.
- **SQL szolgáltatási szint** – A felhasználónak [](develop-tables-external-tables.md) engedélyt kellett adni az adatok külső táblával történő beolvassára vagy a függvény `OPENROWSET` végrehajtására. A szükséges [engedélyekről bővebben ebben](develop-storage-files-overview.md#permissions)a szakaszban olvashat.

Ez a cikk a használható hitelesítő adatok típusait, valamint a hitelesítő adatok keresésének sql- és Azure AD-felhasználók számára történő életbe léptetését ismerteti.

## <a name="storage-permissions"></a>Tárolási engedélyek

A munkaterületen található kiszolgáló nélküli SQL Synapse Analytics olvashatja az Azure Data Lake Storage-ban tárolt fájlok tartalmát. Konfigurálnia kell a tárolóra vonatkozó engedélyeket, hogy az SQL-lekérdezést futtató felhasználók beolvassák a fájlokat. Három módszerrel lehet engedélyezni a hozzáférést a>
- **[A szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)** lehetővé teszi, hogy szerepkört rendeljen a tárterületet tároló bérlő néhány Azure AD-felhasználóhoz. Az RBAC-szerepkörök hozzárendelhetőek Azure AD-felhasználókhoz. Az olvasónak `Storage Blob Data Reader` , `Storage Blob Data Contributor` vagy szerepkörben kell `Storage Blob Data Owner` lennie. Az Azure Storage-ban adatokat író felhasználónak vagy szerepkörben `Storage Blob Data Writer` kell `Storage Blob Data Owner` lennie. Vegye `Storage Owner` figyelembe, hogy a szerepkör nem jelenti azt, hogy a felhasználó is `Storage Data Owner` .
- **Access Control listákkal (ACL-ekkel)** finomhangolt engedélymodellt határozhat meg az Azure Storage-ban található fájlokhoz és könyvtárakhoz. Az ACL hozzárendelhető Azure AD-felhasználókhoz. Ha az olvasók egy Azure Storage-beli elérési úton lévő fájlt szeretne olvasni, akkor a fájl elérési útjának minden mappájában végre kell hajtania az (X) ACL-t, a fájlon pedig a read(R) ACL-t. [További információ az ACL-engedélyek tárolási rétegben való beállításról](../../storage/blobs/data-lake-storage-access-control.md#how-to-set-acls)
- **A közös hozzáférésű jogosultság jogosultsága (SAS)** lehetővé teszi, hogy az olvasó az időkor megadott jogkivonat használatával hozzáférjen az Azure Data Lake Storage-ban lévő fájlokhoz. Az olvasót nem is kell Azure AD-felhasználóként hitelesíteni. Az SAS-jogkivonat tartalmazza az olvasónak megadott engedélyeket, valamint azt az időszakot, amikor a jogkivonat érvényes. Az SAS-jogkivonat jó választás olyan felhasználók számára, akik számára nem is kell ugyanabban az Azure AD-bérlőben lennie. AZ SAS-jogkivonat definiálható a tárfiókban vagy adott könyvtárakban. További információ az Azure Storage-erőforrások korlátozott hozzáférésének közös [hozzáférésű jogosultságok használatával való megadásáról.](../../storage/common/storage-sas-overview.md)

## <a name="supported-storage-authorization-types"></a>Támogatott tárterület-engedélyezési típusok

A kiszolgáló nélküli SQL-készletbe bejelentkezett felhasználónak jogosultnak kell lennie az Azure Storage-ban lévő fájlok elérésére és lekérdezésére, ha a fájlok nem érhetők el nyilvánosan. A nem nyilvános tárolók eléréséhez három hitelesítési típus [használható:](?tabs=user-identity)felhasználói identitás, közös hozzáférésű [jogosultság aláírása](?tabs=shared-access-signature)és [felügyelt identitás.](?tabs=managed-identity)

> [!NOTE]
> Munkaterület létrehozásakor az alapértelmezett viselkedés az **Azure AD-beli** átmenő szolgáltatás.

### <a name="user-identity"></a>[Felhasználói identitás](#tab/user-identity)

**Az**"Azure AD átmenő" néven is ismert felhasználói identitás egy olyan hitelesítési típus, amelyben a kiszolgáló nélküli SQL-készletbe bejelentkezett Azure AD-felhasználó identitását használják az adatelérés hitelesítéséhez. Az adatokhoz való hozzáférés előtt az Azure Storage rendszergazdájának engedélyeket kell adnunk az Azure AD-felhasználónak. Ahogy az alábbi táblázatban is látható, az SQL-felhasználó típusa nem támogatott.

> [!IMPORTANT]
> Előfordulhat, hogy az ügyfélalkalmazások gyorsítótárazták az AAD-hitelesítési jogkivonatot. A PowerBI például gyorsítótárazza az AAD-jogkivonatot, és ugyanazt a jogkivonatot használja egy órán át. A hosszú ideig futó lekérdezések sikertelenek lehetnek, ha a jogkivonat a lekérdezés végrehajtása közben lejár. Ha olyan lekérdezési hibákat tapasztal, amelyeket a lekérdezés közepén elévülő AAD-hozzáférési jogkivonat okoz, fontolja meg a felügyelt identitásra vagy a közös hozzáférésű jogosultsági jogosultságra való [váltást.](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#supported-storage-authorization-types) [](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types)

Az adatok eléréséhez a Storage-blobadatok tulajdonosi/közreműködői/olvasói szerepkörével kell rendelkezik. Másik lehetőségként megadhat a fájlokhoz és mappákhoz való hozzáférésre vonatkozó, finomhangolt ACL-szabályokat. Még ha Ön is egy Storage-fiók tulajdonosa, akkor is fel kell vennie magát a Storage-blobadatok egyik szerepkörbe.
Ha többet szeretne megtudni az Azure Data Lake Store Gen2 hozzáférés-vezérlésről, olvassa el a Hozzáférés-vezérlés [a](../../storage/blobs/data-lake-storage-access-control.md) Azure Data Lake Storage Gen2 cikkben.


### <a name="shared-access-signature"></a>[Közös hozzáférésű jogosultságkód](#tab/shared-access-signature)

**A közös hozzáférésű jogosultság jogosultsága (SAS)** delegált hozzáférést biztosít a tárfiók erőforrásaihoz. Az SAS használatával az ügyfelek fiókkulcsok megosztása nélkül adhatnak hozzáférést az ügyfeleknek a tárfiókban található erőforrásokhoz. Az SAS részletesen szabályozhatja az SAS-sel rendelkező ügyfelek számára biztosított hozzáférés típusát, beleértve az érvényességi időközt, a megadott engedélyeket, az elfogadható IP-címtartományt és az elfogadható protokollt (https/http).

SAS-jogkivonatot úgy kaphat, ha megnyitja az **Azure Portal -> Storage-fiók -> Közös** hozzáférésű jogosultság jogosultsága -> engedélyek konfigurálása -> SAS és kapcsolati sztring létrehozása elérést.

> [!IMPORTANT]
> Sas-jogkivonat generálásakor a jogkivonat elején egy kérdőjel (?) szerepel. Ha a jogkivonatot kiszolgáló nélküli SQL-készletben szeretné használni, el kell távolítania a kérdőjelet (?) a hitelesítő adatok létrehozásakor. Például:
>
> SAS-jogkivonat: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&<6> st=2019-04-18T12:42:12Z&spr=https&sig=lQHemetNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3BROEIq78%3D

Ha SAS-jogkivonattal szeretné engedélyezni a hozzáférést, létre kell hoznia egy adatbázisra vagy kiszolgálóra vonatkozó hitelesítő adatokat 


> [!IMPORTANT]
> Az SAS-jogkivonattal nem férhet hozzá a privát tárfiókhoz. A védett tároló [eléréséhez](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) fontolja meg a Felügyelt identitás vagy az Azure [AD átmenő](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) hitelesítésének használatát.

### <a name="managed-identity"></a>[Felügyelt identitás](#tab/managed-identity)

**A felügyelt identitás** más néven MSI. Az Azure AD (Azure Active Directory szolgáltatása, amely Azure-szolgáltatásokat biztosít a kiszolgáló nélküli SQL-készlethez. Emellett egy automatikusan felügyelt identitást is üzembe helyez az Azure AD-ban. Ezzel az identitással engedélyezheti az Adatelérési kérést az Azure Storage-ban.

Az adatokhoz való hozzáférés előtt az Azure Storage rendszergazdájának engedélyeket kell adjon a felügyelt identitásnak az adatok eléréséhez. Az engedélyek felügyelt identitás számára való megadása ugyanúgy történik, mint bármely más Azure AD-felhasználó számára.

### <a name="anonymous-access"></a>[Névtelen hozzáférés](#tab/public-access)

Az Azure Storage-fiókokon elhelyezett nyilvánosan elérhető fájlok névtelen hozzáférést [érhetők el.](../../storage/blobs/anonymous-read-access-configure.md)

---

### <a name="supported-authorization-types-for-databases-users"></a>Adatbázis-felhasználók által támogatott engedélyezési típusok

Az alábbi táblázatban megtalálja az elérhető engedélyezési típusokat:

| Engedélyezés típusa                    | *SQL-felhasználó*    | *Azure AD-felhasználó*     |
| ------------------------------------- | ------------- | -----------    |
| [Felhasználói identitás](?tabs=user-identity#supported-storage-authorization-types)       | Nem támogatott | Támogatott      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Támogatott     | Támogatott      |
| [Felügyelt identitás](?tabs=managed-identity#supported-storage-authorization-types) | Támogatott | Támogatott      |

### <a name="supported-storages-and-authorization-types"></a>Támogatott tárolók és engedélyezési típusok

Az engedélyezés és az Azure Storage-típusok következő kombinációit használhatja:

| Engedélyezés típusa  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Támogatott\*      | Nem támogatott   | Támogatott\*     |
| [Felügyelt identitás](?tabs=managed-identity#supported-storage-authorization-types) | Támogatott      | Támogatott        | Támogatott     |
| [Felhasználói identitás](?tabs=user-identity#supported-storage-authorization-types)    | Támogatott\*      | Támogatott\*        | Támogatott\*     |

\* Az SAS-jogkivonat és az Azure AD Identity használható a tűzfallal nem védett tárolók eléréséhez.


### <a name="querying-firewall-protected-storage"></a>Tűzfal által védett tárterület lekérdezése

A tűzfallal védett tárolók eléréséhez használhatja  a felhasználói identitást vagy a **felügyelt identitást.**

> [!NOTE]
> A Storage tűzfal funkciója nyilvános előzetes verzióban érhető el, és minden nyilvános felhőrégióban elérhető. 

#### <a name="user-identity"></a>Felhasználói identitás

A tűzfallal védett tároló felhasználói identitással való eléréséhez használhatja a felhasználói Azure Portal az Az.Storage PowerShell-modult.
#### <a name="configuration-via-azure-portal"></a>Konfigurálás Azure Portal

1. Keresse meg a Tárfiókot a Azure Portal.
1. A Beállítások szakaszban kattintson a Hálózat elemre.
1. Az "Erőforráspéldányok" szakaszban adjon hozzá egy kivételt a Synapse-munkaterülethez.
1. Erőforrástípusként válassza a Microsoft.Synapse/workspaces lehetőséget.
1. Példánynévként válassza ki a munkaterület nevét.
1. Kattintson a Mentés gombra.

#### <a name="configuration-via-powershell"></a>Konfigurálás a PowerShell-en keresztül

Az alábbi lépésekkel konfigurálhatja a tárfiók tűzfalát, és kivételt adhat hozzá a Synapse-munkaterülethez.

1. Nyissa meg a PowerShellt, [vagy telepítse a PowerShellt](/powershell/scripting/install/installing-powershell-core-on-windows)
2. Az Az.Storage 3.4.0 modul és az Az.Synapse 0.7.0 telepítése: 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > Győződjön meg arról, hogy a **3.4.0-s verziót használja.** Az Az.Storage verzióját a következő parancs futtatásával ellenőrizheti:  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Csatlakozás az Azure-bérlőhöz: 
    ```powershell
    Connect-AzAccount
    ```
4. Változók meghatározása a PowerShellben: 
    - Erőforráscsoport neve – ezt a Storage Azure Portal áttekintésében találja.
    - Fiók neve – a tűzfalszabályok által védett tárfiók neve.
    - Bérlőazonosító – ezt a bérlői információk Azure Portal a Azure Active Directory a következőben találja: .
    - Munkaterület neve – A Synapse-munkaterület neve.

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
    > Győződjön meg arról, hogy az erőforrás-azonosító megegyezik ezzel a sablonnal a resourceId változó nyomtatásában.
    >
    > Fontos, hogy kisbetűs **erőforráscsoportokat** írjon.
    > Példa egy erőforrás-azonosítóra: 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. Tárolóhálózati szabály hozzáadása: 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. Ellenőrizze, hogy a szabály alkalmazva lett-e a tárfiókban: 
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
Engedélyeznie kell [a megbízható Microsoft-szolgáltatások... az](../../storage/common/storage-network-security.md#trusted-microsoft-services) [azure-beli szerepkör](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) beállítása és explicit hozzárendelése az adott [erőforráspéldány](../../active-directory/managed-identities-azure-resources/overview.md) rendszer által hozzárendelt felügyelt identitásához. Ebben az esetben a példány hozzáférési hatóköre megegyezik a felügyelt identitáshoz rendelt Azure-szerepkörrel.

## <a name="credentials"></a>Hitelesítő adatok

Az Azure Storage-ban található fájlok lekérdezéséhez a kiszolgáló nélküli SQL-készlet végpontjának rendelkeznie kell egy hitelesítő adatokkal, amely tartalmazza a hitelesítési adatokat. Kétféle hitelesítő adat használható:
- A kiszolgálószintű CREDENTIAL a függvény használatával végrehajtott alkalmi lekérdezésekhez `OPENROWSET` használatos. A hitelesítő adatok nevének egyeznie kell a tároló URL-címével.
- A DATABASE SCOPED CREDENTIAL külső táblákhoz használatos. A külső tábla a tároló eléréséhez használni szükséges hitelesítő `DATA SOURCE` adatokra hivatkozik.

Annak érdekében, hogy a felhasználó hitelesítő adatokat hoz létre vagy dobhat el, a rendszergazda a FELHASZNÁLÓNAK ADHAT/MEGTAGADHAT BÁRMELY HITELESÍTŐ ADAT ENGEDÉLYT:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

A külső tárterülethez hozzáférő adatbázis-felhasználóknak engedéllyel kell rendelkezniük a hitelesítő adatok használatára.

### <a name="grant-permissions-to-use-credential"></a>Engedélyek megadása a hitelesítő adatok használatára

A hitelesítő adatokhoz a felhasználónak engedéllyel kell `REFERENCES` rendelkeznie egy adott hitelesítő adathoz. Ha engedélyt ad egy storage_credential egy specific_user számára, hajtsa `REFERENCES` végre a következőt:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>Kiszolgálóra vonatkozó hitelesítő adatok

A kiszolgálóra vonatkozó hitelesítő adatokat akkor használja a rendszer, ha az SQL-bejelentkezési hívások anélkül működnek, hogy beolvasanának `OPENROWSET` `DATA_SOURCE` fájlokat egy tárfiókban. A kiszolgálóra vonatkozó hitelesítő adatok nevének **meg** kell egyeznie az Azure Storage alap URL-címével (opcionálisan egy tárolónévvel). A hitelesítő adatok a CREATE CREDENTIAL (HITELESÍTŐ [ADATOK LÉTREHOZÁSA) futtatásával kerülnek hozzáadásra.](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) Meg kell adnia egy CREDENTIAL NAME argumentumot.

> [!NOTE]
> A `FOR CRYPTOGRAPHIC PROVIDER` argumentum nem támogatott.

A kiszolgálószintű HITELESÍTŐ ADATOK nevének meg kell egyeznie a tárfiók teljes elérési útjának (és opcionálisan a tárolónak) a következő formátumban: `<prefix>://<storage_account_path>[/<container_name>]` . A tárfiók elérési útjait az alábbi táblázat ismerteti:

| Külső adatforrás       | Előtag | Tárfiók elérési útja                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| 1. generációs Azure Data Lake Storage | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| 2. generációs Azure Data Lake Storage | https  | <storage_account>.dfs.core.windows.net              |

A kiszolgálóra vonatkozó hitelesítő adatok az alábbi hitelesítési típusok használatával teszik lehetővé az Azure Storage-hoz való hozzáférést:

### <a name="user-identity"></a>[Felhasználói identitás](#tab/user-identity)

Az Azure AD-felhasználók bármilyen fájlhoz hozzáférhetnek az Azure Storage-ban, ha `Storage Blob Data Owner` , `Storage Blob Data Contributor` vagy `Storage Blob Data Reader` szerepkörük van. Az Azure AD-felhasználóknak nincs szükségük hitelesítő adatokra a tároló eléréséhez. 

Az SQL-felhasználók nem használhatnak Azure AD-hitelesítést a tároló eléréséhez.

### <a name="shared-access-signature"></a>[Közös hozzáférésű jogosultságkód](#tab/shared-access-signature)

A következő szkript egy kiszolgálószintű hitelesítő adatokat hoz létre, amelyet a függvény az Azure Storage bármely fájljának SAS-jogkivonat használatával `OPENROWSET` történő elérésére használhat. Hozza létre ezt a hitelesítő adatokat annak engedélyezéséhez, hogy a függvényt futtató SQL-rendszerbiztonsági tag beolvassa az Azure Storage SAS-kulcsával védett fájlokat, amelyek a hitelesítő adatok nevében `OPENROWSET` megadott URL-lel egyezést végeznek.

Exchange <*mystorageaccountname*> a tárfiók tényleges nevével, <*mystorageaccountcontainername*> a tároló tényleges nevével:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

Használhatja a tárfiók alap URL-címét is tárolónév nélkül.

### <a name="managed-identity"></a>[Felügyelt identitás](#tab/managed-identity)

A következő szkript egy kiszolgálószintű hitelesítő adatokat hoz létre, amelyet a függvény az Azure Storage bármely fájljának elérésére használhat a munkaterület `OPENROWSET` által felügyelt identitás használatával.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

Használhatja a tárfiók alap URL-címét is tárolónév nélkül.

### <a name="public-access"></a>[Nyilvános hozzáférés](#tab/public-access)

Az adatbázisra vonatkozó hitelesítő adatok nem szükségesek a nyilvánosan elérhető fájlokhoz való hozzáféréshez. Adatbázisra [vonatkozó hitelesítő](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) adatok nélkül hozhat létre adatforrást az Azure Storage-ban nyilvánosan elérhető fájlok eléréséhez.

---

## <a name="database-scoped-credential"></a>Adatbázisra vonatkozó hitelesítő adatok

Az adatbázisra vonatkozó hitelesítő adatokat akkor használja a rendszer, ha bármely rendszerbiztonsági tag olyan külső táblából származó adatokat használ vagy választ ki, amelyek nem férnek hozzá a `OPENROWSET` `DATA_SOURCE` nyilvános fájlokhoz. [](develop-tables-external-tables.md) Az adatbázishoz hatókörrel tartozó hitelesítő adatoknak nem kell megegyezni a tárfiók nevével. A data source (ADATFORRÁS) explicit módon használja, amely meghatározza a tároló helyét.

Az adatbázisra vonatkozó hitelesítő adatok az alábbi hitelesítési típusok használatával teszik lehetővé az Azure Storage-hoz való hozzáférést:

### <a name="azure-ad-identity"></a>[Azure AD-identitás](#tab/user-identity)

Az Azure AD-felhasználók bármilyen fájlhoz hozzáférhetnek az Azure Storage-ban, ha legalább `Storage Blob Data Owner` , `Storage Blob Data Contributor` vagy szerepkörük `Storage Blob Data Reader` van. Az Azure AD-felhasználóknak nincs szükségük hitelesítő adatokra a tároló eléréséhez.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Az SQL-felhasználók nem használhatnak Azure AD-hitelesítést a tároló eléréséhez.

### <a name="shared-access-signature"></a>[Közös hozzáférésű jogosultságkód](#tab/shared-access-signature)

A következő szkript létrehoz egy hitelesítő adatokat, amely a tárterületen lévő fájlok eléréséhez használható a hitelesítő adatokban megadott SAS-jogkivonattal. A szkript létrehoz egy külső adatforrásmintát, amely ezt az SAS-jogkivonatot használja a tároló eléréséhez.

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

A következő szkript létrehoz egy adatbázisra érvényes hitelesítő adatokat, amelyek az aktuális Azure AD-felhasználó szolgáltatás felügyelt identitásaként való megszemélyesítését szolgálják. A szkript létrehoz egy külső adatforrásmintát, amely a munkaterület identitásával fér hozzá a tárolóhoz.

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

Az adatbázishoz hatókörrel tartozó hitelesítő adatoknak nem kell egyezniük a tárfiók nevével, mert explicit módon lesz használva az ADATFORRÁSban, amely meghatározza a tároló helyét.

### <a name="public-access"></a>[Nyilvános hozzáférés](#tab/public-access)

Az adatbázisra vonatkozó hitelesítő adatok nem szükségesek a nyilvánosan elérhető fájlokhoz való hozzáféréshez. Adatbázisra [vonatkozó hitelesítő](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) adatok nélkül hozhat létre adatforrást az Azure Storage-ban nyilvánosan elérhető fájlok eléréséhez.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Az adatbázisra vonatkozó hitelesítő adatokat a külső adatforrások használják annak megadásához, hogy milyen hitelesítési módszerrel fér hozzá a tárolóhoz:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Példák

### <a name="access-a-publicly-available-data-source"></a>**Hozzáférés nyilvánosan elérhető adatforráshoz**

A következő szkript használatával hozzon létre egy táblát, amely hozzáfér a nyilvánosan elérhető adatforráshoz.

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

Az adatbázis-felhasználó az adatforrásra hivatkozó külső tábla vagy [OPENROWSET](develop-openrowset.md) függvény használatával olvashatja be a fájlok tartalmát az adatforrásból:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Adatforrás elérése hitelesítő adatokkal**

A következő szkript módosításával hozzon létre egy külső táblát, amely SAS-jogkivonat, a felhasználó Azure AD-identitása vagy a munkaterület felügyelt identitása használatával fér hozzá az Azure Storage-hoz.

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

Az adatbázis-felhasználó az adatforrásra hivatkozó [](develop-tables-external-tables.md) külső tábla vagy [OPENROWSET](develop-openrowset.md) függvény használatával olvashatja be a fájlok tartalmát az adatforrásból:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekből megtudhatja, hogyan lehet különböző mappatípusokat és fájltípusokat lekérdezni, valamint nézeteket létrehozni és használni:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)
- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Adott fájlok lekérdezése](query-specific-files.md)
- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
- [JSON-fájlok lekérdezése](query-json-files.md)
- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)
