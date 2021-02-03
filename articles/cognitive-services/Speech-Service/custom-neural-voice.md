---
title: Egyéni neurális hangfelismerés – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az egyéni neurális hang egy szöveg-beszéd funkció, amely lehetővé teszi, hogy a saját hangadatainak megadásával hozzon létre egy egyedi, testreszabott szintetikus hanganyagot az alkalmazásaihoz.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 9e230ff4e84eba7b61846540fef2915765fa4f77
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509696"
---
# <a name="what-is-custom-neural-voice"></a>Mi az egyéni neurális hang?

Az egyéni neurális hang egy [szöveg-beszéd](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) (TTS) funkció, amely lehetővé teszi, hogy a saját hangadatainak mintaként való megadásával egy egyedi, testreszabott szintetikus hanganyagot hozzon létre az alkalmazásaihoz. A szöveg és a beszéd úgy működik, hogy a szövegeket szintetikus beszédre konvertálja egy gépi tanulási modell használatával, amely úgy hangzik, mint a kiválasztott hang. A [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)segítségével lehetővé teheti, hogy alkalmazásai [előre elkészített hangokkal](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) vagy saját, az egyéni neurális [hangvezérelt funkcióval kifejlesztett hangmodellekkel](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-prepare-data) beszéljenek. Az egyéni neurális hang olyan neurális TTS technológián alapul, amely természetes hangvételt eredményez, amely gyakran nem különböztethető meg az emberi hanghoz képest.
Az egyéni neurális hang reális és természetes hangja a márkákat, a megtestesít gépeket, valamint lehetővé teszi, hogy a felhasználók természetes módon kommunikálni tudjanak az alkalmazásokkal.

> [!NOTE]
> Az egyéni neurális hangfelismerési funkció regisztrációt igényel, és a hozzáférés a Microsoft jogosultsági és használati feltételei alapján korlátozott. Azok a felhasználók, akik ezt a szolgáltatást szeretnék használni, regisztrálniuk kell a használati eseteiket a [beviteli űrlapon](https://aka.ms/customneural)keresztül.

## <a name="the-basics-of-custom-neural-voice"></a>Az egyéni neurális hang alapjai

Az egyéni neurális hang alapjául szolgáló mögöttes neurális TTS technológia három fő összetevőből áll: a Text Analyzer, a neurális akusztikai modell és a neurális vocoder. Természetes szintetikus beszéd szövegből való létrehozásához először a Text Analyzerbe írja be a szöveget, amely fonéma sorrendbe állítja a kimenetet. A fonéma olyan alapszintű hangegység, amely megkülönbözteti az egyik szót egy másiktól egy adott nyelven. A fonémák egy sora határozza meg a szövegben megadott szavak kiejtéseit. 

Ezután a fonéma-sorozatot bekerül a neurális akusztikai modellbe, hogy előre megjósolja a hangjeleket meghatározó akusztikus funkciókat, például a hangszín, a beszéd stílusa, a sebesség, a hanglejtés és a stressz mintázatát. Végül a neurális vocoder hanghullámokra alakítja át az akusztikai funkciókat, így a szintetikus beszéd létrejött.

![Az egyéni neurális hang bevezető képe.](./media/custom-voice/cnv-intro.png)

A neurális TTS hangmodelleket mély neurális hálózatokkal tanítják ki az emberi hangok rögzítési mintái alapján. Ebben a [blogban](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)leírjuk, hogyan működik a neurális TTS a legkorszerűbb neurális beszédfelismerési modellekkel. A blog azt is ismerteti, hogyan lehet az univerzális alapmodelleket 2 órányi beszédfelismerési adatokkal (vagy kevesebb mint 2 000 rögzített hosszúságú kimondott szöveg) módosítani, és megtanulni, hogy beszéljen a cél beszélő hangján. Ha szeretné megtudni, hogyan tanítják meg a neurális vocoder, olvassa el a [blogbejegyzésben](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Az egyéni neurális hang testreszabási képességeinek köszönhetően a neurális TTS motor alkalmazkodik a felhasználói forgatókönyvek jobb illeszkedéséhez. Egyéni neurális hang létrehozásához a [Speech Studio](https://speech.microsoft.com/customvoice) használatával töltse fel a rögzített hang-és a hozzá tartozó parancsfájlokat, és végezze el a modell betanítását és a hang egyéni végpontra való telepítését. A használati esettől függően az egyéni neurális hang használatával valós időben alakíthatja át a szöveget (például egy intelligens virtuális asszisztensben), vagy offline hangtartalmat (például az e-learning-alkalmazásokban használt hanganyagként vagy az e-learning-alkalmazásokban található utasításokban), a felhasználó által megadott szövegbeviteli adatokkal. Ezt a [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech), a [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-text-to-speech?tabs=script%2Cwindowsinstall&pivots=programming-language-csharp)vagy egy [webes portálon](https://speech.microsoft.com/audiocontentcreation)keresztül lehet elérni.

## <a name="terms-and-definitions"></a>Feltételek és definíciók

| **Időszak**      | **Definíció**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hangmodell   | Egy szöveg-beszéd modell, amely a cél beszélő egyedi vokális jellemzőit képes utánozni. A *hangmodelleket* *hang* -vagy *szintetikus hangként* is nevezzük. A hangmodell olyan bináris formátumú paraméterek halmaza, amelyek nem olvashatók, és nem tartalmaznak hangfelvételeket. Nem vonható vissza az emberi hang hangjának származtatása vagy kiépítése. |
| Hangalapú tehetség  | Azok a személyek vagy célcsoportok, akik hangját rögzítik, és olyan hangmodelleket hoznak létre, amelyek a hangtehetségek hangján alapulnak.                                                                                                                                                                                                                                                   |
| Standard TTS  | A (z) standard vagy "Traditional" metódusa, amely a beszélt nyelvet a fonetikus kódrészletbe bontja, hogy a klasszikus programozási vagy statisztikai módszerek használatával összekeverhető legyen.                                                                                                                                                                                                    |
| Neurális TTS    | A neurális TTS olyan mély neurális hálózatokat használ, amelyekben a fonetikus jelek természetes emberi beszédben vannak kombinálva, nem pedig eljárási programozási vagy statisztikai módszerek használata. A cél hangtehetségek felvételén kívül a neurális TTS olyan forrás-függvénytárat/alapmodellt használ, amely számos különböző beszélőből származó hangfelvételekkel van ellátva.          |
| Betanítási adatok | Egyéni neurális hang betanítási adatkészlete, amely tartalmazza a hangfelvételeket és a kapcsolódó szöveges átírásokat.                                                                                                                                                                                                                                                               |
| Persona       | Egy Persona leírja, hogy kinek van szüksége a hangra. Egy jó persona-kialakítás tájékoztatja az összes hanganyagot, függetlenül attól, hogy egy már létrehozott hangmodellt választ ki, vagy teljesen új hangfelvételt készít.                                                                                                |
| Script        | A szkript egy szövegfájl, amely tartalmazza a hangtehetségek által hosszúságú kimondott szöveg. (A "*hosszúságú kimondott szöveg*" kifejezés magában foglalja a teljes mondatokat és a rövidebb kifejezéseket is.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Az MI felelős használata

Az egyéni neurális hang felelősségteljes használatának megismeréséhez tekintse meg az [átláthatóság megjegyzését](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice). A Microsoft átláthatósági megjegyzései segítenek megérteni, hogyan működik a mesterséges intelligencia technológiája, a választható rendszertulajdonosok pedig befolyásolhatják a rendszerteljesítményt és a működést, valamint a teljes rendszerre való gondolkodás fontosságát, beleértve a technológiát, a személyeket és a környezetet.

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Custom Voice szolgáltatásba](how-to-custom-voice.md)
* [Egyéni hang-végpont létrehozása és használata](how-to-custom-voice-create-voice.md)