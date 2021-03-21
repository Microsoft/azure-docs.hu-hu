---
title: Kapcsolatok végpontok és űrlap-kapcsolatok karakterláncok beolvasása az ív használatára képes PostgreSQL nagy kapacitású-kiszolgálócsoport számára
titleSuffix: Azure Arc enabled data services
description: Kapcsolatok végpontok és űrlap-kapcsolatok karakterláncok beolvasása az ív használatára képes PostgreSQL nagy kapacitású-kiszolgálócsoport számára
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: de7d23689ae984ea0abece5edb03cf8a0c3a9be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670341"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Kapcsolatok végpontok és űrlap-kapcsolatok karakterláncok beolvasása az ív használatára képes PostgreSQL nagy kapacitású-kiszolgálócsoport számára

Ez a cikk azt ismerteti, hogyan kérhető le a kiszolgálói csoporthoz tartozó kapcsolatok végpontja, és hogyan hozhatja meg az alkalmazásokkal és/vagy eszközökkel használni kívánt kapcsolatok karakterláncait.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Kapcsolatok végpontjának beolvasása:

### <a name="from-cli-with-azdata"></a>A CLI és a azdata használatával
#### <a name="1-connect-to-your-arc-data-controller"></a>1. kapcsolódjon az ív adatvezérlőhöz:
- Ha már van megnyitott munkamenet az ív-adatkezelő gazdagépén, futtassa a következő parancsot:
```console
azdata login
```

- Ha nincs megnyitott munkamenet az ív-adatkezelő gazdagépén, futtassa a következő parancsot: 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. a kapcsolati végpontok megjelenítése
Futtassa a következő parancsot:
```console
azdata arc postgres endpoint list -n <server group name>
```
Például:
```console
azdata arc postgres endpoint list -n postgres01
```

Megjeleníti a végpontok listáját: az alkalmazáshoz való kapcsolódáshoz használt PostgreSQL-végpontot, valamint az adatbázis, a Kibana és a Grafana végpontokat a log Analytics és a monitoring szolgáltatáshoz. Például: 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
A következő végpontok használata:
- A kapcsolati karakterláncok létrehozása és az ügyféleszközök vagy alkalmazások csatlakoztatása
- A Grafana- és Kibana-irányítópultok megnyitása a böngészőben

Használhatja például a _PostgreSQL-példány_ nevű végpontot a psql való kapcsolódáshoz a kiszolgálócsoport számára. Például:
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - A "_PostgreSQL-példány_" nevű végponton megadott _postgres_ -felhasználó jelszava a kiszolgálócsoport telepítésekor választott jelszó.
> - A azdata névjegye: a kapcsolódáshoz társított bérlet 10 órát vesz igénybe. Ezután újra kell csatlakoznia. Ha a bérlet lejárt, a következő hibaüzenet jelenik meg, amikor megpróbál végrehajtani egy parancsot a azdata (a azdata-bejelentkezésen kívül): _hiba: (401)_ 
>  _OK: jogosulatlan_ 
>  _http-válasz fejlécek: HTTPHeaderDict ({"date": "Sun, 06 Sep 2020 16:58:38 GMT", "Content-Length": "0", "www-Authenticate": "_ 
>  _Basic Realm =" bejelentkezési_ hitelesítő adatok szükségesek ", tulajdonosi hiba =" invalid_token ", error_description =" a jogkivonat lejárt "}) _ ha ez történik, újra kell csatlakoznia a azdata a fentiekben leírtak szerint.

## <a name="from-cli-with-kubectl"></a>A CLI és a kubectl használatával
- Ha a kiszolgálócsoport a 12-es verziójú postgres (alapértelmezett), akkor a következő parancs:
```console
kubectl get postgresql-12/<server group name> -n <namespace name>
```
- Ha a kiszolgálócsoport postgres 11-es verziójú, akkor a következő parancsot kell bemutatnia:
```console
kubectl get postgresql-11/<server group name> -n <namespace name>
```

Ezek a parancsok az alábbihoz hasonló kimenetet hoznak létre. Ezt az információt használhatja a kapcsolódási karakterláncok létrehozásához:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Űrlap-kapcsolatok karakterláncai:
Használja az alábbi táblázatot a kapcsolatok karakterláncai közül a kiszolgálócsoport számára. Ezt követően a kívánt módon másolhat/beilleszthet és testreszabhatja őket:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Következő lépések
- További információ a kiszolgálócsoport [kibővítéséről (munkavégző csomópontok hozzáadásáról)](scale-out-postgresql-hyperscale-server-group.md)
- További információ a kiszolgálócsoport [fel-vagy leskálázásáról (a memória/virtuális mag növelése/csökkentése)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)


