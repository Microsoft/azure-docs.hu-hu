---
title: Algoritmusokra és modulokra vonatkozó referenciák
description: Ismerje meg a saját gépi tanulási projektek létrehozásához használható Azure Machine Learning Designer modulokat.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/09/2020
ms.openlocfilehash: 89ad9aae7c0d01971bbcfc7e392cb9d455ef85cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94376842"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Algoritmus & Azure Machine Learning Designer modul-hivatkozása

Ez a hivatkozási tartalom a Azure Machine Learning Designerben elérhető gépi tanulási algoritmusok és modulok technikai hátterét ismerteti.

Minden modul a kód egy készletét jelöli, amely egymástól függetlenül futtatható, és gépi tanulási feladatot hajt végre, a szükséges bemenetek miatt. A modulok tartalmazhatnak egy adott algoritmust, vagy elvégezhetnek egy olyan feladatot, amely fontos a gépi tanulásban, például a hiányzó érték cseréje vagy a statisztikai elemzés.

Az algoritmusok kiválasztásával kapcsolatos segítségért lásd: 
* [Algoritmusok kiválasztása](../how-to-select-algorithms.md)
* [Azure Machine Learning algoritmus – Cheat Sheet](../algorithm-cheat-sheet.md)

> [!TIP]
> A tervező bármely folyamatában információt kaphat egy adott modulról. Kattintson a **További információ** hivatkozásra a modul kártyáján, ha a modult a modul listában vagy a modul jobb oldali ablaktáblájában viszi.

## <a name="data-preparation-modules"></a>Adatelőkészítési modulok


| Funkció | Leírás | Modul |
| --- |--- | --- |
| Adatok bevitele és kimenete | A Felhőbeli forrásokból származó adatok áthelyezése a folyamatba. Megírhatja az eredményeket vagy köztes adatait az Azure Storage-ba, a SQL Databaseba vagy a kaptárba, egy folyamat futtatása közben, vagy a Felhőbeli tároló használatával átválthatja az adatátviteli folyamatokat.  | [Manuális adatbevitel](enter-data-manually.md) <br/> [Adatexportálás](export-data.md) <br/> [Adatok importálása](import-data.md) |
| Adatátalakítás | A gépi tanuláshoz egyedi adatokon alapuló műveletek, például a normalizálás vagy a dobozolási, a dimenzióját csökkentése és az adatok konvertálása különböző fájlformátumok között.| [Oszlopok hozzáadása](add-columns.md) <br/> [Sorok hozzáadása](add-rows.md) <br/> [Matematikai művelet alkalmazása](apply-math-operation.md) <br/> [SQL-átalakítás alkalmazása](apply-sql-transformation.md) <br/> [Hiányzó adatok törlése](clean-missing-data.md) <br/> [Értékek levágása](clip-values.md) <br/> [Konvertálás CSV formátumba](convert-to-csv.md) <br/> [Átalakítás adathalmazzá](convert-to-dataset.md) <br/> [Átalakítás mutatóértékekké](convert-to-indicator-values.md) <br/> [Metaadatok szerkesztése](edit-metadata.md) <br/> [Adatok csoportosítása intervallumokba](group-data-into-bins.md) <br/> [Csatlakozás adatokhoz](join-data.md) <br/> [Adatok normalizálása](normalize-data.md) <br/> [Partíció és minta](partition-and-sample.md)  <br/> [Ismétlődő sorok eltávolítása](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Oszlopok kiválasztása átalakításhoz](select-columns-transform.md) <br/> [Adathalmaz oszlopainak kiválasztása](select-columns-in-dataset.md) <br/> [Adatok felosztása](split-data.md) |
| Szolgáltatás kiválasztása | Válassza ki a megfelelő, hasznos funkciók egy részhalmazát, amelyet az analitikai modell létrehozásához kíván használni. | [Szűrőalapú szolgáltatásválasztás](filter-based-feature-selection.md) <br/> [A permutáció funkció fontossága](permutation-feature-importance.md) |
| Statisztikai függvények | Az adatelemzéshez kapcsolódó statisztikai módszerek széles skáláját adja meg. | [Adatok összegzése](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Gépi tanulási algoritmusok

| Funkció | Leírás | Modul |
| --- |--- | --- |
| Regresszió | Egy érték előrejelzése. | [Súlyozott döntési fa típusú regresszió](boosted-decision-tree-regression.md) <br/> [Döntési erdő típusú regresszió](decision-forest-regression.md) <br/> [Gyors erdő típusú kvantilis regresszió](fast-forest-quantile-regression.md)  <br/> [Lineáris regresszió](linear-regression.md)  <br/> [Neurális hálózat típusú regresszió](neural-network-regression.md)  <br/> [Poisson-regresszió](poisson-regression.md)  <br/>|
| Fürtözés | Csoportosítsa az adathalmazokat.| [K-közép csoportosítás](k-means-clustering.md)
| Osztályozás | Egy osztály előrejelzése.  Válasszon a bináris (kétosztályos) vagy a többosztályos algoritmusok közül.| [Többosztályos súlyozott döntési fa](multiclass-boosted-decision-tree.md) <br/> [Többosztályos döntési erdő](multiclass-decision-forest.md) <br/> [Többosztályos logisztikai regresszió](multiclass-logistic-regression.md)  <br/> [Többosztályos neurális hálózat](multiclass-neural-network.md) <br/> [One vs. All Multiclass](one-vs-all-multiclass.md) <br/> [One vs. One Multiclass](one-vs-one-multiclass.md) <br/>[Kétosztályos átlagolt perceptron](two-class-averaged-perceptron.md) <br/>  [Kétosztályos súlyozott döntési fa](two-class-boosted-decision-tree.md)  <br/> [Kétosztályos döntési erdő](two-class-decision-forest.md) <br/>  [Kétosztályos logisztikai regresszió](two-class-logistic-regression.md) <br/> [Kétosztályos neurális hálózat](two-class-neural-network.md) <br/> [Kétosztályos támogató vektorgép](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Modellek létrehozásához és kiértékeléséhez használható modulok

| Funkció | Leírás | Modul |
| --- |--- | --- |
| Modell betanítása | Adatfuttatás az algoritmuson keresztül. |  [Csoportosítási modell betanítása](train-clustering-model.md) <br/> [Modell betanítása](train-model.md) <br/> [Pytorch-modell betanítása](train-pytorch-model.md) <br/> [Modell-hiperparaméterek hangolása](tune-model-hyperparameters.md) |
| Modell pontozása és kiértékelése | Mérje fel a betanított modell pontosságát. | [Átalakítás alkalmazása](apply-transformation.md) <br/> [Adatok hozzárendelése fürtökhöz](assign-data-to-clusters.md) <br/> [Modell keresztellenőrzése](cross-validate-model.md) <br/> [Modell értékelése](evaluate-model.md) <br/> [Képmodell pontozása](score-image-model.md) <br/> [Relevanciamodell](score-model.md) |
| Python-nyelv | Kód írása és beágyazása modulba a Python és a folyamat integrálásához. | [Python-modell létrehozása](create-python-model.md) <br/> [Python-szkript futtatása](execute-python-script.md) |
| R nyelv | Kód írása és beágyazása egy modulba az R és a folyamat integrálásához. | [R-szkript végrehajtása](execute-r-script.md) |
| Text Analytics | Speciális számítási eszközöket biztosít a strukturált és strukturálatlan szöveggel való munkához. |  [Szó konvertálása vektorrá](convert-word-to-vector.md) <br/> [N-Gram-funkciók kinyerése szövegből](extract-n-gram-features-from-text.md) <br/> [Funkciókivonatolás](feature-hashing.md) <br/> [Szöveg előfeldolgozása](preprocess-text.md) <br/> [Rejtett Dirichlet-lefoglalás](latent-dirichlet-allocation.md) <br/> [Vowpal Wabbit modell pontozása](score-vowpal-wabbit-model.md) <br/> [Vowpal Wabbit modell betanítása](train-vowpal-wabbit-model.md)|
| Computer Vision | Rendszerkép-adatok előfeldolgozása és rendszerkép-felismeréssel kapcsolatos modulok. |  [Képátalakítás alkalmazása](apply-image-transformation.md) <br/> [Átalakítás képkönyvtárrá](convert-to-image-directory.md) <br/> [Képátalakítás indítása](init-image-transformation.md) <br/> [Képkönyvtár felosztása](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| Ajánlás | Build-javaslatok modelljei. | [Ajánló értékelése](evaluate-recommender.md) <br/> [SVD-ajánló pontozása](score-svd-recommender.md) <br/> [Wide and Deep ajánló pontozása](score-wide-and-deep-recommender.md)<br/> [SVD-ajánló betanítása](train-SVD-recommender.md) <br/> [Wide and Deep ajánló betanítása](train-wide-and-deep-recommender.md)|
| Rendellenességek észlelése | Hozzon létre anomáliák észlelési modelljeit. | [PCA-alapú rendellenesség-észlelés](pca-based-anomaly-detection.md) <br/> [Rendellenesség-észlelési modell betanítása](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Webszolgáltatás

Ismerkedjen meg a [webszolgáltatások moduljaival](web-service-input-output.md) , amelyek a Azure Machine learning Designer valós idejű következtetéséhez szükségesek.

## <a name="error-messages"></a>Hibaüzenetek

Ismerje meg, hogy milyen [hibaüzenetek és kivétel-kódok](designer-error-codes.md) merülhetnek fel a Azure Machine learning Designer moduljaival.

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: modell létrehozása Designerben az automatikus árak előrejelzéséhez](../tutorial-designer-automobile-price-train-score.md)
