---
title: A csoportos adatelemzési folyamat életciklusa
description: A csoportos adatelemzési folyamat (TDSP) egy ajánlott életciklust biztosít, amelyet az adatelemzési projektek felépítéséhez használhat.
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
ms.openlocfilehash: ed54fda744978ac43bbffc6a70d6b331e85dc5e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93305590"
---
# <a name="the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusa

A csoportos adatelemzési folyamat (TDSP) egy ajánlott életciklust biztosít, amelyet az adatelemzési projektek felépítéséhez használhat. Az életciklus a sikeres projektek követésének teljes lépéseit ismerteti. Ha más adattudományi életciklust használ, mint például az adatbányászat [(éles DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), az adatbázisok [(KDD](https://wikipedia.org/wiki/Data_mining#Process)-k) és a szervezet saját egyéni folyamatának az iparági szabványos folyamata, továbbra is használhatja a feladat-alapú TDSP. 

Ez az életciklus olyan adattudományi projektekhez készült, amelyek intelligens alkalmazások részeként való szállításra szolgálnak. Ezek az alkalmazások gépi tanulási vagy mesterséges intelligencia-modelleket helyeznek üzembe a prediktív elemzésekhez. A feltáró adatelemzési projektek és az improvizált elemzési projektek is hasznosak lehetnek a folyamat használatából. Ezen projektek esetében azonban előfordulhat, hogy az itt ismertetett lépések némelyike nem szükséges. 

## <a name="five-lifecycle-stages"></a>Öt életciklus szakasz

A TDSP életciklusa öt fő szakaszból áll, amelyek a iteratív-t futtatják. Ezek a szakaszok a következők:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Itt látható a TDSP életciklus vizuális ábrázolása: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


A TDSP életciklusa ismétlődő lépések sorozatából áll, amely útmutatást nyújt a prediktív modellek használatához szükséges feladatokhoz. A prediktív modelleket olyan éles környezetben helyezheti üzembe, amelyet az intelligens alkalmazások létrehozásához tervez használni. A folyamat életciklusának célja, hogy továbbra is egy adattudományi projektet helyezzen át egy egyértelmű engagement-végpont irányába. Az adatelemzés a kutatás és a felderítés terén folytatott gyakorlat. A feladatoknak a csapatával és ügyfeleivel való kommunikációjának lehetősége a szabványosított sablonokat használó, jól definiált összetevők használatával a félreértések elkerülése érdekében. Ezen sablonok használatával a komplex adattudományi projektek sikeres befejezésének esélye is növekszik.

Az egyes fázisok esetében a következő információkat adjuk meg:

   * **Célok**: az adott célkitűzések.
   * **Útmutató**: a feladatok végrehajtásának részletes ismertetése és útmutatása.
   * Összetevők **: a** termékek és a hozzájuk tartozó támogatás.

## <a name="next-steps"></a>Következő lépések

Teljes körű útmutatót biztosítunk, amely bemutatja a folyamat összes lépését adott forgatókönyvek esetén. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. Az útmutató bemutatja, hogyan egyesítheti a felhőt, a helyszíni eszközöket és a szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](./index.yml).