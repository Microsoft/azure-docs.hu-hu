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
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385025"
---
# <a name="model-and-endpoint-lifecycle"></a>Modell és végpont életciklusa

A standard (nem testreszabott) beszéd az alapmodelleket meghívó AI-modelleken alapul. A legtöbb esetben egy másik alapmodellt is betanítunk minden olyan beszélt nyelvhez, amelyet támogatunk.  A beszédfelismerési szolgáltatást néhány havonta új alapmodellel frissítjük a pontosság és a minőség javítása érdekében.  
A Custom Speech az egyéni modelleket úgy hozza létre, hogy a kiválasztott alapmodellt az adott ügyfél-forgatókönyv adatai alapján alakítja ki. Miután létrehozta az egyéni modellt, a modell nem frissül és nem módosul, még akkor sem, ha a megfelelő alapmodellt, amelyről a rendszer alkalmazkodik, frissülni fog a standard Speech Service-ben.  
Ez a szabályzat lehetővé teszi egy adott egyéni modell hosszú ideig való használatát az igényeinek megfelelő egyéni modell használata után.  Javasoljuk azonban, hogy rendszeresen hozza létre az egyéni modellt, hogy a legújabb alapmodellből alkalmazkodva kihasználja a jobb pontosságot és minőséget.

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

Az alábbi példa a GetModel API-hívás lejárati adatait szemlélteti. A **DEPRECATIONDATES** akkor jelenik meg, ha a modell lejár: 
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
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
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

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>Mi történik, ha a modellek lejárnak és frissítik a modelleket
Mi történik, ha egy modell lejár, és a modell frissítése a használat módjától függ.
### <a name="batch-transcription"></a>Kötegelt átírás
Ha egy modell lejár, amely a [Batch átíró](batch-transcription.md) transzkripciós kérelmekben használatos, 4xx hiba miatt sikertelen lesz. Ennek elkerüléséhez az `model` **átírási** kérelem törzsében elküldett JSON paramétert egy újabb alapmodellre vagy újabb egyéni modellre kell irányítani. A `model` JSON-bejegyzést is eltávolíthatja, hogy mindig a legújabb alapmodellt használja.
### <a name="custom-speech-endpoint"></a>Egyéni beszédfelismerési végpont
Ha egy modell lejár, amelyet egy [Egyéni beszédfelismerési végpont](how-to-custom-speech-train-model.md)használ, akkor a szolgáltatás automatikusan visszakerül a legújabb alapmodell használatára a használt nyelvhez. , a (z) lehetőségre kattintva kiválaszthatja az **üzembe helyezés** elemet az oldal tetején található **Custom Speech** menüben, majd a részletek megtekintéséhez kattintson a végpont nevére. A Részletek lap tetején megjelenik egy **frissítési modell** gomb, amely lehetővé teszi a végpont által a leállás nélkül használt modell zökkenőmentes frissítését. Ezt a módosítást programozott módon is elvégezheti a [**frissítési modell**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) REST API használatával.

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>További források

* [Az adatfeldolgozás előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)