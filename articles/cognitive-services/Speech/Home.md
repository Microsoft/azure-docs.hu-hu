---
title: A Microsoft Bing – beszédszolgáltatás |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Microsoft Speech API használatával beszédvezérlési hozzáadása az alkalmazáshoz, beleértve a felhasználók valós idejű interakciót.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d2c7211831658a18e65e04aa753607f4eb22dac8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515090"
---
# <a name="what-is-bing-speech"></a>Mit jelent a Bing Speech?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A felhőalapú és a Microsoft Bing Speech API biztosít a fejlesztők könnyedén hozhat létre hatékony beszédfeldolgozó szolgáltatásokat biztosítani az alkalmazásokban, például a voice parancsvezérlő, természetes beszéd beszélgetés, és a lejegyzés és a Diktálás felhasználói párbeszédpanel. Microsoft Speech API-t is támogatja *Speech to Text* és *szöveg-beszéd átalakítás* átalakítás.

- **Beszédfelismerés** API emberi beszéd szöveggé, szabályozhatja az alkalmazás bemeneteként vagy parancsok használható alakítja át.
- **Szöveg-beszéd átalakítás** API audiostreamek lejátszásával, a felhasználónak az alkalmazás lejátszhatók szöveget alakítja át.

## <a name="speech-to-text-speech-recognition"></a>Diktálás (beszédfelismerés)

A Microsoft beszédfelismerési API *transcribes* audiostreamek lejátszásával, szöveg, amely az alkalmazás a felhasználót, vagy mint való reagáláshoz bemeneti parancsot. A fejlesztők a beszédfelismerési hozzá alkalmazásaikat két módon biztosít: REST API-k **vagy** Websocket alapuló ügyféloldali kódtáraknál.

- [REST API-k](GetStarted/GetStartedREST.md): A fejlesztők a beszédfelismerési a szolgáltatáshoz az alkalmazások HTTP-hívások.
- [Ügyfélkódtárak](GetStarted/GetStartedClientLibraries.md): Speciális funkciók fejlesztők Microsoft Speech klienskódtárak letöltheti, és hivatkozás alkalmazásokba.  A klienskódtárak érhetők el a különböző platformokon (Windows, Android, iOS) különböző nyelvekhez (C#, Java, JavaScript, ObjectiveC) használatával. A REST API-k, ellentétben a klienskódtárak használatára Websocket-alapú protokoll.

| Használati esetek | [REST API-k](GetStarted/GetStartedREST.md) | [Ügyfélkódtárak](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertálás egy rövid beszélt hangot képes, például parancsokat (hang hossza < 15 mp) ideiglenes eredmények nélkül | Igen | Igen |
| Konvertálja a hosszú hang (> 15 % s) | Nem | Igen |
| A köztes eredményeket kívánt Stream hang | Nem | Igen |
| A LUIS használatával hang tartalomtárolási szöveg ismertetése | Nem | Igen |

Bármelyik módszert a fejlesztők válasszon (REST API vagy klienskódtárak), és a Microsoft beszédfelismerési szolgáltatás a következőket támogatja:

- Speciális speech recognition technológiák a Microsoft, Cortana, a Diktálás Office, Office Translator és más Microsoft-termékek által használt.
- Valós idejű folyamatos felismerése. A beszédfelismerő API lehetővé teszi a felhasználók számára lefényképezze hang valós időben, és támogatja az eddigi felismerhető szavak köztes eredmények szövegre. A beszédfelismerési szolgáltatás end-, beszéd észlelési is támogatja. Emellett a felhasználók választhatnak további formázási lehetőségeket, például a kis-és nagybetűk, és írásjelek, maszkolási cenzúrázása és szöveges normalizálási.
- Támogatja a speech recognition eredményei optimalizált *interaktív*, *beszélgetés*, és *Diktálás* forgatókönyveket. A felhasználói esetek testreszabott nyelvi modell és akusztikai modell, melyekhez [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) lehetővé teszi, hogy az alkalmazás és a felhasználók személyre szabott beszédfelismerési modelleket hozhat létre.
- Több Dialektusok számos használja a beszélt nyelvet támogatnak. Minden egyes felismerés módban támogatott nyelvek teljes listáját lásd: [nyelveket](api-reference-rest/supportedlanguages.md).
- Integráció a language Understanding használatánál. Amellett, hogy a bemeneti audio átalakítása szöveggé, a *Speech to Text* lehetővé teszi a tudni, mi az szöveg azt jelenti, hogy egy további lehetőség. Használja a [Language Understanding Intelligent Service(LUIS)](../LUIS/what-is-luis.md) szándékok és entitások kibontani a felismert szöveget.

### <a name="next-steps"></a>További lépések

- Ismerkedés a Microsoft speech recognition szolgáltatása használandó [REST API-k](GetStarted/GetStartedREST.md) vagy [klienskódtárak](GetStarted/GetStartedClientLibraries.md).
- Tekintse meg [mintaalkalmazások](samples.md) a kedvenc programozási nyelv.
- Nyissa meg a referencia-csoportban található [Microsoft Speech protokoll](API-Reference-REST/websocketprotocol.md) részleteit és API-referenciák.

## <a name="text-to-speech-speech-synthesis"></a>Szöveg-beszéd átalakítás (beszédszintézishez)

*Szöveg-beszéd átalakítás* API-k a REST használata strukturált szöveg átalakítása a audio-adatfolyamokat. Az API-k biztosítanak a gyors szöveg-beszéd átalakítás különböző beszédhangot és nyelveket. Emellett a felhasználók is rendelkeznek a megváltoztathatja hang jellemzőkkel, például írásmódja, a kötet, a terv lényegét stb. címkék használatával SSML.

### <a name="next-steps"></a>További lépések

- Első lépések a Microsoft szöveg-beszéd átalakítás szolgáltatással: [Szöveg-beszéd átalakítás API-referencia](api-reference-rest/bingvoiceoutput.md). Nyelvek és beszédhangot szöveg-beszéd átalakítás által támogatott teljes listáját lásd: [támogatott nyelv és Hangtípust](api-reference-rest/bingvoiceoutput.md#SupLocales).
