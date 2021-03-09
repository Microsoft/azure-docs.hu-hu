---
title: 'Gyors útmutató: kapcsolat az Azure CLI használatával – Azure Database for PostgreSQL – rugalmas kiszolgáló'
description: Ez a rövid útmutató számos lehetőséget kínál az Azure CLI-vel való kapcsolódásra Azure Database for PostgreSQL-rugalmas kiszolgálóval.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: f10978107f80e7dea4e6d5ad40c078c55f225c2d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102494788"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Gyors útmutató: az Azure CLI-vel való kapcsolat és lekérdezés Azure Database for PostgreSQL rugalmas kiszolgálóval

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy Azure Database for PostgreSQL rugalmas kiszolgálóhoz az Azure CLI és a ```az postgres flexible-server connect``` parancs használatával. Ez a parancs lehetővé teszi az adatbázis-kiszolgálóval való kapcsolat tesztelését és a lekérdezések futtatását. Az interaktív mód használatával több lekérdezést is futtathat. 

## <a name="prerequisites"></a>Előfeltételek
- Egy Azure-fiók. Ha még nem rendelkezik ilyennel, [kérjen meg egy ingyenes próbaverziót](https://azure.microsoft.com/free/).
- Az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójának (2.20.0 vagy újabb) telepítése
- Bejelentkezés az Azure CLI-vel a ```az login``` paranccsal 
- A paraméterek megőrzésének bekapcsolása a következővel: ```az config param-persist on``` . A paraméter megőrzése a helyi környezet használatát teszi lehetővé anélkül, hogy meg kellene ismételnie a sok argumentumot, például az erőforráscsoportot vagy a helyet.

## <a name="create-an-postgresql-flexible-server"></a>PostgreSQL rugalmas kiszolgáló létrehozása

A létrehozott első dolog egy felügyelt PostgreSQL-kiszolgáló. A [Azure Cloud Shell](https://shell.azure.com/)futtassa a következő parancsfájlt, és jegyezze fel a **kiszolgáló nevét**, a **felhasználónevet** és a  **jelszót** a parancsból.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
További argumentumokat is megadhat ennek a parancsnak a testreszabásához. Tekintse meg az [az postgres flexibilis-Server Create](/cli/azure/postgres/flexible-server?view=azure-cli-latest#az_postgres_flexible_server_create)argumentumot.

## <a name="view-all-the-arguments"></a>Az összes argumentum megtekintése
A parancshoz tartozó összes argumentumot megtekintheti ```--help``` argumentummal. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Adatbázis-kiszolgáló kapcsolatok tesztelése
A paranccsal ellenőrizheti és ellenőrizheti az adatbázishoz való kapcsolódást a fejlesztési környezetből.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Példa** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Ha a művelet sikeres volt, látni fogja a kimenetet.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Ha a kapcsolatok sikertelenek voltak, próbálkozzon a következő megoldásokkal:
- Ellenőrizze, hogy a 5432-es port nyitva van-e az ügyfélszámítógépen.
- Ha a kiszolgáló rendszergazdája felhasználóneve és jelszava helyes
- Ha konfigurálta az ügyfélszámítógép tűzfalszabály-szabályát
- Ha a kiszolgálót VPN-kapcsolaton keresztül konfigurálta a virtuális hálózatban, győződjön meg arról, hogy az ügyfélszámítógép ugyanabban a virtuális hálózatban van.

## <a name="run-single-query"></a>Egyetlen lekérdezés futtatása
A paranccsal egyetlen lekérdezést is végrehajthat az argumentum használatával ```--querytext``` ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Példa** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Az alábbi ábrán látható kimenet jelenik meg:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Példa**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

A **psql** rendszerhéj-felületet a lent látható módon fogja látni:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A kiszolgáló kezelése](./how-to-manage-server-cli.md)
