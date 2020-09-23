---
title: Pytorch-modell betanítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja ki a pytorch-modellt teljesen vagy Finetune.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 0ba603dad7d48be725f308f3a3296676c5f4f108
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883242"
---
# <a name="train-pytorch-model"></a>Pytorch-modell betanítása

Ez a cikk azt ismerteti, hogyan használható a **Pytorch Model** modul a Azure Machine learning Designerben olyan Pytorch-modellek betanításához, mint a DenseNet. A betanítás a modell meghatározása és a hozzá tartozó paraméterek megadása után történik, és címkézett adatokra van szükség. 

## <a name="how-to-use-train-pytorch-model"></a>A Pytorch-modell használata 

1. Adja hozzá a [DenseNet](densenet.md) modult vagy a [ResNet](resnet.md) a folyamat piszkozatához a tervezőben.

2. Adja hozzá a **Train Pytorch Model** modult a folyamathoz. Ez a modul a **modell betanítása** kategóriában található. Bontsa ki a **vonat**elemet, majd húzza a **vonat Pytorch modell** modult a folyamatba.

   > [!NOTE]
   > A **Pytorch Model** modul a nagyméretű adatkészletek esetében jobb, ha **GPU** típusú számítási kapacitást futtat, ellenkező esetben a folyamat sikertelen lesz. A modul jobb oldali ablaktábláján kiválaszthatja a számítási lehetőséget, ha **más számítási célt használ**.

3.  A bal oldali bemeneten csatoljon egy nem betanított modellt. Csatolja a betanítási adatkészletet és az érvényesítési adatkészletet a **Pytorch-modell**középső és jobb oldali bemenetéhez.

    A nem betanított modell esetében olyan pytorch modellnek kell lennie, mint a DenseNet; Ellenkező esetben a rendszer a "InvalidModelDirectoryError"-et fogja dobni.

    Adatkészlet esetén a betanítási adatkészletnek címkével ellátott képkönyvtárnak kell lennie. A címkével ellátott képek könyvtárának beszerzéséhez tekintse meg a **Konvertálás rendszerkép-könyvtárba** című témakört. Ha nincs megjelölve, a rendszer egy "NotLabeledDatasetError"-t fog dobni.

    A betanítási adatkészlet és az érvényesítési adatkészlet azonos címkével rendelkezik, ellenkező esetben a rendszer InvalidDatasetError fog dobni.

4.  A **korszakok**esetében határozza meg, hogy hány alapértéket szeretne betanítani. A teljes adatkészlet minden alapkorszakban meg lesz ismételve, alapértelmezés szerint 5.

5.  A **Batch-méret**beállításnál határozza meg, hogy hány példányt szeretne betanítani egy kötegbe, alapértelmezés szerint 16.

6.  A **tanulási arány**mezőben határozza meg a *képzési arány*értékét. A tanulási arány értéke határozza meg a modell minden egyes tesztelésekor és javításakor használt lépés méretét.

    A sebesség csökkentésével gyakrabban teszteli a modellt, és azzal a kockázattal jár, hogy egy helyi fennsíkon fog elakadni. A lépés elvégzésével megközelítheti a valódi minimumokat, és így gyorsabban is megoszthatja őket. Alapértelmezés szerint 0,001.

7.  **Véletlenszerű vetőmag**esetén szükség esetén adjon meg egy egész számot, amelyet magként kíván használni. A magok használata ajánlott, ha biztosítani szeretné a kísérletek reprodukálhatóságát a futtatások között.

8.  **Türelmi**idő esetén azt határozza meg, hogy hány alapértéket kell leállítani, ha az érvényesítési veszteség nem csökken egymás után. Alapértelmezés szerint 3.

9.  A folyamat elküldése. Ha az adatkészlet mérete nagyobb, akkor eltarthat egy ideig.

## <a name="results"></a>Results (Eredmények)

A folyamat futásának befejezése után a modell a pontozáshoz való használatához a [Pytorch modellt](train-pytorch-model.md) a [képmodellhez](score-image-model.md)kell kapcsolni az új bemeneti példák értékének előrejelzéséhez.

## <a name="technical-notes"></a>Technikai megjegyzések
###  <a name="expected-inputs"></a>Várt bemenetek  

| Név               | Típus                    | Leírás                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| Nem betanított modell    | UntrainedModelDirectory | Nem betanított modell, pytorch megkövetelése         |
| Betanítási adatkészlet   | ImageDirectory          | Betanítási adatkészlet                         |
| Ellenőrzési adatkészlet | ImageDirectory          | Ellenőrzési adatkészlet az értékeléshez minden alapkorszakban |

###  <a name="module-parameters"></a>Modul paramétereinek  

| Name          | Tartomány            | Típus    | Alapértelmezett | Leírás                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Korszakok        | >0               | Egész szám | 5       | A címke vagy eredmény oszlopot tartalmazó oszlop kiválasztása |
| Köteg mérete    | >0               | Egész szám | 16      | Egy kötegbe tartozó példányok számának betanítása   |
| Tanulási sebesség | >= Double. Epszilon | Float   | 0,001   | A sztochasztikus gradiens elsüllyedés optimalizálásának kezdeti tanulási díja. |
| Véletlenszerű mag   | Bármelyik              | Egész szám | 1       | A modell által használt véletlenszerű számú generátor magja. |
| Türelem      | >0               | Egész szám | 3       | Hány korszakot kell korán leállítani   |

###  <a name="outputs"></a>Kimenetek  

| Név          | Típus           | Leírás   |
| ------------- | -------------- | ------------- |
| Betanított modell | ModelDirectory | Betanított modell |

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 



