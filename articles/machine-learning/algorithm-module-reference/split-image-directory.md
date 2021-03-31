---
title: Képkönyvtár felosztása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan oszthatja szét a lemezképek könyvtárainak lemezképeit két különálló készletre a tervezőben a rendszerkép felosztása moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 3ee4dd9b2e344ecb3e1a6424ce7310270e7cd076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421192"
---
# <a name="split-image-directory"></a>Képkönyvtár felosztása

Ez a témakör azt ismerteti, hogyan lehet a lemezkép-könyvtár lemezképeit két különböző csoportba osztani a Azure Machine Learning Designerben.

Ez a modul különösen akkor hasznos, ha a képadatokat betanítási és tesztelési készletekbe kell elkülöníteni. 

## <a name="how-to-configure-split-image-directory"></a>A felosztott rendszerkép könyvtárának konfigurálása

1. Adja hozzá a **felosztott rendszerkép könyvtára** modult a folyamathoz. Ez a modul a "Computer Vision/képadatok átalakítása" kategóriában található.

2. Kapcsolja össze azt a modult, amelynek kimenete a rendszerkép könyvtára.

3. Az **első kimenetben található rendszerképek bemeneti hányada** , amely a bal oldali felosztásban lévő adatok százalékos arányát adja meg, alapértelmezés szerint 0,9. Ha a tört eredmény nem egész szám, a modul a kisebb közeli egész számot használja.


## <a name="technical-notes"></a>Technikai megjegyzések

### <a name="expected-inputs"></a>Várt bemenetek

| Név                  | Típus           | Description              |
| --------------------- | -------------- | ------------------------ |
| Bemeneti rendszerkép könyvtára | ImageDirectory | Felosztható Képkönyvtár |

### <a name="module-parameters"></a>Modul paramétereinek

| Név                                   | Típus  | Tartomány | Választható | Description                            | Alapértelmezett |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Az első kimenetben található rendszerképek töredéke | Float | 0-1   | Kötelező | Az első kimenetben található rendszerképek töredéke | 0,9     |

### <a name="outputs"></a>Kimenetek

| Név                    | Típus           | Description                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Kimeneti rendszerkép directory1 | ImageDirectory | A kijelölt lemezképeket tartalmazó könyvtár |
| Kimeneti rendszerkép directory2 | ImageDirectory | Az összes többi rendszerképet tartalmazó könyvtár |

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

