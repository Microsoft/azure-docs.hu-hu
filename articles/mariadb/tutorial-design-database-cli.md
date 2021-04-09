---
title: 'Oktatóanyag: Azure Database for MariaDB tervezése – Azure CLI'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és kezelhet Azure Database for MariaDB-kiszolgálókat és-adatbázisokat az Azure CLI használatával a parancssorból.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8f6f8d5a2cc9dc17d08486125fc2e44307c1be46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664485"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Oktatóanyag: Azure Database for MariaDB tervezése az Azure CLI használatával

A Azure Database for MariaDB egy, a Microsoft Cloud-on alapuló, a MariaDB Community Edition adatbázismotor alapján működő adatbázis-szolgáltatás. Ebben az oktatóanyagban az Azure CLI (parancssori felület) és egyéb segédprogramok segítségével a következőket sajátíthatja el:

> [!div class="checklist"]
> * Azure Database for MariaDB létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [MySQL parancssori eszköz](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) használatával
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az-account-set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) az [az group create](/cli/azure/group#az-group-create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása
Hozzon létre egy Azure Database for MariaDB-kiszolgálót a `az mariadb server create` paranccsal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

Az alábbi példa egy Azure Database for MariaDB-kiszolgálót hoz létre, amely a (z `westus` ) nevű erőforráscsoport alatt található `myresourcegroup` `mydemoserver` . A kiszolgáló egy `myadmin` nevű rendszergazdai bejelentkezéssel rendelkezik. Ez egy általános célú, Gen 5 kiszolgáló 2 virtuális mag. A `<server_admin_password>` helyére írja be saját értékét.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
Az sku-name paraméter értéke a {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi, a következő példákban látható módon:
+ Az `--sku-name B_Gen5_4` jelentése: Alapszintű, 5. generációs és 4 virtuális mag.
+ `--sku-name GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
+ `--sku-name MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

A [Tarifacsomagok](./concepts-pricing-tiers.md) dokumentumban megtekintheti az érvényes értékeket régiónként és csomagonként.

> [!IMPORTANT]
> A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.


## <a name="configure-firewall-rule"></a>Tűzfalszabály konfigurálása
Hozzon létre egy Azure Database for MariaDB kiszolgáló szintű tűzfalszabály a `az mariadb server firewall-rule create` paranccsal. A kiszolgálói szintű tűzfalszabályok lehetővé teszik egy külső alkalmazás, például a **MySQL** parancssori eszköz vagy a MySQL Workbench számára a kiszolgálóhoz való kapcsolódást az Azure MariaDB szolgáltatás tűzfala segítségével.

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Helyettesítse be a csatlakozási helyének megfelelő IP-címet vagy IP-címtartományt.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
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

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozás a kiszolgálóhoz a mysql használatával
A [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) építsen ki egy kapcsolatot az Azure Database for MariaDB-kiszolgálóhoz. Példánkban a parancs a következő:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist
Ha csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist.
```sql
mysql> CREATE DATABASE mysampledb;
```

Futtassa a parancssorban a következő parancsot a kapcsolat az újonnan létrehozott adatbázisra történő átállítására:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban
Most, hogy már tudja, hogyan csatlakozhat a Azure Database for MariaDB adatbázishoz, hajtson végre néhány alapvető feladatot.

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

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra
Tegyük fel, hogy véletlenül töröltünk egy fontos adatbázistáblát. Ez nem olyasvalami, ami könnyen helyreállítható. Azure Database for MariaDB lehetővé teszi, hogy visszalépjen az elmúlt 35 nap bármely pontjára, és visszaállítsa ezt a pontot egy új kiszolgálóra. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépések a mintakiszolgálót a tábla hozzáadása előtti időpontra állítják vissza.

A visszaállításhoz a rendszer a következő információk megadását kéri:

- Visszaállítási pont: Válasszon ki egy, a kiszolgáló módosítása előtti időpontot. Ez a pont nem lehet korábbi, mint az adatbázis legrégebbi biztonsági másolata.
- Célkiszolgáló: Adja meg az új kiszolgáló nevét, amelyre a biztonsági másolatot vissza kívánja állítani.
- Forráskiszolgáló: Adja meg a kiszolgáló nevét, amelyről a biztonsági másolatot vissza kívánja állítani.
- Hely: Nem választhatja ki a régiót – alapértelmezés szerint ugyanaz lesz, mint a forráskiszolgálóé.

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Az `az mariadb server restore` parancshoz a következő paraméterekre van szükség:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| name | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Válassza ki az időpontot, amelynek az állapotát vissza szeretné állítani. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. ISO8601 dátum- és időformátumot használjon. Használhatja például a saját helyi időzónáját (például `2017-04-13T05:59:00-08:00`), de UTC Zulu formátumot is használhat (`2017-04-13T13:59:00Z`). |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Ha egy adott időpontra állít vissza egy kiszolgálót, azzal létrehoz egy újat, amely az eredeti kiszolgáló adott időpontra jellemző állapotának másolata. A visszaállított kiszolgáló hely- és tarifacsomag-értékei ugyanazok, mint a forráskiszolgálón.

Egy szinkron parancsról van szó, amelyet a rendszer a kiszolgáló visszaállítása után visszaad. Ha a visszaállítás kész, keresse meg a létrehozott új kiszolgálót. Ellenőrizze, hogy az adatok a várt módon álltak-e vissza.

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag a következőket mutatta be:
> [!div class="checklist"]
> * Azure Database for MariaDB-kiszolgáló létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [MySQL parancssori eszköz](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) használatával
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása