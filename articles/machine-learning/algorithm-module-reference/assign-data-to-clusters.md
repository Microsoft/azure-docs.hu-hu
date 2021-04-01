---
title: 'Az adatfürthöz való hozzárendelés: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az adathozzárendelési modult Azure Machine Learning a fürtözési modell pontozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e618395a2a4a11b4afb311d612cf5d0e27503dc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898855"
---
# <a name="module-assign-data-to-clusters"></a>Modul: az adathozzárendelés a fürtökhöz

Ez a cikk azt ismerteti, hogyan használható az *adathozzárendelés a fürtökhöz* modul Azure Machine learning Designerben. A modul előrejelzéseket készít egy olyan fürtözött modell használatával, amely a *K-means fürtszolgáltatási* algoritmussal lett betanítva.

Az adatok hozzárendelése a fürtökhöz modul olyan adatkészletet ad vissza, amely tartalmazza az egyes új adatpontok lehetséges hozzárendeléseit. 

## <a name="how-to-use-assign-data-to-clusters"></a>Az adathozzárendelés fürthöz való használata
  
1. Azure Machine Learning Designerben keresse meg a korábban betanított fürtszolgáltatási modellt. A következő módszerek egyikével hozhat létre és állíthat be egy fürtszolgáltatási modellt:  
  
    - Konfigurálja a K-azt a fürtözési algoritmust a [k-Meaning-fürtszolgáltatási](k-means-clustering.md) modul használatával, és a modell betanítását egy adatkészlet és a vonat-fürtszolgáltatási modell modul (ez a cikk) használatával.  
  
    - Hozzáadhat egy meglévő betanított fürtszolgáltatási modellt is a munkaterület **mentett modellek** csoportjából.

2. Csatolja a betanított modellt a bal oldali bemeneti porthoz, amely **adatokat rendel a fürtökhöz**.  

3. Új adatkészlet csatlakoztatása bemenetként. 

   Ebben az adatkészletben a címkék megadása nem kötelező. A fürtözés általában egy nem felügyelt tanulási módszer. A kategóriákat nem várható előre megismerni. A bemeneti oszlopoknak azonban meg kell egyezniük a fürtszolgáltatási modell betanításakor használt oszlopokkal, vagy hiba történik.

    > [!TIP]
    > Ha csökkenteni szeretné a tervező számára a fürt előrejelzései között írt oszlopok számát, használja [az adatkészletben az Oszlopok kiválasztása lehetőséget](select-columns-in-dataset.md), majd válassza ki az oszlopok egy részhalmazát. 
    
4. Ha azt szeretné, hogy az eredmények tartalmazzák a teljes bemeneti adatkészletet, ne jelölje be a **Hozzáfűzés vagy a kijelölés jelölőnégyzetet** , ha azt szeretné, hogy a rendszer az eredményeket tartalmazó oszlopot (fürt-hozzárendeléseket) is tartalmazza.
  
    Ha törli a jelölést a jelölőnégyzetből, akkor a rendszer csak az eredményeket adja vissza. Ez a beállítás akkor lehet hasznos, ha egy webszolgáltatás részeként hoz létre előrejelzéseket.
  
5.  A folyamat elküldése.  
  
### <a name="results"></a>Results (Eredmények)

+  Az adatkészlet értékeinek megtekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Megjelenítés** lehetőséget. Vagy válassza ki a modult, és váltson a **kimenetek** lapra a jobb oldali panelen, kattintson a **port kimenetének** hisztogram ikonjára az eredmény megjelenítéséhez.

