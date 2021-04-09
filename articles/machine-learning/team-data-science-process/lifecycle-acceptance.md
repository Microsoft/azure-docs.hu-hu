---
title: A csoportos adatelemzési folyamat életciklusának ügyfél-elfogadási szakasza
description: Az adatelemzési projektek vevői elfogadási szakaszának célja, feladatai és termékei
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f2294ccb1d958b229a71e45bb502b8134d8d5c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305661"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusának ügyfél-elfogadási szakasza

Ez a cikk a csoportos adatelemzési folyamat (TDSP) ügyfél-elfogadási szakaszához kapcsolódó célokat, feladatokat és teljesítéseket ismerteti. Ez a folyamat egy javasolt életciklust biztosít, amely segítségével strukturálhatja az adatelemzési projekteket. Az életciklus a projektek jellemzően végrehajtandó főbb szakaszait vázolja fel, gyakran iteratív:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

Itt látható a TDSP életciklus vizuális ábrázolása: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
**Projekt-végtermékek véglegesítése**: Ellenőrizze, hogy a folyamat, a modell és az üzemi környezetben való üzembe helyezése megfelel-e az ügyfél célkitűzéseinek.

## <a name="how-to-do-it"></a>Útmutató
Ebben a szakaszban két fő feladat foglalkozik:

   * **Rendszer-ellenőrzés**: Ellenőrizze, hogy az üzembe helyezett modell és a folyamat megfelel-e az ügyfél igényeinek.
   * **Projekt-kikapcsolás**: kapcsolja ki a projektet arra az entitásra, amelyen a rendszer éles környezetben fog futni.

Az ügyfélnek ellenőriznie kell, hogy a rendszer megfelel-e az üzleti igényeknek, és hogy a megfelelő pontossággal válaszol-e a rendszer éles környezetben való üzembe helyezésére az ügyfél alkalmazása általi használatra. Az összes dokumentáció véglegesítése és felülvizsgálata megtörténik. A projekt átadásra kerül a műveletekért felelős entitás számára. Ez az entitás lehet például egy IT vagy Customer adattudományi csapat, vagy az ügyfélnek a rendszer éles környezetben való futtatásért felelős ügynöke. 

## <a name="artifacts"></a>Artifacts
Az ebben az utolsó szakaszban létrehozott fő összetevő az **ügyfélre vonatkozó projekt kilépési jelentése**. Ez a technikai jelentés tartalmazza a teljes projekt részletes ismertetését, amely a rendszer működésének megismeréséhez hasznos. A TDSP egy [kilépési jelentési](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) sablont biztosít. A sablont használhatja, vagy testre is szabhatja az ügyfél igényeihez. 


## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának egyes lépéseire mutató hivatkozásokat talál:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes körű bemutatókat biztosítunk, amelyek bemutatják a folyamat összes lépését adott forgatókönyvek esetében. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. Az útmutató bemutatja, hogyan egyesítheti a felhőt, a helyszíni eszközöket és a szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](./index.yml).