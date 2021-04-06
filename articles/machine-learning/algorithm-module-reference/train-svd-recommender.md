---
title: 'A Train SVD ajánló: modul referenciája'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Train SVD ajánló modulját a SVD algoritmus használatával a Bayes-ajánlások betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 77407f253bb347160ea331bd7384d8085f21b040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654458"
---
# <a name="train-svd-recommender"></a>SVD-ajánló betanítása

Ez a cikk azt ismerteti, hogyan használható a SVD ajánló modul a Azure Machine Learning Designerben. Ezzel a modullal betaníthatja a javaslat modelljét az egyértékű dekompozíció (SVD) algoritmus alapján.  

A Train SVD ajánló modul beolvassa a felhasználói elemek minősítésének háromszorosát tartalmazó adatkészletet. Egy betanított SVD-ajánlót ad vissza. Ezután használhatja a betanított modellt a minősítések előrejelzéséhez vagy javaslatok létrehozásához a [score SVD ajánló](score-svd-recommender.md) moduljának összekapcsolásával.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>További tudnivalók a javaslatok modelljeiről és a SVD ajánlóról  

Egy ajánlási rendszer fő célja, hogy a rendszer egy vagy több *elemét* javasolja a *felhasználóknak* . Egy elem például film, étterem, könyv vagy zeneszám lehet. A felhasználók lehetnek személy, személyek csoportja vagy más entitások, amelyek elem-beállításokkal rendelkeznek.  

Az ajánló rendszerek két fő megközelítéssel rendelkeznek: 

+ A **Content-based** módszer a felhasználók és az elemek funkcióinak használatát is lehetővé teszi. A felhasználókat a tulajdonságok, például az életkor és a nemek szerint lehet leírva. Az elemeket a következő tulajdonságok írják le: Szerző és gyártó. A közösségi partnerkereső webhelyeken tipikus példákat talál a Content-based ajánlási rendszerekre. 
+ Az **együttműködési szűrés** csak a felhasználók és az elemek azonosítóit használja. A felhasználók által az elemeknek megadott minősítési (ritka) mátrixokból származó, implicit információkat kap ezekről az entitásokról. Megtudhatja, hogy egy felhasználó a minősítéssel rendelkező elemekről, illetve azokról a felhasználókról, akik azonos elemeket értékeltek.  

A SVD ajánló a felhasználók és az elemek azonosítóit, valamint a felhasználók által az elemeknek megadott minősítési mátrixot használja. Ez egy *együttműködési javaslat*. 

A SVD ajánlóval kapcsolatos további információkért tekintse meg a kapcsolódó kutatási papírt: [Matrix faktorizációs Techniques for ajánló Systems](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>A Train SVD ajánló konfigurálása  

### <a name="prepare-data"></a>Adatok előkészítése

A modul használata előtt a bemeneti adatoknak az ajánlási modell által várt formátumban kell lenniük. A felhasználó-tétel minősítéssel rendelkező triplák betanítási adatkészlete kötelező.

+ Az első oszlop felhasználói azonosítókat tartalmaz.
+ A második oszlop az elemek azonosítóit tartalmazza.
+ A harmadik oszlop tartalmazza a felhasználó-tétel párok minősítését. A minősítési értékeknek numerikus típusúnak kell lenniük.  

A Azure Machine Learning Designer **Movie Ratings** adatkészlete (az **adatkészletek** kiválasztása, majd a **minták**) a várt formátumot mutatja:

![Film minősítése](media/module/movie-ratings-dataset.png)

Ebből a mintából láthatja, hogy egyetlen felhasználó értékelte a több filmet. 

### <a name="train-the-model"></a>A modell betanítása

1.  Adja hozzá a Train SVD Ajánlói modult a folyamathoz a tervezőben, és kapcsolja össze a betanítási adataival.  
   
2.  A **tényezők száma** mezőben adhatja meg, hogy hány tényezőt kell használni az ajánlóban.  
    
    Az egyes tényezők azt mérik, hogy a felhasználó milyen mértékben kapcsolódik az elemhez. A tényezők száma egyben a látens dimenzióját is. A felhasználók és az elemek számának növelésével jobb, ha nagyobb számú tényezőt állít be. Ha azonban a szám túl nagy, csökkenhet a teljesítmény.
    
3.  Az **ajánlási algoritmusok Ismétlések száma** azt jelzi, hogy az algoritmus hányszor dolgozza fel a bemeneti adatokat. Minél nagyobb ez a szám, annál pontosabbak lesznek a jóslatok. A nagyobb szám azonban lassabb betanítást jelent. Az alapértelmezett érték 30.

4.  A **tanulási arány** mezőben adjon meg egy 0,0 és 2,0 közötti számot, amely meghatározza a tanulási lépés méretét.

    A tanulási arány meghatározza a lépés méretét az egyes iterációk során. Ha a lépés mérete túl nagy, lehet, hogy az optimális megoldást is lelövi. Ha a lépés mérete túl kicsi, a képzés továbbra is a legjobb megoldást keresi. 
  
5.  A folyamat elküldése.  

## <a name="results"></a>Results (Eredmények)

A folyamat futásának befejezése után a modell a pontozáshoz való használatához a [SVD](train-svd-recommender.md) -ajánlót az új bemeneti példák értékének előrejelzéséhez kapcsolja össze a [SVD](score-svd-recommender.md).

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
