---
title: Speech CLI rövid útmutató – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerkedés az Azure Speech CLI-vel. A beszédfelismerési szolgáltatásokkal, például a beszéd szöveggel, a beszéd szöveggel és a beszédfelismeréssel folytatott kommunikációhoz kód írása nélkül is használható.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 53138a22c58e89ade4af234630e9429a19738a6a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556468"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Ismerkedés az Azure Speech CLI-vel

Ebből a cikkből megtudhatja, hogyan használhatja a Speech parancssori felületet a beszédfelismerési szolgáltatásokhoz, például a beszédekhez, szövegekhez és beszédekhez, valamint kód írása nélkül. A Speech CLI éles használatra kész, és felhasználható az egyszerű munkafolyamatok automatizálására a Speech szolgáltatásban, a `.bat` vagy a rendszerhéj parancsfájljainak használatával.

Ez a cikk azt feltételezi, hogy a parancssorból, a terminálból vagy a PowerShellből dolgozik.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Alapvető használat

Ez a szakasz néhány alapszintű SPX-parancsot mutat be, amelyek gyakran hasznosak az első alkalommal történő teszteléshez és kísérletezéshez. Először az alábbi parancs futtatásával tekintse meg az eszközre épített súgót.

```console
spx
```

A súgótémakörök kulcsszava alapján is megkereshetők. Adja meg például a következő parancsot a Speech CLI használati példák listájának megtekintéséhez:

```console
spx help find --topics "examples"
```

A következő parancs megadásával tekintheti meg a felismerési parancs beállításait:

```console
spx help recognize
```

A jobb oldali oszlopban szereplő további Súgó parancsok. Ezeket a parancsokat megadhatja az alparancsokkal kapcsolatos részletes súgó megjelenítéséhez.

## <a name="speech-to-text-speech-recognition"></a>Beszéd – szöveg (beszédfelismerés)

A beszédfelismerési parancssori felület használatával alakítsa át a beszédet szöveggé (beszédfelismerés) a rendszer alapértelmezett mikrofonjának használatával. A parancs beírása után az SPX megkezdi a hang figyelését az aktuális aktív bemeneti eszközön, és leállítja az **ENTER** billentyűt. A rendszer ezután felismeri és átalakítja a rögzített beszédet a konzol kimenetében lévő szövegre.

>[!IMPORTANT]
> Ha Docker-tárolót használ, a `--microphone` nem fog működni.

Futtassa ezt a parancsot:

```console
spx recognize --microphone
```

A Speech CLI használatával hangfájlból is felismerheti a beszédet.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Ha egy Docker-tárolóban lévő hangfájlból készít beszédet, győződjön meg arról, hogy a hangfájl az előző lépésben csatlakoztatott könyvtárban található.

Ne felejtse el, ha elakad, vagy szeretne többet megtudni a Speech CLI felismerési lehetőségeiről, csak gépelje be a következőt:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Szöveg-beszéd (Speech szintézis)

Az alábbi parancs futtatásával bemenetként fog megjelenni a szöveg, és a szintetizált beszédet a jelenlegi aktív kimeneti eszközre (például a számítógép-hangszórókra) is kiírja.

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

A szintetizált kimenet fájlba is menthető. Ebben a példában egy nevű fájlt hozunk létre a `my-sample.wav` könyvtárban, amelyet a parancs futtat.

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

Ezek a példák azt feltételezik, hogy az angol nyelvű tesztelést végzi. A beszédfelismerést azonban számos nyelven támogatjuk. Ezzel a paranccsal lekérheti a hangok teljes listáját, vagy megkeresheti a [nyelvi támogatás lapot](./language-support.md).

```console
spx synthesize --voices
```

A felderített hangok egyikét használja.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Ne felejtse el, ha elakad, vagy szeretne többet megtudni a Speech CLI szintézisi lehetőségeiről, csak gépelje be a következőt:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Beszéd – szöveg fordítása

A beszédfelismerési parancssori felülettel a szöveg fordítását is elvégezheti. Futtassa ezt a parancsot az alapértelmezett mikrofon hangjának rögzítéséhez, és szövegként adja ki a fordítást. Ne feledje, hogy a és a nyelvet a paranccsal kell megadnia `source` `target` `translate` .

```console
spx translate --microphone --source en-US --target ru-RU
```

Több nyelvre való fordításkor külön nyelvi kódokat kell megválasztani `;` .

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Ha menteni szeretné a fordítás kimenetét, használja a `--output` jelzőt. Ebben a példában egy fájlból is beolvasunk egy fájlt.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Tekintse meg az összes támogatott nyelv listáját a [nyelvi és területi beállításban](language-support.md) a megfelelő területi beállításokkal.

Ne felejtse el, ha elakad, vagy szeretne többet megtudni a Speech CLI fordítási lehetőségeiről, csak gépelje be a következőt:

```console
spx help translate
```

## <a name="next-steps"></a>Következő lépések

* [A Speech parancssori felület konfigurációs beállításai](./spx-data-store-configuration.md)
* [Batch-műveletek a Speech CLI-vel](./spx-batch-operations.md)
