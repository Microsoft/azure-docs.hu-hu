---
title: Az Azure Speech CLI
titleSuffix: Azure Cognitive Services
description: A Speech CLI egy parancssori eszköz, amellyel a beszédfelismerési szolgáltatás bármilyen kód írása nélkül használható. A beszédfelismerési parancssori felület minimális beállítást igényel, és a beszédfelismerési szolgáltatás legfontosabb funkcióinak kipróbálásával azonnal megtekintheti, hogy a használati esetek teljesíthetők-e.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: ddba62b37c200ade87dbb51042fe0cb084a9ef9a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106122"
---
# <a name="what-is-the-speech-cli"></a>Mi az a Speech parancssori felület?

A Speech CLI egy parancssori eszköz, amellyel a beszédfelismerési szolgáltatás bármilyen kód írása nélkül használható. A beszédfelismerési parancssori felület minimális beállítást igényel, és a beszédfelismerési szolgáltatás legfontosabb funkcióinak kipróbálásával azonnal megtekintheti, hogy a használati esetek teljesíthetők-e. Néhány percen belül futtathat olyan egyszerű tesztelési munkafolyamatokat, mint a Batch Speech-Recognition egy fájlból, vagy szöveges – beszéd típusú sztringek egy fájlból származó karakterláncok gyűjteményében. Az egyszerű munkafolyamatokon túl a Speech CLI éles használatra kész, és akár több folyamat futtatására is alkalmas automatizált `.bat` vagy rendszerhéj-parancsfájlok használatával.

A Speech SDK legtöbb funkciója elérhető a Speech CLI-ben, és néhány speciális funkció és testreszabás egyszerűsített a Speech CLI-ben. Az alábbi útmutatást követve döntse el, mikor kell használni a Speech CLI-t vagy a Speech SDK-t.

A Speech CLI használata:
* A beszédfelismerési szolgáltatás funkcióit minimális telepítéssel szeretné kísérletezni, és nincs kód
* A beszédfelismerési szolgáltatást használó éles alkalmazásokhoz viszonylag egyszerű követelmények vonatkoznak.

A Speech SDK használata:
* Egy adott nyelven vagy platformon belül szeretné integrálni a beszédfelismerési szolgáltatásokat (például C#, Python, C++)
* Olyan összetett követelményekkel rendelkezik, amelyek speciális szolgáltatási kérelmeket igényelhetnek, vagy egyéni viselkedést fejlesztenek, beleértve a válaszadási adatfolyamot is

## <a name="core-features"></a>Alapvető funkciók

* Beszédfelismerés – beszéd – szöveg konvertálása hangfájlokból vagy közvetlenül egy mikrofonból, vagy egy rögzített beszélgetés átírása.

* Beszédfelismerés – a szöveg és a beszéd szöveggé konvertálása szöveges fájlok bevitele vagy közvetlenül a parancssorból való bevitel használatával. [SSML-konfigurációk](speech-synthesis-markup.md), valamint [standard vagy neurális hangok](speech-synthesis-markup.md#neural-and-custom-voices)használatával testreszabhatja a beszédfelismerési kimenet jellemzőit.

* Beszéd fordítása – a hanganyagot szövegre vagy hangra fordíthatja a célnyelv nyelvén.

* Futtatás Azure számítási erőforrásokon – beszédfelismerési CLI-parancsok küldése Azure távoli számítási erőforráson való futtatáshoz a használatával `spx webjob` .

## <a name="get-started"></a>Bevezetés

A beszédfelismerési parancssori felület használatának megkezdéséhez tekintse meg a rövid [útmutatót.](spx-basics.md) Ebből a cikkből megtudhatja, hogyan futtathat néhány alapszintű parancsot, és hogyan jelenítheti meg valamivel több speciális parancsot a Batch-műveletek futtatásához a beszédfelismerés és a szöveg közötti kommunikációhoz. Az alapismeretek című cikk elolvasása után meg kell ismernie a szintaxist, hogy megismerje az egyéni parancsok írását, vagy automatizálja az egyszerű beszédfelismerési szolgáltatás műveleteit.

## <a name="next-steps"></a>Következő lépések

- Ismerkedés a [SPEECH CLI](spx-basics.md) gyors üzembe helyezésével
- [Az adattár konfigurálása](./spx-data-store-configuration.md)
- Megtudhatja, hogyan [futtathat batch-műveleteket a SPEECH CLI](./spx-batch-operations.md) használatával
