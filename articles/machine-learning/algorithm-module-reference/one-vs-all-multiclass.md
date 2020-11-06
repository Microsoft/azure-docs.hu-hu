---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a One-vs-All Multiclass modult a Azure Machine Learning Designerben a bináris besorolású modellek együttes létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: f09a77043a552d8d7f09c2a6abcb78f89cbf8ee2
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420682"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

Ez a cikk azt ismerteti, hogyan használható a One-vs-All Multiclass modul a Azure Machine Learning Designerben. A cél egy olyan besorolási modell létrehozása, amely több osztály előrejelzését is elvégezheti az *egyirányú megközelítés használatával* .

Ez a modul olyan modellek létrehozásához hasznos, amelyek három vagy több lehetséges eredményt jeleznek, ha az eredmény a folyamatos vagy a kategorikus prediktív változóktól függ. Ez a módszer lehetővé teszi a bináris besorolási módszerek használatát is olyan problémák esetén, amelyek több kimeneti osztályt igényelnek.

### <a name="more-about-one-versus-all-models"></a>További információ az egy-és az összes modellről

Egyes besorolási algoritmusok lehetővé teszik a kettőnél több osztály használatát a kialakítás alapján. Mások a lehetséges eredményeket két érték (bináris vagy kétosztályos modell) egyikére korlátozzák. A többosztályos besorolási feladatokhoz azonban még a bináris besorolási algoritmusokat is át lehet igazítani különböző stratégiákkal. 

Ez a modul megvalósítja az egy-és az összes módszert, amelyben a több kimeneti osztályhoz létrehoznak egy bináris modellt. A modul az egyes osztályokhoz tartozó bináris modellek mindegyikét kivizsgálja a komplement (a modell minden más osztálya) alapján, mintha bináris besorolási probléma lenne. A modul ezután végrehajtja az előrejelzést a bináris besorolások futtatásával, és a legmagasabb szintű megbízhatósági pontszám alapján kiválaszthatja az előrejelzést.  

Lényegében a modul az egyes modellek együttesét hozza létre, majd egyesíti az eredményeket, hogy egyetlen modellt hozzon létre, amely minden osztályt előre jelez. Bármely bináris osztályozó használható az egy-és az összes modell alapjaként.  

Tegyük fel például, hogy egy [kétosztályos támogatási vektoros](two-class-support-vector-machine.md) modellt konfigurál, és a One-vs-all Multiclass modulba bemenetként adja meg. A modul kétosztályos támogatást hoz létre a kimeneti osztály összes tagjához. Ezután alkalmazza a One-versus-all metódust az összes osztály eredményeinek összevonásához.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Az One-vs-All Multiclass osztályozó konfigurálása  

Ez a modul a bináris besorolási modellek együttesét hozza létre több osztály elemzéséhez. A modul használatához először be kell állítania és be kell tanítania egy *bináris besorolási* modellt. 

A bináris modellt a One-vs-All Multiclass modulhoz kell kötni. Ezután betaníthatja a modellek együttesét egy címkézett betanítási adatkészlettel a [betanítási modell](train-model.md) használatával.

A modellek kombinálása esetén One-vs-All Multiclass több bináris besorolási modellt hoz létre, optimalizálja az egyes osztályok algoritmusát, majd egyesíti a modelleket. A modul ezeket a feladatokat akkor is elvégzi, ha a betanítási adatkészlet több osztállyal is rendelkezhet.

1. Adja hozzá a One-vs-All Multiclass modult a folyamathoz a tervezőben. Ezt a modult a **besorolási** kategóriában, a **Machine learning-inicializálás** alatt találja.

   Az One-vs-All Multiclass osztályozó nem rendelkezik saját konfigurálható paraméterekkel. A testreszabásokat a bemenetként megadott bináris besorolási modellben kell elvégezni.

2. Adjon hozzá egy bináris besorolási modellt a folyamathoz, és konfigurálja a modellt. Használhatja például a [kétosztályos támogatású vektoros gépet](two-class-support-vector-machine.md) vagy a [kétosztályos megnövelt döntési fát](two-class-boosted-decision-tree.md).

3. Adja hozzá a [Train Model](train-model.md) modult a folyamathoz. Kapcsolja össze a nem betanított osztályozó, amely a One-vs-all Multiclass kimenete.

4. A [betanítási modell](train-model.md)másik bemenetén csatlakoztasson egy olyan címkézett betanítási adatkészletet, amely több osztállyal is rendelkezik.

5. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után a modellt használhatja a többosztályos előrejelzések készítéséhez.

Azt is megteheti, hogy átadja a nem betanított osztályozó [modellt a modell](cross-validate-model.md) átadására a címkével ellátott ellenőrzési adatkészlet alapján.


## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
