---
title: 'Rövid útmutató: Kiszolgáló létrehozása – Azure CLI – Azure Database for MySQL'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-adatbázist MySQL-kiszolgálóhoz az Azure CLI használatával az Azure-erőforráscsoportban.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 63c7fe703a1fbf4cb46532085a33efd74e6a76ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875376"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Rövid útmutató: Új Azure Database for MySQL létrehozása az Azure CLI használatával

> [!TIP]
> Érdemes lehet az [egyszerűbb az mysql up](/cli/azure/mysql#az_mysql_up) Azure CLI-parancsot használni (jelenleg előzetes verzióban érhető el). Próbálja ki a [rövid útmutatót.](./quickstart-create-server-up-azure-cli.md)

Ez a rövid útmutató bemutatja, hogyan [](https://shell.azure.com) hozhat létre Azure Cloud Shell azure [cli-parancsokkal](/cli/azure/get-started-with-azure-cli) Azure Database for MySQL kiszolgálót öt perc alatt. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez a rövid útmutatóhoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

 - Válassza ki a fiókjában az adott előfizetést [az az account set paranccsal.](/cli/azure/account) Jegyezze fel  az az **login** kimenet azonosítóértékét,  amely a subscription argumentum értékeként lesz használva a parancsban. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetését az [az account list használatával használhatja.](/cli/azure/account#az_account_list)

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Hozzon létre [egy Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) [az az group create paranccsal,](/cli/azure/group) majd hozza létre a MySQL-kiszolgálót ebben az erőforráscsoportban. Egyedi nevet adjon meg. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Hozzon létre egy Azure Database for MySQL-kiszolgálót az [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) paranccsal. Egy kiszolgáló több adatbázist tartalmazhat.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

A fenti argumentumok részletei a következőek: 

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon egyedi nevet a Azure Database for MySQL kiszolgálónak. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
location | westus | A kiszolgáló Azure-helye.
admin-user | myadmin | A rendszergazda bejelentkezéshez használt felhasználóneve. Nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.
admin-password | *secure password* | A rendszergazda felhasználó jelszava. A jelszó 8–128 karakterből állhat. A jelszónak legalább háromféle karaktert tartalmaznia kell a következő kategóriák közül: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek.
sku-name|GP_Gen5_2|Adja meg a tarifacsomag és a számítási konfiguráció nevét. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. További [információért tekintse meg a](./concepts-pricing-tiers.md) tarifacsomagokat.

>[!IMPORTANT] 
>- A kiszolgálón az alapértelmezett MySQL-verzió az 5.7-es. Jelenleg 5.6-os és 8.0-s verziók is elérhetők.
>- Az **az mysql server create** parancs összes argumentumának megtekintéséhez tekintse meg ezt a [referenciadokumentumot.](/cli/azure/mysql/server#az_mysql_server_create)
>- Az SSL alapértelmezés szerint engedélyezve van a kiszolgálón. Az SSL-lel kapcsolatos további problémákért lásd: [SSL-kapcsolat konfigurálása](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása 
Alapértelmezés szerint a létrehozott új kiszolgáló tűzfalszabályokkal van védve, és nem érhető el nyilvánosan. A tűzfalszabályt az [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule) paranccsal konfigurálhatja a kiszolgálón. Ez lehetővé teszi a helyi csatlakozást a kiszolgálóhoz.

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Cserélje le azt az IP-címet, amelyről csatlakozni fog. Szükség esetén IP-címtartományt is használhat. Nem tudom, hogyan keresse meg az IP-címet, majd keresse fel az [https://whatismyipaddress.com/](https://whatismyipaddress.com/) IP-címét.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja a 3306-os portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
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

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Csatlakozás Azure Database for MySQL kiszolgálóhoz a mysql parancssori ügyféllel
A kiszolgálóhoz egy népszerű ügyféleszköz, **[](https://dev.mysql.com/downloads/)** mysql.exeparancssori eszköz és a [Azure Cloud Shell.](../cloud-shell/overview.md) Másik lehetőségként használhatja a mysql parancssort a helyi környezetben.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) parancsot.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[PHP-alkalmazás összeállítása Windows rendszeren a MySQL használatával](../app-service/tutorial-php-mysql-app.md)