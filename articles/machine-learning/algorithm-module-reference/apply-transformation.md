---
title: 'Átalakítás alkalmazása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning átalakítási modulját a bemeneti adatkészlet módosítására egy korábban számított átalakítás alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: a5db3935ae445ee7dcf8129eb1d4c75fcb64302f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96003905"
---
# <a name="apply-transformation-module"></a>Átalakítási modul alkalmazása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal módosíthatja a bemeneti adatkészletet egy korábban számított transzformáció alapján. Ez a modul akkor szükséges, ha frissítenie kell az átalakításokat a következtetési folyamatokban.

Ha például a z-score-t használta a betanítási adatok normalizálása érdekében a **normalizálás** adatmodul használatával, akkor azt a z-score értéket érdemes használni, amelyet a program a pontozási fázisban is kiszámított képzéshez. Azure Machine Learning a normalizálás módszerét átalakíthatja átalakításként, majd az **átalakítás alkalmazása** paranccsal alkalmazhatja a z-pontszámot a bemeneti adatokra a pontozás előtt.

## <a name="how-to-save-transformations"></a>Átalakítások mentése

A Designer lehetővé teszi adatátalakítások **adatkészletként** való mentését, így azokat más folyamatokban is használhatja.

1. Válassza ki azt az Adatátalakítási modult, amely sikeresen futott.

1. Válassza a **kimenetek + naplók** lapot.

1. Keresse meg az átalakítás kimenetét, és válassza az **adatkészlet regisztrálása** lehetőséget, hogy a modul paletta **adatkészletek** kategóriája alatt mentse modulként.

## <a name="how-to-use-apply-transformation"></a>Az átalakítás alkalmazása  
  
1. Adja hozzá az **átalakítási modul alkalmazása** a folyamathoz lehetőséget. Ez a modul a modul paletta **modell pontozás & próbaverzió** szakaszában található. 
  
1. Keresse meg a modul paletta **adatkészletek** területén használni kívánt mentett átalakítást.

1. A mentett transzformáció kimenetének összekötése az **átalakítási modul alkalmazása** bal oldali bemeneti portjával.

    Az adatkészletnek pontosan ugyanazzal a sémával kell rendelkeznie (oszlopok száma, oszlopnevek, adattípusok), mint azt az adatkészletet, amelyhez az átalakítást először tervezték.  
  
1. Kapcsolja össze a kívánt modul adatkészlet-kimenetét az **átalakítási** modul megfelelő bemeneti portjával.
  
1. Ha egy átalakítást szeretne alkalmazni az új adatkészletre, küldje el a folyamatot.  

> [!IMPORTANT]
> Annak biztosítása érdekében, hogy a folyamatban lévő folyamatok a betanítási folyamatokban is megvalósíthatók legyenek, az alábbi lépéseket kell követnie minden alkalommal, amikor a betanítási folyamat frissült az átalakítás során:
> 1. A betanítási folyamat során regisztrálja a [Select Columns átalakító](select-columns-transform.md) kimenetét adatkészletként.
> ![A modul kimeneti adatkészletének regisztrálása](media/module/select-columns-transform-register-dataset.png)
> 1. A következtetési folyamatban távolítsa el a **TD-** modult, és cserélje le az előző lépésben regisztrált adatkészletre.
> ![A TD modul cseréje](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 