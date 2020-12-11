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
ms.date: 11/11/2020
ms.author: trbye
ms.custom: contperf-fy21q2
ms.openlocfilehash: be01309fee3454fbd4be78130f9826b493e7bf7a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033765"
---
# <a name="what-is-custom-speech"></a>Mi az a Custom Speech?

A [Custom SPEECH](https://aka.ms/customspeech) felhasználóifelület-alapú eszközök, amelyek segítségével kiértékelheti és javíthatja a Microsoft beszéd-szöveg pontosságát alkalmazásai és termékei számára. Az első lépésekhez mindössze néhány teszt hangfájlra van szüksége. Az ebben a cikkben található hivatkozásokat követve létrehozhat egy egyéni beszéd-szöveg élményt.

## <a name="whats-in-custom-speech"></a>Mi a Custom Speech?

Mielőtt bármit elvégezhet a Custom Speech, szüksége lesz egy Azure-fiókra és egy Speech Service-előfizetésre. A fiók létrehozása után előkészítheti az adatait, betaníthatja és tesztelheti a modelleket, megvizsgálhatja az adatok felismerésének minőségét, kiértékelheti a pontosságot, és végső soron üzembe helyezheti és használhatja az egyéni beszéd – szöveg modellt.

Ez a diagram kiemeli a [Custom Speech portált](https://aka.ms/customspeech)alkotó elemeket. Az alábbi hivatkozásokat követve további információkat tudhat meg az egyes lépésekről.

![Diagram, amely kiemeli a Custom Speech portált alkotó összetevőket.](./media/custom-speech/custom-speech-overview.png)

1. [Előfizetés és projekt létrehozása](#set-up-your-azure-account). Hozzon létre egy Azure-fiókot, és fizessen elő a beszédfelismerési szolgáltatásra. Ez az egyesített előfizetés hozzáférést biztosít a beszéd-szöveg, a szöveg és a beszéd fordításához, valamint a [Custom Speech portálhoz](https://speech.microsoft.com/customspeech). Ezután használja a Speech Service-előfizetést az első Custom Speech projekt létrehozásához.

1. [Tesztelési adatok feltöltése](./how-to-custom-speech-test-and-train.md). A tesztelési adatok (hangfájlok) feltöltésével kiértékelheti az alkalmazásaihoz, eszközeihez és termékeihez tartozó Microsoft beszéd-szöveg típusú ajánlatát.

1. A [felismerés minőségének vizsgálata](how-to-custom-speech-inspect-data.md). A [Custom Speech-portálon](https://speech.microsoft.com/customspeech) visszajátszhatja a feltöltött hangot, és megvizsgálhatja a tesztelési adatok beszédfelismerési minőségét. A mennyiségi mérések esetében lásd: [az adatvizsgálat](how-to-custom-speech-inspect-data.md).

1. [Értékelje és javítsa a pontosságot](how-to-custom-speech-evaluate-data.md). Kiértékelheti és javíthatja a beszéd – szöveg modell pontosságát. A [Custom Speech-portál](https://speech.microsoft.com/customspeech) egy *szó szerinti hibát jelez*, amelynek segítségével meghatározhatja, hogy van-e szükség további képzésre. Ha elégedett a pontossággal, a Speech Service API-jait közvetlenül is használhatja. Ha a pontosságot a relatív átlag 5%-ról 20%-ra szeretné növelni, használja a portál **képzés** lapját további betanítási adatok, például az emberi címkével ellátott átiratok és a kapcsolódó szövegek feltöltéséhez.

1. [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md). A beszédfelismerési modell pontosságának javításához írásos átiratok (10 – 1 000 óra) és a kapcsolódó szöveg (<200 MB), valamint a hang-tesztelési adatok. Ez az érték segít a beszéd – szöveg modell betanításában. A képzés után ellenőrizze az újratesztelést. Ha elégedett az eredménnyel, a modellt üzembe helyezheti egy egyéni végponton.

## <a name="set-up-your-azure-account"></a>Azure-fiók beállítása

Ahhoz, hogy az [Custom Speech portálon](https://speech.microsoft.com/customspeech) egyéni modellt hozzon létre, rendelkeznie kell Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Mindenképpen hozzon létre egy standard (S0) előfizetést. Az ingyenes (F0) előfizetések nem támogatottak.

Miután létrehozta az Azure-fiókot és a Speech Service-előfizetést, be kell jelentkeznie a [Custom Speech-portálra](https://speech.microsoft.com/customspeech) , és csatlakoznia kell az előfizetéséhez.

1. Jelentkezzen be a [Custom Speech portálra](https://aka.ms/custom-speech).
1. Válassza ki azt az előfizetést, amellyel dolgozni szeretne, és hozzon létre egy beszédfelismerési projektet.
1. Ha módosítani szeretné az előfizetését, kattintson a felső menüben található fogaskerék gombra.

## <a name="how-to-create-a-project"></a>Projekt létrehozása

A tartalmak, például az adattípusok, a modellek, a tesztek és a végpontok a [Custom Speech portálon](https://speech.microsoft.com/customspeech)belüli *projektekben* vannak rendszerezve. Minden projekt egy tartományra és országra/nyelvre vonatkozik. Létrehozhat például egy olyan projektet a hívási központok számára, amelyek az angol nyelvet használják a Egyesült Államok.

Az első projekt létrehozásához válassza a **beszéd-szöveg/egyéni beszéd** lehetőséget, majd válassza az **új projekt** lehetőséget. A projekt létrehozásához kövesse a varázsló utasításait. A projekt létrehozása után négy lapot kell látnia: **az adatelemzést**, a **tesztelést**, a **betanítást** és a **telepítést**. Az egyes lapok használatának megismeréséhez használja a [következő lépések](#next-steps) hivatkozásait.

> [!IMPORTANT]
> A [Custom Speech portált](https://aka.ms/custom-speech) nemrég frissítették! Ha korábban létrehozott, modelleket, teszteket és közzétett végpontokat hozott létre a CRIS.ai-portálon vagy API-kkal, létre kell hoznia egy új projektet az új portálon a régi entitásokhoz való kapcsolódáshoz.

## <a name="model-lifecycle"></a>Modell életciklusa

Custom Speech az *alapmodelleket* és az *Egyéni modelleket* is használja. Minden nyelvhez egy vagy több alapmodell tartozik. Általánosságban elmondható, hogy amikor egy új beszédfelismerési modellt bocsátanak ki a normál beszédfelismerési szolgáltatásba, azt a rendszer új alapmodellként is importálja a Custom Speech szolgáltatásba. 3 – 6 havonta frissülnek. A régebbi modellek általában kevésbé hasznosak az idő múlásával, mert a legújabb modell általában nagyobb pontossággal rendelkezik.

Ezzel szemben az egyéni modellek úgy jönnek létre, hogy egy kiválasztott alapmodellt egy adott felhasználói forgatókönyvhöz igazítanak. Egy adott egyéni modellt sokáig használhat, miután az igényei szerint megfelelt. Azt javasoljuk azonban, hogy rendszeresen frissítsen a legújabb alapmodellre, és az idő múlásával újra betanítsa a további adatokkal. 

A modell életciklusával kapcsolatos egyéb kulcsfontosságú feltételek a következők:

* **Alkalmazkodás**: alapmodell készítése és testreszabása a tartományhoz/forgatókönyvhöz szöveges adatok és/vagy hangadatok használatával.
* **Dekódolás**: modell használata és beszédfelismerés végrehajtása (hang kódolása szöveggé).
* **Endpoint (végpont**): egy alapmodell vagy egy egyéni modell felhasználó-specifikus telepítése, amely *csak* egy adott felhasználó számára érhető el.

### <a name="expiration-timeline"></a>Lejárat idővonala

Ahogy az új modellek és az új funkciók elérhetővé válnak, és a régebbi, kevésbé pontos modellek kimaradnak, tekintse meg a modell és a végpont lejáratának következő ütemterveit:

**Alapmodellek** 

* Alkalmazkodás: egy évig elérhető. A modell importálása után az egy évig elérhetővé válik egyéni modellek létrehozásához. Egy év elteltével új egyéni modelleket kell létrehozni az alapmodell újabb verziójából.  
* Dekódolás: az importálás után két évig elérhető. Így létrehozhat egy végpontot, és két évig használhatja a Batch-átírást ezzel a modellel. 
* Végpontok: a dekódolással megegyező idővonalon érhető el.

**Egyéni modellek**

* Dekódolás: a modell létrehozása után két évig elérhető. Így az egyéni modellt két évig használhatja (batch/Realtime/tesztelés) a létrehozása után. Két év elteltével *újra be kell tanítania a modellt* , mert az alapmodell általában elavulttá válik az átalakításhoz.  
* Végpontok: a dekódolással megegyező idővonalon érhető el.

Ha egy alapmodell vagy egy egyéni modell lejár, akkor mindig vissza fog térni a *legújabb alapmodell-verzióra*. Így a megvalósítás soha nem fog megszakadni, de előfordulhat, hogy kevésbé pontosak lesznek a *konkrét adatai* , ha az egyéni modellek elérik a lejáratot. A modell lejáratát a következő helyeken tekintheti meg a Custom Speech-portálon:

* Modell képzésének összefoglalása
* Modell betanításának részletei
* A központi telepítés összegzése
* Központi telepítés részletei

A lejárati dátumokat a JSON- [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) Válasz tulajdonság alatt található és az egyéni beszédfelismerési API-k segítségével is megtekintheti `deprecationDates` .

Vegye figyelembe, hogy az egyéni beszédfelismerési végponton anélkül frissítheti a modellt, hogy az egyéni beszédfelismerési portál üzembe helyezés szakaszában vagy a Custom Speech API-n keresztül megváltoztatja a végpont által használt modellt.

## <a name="next-steps"></a>Következő lépések

* [Az adatfeldolgozás előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Modell pontosságának kiértékelése és javítása](how-to-custom-speech-evaluate-data.md)
* [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md)