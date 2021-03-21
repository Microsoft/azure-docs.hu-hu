---
title: A Custom Speech-Speech szolgáltatás modell-és végpont-életciklusa
titleSuffix: Azure Cognitive Services
description: A Custom Speech alapmodelleket biztosít az alkalmazkodáshoz, és lehetővé teszi, hogy egyéni modelleket hozzon létre az adataiból. Ez a cikk a modellek és a modelleket használó végpontok ütemterveit ismerteti.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555330"
---
# <a name="model-and-endpoint-lifecycle"></a>Modell és végpont életciklusa

Custom Speech az *alapmodelleket* és az *Egyéni modelleket* is használja. Minden nyelvhez egy vagy több alapmodell tartozik. Általánosságban elmondható, hogy amikor egy új beszédfelismerési modellt bocsátanak ki a normál beszédfelismerési szolgáltatásba, azt a rendszer új alapmodellként is importálja a Custom Speech szolgáltatásba. Minden 6 – 12 hónapig frissülnek. A régebbi modellek általában kevésbé hasznosak az idő múlásával, mert a legújabb modell általában nagyobb pontossággal rendelkezik.

Ezzel szemben az egyéni modellek úgy jönnek létre, hogy a kiválasztott alapmodellt az adott ügyfél-forgatókönyvből származó adatokkal igazítják. Egy adott egyéni modellt sokáig használhat, miután az igényei szerint megfelelt. Azt javasoljuk azonban, hogy rendszeresen frissítsen a legújabb alapmodellre, és az idő múlásával újra betanítsa a további adatokkal. 

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

Ha egy alapmodell vagy egy egyéni modell lejár, akkor mindig vissza fog térni a *legújabb alapmodell-verzióra*. Így a megvalósítás soha nem fog megszakadni, de előfordulhat, hogy kevésbé pontosak lesznek a *konkrét adatai* , ha az egyéni modellek elérik a lejáratot. A modell lejáratát a következő helyeken tekintheti meg a Speech Studio Custom Speech területén:

* Modell képzésének összefoglalása
* Modell betanításának részletei
* A központi telepítés összegzése
* Központi telepítés részletei

Íme egy példa a modell betanítási összegzésére:

![Modell képzések összefoglalása ](media/custom-speech/custom-speech-model-training-with-expiry.png) és a modell betanítása részlet oldalról is:

![Modell betanításának részletei](media/custom-speech/custom-speech-model-details-with-expiry.png)

A lejárati dátumokat a JSON- [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) Válasz tulajdonság alatt található és az egyéni beszédfelismerési API-k segítségével is megtekintheti `deprecationDates` .

Az alábbi példa a GetModel API-hívás lejárati adatait szemlélteti. A "DEPRECATIONDATES" a következőket jeleníti meg: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Vegye figyelembe, hogy a modellt egy egyéni beszéd végponton, állásidő nélkül is frissítheti, ha megváltoztatja a végpont által a Speech Studio üzembe helyezés szakaszában vagy a Custom Speech API-n keresztül használt modellt.

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>További források

* [Az adatfeldolgozás előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)