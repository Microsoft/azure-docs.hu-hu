---
title: 'Oktatóanyag: Azure Database for MySQL-Azure Resource Manager sablon létrehozása'
description: Ez az oktatóanyag azt ismerteti, hogyan lehet kiépíteni és automatizálni Azure Database for MySQL Server-telepítéseket Azure Resource Manager sablon használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd5fa4e09dd3f73aa7a8f044ded04d504bd2f3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705472"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Oktatóanyag: Azure Database for MySQL-kiszolgáló kiépítése Azure Resource Manager sablon használatával

A [Azure Database for MySQL REST API](/rest/api/mysql/) lehetővé teszi, hogy a DevOps-mérnökök automatizálják és integrálják a felügyelt MySQL-kiszolgálók és adatbázisok üzembe helyezését, konfigurálását és működését az Azure-ban.  Az API lehetővé teszi a MySQL-kiszolgálók és-adatbázisok létrehozását, számbavételét, kezelését és törlését a Azure Database for MySQL szolgáltatásban.

A Azure Resource Manager kihasználja az alapul szolgáló REST API, hogy kinyilvánítsa és beindítsa az üzembe helyezéshez szükséges Azure-erőforrásokat a skálázáshoz, és az infrastruktúrát kód-koncepcióként igazítsa. A sablon felparaméterezi az Azure-erőforrás nevét, az SKU-t, a hálózatot, a tűzfal konfigurációját és a beállításokat, ami lehetővé teszi, hogy egyszerre legyen létrehozva, és többször is használható legyen.  Azure Resource Manager sablonok könnyen létrehozhatók [Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) vagy [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI)használatával. Lehetővé teszik az alkalmazások csomagolását, a szabványosítást és az üzembe helyezés automatizálását, amely integrálható az DevOps CI/CD-folyamatba.  Ha például gyorsan szeretne üzembe helyezni egy webalkalmazást Azure Database for MySQL háttérrel, akkor a GitHub-galériából elvégezheti a végpontok közötti telepítést a gyors üzembe helyezési [sablon](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) használatával.

Ebben az oktatóanyagban Azure Resource Manager sablonnal és más segédprogramokkal megismerheti a következőket:

> [!div class="checklist"]
> * Azure Database for MySQL kiszolgáló létrehozása a VNet szolgáltatás-végponttal Azure Resource Manager sablon használatával
> * Adatbázis létrehozása a [MySQL parancssori eszköz](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) használatával
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Azure Database for MySQL kiszolgáló létrehozása a VNet szolgáltatás-végponttal Azure Resource Manager sablon használatával

A Azure Database for MySQL-kiszolgáló JSON-sablonjának hivatkozását a [Microsoft. DBforMySQL-kiszolgálók](/azure/templates/microsoft.dbformysql/servers) sablonjának hivatkozása alatt érheti el. Az alábbiakban látható a JSON-sablon, amely egy Azure Database for MySQL rendszert futtató új kiszolgáló létrehozásához használható VNet szolgáltatás-végponttal.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
Ebben a kérésben a testre szabható értékek a következők:
+   `name` -Adja meg a MySQL-kiszolgáló nevét (tartománynév nélkül).
+   `location` -Adja meg a MySQL-kiszolgáló érvényes Azure-beli adatközpont-régióját. Például: westus2.
+   `properties/version` -A telepítendő MySQL-kiszolgáló verziószámának meghatározása. Például 5,6 vagy 5,7.
+   `properties/administratorLogin` -A kiszolgáló MySQL-rendszergazdai bejelentkezési azonosítójának megadása. A rendszergazdai bejelentkezési név nem lehet azure_superuser, admin, administrator, root, guest vagy public.
+   `properties/administratorLoginPassword` -Adja meg a fent megadott MySQL-rendszergazda felhasználó jelszavát.
+   `properties/sslEnforcement` -Engedélyezett/letiltott érték megadása a sslEnforcement engedélyezéséhez vagy letiltásához.
+   `storageProfile/storageMB` -A kiszolgáló megabájtban kifejezett maximális kiépített tárolási méretének megadása. Például 5120.
+   `storageProfile/backupRetentionDays` -A biztonsági másolat megőrzési idejének megadásának napja napokban. Például: 7. 
+   `storageProfile/geoRedundantBackup` -A Geo-DR követelményektől függően engedélyezhető/letiltva adható meg.
+   `sku/tier` -Az alapszintű, a GeneralPurpose vagy a MemoryOptimized szint megadása az üzembe helyezéshez.
+   `sku/capacity` -A virtuális mag kapacitásának meghatározása. A lehetséges értékek a következők: 2, 4, 8, 16, 32 vagy 64.
+   `sku/family` -Adja meg a Gen5 a kiszolgáló üzembe helyezéséhez.
+   `sku/name` -TierPrefix_family_capacity megadására. Például B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Tekintse meg a [díjszabási szintek](./concepts-pricing-tiers.md) dokumentációját, hogy megértse az érvényes értékeket régiónként és szintenként.
+   `resources/properties/virtualNetworkSubnetId` -Itt adhatja meg annak az alhálózatnak az Azure-azonosítóját a VNet, ahol az Azure MySQL-kiszolgálót el kell helyezni. 
+   `tags(optional)` – A választható címkék megadása olyan kulcs-érték párok, amelyeket a számlázási erőforrások kategorizálásához kíván használni.

Ha olyan Azure Resource Manager sablont szeretne létrehozni, amely automatizálja Azure Database for MySQL üzemelő példányait a szervezete számára, akkor a javaslat az Azure rövid útmutatójában a GitHub-katalógus mintájának [Azure Resource Manager sablonjában](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) elsőként fog megjelenni, és fel kell építenie rá. 

Ha új Azure Resource Manager sablonokat, és szeretné kipróbálni, az alábbi lépéseket követve kezdheti el:
+   A minta [Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) klónozása vagy letöltése az Azure Gyorsindítás galériából.  
+   Módosítsa a azuredeploy.parameters.jsa értékre, hogy frissítse a paraméterek értékét a preferencia alapján, és mentse a fájlt. 
+   Az Azure MySQL-kiszolgáló létrehozása az Azure CLI használatával az alábbi parancsokkal

Egy böngészőben az Azure Cloud Shell használatával vagy a számítógépen az Azure CLI telepítésével futtathatja az oktatóanyag kódblokkjait.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az deployment group create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése
A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozás a kiszolgálóhoz a mysql használatával
A [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) használatával építsen ki egy kapcsolatot az Azure Database for MySQL-kiszolgálóhoz. Példánkban a parancs a következő:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist
Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist.
```sql
mysql> CREATE DATABASE mysampledb;
```

Futtassa a parancssorban a következő parancsot a kapcsolat az újonnan létrehozott adatbázisra történő átállítására:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban
Most, hogy megtanulta, hogyan csatlakozhat az Azure Database for MySQL-adatbázishoz, végezzünk el néhány alapvető feladatot.

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Hozzunk létre egy táblát leltáradatok tárolásához.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba
Most, hogy már rendelkezünk egy táblával, szúrjunk be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Most két mintaadatsorral rendelkezünk a korábban létrehozott táblában.

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése
Hajtsa végre a következő lekérdezést az adatbázistáblában lévő információk lekéréséhez.
```sql
SELECT * FROM inventory;
```

A táblákban lévő adatokat frissítheti is.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A sor az adatok lekérésekor megfelelően frissül.
```sql
SELECT * FROM inventory;
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok** elemet.

2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.

3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése** elemet.

4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag a következőket mutatta be:
> [!div class="checklist"]
> * Azure Database for MySQL kiszolgáló létrehozása a VNet szolgáltatás-végponttal Azure Resource Manager sablon használatával
> * Adatbázis létrehozása a mysql parancssori eszközzel
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése

> [!div class="nextstepaction"]
> [Alkalmazások csatlakoztatása az Azure Database for MySQL-adatbázishoz](./howto-connection-string.md)