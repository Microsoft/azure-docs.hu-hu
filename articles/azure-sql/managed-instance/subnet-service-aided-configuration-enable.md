---
title: A szolgáltatással segített alhálózat konfigurációjának engedélyezése a felügyelt Azure SQL-példányhoz
description: A szolgáltatással segített alhálózat konfigurációjának engedélyezése a felügyelt Azure SQL-példányhoz
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 67b398194d9094cd99fccaa85ed0df3be362ce2b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91618012"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>A szolgáltatással segített alhálózat konfigurációjának engedélyezése a felügyelt Azure SQL-példányhoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A szolgáltatással segített alhálózat-konfiguráció automatizált hálózati konfiguráció-felügyeletet biztosít a felügyelt példányokat üzemeltető alhálózatok számára. A szolgáltatással segített alhálózat-konfigurációval rendelkező felhasználó teljes mértékben felügyeli az adatokhoz való hozzáférést (TDS-adatforgalom), míg a felügyelt példányok feladata, hogy a felügyeleti forgalom zavartalan áramlását biztosítsa, hogy az SLA-t teljesítse.

Az automatikusan konfigurált hálózati biztonsági csoportok és útválasztási szabályok az ügyfél számára láthatók, és a _Microsoft. SQL-managedInstances_UseOnly_ _ előtaggal vannak ellátva.

A szolgáltatással segített konfiguráció automatikusan engedélyezve van, ha bekapcsolja az [alhálózat delegálását](../../virtual-network/subnet-delegation-overview.md) az `Microsoft.Sql/managedInstances` erőforrás-szolgáltatónál.

> [!IMPORTANT] 
> Ha az alhálózat – delegálás be van kapcsolva, nem kapcsolhatja ki, amíg el nem távolítja az utolsó virtuális fürtöt az alhálózatról. A virtuális fürt törlésével kapcsolatos további információkért tekintse meg a következő [cikket](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Mivel a szolgáltatással segített alhálózati konfiguráció elengedhetetlen az SLA fenntartásához, a 2020. május 1-től kezdődően a felügyelt példányokat nem lehet olyan alhálózatokban telepíteni, amelyek nem a felügyelt példány erőforrás-szolgáltatóhoz vannak delegálva. Július 1-jén 2020 a felügyelt példányokat tartalmazó összes alhálózat automatikusan delegálásra kerül a felügyelt példány erőforrás-szolgáltatója számára. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Alhálózat delegálásának engedélyezése új központi telepítések esetén
Ha felügyelt példányt kíván üzembe helyezni az üres alhálózaton, a `Microsoft.Sql/managedInstances` következő [cikkben](../../virtual-network/manage-subnet-delegation.md)leírtak szerint delegálnia kell az erőforrás-szolgáltatónak. _Vegye figyelembe, hogy a hivatkozott cikk `Microsoft.DBforPostgreSQL/serversv2` erőforrás-szolgáltatót használ például. `Microsoft.Sql/managedInstances` Ehelyett erőforrás-szolgáltatót kell használnia._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Alhálózati delegálás engedélyezése meglévő központi telepítésekhez

Az alhálózati delegálás engedélyezéséhez a felügyelt példányok meglévő központi telepítéséhez meg kell találnia azt a virtuális hálózati alhálózatot, ahol elhelyezve van. 

Ennek megismeréséhez tekintse meg `Virtual network/subnet` a `Overview` felügyelt példány portáljának paneljét.

Másik lehetőségként a következő PowerShell-parancsok futtatásával is elsajátíthatja ezt. Cserélje le az **előfizetés-azonosítót** az előfizetés-azonosítójával. Továbbá cserélje le a **RG nevet** a felügyelt példányhoz tartozó erőforráscsoporthoz, és cserélje le a **mi-Name nevet** a felügyelt példány nevére.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Miután megtalálta a felügyelt példány alhálózatát, delegálnia kell az `Microsoft.Sql/managedInstances` erőforrás-szolgáltatónak az alábbi [cikkben](../../virtual-network/manage-subnet-delegation.md)leírtak szerint. _Vegye figyelembe, hogy a hivatkozott cikk `Microsoft.DBforPostgreSQL/serversv2` erőforrás-szolgáltatót használ például. `Microsoft.Sql/managedInstances` Ehelyett erőforrás-szolgáltatót kell használnia._


> [!IMPORTANT]
> A szolgáltatással segített konfiguráció engedélyezése nem okoz feladatátvételt vagy megszakítást az alhálózatban már meglévő felügyelt példányok esetén.
