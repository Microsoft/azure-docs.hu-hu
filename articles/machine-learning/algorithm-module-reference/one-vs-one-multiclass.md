---
title: Egy-vs-One többosztályos
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning egy többosztályos modulját egy többosztályos besorolási modell létrehozásához a bináris besorolási modellek együttes használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94592906"
---
# <a name="one-vs-one-multiclass"></a>Egy-vs-One többosztályos

Ez a cikk azt ismerteti, hogyan használható az egy-és többosztályos modul a Azure Machine Learning Designerben. A cél egy olyan besorolási modell létrehozása, amely több osztályt is megjósolhat az *egy-az-egyhez* megközelítés használatával.

Ez a modul olyan modellek létrehozásához hasznos, amelyek három vagy több lehetséges eredményt jeleznek, ha az eredmény a folyamatos vagy a kategorikus prediktív változóktól függ. Ez a módszer lehetővé teszi a bináris besorolási módszerek használatát is olyan problémák esetén, amelyek több kimeneti osztályt igényelnek.

### <a name="more-about-one-versus-one-models"></a>További információ az egyirányú modellekről

Egyes besorolási algoritmusok lehetővé teszik a kettőnél több osztály használatát a kialakítás alapján. Mások a lehetséges eredményeket két érték (bináris vagy kétosztályos modell) egyikére korlátozzák. A többosztályos besorolási feladatokhoz azonban még a bináris besorolási algoritmusokat is át lehet igazítani különböző stratégiákkal. 

Ez a modul megvalósítja az egy-az-egyhez metódust, amelyben a rendszer egy bináris modellt hoz létre. Előrejelzési időpontban a legtöbb szavazatot fogadó osztály van kiválasztva. Mivel az `n_classes * (n_classes - 1) / 2` osztályozók számára szükséges, ez a módszer általában lassabb, mint az összes, az O (n_classes ^ 2) összetettsége miatt. Ez a módszer azonban előnyös lehet az olyan algoritmusokhoz, mint például a kernel-algoritmusok, amelyek nem jól méretezhetők `n_samples` . Ennek az az oka, hogy minden egyes tanulási probléma csak az adat egy kis részhalmazát foglalja magában, míg a teljes adathalmazt használják `n_classes` .

Lényegében a modul az egyes modellek együttesét hozza létre, majd egyesíti az eredményeket, hogy egyetlen modellt hozzon létre, amely minden osztályt előre jelez. Bármely bináris osztályozó használható egy-az-egyhez modell alapjaként.  

Tegyük fel például, hogy konfigurál egy [kétosztályos támogatást a vektoros gépekhez](two-class-support-vector-machine.md) , és úgy adja meg, hogy az egy-egy többosztályos modul bemenete legyen. A modul kétosztályos támogatást hoz létre a kimeneti osztály összes tagjához. Ezután alkalmazza az egy-az-egyhez metódust az összes osztály eredményeinek összevonásához.  

A modul a sklearn OneVsOneClassifier használja, és további részletekért [itt](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html)talál további információt.

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Az egy-és többosztályos osztályozó konfigurálása  

Ez a modul a bináris besorolási modellek együttesét hozza létre több osztály elemzéséhez. A modul használatához először be kell állítania és be kell tanítania egy *bináris besorolási* modellt. 

A bináris modellt a One-vs-One többosztályos modulhoz kell kötni. Ezután betaníthatja a modellek együttesét egy címkézett betanítási adatkészlettel a [betanítási modell](train-model.md) használatával.

Ha egyesíti a modelleket, egy-egy vagy több osztály több bináris besorolási modellt hoz létre, a optimalizálja az egyes osztályok algoritmusát, majd egyesíti a modelleket. A modul ezeket a feladatokat akkor is elvégzi, ha a betanítási adatkészlet több osztállyal is rendelkezhet.

1. Adja hozzá az egy-vs-One többosztályos modult a folyamathoz a tervezőben. Ezt a modult a **besorolási** kategóriában, a **Machine learning-inicializálás** alatt találja.

   Az egy-vagy többosztályos osztályozó nem rendelkezhet saját konfigurálható paraméterekkel. A testreszabásokat a bemenetként megadott bináris besorolási modellben kell elvégezni.

2. Adjon hozzá egy bináris besorolási modellt a folyamathoz, és konfigurálja a modellt. Használhatja például a [kétosztályos támogatású vektoros gépet](two-class-support-vector-machine.md) vagy a [kétosztályos megnövelt döntési fát](two-class-boosted-decision-tree.md).

3. Adja hozzá a [Train Model](train-model.md) modult a folyamathoz. Kapcsolja össze a nem betanított osztályozó, amely egy-vs-egy többosztályos kimenet.

4. A [betanítási modell](train-model.md)másik bemenetén csatlakoztasson egy olyan címkézett betanítási adatkészletet, amely több osztállyal is rendelkezik.

5. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után a modellt használhatja a többosztályos előrejelzések készítéséhez.

Azt is megteheti, hogy átadja a nem betanított osztályozó [modellt a modell](cross-validate-model.md) átadására a címkével ellátott ellenőrzési adatkészlet alapján.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
