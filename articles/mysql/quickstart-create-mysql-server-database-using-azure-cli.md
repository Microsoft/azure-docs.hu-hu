---
title: 'Gyors útmutató: Hozzon létre egy Azure Database for MySQL-kiszolgálóhoz - Azure CLI-vel'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-adatbázist MySQL-kiszolgálóhoz az Azure CLI használatával az Azure-erőforráscsoportban.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: c9944aeae425d3173fa4700caa004097d29d417d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870065"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Gyors útmutató: Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával
Ez a rövid útmutató bemutatja, hogyan hozhat létre öt perc alatt egy Azure-adatbázist MySQL-kiszolgálóhoz az Azure CLI használatával az Azure-erőforráscsoportban. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az-account-set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az [az group create](/cli/azure/group#az-group-create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Hozzon létre egy Azure Database for MySQL-kiszolgálót az **[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create)** paranccsal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
név | mydemoserver | Válasszon egy egyedi nevet, amely azonosítja a MySQL-kiszolgálóhoz készült Azure-adatbázist. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name | GP_Gen4_2 | A termékváltozat neve. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. Az sku-name paraméterről az alábbi táblázatban talál további információt.
backup-retention | 7 | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap. A tartomány 7–35. 
geo-redundant-backup | Letiltva | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz. Megengedett értékek: Engedélyezve, letiltva.
location | westus | A kiszolgáló Azure-helye.
ssl-enforcement | Engedélyezve | Azt adja meg, hogy engedélyezni kell-e az ssl-t ehhez a kiszolgálóhoz. Megengedett értékek: Engedélyezve, letiltva.
storage-size | 51 200 | A kiszolgáló tárkapacitása (megabájtban megadva). Az érvényes storage-size paraméter legkisebb értéke 5120 MB, és 1024 MB-os egységekben növekszik. A tárterület korlátairól a [tarifacsomagokról szóló](./concepts-pricing-tiers.md) dokumentumban találhat további információkat. 
version | 5.7 | A MySQL legújabb főverziója.
admin-user | myadmin | A rendszergazda bejelentkezéshez használt felhasználóneve. Nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.
admin-password | *secure password* | A rendszergazda felhasználó jelszava. A jelszó 8–128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: Angol nagybetűk, angol kisbetűs karakterek, számok és nem alfanumerikus karakterek.


Az sku-name paraméter értéke a {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi, a következő példákban látható módon:
+ `--sku-name B_Gen4_1` Alapszintű, 4. generációs és 1 virtuális mag leképezések. Ez a lehetőség akkor a legkisebb Termékváltozat érhető el.
+ `--sku-name GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
+ `--sku-name MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

A [Tarifacsomagok](./concepts-pricing-tiers.md) dokumentumban megtekintheti az érvényes értékeket régiónként és csomagonként.

A következő példában az USA nyugati régiójában létrehozunk egy `mydemoserver` nevű MySQL 5.7-kiszolgálót a `myresourcegroup` erőforráscsoportban `myadmin` kiszolgálói rendszergazdai bejelentkezéssel. Ez egy **általános célú** **4. generációs** kiszolgáló 2 **virtuális maggal**. A `<server_admin_password>` helyére írja be saját értékét.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```

> [!NOTE]
> Fontolja meg az alapszintű díjcsomag kis számítási és i/o-e megfelelő, a számítási feladatok számára. Vegye figyelembe, hogy a kiszolgálók, az alapszintű tarifacsomagban létrehozott később nem lehet az általános célú és memóriahasználatra optimalizált skálázhatók. Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/mysql/) további információt.
> 

## <a name="configure-firewall-rule"></a>Tűzfalszabály konfigurálása
Hozzon létre egy Azure Database for MySQL-kiszolgáló szintű tűzfalszabályt az **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create)** paranccsal. Egy kiszolgálószintű tűzfalszabály lehetővé teszi olyan külső alkalmazások használatát, mint a **mysql.exe** parancssori eszköz vagy a MySQL Workbench, amelyekkel kapcsolódhat a kiszolgálóhoz az Azure MySQL szolgáltatás tűzfalán keresztül. 

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Helyettesítse be a csatlakozási helyének megfelelő IP-címet vagy IP-címtartományt. 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja a 3306-os portot.
> 


## <a name="configure-ssl-settings"></a>Az SSL-beállítások konfigurálása
Alapértelmezés szerint a kiszolgáló és az ügyfélalkalmazások közti SSL-kapcsolatok kényszerítve vannak. Ez az alapértelmezett beállítás biztosítja a „mozgó” adatok biztonságát az adatfolyam interneten keresztüli titkosításával. A rövid útmutató egyszerűsége érdekében tiltsa le az SSL-kapcsolatokat a kiszolgálóján. Ennek az SSL-nek a letiltása éles kiszolgálók esetében nem javasolt. További információkért lásd [Az SSL-kapcsolatok a MySQL-hez készült Azure Database-hez való kapcsolódásra az alkalmazásban való konfigurálását](./howto-configure-ssl.md) bemutató cikket.

A következő példában letiltjuk az SSL kényszerítését a MySQL-kiszolgálón.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
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
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
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

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Csatlakozás a kiszolgálóhoz a mysql.exe parancssori eszköz használatával
Csatlakozzon kiszolgálójához a **mysql.exe** parancssori eszközzel. A MySQL-t [innen](https://dev.mysql.com/downloads/) töltheti le és telepítheti számítógépén. 

Írja be a következő parancsokat: 

1. Csatlakozás a kiszolgálóhoz a **mysql** parancssori eszköz használatával:
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

2. Kiszolgáló állapotának megtekintése:
```sql
 mysql> status
```
Ha minden megfelelően működik, a parancssori eszköz a következő szöveget jeleníti meg:

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> További parancsokért lásd: [az MySQL 5.7 referenciaútmutatójának 4.5.1-es fejezetét](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench GUI eszköz használatával
1.  Indítsa el a MySQL Workbench alkalmazást az ügyfélszámítógépen. A MySQL Workbench-et [innen](https://dev.mysql.com/downloads/workbench/) töltheti le és telepítheti.

2.  Az **Új kapcsolat létrehozása** párbeszédpanelen adja meg a következő információkat a **Paraméterek** lapon:

   ![új kapcsolat beállítása](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Beállítás** | **Ajánlott érték** | **Leírás** |
|---|---|---|
|   Kapcsolat neve | My Connection | Adjon meg egy címkét a kapcsolathoz (tetszőlegesen kiválasztható) |
| Kapcsolati módszer | válassza a Standard (TCP/IP) lehetőséget | Csatlakozzon a MySQL-hez készült Azure Database-hez a TCP/IP protokollal> |
| Gazdanév | mydemoserver.mysql.database.azure.com | A korábban feljegyzett kiszolgálónév. |
| Port | 3306 | A rendszer a MySQL alapértelmezett portját használja. |
| Felhasználónév | myadmin@mydemoserver | A korábban feljegyzett kiszolgáló-rendszergazdai bejelentkezés. |
| Jelszó | **** | Használja a korábban beállított rendszergazdaifiók-jelszót. |

Kattintson a **Kapcsolat tesztelése** lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva.
Ezután a kapcsolatra való kattintva sikeresen csatlakozhat a kiszolgálóhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az **[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete)** parancsot.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [MySQL-adatbázis tervezése az Azure CLI-vel](./tutorial-design-database-using-cli.md)
