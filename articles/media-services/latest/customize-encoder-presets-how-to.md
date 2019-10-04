---
title: Kódolás a Media Services v3 .NET – Azure használatával egyéni átalakító |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható az Azure Media Services v3 kódolása a .NET használatával egyéni átalakító.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/03/2019
ms.author: juliako
ms.openlocfilehash: 2167a74dc81bdbb2562211cf5c0195a755941d9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148334"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Az egyéni átalakítási – .NET kódolása

Ha az Azure Media Services encoding, ismerkedhet meg gyorsan az, ahogyan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét a [fájlok Streamelési](stream-files-tutorial-with-api.md) oktatóanyag. Az egyedi, amelyekre az adott forgatókönyv vagy eszközkövetelmények készletek is létrehozható.

## <a name="considerations"></a>Megfontolandó szempontok

Egyéni készletek létrehozásakor a következő szempontokat kell figyelembe venni:

* Minden értékét magasságát és szélességét AVC tartalom kezelésére kell költeni 4 többszörösének kell lennie.
* Az Azure Media Services v3-as a kódolási bitsebességre való átkódolása összes bit / másodperc. Ez különbözik a készletek a v2 API-kkal használt egységet kilobit/másodperc. Például ha az átviteli sebesség a v2-ben van megadva, 128 (kilobit/másodperc), a v3-as volna meg akkor 128000 (bit/másodperc).

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a GitHub-adattár, amely tartalmazza a teljes .NET Core-minta a gépre a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Az egyéni előre definiált mintát található a [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) mappát.

## <a name="create-a-transform-with-a-custom-preset"></a>Hozzon létre egy-egy átalakítási egyéni előbeállítás 

Egy új létrehozásakor [átalakítása](https://docs.microsoft.com/rest/api/media/transforms), meg kell adnia a kívánt műveleteket, mint kimenet előállításához. A kötelező paraméter egy [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. A **Előbeállítást** ismerteti, részletes utasításokat a videó és/vagy hang feldolgozási műveletek, amelyeket szeretne létrehozni a kívánt **TransformOutput**. A következő **TransformOutput** hoz létre egyéni kodeket, és a réteg kimeneti beállításait.

[Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható. A Media Services v3 **első** módszerek is szolgálnak az entitásokon **null** Ha az entitás nem létezik (a kis-és ellenőrizze a nevét).

### <a name="example"></a>Példa

Az alábbi példa határozza meg, amelyet szeretnénk hozható létre, ha az átalakítás szolgál. Először hozzáadunk egy AacAudio réteget a hang kódolása és két H264Video réteget a videó kódolásához. A videó rétegekben hogy címkék hozzárendelésére, hogy a kimeneti fájl nevében szereplő használható. Ezután szeretnénk a kimenetet a miniatűrök is tartalmazhatnak. Az alábbi példában képek PNG formátumú, a bemeneti videó felbontása 50 %-át, vagy három időbélyegeket – {25 %-kal, 50 %-os, 75}, a bemeneti videó hosszától egy előállított adjuk meg. Végül adjuk meg a formátum a kimeneti fájlok – egy videó és hang-, a másik pedig a miniatűrök. Mivel több H264Layers rendelkezünk, egyedi nevek rétegenként eredményező makrók van. Azt is, vagy használja a `{Label}` vagy `{Bitrate}` makró-, a példa bemutatja a korábbi.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>További lépések

[Adatfolyam-fájlok](stream-files-tutorial-with-api.md) 
