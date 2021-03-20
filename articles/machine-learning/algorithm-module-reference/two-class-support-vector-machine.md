---
title: 'Two-Class támogatási vektoros gép: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható a Azure Machine Learning a Two-Class támogatja a Vector Machine modult a bináris osztályozó létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 46cfdd319fc89e569d165dc2e11303e67c6dd54e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420563"
---
# <a name="two-class-support-vector-machine-module"></a>Two-Class a Vector Machine modul támogatása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal olyan modellt hozhat létre, amely a támogatási vektoros gép algoritmusán alapul. 

A support Vector Machines (SVMs) a felügyelt tanulási módszerek jól kutatott osztálya. Ez az adott implementáció a két lehetséges eredmény előrejelzésére alkalmas, akár folyamatos, akár kategorikus változók alapján.

A modell paramétereinek meghatározása után betanítja a modellt a betanítási modulok használatával, és olyan *címkézett adatkészletet* biztosít, amely címkét vagy eredmény oszlopot tartalmaz.

## <a name="about-support-vector-machines"></a>Tudnivalók a támogatási vektoros gépekről

A támogatott vektoros gépek a legkorábbi a gépi tanulási algoritmusok közé tartoznak, a SVM-modelleket pedig számos alkalmazásban használták, a szöveg-és képbesorolási adatok lekérése alapján. A SVMs a besorolási és a regressziós feladatokhoz is használható.

Ez a SVM-modell egy felügyelt tanulási modell, amelyhez címkézett adat szükséges. A betanítási folyamat során az algoritmus elemzi a bemeneti adatokat, és felismeri a mintákat a *hyperplane* nevű többdimenziós szolgáltatásban.  Az összes bemeneti példa erre a területre mutat, és a kimeneti kategóriákra van leképezve oly módon, hogy a kategóriák a lehető legszélesebb és egyértelműek legyenek.

Az előrejelzéshez a SVM algoritmus új példákat rendel egy kategóriához vagy a másikhoz, és hozzárendeli őket ugyanahhoz a helyhez. 

## <a name="how-to-configure"></a>Konfigurálás 

Ennél a modellnél javasolt az adatkészlet normalizálása, mielőtt az osztályozó betanítására használhatná.
  
1.  Adja hozzá a **kétosztályos támogatás Vector Machine** modult a folyamathoz.  
  
2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek adott készletét argumentumként.  

    -   **Paraméter tartománya**: Ha nem biztos benne, hogy a legjobb paramétereket használja, megkeresheti az optimális paramétereket az [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modullal. Bizonyos értékeket adhat meg, és az oktató a beállítások több kombinációján keresztül megismétli a legjobb eredményt eredményező értékek kombinációját.

3.  Az **Ismétlések számának** megadásához írjon be egy számot, amely a modell létrehozásakor használt ismétlések számát jelöli.  
  
     Ezzel a paraméterrel szabályozhatja a kompromisszumot a betanítási sebesség és a pontosság között.  
  
4.  A **lambda** mezőben adja meg az L1 regularizációs súlyozásához használandó értéket.  
  
     Ez a regularizációs együttható a modell finomhangolására használható. Nagyobb értékek szankcionálják az összetettebb modelleket.  
  
5.  Válassza ki a lehetőséget, **normalizálja a szolgáltatásokat**, ha a betanítás előtt szeretné normalizálni a szolgáltatásokat.
  
     Ha a normalizálás alkalmazása előtt betanítást alkalmaz, az adatpontok középpontba kerülnek és méretezhetők, hogy a szórás egy egysége legyen.
  
6.  Válassza ki a lehetőséget, a **projekt az egység szférában** lehetőségre az együtthatók normalizálása érdekében.
  
     Az adategységek térközének kivetítése azt jelenti, hogy a betanítás előtt az adatpontok 0 és méretezhetők, hogy a szórás egy egysége legyen.
  
7.  A **véletlenszerű számú magok** mezőben adjon meg egy egész számot, amelyet magként kíván használni, ha biztosítani kívánja az egyes futtatások reprodukálhatóságát.  Ellenkező esetben a rendszer egy rendszeróra-értéket használ a magokként, ami némileg eltérő eredményeket eredményezhet a futtatások között.
  
9. Egy címkézett adatkészlet csatlakoztatása és a modell betanítása:

    + Ha az **oktatói módot** **egyetlen paraméterként** állítja be, csatlakoztasson egy címkézett adatkészletet és a [betanítási modell](train-model.md) modult.  
  
    + Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, csatlakoztasson egy címkézett adatkészletet, és a modell betanításához használja a [modell hiperparaméterek beállítása](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az alapértelmezett értéket használja az egyetlen paraméteres listában.  
    > 
    > Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.  
    > 
    > Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.
  
10. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha menteni szeretné a betanított modell pillanatképét, válassza a **kimenetek** fület a **vonat modell** moduljának jobb oldali paneljén. Válassza az **adatkészlet regisztrálása** ikont a modell újrafelhasználható modulként való mentéséhez.

+ A modell pontozáshoz való használatához adja hozzá a **pontszám modell** modult egy folyamathoz.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 