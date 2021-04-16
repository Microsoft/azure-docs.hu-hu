---
title: 'Oktatóanyag: Ismerkedés a Synapse tudásközponttal'
description: Ez az oktatóanyag bemutatja, hogyan használhatja a Synapse tudásközpontot.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/04/2021
ms.openlocfilehash: f4cc631bd3ff05dc63566677ec96ef0360d362c9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517351"
---
# <a name="explore-the-synapse-knowledge-center"></a>Ismerkedés a Synapse Információs központ

Ez az oktatóanyag bemutatja, hogyan használhatja a Synapse Studio Tudásközpontot.

## <a name="finding-to-the-knowledge-center"></a>A Információs központ

A Tudásbázist kétféleképpen lehet Synapse Studio:

  1. A Kezdőlapon, az oldal jobb felső része közelében kattintson a **Learn elemre.**
  2. A felső menüsávon kattintson a **?** majd a **Tudásközpont.**

Válassza ki bármelyik metódust, és nyissa meg **a Tudásközpontot.**

## <a name="exploring-the-knowledge-center"></a>A Információs központ

Amint láthatóvá válik, láthatja, hogy a Információs központ **három** dolgot tesz lehetővé:
* **Azonnal használhatja a mintákat.** Ha szeretne egy gyors példát látni a Synapse működését, válassza ezt a lehetőséget.
* **Tallózhat a katalógusban.** Ez a lehetőség lehetővé teszi mintaadatkészletek csatolását és mintakód hozzáadását SQL-szkriptek, jegyzetfüzetek és folyamatok formájában.
* **Bemutató Synapse Studio.** Ez a lehetőség röviden bevezetőt ad a Synapse Studio. Ez akkor hasznos, ha még soha nem használt Synapse Studio korábban.

## <a name="use-samples-immediately-three-samples-to-help-you-get-started-fast"></a>Minták azonnali használata: Három minta segít a gyors használatban

Ebben a szakaszban három elem található:
* Mintaadatok feltárása a Sparkkal
* Adatok lekérdezése AZ SQL használatával
* Külső tábla létrehozása SQL használatával

1. A **Információs központ** kattintson a **Minták azonnali használata elemre.**
1. Válassza az **Adatok lekérdezése SQL használatával lehetőséget.**
1. Kattintson **a Minta használata elemre.**
1. Megnyílik egy új SQL-példaszkprogram.
1. Görgessen az első lekérdezéshez (28–32. sor), és válassza ki a lekérdezés szövegét.
1. Kattintson a Futtatás elemre. Csak a kiválasztott kódot fogja futtatni.

## <a name="gallery-a-collectiopn-of-sample-data-sets-and-sample-code"></a>Gyűjtemény: Mintaadatkészletek és mintakódok gyűjteménye

1. Lépjen a **Tudásközpontba,** és kattintson **a Katalógus tallózása elemre.**
1. Válassza a **felül található SQL-szkriptek** lapot.
1. Válassza **a New York-i taxicab adatkészlet** betöltéseAdatbetöltési minta lehetőséget, majd kattintson a **Folytatás gombra.**
1. Az **SQL-készlet alatt** válassza a Meglévő készlet **kiválasztása,** majd az **SQLPOOL1** lehetőséget, és válassza ki a korábban létrehozott **SQLPOOL1** adatbázist.
1. Kattintson **a Parancsfájl megnyitása elemre.**
1. Megnyílik egy új SQL-példaszkprogram.
1. Kattintson a **Futtatás gombra**
1. Ez több táblát hoz létre az NYC Taxi összes adata számára, és betölti őket a T-SQL COPY paranccsal. Ha ezeket a táblákat az előző első lépésekben hozta létre, válassza ki és hajtsa végre a create és a COPY kódot a nem létező táblákhoz.

    > [!NOTE] 
    > Ha a mintatagalériát sql-szkripthez dedikált SQL-készlethez (korábban SQL DW) használja, csak meglévő dedikált SQL-készletet (korábban SQL DW) használhat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Rendszergazda hozzáadása](get-started-add-admin.md)

