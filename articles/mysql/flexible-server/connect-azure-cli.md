---
title: 'Gyors útmutató: kapcsolat az Azure CLI használatával – Azure Database for MySQL – rugalmas kiszolgáló'
description: Ez a rövid útmutató számos lehetőséget kínál az Azure CLI-vel való kapcsolódásra Azure Database for MySQL-rugalmas kiszolgálóval.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: b28c4457129985a1d5c47d251873eaa52a253f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607968"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Gyors útmutató: az Azure CLI-vel való kapcsolat és lekérdezés Azure Database for MySQL rugalmas kiszolgálóval

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy Azure Database for MySQL rugalmas kiszolgálóhoz az Azure CLI és a ```az mysql flexible-server connect``` parancs használatával. Ez a parancs lehetővé teszi az adatbázis-kiszolgálóval való kapcsolat tesztelését, és a lekérdezések közvetlen futtatását a kiszolgálón.  A parancs futtatása interaktív módban is végezhető több lekérdezés futtatásához.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha még nem rendelkezik ilyennel, [kérjen meg egy ingyenes próbaverziót](https://azure.microsoft.com/free/).
- Az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójának (2.20.0 vagy újabb) telepítése
- Bejelentkezés az Azure CLI-vel a ```az login``` paranccsal 
- A paraméterek megőrzésének bekapcsolása a következővel: ```az config param-persist on``` . A paraméterek megőrzése a helyi környezet használatát teszi lehetővé anélkül, hogy meg kellene ismételnie a sok argumentumot, például az erőforráscsoportot vagy a helyet.

## <a name="create-an-mysql-flexible-server"></a>MySQL rugalmas kiszolgáló létrehozása

Először a felügyelt MySQL-kiszolgálót hozzuk létre. A [Azure Cloud Shell](https://shell.azure.com/)futtassa a következő parancsfájlt, és jegyezze fel a **kiszolgáló nevét**, a **felhasználónevet** és a  **jelszót** a parancsból.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

További argumentumokat is megadhat ennek a parancsnak a testreszabásához. Az [az MySQL flexibilis-Server Create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)összes argumentumának megjelenítése.

## <a name="create-a-database"></a>Adatbázis létrehozása
A következő parancs futtatásával hozzon létre egy adatbázist, **newdatabase** , ha még nem hozott létre egyet.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Az összes argumentum megtekintése
A parancshoz tartozó összes argumentumot megtekintheti ```--help``` argumentummal. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Adatbázis-kiszolgáló kapcsolatok tesztelése
Futtassa a következő szkriptet az adatbázishoz való kapcsolódás teszteléséhez és ellenőrzéséhez a fejlesztői környezetből.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Példa**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

A sikeres csatlakoztatáshoz a következő kimenetnek kell megjelennie:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Ha a kapcsolatok sikertelenek voltak, próbálkozzon a következő megoldásokkal:
- Ellenőrizze, hogy a 3306-es port nyitva van-e az ügyfélszámítógépen.
- Ha a kiszolgáló rendszergazdája felhasználóneve és jelszava helyes
- Ha konfigurálta az ügyfélszámítógép tűzfalszabály-szabályát
- Ha a kiszolgálót VPN-kapcsolaton keresztül konfigurálta a virtuális hálózatban, győződjön meg arról, hogy az ügyfélszámítógép ugyanabban a virtuális hálózatban van.

## <a name="run-single-query"></a>Egyetlen lekérdezés futtatása
Futtassa az alábbi parancsot egyetlen lekérdezés végrehajtásához argumentum használatával ```--querytext``` ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Példa**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Az alábbi ábrán látható kimenet jelenik meg:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Több lekérdezés futtatása interaktív mód használatával
Az **interaktív** mód használatával több lekérdezést is futtathat. Az interaktív mód engedélyezéséhez futtassa a következő parancsot

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Példa**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

A **MySQL** rendszerhéj felületét az alábbi ábrán látható módon fogja látni:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A kiszolgáló kezelése](./how-to-manage-server-cli.md)

