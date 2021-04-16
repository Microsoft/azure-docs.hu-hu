---
title: 'Rövid útmutató: Csatlakozás az Azure CLI-Azure Database for MySQL – Rugalmas kiszolgáló'
description: Ez a rövid útmutató számos lehetőséget kínál az Azure CLI-hez való csatlakozásra Azure Database for MySQL – rugalmas kiszolgálóval.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: e0fd5969a3c4f84b6e8f98e99335bf120179e7af
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481089"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Rövid útmutató: Csatlakozás és lekérdezés az Azure CLI-Azure Database for MySQL – Rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a rövid útmutató azt mutatja be, hogyan csatlakozhat rugalmas Azure Database for MySQL kiszolgálóhoz az Azure CLI és a ```az mysql flexible-server connect``` parancs használatával. Ezzel a paranccsal tesztelheti az adatbázis-kiszolgálóval való kapcsolatot, és lekérdezéseket futtathat közvetlenül a kiszolgálón.  A parancsot interaktív módban is futtathatja több lekérdezés futtatásához.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha még nincs előfizetése, [szerezze be az ingyenes próbaverziót.](https://azure.microsoft.com/free/)
- Az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójának telepítése (2.20.0-s vagy újabb verzió)
- Bejelentkezés az Azure CLI használatával a ```az login``` paranccsal 
- Kapcsolja be a paramétermegőrzést a ```az config param-persist on``` következővel: . A paraméterek megőrzésével anélkül használhatja a helyi környezetet, hogy sok argumentumot, például erőforráscsoportot vagy helyet meg kell ismételnie.

## <a name="create-an-mysql-flexible-server"></a>Rugalmas MySQL-kiszolgáló létrehozása

Először a felügyelt MySQL-kiszolgálót hozzuk létre. A [Azure Cloud Shell](https://shell.azure.com/)futtassa a következő szkriptet, és jegyezze  fel  a parancsból létrehozott kiszolgálónevet, felhasználónevet és jelszót.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

A parancs testreszabásához további argumentumokat is meg lehet adni. Tekintse meg az [az mysql flexible-server create összes argumentumát.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)

## <a name="create-a-database"></a>Adatbázis létrehozása
Futtassa a következő parancsot egy új **adatbázis** létrehozásához, ha még nem hozott létre adatbázist.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Az összes megtekintése az argumentumok
A parancs összes argumentumát megtekintheti a ```--help``` argumentummal. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Adatbázis-kiszolgáló kapcsolatának tesztelése
Futtassa a következő szkriptet a fejlesztési környezetből származó adatbázissal való kapcsolat teszteléséhez és ellenőrzéséhez.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Példa**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

A sikeres csatlakozáshoz a következő kimenetnek kell látsza:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Ha a kapcsolat sikertelen volt, próbálkozzon az alábbi megoldásokkal:
- Ellenőrizze, hogy a 3306-os port nyitva van-e az ügyfélszámítógépen.
- ha a kiszolgáló-rendszergazda felhasználóneve és jelszava helyes
- ha konfigurált tűzfalszabályt az ügyfélszámítógéphez
- Ha privát hozzáféréssel konfigurálta a kiszolgálót a virtuális hálózatban, győződjön meg arról, hogy az ügyfélszámítógép ugyanabban a virtuális hálózatban van.

## <a name="run-single-query"></a>Egyetlen lekérdezés futtatása
Futtassa a következő parancsot egyetlen lekérdezés végrehajtásához a ```--querytext``` argumentum ```-q``` használatával.

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Példa**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

A kimenet az alábbi módon jelenik meg:

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

## <a name="run-multiple-queries-using-interactive-mode"></a>Több lekérdezés futtatása interaktív módban
Az interaktív móddal több **lekérdezést is futtathat.** Az interaktív mód engedélyezéséhez futtassa a következő parancsot

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Példa**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Az alábbiakban látható **módon** megjelenik a MySQL-rendszerhéj:

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
* [Csatlakozás a Azure Database for MySQL – Titkosított kapcsolatokkal rugalmas kiszolgálóhoz](how-to-connect-tls-ssl.md)
* [A kiszolgáló kezelése](./how-to-manage-server-cli.md)

