---
title: 'Rövid útmutató: Fivetran és dedikált SQL-készlet (korábban SQL DW)'
description: Ismerkedjen meg a Fivetran és a dedikált SQL-készlettel (korábban SQL DW) az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f332c3b0bd53d80d4a8471f53c56ecab611787c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96456378"
---
# <a name="quickstart-fivetran-with-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Rövid útmutató: Fivetran dedikált SQL-készlettel (korábban SQL DW) az Azure szinapszis Analyticsben 

Ez a rövid útmutató azt ismerteti, hogyan állítható be egy új Fivetran-felhasználó egy dedikált SQL-készlettel (korábban SQL DW) való működéshez. A cikk feltételezi, hogy rendelkezik egy meglévő dedikált SQL-készlettel (korábban SQL DW).

## <a name="set-up-a-connection"></a>Kapcsolatok beállítása

1. Keresse meg a dedikált SQL-készlethez (korábban SQL DW) való kapcsolódáshoz használt teljes kiszolgálónevet és adatbázisnevet.
    
    Ha segítségre van szüksége az információk megtalálásához, tekintse [meg a Kapcsolódás a DEDIKÁLT SQL-készlethez (korábbi nevén SQL DW)](sql-data-warehouse-connection-strings.md)című témakört.

2. A telepítővarázsló segítségével válassza ki, hogy közvetlenül vagy SSH-alagúton keresztül kívánja-e csatlakozni az adatbázist.

   Ha úgy dönt, hogy közvetlenül csatlakozik az adatbázishoz, létre kell hoznia egy tűzfalszabály használatát a hozzáférés engedélyezéséhez. Ez a legegyszerűbb és legbiztonságosabb módszer.

   Ha úgy dönt, hogy SSH-alagúton keresztül csatlakozik, a Fivetran egy külön kiszolgálóhoz csatlakozik a hálózaton. A kiszolgáló egy SSH-alagutat biztosít az adatbázishoz. Ezt a módszert kell használnia, ha az adatbázis nem elérhető alhálózatban van egy virtuális hálózaton.

3. Adja hozzá az IP- **52.0.2.4** a kiszolgálói szintű tűzfallal, hogy engedélyezze a bejövő kapcsolatokat a dedikált SQL-készlet (korábban SQL DW) példányához a Fivetran.

   További információ: [kiszolgálói szintű tűzfalszabály létrehozása](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Felhasználói hitelesítő adatok beállítása

1. Kapcsolódjon a dedikált SQL-készlethez (korábbi nevén SQL DW) a SQL Server Management Studio (SSMS) vagy az Ön által előnyben részesített eszköz használatával. Jelentkezzen be kiszolgálói rendszergazda felhasználóként. Ezután futtassa a következő SQL-parancsokat a Fivetran felhasználó létrehozásához:

    - A Master adatbázisban: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - A dedikált SQL-készlet (korábban SQL DW) adatbázisban:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Adja meg a Fivetran felhasználónak a következő engedélyeket a dedikált SQL-készlethez (korábban SQL DW):

    ```sql
    GRANT CONTROL to fivetran;
    ```

    A VEZÉRLÉSi engedély szükséges ahhoz, hogy adatbázis-hatókörű hitelesítő adatokat hozzon létre, amelyeket akkor használ a rendszer, ha a felhasználó a fájlok Azure Blob Storage-ból való betöltését végzi.

3. Adjon hozzá egy megfelelő erőforrás-osztályt a Fivetran-felhasználóhoz. A használt erőforrás-osztály a oszlopcentrikus index létrehozásához szükséges memóriától függ. Például a Marketo és a Salesforce termékekkel való integrációhoz nagyobb erőforrás-osztályra van szükség, mert a nagy számú oszlop és a termékek által használt nagyobb mennyiségű érték van. A magasabb szintű erőforrásokhoz több memória szükséges a oszlopcentrikus indexek létrehozásához.

    Javasoljuk, hogy használjon statikus erőforrás-osztályokat. A kezdéshez használhatja az `staticrc20` erőforrás osztályt. Az `staticrc20` erőforrás osztály 200 MB-ot foglal le minden felhasználó számára, a használt teljesítményszinttól függetlenül. Ha a oszlopcentrikus indexelése sikertelen a kezdeti erőforrás-osztály szintjén, növelje az erőforrás osztályt.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    További információért olvassa el a [memória-és egyidejűségi korlátokkal](memory-concurrency-limits.md) és az [erőforrás-osztályokkal](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)kapcsolatos tudnivalókat.


## <a name="connect-from-fivetran"></a>Kapcsolat a Fivetran

Ha a Fivetran-fiókból szeretne csatlakozni a dedikált SQL-készlethez (korábban SQL DW), adja meg a dedikált SQL-készlet eléréséhez használt hitelesítő adatokat (korábbi nevén SQL DW): 

* Gazdagép (a kiszolgáló neve).
* Port.
* Adatbázis.
* Felhasználó (a felhasználónévnek **\@ _server_name_ fivetran** kell lennie, ahol a *server_name* az Azure gazdagép URI-ja: **_kiszolgálónév. \__ Database.Windows.net**).
* Jelszó.
