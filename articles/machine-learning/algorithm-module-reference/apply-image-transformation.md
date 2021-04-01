---
title: Képátalakítás alkalmazása
titleSuffix: Azure Machine Learning
description: Bemutatjuk, hogyan használható a rendszerkép-átalakítási modul a rendszerkép-átalakításra a képkönyvtárra való alkalmazásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898907"
---
# <a name="apply-image-transformation"></a>Képátalakítás alkalmazása 

Ez a cikk azt ismerteti, hogyan használható a rendszerkép-átalakítási modul alkalmazása a Azure Machine Learning Designerben, hogy a rendszer egy előzőleg megadott rendszerkép-átalakítás alapján módosítsa a bemeneti képkönyvtárat.  

Az átalakítás megadásához kapcsolódnia kell egy [init rendszerkép-transzformációs](init-image-transformation.md) modullal, majd az átalakítást alkalmazhatja a rendszerkép-átalakítási modul bemeneti rendszerkép könyvtárába.

## <a name="how-to-use-apply-image-transformation"></a>A rendszerkép-átalakítás alkalmazása  

1. Adja hozzá a **rendszerkép-átalakítási modul alkalmazása** a folyamathoz lehetőséget. Ez a modul a *Computer Vision/képadatok átalakítása* kategóriában található. 

2. Az **init rendszerkép** -átalakítás kimenetének összekötése a **képátalakítás alkalmazásának** bal oldali bemenetével.

     > [!NOTE]
     > Ez a modul csak az [init Képátalakítási](init-image-transformation.md) modul által generált képátalakítást fogadja el. Más átalakítás esetén kérjük, kapcsolja össze az [átalakítás alkalmazásával](apply-transformation.md), ellenkező esetben a rendszer a "InvalidTransformationDirectoryError"-t fogja dobni.


3. Kapcsolódjon az átalakítani kívánt rendszerkép-könyvtárhoz.

4. A **Mode (mód**) beállításnál adja meg, hogy milyen célra használja a bemeneti átalakítást: "képzéshez" vagy "for következtetés". 

   Ha **a képzés** lehetőséget választja, a rendszer az init-rendszerkép átalakításakor megadott összes átalakítást alkalmazza.

   Ha **a következtetést** választja, akkor a rendszer kizárja az átalakítást, például az új minták véletlenszerűen történő létrehozását. Ennek az az oka, hogy az átalakítási műveletek az új mintákat véletlenszerűen, például a "véletlenszerű horizontális tükrözést" használják a képzések adatbővítéséhez, ezért a következtetést el kell távolítani, mivel a következtetéseket meg kell oldani a pontos előrejelzéshez és értékeléshez.

   > [!NOTE]
   > A **következtetési** sorrendbe foglalt átalakítások a következők: véletlenszerű átméretezett termés, véletlenszerű termés, véletlenszerű vízszintes tükrözés, véletlenszerű Függőleges tükrözés, véletlenszerű rotáció, véletlenszerű affin, véletlenszerű szürkeárnyalatos, véletlenszerű perspektíva, véletlenszerű törlés.

5. Ha egy rendszerkép-átalakítást szeretne alkalmazni egy új rendszerkép-könyvtárba, küldje el a folyamatot.  

### <a name="module-parameters"></a>Modul paramétereinek

| Name | Tartomány | Típus | Alapértelmezett                   | Leírás                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mód | Bármelyik   | Mód | (Meg kell adni a felhasználót a megadásához) | Milyen célra használja a bemeneti átalakítást. Érdemes kizárni a "véletlenszerű" átalakítási műveleteket a következtetésekben, de megtarthatja őket a képzésben |

### <a name="expected-inputs"></a>Várt bemenetek  

| Név                       | Típus                    | Leírás                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Bemeneti rendszerkép átalakítása | TransformationDirectory | Bemeneti rendszerkép átalakítása        |
| Bemeneti rendszerkép könyvtára      | ImageDirectory          | Átalakítandó Képkönyvtár |

### <a name="outputs"></a>Kimenetek  

| Név                   | Típus           | Leírás            |
| ---------------------- | -------------- | ---------------------- |
| Kimeneti rendszerkép könyvtára | ImageDirectory | Kimeneti rendszerkép könyvtára |

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
