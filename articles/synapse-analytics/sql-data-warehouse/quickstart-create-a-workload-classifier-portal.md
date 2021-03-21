---
title: 'Gyors útmutató: számítási feladatok besorolása – portál'
description: A Azure Portal használatával nagy jelentőséggel rendelkező számítási feladatok besorolása hozható létre.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 1f4d113f3bc6add67dd34a7ef5e3f8cdc08cecf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677521"
---
# <a name="quickstart-create-a-dedicated-sql-pool-workload-classifier-using-the-azure-portal"></a>Gyors útmutató: dedikált SQL Pool számítási feladatok besorolásának létrehozása a Azure Portal használatával

Ebben a rövid útmutatóban egy számítási feladatot fog [létrehozni, amellyel](sql-data-warehouse-workload-classification.md) lekérdezéseket rendelhet a munkaterhelés-csoportokhoz.  Az osztályozó az SQL-felhasználótól érkező kérelmeket `ELTLogin` a `DataLoads` munkaterhelés csoportjába rendeli.   Kövesse a gyors üzembe helyezési útmutató: a számítási [feladatok elkülönítése](quickstart-configure-workload-isolation-portal.md) oktatóanyagot a `DataLoads` munkaterhelés csoport létrehozásához.  Ez az oktatóanyag az WLM_LABEL lehetőséggel létrehoz egy számítási feladatot, amely segít a kérelmek helyes osztályozásában.  Az osztályozó a számítási `HIGH` [feladatok fontosságát](sql-data-warehouse-workload-importance.md) is hozzárendeli a kérelmekhez.


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

> [!NOTE]
> Ha egy dedikált SQL Pool-példányt hoz létre az Azure szinapszis Analytics szolgáltatásban, akkor egy új számlázható szolgáltatást eredményezhet.  További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy dedikált SQL Pool-példánnyal, amely rendelkezik az adatbázis VEZÉRLÉSéhez szükséges engedélyekkel. Ha létre kell hoznia egyet, a [Létrehozás és összekapcsolás-portál](create-data-warehouse-portal.md) használatával hozzon létre egy **MYSAMPLEDATAWAREHOUSE** nevű dedikált SQL-készletet.
<br><br>
Egy munkaterhelés-csoport `DataLoads` létezik.  A munkaterhelési csoport létrehozásához tekintse meg a következő rövid útmutatót [: a munkaterhelés elkülönítésének konfigurálása](quickstart-configure-workload-isolation-portal.md) oktatóanyag.
<br><br>
>[!IMPORTANT] 
>A számítási feladatok kezelésének konfigurálásához a dedikált SQL-készletnek online állapotban kell lennie. 


## <a name="create-a-login-for-eltlogin"></a>ELTLogin-bejelentkezés létrehozása

Hozzon létre egy SQL Server hitelesítési bejelentkezést az `master` adatbázisban a [létrehozási bejelentkezés](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával `ELTLogin` .

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Felhasználói és engedélyezési engedélyek létrehozása

A bejelentkezés után létre kell hozni egy felhasználót az adatbázisban.  A [felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) paranccsal hozza létre az SQL `ELTRole` -felhasználót a **mySampleDataWarehouse**.  Mivel az oktatóanyag során tesztelni fogjuk a besorolást, engedélyeket adhatunk a `ELTLogin` **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>A számítási feladatok besorolásának konfigurálása
A besorolás lehetővé teszi, hogy a kérelmeket egy adott szabálykészlet alapján átirányítsa egy munkaterhelés-csoportra.  A gyors üzembe helyezési útmutató [: a munkaterhelés elkülönítésének konfigurálása](quickstart-configure-workload-isolation-portal.md) oktatóanyag létrehozta a `DataLoads` munkaterhelés csoportot.  Most létre kell hoznia egy számítási feladat-osztályozó a lekérdezések a munkaterhelés-csoportba való továbbításához `DataLoads` .


1.  Navigáljon a **mySampleDataWarehouse** dedikált SQL-készlet lapjára.
3.  Válassza a **munkaterhelés-kezelés** lehetőséget.

    ![Kattintson a menü](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Válassza a **beállítások & osztályozók** lehetőséget a `DataLoads` munkaterhelés csoport jobb oldalán.

    ![Kattintson a Létrehozás gombra](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Válassza a  **nincs konfigurálva** lehetőséget az osztályozók oszlopban.
6. Válassza az **+ osztályozó hozzáadása** elemet.

    ![Kattintson az Add (Hozzáadás) parancsra](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Adja meg `ELTLoginDataLoads` a **nevet**.
8.  Adja meg `ELTLogin` a **tagot**.
9.  Válassza `High` a **kérés fontosságát**.  Nem *kötelező*, a normál fontosság alapértelmezett.
10. Adja meg `fact_loads` a **címkét**.
11. Válassza a **Hozzáadás** lehetőséget.
12. Kattintson a **Mentés** gombra.

    ![Kattintson a konfiguráció elemre.](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Ellenőrzés és tesztelés besorolása
Az osztályozó létezésének ellenőrzéséhez tekintse meg a [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalógus nézetét `ELTLoginDataLoads` .

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Az osztályozó részleteinek ellenőrzéséhez tekintse meg a [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalógus nézetét.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

A besorolás teszteléséhez futtassa az alábbi utasításokat.  Győződjön meg arról, hogy a ``ELTLogin`` és a ``Label`` lekérdezésben használatban van.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Ellenőrizze a munkaterhelés `CREATE TABLE` csoportba sorolt utasítást a `DataLoads` munkaterhelés- `ELTLoginDataLoads` osztályozó használatával.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az `ELTLoginDataLoads` oktatóanyagban létrehozott számítási feladatok besorolásának törlése:

1. Kattintson az **1. besorolásra** a `DataLoads` munkaterhelés csoport jobb oldalán.

    ![Kattintson a Törlés gombra.](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Kattintson az **osztályozók** elemre.
3. Kattintson a **`...`** munkaterhelés-osztályozó jobb oldalán `ELTLoginDataLoads` .
4. Kattintson a **Törlés** gombra.
5. Kattintson a **Save (Mentés**) gombra.

    ![Kattintson a Save (Mentés) gombra.](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Az adatraktár-egységek és a dedikált SQL-készletben tárolt adatmennyiségért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást, ha nem használja a dedikált SQL-készletet. A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Ha készen áll az adatok feldolgozására, folytassa a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti a dedikált SQL-készletet.

Az erőforrások tisztításához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki a dedikált SQL-készletet.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha a dedikált SQL-készlet fel van függesztve, a **Start** gomb jelenik meg.  A számítás folytatásához kattintson a **Start** gombra.

3. Ha el szeretné távolítani a dedikált SQL-készletet, hogy ne számítsa ki a számítási vagy a tárolási díjat, válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A számítási feladatok monitorozása a Azure Portal figyelési metrikák használatával.  A részletekért lásd: a [munkaterhelés-kezelés kezelése és figyelése](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .
