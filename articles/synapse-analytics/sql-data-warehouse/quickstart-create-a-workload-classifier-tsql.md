---
title: 'Gyors útmutató: számítási feladatok besorolása – T-SQL'
description: A T-SQL használatával hozzon létre egy nagy fontosságú számítási feladatokat.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677368"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Gyors útmutató: számítási feladatok besorolásának létrehozása T-SQL használatával

Ebben a rövid útmutatóban gyorsan létre fog hozni egy számítási feladatokat, amely nagy fontossággal bír a szervezet VEZÉRIGAZGATÓJA számára. Ez a számítási feladatok besorolása lehetővé teszi, hogy az adatkezelési lekérdezések elsőbbséget élvezzenek más lekérdezésekkel szemben, amelyek kisebb jelentőséggel bírnak a várólistá

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!NOTE]
> Ha egy dedikált SQL Pool-példányt hoz létre az Azure szinapszis Analytics szolgáltatásban, akkor egy új számlázható szolgáltatást eredményezhet.  További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató azt feltételezi, hogy már kiépített egy dedikált SQL-készletet az Azure szinapszis Analyticsben, és hogy rendelkezik az ADATBÁZISra vonatkozó engedélyekkel. Ha létre kell hoznia egyet, a [Létrehozás és összekapcsolás-portál](create-data-warehouse-portal.md) használatával hozzon létre egy **MYSAMPLEDATAWAREHOUSE** nevű dedikált SQL-készletet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>TheCEO-beli bejelentkezés létrehozása

Hozzon létre egy SQL Server hitelesítési bejelentkezést az `master` adatbázisban az "TheCEO" [létrehozási bejelentkezés](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Felhasználó létrehozása

[Felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)"TheCEO", mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Számítási feladatok besorolásának létrehozása

Nagy fontossággal hozza létre a "TheCEO" számítási [feladatok besorolását](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Meglévő osztályozók megtekintése

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Az adatraktár-egységek és a dedikált SQL-készletben tárolt adatmennyiségért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást, ha nem használja a dedikált SQL-készletet. A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Ha készen áll az adatok feldolgozására, folytassa a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti a dedikált SQL-készletet.

Az erőforrások tisztításához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki a dedikált SQL-készletet.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha a dedikált SQL-készlet fel van függesztve, a **Start** gomb jelenik meg.  A számítás folytatásához kattintson a **Start** gombra.

3. Ha el szeretné távolítani a dedikált SQL-készletet, hogy ne kelljen fizetnie a számításért vagy a tárolásért, válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- Ezzel létrehozta a számítási feladatok besorolását. Futtasson néhány lekérdezést a TheCEO, hogy láthassa, hogyan végzik el. A lekérdezéseket és a hozzárendelt fontosságot a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) oldalon tekintheti meg.
- További információ az SQL Pool dedikált számítási feladatainak kezeléséről: a számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md) és a számítási [feladatok besorolása](sql-data-warehouse-workload-classification.md).
- Tekintse meg az útmutatókat a számítási [feladatok fontosságának konfigurálásához](sql-data-warehouse-how-to-configure-workload-importance.md) , valamint a számítási [feladatok felügyeletének kezeléséhez és figyeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
