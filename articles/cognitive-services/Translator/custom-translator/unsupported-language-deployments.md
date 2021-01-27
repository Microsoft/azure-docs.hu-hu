---
title: Nem támogatott nyelvi telepítések – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan helyezhet üzembe nem támogatott nyelvi párokat az Azure Cognitive Services Custom Translator szolgáltatásban.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: fb31388647fc6022a2e6670baf7b3e73f345c36a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898070"
---
# <a name="unsupported-language-deployments"></a>Nem támogatott nyelvű környezetek

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

A Microsoft Translator hub közelgő kivezetésével a Microsoft a központon keresztül jelenleg üzembe helyezett összes modellt törli. Számos olyan modell van telepítve a központban, amelynek nyelvi párjai nem támogatottak az egyéni fordítóban.  Ebben a helyzetben nem szeretnénk, hogy a felhasználók ne legyenek felhasználva a tartalmuk fordítására.

Most már van egy olyan folyamat, amely lehetővé teszi a nem támogatott modellek üzembe helyezését az egyéni fordítón keresztül.  Ez a folyamat lehetővé teszi, hogy a legújabb V3 API-val továbbra is lefordítsa a tartalmakat.  Ezeket a modelleket a rendszer addig üzemelteti, amíg meg nem adja az üzembe helyezést, vagy a nyelvi pár elérhetővé válik az egyéni fordítóban.  Ez a cikk a modellek nem támogatott nyelvi párokkal való üzembe helyezésének folyamatát ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a modellek központi telepítésre legyenek kijelöltek, a következő feltételeknek kell megfelelniük:
* A modellt tartalmazó projektet át kell telepíteni a központból az egyéni Fordítóba az áttelepítési eszköz használatával.  A projektek és munkaterületek áttelepítésének folyamata [itt](how-to-migrate.md)található.
* A modellnek központilag telepített állapotban kell lennie, amikor az áttelepítés történik.  
* A modell nyelvi párja nem támogatott nyelvi pár kell, hogy legyen az egyéni fordítóban.  Nyelvi párok, amelyekben a nyelv az angoltól vagy az angoltól támogatott, de a pár nem tartalmazza az angol nyelvet, a nem támogatott nyelvi telepítésekre jelöltek.  Egy francia – német nyelvű pár hub-modell például nem támogatott nyelvi pár, bár francia – angol, angol és német nyelven is támogatott nyelvi pár.

## <a name="process"></a>Folyamat
Miután áttelepítette a modelleket az üzembe helyezésre jelölt központból, megkeresheti őket a munkaterület **Beállítások** lapján, és görgetheti az oldal végére, ahol megjelenik a nem **támogatott Translator hub tréningek** szakasz.  Ez a szakasz csak akkor jelenik meg, ha olyan projektekkel rendelkezik, amelyek megfelelnek a fent említett előfeltételeknek.

![Képernyőfelvétel: a nem támogatott Translator hub képzések szakasz.](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

A nem **támogatott Translator hub tréningek** kiválasztási lapján a nem **kérelmezett képzések** lap az üzembe helyezésre jogosult modelleket tartalmazza.  Válassza ki a telepíteni kívánt modelleket, és küldje el a kérést.   A 30. áprilisi üzembe helyezési határidő előtt annyi modellt választhat, amennyit csak szeretne üzembe helyezéshez.
 
![A nem kérelmezett Betanítások lapot megjelenítő képernyőkép.](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Az elküldés után a modell többé nem lesz elérhető a nem **kérelmezett képzések** lapon, és ehelyett a **kért képzések** lapon fog megjelenni.  A kért képzések bármikor megtekinthetők.

![Áttelepítés az elosztóról](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>A következő lépések

A központi telepítéshez kiválasztott modelleket a rendszer a központ leszerelése és az összes modell üzembe helyezése után menti.  A nem támogatott modellek üzembe helyezésére vonatkozó kérések elküldésére május 24-én van lehetőség.  Ezeket a modelleket június 15-én fogjuk üzembe helyezni, ekkor a Translator V3 API-n keresztül lesznek elérhetők.  Ezen kívül a v2 API-n keresztül lesznek elérhetők az 1. július 1-jéig.  

További információ a központ elavulása a fontos dátumokról [itt](https://www.microsoft.com/translator/business/hub/)található.
Az üzembe helyezést követően a szokásos üzemeltetési díjak érvényesek.  A részletekért tekintse meg a [díjszabást](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) .  

A standard egyéni fordítói modellektől eltérően a hub-modellek csak egyetlen régióban lesznek elérhetők, így a többrégiós üzemeltetési díjak nem lesznek érvényesek.  A üzembe helyezést követően bármikor megkezdheti a hub-modell üzembe helyezését az áttelepített egyéni Translator projekten keresztül.

## <a name="next-steps"></a>Következő lépések

- [Modell betanítása](how-to-train-model.md).
- Az üzembe helyezett egyéni fordítási modell használatának megkezdése a [Microsoft Translator Text API v3](../reference/v3-0-translate.md?tabs=curl)segítségével.