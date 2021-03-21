---
title: Beszéd fordításának áttekintése – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés lehetővé teszi a beszéd teljes körű, valós idejű, többnyelvű fordításának hozzáadását az alkalmazásokhoz, eszközökhöz és eszközökhöz. Ugyanez az API használható tolmácsoláshoz és beszéd lefordított szöveggé alakításához is. Ez a cikk áttekintést nyújt a Speech Translation szolgáltatás előnyeiről és képességeiről.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: beszéd fordítása
ms.openlocfilehash: 36c2943d7cd5ce2c5c7c4a8501dc0033aa8c9cbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015205"
---
# <a name="what-is-speech-translation"></a>Mi az a beszédfordítás?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Ebben az áttekintésben megismerheti a beszédfelismerési szolgáltatás előnyeit és képességeit, ami lehetővé teszi a valós idejű, többnyelvű beszédfelismerési és beszéd-szöveges fordítást a hangadatfolyamok számára. A Speech SDK-val az alkalmazások, eszközök és eszközök hozzáférhetnek a forrás-átírásokhoz és a fordítási kimenetekhez a megadott hanghoz. Az ideiglenes átírási és fordítási eredményeket a rendszer beszéd észlelésekor adja vissza, a végső eredmények pedig szintetizált beszédbe alakíthatók.

A Microsoft fordítói motorját két különböző módszer látja el: statisztikai gépi fordítás (SMT) és neurális gépi fordítás (NMT). A SMT speciális statisztikai elemzést használ a lehetséges fordítások megbecslésére néhány szó kontextusában. A NMT-alapú neurális hálózatokkal pontosabb és természetesebb fordításokat biztosítanak a szavak fordításához a mondatok teljes kontextusával.

A Microsoft jelenleg a NMT használja a legnépszerűbb nyelvekre való fordításhoz. A [beszéd-beszéd fordításhoz elérhető összes nyelv](language-support.md#speech-translation) a NMT. A beszédfelismerési és a szöveges fordítás a nyelvi pároktól függően SMT-t vagy NMT-t is használhat. Ha a NMT támogatja a célként megadott nyelvet, a teljes fordítás NMT-alapú. Ha a NMT nem támogatja a célként megadott nyelvet, a fordítás a NMT és az SMT hibrid változata, amely az angol nyelvet használja a két nyelv közötti "pivot" kifejezéssel.

## <a name="core-features"></a>Alapvető funkciók

* Beszéd és szöveg közötti fordítás felismerési eredményekkel.
* Beszéd – beszéd fordítás.
* Több célnyelv fordításának támogatása.
* Az ideiglenes felismerés és a fordítás eredményei.

## <a name="get-started"></a>Bevezetés 

Tekintse [meg a](get-started-speech-translation.md) rövid útmutató lépéseit a beszédfelismerési fordítás megkezdéséhez. A beszédfelismerési szolgáltatás a [SPEECH SDK](speech-sdk.md) és a [Speech CLI](spx-overview.md)használatával érhető el.

## <a name="sample-code"></a>Mintakód

A Speech SDK mintakód a GitHubon érhető el. Ezek a minták olyan gyakori forgatókönyveket foglalnak magukban, mint például a hang olvasása egy fájlból vagy adatfolyamból, a folyamatos és az egylövéses felismerés/fordítás, valamint az egyéni modellek használata.

* [Beszéd – szöveg és fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migrálási útmutatók

Ha alkalmazásai, eszközei vagy termékei a [Translator Speech API](./how-to-migrate-from-translator-speech-api.md)használják, akkor a beszédfelismerési szolgáltatásba való Migrálás megkönnyítéséhez létrehozott útmutatót is készítettünk.

* [Migrálás a Translator Speech APIról a beszédfelismerési szolgáltatásba](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [Beszéd SDK](./speech-sdk.md)
* [Beszédeszközök SDK](speech-devices-sdk.md)
* [REST API: beszéd – szöveg](rest-speech-to-text.md)
* [REST API: szövegről beszédre](rest-text-to-speech.md)
* [REST API: kötegelt átírás és testreszabás](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Következő lépések

* A [beszéd fordításának](get-started-speech-translation.md) befejezése
* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
* [A Speech SDK beszerzése](speech-sdk.md)