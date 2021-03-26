---
title: Beszédfelismerési eszközök SDK-dokumentációja
titleSuffix: Azure Cognitive Services
description: A kibocsátási megjegyzésekben a frissítések, a javítások, a hibajavítások és a Speech Devices SDK módosításai szerepelnek. Ez a cikk a Speech Device SDK minden kiadásával frissül.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: f9215469b1436ce9dc95c140e6969366d80d1ced
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607133"
---
# <a name="release-notes-speech-devices-sdk"></a>Kibocsátási megjegyzések: beszédfelismerési eszközök SDK

A következő fejezetek a legutóbbi kiadások változásait sorolja fel.

## <a name="speech-devices-sdk-1150"></a>Beszédfelismerési eszközök SDK-1.15.0:

- Frissítve az új Microsoft audio stack (MAS) szolgáltatással, amely továbbfejlesztett beamforming és zajcsökkentést biztosít a beszédfelismeréshez.
- A bináris méretet a célként megadott mennyiségtől 70%-kal csökkentve.
- Az [Azure Percept hangjának](../../azure-percept/overview-azure-percept-audio.md) támogatása [bináris kiadással](https://aka.ms/sdsdk-download-APAudio).
- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőjét a 1.15.0 verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.

## <a name="speech-devices-sdk-1110"></a>Beszédfelismerési eszközök SDK-1.11.0:

- [Tetszőleges mikrofonos tömb geometriáinak](how-to-devices-microphone-array-configuration.md) támogatása és a munkaszög beállítása [konfigurációs fájlon](https://aka.ms/sdsdk-micarray-json)keresztül.
- A [URBETTER DDK](http://www.urbetter.com/products_56/278.html)támogatása.
- Megjelent bináris fájlok a [hangsegéd mintában](https://aka.ms/sdsdk-speaker)használt [GGEC-hangszórókhoz](https://aka.ms/sdsdk-download-speaker) .
- Megjelent bináris fájlok a [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) és a [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) -hez a málna PI és hasonló eszközökhöz.
- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőjét a 1.11.0 verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.

## <a name="speech-devices-sdk-190"></a>Beszédfelismerési eszközök SDK-1.9.0:

- A [URBETTER DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) kezdeti bináris fájljai vannak megadva.
- A Roobo v1 mostantól a Mavent használja a Speech SDK-hoz
- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőjét a 1.9.0 verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.

## <a name="speech-devices-sdk-170"></a>Beszédfelismerési eszközök SDK-1.7.0:

- A Linux ARM mostantól támogatott.
- A [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) kezdeti bináris fájljai (Linux ARM64) vannak megadva.
- A Windows-felhasználók használhatják `AudioConfig.fromDefaultMicrophoneInput()` vagy `AudioConfig.fromMicrophoneInput(deviceName)` megadhatják a használni kívánt mikrofont.
- A könyvtár mérete optimalizált.
- Több-turn felismerés támogatása ugyanazzal a beszédfelismerési/szándék-felismerő objektummal.
- Javítsa ki az alkalmi hibát, amikor a folyamat nem válaszol az elismerés leállításakor.
- A minta alkalmazások mostantól egy minta résztvevő. properties fájlt tartalmaznak a fájl formátumának bemutatásához.
- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőjét a 1.7.0 verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.

## <a name="speech-devices-sdk-160"></a>Beszédfelismerési eszközök SDK-1.6.0:

- Az [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) támogatása a Windows és a Linux rendszerben a gyakori [minta alkalmazással](./speech-devices-sdk.md)
- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőjét a 1.6.0 verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.

## <a name="speech-devices-sdk-151"></a>Beszédfelismerési eszközök SDK 1.5.1:

- Belefoglalja a [beszélgetés átírását](./conversation-transcription.md) a minta alkalmazásba.
- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőt a 1.5.1-es verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.

## <a name="speech-devices-sdk-150-2019-may-release"></a>Beszédfelismerési eszközök SDK 1.5.0:2019 – májusi kiadás

- A Speech Devices SDK mostantól már nem egy nem elérhető előzetes verzió.
- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőt a 1.5.0-es verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.
- Az új kulcsszó-technológia jelentős minőségbeli újdonságokat eredményez, lásd: a változtatások megszakítása.
- Új hangfeldolgozási folyamat a továbbfejlesztett távoli mezők felismeréséhez.

**Kompatibilitástörő változások**

- Az új kulcsszó-technológia miatt minden kulcsszót újra létre kell hozni a továbbfejlesztett kulcsszavas portálon. Ha teljesen el szeretné távolítani a régi kulcsszavakat az eszközről, távolítsa el a régi alkalmazást.
  - ADB uninstall com. microsoft. cognitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0:2019 – Apr kiadás

- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőjét a 1.4.0 verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1:2019 – Mar kiadás

- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőt a 1.3.1-es verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.
- Frissített kulcsszavas kezelés: a módosítások megszakítása.
- A minta alkalmazás a beszédfelismerés és a fordítás nyelvének megválasztását is hozzáadja.

**Kompatibilitástörő változások**

- [A kulcsszó telepítése](./custom-keyword-basics.md) egyszerűsített, mostantól az alkalmazás része, és nincs szükség külön telepítésre az eszközön.
- A kulcsszó-felismerés módosult, és két esemény támogatott.
  - `RecognizingKeyword,` azt jelzi, hogy a beszédfelismerési eredmény tartalmaz (nem ellenőrzött) kulcsszavas szöveget.
  - `RecognizedKeyword`azt jelzi, hogy a kulcsszó-felismerés befejeződött az adott kulcsszó felismerése során.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0:2018 – november kiadás

- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőt a 1.1.0 verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.
- A továbbfejlesztett hangfeldolgozási algoritmussal továbbfejlesztettük a beszédfelismerési pontosságot.
- Példa a kínai beszédfelismerési támogatás hozzáadására.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1:2018 – Oct kiadás

- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőt a 1.0.1-es verzióra. További információt a [kibocsátási megjegyzései](./releasenotes.md)című témakörben talál.
- A beszédfelismerés pontossága továbbfejlesztett hangfeldolgozási algoritmussal bővült
- Egy folyamatos felismerési hang-munkamenet hibája javítva lett.

**Kompatibilitástörő változások**

- Ezzel a kiadással számos feltörési változást vezetünk be. Az API-kkal kapcsolatos részletekért tekintse meg [ezt a lapot](https://aka.ms/csspeech/breakingchanges_1_0_0) .
- A kulcsszó-felismerő modell fájljai nem kompatibilisek a Speech Devices SDK 1.0.1-vel. A meglévő kulcsszavas fájlokat a rendszer az új Kulcsszóválasztó fájlok az eszközre való írása után törli.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0:2018 – Aug kiadás

- Javította a beszédfelismerés pontosságát azáltal, hogy kijavít egy hibát a hangfeldolgozási kódban.
- Frissítette a [SPEECH SDK](./speech-sdk.md) összetevőjét a 0.5.0 verzióra. További információt a [kibocsátási megjegyzései](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)című témakörben talál.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733:2018 – májusi kiadás

A Cognitive Services Speech Devices SDK első nyilvános előzetes kiadása.