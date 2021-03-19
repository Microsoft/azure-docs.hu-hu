---
title: A projekt frissítése a 3,0 API-ra
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan frissítheti Custom Vision projekteket az API előző verziójáról az 3,0 API-ra.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "73647505"
---
# <a name="update-to-the-30-api"></a>Frissítés a 3,0 API-ra

A Custom Vision mostantól elérte az általános elérhetőséget, és egy API-frissítésen esett át.
Ez a frissítés néhány új funkciót tartalmaz, és fontos, hogy néhány megszakítási változás:

* Az előrejelzési API mostantól két, a projekt típusa alapján oszlik meg.
* A jövőkép AI Developer Kit (VAIDK) exportálási beállításához adott módon kell létrehozni egy projektet.
* Az alapértelmezett iterációk el lettek távolítva a közzététel vagy a Közzététel visszavonása egy elnevezett iteráció mellett.

Ez az útmutató bemutatja, hogyan frissítheti projektjeit az új API-verzióval való együttműködéshez. A módosítások teljes listájáért tekintse meg a [kibocsátási megjegyzéseket](release-notes.md) .

## <a name="use-the-updated-prediction-api"></a>A frissített előrejelzési API használata

A 2. x API-k ugyanazt az előrejelzési hívást használták mind a képosztályozó, mind az objektum-Kiderítő projekt esetében. Mindkét projekttípus elfogadható volt a **PredictImage** és a **PredictImageUrl** -hívások esetében is. Az 3,0-es verziótól kezdődően az API-t felosztjuk, így a projekt típusának meg kell egyeznie a hívással:

* A **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** és a **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** használatával előrejelzéseket kaphat a képbesorolási projektekhez.
* Az **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** és a **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** használatával megtekintheti az objektum-észlelési projektek előrejelzéseit.

## <a name="use-the-new-iteration-publishing-workflow"></a>Az új iterációs közzétételi munkafolyamat használata

A 2. x API-k az alapértelmezett iterációt vagy egy megadott iterációs azonosítót használták az előrejelzéshez használandó iteráció kiválasztásához. A 3,0-től kezdődően egy közzétételi folyamatot hoztunk létre, amelynek során először közzé kell tenni egy iterációt a betanítási API megadott neve alatt. Ezután adja át a nevet az előrejelzési metódusoknak a használni kívánt iteráció megadásához.

> [!IMPORTANT]
> Az 3,0 API-k nem az alapértelmezett iterációs funkciót használják. Amíg a régebbi API-k elavultak, továbbra is használhatja a 2. x API-kat az iterációk alapértelmezettként való váltásához. Ezek az API-k egy adott időszakban megmaradnak, és meghívhatja a **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metódust az iteráció alapértelmezettként való megjelölésére.

### <a name="publish-an-iteration"></a>Iteráció közzététele

Az iteráció betanítása után elérhetővé teheti az előrejelzést a **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metódus használatával. Az iteráció közzétételéhez szüksége lesz a CustomVision webhely beállítások lapján elérhető előrejelzési erőforrás-AZONOSÍTÓra.

![A Custom Vision webhely beállításai lap, amely az előrejelzési erőforrás AZONOSÍTÓját ismerteti.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Ezt az információt az [Azure Portalon](https://portal.azure.com) is megtekintheti, ha a Custom Vision előrejelzési erőforrást választja, és kiválasztja a **Tulajdonságok** elemet.

Miután közzétette az iterációt, az alkalmazások az előrejelzési API-hívásban megadhatják a nevet az előrejelzéshez. Ha nem szeretné, hogy egy iteráció előrejelzési hívásokhoz ne legyen elérhető, használja a **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API-t.

## <a name="next-steps"></a>Következő lépések

* [A képzés API-referenciájának dokumentációja (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Előrejelzési API-referenciák dokumentációja (REST)](https://go.microsoft.com/fwlink/?linkid=865445)