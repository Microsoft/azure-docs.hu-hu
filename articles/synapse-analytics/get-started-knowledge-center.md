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
ms.date: 04/04/2021
ms.openlocfilehash: 92d1534b5dd0233ce88b81d605c6502b67a121df
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307894"
---
# <a name="explore-the-synapse-knowledge-center"></a>A szinapszis Tudásközpont megismerése

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a szinapszis Studio Knowledge centert.

## <a name="introduction-to-the-knowledge-center"></a>A Knowledge Center bemutatása

A Knowledge Center a szinapszis Studióban kétféleképpen megtalálható:

  1. A kezdőlapon az oldal jobb felső sarkában kattintson a **Learn (tanulás**) elemre.
  2. A felső menüsorban kattintson a gombra **?** és a **Knowledge Center**.

Válassza a metódust, és nyissa meg a **tudásbázist**.

Amint látható, látni fogja, hogy a **Knowledge Center** lehetővé teszi, hogy három dolgot tegyenek:
* A **mintákat azonnal használhatja**. Ha a szinapszis működésének gyors példáját szeretné használni, válassza ezt a lehetőséget.
* **Tallózás** a katalógusban. Ez a beállítás lehetővé teszi a mintaadatok-készletek összekapcsolását és a mintakód hozzáadását SQL-parancsfájlok, jegyzetfüzetek és folyamatok formájában.
* A **szinapszis Studio** bemutatása. Ez a lehetőség a szinapszis Studio alaprészének rövid bemutatóját veszi igénybe. Ez akkor hasznos, ha még soha nem használta a szinapszis Studio alkalmazást.

## <a name="exploring-use-samples-immediately"></a>Vizsgálat: azonnali minták használata

A szakasz három elemet tartalmaz:
* Mintaadatok feltárása a Sparktal
* SQL-alapú lekérdezések
* Külső tábla létrehozása SQL-sel

1. A **Tudásbázisban** kattintson a **minták azonnali használata** elemre.
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

> [!div class="nextstepaction"]
> [Rendszergazda hozzáadása](get-started-add-admin.md)

