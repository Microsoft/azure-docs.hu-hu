---
title: Virtuális hálózati végpontok és a Azure SQL Database adatbázisainak szabályai
description: Alhálózat megjelölése virtuális hálózati szolgáltatásbeli végpontként. Ezután adja hozzá a végpontot virtuális hálózati szabályként az adatbázis ACL-jéhez. Az adatbázis ezután fogadja a kommunikációt az alhálózaton lévő összes virtuális gépről és más csomópontról.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 0dcffe6731c177d1d45c569361fcb200f23af86c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095358"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Virtuális hálózati szolgáltatásvégpontok és szabályok használata kiszolgálókhoz az Azure SQL Database-ben

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A *virtuális hálózati szabályok* egy tűzfal biztonsági funkciója, amely azt szabályozza, hogy az adatbázisok és a rugalmas készletek kiszolgálója [Azure SQL Database](sql-database-paas-overview.md) vagy a dedikált SQL-készlet (korábban SQL DW) adatbázisai az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) szolgáltatásban a virtuális hálózatok adott alhálózatáról érkező kommunikációt fogadják-e. Ez a cikk azt ismerteti, hogy a virtuális hálózati szabályok miért hasznosak a SQL Database és az Azure szinapszis Analytics szolgáltatásban való kommunikáció biztonságos engedélyezéséhez.

> [!NOTE]
> Ez a cikk a SQL Database és az Azure szinapszis Analytics szolgáltatásra is vonatkozik. Az egyszerűség kedvéért az *adatbázis* kifejezés a SQL Database és az Azure szinapszis Analytics mindkét adatbázisára vonatkozik. Hasonlóképpen, a *kiszolgálóra* mutató hivatkozások a SQL Database és az Azure szinapszis Analytics szolgáltatást futtató [logikai SQL Serverre](logical-servers.md) hivatkoznak.

Virtuális hálózati szabály létrehozásához először egy [virtuális hálózati szolgáltatás végpontjának][vm-virtual-network-service-endpoints-overview-649d] kell lennie ahhoz, hogy a szabály hivatkozzon.

## <a name="create-a-virtual-network-rule"></a>Virtuális hálózati szabály létrehozása

Ha csak virtuális hálózati szabályt szeretne létrehozni, ugorjon a [cikk későbbi részében](#anchor-how-to-by-using-firewall-portal-59j)ismertetett lépésekre és magyarázatokra.

## <a name="details-about-virtual-network-rules"></a>A virtuális hálózati szabályok részletei

Ez a szakasz a virtuális hálózati szabályokkal kapcsolatos néhány részletet ismerteti.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi régió

Minden egyes virtuális hálózati szolgáltatás végpontja csak egy Azure-régióra vonatkozik. A végpont nem engedélyezi más régiók számára, hogy fogadják a kommunikációt az alhálózatból.

Bármely virtuális hálózati szabály arra a régióra korlátozódik, amelyre a mögöttes végpont vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgáló szintje, nem az adatbázis szintje

Minden virtuális hálózati szabály a teljes kiszolgálóra vonatkozik, nem csupán egy adott adatbázisra a kiszolgálón. Más szóval a virtuális hálózati szabályok a kiszolgáló szintjén érvényesek, nem az adatbázis szintjén.

Ezzel szemben az IP-szabályok bármelyik szinten alkalmazhatók.

### <a name="security-administration-roles"></a>Biztonsági felügyeleti szerepkörök

A biztonsági szerepkörök elkülönítése a virtuális hálózati szolgáltatás végpontjának felügyelete alatt áll. A következő szerepkörök mindegyike esetében beavatkozásra van szükség:

- **Hálózati rendszergazda ([hálózati közreműködő](../../role-based-access-control/built-in-roles.md#network-contributor) szerepkör):** &nbsp; Kapcsolja be a végpontot.
- **Adatbázis-rendszergazda ([SQL Server közreműködő](../../role-based-access-control/built-in-roles.md#sql-server-contributor) szerepkör):** &nbsp; Frissítse a hozzáférés-vezérlési listát (ACL), hogy hozzáadja a megadott alhálózatot a kiszolgálóhoz.

#### <a name="azure-rbac-alternative"></a>Az Azure RBAC alternatíva

A hálózati rendszergazda és az adatbázis-rendszergazda szerepkörének több funkciója van, mint amennyi a virtuális hálózati szabályok kezeléséhez szükséges. A képességeinek csak egy részhalmazára van szükség.

Használhat [szerepköralapú hozzáférés-vezérlést (RBAC)][rbac-what-is-813s] az Azure-ban, hogy egyetlen egyéni szerepkört hozzon létre, amely csak a képességek megfelelő részhalmazát használja. Az egyéni szerepkör felhasználható a hálózati rendszergazda vagy az adatbázis-rendszergazda bevonása helyett. Ha hozzáad egy felhasználót egy egyéni szerepkörhöz, és a másik két fő rendszergazdai szerepkörhöz adja hozzá a felhasználót, a biztonsági expozíció területe alacsonyabb.

> [!NOTE]
> Bizonyos esetekben a SQL Database és a virtuális hálózat alhálózatának adatbázisa különböző előfizetésekben található. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
>
> - Mindkét előfizetésnek ugyanahhoz a Azure Active Directory (Azure AD) bérlőhöz kell tartoznia.
> - A felhasználó rendelkezik a szükséges engedélyekkel a műveletek elindításához, például a szolgáltatási végpontok engedélyezéséhez és egy virtuális hálózati alhálózat hozzáadásához az adott kiszolgálóhoz.
> - Mindkét előfizetéshez regisztrálni kell a Microsoft. SQL-szolgáltatót.

## <a name="limitations"></a>Korlátozások

SQL Database esetében a virtuális hálózati szabályok funkció a következő korlátozásokkal rendelkezik:

- A SQL Databaseban található adatbázis tűzfalában minden egyes virtuális hálózati szabály egy alhálózatra hivatkozik. Az összes hivatkozott alhálózatnak ugyanabban a földrajzi régióban kell futnia, amely az adatbázist üzemelteti.
- Minden kiszolgáló legfeljebb 128 ACL-bejegyzést tartalmazhat bármely virtuális hálózathoz.
- A virtuális hálózati szabályok csak Azure Resource Manager virtuális hálózatokra vonatkoznak, és nem a [klasszikus üzemi modell][arm-deployment-model-568f] hálózatokra.
- A virtuális hálózati szolgáltatási végpontok bekapcsolása a SQL Database is lehetővé teszi Azure Database for MySQL és Azure Database for PostgreSQL végpontját. Ha **a** végpontok be értékre vannak állítva, a végpontokról a Azure Database for MySQL próbál csatlakozni, vagy Azure Database for PostgreSQL példányok sikertelenek lehetnek.
  - A mögöttes ok az, hogy Azure Database for MySQL és Azure Database for PostgreSQL valószínűleg nincs konfigurálva virtuális hálózati szabály. Konfigurálnia kell egy virtuális hálózati szabályt a Azure Database for MySQL és a Azure Database for PostgreSQLhoz, és a kapcsolatok sikeresek lesznek.
  - A virtuális hálózati tűzfalszabályok olyan SQL logikai kiszolgálón való definiálásához, amely már konfigurálva van privát végpontokkal, állítsa a **nem** értékre a **nyilvános hálózati hozzáférés megtagadása** beállítást.
- A tűzfalon az IP-címtartományok a következő hálózatkezelési elemekre vonatkoznak, a virtuális hálózati szabályok azonban nem:
  - [Helyek közötti (S2S) virtuális magánhálózat (VPN)][vpn-gateway-indexmd-608y]
  - Helyszíni [Azure ExpressRoute](../../expressroute/index.yml) -n keresztül

### <a name="considerations-when-you-use-service-endpoints"></a>A szolgáltatási végpontok használatakor megfontolandó szempontok

Ha SQL Database szolgáltatás-végpontokat használ, tekintse át a következő szempontokat:

- **A Azure SQL Database nyilvános IP-címekre való kimenő forgalom megadása kötelező.** A kapcsolat engedélyezéséhez meg kell nyitni a hálózati biztonsági csoportokat (NSG) SQL Database IP-címekre. Ezt a SQL Database NSG- [szolgáltatásának címkéi](../../virtual-network/network-security-groups-overview.md#service-tags) segítségével teheti meg.

### <a name="expressroute"></a>ExpressRoute

Ha a helyszíni [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -t használja, a nyilvános vagy a Microsoft-társak számára meg kell határoznia a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. A Microsoft-társak esetében a használt NAT IP-címeket vagy az ügyfél vagy a szolgáltató biztosítja. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. Ha többet szeretne megtudni a nyilvános és a Microsoft-ExpressRoute NAT-ról, tekintse meg [Az Azure-beli nyilvános hálózat NAT-követelményei](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)című témakört.

Ha engedélyezni szeretné az áramkörről a SQL Database felé irányuló kommunikációt, létre kell hoznia az IP-hálózati szabályokat a NAT nyilvános IP-címeihez.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Virtuális hálózati szolgáltatásbeli végpontok használatának következményei az Azure Storage-ban

Az Azure Storage ugyanazt a funkciót implementálta, amellyel korlátozhatja az Azure Storage-fiók kapcsolatait. Ha úgy dönt, hogy ezt a funkciót egy olyan Azure Storage-fiókkal használja, amelyet SQL Database használ, akkor problémákba léphet. A következő lista a SQL Database és az Azure szinapszis Analytics azon funkcióit sorolja fel és tárgyalja, amelyekre ez hatással van.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Az Azure szinapszis Analytics alapszintű és MÁSOLÁSi utasítása

A "Base" és a "COPY" utasítást általában arra használják, hogy az Azure-beli Storage-fiókokból származó adatok betöltését a nagy átviteli sebességű adatfeldolgozási szolgáltatásba. Ha az Azure Storage-fiók, amelyről adatok vannak betöltve, csak a virtuális hálózati alhálózatok egy készletéhez fér hozzá, akkor a rendszer a Base elemet használja, és a MÁSOLÁSi utasítást a Storage-fiókba bontja. Az importálási és exportálási forgatókönyvek az Azure-beli virtuális hálózathoz védett Azure-tárolóhoz való MÁSOLÁSsal és a létrehozással való használattal való engedélyezésével, az ebben a szakaszban ismertetett lépések végrehajtásával engedélyezhető.

#### <a name="prerequisites"></a>Előfeltételek

- Azure PowerShell telepítése az [útmutató](/powershell/azure/install-az-ps)segítségével.
- Ha rendelkezik egy általános célú v1-vagy Azure Blob Storage-fiókkal, először az általános célú v2-re kell frissítenie az általános célú [v2-es Storage-fiókra való frissítés](../../storage/common/storage-account-upgrade.md)lépéseit követve.
- Engedélyeznie kell, **hogy a megbízható Microsoft-szolgáltatások hozzáférjenek ehhez a Storage-fiókhoz** az Azure Storage **-fiók tűzfala és a virtuális hálózatok** beállítások menüjében. Ennek a konfigurációnak a engedélyezése lehetővé teszi a Base és a COPY utasítás számára, hogy az erős hitelesítéssel kapcsolódjon a Storage-fiókhoz, ahol a hálózati forgalom az Azure-gerincen marad. További információkért tekintse meg [ezt az útmutatót](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Az SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor. Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Lépések

1. Ha önálló dedikált SQL-készlettel rendelkezik, regisztrálja az SQL Servert az Azure AD-ben a PowerShell használatával:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Ez a lépés nem szükséges a dedikált SQL-készletekhez az Azure szinapszis Analytics-munkaterületen belül.

1. Ha rendelkezik Azure szinapszis Analytics-munkaterülettel, regisztrálja a munkaterület rendszer által felügyelt identitását:

   1. Nyissa meg az Azure szinapszis Analytics-munkaterületet a Azure Portal.
   2. Nyissa meg a **felügyelt identitások** ablaktáblát.
   3. Győződjön meg arról, hogy engedélyezve van a **folyamatok engedélyezése** beállítás.
   
1. Hozzon létre egy **általános célú v2-es Storage-fiókot** a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md)című témakör lépéseit követve.

   > [!NOTE]
   >
   > - Ha rendelkezik általános célú v1-vagy Blob Storage-fiókkal, először a *v2-re kell frissítenie* [egy általános célú v2-es Storage-fiókra való frissítés](../../storage/common/storage-account-upgrade.md)lépéseit követve.
   > - Azure Data Lake Storage Gen2tel kapcsolatos ismert problémák esetén tekintse meg a [Azure Data Lake Storage Gen2 ismert problémáit](../../storage/blobs/data-lake-storage-known-issues.md).

1. A Storage-fiók területen lépjen a **Access Control (iam)** elemre, és válassza a **szerepkör-hozzárendelés hozzáadása** elemet. Rendelje hozzá a **Storage blob-adatközreműködő** Azure-szerepkört a dedikált SQL-készletet üzemeltető kiszolgálóhoz vagy munkaterülethez, amelyet az Azure ad-ben regisztrált.

   > [!NOTE]
   > Ezt a lépést csak a Storage-fiók tulajdonosi jogosultsággal rendelkező tagjai hajthatják végre. A különböző Azure-beli beépített szerepkörökhöz lásd: az [Azure beépített szerepkörei](../../role-based-access-control/built-in-roles.md).
  
1. Az Azure Storage-fiókhoz való alapszintű kapcsolat engedélyezése:

   1. Hozzon létre egy adatbázis- [főkulcsot](/sql/t-sql/statements/create-master-key-transact-sql) , ha még nem hozott létre ilyet.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Hozzon létre egy adatbázis-hatókörű hitelesítő adatokat az **Identity = ' Managed Service Identity '** paranccsal.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Nem kell megadnia a titkos kulcsot egy Azure Storage-hozzáférési kulccsal, mert ez a mechanizmus [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md) használ a borítók alatt.
       > - Az identitás neve **"Managed Service Identity"** lehet a virtuális hálózathoz védett Azure Storage-fiókkal való együttműködéshez.

   1. Hozzon létre egy külső adatforrást a `abfss://` sémával az általános célú v2 Storage-fiókhoz a Base használatával való csatlakozáshoz.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Ha már rendelkezik egy általános célú v1-vagy Blob Storage-fiókkal társított külső táblákkal, először el kell dobnia ezeket a külső táblákat. Ezután dobja el a megfelelő külső adatforrást. Ezután hozzon létre egy külső adatforrást a `abfss://` sémával, amely egy általános célú v2 Storage-fiókhoz csatlakozik, amint azt korábban már látható. Ezután hozza létre újra az összes külső táblát az új külső adatforrás használatával. Használhatja a parancsfájlok létrehozása [és közzététele varázslót](/sql/ssms/scripting/generate-and-publish-scripts-wizard) a Create-scriptek létrehozásához az összes külső táblához a könnyebb kezelhetőség érdekében.
       > - A sémával kapcsolatos további információkért `abfss://` lásd: [a Azure Data Lake Storage Gen2 URI használata](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - A külső ADATFORRÁS LÉTREHOZÁSával kapcsolatos további információkért tekintse meg [ezt az útmutatót](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Lekérdezés normál módon [külső táblák](/sql/t-sql/statements/create-external-table-transact-sql)használatával.

### <a name="sql-database-blob-auditing"></a>BLOB-naplózás SQL Database

A blob-naplózás leküldi a naplókat a saját Storage-fiókjába. Ha ez a Storage-fiók a virtuális hálózati szolgáltatás végpontok szolgáltatását használja, akkor a SQL Databaseról a Storage-fiókhoz való kapcsolódás megszakad.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Virtuális hálózati tűzfalszabály hozzáadása a kiszolgálóhoz

Régen a funkció továbbfejlesztése előtt be kellett kapcsolni a virtuális hálózati szolgáltatás-végpontokat, mielőtt a tűzfalon üzembe kellene léptetni egy élő virtuális hálózati szabályt. Az adott virtuális hálózati alhálózathoz kapcsolódó végpontok egy SQL Database adatbázisában. Január 2018-én ezt a követelményt a **IgnoreMissingVNetServiceEndpoint** jelző beállításával lehet megkerülni. Most hozzáadhat egy virtuális hálózati tűzfalszabály-szabályt a kiszolgálóhoz a virtuális hálózati szolgáltatás végpontjai bekapcsolása nélkül.

Egy tűzfalszabály beállítása nem segít a kiszolgáló biztonságossá tételében. A biztonság érvénybe léptetéséhez be kell kapcsolni a virtuális hálózati szolgáltatás végpontját is. A szolgáltatási végpontok bekapcsolásakor a virtuális hálózat alhálózata leállást tapasztal, amíg be nem fejeződik a kikapcsolás és a közötti váltás. Ez az állásidő különösen igaz a nagyméretű virtuális hálózatok kontextusában. A **IgnoreMissingVNetServiceEndpoint** jelzővel csökkentheti vagy törölheti az állásidőt az áttérés során.

A **IgnoreMissingVNetServiceEndpoint** jelzőt a PowerShell használatával állíthatja be. További információ: a [virtuális hálózati szolgáltatás végpontjának és szabályának létrehozása a PowerShell][sql-db-vnet-service-endpoint-rule-powershell-md-52d]-lel a SQL Databasehoz.

## <a name="errors-40914-and-40615"></a>40914 és 40615 hibák

A 40914-es kapcsolati hiba a Azure Portal **tűzfal** ablaktábláján megadott *virtuális hálózati szabályokra* vonatkozik. A 40615-es hiba hasonló, azzal a különbséggel, hogy a tűzfal *IP-címeire vonatkozó szabályokra* vonatkozik.

### <a name="error-40914"></a>40914-es hiba

**Üzenet szövege:** "Nem lehet megnyitni a bejelentkezés által kért"*[Server-Name]*"kiszolgálót. Az ügyfél nem jogosult a kiszolgálóhoz való hozzáférésre. "

**Hiba leírása:** Az ügyfél olyan alhálózatban található, amely virtuális hálózati kiszolgáló-végpontokkal rendelkezik. A kiszolgálónak azonban nincs olyan virtuális hálózati szabálya, amely engedélyezi az alhálózatnak az adatbázissal való kommunikációt.

**Hiba feloldása:** A Azure Portal **tűzfal** paneljén a virtuális hálózati szabályok vezérlőelem használatával [adjon hozzá egy virtuális hálózati szabályt](#anchor-how-to-by-using-firewall-portal-59j) az alhálózathoz.

### <a name="error-40615"></a>40615-es hiba

**Üzenet szövege:** "A bejelentkezés által kért" kiszolgáló nem nyitható meg {0} . A (z) "" IP-címmel rendelkező ügyfél {1} nem jogosult a kiszolgálóhoz való hozzáférésre. "

**Hiba leírása:** Az ügyfél olyan IP-címről próbál csatlakozni, amely nem rendelkezik jogosultsággal a kiszolgálóhoz való csatlakozásra. A kiszolgáló tűzfalának nincs olyan IP-cím szabálya, amely lehetővé teszi, hogy az ügyfél a megadott IP-címről kommunikáljon az adatbázissal.

**Hiba feloldása:** Adja meg az ügyfél IP-címét IP-szabályként. Ezt a lépést a Azure Portal **tűzfal** paneljén végezheti el.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Virtuális hálózati szabály létrehozása a portál használatával

Ez a szakasz azt szemlélteti, hogyan használható a [Azure Portal][http-azure-portal-link-ref-477t] egy *virtuális hálózati szabály* létrehozásához az adatbázisban a SQL Databaseban. A szabály arra utasítja az adatbázist, hogy fogadja a kommunikációt egy adott alhálózatból, amely egy *virtuális hálózati szolgáltatás-végpontként* van megjelölve.

> [!NOTE]
> Ha szolgáltatási végpontot szeretne hozzáadni a kiszolgáló virtuális hálózati tűzfalának szabályaihoz, először győződjön meg arról, hogy a szolgáltatási végpontok be vannak kapcsolva az alhálózathoz.
>
> Ha a szolgáltatási végpontok nincsenek bekapcsolva az alhálózathoz, a portál arra kéri, hogy engedélyezze őket. Kattintson az **Engedélyezés** gombra azon a ablaktáblán, amelyen hozzá szeretné adni a szabályt.

## <a name="powershell-alternative"></a>PowerShell-alternatíva

A parancsfájlok létrehozhatnak virtuális hálózati szabályokat is a **New-AzSqlServerVirtualNetworkRule PowerShell-** parancsmag vagy [az az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal. Ha érdekli, tekintse meg a következő témakört: [PowerShell virtuális hálózati szolgáltatás végpontjának és szabályának létrehozása SQL Databasehoz][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternatív megoldás

Belsőleg a PowerShell-parancsmagok az SQL-alapú virtuális hálózati műveletekhez REST API-k hívására használhatók. A REST API-kat közvetlenül is meghívhatja.

- [Virtuális hálózati szabályok: műveletek][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Előfeltételek

Már rendelkeznie kell egy olyan alhálózattal, amely a SQL Databasehoz tartozó virtuális hálózati szolgáltatás végpontjának a *nevét adja* meg.

- A megfelelő végpont-típus neve a **Microsoft. SQL**.
- Ha az alhálózat nem címkézhető a típus nevével, tekintse [meg az alhálózat ellenőrzése végpontot][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal lépések

1. Jelentkezzen be az [Azure Portalra][http-azure-portal-link-ref-477t].

1. Keresse meg és válassza ki az **SQL-kiszolgálók** elemet, majd válassza ki a kiszolgálót. A **Biztonság** területen válassza a **tűzfalak és virtuális hálózatok** lehetőséget.

1. Állítsa **be az** **Azure-szolgáltatásokhoz való hozzáférés engedélyezése lehetőséget** .

    > [!IMPORTANT]
    > Ha a vezérlőt **a** be értékre hagyja, a kiszolgáló az Azure határán belüli bármely alhálózatról fogad kommunikációt. Ez olyan kommunikáció, amely az Azure-adatközpontok számára meghatározott tartományon belül elismert egyik IP-címről származik. **Ha a vezérlőt a be** értékre állítja, előfordulhat, hogy a biztonsági szempontból túlságosan nagy a hozzáférés. A Microsoft Azure Virtual Network szolgáltatás végpontja SQL Database és a Virtual Network Rules szolgáltatással együttműködve csökkentheti a biztonsági felületet.

1. Válassza a **+ meglévő hozzáadása** lehetőséget a **virtuális hálózatok** szakaszban.

    ![Képernyőfelvétel: a meglévő (alhálózat-végpont) és az SQL-szabály hozzáadása lehetőség kiválasztása.][image-portal-firewall-vnet-add-existing-10-png]

1. Az új **Létrehozás/frissítés** panelen töltse ki az Azure-erőforrások nevét tartalmazó mezőket.

    > [!TIP]
    > Meg kell adnia az alhálózat helyes címének előtagját. A **címek előtagjának** értéke a portálon található. Nyissa meg az összes típusú **erőforrást** a &gt;  &gt; **virtuális hálózatok** között. A szűrő megjeleníti a virtuális hálózatokat. Válassza ki a virtuális hálózatot, majd válassza az **alhálózatok** lehetőséget. A **címtartomány** oszlopban a szükséges előtag szerepel.

    ![Képernyőkép, amely megjeleníti az új szabályhoz tartozó mezők kitöltését.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Kattintson a panel alján található **OK** gombra.

1. Tekintse meg az eredményül kapott virtuális hálózati szabályt a **tűzfal** ablaktáblán.

    ![Képernyőkép, amely megjeleníti az új szabályt a tűzfal ablaktáblán.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> A következő állapotok vagy állapotok érvényesek a szabályokra:
>
> - **Ready (kész**): azt jelzi, hogy az elindított művelet sikeresen befejeződött.
> - **Sikertelen**: azt jelzi, hogy a kezdeményezett művelet meghiúsult.
> - **Törölve**: csak a törlési műveletre vonatkozik, és azt jelzi, hogy a szabály törölve lett, és már nem érvényes.
> - **Inprogress**: azt jelzi, hogy a művelet folyamatban van. A régi szabály akkor érvényes, ha a művelet ebben az állapotban van.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Azure Virtual Network szolgáltatásbeli végpontok][vm-virtual-network-service-endpoints-overview-649d]
- [Kiszolgálói szintű és adatbázis-szintű tűzfalszabályok][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Következő lépések

- [A PowerShell használatával hozzon létre egy virtuális hálózati szolgáltatás végpontját, majd egy virtuális hálózati szabályt SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtuális hálózati szabályok:][rest-api-virtual-network-rules-operations-862r] a REST API-kkal rendelkező műveletek

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
