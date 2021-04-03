---
title: Meglévő dedikált SQL-készlet visszaállítása
description: Útmutató egy meglévő dedikált SQL-készlet visszaállításához.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94332061"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Meglévő dedikált SQL-készlet visszaállítása

Ebből a cikkből megtudhatja, hogyan állíthatja vissza egy meglévő dedikált SQL-készletet az Azure szinapszis Analyticsben az Azure Portal és a szinapszis Studio használatával. Ez a cikk a visszaállításokra és a Geo-visszaállításokra is vonatkozik. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Meglévő dedikált SQL-készlet visszaállítása a szinapszis Studióval

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Navigáljon a szinapszis munkaterületére. 
3. A Első lépések-> nyissa meg a szinapszis studiót, majd kattintson a **Megnyitás** gombra.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. A bal oldali navigációs **panelen válassza az adatelemet**.
5. Válassza a **készletek kezelése** lehetőséget. 
6. Válassza az **+ új** lehetőséget egy új dedikált SQL-készlet létrehozásához. 
7. A további beállítások lapon válasszon egy visszaállítási pontot a visszaállításhoz. 

    Ha Geo-visszaállítást szeretne végezni, válassza ki a helyreállítani kívánt munkaterületet és dedikált SQL-készletet. 

8. Válassza ki az **Automatikus visszaállítási pontokat** vagy a **felhasználó által definiált visszaállítási pontokat**. 

    ![Visszaállítási pontok](../media/sql-pools/restore-point.PNG)

    Ha a dedikált SQL-készlet nem rendelkezik Automatikus visszaállítási ponttal, várjon néhány órát, vagy hozzon létre egy felhasználó által definiált visszaállítási pontot a visszaállítás előtt. User-Defined visszaállítási pontok esetén válasszon ki egy meglévőt, vagy hozzon létre egy újat.

    Ha a Geo-biztonsági mentést állítja vissza, egyszerűen válassza ki a forrás régióban található munkaterületet és a visszaállítani kívánt dedikált SQL-készletet. 

9. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Meglévő dedikált SQL-készlet visszaállítása a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Navigáljon arra a dedikált SQL-készletre, amelyet vissza szeretne állítani.
3. Az Áttekintés panel tetején válassza a **visszaállítás** lehetőséget.

    ![ Visszaállítás áttekintése](../media/sql-pools/restore-sqlpool-01.png)

4. Válassza ki az **Automatikus visszaállítási pontokat** vagy a **felhasználó által definiált visszaállítási pontokat**. 

    Ha a dedikált SQL-készlet nem rendelkezik Automatikus visszaállítási ponttal, várjon néhány órát, vagy hozzon létre egy felhasználó által definiált visszaállítási pontot a visszaállítás előtt. 

    Ha Geo-visszaállítást szeretne végezni, válassza ki a helyreállítani kívánt munkaterületet és dedikált SQL-készletet. 

5. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Visszaállítási pont létrehozása](sqlpool-create-restore-point.md)
