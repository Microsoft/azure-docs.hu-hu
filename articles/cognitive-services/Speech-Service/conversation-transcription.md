---
title: Beszélgetés átírása (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszélgetés átírása egy olyan megoldás, amely összefoglalja az elismerést, a hangszórók AZONOSÍTÓját és a diarization, és lehetővé teszi bármely beszélgetés átírását.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: trbye
ms.openlocfilehash: 903e8db14a2830236ae81a2a3b5416491d03e8c7
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643254"
---
# <a name="what-is-conversation-transcription-preview"></a>Mi a beszélgetés átírása (előzetes verzió)?

A beszélgetés átirata egy [beszéd-szöveg](speech-to-text.md) típusú megoldás, amely kombinálja a beszédfelismerést, a hangszórók azonosítását és a mondatok kiosztását az egyes hangszórók (más néven _diarization_) számára, hogy valós idejű és/vagy aszinkron átírást nyújtson a beszélgetésekhez. A beszélgetés átirata megkülönbözteti a beszélőket a beszélgetésekben, hogy kiderítse, ki mit és mikor, és hogyan könnyíti meg a fejlesztők számára, hogy beszéd-szövegeket adjanak a több hangszórót használó diarization.

## <a name="key-features"></a>A legfontosabb jellemzők

- **Időbélyegek** – az egyes hangszórók teljes időbélyegzővel rendelkeznek, így könnyen megtalálhatja a kifejezést.
- **Olvasható átiratok** – a átiratok formátuma és az írásjelek automatikusan hozzáadódnak, hogy a szöveg pontosan illeszkedjen a megjelenő szöveghez.
- **Felhasználói profilok** – a felhasználói profilokat a felhasználói hangminták összegyűjtésével és az aláírás-generálásba való küldéssel hozza létre a rendszer.
- **Beszélő azonosítása** – a hangszórók felhasználói profilokkal vannak azonosítva, és mindegyikhez hozzá van rendelve egy _hangszóró-azonosító_ .
- **Több hangszórós diarization** – határozza meg, hogy ki mondta, hogy mi az, amit a hangadatfolyam előállításával látnak el az egyes beszélő azonosítókkal.
- **Valós idejű átírás** – élő átiratokat biztosít arról, hogy kik és mikor zajlik a beszélgetés.
- **aszinkron átírás** – nagyobb pontosságú átiratokat biztosít többcsatornás hangadatfolyam használatával.

> [!NOTE]
> Bár a beszélgetés átirata nem korlátozza a beszélők számát a helyiségben, a 2-10 Beszélők száma munkamenetre van optimalizálva.

## <a name="get-started"></a>Bevezetés

A kezdéshez tekintse [meg a](how-to-use-conversation-transcription.md) valós idejű beszélgetés átiratának rövid útmutatóját.

## <a name="use-cases"></a>Használati esetek

Ahhoz, hogy a találkozók mindenki számára elérhetők legyenek, például a siketek és nagyothallók számára, fontos, hogy valós időben átírást készítsenek. A valós idejű mód beszélgetési átírása a hangfelvételt végzi, és meghatározza, hogy ki a mi, ami lehetővé teszi, hogy az összes értekezlet résztvevője elkövesse az átiratot, és késedelem nélkül részt vehessen az értekezleten.

### <a name="improved-efficiency"></a>Hatékonyabb hatékonyság

Az értekezlet résztvevői az értekezletre összpontosítanak, és nem veszik figyelembe a beszélgetés átírását. A résztvevők aktívan részt vehetnek az értekezleten, és gyorsan követhetik nyomon a következő lépéseket, és nem a jegyzeteket, hanem a találkozó során esetlegesen hiányzókat is felhasználhatják.

## <a name="how-it-works"></a>Működés

Ez magas szintű áttekintést nyújt a beszélgetések átírásának működéséről.

![Az importálási beszélgetés átiratának diagramja](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Várt bemenetek

- **Többcsatornás hangstream** – a specifikáció és a kialakítás részleteiért lásd: [Microsoft Speech Device SDK mikrofon](./speech-devices-sdk-microphone.md). További információért vagy a fejlesztői csomag megvásárlásához lásd: [Microsoft Speech Device SDK](./get-speech-devices-sdk.md)beszerzése.
- **Felhasználói hangminták** – a beszélgetés átirata felhasználói profilokat igényel a beszélgetés előtt. Az egyes felhasználóktól származó hangfelvételeket kell összegyűjtenie, majd a hangfelvételeket az aláírás-létrehozási [szolgáltatásba](https://aka.ms/cts/signaturegenservice) kell küldenie, hogy érvényesítse a hangot, és létrehozza a felhasználói profilokat.

> [!NOTE]
> A felhasználói hangminták megadása nem kötelező. A bevitel nélkül az átírás a különböző előadókat jeleníti meg, de "Speaker1", "Speaker2" néven jelenik meg, és nem az előre regisztrált specifikus hangnévként.


## <a name="real-time-vs-asynchronous"></a>Valós idejű és aszinkron

A beszélgetés átirata háromféle átírási módot kínál:

### <a name="real-time"></a>Valós idejű

A rendszer a hangadatokat élő formában dolgozza fel a beszélő azonosítójának és átiratának visszaadásához. Akkor válassza ezt a módot, ha az átírási megoldás követelménye, hogy a beszélgetés résztvevőinek élő átirattal lássa el a folyamatos beszélgetést. Például egy olyan alkalmazás létrehozása, amely lehetővé teszi, hogy az értekezletek könnyebben elérhetők legyenek a siketek és nagyothallók számára, ideális esetben a valós idejű átíráshoz.

### <a name="asynchronous"></a>Aszinkron

A hangadatok kötegelt feldolgozása a hangszórók azonosítójának és átiratának visszaküldésére szolgál. Válassza ezt a módot, ha az átírási megoldás követelménye, hogy nagyobb pontosságot biztosítson az élő átirat nézet nélkül. Ha például olyan alkalmazást szeretne létrehozni, amely lehetővé teszi a résztvevők számára a kihagyott értekezletek egyszerű feladatának elvégzését, akkor az aszinkron átírási módot használja a nagy pontosságú transzkripciós eredmények eléréséhez.

### <a name="real-time-plus-asynchronous"></a>Valós idejű és aszinkron

A rendszer a hangadatokat élő módon dolgozza fel az előadói azonosító és a átiratok visszaadására, továbbá egy kérést is létrehoz, hogy az aszinkron feldolgozás során is nagy pontosságú átiratot kapjon. Akkor válassza ezt a módot, ha az alkalmazásnak valós idejű átírásra van szüksége, de a beszélgetés vagy az értekezlet utáni használathoz is szükség van egy magasabb pontosságú átiratra.

## <a name="language-support"></a>Nyelvi támogatás

A társalgási átirat jelenleg a következő régiókban támogatja az [összes beszéd és szöveg nyelvét](language-support.md#speech-to-text) :,,  `centralus` `eastasia` `eastus` , `westeurope` . Ha további területi támogatásra van szüksége, lépjen kapcsolatba a [beszélgetés átirata szolgáltatás legénységével](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beszélgetések valós idejű átírása](how-to-use-conversation-transcription.md)
