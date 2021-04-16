---
title: 'Rövid útmutató: Számítási feladat osztályozójának létrehozása – Portal'
description: A Azure Portal hozzon létre egy nagy jelentőséggel bíró számítási feladat osztályozóját.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: 6f1997a80e66d1ca7928afc02acf92f376fc3376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534270"
---
# <a name="quickstart-create-a-dedicated-sql-pool-workload-classifier-using-the-azure-portal"></a>Rövid útmutató: Dedikált SQL-készlet számítási feladatosztályozó létrehozása a Azure Portal

Ebben a rövid útmutatóban egy számítási feladat osztályozóját fogja létrehozni [a](sql-data-warehouse-workload-classification.md) lekérdezések számítási feladatcsoporthoz való hozzárendeléséhez.  Az osztályozó az SQL-felhasználótól származó kéréseket `ELTLogin` rendeli hozzá a számítási `DataLoads` feladatcsoporthoz.   Kövesse a [rövid útmutató: Számítási feladatok elkülönítésének](quickstart-configure-workload-isolation-portal.md) konfigurálása oktatóanyagot a számítási `DataLoads` feladatcsoport létrehozásához.  Ez az oktatóanyag egy számítási feladat osztályozóját hozza létre a WLM_LABEL lehetőséggel, amely segít a kérések helyes további besorolásában.  Az osztályozó a számítási feladatok fontosságát is hozzárendeli `HIGH` [](sql-data-warehouse-workload-importance.md) ezekhez a kérelmekhez.


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

> [!NOTE]
> Dedikált SQL-készletpéldány létrehozása a Azure Synapse Analytics új számlázható szolgáltatást eredményezhet.  További információkért lásd: [Azure Synapse Analytics díjszabása.](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy dedikált SQL-készletpéldánysal, amely rendelkezik CONTROL DATABASE engedélyekkel. Ha létre kell hoznia egyet, a Létrehozás és csatlakozás [– portál](create-data-warehouse-portal.md) használatával hozzon létre egy **mySampleDataWarehouse nevű dedikált SQL-készletet.**
<br><br>
Létezik egy számítási `DataLoads` feladatcsoport.  A számítási [feladatok csoportjának létrehozásához lásd:](quickstart-configure-workload-isolation-portal.md) Rövid útmutató: Számítási feladatok elkülönítésének konfigurálása oktatóanyag.
<br><br>
>[!IMPORTANT] 
>A számítási feladatok felügyeletének konfigurálása érdekében a dedikált SQL-készletnek online állapotúnak kell lennie. 


## <a name="create-a-login-for-eltlogin"></a>Bejelentkezés létrehozása az ELTLoginhez

Hozzon létre SQL Server hitelesítő bejelentkezést az adatbázisban `master` a [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) for `ELTLogin` használatával.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Felhasználó létrehozása és engedélyek megadása

A bejelentkezés létrehozása után egy felhasználót kell létrehozni az adatbázisban.  A [CREATE USER használatával](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) hozza létre az SQL-felhasználót a `ELTRole` **mySampleDataWarehouse mappában.**  Mivel az oktatóanyag során tesztelni fogjuk a besorolást, adjon engedélyeket `ELTLogin` a **mySampleDataWarehouse számára.** 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Számítási feladatok besorolásának konfigurálása
A besorolás lehetővé teszi, hogy a kéréseket szabálykészlet alapján egy számítási feladatcsoporthoz irányítsa.  A rövid [útmutató: Számítási feladatok elkülönítésének](quickstart-configure-workload-isolation-portal.md) konfigurálása oktatóanyagban létrehoztuk a számítási `DataLoads` feladatcsoportot.  Most létre fog hozni egy számítási feladat osztályozóját, amely a lekérdezéseket a számítási `DataLoads` feladatcsoporthoz irányíthatja.


1.  Lépjen a **mySampleDataWarehouse dedikált** SQL-készlet lapra.
3.  Válassza a **Számítási feladatok kezelése lehetőséget.**

    ![Kattintson a Menü elemre.](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Válassza **a & osztályozókat** a számítási feladatcsoport jobb `DataLoads` oldalán.

    ![Kattintson a Létrehozás gombra](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Az  **Osztályozók** oszlopban válassza a Nincs konfigurálva lehetőséget.
6. Válassza **a + Osztályozó hozzáadása lehetőséget.**

    ![Kattintson az Add (Hozzáadás) parancsra](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  A `ELTLoginDataLoads` Név alatt adja meg a **nevet.**
8.  Tagként `ELTLogin` adja meg az **et.**
9.  Válassza `High` a Kérelem **fontossága lehetőséget.**  *Nem kötelező,* a normál fontosság az alapértelmezett.
10. A `fact_loads` Label **(Címke) szöveghez** adja meg az 1.
11. Válassza a **Hozzáadás** lehetőséget.
12. Kattintson a **Mentés** gombra.

    ![Kattintson a Config (Konfiguráció) elemre.](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Besorolás ellenőrzése és tesztelése
Ellenőrizze [a sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalógusnézetben az osztályozó `ELTLoginDataLoads` meglétét.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Ellenőrizze az [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalógusnézetben az osztályozó részleteit.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

A besorolás tesztelése során futtassa a következő utasításokat.  Győződjön meg arról, hogy a kapcsolat a következőként van ``ELTLogin`` ``Label`` csatlakoztatva: és a lekérdezésben van használva.
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

Ellenőrizze `CREATE TABLE` a számítási feladatcsoportba besorolt utasítást a számítási feladat `DataLoads` `ELTLoginDataLoads` osztályozója használatával.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban létrehozott számítási feladat `ELTLoginDataLoads` osztályozó törlése:

1. Kattintson az **1 osztályozó** elemre a számítási feladatcsoport `DataLoads` jobb oldalán.

    ![Kattintson a Törlés gombra.](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Kattintson az **Osztályozók elemre.**
3. Kattintson a számítási **`...`** feladat osztályozótól jobbra `ELTLoginDataLoads` gombra.
4. Kattintson a **Törlés gombra.**
5. Kattintson a **Mentés gombra.**

    ![Kattintson a Save (Mentés) gombra.](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Díjat számítunk fel az adattárházegységekért és a dedikált SQL-készletben tárolt adatokért. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha az adatokat a tárolóban szeretné tartani, szüneteltetheti a számítást, ha nem a dedikált SQL-készletet használja. A számítási erőforrások szüneteltetésért csak az adattárolásért kell fizetnie. Ha készen áll az adatokkal való munkára, folytassa a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti a dedikált SQL-készletet.

Kövesse az alábbi lépéseket az erőforrások megtisztítása érdekében.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)majd válassza ki a dedikált SQL-készletet.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéshez válassza a **Szüneteltetés** gombot. Ha a dedikált SQL-készlet fel van függesztve, egy **Indítás gomb látható.**  A számítás folytatásához válassza az Indítás **lehetőséget.**

3. Ha el szeretné távolítani a dedikált SQL-készletet, hogy ne számítson fel díjat a számítási vagy tárolási erőforrásokért, válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

A számítási feladatok monitorozása a Azure Portal metrikák használatával.  A [részletekért lásd: A számítási feladatok kezelése és](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) figyelése.
