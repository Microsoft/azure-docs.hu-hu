---
title: 'Rövid útmutató: Számítási erőforrások méretezése Synapse SQL készlethez (Azure Portal)'
description: Az adatkészlet (adattárház) Synapse SQL számítási kapacitását a Azure Portal.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: jrasnick
ms.date: 04/28/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: aa339274a1ff68764fa5573d9c031e84f290e57c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534217"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Rövid útmutató: Számítási erőforrások méretezése Synapse SQL készlethez a Azure Portal

Az adatkészlet (adattárház) Synapse SQL számítási kapacitását a Azure Portal. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

Skálázhat egy már rendelkezik SQL-készletet, vagy használhatja a Létrehozás és csatlakozás – portál gyorsútmutatót egy **mySampleDataWarehouse** nevű SQL-készlet létrehozásához. [](create-data-warehouse-portal.md) A jelen gyors útmutató a **mySampleDataWarehouse** nevű adattárház méretezését mutatja be.

>[!IMPORTANT] 
>A méretezéshez az SQL-készletnek online állapotúnak kell lennie. 

## <a name="scale-compute"></a>Számítások méretezése

Az SQL-készlet számítási erőforrásai az adattárházegységek növelésével vagy csökkentésével skálázhatóak. A [rövid útmutató: létrehozás és csatlakozás – a portál](create-data-warehouse-portal.md) létrehozta a **mySampleDataWarehouse** 400 DWUs-t, és inicializálta azt. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. A **Azure Synapse Analytics bal** oldalán kattintson a Azure Portal.
2. Válassza **ki a mySampleDataWarehouse** Azure Synapse Analytics **(korábban SQL DW)** oldalon. Megnyílik az SQL-készlet.
3. Kattintson **Méretezés** lehetőségre.

    ![Méretezés lehetőség választása](./media/quickstart-scale-compute-portal/click-scale.png)

2. A Méretezés panelen húzza a csúszkát balra vagy jobbra az adattárházegységek (DWU-k) beállításának módosításához. Ezután válassza a skáláz lehetőséget.

    ![Csúszka mozgatása](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Következő lépések
Az SQL-készletről további információt az Adatok betöltése az SQL-készletbe [oktatóanyagban](./load-data-from-azure-blob-storage-using-copy.md) olvashat.
