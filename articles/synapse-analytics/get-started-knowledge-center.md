---
title: 'Oktatóanyag: első lépések a szinapszis Tudásközpont megismeréséhez'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a szinapszis tudásbázist.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98209202"
---
# <a name="explore-the-synapse-knowledge-center"></a>A szinapszis Tudásközpont megismerése

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a szinapszis Studio Knowledge centert.

## <a name="getting-to-the-knowledge-center"></a>Ismerkedés a Knowledge centerrel

A Knowledge Center a szinapszis Studióban kétféleképpen megtalálható:

  1. A kezdőlapon az oldal jobb felső sarkában kattintson a **Learn (tanulás**) elemre.
  2. A felső menüsorban kattintson a gombra **?** és a **Knowledge Center**.

Válassza a metódust, és nyissa meg a **tudásbázist**.

## <a name="overview"></a>Áttekintés

A **Tudásközpont** három dolgot tesz lehetővé:
* A **mintákat azonnal használhatja**. Ha a szinapszis működésének gyors példáját szeretné használni, válassza ezt a lehetőséget.
* **Tallózás** a katalógusban. Ez a beállítás lehetővé teszi a mintaadatok-készletek összekapcsolását és a mintakód hozzáadását SQL-parancsfájlok, jegyzetfüzetek és folyamatok formájában.
* A **szinapszis Studio** bemutatása. Ez a lehetőség a szinapszis Studio alaprészének rövid bemutatóját veszi igénybe. Ez akkor hasznos, ha még soha nem használta a szinapszis Studio alkalmazást.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>BLOB Storage felderítése kiszolgáló nélküli SQL-készlettel

1. Lépjen a **Knowledge Center webhelyre**, és kattintson a **minták azonnali használata** elemre.
1. Válassza ki **az SQL-t a lekérdezési adatként**.
1. Kattintson a **minta használata** lehetőségre.
1. Ekkor megnyílik egy új minta SQL-parancsfájl.
1. Görgessen az első lekérdezéshez (28 – 32. sor), és válassza ki a lekérdezés szövegét.
1. Kattintson a Futtatás elemre. Csak a kiválasztott kódot fogja futtatni.

## <a name="loading-more-nyc-taxi-data"></a>A New York-i taxi-adathalmazok betöltése
1. Nyissa meg a **Knowledge centert**, és kattintson a **Tallózás** katalógus lehetőségre.
1. Válassza a felül található **SQL-parancsfájlok** lapot.
1. Válassza **a New York taxik adatkészlet** -adatfeldolgozási minta betöltése lehetőséget, majd kattintson a **Folytatás** gombra.
1. Az **SQL-készlet** területen válassza a **meglévő készlet kiválasztása** lehetőséget, majd a **SQLPOOL1** elemet, és válassza ki a korábban létrehozott **SQLPOOL1** -adatbázist.
1. Kattintson a **parancsfájl megnyitása** lehetőségre.
1. Ekkor megnyílik egy új minta SQL-parancsfájl.
1. Kattintson a **Futtatás** gombra.
1. Ez több táblát hoz létre a New York-i taxik adataihoz, és betölti azokat a T-SQL COPY paranccsal. Ha ezeket a táblákat az előző gyors üzembe helyezési lépések során hozta létre, válassza ki és hajtsa végre a csak a nem létező táblák LÉTREHOZÁSához és MÁSOLÁSához szükséges kódot.

    > [!NOTE] 
    > Ha az SQL-parancsfájlhoz egy dedikált SQL-készlettel (korábban SQL DW) használja a minta-katalógust, csak egy meglévő dedikált SQL-készletet (korábban SQL DW) használhat.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Synapse Analytics első lépései](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)
