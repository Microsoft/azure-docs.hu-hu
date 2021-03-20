---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: dc569050b78a5797808f2e2e000019ba516ba22e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739236"
---
**Interaktív**
- A parancs-és vezérlési forgatókönyvek esetében jelent meg.
- Szegmentálási időtúllépési érték (X).
- Az egyik felismertség végén a szolgáltatás leállítja a hangfeldolgozást a kérelem-AZONOSÍTÓból, és befejezi a bekapcsolást. A rendszer nem zárja be a kapcsolatokat.
- Az elismerés maximális korlátja 20-as.
- A meghívó tipikus széndioxid-hívás `RecognizeOnceAsync` .

**Beszélgetés**
- A már futó felismerések esetében jelent meg.
- A szegmentálási időtúllépési értéke Y. (Y! = X)
- Több teljes hosszúságú kimondott szöveg fog feldolgozni a turn befejezése nélkül.
- A túl sok csend bekapcsolását fogja eredményezni.
- A Carbon továbbra is egy új kérelem-azonosítót fog használni, és szükség szerint visszajátssza a hanganyagot.
- A szolgáltatás 10 percnyi beszédfelismerés után kényszeríti a kapcsolatot.
- A Carbon újrakapcsolódik, és visszafogja a nem nyugtázott hangot.
- Meghívása a szén és a között `StartContinuousRecognition` .

**Diktálás**
- Lehetővé teszi a felhasználók számára a központozás megadását.
- Meghívása a széndioxidban az objektum megadásával, `EnableDictation` `SpeechConfig` az elismerést indító API-hívástól függetlenül.
- Az 1<sup>.</sup> fél fürt a `speech.fragment` közbenső eredményekhez tartozó üzeneteket ad vissza, a 3<sup>Távoli asztali</sup> fél pedig visszaküldi az üzeneteket `speech.hypothesis` .