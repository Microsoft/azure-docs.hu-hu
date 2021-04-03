---
title: Átalakítás képkönyvtárrá
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alakíthatja át az adatkészletet a Képkönyvtár formátumára a rendszerkép konvertálása a képkönyvtárba modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: 1489ce74da2ecff5212feb5a1a2e3c9151b73424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94555583"
---
# <a name="convert-to-image-directory"></a>Átalakítás képkönyvtárrá

Ez a cikk bemutatja, hogyan alakíthatja át a képkészletet a rendszerkép-adattípusra a rendszerkép konvertálása a *képkönyvtárba* modul használatával, amely szabványosított adatformátumot tartalmaz a képekhez kapcsolódó feladatokban, például a képbesorolásban Azure Machine learning Designerben.

## <a name="how-to-use-convert-to-image-directory"></a>Az átalakítás Rendszerképbeli könyvtárba való használata  

1. Először készítse elő a rendszerkép-adatkészletet. 

    A felügyelt tanuláshoz meg kell adnia a betanítási adatkészlet címkéjét. A rendszerkép-adatkészlet fájljának a következő struktúrában kell lennie:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    A rendszerkép-adatkészlet mappában több almappa is található. Minden almappa egy kategóriába tartozó képeket tartalmaz. Az almappák nevei olyan feladatok címkéi, mint a képbesorolás. További információkért tekintse meg a [torchvision adatkészletek](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) című témakört.

    > [!WARNING]
    > A tervező nem támogatja az adatfeliratokból exportált adatkészletek jelenleg nem támogatottak.

    A következő kiterjesztésű (kisbetűs) képek támogatottak: ". jpg", ". jpeg", ". png", ". ppm", ". bmp", ". PGM", ". tif", ". TIFF", ". WebP". Egy mappában több típusú képet is használhat. Az egyes kategóriák mappájában nem szükséges azonos számú lemezképet tartalmaznia.

    Használhatja a ". zip", a ". tar", a ". gz" és a ". bz2" kiterjesztésű mappát vagy tömörített fájlt. **A jobb teljesítmény érdekében a tömörített fájlok használata javasolt.** 
    
    ![Képminta-adatkészlet](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > A következtetéshez a képadatkészlet mappájának csak nem besorolt képeket kell tartalmaznia.

1. [Regisztrálja a rendszerkép-adatkészletet](../how-to-create-register-datasets.md) a munkaterületen található fájl adatkészletként, mivel a képkönyvtári modulba való konvertálás bemenetének **fájl-adatkészletnek** kell lennie.

1. Adja hozzá a regisztrált rendszerkép-adatkészletet a vászonhoz. A regisztrált adatkészletet a vászon bal oldalán található modul lista **adatkészletek** kategóriájában találja. A Designer jelenleg nem támogatja a képadatkészlet megjelenítését.

    > [!WARNING]
    > Az **adatimportálási** modul **nem** használható a képadatkészlet importálására, mert az **adatimportálási** modul kimeneti típusa DataFrame könyvtár, amely csak a fájl elérési útja karakterláncot tartalmazza.

1. Adja hozzá az **átalakítás a rendszerképhez könyvtárba** modult a vászonra. Ezt a modult a "Computer Vision/képadatok átalakítása" kategóriában találja a modul listában. Csatlakoztatása a rendszerkép-adatkészlethez.
    
3.  A folyamat elküldése. Ezt a modult a GPU vagy a CPU is futtathatja.

## <a name="results"></a>Results (Eredmények)

A Képkönyvtár-modulba **való átalakítás** kimenete **Képkönyvtár** formátumú, és más rendszerképhez kapcsolódó modulokhoz is csatlakoztatható, amelyekben a bemeneti port formátuma is a rendszerkép könyvtára.

![Konvertálás rendszerkép-könyvtár kimenetére](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>Technikai megjegyzések 

###  <a name="expected-inputs"></a>Várt bemenetek  

| Név          | Típus                  | Leírás   |
| ------------- | --------------------- | ------------- |
| Bemeneti adatkészlet | AnyDirectory, ZipFile | Bemeneti adatkészlet |

###  <a name="output"></a>Kimenet  

| Név                   | Típus           | Leírás            |
| ---------------------- | -------------- | ---------------------- |
| Kimeneti rendszerkép könyvtára | ImageDirectory | Kimeneti rendszerkép könyvtára |

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .