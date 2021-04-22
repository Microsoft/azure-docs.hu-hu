---
title: 'Rövid útmutató: Kiszolgáló létrehozása – Azure CLI – Azure Database for PostgreSQL – egyetlen kiszolgáló'
description: Ebben a rövid útmutatóban létre fog hozni egy Azure Database for PostgreSQL-kiszolgálót az Azure CLI használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d4d3b44b0ee878028df369de41451e3dc1d3c6de
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875214"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Rövid útmutató: Azure Database for PostgreSQL létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, [](https://shell.azure.com) hogyan hozhat létre Azure Cloud Shell Azure [CLI-parancsokkal](/cli/azure/get-started-with-azure-cli) Azure Database for PostgreSQL kiszolgálót öt perc alatt. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

    > [!TIP]
    >  Érdemes lehet az [az postgres up](/cli/azure/postgres#az_postgres_up) Azure CLI egyszerűbb, jelenleg előzetes verzióban elérhető parancsát használni. Próbálja ki a [rövid útmutatót.](./quickstart-create-server-up-azure-cli.md)

- Az az account set paranccsal válassza ki a fiókjában az [adott előfizetés-azonosítót.](/cli/azure/account)

    - Jegyezze fel  az az **login** output parancs id értékét,  amely az subscription argumentum értékeként lesz használva a parancsban. 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés lekért listájához használja [az az account list lehetőséget.](/cli/azure/account#az_account_list)

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre [egy Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) [az az group create paranccsal,](/cli/azure/group#az_group_create) majd hozza létre a PostgreSQL-kiszolgálót ebben az erőforráscsoportban. Egyedi nevet adjon meg. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Hozzon [létre Azure Database for PostgreSQL az](overview.md) az [postgres server create paranccsal.](/cli/azure/postgres/server) Egy kiszolgáló több adatbázist tartalmazhat.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Az előző argumentumok részletei a következőek: 

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | A kiszolgálót azonosító Azure Database for PostgreSQL egyedi név. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet. További információ: Azure Database for PostgreSQL [szabályok.](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql)
resource-group | myResourceGroup | Az Azure-erőforráscsoport neve.
location | westus | A kiszolgáló Azure-beli helye.
admin-user | myadmin | A rendszergazdai bejelentkezéshez tartozó felhasználónév. Nem lehet az , **azure_superuser** **,** **rendszergazda,** **gyökér,** **vendég** vagy **nyilvános**.
admin-password | *secure password* | A rendszergazda felhasználó jelszava. A következő kategóriák közül legalább háromból 8–128 karakterből kell állhat: angol nagybetűs karakterek, angol kisbetűs karakterek, számok és nem alfanumerikus karakterek.
sku-name|GP_Gen5_2| A tarifacsomag és a számítási konfiguráció neve. Kövesse röviden a {tarifacsomag}_{számítási generáció}_{virtuális magok} konvenciót. További információ: díjszabás [Azure Database for PostgreSQL.](https://azure.microsoft.com/pricing/details/postgresql/server/)

>[!IMPORTANT] 
>- A kiszolgálón az alapértelmezett PostgreSQL-verzió a 9.6-os. Az összes támogatott verzióért lásd: [Támogatott PostgreSQL-főverziók.](./concepts-supported-versions.md)
>- Az **az postgres server create** parancs összes argumentumának megtekintéséhez tekintse meg ezt a [referenciadokumentumot.](/cli/azure/postgres/server#az_postgres_server_create)
>- Az SSL alapértelmezés szerint engedélyezve van a kiszolgálón. További információ az SSL-ről: [SSL-kapcsolat konfigurálása.](./concepts-ssl-connection-security.md)

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása 
Alapértelmezés szerint a létrehozott kiszolgáló nem érhető el nyilvánosan, és tűzfalszabályokkal van védve. A kiszolgálón a tűzfalszabályokat az [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) paranccsal konfigurálhatja, amely hozzáférést biztosít a helyi környezetnek a kiszolgálóhoz való csatlakozáshoz. 

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Cserélje le azt az IP-címet vagy IP-címtartományt, amely annak felel meg, ahonnan csatlakozni fog. Ha nem ismeri az IP-címét, a WhatIsMyIPAddress.com [meg.](https://whatismyipaddress.com/)


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> A csatlakozási problémák elkerülése érdekében győződjön meg arról, hogy a hálózat tűzfala engedélyezi az 5432-es portot. Azure Database for PostgreSQL kiszolgálók használják ezt a portot. 

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való csatlakozáshoz adja meg a gazdagép adatait és a hozzáférési hitelesítő adatokat.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel az **administratorLogin és** **a fullyQualifiedDomainName értékeket.**

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

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Csatlakozás a Azure Database for PostgreSQL kiszolgálóhoz a psql használatával
A [psql-ügyfél](https://www.postgresql.org/docs/current/static/app-psql.html) népszerű választás a PostgreSQL-kiszolgálókhoz való csatlakozáshoz. A kiszolgálóhoz a psql és a [Azure Cloud Shell.](../cloud-shell/overview.md) A psql-t helyi környezetben is használhatja. A rendszer automatikusan létrehoz egy **üres adatbázist (postgres)** egy új PostgreSQL-kiszolgálóval. Ezzel az adatbázissal csatlakozhat a psql-hez az alábbi kódban látható módon. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Ha inkább URL-címet szeretne használni a Postgreshez való csatlakozáshoz, kódolja a @ jelet a felhasználónévben a `%40` következővel: . A psql kapcsolati sztring például a következő lesz:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha ezekre az erőforrásokra már nincs szüksége egy másik rövid útmutatóhoz vagy oktatóanyaghoz, a következő parancs futtatásával törölheti őket. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtassa az [az postgres server delete](/cli/azure/postgres/server) parancsot.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis áttelepítése exportálással és importálással](./howto-migrate-using-export-and-import.md)
