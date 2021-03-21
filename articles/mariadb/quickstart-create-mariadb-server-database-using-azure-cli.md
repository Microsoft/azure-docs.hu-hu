---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure CLI – Azure Database for MariaDB'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Database MariaDB-kiszolgálót az Azure CLI használatával egy Azure-erőforráscsoportban.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3279150d0cb7b287f0a78581094a51356033596c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662160"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Gyors útmutató: Azure Database for MariaDB-kiszolgáló létrehozása az Azure CLI használatával

Az Azure CLI használatával a parancssorból vagy szkriptekkel hozhat létre és kezelhet Azure-erőforrásokat. Ez a rövid útmutató bemutatja, hogyan hozhat létre öt perc alatt egy Azure Database for MariaDB-kiszolgálót az Azure CLI használatával egy Azure-erőforráscsoportban.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

Ha több előfizetéssel rendelkezik, válassza ki az erőforrást tartalmazó előfizetés vagy azt az előfizetést, amelyért fizet. A fiókja egy megadott előfizetés-azonosítójának kiválasztásához használja az [az account set](/cli/azure/account#az-account-set) parancsot:

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) az [az group create](/cli/azure/group#az-group-create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása

Hozzon létre egy Azure Database for MariaDB-kiszolgálót az [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) paranccsal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

Beállítás | Mintaérték | Description
---|---|---
name | **mydemoserver** | Adjon meg egy egyedi nevet, amely azonosítja az Azure Database for MariaDB-kiszolgálót. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. A jelszó 3–63 karakterből állhat.
resource-group | **myresourcegroup** | Adja meg az Azure-erőforráscsoport nevét.
sku-name | **GP_Gen5_2** | A termékváltozat neve. Az egyezmény *díjszabási szintjéhez* tartozó \_ *számítási generációs* \_ *virtuális mag* a gyorsírásban követhető. Az **sku-name** paraméterről az alábbi táblázat utáni szakaszban talál további információt.
backup-retention | **7** | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap. Tartomány: 7–35. 
geo-redundant-backup | **Disabled** | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz. Engedélyezett értékek: **engedélyezve**, **Letiltva**.
location | **westus** | A kiszolgáló Azure-helye.
ssl-enforcement | **Engedélyezve** | Azt adja meg, hogy engedélyezni kell-e az SSL-t ehhez a kiszolgálóhoz. Engedélyezett értékek: **engedélyezve**, **Letiltva**.
storage-size | **51 200** | A kiszolgáló tárkapacitása (megabájtban megadva). Az érvényes tárolóméretek: 5120 Mb (legalább), 1024 MB-os egységekben történő növekedéssel. További információ a tárolóméret korlátairól: [Tarifacsomagok](./concepts-pricing-tiers.md). 
version | **10,2** | A MariaDB fő motorjának verziója.
admin-user | **myadmin** | A rendszergazdai bejelentkezés felhasználóneve. Az **admin-user** paraméter nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.
admin-password | *a jelszó* | A rendszergazda felhasználó jelszava. A jelszó 8–128 karakterből állhat. Legalább háromféle karaktert tartalmaznia kell a következő kategóriák közül: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek.

Az sku-name paraméter értéke a {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi, a következő példákban látható módon:
+ `--sku-name B_Gen5_1` az alapszintű, a Gen 5 és az 1 virtuális mag képezi le. Ez a lehetőség az elérhető legkisebb SKU.
+ `--sku-name GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
+ `--sku-name MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

A régiók és csomagok érvényes értékeivel kapcsolatos további információ: [Tarifacsomagok](./concepts-pricing-tiers.md).

A következő példában létrehozunk egy **mydemoserver** nevű kiszolgálót az USA nyugati régiójában. A kiszolgáló a **myresourcegroup** nevű erőforráscsoportban van, és a kiszolgáló-rendszergazdai bejelentkezési név a **myadmin**. A kiszolgáló egy 5. generációs kiszolgáló az Általános célú tarifacsomagban, és 2 virtuális maggal rendelkezik. A kiszolgáló neve egy DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban. Cserélje le a `<server_admin_password>` elemet a saját kiszolgáló-rendszergazdai jelszavára.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O. Vegye figyelembe, hogy az alapszintű díjszabásban létrehozott kiszolgálók később nem méretezhetők át általános célú vagy a memóriára optimalizált értékre. További információért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/mariadb/) .

## <a name="configure-a-firewall-rule"></a>Tűzfalszabály konfigurálása

Hozzon létre egy Azure Database for MariaDB-kiszolgálószintű tűzfalszabályt az [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) paranccsal. A kiszolgálószintű tűzfalszabályok olyan külső alkalmazások használatát teszik lehetővé, mint a mysql parancssori eszköz vagy a MySQL Workbench, amelyekkel kapcsolódhat a kiszolgálóhoz az Azure Database for MariaDB-szolgáltatás tűzfalán keresztül.

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Helyettesítse be a csatlakozási helyének megfelelő IP-címet vagy IP-címtartományt.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Az Azure Database for MariaDB kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-os porton keresztül. Ebben az esetben csak akkor tud csatlakozni a kiszolgálóhoz, ha az informatikai részleg megnyitja a 3306-os portot.

## <a name="configure-ssl-settings"></a>Az SSL-beállítások konfigurálása

Alapértelmezés szerint a kiszolgáló és az ügyfélalkalmazások közti SSL-kapcsolatok kényszerítve vannak. Ez az alapértelmezett beállítás biztosítja a „mozgó” adatok biztonságát az adatstream interneten keresztüli titkosításával. Ehhez a rövid útmutatóhoz tiltsa le az SSL-kapcsolatokat a kiszolgálóján. Ennek az SSL-nek a letiltása éles kiszolgálók esetében nem javasolt. További információ: [Az SSL-kapcsolatok konfigurálása az alkalmazásban az Azure Database for MariaDB-hez való biztonságos kapcsolódásra](./howto-configure-ssl.md).

A következő példa letiltja az SSL-kényszerítést az Azure Database for MariaDB-kiszolgálón:

```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat. A kapcsolati adatok lekéréséhez futtassa a következő parancsot:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a **fullyQualifiedDomainName** és az **administratorLogin** értékeit.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Csatlakozás a kiszolgálóhoz a mysql parancssori eszköz használatával

Csatlakozzon a kiszolgálójához a mysql parancssori eszközzel. A parancssori eszközt [letöltheti](https://dev.mysql.com/downloads/) és telepítheti a számítógépén. A parancssori eszközt a jelen cikkben szereplő egyik kódmintában lévő **Próbálja ki** gombra kattintva is elérheti. A parancssori eszközt úgy is elérheti, hogy az Azure Portal jobb felső sarkában lévő eszköztáron a **>_** gombra kattint az **Azure Cloud Shell** megnyitásához.

Csatlakozás a kiszolgálóhoz a mysql parancssori eszköz használatával:

1. Csatlakozás a kiszolgálóhoz:

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Kiszolgáló állapotának megtekintése a `mysql>` parancssorban:

   ```sql
   status
   ```

   Az alábbihoz hasonló szövegnek kell megjelennie:

   ```cmd
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> További parancsokról a [MySQL 5.7 referencia-kézikönyvének 4.5.1 fejezetében](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) tájékozódhat.

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench használatával

1. Indítsa el a MySQL Workbench eszközt az ügyfélszámítógépen. Ha még nincs telepítve, [töltse le](https://dev.mysql.com/downloads/workbench/) és telepítse az alkalmazást.

2. Az **új kapcsolat létrehozására szolgáló** párbeszédpanelen adja meg a következő információkat a **Parameters** (Paraméterek) lapon:

   ![Új kapcsolat beállítása](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Beállítás | Ajánlott érték | Leírás |
   |---|---|---|
   | Kapcsolat neve | **Bemutató-kapcsolatok** | Adjon meg egy címkét a kapcsolathoz (a kapcsolat neve tetszőlegesen kiválasztható) |
   | Connection Method (Kapcsolati módszer) | **Standard (TCP/IP)** | Csatlakozás az Azure Database for MariaDB-hez a TCP/IP protokollal |
   | Hostname (Gazdanév) | **mydemoserver.mariadb.database.azure.com** | A korábban feljegyzett kiszolgálónév. |
   | Port | **3306** | Az Azure Database for MariaDB alapértelmezett portja. |
   | Felhasználónév | **myadmin \@ mydemoserver** | A korábban feljegyzett kiszolgáló-rendszergazdai bejelentkezési név. |
   | Jelszó | *a jelszó* | A korábban beállított rendszergazdai fiók jelszavát használja. |

3. Válassza a **Test Connection** (Kapcsolat tesztelése) lehetőséget annak teszteléséhez, hogy minden paraméter helyesen lett-e konfigurálva.

4. A kapcsolatra kattintva sikeresen csatlakozhat a kiszolgálóhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az ebben a rövid útmutatóban használt erőforrásokra már nincs szüksége egy másik rövid útmutatóhoz vagy oktatóanyagokhoz, a következő parancs futtatásával törölheti azokat: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az ebben a rövid útmutatóban létrehozott kiszolgálót szeretné törölni, futtassa az [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete) parancsot:

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [MariaDB-adatbázis tervezése az Azure CLI-vel](tutorial-design-database-cli.md)
