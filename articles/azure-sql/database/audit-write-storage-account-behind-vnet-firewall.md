---
title: Naplózás a Storage-fiókba a VNet és a tűzfal mögött
description: Naplózás konfigurálása adatbázis-események írásához a virtuális hálózat és a tűzfal mögötti Storage-fiókban
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 908c9f1d05c83eaa58f77b79a32d956898c35076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93348253"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Naplózás írása a VNet és a tűzfal mögötti Storage-fiókba
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


A [Azure SQL Database](sql-database-paas-overview.md) és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) naplózása támogatja az adatbázis-események megírását egy [Azure Storage-fiókba](../../storage/common/storage-account-overview.md) egy virtuális hálózat és tűzfal mögött.

Ez a cikk két módszert ismertet a Azure SQL Database és az Azure Storage-fiók konfigurálásához ehhez a beállításhoz. Az első a Azure Portal használja, a második pedig REST-et használ.

## <a name="background"></a>Háttér

Az [azure Virtual Network (VNet)](../../virtual-network/virtual-networks-overview.md) az Azure-beli magánhálózat alapvető építőeleme. A VNet számos Azure-erőforrást, például Azure Virtual Machines (VM) tesz lehetővé az egymással, az internettel és a helyszíni hálózatokkal való biztonságos kommunikációhoz. A VNet hasonló a saját adatközpontban található hagyományos hálózathoz, de az Azure-infrastruktúra további előnyeit, például a méretezhetőséget, a rendelkezésre állást és az elkülönítést is lehetővé teszi.

További információ a VNet-fogalmakról, az ajánlott eljárásokról és sok egyébről: [Mi az az Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

A virtuális hálózatok létrehozásával kapcsolatos további tudnivalókért lásd: gyors útmutató [: virtuális hálózat létrehozása a Azure Portal használatával](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a naplózás egy VNet vagy tűzfal mögötti Storage-fiókba írjon, a következő előfeltételek szükségesek:

> [!div class="checklist"]
>
> * Általános célú v2-es Storage-fiók. Ha rendelkezik általános célú v1-vagy blob Storage-fiókkal, [frissítsen egy általános célú v2 Storage-fiókra](../../storage/common/storage-account-upgrade.md). További információ: Storage- [fiókok típusai](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * A Storage-fióknak ugyanazon az előfizetésen belül kell lennie, és ugyanazon a helyen kell lennie, mint a [logikai SQL Server](logical-servers.md).
> * Az Azure Storage-fiókhoz szükséges `Allow trusted Microsoft services to access this storage account` . Állítsa be ezt a Storage **-fiók tűzfalakon és virtuális hálózatokon**.
> * Rendelkeznie kell `Microsoft.Authorization/roleAssignments/write` engedéllyel a kiválasztott Storage-fiókhoz. További információ: [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Konfigurálás az Azure Portalon

Kapcsolódjon [Azure Portal](https://portal.azure.com) az előfizetéséhez. Navigáljon az erőforráscsoport és a kiszolgáló területére.

1. Kattintson a **naplózás** elemre a biztonsági fejléc alatt. Válassza **a** be lehetőséget.

2. Válassza a **tárterület** lehetőséget. Válassza ki azt a Storage-fiókot, ahol a rendszer menti a naplókat. A Storage-fióknak meg kell felelnie az [Előfeltételek](#prerequisites)szakaszban felsorolt követelményeknek.

3. **Tároló részleteinek** megnyitása

  > [!NOTE]
  > Ha a kiválasztott Storage-fiók a VNet mögött található, a következő üzenet jelenik meg:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Ha nem látja ezt az üzenetet, a Storage-fiók nem egy VNet mögött található.

4. Adja meg a megőrzési időtartam napjainak számát. Ezután kattintson az **OK** gombra. A megőrzési időtartamnál régebbi naplók törlődnek.

5. Válassza a **Mentés** lehetőséget a naplózási beállításokban.

Sikeresen konfigurálta a naplózást egy VNet vagy tűzfal mögötti Storage-fiókba való íráshoz.

## <a name="configure-with-rest-commands"></a>Konfigurálás REST-parancsokkal

A Azure Portal használatának alternatívájaként a REST-parancsok használatával konfigurálhatja a naplózást, hogy az adatbázis-eseményeket egy VNet és tűzfal mögötti Storage-fiókba írja.

Az ebben a szakaszban szereplő parancsfájloknak a futtatása előtt frissíteniük kell a parancsfájlt. Cserélje le a következő értékeket a parancsfájlokban:

|Mintaérték|Minta leírása|
|:-----|:-----|
|`<subscriptionId>`| Azure-előfizetés azonosítója|
|`<resource group>`| Erőforráscsoport|
|`<logical SQL Server>`| Kiszolgálónév|
|`<administrator login>`| Rendszergazdai fiók |
|`<complex password>`| A rendszergazdai fiókhoz tartozó összetett jelszó|

Az SQL audit beállítása az események VNet vagy tűzfal mögötti Storage-fiókba való írásához:

1. Regisztrálja a kiszolgálót a Azure Active Directory (Azure AD) szolgáltatásban. Használja a PowerShellt vagy a REST API.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**REST API**](/rest/api/sql/servers/createorupdate):

   Példa a kérelemre

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   A kérés törzse

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
     }
   }
   ```

2. Nyissa meg az [Azure Portalt](https://portal.azure.com). Nyissa meg a tárfiókot. Keresse meg **Access Control (iam)**, majd kattintson a **szerepkör-hozzárendelés hozzáadása** lehetőségre. Rendeljen hozzá **Storage blob-adatközreműködő** Azure-szerepkört a Azure Active Directory (Azure ad) szolgáltatásban regisztrált adatbázist futtató kiszolgálóhoz az előző lépésben leírtak szerint.

   > [!NOTE]
   > Ezt a lépést csak a tulajdonosi jogosultsággal rendelkező tagok hajthatják végre. A különböző Azure-beli beépített szerepkörökhöz tekintse meg az [Azure beépített szerepköreit](../../role-based-access-control/built-in-roles.md).

3. Konfigurálja a [kiszolgáló blob naplózási házirendjét](/rest/api/sql/server%20auditing%20settings/createorupdate)a *storageAccountAccessKey* megadása nélkül:

   Példa a kérelemre

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
   ```

   A kérés törzse

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata

- [Adatbázis-naplózási szabályzat létrehozása vagy frissítése (set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Kiszolgáló naplózási szabályzatának létrehozása vagy frissítése (set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata

A naplózás konfigurálásával adatbázis-eseményeket írhat a virtuális hálózat és a tűzfal mögötti Storage-fiókban [Azure Resource Manager](../../azure-resource-manager/management/overview.md) sablonnal, ahogy az alábbi példában is látható:

> [!IMPORTANT]
> A virtuális hálózat és a tűzfal mögötti Storage-fiók használatához a **isStorageBehindVnet** paramétert True értékre kell állítani.

- [Azure-SQL Server üzembe helyezése, amely lehetővé teszi, hogy naplózza a naplófájlokat a blob Storage-ba](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> A csatolt minta egy külső nyilvános tárházban található, és az "adott állapotban" van megadva, garancia nélkül, és semmilyen Microsoft támogatási program/szolgáltatás nem támogatott.

## <a name="next-steps"></a>Következő lépések

* [A PowerShell használatával hozzon létre egy virtuális hálózati szolgáltatási végpontot, majd egy virtuális hálózati szabályt a Azure SQL Databasehoz.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Virtual Network szabályok: a REST API-kkal végzett műveletek](/rest/api/sql/virtualnetworkrules)
* [Virtuális hálózati szolgáltatási végpontok és szabályok használata a kiszolgálókon](vnet-service-endpoint-rule-overview.md)
