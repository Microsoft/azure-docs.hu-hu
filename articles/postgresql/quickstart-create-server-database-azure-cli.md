---
title: 'Rövid útmutató: kiszolgáló létrehozása – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló'
description: Ebben a rövid útmutatóban egy Azure Database for PostgreSQL-kiszolgálót fog létrehozni az Azure CLI használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 1d7b6cd6d61be6df66d215613222c12a0f2c9c5e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606711"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Gyors útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](/cli/azure/get-started-with-azure-cli) -parancsokat a [Azure Cloud Shellban](https://shell.azure.com) egy Azure Database for PostgreSQL-kiszolgáló öt perc alatt történő létrehozásához. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

    > [!TIP]
    >  Érdemes lehet az egyszerűbb az [postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-parancs használata, amely jelenleg előzetes verzióban érhető el. Próbálja [ki a rövid](./quickstart-create-server-up-azure-cli.md)útmutatót.

- Válassza ki az adott előfizetés-azonosítót a fiókja alatt az az  [Account set](/cli/azure/account) parancs használatával.

    - Jegyezze fel az **ID** értéket az az **login** kimenetből, hogy az a parancs **előfizetés** argumentumának értékeként használja. 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](/cli/azure/account#az-account-list)lehetőséget.

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) az az [Group Create](/cli/azure/group#az-group-create) paranccsal, majd hozza létre a PostgreSQL-kiszolgálót az erőforráscsoport belsejében. Egyedi nevet adjon meg. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Hozzon létre egy [Azure Database for PostgreSQL-kiszolgálót](overview.md) az az [postgres Server Create](/cli/azure/postgres/server) paranccsal. Egy kiszolgáló több adatbázist tartalmazhat.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Az előző argumentumok részletei: 

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | A Azure Database for PostgreSQL-kiszolgálót azonosító egyedi név. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Az értéknek 3 – 63 karaktert kell tartalmaznia. További információ: [Azure Database for PostgreSQL elnevezési szabályok](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql).
resource-group | myResourceGroup | Az Azure-erőforráscsoport neve.
location | westus | A kiszolgáló Azure-beli helye.
admin-user | myadmin | A rendszergazdai bejelentkezéshez használt Felhasználónév. Nem lehet **azure_superuser**, **Admin**, **Administrator**, **root**, **Guest** vagy **Public**.
admin-password | *secure password* | A rendszergazda felhasználó jelszava. A következő kategóriák közül legalább a 8 – 128 karaktert kell tartalmaznia: angol nagybetűs karakterek, angol kisbetűk, számok és nem alfanumerikus karakterek.
sku-name|GP_Gen5_2| A díjszabási csomag és a számítási konfiguráció neve. Kövesse a {díjszabási csomag} {_számítási generáció}_{virtuális mag} konvenciót a gyorsírásban. További információ: [Azure Database for PostgreSQL díjszabása](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Az alapértelmezett PostgreSQL-verzió a kiszolgálón 9,6. Az összes támogatott verzió megtekintéséhez lásd: [támogatott PostgreSQL főverziók](./concepts-supported-versions.md).
>- Az **az postgres Server Create** parancs összes argumentumának megtekintéséhez tekintse meg [ezt a hivatkozási dokumentumot](/cli/azure/postgres/server#az-postgres-server-create).
>- Az SSL alapértelmezés szerint engedélyezve van a kiszolgálón. További információ az SSL-ről: az [SSL-kapcsolat konfigurálása](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása 
Alapértelmezés szerint a létrehozott kiszolgáló nem érhető el nyilvánosan, és tűzfal-szabályokkal van védve. A tűzfalszabályok konfigurálása a kiszolgálón az az [postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) paranccsal biztosítható, hogy a helyi környezet hozzáférhessen a kiszolgálóhoz való kapcsolódáshoz. 

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Cserélje le azt az IP-címet vagy IP-címtartományt, amely megfelel a helyének, ahová csatlakozni fog. Ha nem ismeri az IP-címét, lépjen a [whatismyipaddress.com](https://whatismyipaddress.com/) elemre a letöltéshez.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> A kapcsolódási problémák elkerülése érdekében győződjön meg arról, hogy a hálózati tűzfal engedélyezi a 5432-es portot. Azure Database for PostgreSQL-kiszolgálók ezt a portot használják. 

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz adja meg a gazdagép adatait és a hozzáférési hitelesítő adatokat.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a **administratorLogin** és a **fullyQualifiedDomainName** értékeket.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Kapcsolódás a Azure Database for PostgreSQL kiszolgálóhoz a psql használatával
A [psql](https://www.postgresql.org/docs/current/static/app-psql.html) -ügyfél népszerű választás a PostgreSQL-kiszolgálókhoz való csatlakozáshoz. A psql [Azure Cloud Shell](../cloud-shell/overview.md)használatával csatlakozhat a kiszolgálóhoz. A psql-t helyi környezetben is használhatja. Egy új PostgreSQL-kiszolgálóval automatikusan létrejön egy üres adatbázis, a **postgres**. Ezt az adatbázist használhatja a psql-hez való kapcsolódáshoz, ahogy az a következő kódban is látható. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Ha inkább URL-elérési utat szeretne használni a postgres-hez való kapcsolódáshoz, az URL-cím kódolja a @ Sign nevet a következővel: `%40` . A psql-hez tartozó kapcsolatok karakterlánca például a következő lesz:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nincs szüksége ezekre az erőforrásokra egy másik rövid útmutatóhoz vagy oktatóanyaghoz, akkor a következő parancs futtatásával törölheti őket. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az az [postgres Server delete](/cli/azure/postgres/server) parancsot.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálás és importálás használatával](./howto-migrate-using-export-and-import.md)
