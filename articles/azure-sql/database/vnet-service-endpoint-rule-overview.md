---
title: Virtuális hálózati végpontok és szabályok az Azure SQL Database
description: Alhálózat megjelölése virtuális hálózati szolgáltatásvégpontként. Ezután adja hozzá a végpontot virtuális hálózati szabályként az adatbázis ACL-jhez. Az adatbázis ezután fogadja az alhálózaton lévő összes virtuális gépről és más csomópontról származó kommunikációt.
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
ms.openlocfilehash: 67e807e948caf1fec014457814c1b7f105630f9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784424"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Virtuális hálózati szolgáltatásvégpontok és szabályok használata kiszolgálókhoz az Azure SQL Database-ben

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

 A virtuális hálózati szabályok olyan tűzfalbiztonsági szolgáltatások, amelyek azt vezérlik, hogy az [Azure SQL Database](sql-database-paas-overview.md) adatbázisai és rugalmas készletei kiszolgálója, vagy az Azure Synapse Analytics-ban a dedikált SQL-készlet (korábban SQL DW) adatbázisai [fogadnak-e](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) kommunikációt a virtuális hálózatok adott alhálózatairól. Ez a cikk ismerteti, hogy miért a virtuális hálózati szabályok a legjobb választás az adatbázissal való kommunikáció biztonságos engedélyezéséhez a SQL Database és Azure Synapse Analytics.

> [!NOTE]
> Ez a cikk a SQL Database és a Azure Synapse Analytics. Az egyszerűség kedvéért az *adatbázis kifejezés* a két adatbázisban SQL Database adatbázisra Azure Synapse Analytics. Hasonlóképpen, a kiszolgálóra mutató *hivatkozások* a kiszolgálót futtató logikai [SQL-kiszolgálóra](logical-servers.md) SQL Database Azure Synapse Analytics.

Virtuális hálózati szabály létrehozásához először lennie [][vm-virtual-network-service-endpoints-overview-649d] kell egy virtuális hálózati szolgáltatásvégpontnak, hogy a szabály hivatkozni fog.

## <a name="create-a-virtual-network-rule"></a>Virtuális hálózati szabály létrehozása

Ha csak virtuális hálózati szabályt szeretne létrehozni, ugorjon a cikk későbbi részében található lépésekre és [magyarázatra.](#anchor-how-to-by-using-firewall-portal-59j)

## <a name="details-about-virtual-network-rules"></a>A virtuális hálózati szabályok részletei

Ez a szakasz a virtuális hálózati szabályokkal kapcsolatos néhány részletet ismertet.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi régió

Minden virtuális hálózati szolgáltatásvégpont csak egy Azure-régióra vonatkozik. A végpont nem engedélyezi, hogy más régiók kommunikációt fogadjanak az alhálózatról.

A virtuális hálózati szabályok arra a régióra korlátozódnak, amelyekre a mögöttes végpont vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgálói szint, nem adatbázisszint

Minden virtuális hálózati szabály a teljes kiszolgálóra vonatkozik, nem csak a kiszolgálón található egy adott adatbázisra. Más szóval a virtuális hálózati szabályok kiszolgálószinten érvényesek, nem az adatbázis szintjén.

Ezzel szemben az IP-szabályok mindkét szinten alkalmazhatók.

### <a name="security-administration-roles"></a>Biztonsági adminisztrációs szerepkörök

A virtuális hálózati szolgáltatásvégpont felügyeletében a biztonsági szerepkörök elkülönülnek. A következő szerepkörök mindegyikéhez műveletet kell megadni:

- **Hálózati rendszergazda [(Hálózati közreműködő szerepkör):](../../role-based-access-control/built-in-roles.md#network-contributor)** &nbsp; Kapcsolja be a végpontot.
- **Adatbázisgazda [(SQL Server közreműködői](../../role-based-access-control/built-in-roles.md#sql-server-contributor) szerepkör):** &nbsp; Frissítse a hozzáférés-vezérlési listát (ACL), hogy hozzáadja az adott alhálózatot a kiszolgálóhoz.

#### <a name="azure-rbac-alternative"></a>Azure RBAC-alternatíva

A Hálózati rendszergazda és az Adatbázisgazda szerepkör több képességgel rendelkezik, mint a virtuális hálózati szabályok kezeléséhez szükséges. Csak a képességeik egy részére van szükség.

Lehetősége van szerepköralapú [hozzáférés-vezérlést (RBAC)][rbac-what-is-813s] használni az Azure-ban egyetlen egyéni szerepkör létrehozásához, amely csak a képességek szükséges részkészletét használja. Az egyéni szerepkör használható a hálózati rendszergazda vagy az adatbázis-rendszergazda bevonása helyett. A biztonsági kitettség felülete alacsonyabb, ha egy felhasználót hozzáad egy egyéni szerepkörhöz, és nem adja hozzá a felhasználót a másik két fő rendszergazdai szerepkörhöz.

> [!NOTE]
> Bizonyos esetekben az adatbázis a SQL Database és a virtuális hálózat alhálózata eltérő előfizetésben található. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
>
> - Mindkét előfizetésnek ugyanabban a Azure Active Directory (Azure AD)-bérlőben kell lennie.
> - A felhasználó rendelkezik a szükséges engedélyekkel a műveletek kezdeményezéséhez, például a szolgáltatásvégpont engedélyezéséhez és egy virtuális hálózati alhálózat adott kiszolgálóhoz való hozzáadásához.
> - Mindkét előfizetésben regisztrálni kell a Microsoft.Sql szolgáltatót.

## <a name="limitations"></a>Korlátozások

A SQL Database a virtuális hálózati szabályok funkcióra a következő korlátozások vonatkoznak:

- Az adatbázis tűzfalán az SQL Database virtuális hálózati szabály egy alhálózatra hivatkozik. Az összes hivatkozott alhálózatot ugyanabban a földrajzi régióban kell üzemeltetni, ahol az adatbázis található.
- Minden kiszolgálónak legfeljebb 128 ACL-bejegyzése lehet bármely virtuális hálózathoz.
- A virtuális hálózati szabályok csak a virtuális Azure Resource Manager, a klasszikus üzembe [helyezési modellben lévő hálózatokra nem vonatkoznak.][arm-deployment-model-568f]
- Ha bekapcsolja a virtuális hálózati szolgáltatásvégpontokat a SQL Database a végpontok számára is engedélyezi a Azure Database for MySQL és Azure Database for PostgreSQL. Ha a végpontok **ON (BE)** beállításra vannak állítva, a végpontok és a Azure Database for MySQL vagy Azure Database for PostgreSQL sikertelen lehet.
  - Ennek az az oka Azure Database for MySQL hogy Azure Database for PostgreSQL virtuális hálózati szabály valószínűleg nincs konfigurálva. Konfigurálnia kell egy virtuális hálózati szabályt a Azure Database for MySQL és Azure Database for PostgreSQL, és a kapcsolat sikeres lesz.
  - Ha privát végpontokkal már konfigurált SQL logikai kiszolgálón virtuális hálózati tűzfalszabályokat kell meghatároznia, állítsa a Nyilvános hálózati hozzáférés megtagadása beállítását **a** **Nem beállításra.**
- A tűzfalon az IP-címtartományok a következő hálózati elemekre vonatkoznak, a virtuális hálózati szabályok viszont nem:
  - [Hely–hely (S2S) virtuális magánhálózat (VPN)][vpn-gateway-indexmd-608y]
  - Helyszíni hálózat [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Megfontolandó szempontok szolgáltatásvégpont használata esetén

Ha szolgáltatásvégpontokat használ a SQL Database tekintse át a következő szempontokat:

- **A nyilvános IP Azure SQL Database felé irányuló kimenő forgalomra van szükség.** A hálózati biztonsági csoportokat (NSG-ket) meg kell nyitni a SQL Database IP-k számára a csatlakozáshoz. Ezt az NSG-szolgáltatáscímkék használatával [SQL Database.](../../virtual-network/network-security-groups-overview.md#service-tags)

### <a name="expressroute"></a>ExpressRoute

Ha a helyszínről használja az [ExpressRoute-et,](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a nyilvános társviszony-létesítéshez vagy a Microsoft-társviszony-létesítéshez azonosítania kell a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. Microsoft-társviszony-létesítésnél a használt NAT IP-címeket az ügyfél vagy a szolgáltató biztosítja. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. Az ExpressRoute nyilvános és Microsoft társviszony-létesítés NAT-ral kapcsolatos további információért lásd: Az Azure nyilvános társviszony-létesítés [NAT-követelményei.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

Ahhoz, hogy lehetővé tegye a kapcsolati kapcsolat SQL Database közötti kommunikációt, IP-hálózati szabályokat kell létrehoznia a NAT nyilvános IP-címeinek számára.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>A virtuális hálózati szolgáltatásvégpont azure storage-ral való használatának hatása

Az Azure Storage ugyanazt a funkciót implementálta, amellyel korlátozhatja az Azure Storage-fiók kapcsolatait. Ha ezt a funkciót olyan Azure Storage-fiókkal használja, SQL Database használja, problémákba fog belefutni. A következő egy lista, amely az SQL Database Azure Synapse Analytics funkciókat sorolja fel és megvitatja.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase és a COPY utasítás

A PolyBase-t és a COPY utasítást általában arra használják, hogy adatokat töltsenek Azure Synapse Analytics Azure Storage-fiókokból a nagy átviteli sebességű adatbetöltéshez. Ha az Azure Storage-fiók, amelyből a korlátokból tölt be adatokat, csak virtuális hálózati alhálózatok egy halmazához fér hozzá, a PolyBase használata és a COPY utasítás a tárfiókba való csatlakoztatása megszakad. Az importálási és exportálási forgatókönyvek a COPY és a PolyBase Azure Synapse Analytics virtuális hálózathoz védett Azure Storage-hoz való csatlakozásával való engedélyezéséhez kövesse az ebben a szakaszban található lépéseket.

#### <a name="prerequisites"></a>Előfeltételek

- Telepítse Azure PowerShell az útmutató [segítségével.](/powershell/azure/install-az-ps)
- Ha általános célú v1- vagy Azure Blob Storage-fiókja van, először frissítenie kell az általános célú v2-re a Frissítés általános célú [v2 tárfiókra lépéseit követve.](../../storage/common/storage-account-upgrade.md)
- Az Azure **Storage-fiók** Tűzfalak és virtuális hálózatok beállításai menüjében be kell kapcsolva a Tárfiók hozzáférésének engedélyezése a megbízható Microsoft-szolgáltatások számára **beállítással.** Ennek a konfigurációnak az engedélyezése lehetővé teszi, hogy a PolyBase és a COPY utasítás erős hitelesítéssel csatlakozzon a tárfiókhoz, ahol a hálózati forgalom az Azure gerinchálózatán marad. További információért tekintse meg ezt [az útmutatót.](../../storage/common/storage-network-security.md#exceptions)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modult továbbra is támogatja a SQL Database, de minden jövőbeli fejlesztés az Az.Sql modulra lesz kihozva. Az AzureRM modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat. Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek. További információ a kompatibilitásukról: [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Az új Azure PowerShell Az modul).

#### <a name="steps"></a>Lépések

1. Ha önálló dedikált SQL-készlete van, regisztrálja az SQL-kiszolgálót az Azure AD-ban a PowerShell használatával:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Ez a lépés nem szükséges a dedikált SQL-készletekhez egy Azure Synapse Analytics munkaterületen.

1. Ha már van Azure Synapse Analytics munkaterülete, regisztrálja a munkaterület rendszer által felügyelt identitását:

   1. A munkaterület Azure Synapse Analytics a Azure Portal.
   2. Ugrás a **Felügyelt identitások panelre.**
   3. Győződjön meg **arról, hogy a Folyamatok engedélyezése** lehetőség engedélyezve van.
   
1. Hozzon **létre egy általános célú v2-tárfiókot** a [Tárfiók létrehozása lépéseit követve.](../../storage/common/storage-account-create.md)

   > [!NOTE]
   >
   > - Ha általános célú v1- vagy Blob Storage-fiókja van, először frissítenie kell a *v2-re* a Frissítés általános célú [v2-tárfiókra lépéseit követve.](../../storage/common/storage-account-upgrade.md)
   > - A hibákkal kapcsolatos ismert Azure Data Lake Storage Gen2 lásd: [A](../../storage/blobs/data-lake-storage-known-issues.md)Azure Data Lake Storage Gen2.

1. A tárfiók alatt válassza a Access Control **(IAM)** lehetőséget, és válassza a **Szerepkör-hozzárendelés hozzáadása lehetőséget.** Rendelje hozzá **a Storage-blobadatok közreműködője** Azure-szerepkört a dedikált SQL-készletet üzemeltető kiszolgálóhoz vagy munkaterülethez, amelyet az Azure AD-ban regisztrált.

   > [!NOTE]
   > Ezt a lépést csak a tárfiók tulajdonosi jogosultsággal rendelkező tagjai hajthatja végre. A különböző beépített Azure-szerepkörökről az [Azure beépített szerepköreit lásd:](../../role-based-access-control/built-in-roles.md).
  
1. PolyBase-kapcsolat engedélyezése az Azure Storage-fiókhoz:

   1. Hozzon létre egy [adatbázis-főkulcsot,](/sql/t-sql/statements/create-master-key-transact-sql) ha korábban még nem hozott létre egyet.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Hozzon létre egy adatbázisra vonatkozó hitelesítő adatokat **az IDENTITY = 'Managed Service Identity' paraméterrel.**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Nem kell megadni a SECRET értéket egy Azure Storage hozzáférési kulccsal, mivel ez a mechanizmus a felügyelt identitást [használja.](../../active-directory/managed-identities-azure-resources/overview.md)
       > - Az IDENTITÁS neve **"Felügyeltszolgáltatás-identitás",** hogy a PolyBase-kapcsolat egy virtuális hálózathoz védett Azure Storage-fiókkal működjön.

   1. Hozzon létre egy külső adatforrást azzal a sémával, amely az általános célú `abfss://` v2-tárfiókhoz csatlakozik a PolyBase használatával.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Ha már rendelkezik társított külső táblákkal egy általános célú v1 vagy Blob Storage fiókhoz, először ezeket a külső táblákat kell eldobni. Ezután hagyja el a megfelelő külső adatforrást. Ezután hozzon létre egy külső adatforrást azzal a sémával, amely egy általános célú v2-tárfiókhoz csatlakozik `abfss://` a korábban bemutatott módon. Ezután hozza létre újra az összes külső táblát ezzel az új külső adatforrással. A Parancsfájlok [létrehozása](/sql/ssms/scripting/generate-and-publish-scripts-wizard) és közzététele varázslóval egyszerűen hozhat létre create-scripts parancsprogramokat az összes külső táblához.
       > - További információ a sémáról: A Azure Data Lake Storage Gen2 `abfss://` [URI használata.](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)
       > - A CREATE EXTERNAL DATA SOURCE (KÜLSŐ ADATFORRÁS LÉTREHOZÁSA) ről további információt ebben az [útmutatóban láthat.](/sql/t-sql/statements/create-external-data-source-transact-sql)

   1. A lekérdezést a szokásos módon, külső [táblák használatával kell lekérdezni.](/sql/t-sql/statements/create-external-table-transact-sql)

### <a name="sql-database-blob-auditing"></a>SQL Database blobok naplózása

A blobnaplózás lekérte az auditnaplókat a saját tárfiókjára. Ha ez a tárfiók a virtuális hálózati szolgáltatásvégpont szolgáltatást használja, a SQL Database és a tárfiók között megszakad a kapcsolat.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Virtuális hálózati tűzfalszabály hozzáadása a kiszolgálóhoz

Már a funkció továbbfejlesztsége előtt be kellett kapcsolnia a virtuális hálózati szolgáltatásvégpontokat, mielőtt egy élő virtuális hálózati szabályt implementált volna a tűzfalon. A végpontok egy adott virtuális hálózat alhálózatát egy adatbázishoz kapcsolták SQL Database. 2018 januárjában ezt a követelményt megkerülheti az **IgnoreMissingVNetServiceEndpoint jelző beállításával.** Most hozzáadhat egy virtuális hálózati tűzfalszabályt a kiszolgálóhoz anélkül, hogy bekapcsolja a virtuális hálózati szolgáltatásvégpontokat.

Egy tűzfalszabály beállítása nem segít a kiszolgáló biztonságának beállításában. A biztonság effektushoz be kell kapcsolnia a virtuális hálózati szolgáltatásvégpontokat is. Amikor bekapcsolja a szolgáltatásvégpontokat, a virtuális hálózat alhálózata állásidőt tapasztal, amíg be nem fejeződik a kikapcsoltról a bekapcsoltra való váltás. Ez az állásidő különösen nagy virtuális hálózatok esetén igaz. Az **IgnoreMissingVNetServiceEndpoint** jelzővel csökkentheti vagy megszüntetheti az állásidőt az átváltás során.

Az **IgnoreMissingVNetServiceEndpoint** jelzőt a PowerShell használatával állíthatja be. További információ: [PowerShell][sql-db-vnet-service-endpoint-rule-powershell-md-52d]virtuális hálózati szolgáltatásvégpont és szabály létrehozásához a SQL Database.

## <a name="errors-40914-and-40615"></a>40914-es és 40615-ös hibák

A 40914-es csatlakozási hiba a virtuális  hálózati szabályokkal kapcsolatos, a tűzfal panelen megadott Azure Portal. A 40615-ös hiba hasonló, azzal a kivétellal, hogy a tűzfal *IP-cím* szabályaihoz kapcsolódik.

### <a name="error-40914"></a>40914-es hiba

**Üzenet szövege:** "A bejelentkezés által kért *[kiszolgálónév]*" kiszolgáló nem nyílik meg. Az ügyfél nem férhet hozzá a kiszolgálóhoz."

**Hiba leírása:** Az ügyfél egy olyan alhálózatban található, amely virtuális hálózati kiszolgálóvégpontokkal rendelkezik. A kiszolgálónak azonban nincs olyan virtuális hálózati szabálya, amely jogot biztosít az alhálózatnak az adatbázissal való kommunikációra.

**Hibafeloldás:** A virtuális **hálózat Tűzfal** Azure Portal virtuális hálózati szabályok vezérlővel adjon hozzá egy virtuális hálózati szabályt [az](#anchor-how-to-by-using-firewall-portal-59j) alhálózathoz.

### <a name="error-40615"></a>40615-ös hiba

**Üzenet szövege:** "A bejelentkezés által kért " {0} kiszolgáló nem megnyitására szolgál. "" IP-címmel nem lehet {1} hozzáférni a kiszolgálóhoz."

**Hiba leírása:** Az ügyfél olyan IP-címről próbál csatlakozni, amely nem jogosult a kiszolgálóhoz való csatlakozásra. A kiszolgáló tűzfala nem rendelkezik olyan IP-címszabálysal, amely lehetővé teszi az ügyfél számára, hogy az adott IP-címről kommunikáljon az adatbázissal.

**Hibafeloldás:** Adja meg az ügyfél IP-címét IP-szabályként. Ehhez **a lépéshez** használja Azure Portal panelen.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Virtuális hálózati szabály létrehozása a portál használatával

Ez *a* szakasz azt mutatja [][http-azure-portal-link-ref-477t] be, hogyan használhatja a Azure Portal virtuális hálózati szabály létrehozására az adatbázisban a SQL Database. A szabály arra utasítja az adatbázist, hogy fogadjon kommunikációt egy adott alhálózatról, amely virtuális hálózati szolgáltatásvégpontként *van megjelölve.*

> [!NOTE]
> Ha szolgáltatásvégpontot szeretne hozzáadni a kiszolgáló virtuális hálózati tűzfalszabályaihoz, először győződjön meg arról, hogy a szolgáltatásvégpont be van kapcsolva az alhálózathoz.
>
> Ha a szolgáltatásvégpont nincs bekapcsolva az alhálózathoz, a portál megkéri, hogy engedélyezze őket. Kattintson az **Engedélyezés gombra** ugyanazon a panelen, amelyen hozzáadja a szabályt.

## <a name="powershell-alternative"></a>PowerShell-alternatíva

A szkriptek virtuális hálózati szabályokat is létrehozhatnak a **New-AzSqlServerVirtualNetworkRule** vagy [az az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)PowerShell-parancsmag használatával. Ha érdekli, tekintse meg [a PowerShellt][sql-db-vnet-service-endpoint-rule-powershell-md-52d]egy virtuális hálózati szolgáltatásvégpont és szabály létrehozásához a SQL Database.

## <a name="rest-api-alternative"></a>REST API alternatíva

Belsőleg az SQL virtuális hálózati műveletekHez szükséges PowerShell-parancsmagok REST API-kat hívnak meg. A REST API-kat közvetlenül is meg lehet hívni.

- [Virtuális hálózati szabályok: Műveletek][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Előfeltételek

Már rendelkezik egy alhálózattal, amely meg van jelölve az adott virtuális hálózati szolgáltatásvégpont-típus *nevével,* amely az adott SQL Database.

- A megfelelő végponttípus neve **Microsoft.Sql.**
- Ha előfordulhat, hogy az alhálózat nem a típus nevével van megcímkézve, tekintse meg annak ellenőrzését, hogy [az alhálózat végpont-e.][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal lépések

1. Jelentkezzen be az [Azure Portalra][http-azure-portal-link-ref-477t].

1. Keresse meg és válassza az **SQL-kiszolgálók lehetőséget,** majd válassza ki a kiszolgálót. A **Biztonság alatt** válassza a **Tűzfalak és virtuális hálózatok lehetőséget.**

1. Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése beállítását állítsa** **KI beállításra.**

    > [!IMPORTANT]
    > Ha a vezérlőt ON (be) beállításon **hagyja,** a kiszolgáló az Azure-határon belüli bármely alhálózatról fogad kommunikációt. Ez a kommunikáció az egyik olyan IP-címről származik, amely az Azure-adatközpontok számára meghatározott tartományokban találhatóként van felismerve. Ha a vezérlő be van **állítva,** az túlzott hozzáférést biztosít a biztonsági szempontból. Az Microsoft Azure Virtual Network szolgáltatásvégpont funkció a virtuális hálózati szabályokkal koordinálva együttesen SQL Database csökkentheti a biztonsági felületét.

1. A **Virtuális hálózatok szakaszban** válassza a + Meglévő **hozzáadása** lehetőséget.

    ![Képernyőkép a + Meglévő hozzáadása (alhálózati végpont, SQL-szabályként) lehetőség kiválasztásáról.][image-portal-firewall-vnet-add-existing-10-png]

1. Az új **Létrehozás/Frissítés panelen** töltse ki a mezőket az Azure-erőforrások nevével.

    > [!TIP]
    > Meg kell tartalmaznia az alhálózathoz megfelelő címelőtagot. A **Címelőtag értékét** a portálon találja. Ugrás a Minden **erőforrás minden típusú** &gt; **virtuális** &gt; **hálózatra.** A szűrő megjeleníti a virtuális hálózatokat. Válassza ki a virtuális hálózatot, majd válassza az **Alhálózatok lehetőséget.** A **CÍMTARTOMÁNY** oszlopban található a szükséges címelőtag.

    ![Képernyőkép az új szabály mezőinek kitöltésével.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Kattintson az **OK** gombra a panel alján.

1. Tekintse meg az eredményül kapott virtuális hálózati szabályt a **Tűzfal panelen.**

    ![Képernyőkép az új szabályról a Tűzfal panelen.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> A szabályokra a következő állapotok vagy állapotok vonatkoznak:
>
> - **Ready**(Kész): Azt jelzi, hogy a kezdeményezett művelet sikeres volt.
> - **Sikertelen:** Azt jelzi, hogy a kezdeményezett művelet sikertelen volt.
> - **Törölt:** Csak a Törlés műveletre vonatkozik, és jelzi, hogy a szabály törölve lett, és már nem érvényes.
> - **InProgress**: Azt jelzi, hogy a művelet folyamatban van. A régi szabály akkor érvényes, amikor a művelet ebben az állapotban van.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Azure-beli virtuális hálózati szolgáltatásvégpont][vm-virtual-network-service-endpoints-overview-649d]
- [Kiszolgálószintű és adatbázisszintű tűzfalszabályok][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Következő lépések

- [A PowerShell használatával hozzon létre egy virtuális hálózati szolgáltatásvégpontot, majd egy virtuális hálózati szabályt a SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtuális hálózati szabályok: REST][rest-api-virtual-network-rules-operations-862r] API-kat kiszolgáló műveletek

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
