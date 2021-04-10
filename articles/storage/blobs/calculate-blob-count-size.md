---
title: Blobok számának és méretének kiszámítása az Azure Storage-leltár használatával
description: Megtudhatja, hogyan számíthatja ki a Blobok számát és teljes méretét tárolóban.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: d1aa91ea0f698e609e786d87a0072e6a07c143a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047317"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Blobok számának és a tárolók teljes méretének kiszámítása az Azure Storage-leltár használatával

Ez a cikk az Azure Blob Storage Inventory szolgáltatást és az Azure Szinapszisot használja a Blobok számának és a Blobok teljes méretének kiszámításához. Ezek az értékek akkor hasznosak, ha a blob-használatot tárolóként optimalizálja.

Ez a metódus nem tartalmazza a blob metaadatait. Az Azure Blob Storage Inventory szolgáltatás a [Blobok listázása](/rest/api/storageservices/list-blobs) REST API alapértelmezett paraméterekkel használja. Így a példa nem támogatja a pillanatképeket, a "$" tárolókat és így tovább.

> [!IMPORTANT]
> A blob-leltár jelenleg **előzetes** verzióban érhető el. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="enable-inventory-reports"></a>Leltározási jelentések engedélyezése

Ennek a módszernek az első lépése a [leltározási jelentések engedélyezése](blob-inventory.md#enable-inventory-reports) a Storage-fiókban. Előfordulhat, hogy az első jelentéshez tartozó leltári jelentések engedélyezése után akár 24 óráig is várnia kell.

Ha van egy leltári jelentés az elemzéshez, adja meg a blob olvasási hozzáférését ahhoz a tárolóhoz, ahol a jelentés CSV-fájlja található.

1. Navigáljon a tárolóhoz a készlet CSV-jelentés fájljával.
1. Válassza a **Access Control (iam)** lehetőséget, majd **adja hozzá a szerepkör-hozzárendeléseket**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Válassza a szerepkör-hozzárendelések hozzáadása lehetőséget":::

1. Válassza ki a **Storage blob Adatolvasót** a **szerepkör** legördülő listából.

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Adja hozzá a Storage blob Adatolvasó szerepkört a legördülő listából":::

1. Adja meg annak a fióknak az e-mail-címét, amelyet a jelentés a **Select** mezőben való futtatásához használ.

## <a name="create-an-azure-synapse-workspace"></a>Azure Synapse-munkaterület létrehozása

Ezután [hozzon létre egy Azure szinapszis-munkaterületet](../../synapse-analytics/get-started-create-workspace.md) , ahol SQL-lekérdezést fog végrehajtani a leltár eredményeinek jelentéséhez.

## <a name="create-the-sql-query"></a>SQL-lekérdezés létrehozása

Miután létrehozta az Azure szinapszis-munkaterületét, hajtsa végre a következő lépéseket.

1. Navigáljon a következőhöz: [https://web.azuresynapse.net](https://web.azuresynapse.net) .
1. Válassza a **fejlesztés** fület a bal oldali szegélyen.
1. Egy elem hozzáadásához kattintson a nagy plusz jelre (+).
1. Válassza az **SQL-szkript** lehetőséget.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Új lekérdezés létrehozásához válassza az SQL-szkriptet":::

## <a name="run-the-sql-query"></a>SQL-lekérdezés futtatása

1. Adja hozzá a következő SQL-lekérdezést az Azure szinapszis-munkaterületen a [készlet CSV-fájljának olvasásához](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file).

    A `bulk` paraméter esetében használja az elemezni kívánt leltári jelentés CSV-fájljának URL-címét.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Nevezze el az SQL-lekérdezést a jobb oldali Tulajdonságok ablaktáblán.

1. Tegye közzé SQL-lekérdezését a CTRL + S billentyűkombináció lenyomásával vagy az **összes közzététele** gomb kiválasztásával.

1. Kattintson a **Futtatás** gombra az SQL-lekérdezés végrehajtásához. A Blobok számát és a tárolók teljes méretét az **eredmények** ablaktábláján lehet jelenteni.

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="A szkript futtatásának kimenete a Blobok számának és a teljes méret kiszámításához.":::

## <a name="next-steps"></a>Következő lépések

- [A blob-adatkészletek kezelése az Azure Storage blob Inventory szolgáltatásával](blob-inventory.md)
- [BLOB-tároló teljes számlázási méretének kiszámítása](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)