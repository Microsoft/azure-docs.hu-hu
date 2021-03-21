---
title: Önálló adatbázis létrehozása
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával hozzon létre egyetlen adatbázist a Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/27/2021
ms.openlocfilehash: c0e86cad252da1b5484944c12351d4ee1e39edbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101656303"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Rövid útmutató: Azure SQL Database önálló adatbázis létrehozása

Ebben a rövid útmutatóban [egyetlen adatbázist](single-database-overview.md) hoz létre Azure SQL Database a Azure Portal, egy PowerShell-parancsfájl vagy egy Azure CLI-parancsfájl használatával. Ezután lekérdezheti az adatbázist a Azure Portalban található **query Editor** használatával.



## <a name="prerequisite"></a>Előfeltétel

- Aktív Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

Ez a rövid útmutató egyetlen adatbázist hoz létre a [kiszolgáló nélküli számítási rétegben](serverless-tier-overview.md).

# <a name="portal"></a>[Portál](#tab/azure-portal)

Egyetlen adatbázis létrehozásához a Azure Portal ez a rövid útmutató az Azure SQL-oldalon indul el.

1. Keresse meg az [SQL-telepítési lehetőség kiválasztása](https://portal.azure.com/#create/Microsoft.AzureSQL) lapot.
1. Az **SQL-adatbázisok** területen hagyja üresen az **Erőforrás típusa** beállítást **egyetlen adatbázis** értékre, majd válassza a **Létrehozás** lehetőséget.

   ![Hozzáadás az Azure SQL-hez](./media/single-database-create-quickstart/select-deployment.png)

1. A **SQL Database létrehozása** űrlap **alapok** lapján a **Project Details (projekt részletei**) területen válassza ki a kívánt Azure- **előfizetést**.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, írja be a *MyResourceGroup*, majd kattintson **az OK gombra**.
1. Az **adatbázis neve** mezőbe írja be a következőt: *mySampleDatabase*.
1. A **kiszolgáló** területen válassza az **új létrehozása** lehetőséget, és töltse ki az **új kiszolgáló** űrlapot a következő értékekkel:
   - **Kiszolgáló neve**: adja meg a *portra beállított mysqlserver*, és adjon hozzá néhány karaktert az egyediséghez. Nem lehet pontosan megadni a kiszolgáló nevét, mert a kiszolgáló nevének globálisan egyedinek kell lennie az Azure összes kiszolgálóján, nem csak az előfizetésen belül. Tehát írjon be valamit, például mysqlserver12345, és a portálon megtudhatja, hogy elérhető-e vagy sem.
   - **Kiszolgáló-rendszergazdai bejelentkezés**: írja be az *Azureus* nevet.
   - **Password (jelszó**): adjon meg egy jelszót, amely megfelel a követelményeknek, majd írja be újra a **Jelszó megerősítése** mezőbe.
   - **Hely**: válasszon ki egy helyet a legördülő listából.

   Válassza az **OK** lehetőséget.

1. Ha **nem** értékre szeretné ÁLLÍTANI az **SQL rugalmas** készletet, ne használja.
1. A **Számítás + tárolás** területen válassza az **Adatbázis konfigurálása** lehetőséget.
1. Ez a rövid útmutató kiszolgáló nélküli adatbázist használ, ezért válassza a **kiszolgáló** nélküli lehetőséget, majd kattintson az **alkalmaz** gombra. 

      ![kiszolgáló nélküli adatbázis konfigurálása](./media/single-database-create-quickstart/configure-database.png)

1. Válassza a **Tovább: hálózatkezelés** elemet az oldal alján.

   ![Új SQL Database-adatbázis – alapszintű lap](./media/single-database-create-quickstart/new-sql-database-basics.png)

1. A **hálózat** lapon, a **kapcsolati módszernél** válassza a **nyilvános végpont** lehetőséget.
1. A **Tűzfalszabályok** esetében állítsa az **aktuális ügyfél IP-címének hozzáadása** **Igen értéket**. Hagyja meg, **hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz** a **nem** értékre.
1. Válassza a **Tovább: további beállítások** elemet az oldal alján.

   ![Hálózatkezelés lap](./media/single-database-create-quickstart/networking.png)
  

1. A **További beállítások** lap **adatforrás** szakaszában, a meglévő adatforrások **használatához** válassza a **minta** lehetőséget. Ez létrehoz egy AdventureWorksLT-mintaadatbázis, így a lekérdezés és a kísérlet során bizonyos táblák és lekérdezések nem üresek az üres adatbázisokkal szemben.
1. Opcionálisan engedélyezze [Az Azure Defender for SQL](../database/azure-defender-for-sql.md)-t.
1. Megadhatja a [karbantartási](../database/maintenance-window.md) időszakot is, hogy a tervezett karbantartás a lehető legrövidebb időn belül elvégezze az adatbázisát.
1. A lap alján válassza a **felülvizsgálat + létrehozás** elemet:

   ![További beállítások lap](./media/single-database-create-quickstart/additional-settings.png)

1. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás** gombra.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com](https://shell.azure.com) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd nyomja le az **ENTER** billentyűt a futtatásához.

## <a name="set-parameter-values"></a>Paraméterek értékének beállítása

A következő értékek használatosak az adatbázis és a szükséges erőforrások létrehozásához a következő parancsokban. A kiszolgálónévnek globálisan egyedinek kell lennie az összes Azure-ban, így a kiszolgáló nevének létrehozásához a $RANDOM függvényt kell használni. Cserélje le az IP-címtartomány 0.0.0.0 értékeit az adott környezetnek megfelelően.

```azurecli-interactive
# Set the resource group name and location for your server
resourceGroupName=myResourceGroup
location=eastus

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567!

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
serverName=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name $resourceGroupName --location $location
```

## <a name="create-a-server"></a>A kiszolgáló létrehozása

Hozzon létre egy kiszolgálót az az [SQL Server Create](/cli/azure/sql/server) paranccsal.

```azurecli-interactive
az sql server create \
    --name $serverName \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password
```


## <a name="configure-a-firewall-rule-for-the-server"></a>Tűzfalszabály konfigurálása a kiszolgálóhoz

Hozzon létre egy tűzfalszabály az az [SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) paranccsal.

```azurecli-interactive
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $serverName \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip
```


## <a name="create-a-single-database-with-azure-cli"></a>Önálló adatbázis létrehozása az Azure CLI-vel

Hozzon létre egy adatbázist az az [SQL db Create](/cli/azure/sql/db) paranccsal. A következő kód jön létre


```azurecli-interactive
az sql db create \
    --resource-group $resourceGroupName \
    --server $serverName \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --compute-model Serverless \
    --family Gen5 \
    --capacity 2
```


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Létrehozhat egy erőforráscsoportot, kiszolgálót és önálló adatbázist a Windows PowerShell használatával.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com](https://shell.azure.com) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd nyomja le az **ENTER** billentyűt a futtatásához.

## <a name="set-parameter-values"></a>Paraméterek értékének beállítása

A következő értékek használatosak az adatbázis és a szükséges erőforrások létrehozásához a következő parancsokban. A kiszolgáló nevének globálisan egyedinek kell lennie az összes Azure-ban, hogy a Get-Random parancsmagot használja a kiszolgáló nevének létrehozásához. Cserélje le az IP-címtartomány 0.0.0.0 értékeit az adott környezetnek megfelelően.

```azurepowershell-interactive
   # Set variables for your server and database
   $resourceGroupName = "myResourceGroup"
   $location = "eastus"
   $adminLogin = "azureuser"
   $password = "Azure1234567!"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName
```


## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurepowershell-interactive
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
```


## <a name="create-a-server"></a>A kiszolgáló létrehozása

Hozzon létre egy kiszolgálót a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) parancsmaggal.

```azurepowershell-interactive
  Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server
```

## <a name="create-a-firewall-rule"></a>Tűzfalszabály létrehozása

Hozzon létre egy kiszolgálói tűzfalszabály-szabályt a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) parancsmaggal.

```azurepowershell-interactive
   Write-host "Configuring server firewall rule..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule
```


## <a name="create-a-single-database-with-powershell"></a>Önálló adatbázis létrehozása a PowerShell-lel

Hozzon létre egyetlen adatbázist a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmaggal.

```azurepowershell-interactive
   Write-host "Creating a gen5 2 vCore serverless database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeModel Serverless `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
```

---



## <a name="query-the-database"></a>Az adatbázis lekérdezése

Az adatbázis létrehozása után a Azure Portalban használhatja a **lekérdezési szerkesztőt (előzetes verzió)** az adatbázishoz való kapcsolódáshoz és a lekérdezési adataihoz.

1. A portálon keresse meg és válassza ki az **SQL-adatbázisok** elemet, majd válassza ki az adatbázist a listából.
1. Az adatbázis lapon válassza a bal oldali menüben a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.
1. Adja meg a kiszolgáló-rendszergazdai bejelentkezési adatait, majd kattintson **az OK gombra**.

   ![Bejelentkezés a lekérdezés-szerkesztőbe](./media/single-database-create-quickstart/query-editor-login.png)

1. Adja meg a következő lekérdezést a **lekérdezéstervező** ablaktáblán.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Válassza a **Futtatás** gombot, majd tekintse át a lekérdezési eredményeket az **Eredmények** panelen.

   ![A lekérdezési szerkesztő eredményei](./media/single-database-create-quickstart/query-editor-results.png)

1. A **Lekérdezés-szerkesztő** oldal bezárásához kattintson az **OK gombra** , amikor a rendszer a nem mentett módosítások elvetésére kéri.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoportot, a kiszolgálót és az önálló adatbázist, és folytassa a következő lépésekkel, és megtudhatja, hogyan csatlakozhat az adatbázishoz, és hogyan kérdezheti le azokat különböző módszerekkel.

Ha elkészült ezeknek az erőforrásoknak a használatával, törölheti a létrehozott erőforráscsoportot, amely a kiszolgálót és az önálló adatbázist is törli.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A **myResourceGroup** és az összes erőforrás törlése a Azure Portal használatával:

1. A portálon keresse meg és válassza ki az **erőforráscsoportok** elemet, majd válassza a **myResourceGroup** elemet a listából.
1. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése** elemet.
1. **A írja be az erőforráscsoport nevét** mezőbe írja be a *myResourceGroup* nevet, majd válassza a **Törlés** lehetőséget.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforráscsoport és az összes erőforrás törléséhez futtassa az alábbi Azure CLI-parancsot az erőforráscsoport neve alapján:

```azurecli-interactive
az group delete --name $resourceGroupName
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoport és az összes erőforrás törléséhez futtassa az alábbi PowerShell-parancsmagot az erőforráscsoport neve alapján:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Következő lépések

Az adatbázis [összekötése és lekérdezése](connect-query-content-reference-guide.md) különböző eszközök és nyelvek használatával:
> [!div class="nextstepaction"]
> [Kapcsolódás és lekérdezés az SQL Server Management Studióval](connect-query-ssms.md)
>
> [Kapcsolódás és lekérdezés az Azure Data Studióval](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)