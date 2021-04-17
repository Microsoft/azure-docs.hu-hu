---
title: Meglévő dedikált SQL-készlet visszaállítása
description: Útmutató meglévő dedikált SQL-készlet visszaállításához.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f2fb6f809794781559683907a806e6d30ca9bed6
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567978"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Meglévő dedikált SQL-készlet visszaállítása

Ebből a cikkből megtudhatja, hogyan lehet visszaállítani egy meglévő dedikált SQL-készletet a Azure Synapse Analytics a Azure Portal és Synapse Studio. Ez a cikk a visszaállításra és a georedens visszaállításra egyaránt vonatkozik. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Meglévő dedikált SQL-készlet visszaállítása a Synapse Studio

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Lépjen a Synapse-munkaterületre. 
3. A Getting Started -> Open Synapse Studio (Első lépések – első lépések) alatt válassza Synapse Studio **Open (Megnyitás) lehetőséget.**

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. A bal oldali navigációs panelen válassza az Adatok **lehetőséget.**
5. Válassza **a Készletek kezelése lehetőséget.** 
6. Új **dedikált SQL-készlet** létrehozásához válassza az + Új lehetőséget. 
7. A További beállítások lapon válassza ki azt a visszaállítási pontot, amelyről visszaállítást fog tudni eszközre állítani. 

    Ha földrajzi visszaállítást szeretne végezni, válassza ki a helyreállítani kívánt munkaterületet és dedikált SQL-készletet. 

8. Válassza az **Automatikus visszaállítási pontok vagy** a Felhasználó által megadott **visszaállítási pontok lehetőséget.** 

    ![Visszaállítási pontok](../media/sql-pools/restore-point.PNG)

    Ha a dedikált SQL-készlet nem rendelkezik automatikus visszaállítási pontokkal, várjon néhány órát, vagy hozzon létre egy felhasználó által megadott visszaállítási pontot a visszaállítás előtt. A User-Defined pont létrehozásához válasszon ki egy meglévőt, vagy hozzon létre egy újat.

    Georedmentés visszaállítása esetén egyszerűen válassza ki a forrás régióban található munkaterületet és a visszaállítani kívánt dedikált SQL-készletet. 

9. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Meglévő dedikált SQL-készlet visszaállítása a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Keresse meg azt a dedikált SQL-készletet, amelyből vissza szeretné állítani a készletet.
3. Az Áttekintés panel tetején válassza a Visszaállítás **lehetőséget.**

    ![ Visszaállítás áttekintése](../media/sql-pools/restore-sqlpool-01.png)

4. Válassza az **Automatikus visszaállítási pontok vagy** a Felhasználó által megadott **visszaállítási pontok lehetőséget.** 

    Ha a dedikált SQL-készlet nem rendelkezik automatikus visszaállítási pontokkal, várjon néhány órát, vagy hozzon létre egy felhasználó által meghatározott visszaállítási pontot a visszaállítás előtt. 

    Ha földrajzi visszaállítást szeretne végezni, válassza ki a helyreállítani kívánt munkaterületet és dedikált SQL-készletet. 

5. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Visszaállítási pont létrehozása](sqlpool-create-restore-point.md)
