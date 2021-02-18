---
title: Feladatátvétel manuális kezdeményezése SQL felügyelt példányon
description: Ismerje meg, hogyan végezheti el az elsődleges és másodlagos replikák manuális feladatátvételét az Azure SQL felügyelt példányain.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 02/17/2021
ms.openlocfilehash: ecd97efbf12fb149037a94749bc899169f2c5a92
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096523"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Felhasználó által, felügyelt SQL-példányon kezdeményezett manuális feladatátvétel
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan lehet manuálisan feladatátvételt elvégezni az SQL felügyelt példányok általános célú (GP) és a üzletileg kritikus (BC) szolgáltatáshoz tartozó elsődleges csomópontok számára, és hogyan lehet manuálisan feladatátvételt elvégezni a csak a BC szolgáltatási szinten található másodlagos írásvédett replika csomóponton.

## <a name="when-to-use-manual-failover"></a>Mikor kell manuális feladatátvételt használni

A [magas rendelkezésre állás](../database/high-availability-sla.md) az SQL felügyelt példányok platformjának alapvető része, amely transzparens módon működik az adatbázis-alkalmazásokhoz. Az elsődlegestől a másodlagos csomópontok felé irányuló feladatátvételek csomópontok romlása vagy meghibásodása esetén, illetve a normál havi szoftverfrissítések esetében várhatóan előfordul minden olyan alkalmazás esetében, amely az SQL felügyelt példányát használja az Azure-ban.

A következő okok miatt érdemes lehet [manuális feladatátvételt](../database/high-availability-sla.md#testing-application-fault-resiliency) végrehajtani a FELÜGYELt SQL-példányon:
- Alkalmazás tesztelése feladatátvételi rugalmassághoz az éles környezetbe való üzembe helyezés előtt
- Végpontok közötti rendszerek tesztelése az automatikus feladatátvétel hibatűrése érdekében
- Annak tesztelése, hogyan befolyásolják a feladatátvétel a meglévő adatbázis-munkameneteket
- Annak ellenőrzése, hogy a feladatátvétel a hálózati késés változásai miatt megváltoztatja-e a végpontok közötti teljesítményt
- A lekérdezési teljesítmény romlása bizonyos esetekben a manuális feladatátvétel segít enyhíteni a teljesítménnyel kapcsolatos problémát.

> [!NOTE]
> Annak biztosítása, hogy az alkalmazások az éles környezetben történő üzembe helyezés előtt rugalmasak legyenek, segít csökkenteni az alkalmazás-meghibásodások kockázatát az éles környezetben, és az ügyfelek számára elérhetővé teszi az alkalmazások rendelkezésre állását.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Manuális feladatátvétel kezdeményezése az SQL felügyelt példányán

### <a name="azure-rbac-permissions-required"></a>Azure RBAC-engedélyek szükségesek

A feladatátvételt kezdeményező felhasználónak a következő Azure-szerepkörök egyikével kell rendelkeznie:

- Előfizetés tulajdonosi szerepköre, vagy
- Felügyelt példány közreműködői szerepköre, vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>A PowerShell használata

Az az. SQL minimális verziójának [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)kell lennie. Vegye fontolóra [Azure Cloud Shell](../../cloud-shell/overview.md) használatát a Azure Portal, amely mindig elérhető a legújabb PowerShell-verzióval. 

Előfeltételként használja az alábbi PowerShell-szkriptet a szükséges Azure-modulok telepítéséhez. Továbbá válassza ki azt az előfizetést, amelyben a feladatátvételhez használni kívánt felügyelt példány található.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Használja az alábbi PowerShell-parancsot: [AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) a következő példával kezdeményezheti az elsődleges csomópont feladatátvételét, amely a BC és a GP szolgáltatási szintjére is érvényes.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

A következő PS parancs használatával hajtson végre feladatátvételi olvasási másodlagos csomópontot, amely csak a BC szolgáltatási szintjére vonatkozik.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>A parancssori felület használata

Győződjön meg arról, hogy telepítve vannak a legújabb CLI-parancsfájlok.

Használja az az SQL mi feladatátvétel CLI parancsot az alábbi példával az elsődleges csomópont feladatátvételének elindításához, amely a BC és a GP szolgáltatási szintjére is érvényes.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

A következő CLI-parancs használatával hajtson végre feladatátvételi olvasási másodlagos csomópontot, amely csak a BC szolgáltatási szintjére vonatkozik.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>A REST API-val együtt

A folyamatos tesztelési folyamat vagy az automatizált teljesítmény-elhárító használatával olyan speciális felhasználók számára, akik az SQL-felügyelt példányaik feladatátvételét szeretnék automatizálni, egy API-hívással végezheti el a feladatátvétel kezdeményezését. Lásd: [felügyelt példányok – feladatátvételi REST API](/rest/api/sql/managed%20instances%20-%20failover/failover) a részletekért.

Ha REST API hívással szeretné elindítani a feladatátvételt, először az Ön által választott API-ügyféllel kell előállítania az Auth-tokent. A generált hitelesítési token az API-kérelem fejlécében található engedélyezési tulajdonságként van használatban, és kötelező megadni.

A következő kód a meghívni kívánt API URI-t szemlélteti:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

A következő tulajdonságokat kell átadni az API-hívásban:

| **API-tulajdonság** | **Paraméter** |
| --- | --- |
| subscriptionId | Az előfizetés azonosítója, amelyre a felügyelt példány telepítve van |
| resourceGroupName | Felügyelt példányt tartalmazó erőforráscsoport |
| managedInstanceName | Felügyelt példány neve |
| replicaType | Választható (Elsődleges vagy ReadableSecondary). Ezek a paraméterek a feladatátvételi replika típusát jelölik: elsődleges vagy olvasható másodlagos. Ha nincs megadva, a rendszer alapértelmezés szerint az elsődleges replikán kezdeményezi a feladatátvételt. |
| api-verzió | A statikus értéknek és a jelenleginek "2019-06-01-Preview" értékűnek kell lennie |

Az API-válasz az alábbi kettő egyike lesz:

- 202 elfogadva
- Az 400-es kérelem hibáinak egyike.

A műveleti állapot nyomon követhető az API-válaszok áttekintésével a válasz fejlécekben. További információ: [aszinkron Azure-műveletek állapota](../../azure-resource-manager/management/async-operations.md).

## <a name="monitor-the-failover"></a>A feladatátvétel figyelése

A következő T-SQL-lekérdezés végrehajtásával figyelheti a felhasználó által kezdeményezett feladatátvételt a BC-példányon: az SQL felügyelt példányán futtassa az alábbi T-SQL-lekérdezést a kedvenc ügyfelén (például SSMS). A rendszer beolvassa a példányon elérhető rendszernézet sys.dm_hadr_fabric_replica_states és jelentés replikáit. A manuális feladatátvétel megkezdése után frissítse ugyanazt a lekérdezést.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

A feladatátvétel megkezdése előtt a kimenet az alAlwaysOnon rendelkezésre állási csoport egy elsődleges és három formátumú másodlagos zónák tartalmazó jelenlegi elsődleges replikáját fogja jelezni a BC szolgáltatási szinten. A feladatátvétel végrehajtásakor a lekérdezés ismételt futtatásához meg kell adni az elsődleges csomópont változását.

Nem fogja tudni ugyanazt a kimenetet látni a GP szolgáltatási szintjével, mint a BC esetében. Ennek az az oka, hogy a GP szolgáltatási réteg csak egy csomóponton alapul. Alternatív T-SQL-lekérdezést is használhat, amely a GP szolgáltatási réteg példányának csomópontján elindított idő SQL-folyamatát mutatja:

```T-SQL
SELECT sqlserver_start_time, sqlserver_start_time_ms_ticks FROM sys.dm_os_sys_info
```

A feladatátvétel során az ügyféltől való kapcsolat rövid elvesztése, amely általában egy percen belül hosszú ideig tart, a szolgáltatási szintjétől függetlenül a feladatátvétel végrehajtásának jelzése lesz.

> [!NOTE]
> A feladatátvételi folyamat (nem a tényleges rövid elérhetetlenség) befejezése több percet is igénybe vehet, ha **nagy intenzitású** számítási feladatokra van szükség. Ennek az az oka, hogy a példány-motor gondoskodik az elsődleges és a másodlagos csomóponton zajló összes aktuális tranzakcióról a feladatátvétel előtt.

> [!IMPORTANT]
> A felhasználó által kezdeményezett manuális feladatátvétel működési korlátai a következők:
> - **15 percenként** egyetlen (1) feladatátvételt kezdeményeztek ugyanazon a felügyelt példányon.
> - BC-példányok esetében léteznie kell a feladatátvételi kérelem elfogadásához szükséges replikák Kvórumának.
> - BC-példányok esetében nem lehet megadni, hogy melyik olvasható másodlagos replika kezdeményezi a feladatátvételt.
> - A feladatátvétel nem engedélyezett, amíg az új adatbázishoz tartozó első teljes biztonsági mentést az automatizált biztonsági mentési rendszerek végzik.

## <a name="next-steps"></a>Következő lépések

- További információ a felügyelt példány magas rendelkezésre állásáról [Az Azure SQL felügyelt példányain](../database/high-availability-sla.md).
- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
