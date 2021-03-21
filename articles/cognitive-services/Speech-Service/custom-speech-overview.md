---
title: Custom Speech áttekintése – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Custom Speech olyan online eszközöket biztosít, amelyekkel kiértékelheti és javíthatja az alkalmazásai, eszközei és termékei számára a Microsoft beszéd-szöveg pontosságát.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 13bf0f2430e0d58dd9ef28061aad897acf94ac3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493050"
---
# <a name="what-is-custom-speech"></a>Mi az a Custom Speech?

A [Custom SPEECH](https://aka.ms/customspeech) felhasználóifelület-alapú eszközök, amelyek segítségével kiértékelheti és javíthatja a Microsoft beszéd-szöveg pontosságát alkalmazásai és termékei számára. Az első lépésekhez mindössze néhány teszt hangfájlra van szüksége. Az ebben a cikkben található hivatkozásokat követve létrehozhat egy egyéni beszéd-szöveg élményt.

## <a name="whats-in-custom-speech"></a>Mi a Custom Speech?

Mielőtt bármit elvégezhet a Custom Speech, szüksége lesz egy Azure-fiókra és egy Speech Service-előfizetésre. A fiók létrehozása után előkészítheti az adatait, betaníthatja és tesztelheti a modelleket, megvizsgálhatja az adatok felismerésének minőségét, kiértékelheti a pontosságot, és végső soron üzembe helyezheti és használhatja az egyéni beszéd – szöveg modellt.

Ez a diagram a [Speech Studio Custom Speech területét](https://aka.ms/customspeech)alkotó darabokat emeli ki. Az alábbi hivatkozásokat követve további információkat tudhat meg az egyes lépésekről.

![Diagram, amely kiemeli a Speech Studio Custom Speech területét alkotó összetevőket.](./media/custom-speech/custom-speech-overview.png)

1. [Előfizetés és projekt létrehozása](#set-up-your-azure-account). Hozzon létre egy Azure-fiókot, és fizessen elő a beszédfelismerési szolgáltatásra. Ez az egyesített előfizetés hozzáférést biztosít a beszéd-szöveg, a szöveg és a beszéd fordításához, valamint a Speech [studióhoz](https://speech.microsoft.com/customspeech). Ezután használja a Speech Service-előfizetést az első Custom Speech projekt létrehozásához.

1. [Tesztelési adatok feltöltése](./how-to-custom-speech-test-and-train.md). A tesztelési adatok (hangfájlok) feltöltésével kiértékelheti az alkalmazásaihoz, eszközeihez és termékeihez tartozó Microsoft beszéd-szöveg típusú ajánlatát.

1. A [felismerés minőségének vizsgálata](how-to-custom-speech-inspect-data.md). A [Speech Studio](https://speech.microsoft.com/customspeech) használatával visszajátszhatja a feltöltött hangokat, és megvizsgálhatja a tesztelési adatok beszédfelismerési minőségét. A mennyiségi mérések esetében lásd: [az adatvizsgálat](how-to-custom-speech-inspect-data.md).

1. [Értékelje és javítsa a pontosságot](how-to-custom-speech-evaluate-data.md). Kiértékelheti és javíthatja a beszéd – szöveg modell pontosságát. A [Speech Studio](https://speech.microsoft.com/customspeech) a *szó hibájának mértékét* adja meg, amellyel meghatározhatja, hogy szükséges-e további képzés. Ha elégedett a pontossággal, a Speech Service API-jait közvetlenül is használhatja. Ha a pontosságot a relatív átlag 5%-ról 20%-ra szeretné növelni, használja a portál **képzés** lapját további betanítási adatok, például az emberi címkével ellátott átiratok és a kapcsolódó szövegek feltöltéséhez.

1. [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md). A beszédfelismerési modell pontosságának javításához írásos átiratok (10 – 1 000 óra) és a kapcsolódó szöveg (<200 MB), valamint a hang-tesztelési adatok. Ez az érték segít a beszéd – szöveg modell betanításában. A képzés után ellenőrizze az újratesztelést. Ha elégedett az eredménnyel, a modellt üzembe helyezheti egy egyéni végponton.

## <a name="set-up-your-azure-account"></a>Azure-fiók beállítása

Ahhoz, hogy a [Speech Studio](https://speech.microsoft.com/customspeech) segítségével egyéni modellt hozzon létre, rendelkeznie kell egy Azure-fiókkal és egy Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Mindenképpen hozzon létre egy standard (S0) előfizetést. Az ingyenes (F0) előfizetések nem támogatottak.

Ha egy egyéni modellt szeretne **hangadatokkal** betanítani, válasszon a következő régiók közül, amelyekhez dedikált hardver áll rendelkezésre a betanításhoz. Ez csökkenti a modell betanításához szükséges időt, és lehetővé teszi, hogy több hangfelvételt használjon a képzéshez. Ezekben a régiókban a beszédfelismerési szolgáltatás akár 20 órányi hang használatát is felhasználhatja a képzéshez. más régiókban a szolgáltatás legfeljebb 8 órát vesz igénybe.

* Kelet-Ausztrália
* Közép-Kanada
* Közép-India
* USA keleti régiója
* USA 2. keleti régiója
* USA északi középső régiója
* Észak-Európa
* USA déli középső régiója
* Délkelet-Ázsia
* Az Egyesült Királyság déli régiója
* USA-beli államigazgatás – Arizona
* USA-beli államigazgatás – Virginia
* Nyugat-Európa
* USA 2. nyugati régiója

Miután létrehozta az Azure-fiókot és a Speech Service-előfizetést, be kell jelentkeznie a [Speech studióba](https://speech.microsoft.com/customspeech) , és hozzá kell kötnie az előfizetését.

1. Jelentkezzen be a [Speech studióba](https://aka.ms/custom-speech).
1. Válassza ki azt az előfizetést, amellyel dolgozni szeretne, és hozzon létre egy beszédfelismerési projektet.
1. Ha módosítani szeretné az előfizetését, kattintson a felső menüben található fogaskerék gombra.

## <a name="how-to-create-a-project"></a>Projekt létrehozása

Az adatokat, modelleket, teszteket és végpontokat például a [Speech Studio](https://speech.microsoft.com/customspeech) *projektjeibe* rendezi. Minden projekt egy tartományra és országra/nyelvre vonatkozik. Létrehozhat például egy olyan projektet a hívási központok számára, amelyek az angol nyelvet használják a Egyesült Államok.

Az első projekt létrehozásához válassza a **beszéd-szöveg/egyéni beszéd** lehetőséget, majd válassza az **új projekt** lehetőséget. A projekt létrehozásához kövesse a varázsló utasításait. A projekt létrehozása után négy lapot kell látnia: **az adatelemzést**, a **tesztelést**, a **betanítást** és a **telepítést**. Az egyes lapok használatának megismeréséhez használja a [következő lépések](#next-steps) hivatkozásait.

> [!IMPORTANT]
> A korábban "Custom Speech portál" néven ismert [Speech Studio](https://aka.ms/custom-speech) nemrég frissült! Ha korábban létrehozott, modelleket, teszteket és közzétett végpontokat hozott létre a CRIS.ai-portálon vagy API-kkal, létre kell hoznia egy új projektet az új portálon a régi entitásokhoz való kapcsolódáshoz.

## <a name="model-and-endpoint-lifecycle"></a>Modell és végpont életciklusa

A régebbi modellek általában kevésbé hasznosak az idő múlásával, mert a legújabb modell általában nagyobb pontossággal rendelkezik. Ezért az alapmodelleket, valamint a portálon keresztül létrehozott egyéni modelleket és végpontokat az 1. év és a dekódolás 2 év után kell lejáratni. Részletes leírást a [modell és a végpont életciklusáról](./how-to-custom-speech-model-and-endpoint-lifecycle.md) szóló cikkben talál.

## <a name="next-steps"></a>Következő lépések

* [Az adatfeldolgozás előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Modell pontosságának kiértékelése és javítása](how-to-custom-speech-evaluate-data.md)
* [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md)
