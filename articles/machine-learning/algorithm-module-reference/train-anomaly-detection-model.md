---
title: 'A Train rendellenesség észlelési modellje: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre egy betanított anomália-észlelési modellt a Train anomália-észlelési modell modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: edf35fada4233fbe43bc7f859c2414bfb8130714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905721"
---
# <a name="train-anomaly-detection-model-module"></a>A Train rendellenesség észlelési modell modulja

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designerben a Train rendellenesség-észlelési modell modul a betanított anomáliák észlelési modelljének létrehozásához.

A modul bemenetként egy anomália-észlelési modell és egy címkézetlen adatkészlet paramétereit adja meg. Egy betanított anomália-észlelési modellt ad vissza, amely a betanítási adatokhoz tartozó címkék készletével együtt.  

A tervezőben elérhető anomália-észlelési algoritmusokkal kapcsolatos további információkért lásd: [PCA-alapú anomáliák észlelése](pca-based-anomaly-detection.md).  

## <a name="how-to-configure-train-anomaly-detection-model"></a>A Train anomália észlelési modell konfigurálása 

1.  Adja hozzá a **vonat anomália észlelési modell** modult a folyamathoz a tervezőben. Ez a modul az **anomáliák észlelése** kategóriában található.

2. Csatlakoztasson egy olyan modult, amely anomáliák észlelésére szolgál (például [PEM-alapú anomáliák észlelése](pca-based-anomaly-detection.md)).

    Más típusú modellek nem támogatottak. A folyamat futtatásakor a következő hibaüzenet jelenik meg: "az összes modellnek ugyanazzal a tanulói típussal kell rendelkeznie."  

3.  Konfigurálja az anomáliák észlelése modult a Label (címke) oszlop kiválasztásával, és az algoritmushoz tartozó egyéb paraméterek megadásával.  

4.  Csatoljon egy betanítási adatkészletet a **vonat anomália észlelési modell** jobb oldali bemenetéhez.  

5.  A folyamat elküldése.  

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ A modell paramétereinek megtekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Megjelenítés** lehetőséget. 

+ Az előrejelzések létrehozásához használja a [score Model](score-model.md) modult az új bemeneti adatokkal.

+ A betanított modell pillanatképének mentéséhez válassza ki a modult. Ezután válassza az **adatkészlet regisztrálása** ikont a jobb oldali panel **outputs + naplók** lapján.   

 
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

A Designer modulokra jellemző hibák listáját lásd: [kivételek és hibakódok a tervezőhöz](designer-error-codes.md) .
'