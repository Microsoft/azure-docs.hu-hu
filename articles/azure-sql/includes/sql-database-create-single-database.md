---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: sstein
ms.reviewer: vanto
ms.openlocfilehash: 0568860c387aa1239ec56005e404606272ae8275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799865"
---
Ebben a lépésben létrehoz egy logikai [](../database/single-database-overview.md) [SQL-kiszolgálót](../database/logical-servers.md) és egy adatbázist, amely az AdventureWorksLT mintaadatokat használja. Az adatbázist létrehozhatja helyi menük Azure Portal képernyők használatával, vagy egy Azure CLI- vagy PowerShell-szkript használatával a Azure Cloud Shell.

Az összes módszer magában foglalja egy kiszolgálószintű tűzfalszabály beállítását, amely engedélyezi a kiszolgáló eléréséhez használt számítógép nyilvános IP-címét. További információ a kiszolgálószintű tűzfalszabályok létrehozásáról: [Kiszolgálószintű tűzfal létrehozása.](../database/firewall-create-server-level-portal-quickstart.md) Adatbázisszintű tűzfalszabályokat is beállíthatja. Lásd: [Adatbázisszintű tűzfalszabály létrehozása.](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Erőforráscsoport, kiszolgáló és egyetlen adatbázis létrehozása a Azure Portal:

1. Jelentkezzen be a [portálra.](https://portal.azure.com)
1. A keresősávon keresse meg és válassza **a** Azure SQL.
1. A **Azure SQL** válassza a Hozzáadás **lehetőséget.**

   ![Hozzáadás Azure SQL](./media/sql-database-create-single-database/sqldbportal.png)

1. Az **SQL üzembe helyezési lehetőségének kiválasztása lapon** válassza az **SQL-adatbázisok** csempét, az Erőforrástípus alatt pedig az **Egyetlen** adatbázis **lehetőséget.** A különböző adatbázisokról a Részletek megjelenítése lehetőség kiválasztásával **további információkat is megtekinthet.**
1. Válassza a **Létrehozás** lehetőséget.

   ![Önálló adatbázis létrehozása](./media/sql-database-create-single-database/create-single-database.png)

1. Az **SQL-adatbázis** létrehozása  űrlap Alapvető beállítások lapján, a **Projekt részletei** alatt válassza ki a megfelelő Azure-előfizetést, ha még nincs kiválasztva. 
1. Az **Erőforráscsoport alatt** válassza az **Új létrehozása** lehetőséget, írja be a *myResourceGroup* gombra, majd kattintson az **OK gombra.**
1. Az **Adatbázis adatai alatt** az Adatbázis neve alatt adja **meg** a *mySampleDatabase nevet.*
1. A **Kiszolgáló mezőben** válassza **az Új létrehozása** lehetőséget, és töltse ki az Új **kiszolgáló** űrlapot az alábbiak szerint:
   - **Kiszolgáló neve:** Az egyediség érdekében adja meg *a mysqlserver* nevet és néhány karaktert.
   - **Kiszolgáló-rendszergazdai bejelentkezés:** Írja be az *azureuser nevet.*
   - **Jelszó:** Adjon meg egy, a követelményeknek megfelelő jelszót, majd írja be újra a **Jelszó megerősítése** mezőbe.
   - **Hely:** Legördülő menü, és válasszon egy helyet, például **(USA) AZ USA keleti régiója.**

   Válassza az **OK** lehetőséget.

   ![Új kiszolgáló](./media/sql-database-create-single-database/new-server.png)

   Jegyezheti fel a kiszolgáló rendszergazdai bejelentkezési nevét és jelszavát, hogy bejelentkezhet a kiszolgálóra és annak adatbázisaiba. Ha elfelejtette a bejelentkezési nevét vagy jelszavát, az adatbázis létrehozása után le tudja szerezni a bejelentkezési nevet, vagy visszaállíthatja a jelszót az **SQL Server** oldalán. Az **SQL Server oldal megnyitásához** válassza ki a kiszolgáló nevét az adatbázis Áttekintés **lapján.**

1. Ha **újra szeretné konfigurálni** az alapértelmezett beállításokat, a Számítás + tárolás alatt válassza az **Adatbázis konfigurálása lehetőséget.**

   A **Konfigurálás** lapon igény szerint a következőt is használhatja:
   - Módosítsa a **Számítási szintet** **Kiépítve értékről** **Kiszolgáló nélküli szintre.**
   - Tekintse át és  módosítsa a virtuális magok és a **Maximális adatméret beállításait.**
   - A **hardvergenerációt a** Konfiguráció módosítása lehetőséget választva módosíthatja.

   A módosítások után válassza az **Alkalmaz lehetőséget.**

1. Válassza **a Tovább: Hálózat lehetőséget** a lap alján.

   ![Új SQL-adatbázis – Alapszintű lap](./media/sql-database-create-single-database/new-sql-database-basics.png)

1. A Hálózat **lap** **Kapcsolati** módszer csoportjában válassza a Nyilvános **végpont lehetőséget.**
1. A **Tűzfalszabályok alatt** állítsa az **Add current client IP address (Aktuális ügyfél IP-címének hozzáadása) et** **Yes (Igen) állapotra.**
1. Válassza **a Tovább: További beállítások** lehetőséget a lap alján.

   ![Hálózat lap](./media/sql-database-create-single-database/networking.png)
  
   További információ a tűzfalbeállításokról: [Allow Azure services and resources to access this server (Azure-szolgáltatások](../database/network-access-controls-overview.md) és -erőforrások hozzáférésének engedélyezése a kiszolgálóhoz) és [Add a private endpoint (Privát végpont hozzáadása).](../database/private-endpoint-overview.md)

1. A További **beállítások lap** Adatforrás szakaszában **a** Meglévő **adatok** használata területen válassza a Minta **lehetőséget.**
1. Ha szeretné, engedélyezze a [Azure Defender SQL-hez.](../database/azure-defender-for-sql.md)
1. Ha szükséges, állítsa be a karbantartási [ablakot](../database/maintenance-window.md) úgy, hogy a tervezett karbantartás az adatbázishoz leginkább megfelelő időpontban hajtható végre.
1. A **lap alján** válassza az Áttekintés + létrehozás lehetőséget.

   ![További beállítások lap](./media/sql-database-create-single-database/additional-settings.png)

1. A beállítások áttekintése után válassza a **Létrehozás lehetőséget.**

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure parancssori felületével (Azure CLI) létrehozhat egy Azure-erőforráscsoportot, kiszolgálót és egy adatbázist. Ha nem szeretné használni a Azure Cloud Shell telepítse [az Azure CLI-t](/cli/azure/install-azure-cli) a számítógépre.

Az alábbi kódminta futtatásához Azure Cloud Shell próbálja **ki a** kódminta címsorában. A Cloud Shell a kódminta  címsorában válassza a Másolás lehetőséget, és illessze be a mintakódot a Cloud Shell ablakába. A kódban cserélje le a helyére az Azure-előfizetés azonosítóját, a és a helyére pedig a használt számítógép nyilvános `<Subscription ID>` `$startip` `$endip` `0.0.0.0` IP-címét.

A képernyőn megjelenő utasításokat követve jelentkezzen be az Azure-ba, és futtassa a kódot.

A felső sávon Azure Cloud Shell ikon Azure Portal használhatja Cloud Shell ikont.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Amikor először használja a Cloud Shell a portálon, válassza a **Bash lehetőséget** az Üdvözlő **párbeszédpanelen.** A későbbi munkamenetek az Azure CLI-t fogják használni egy Bash-környezetben, vagy választhatja a **Basht** a Cloud Shell vezérlősávon.

Az alábbi Azure CLI-kód létrehoz egy erőforráscsoportot, egy kiszolgálót, egy adatbázist és egy kiszolgálószintű IP-tűzfalszabályt a kiszolgálóhoz való hozzáféréshez. Jegyezheti fel a létrehozott erőforráscsoport- és kiszolgálóneveket, hogy később kezelni tudja ezeket az erőforrásokat.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a server-level firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

A fenti kód ezeket az Azure CLI-parancsokat használja:

| Parancs | Leírás |
|---|---|
| [az account set](/cli/azure/account#az_account_set) | Beállítja, hogy az előfizetés az aktuális aktív előfizetés legyen. |
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | Adatbázisokat és rugalmas készleteket tartalmazó kiszolgálót hoz létre. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_create) | Kiszolgálószintű tűzfalszabályt hoz létre. |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | Létrehoz egy adatbázist. |

További Azure SQL Database Azure CLI-mintákért lásd: [Azure CLI-minták.](../database/az-cli-script-samples-content-guide.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erőforráscsoportot, kiszolgálót és egyetlen adatbázist hozhat létre a Windows PowerShell. Ha nem szeretné használni a Azure Cloud Shell, [telepítse a Azure PowerShell modult.](/powershell/azure/install-az-ps)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következő kódminta futtatásához a Azure Cloud Shell válassza a Próbálja **ki a** kód címsorában lehetőséget. Amikor a Cloud Shell megnyílik,  válassza a Másolás lehetőséget a kódminta címsorában, és illessze be a kódmintát a Cloud Shell ablakába. A kódban cserélje le a helyére az Azure-előfizetés azonosítóját, a és a helyére pedig a használt számítógép nyilvános `<Subscription ID>` `$startIp` `$endIp` `0.0.0.0` IP-címét.

Kövesse a képernyőn megjelenő utasításokat az Azure-ba való bejelentkezéshez és a kód futtatásához.

A felső sávon Azure Cloud Shell Azure Portal ikont választva Cloud Shell is használhatja.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Amikor először használja a Cloud Shell a portálon, válassza a **PowerShell lehetőséget** az Üdvözlő **párbeszédpanelen.** A további munkamenetek a PowerShellt fogják használni, vagy kiválaszthatja azt a Cloud Shell vezérlősávon.

A következő PowerShell-kód létrehoz egy Azure-erőforráscsoportot, egy kiszolgálót, egy adatbázist és egy tűzfalszabályt a kiszolgálóhoz való hozzáféréshez. Jegyezheti fel a létrehozott erőforráscsoport- és kiszolgálóneveket, hogy később kezelni tudja ezeket az erőforrásokat.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

A fenti kód ezeket a PowerShell-parancsmagokat használja:

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy adatbázisokat és rugalmas készleteket tartalmazó kiszolgálót. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Kiszolgálószintű tűzfalszabályt hoz létre egy kiszolgálóhoz. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy adatbázist. |

A PowerShell Azure SQL Database mintákról további információt a [példákat Azure PowerShell meg.](../database/powershell-script-content-guide.md)

---
