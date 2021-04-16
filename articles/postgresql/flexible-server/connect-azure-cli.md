---
title: 'Rövid útmutató: Csatlakozás az Azure CLI használatával – Azure Database for PostgreSQL – Rugalmas kiszolgáló'
description: Ez a rövid útmutató számos lehetőséget kínál az Azure CLI-hez való csatlakozásra Azure Database for PostgreSQL – rugalmas kiszolgálóval.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 7526644e02b0ed4d0522ad00a27b691ece98754a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479236"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Rövid útmutató: Csatlakozás és lekérdezés az Azure CLI-Azure Database for PostgreSQL – Rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a rövid útmutató bemutatja, hogyan csatlakozhat rugalmas Azure Database for PostgreSQL kiszolgálóhoz az Azure CLI és a parancs ```az postgres flexible-server connect``` használatával. Ez a parancs lehetővé teszi az adatbázis-kiszolgálóval való kapcsolat tesztelését és lekérdezések futtatását. Az interaktív móddal több lekérdezést is futtathat. 

## <a name="prerequisites"></a>Előfeltételek
- Egy Azure-fiók. Ha még nincs előfizetése, [szerezze be az ingyenes próbaverziót.](https://azure.microsoft.com/free/)
- Az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójának telepítése (2.20.0-s vagy újabb verzió)
- Bejelentkezés az Azure CLI használatával a ```az login``` paranccsal 
- Kapcsolja be a paramétermegőrzést a ```az config param-persist on``` következővel: . A paraméterek megőrzésével anélkül használhatja a helyi környezetet, hogy sok argumentumot, például erőforráscsoportot vagy helyet kell megismételnie.

## <a name="create-an-postgresql-flexible-server"></a>Rugalmas PostgreSQL-kiszolgáló létrehozása

Először is létrehozunk egy felügyelt PostgreSQL-kiszolgálót. A [Azure Cloud Shell](https://shell.azure.com/)futtassa a következő szkriptet, és jegyezze  fel  a parancs által létrehozott kiszolgálónevet, felhasználónevet és jelszót.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
A parancs testreszabásához további argumentumokat is meg lehet adni. Tekintse meg az [az postgres flexible-server create összes argumentumát.](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create)

## <a name="view-all-the-arguments"></a>Az összes megtekintése az argumentumok
A parancs összes argumentumát megtekintheti a ```--help``` argumentummal. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Adatbázis-kiszolgáló kapcsolatának tesztelése
A fejlesztési környezetből az paranccsal tesztelheti és ellenőrizheti az adatbázissal létesített kapcsolatot.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Példa** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Ha a kapcsolat sikeres volt, a kimenetet fogja látni.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Ha a kapcsolat sikertelen volt, próbálkozzon az alábbi megoldásokkal:
- Ellenőrizze, hogy az 5432-es port nyitva van-e az ügyfélszámítógépen.
- ha a kiszolgáló-rendszergazda felhasználóneve és jelszava helyes
- ha konfigurált tűzfalszabályt az ügyfélszámítógéphez
- Ha privát hozzáféréssel konfigurálta a kiszolgálót a virtuális hálózatban, győződjön meg arról, hogy az ügyfélszámítógép ugyanabban a virtuális hálózatban van.

## <a name="run-single-query"></a>Egyetlen lekérdezés futtatása
Egyetlen lekérdezést a paranccsal hajthat végre a ```--querytext``` argumentummal. ```-q```

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Példa** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

A kimenet az alábbi módon jelenik meg:

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

## <a name="run-multiple-queries-using-interactive-mode"></a>Több lekérdezés futtatása interaktív módban
Az interaktív móddal több **lekérdezést is futtathat.** Az interaktív mód engedélyezéséhez futtassa a következő parancsot

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Példa**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Az alábbiakban látható módon megjelenik a **psql** rendszerhéj:

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
