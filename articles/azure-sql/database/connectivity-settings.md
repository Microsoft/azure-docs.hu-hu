---
title: A Azure SQL Database és az adatraktár csatlakozási beállításai
description: Ez a dokumentum a Azure SQL Database és az Azure szinapszis Analytics Transport Layer Security (TLS) verziójának megválasztását és proxy-és átirányítási beállításait ismerteti.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/06/2020
ms.openlocfilehash: f664ffbfc9aa38dcf8eb7736b28613efb95bde63
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438177"
---
# <a name="azure-sql-connectivity-settings"></a>Az Azure SQL-kapcsolatok beállításai
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ez a cikk a Azure SQL Database és az Azure szinapszis Analytics-kiszolgálóhoz való kapcsolódást szabályozó beállításokat ismerteti. Ezek a beállítások a kiszolgálóhoz társított **összes** SQL Database és Azure szinapszis-adatbázisra érvényesek.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik az **Azure SQL felügyelt példányaira**

A kapcsolódási beállítások a **tűzfalak és a virtuális hálózatok** képernyőn érhetők el, az alábbi képernyőképen látható módon:

 ![A kapcsolati beállítások képernyőképe][1]

> [!NOTE]
> A beállítások alkalmazása után **azonnal érvénybe** lépnek, és az ügyfelek számára a kapcsolatok elvesztését eredményezhetik, ha nem felelnek meg az egyes beállítások követelményeinek.

## <a name="deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadása

Ha a **nyilvános hálózati hozzáférés megtagadása** beállítást az **Igen**értékre állítja, csak a magánhálózati végpontokon keresztüli kapcsolatok engedélyezettek. Ha a beállítás értéke **nem** (alapértelmezett), az ügyfelek csatlakozhatnak nyilvános végpontokkal (az IP-alapú tűzfalszabályok, a VNET-alapú tűzfalszabályok) vagy a privát végpontok (privát hivatkozás használatával), a [hálózati hozzáférés áttekintése című](network-access-controls-overview.md)témakörben leírtak szerint. 

 ![Képernyőfelvétel a nyilvános hálózati hozzáférés megtagadásával létesített kapcsolatról][2]

A **nyilvános hálózati hozzáférés megtagadási** beállításának az **Igen** értékre állítására tett kísérletek a logikai kiszolgálón lévő meglévő privát végpontok nélkül sikertelenek lesznek, és a következőhöz hasonló hibaüzenet jelenik meg:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Ha a **nyilvános hálózati hozzáférés megtagadása** beállítást az **Igen**értékre állítja, csak a magánhálózati végpontokon keresztüli kapcsolatok engedélyezettek, és a nyilvános végpontokon keresztül létesített összes kapcsolat megtagadva a következőhöz hasonló hibaüzenettel:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Ha a **nyilvános hálózati hozzáférés megtagadása** beállítást az **Igen**értékre állítja, a tűzfalszabályok hozzáadására vagy frissítésére tett kísérletek a következőhöz hasonló hibaüzenettel lesznek megtagadva:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Nyilvános hálózati hozzáférés módosítása a PowerShell használatával

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-szkript bemutatja, hogyan `Get` és `Set` a **nyilvános hálózati hozzáférés** tulajdonságot a kiszolgáló szintjén:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled"
```

## <a name="change-public-network-access-via-cli"></a>Nyilvános hálózati hozzáférés módosítása a CLI-n keresztül

> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban

A következő CLI-szkript bemutatja, hogyan változtathatók meg a **nyilvános hálózati hozzáférés** egy bash-rendszerhéjban:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>Minimális TLS-verzió 

A minimális [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) verzió beállítás lehetővé teszi, hogy az ügyfelek szabályozzák a Azure SQL Database által használt TLS-verziót.

Jelenleg a TLS 1,0, 1,1 és 1,2 támogatott. A TLS minimális verziójának beállítása biztosítja, hogy a következő, újabb TLS-verziók támogatottak legyenek. Például a 1,1-nál nagyobb TLS-verzió kiválasztásával. azt jelenti, hogy a rendszer csak a TLS 1,1-es és 1,2-as kapcsolatait fogadja el, és a TLS 1,0 Az alkalmazások támogatásának megerősítését követően javasoljuk, hogy a minimális TLS-verziót 1,2-re állítsa be, mert a korábbi verziókban talált biztonsági rések javításait tartalmazza, és a Azure SQL Database által támogatott TLS legmagasabb verziója.

> [!IMPORTANT]
> A minimális TLS-verzió alapértelmezett értéke az összes verzió engedélyezése. A TLS verziójának betartatása után azonban nem lehet visszaállítani az alapértelmezett értéket.

A TLS régebbi verzióit használó ügyfelek esetében javasoljuk, hogy az alkalmazások követelményeinek megfelelően állítsa be a TLS minimális verziószámát. Azon ügyfelek esetében, akik nem titkosított kapcsolaton keresztül kapcsolódnak az alkalmazásokhoz, javasoljuk, hogy ne állítson be minimális TLS-verziót.

További információ: TLS- [megfontolások SQL Database kapcsolathoz](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

A minimális TLS-verzió beállítása után a bejelentkezés a következő hibával meghiúsul, ha a TLS-t használó ügyfelek nem a minimális TLS-verziót használják:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Minimális TLS-verzió beállítása a PowerShell használatával

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-szkript bemutatja, hogyan `Get` és `Set` a **minimális TLS-verzió** tulajdonságot a logikai kiszolgáló szintjén:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Minimális TLS-verzió beállítása az Azure CLI-n keresztül

> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban

A következő CLI-szkript bemutatja, hogyan módosítható a **minimális TLS-verzió** beállítása egy bash-rendszerhéjban:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>A kapcsolatok házirendjének módosítása

A [kapcsolati házirend](connectivity-architecture.md#connection-policy) határozza meg, hogy az ügyfelek hogyan kapcsolódjanak a Azure SQL Databasehoz.


## <a name="change-connection-policy-via-powershell"></a>A kapcsolatbiztonsági szabályzat módosítása a PowerShell használatával

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-szkript bemutatja, hogyan módosíthatja a kapcsolódási szabályzatot a PowerShell használatával:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>A kapcsolatkérelem-házirend módosítása az Azure CLI-n keresztül

> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban

A következő CLI-szkript bemutatja, hogyan módosíthatja a kapcsolódási szabályzatot egy bash-rendszerhéjban:

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI Windows-parancssorból

A következő CLI-szkript bemutatja, hogyan lehet módosítani a Windows-parancssorból a kapcsolódási szabályzatot (az Azure CLI telepítve van).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Következő lépések

- A Azure SQL Database kapcsolatok működésének áttekintését lásd: [kapcsolati architektúra](connectivity-architecture.md)
- További információ a kiszolgálók kapcsolódási házirendjének módosításáról: [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
