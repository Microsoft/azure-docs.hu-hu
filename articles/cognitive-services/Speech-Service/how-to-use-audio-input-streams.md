---
title: Speech SDK hangbemeneti stream – fogalmak
titleSuffix: Azure Cognitive Services
description: A Speech SDK hangbemeneti stream API funkcióinak áttekintése.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.custom: devx-track-csharp
ms.openlocfilehash: 87fa97dafe9de4a23f5eaadfd4083cd1ca517cde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026590"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Tudnivalók a Speech SDK hangbemeneti stream API-ról

A Speech SDK **hangbemeneti stream** API a mikrofon vagy a bemeneti fájl API-k használata helyett hangátvitelt biztosít a felismerők számára.

A hangbemeneti streamek használatakor a következő lépések szükségesek:

- A hangadatfolyam formátumának meghatározása. A formátumnak a Speech SDK és a Speech szolgáltatás által támogatottnak kell lennie. Jelenleg csak a következő konfiguráció támogatott:

  A hangminták PCM formátumúak, egy csatorna, 16 bit/minta, 8000 vagy 16000 minta másodpercenként (16000 vagy 32000 bájt/s), két blokk igazítása (16 bit, beleértve a minta kitöltését).

  A hangformátum létrehozásához az SDK-ban található megfelelő kód a következőképpen néz ki:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Győződjön meg arról, hogy a kód biztosítja a nyers hangadatokat ezeknek a specifikációknak megfelelően. Gondoskodjon arról is, hogy a 16 bites minták a kis endian formátumban érkezzenek meg. Az aláírt minták is támogatottak. Ha a hangforrások adatai nem felelnek meg a támogatott formátumoknak, a hangot át kell kódolni a szükséges formátumba.

- Hozzon létre saját hangbemeneti stream-osztályt a következőből származtatva: `PullAudioInputStreamCallback` . A és a tagok implementálása `Read()` `Close()` . A pontos függvény aláírása nyelvtől függ, de a kód a következőhöz hasonlóan fog kinézni:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Hozzon létre egy hangkonfigurációt a hang formátuma és a bemeneti adatfolyam alapján. A felismerő létrehozásakor adja meg a normál beszédfelismerési konfigurációt és a hangbemeneti konfigurációt. Például:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Következő lépések

- [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/cognitive-services/)
- [Lásd: beszéd felismerése a C-ben #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)