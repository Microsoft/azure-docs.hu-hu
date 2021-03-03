---
title: A Azure SQL Database és az Azure szinapszis Analytics kapcsolódási beállításai
description: Ez a cikk ismerteti a Transport Layer Security (TLS) verzióját, valamint a proxy és az átirányítási beállítások Azure SQL Database és az Azure szinapszis Analytics számára című témakört.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 84fdca96f2ce42c608e7def98f6a3400964cfe46
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691926"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL-kapcsolati beállítások
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ez a cikk a Azure SQL Database és az Azure szinapszis Analytics-kiszolgálóhoz való kapcsolódást szabályozó beállításokat ismerteti. Ezek a beállítások a kiszolgálóhoz társított összes SQL Database és Azure szinapszis Analytics-adatbázisra érvényesek.

> [!IMPORTANT]
> Ez a cikk nem vonatkozik az Azure SQL felügyelt példányaira.

A kapcsolódási beállítások a **tűzfalak és a virtuális hálózatok** képernyőn érhetők el, az alábbi képernyőképen látható módon:

 ![Képernyőfelvétel a kapcsolati beállítások ablakról.][1]

> [!NOTE]
> Ezek a beállítások azonnal érvénybe lépnek az alkalmazása után. Az ügyfelek akkor tapasztalhatnak a kapcsolatok elvesztését, ha nem felelnek meg az egyes beállítások követelményeinek.

## <a name="deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadása

Ha a **nyilvános hálózati hozzáférés megtagadása** **Igen** értékre van állítva, csak a magánhálózati végpontokon keresztüli kapcsolatok engedélyezettek. Ha ez a beállítás **nem** (alapértelmezett), az ügyfelek a [hálózati hozzáférés áttekintése című témakörben](network-access-controls-overview.md)leírtak szerint csatlakozhatnak nyilvános végpontokkal (IP-alapú tűzfalszabályok vagy virtuális hálózatra vonatkozó tűzfalszabályok) vagy privát végpontok (az Azure Private link használatával).

 ![A kapcsolat, ha a nyilvános hálózati hozzáférés megtagadása beállítás értéke Igen, a nyilvános hálózati hozzáférés megtagadása esetén pedig a nem értékre van állítva.][2]

A **nyilvános hálózati hozzáférés megtagadására** tett kísérletek a logikai kiszolgálón meglévő privát végpontok nélkül is **Igen** , a következőhöz hasonló hibaüzenettel meghiúsulnak:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> A virtuális hálózati tűzfalszabályok olyan logikai kiszolgálón való definiálásához, amely már konfigurálva van privát végpontokkal, állítsa a **nem** értékre a **nyilvános hálózati hozzáférés megtagadása** beállítást.

Ha a **nyilvános hálózati hozzáférés megtagadása** **Igen** értékre van állítva, csak a magánhálózati végpontokon keresztüli kapcsolatok engedélyezettek. Az összes nyilvános végponton keresztüli kapcsolat megtagadva a következőhöz hasonló hibaüzenettel:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Ha a **nyilvános hálózati hozzáférés megtagadása** **Igen** értékre van állítva, a tűzfalszabályok hozzáadására vagy frissítésére tett kísérletek a következőhöz hasonló hibaüzenettel lesznek megtagadva:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Nyilvános hálózati hozzáférés módosítása a PowerShell használatával

> [!IMPORTANT]
> A Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de az az. SQL modul jövőbeli fejlesztése is. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-szkript bemutatja, hogyan `Get` és `Set` a **nyilvános hálózati hozzáférés** tulajdonságot a kiszolgáló szintjén:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Nyilvános hálózati hozzáférés módosítása a CLI-n keresztül

> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban

A következő CLI-szkript bemutatja, hogyan változtathatók meg a **nyilvános hálózati hozzáférés** beállítása egy bash-rendszerhéjban:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>Minimális TLS-verzió 

A minimális [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) verzió beállítása lehetővé teszi, hogy az ügyfelek kiválaszthatják, hogy az SQL Database milyen verziójú TLS-verziót használ.

Jelenleg a TLS 1.0-s, 1.1-es és 1.2-es verziója támogatott. Egy minimális TLS-verzió beállítása biztosítja, hogy az újabb TLS-verziók támogatottak legyenek. Például a TLS 1,1-es verziójának kiválasztása azt jelenti, hogy a rendszer csak a TLS 1,1 és 1,2 kapcsolatait fogadja el, és a TLS 1,0-mel való kapcsolat el lett utasítva. Miután megerősítette, hogy az alkalmazások támogatják, ajánlott az 1.2-es minimális TLS-verziót beállítani. Ez a verzió tartalmazza a korábbi verziókban található biztonsági rések javításait, és ez az Azure SQL-adatbázisban támogatott legmagasabb TLS-verzió.

> [!IMPORTANT]
> A minimális TLS-verzió alapértelmezett értéke az összes verzió engedélyezése. A TLS verziójának betartatása után nem lehet visszaállítani az alapértelmezett értéket.

A TLS régebbi verzióit futtató alkalmazásokat használó ügyfelek esetében javasoljuk, hogy az alkalmazások követelményeinek megfelelően állítsa be a TLS minimális verzióját. A nem titkosított kapcsolaton keresztül kapcsolódó alkalmazásokat használó ügyfelek esetében javasoljuk, hogy ne állítson be minimális TLS-verziót.

További információ: TLS- [megfontolások SQL Database kapcsolathoz](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

A minimális TLS-verzió beállítása után a bejelentkezés a következő hiba miatt sikertelen lesz a TLS-t használó ügyfelektől, akik nem a minimális TLS-verziónál alacsonyabbak lesznek:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>A minimális TLS-verzió beállítása a PowerShell használatával

> [!IMPORTANT]
> A Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de az az. SQL modul jövőbeli fejlesztése is. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-szkript bemutatja, hogyan `Get` és `Set` a **minimális TLS-verzió** tulajdonságot a logikai kiszolgáló szintjén:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>A minimális TLS-verzió beállítása az Azure CLI-n keresztül

> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban

A következő CLI-szkript bemutatja, hogyan módosítható a **minimális TLS-verzió** beállítása egy bash-rendszerhéjban:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>A kapcsolatok házirendjének módosítása

A [kapcsolati házirend](connectivity-architecture.md#connection-policy) határozza meg, hogy az ügyfelek hogyan csatlakozzanak Azure SQL Databasehoz.

## <a name="change-the-connection-policy-via-powershell"></a>A kapcsolatbiztonsági szabályzat módosítása a PowerShell használatával

> [!IMPORTANT]
> A Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de az az. SQL modul jövőbeli fejlesztése is. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

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

## <a name="change-the-connection-policy-via-the-azure-cli"></a>A csatlakoztatási szabályzat módosítása az Azure CLI-n keresztül

> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](/cli/azure/install-azure-cli)szükséges.

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

A következő CLI-szkript bemutatja, hogyan lehet módosítani a Windows-parancssorból a kapcsolódási szabályzatot (az Azure CLI telepítve van):

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Következő lépések

- A Azure SQL Database kapcsolatok működésének áttekintését a [kapcsolati architektúrában](connectivity-architecture.md)tekintheti át.
- További információ a kiszolgálók kapcsolódási házirendjének módosításáról: [Conn-Policy](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
