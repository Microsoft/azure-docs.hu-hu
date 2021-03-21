---
title: Fordítás testreszabása – Translator
titleSuffix: Azure Cognitive Services
description: A Microsoft Translator hub használatával saját gépi fordítási rendszereket hozhat létre a kívánt terminológiával és stílussal.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898036"
---
# <a name="customize-your-text-translations"></a>A szöveges fordítások testreszabása

Az egyéni Translator a Translator Service funkciója, amely lehetővé teszi, hogy a felhasználók testre szabják a Microsoft Translator speciális neurális gépi fordítását a fordítók fordításakor (csak a 3. verzióban).

A funkció a beszédfelismerési fordítás testreszabására is használható [Cognitive Services Speech](../speech-service/index.yml)használata esetén.

## <a name="custom-translator"></a>Custom Translator

Az egyéni fordítóval olyan neurális fordítási rendszereket hozhat létre, amelyek megértik a saját üzleti és iparági terminológiáját. A testreszabott fordítási rendszer ezután integrálva lesz a meglévő alkalmazásokhoz, munkafolyamatokhoz és webhelyekhez.

### <a name="how-does-it-work"></a>Hogyan működik?

A korábban lefordított dokumentumok (szórólapok, weblapok, dokumentációk stb.) használatával olyan fordítási rendszer hozható létre, amely a tartományra jellemző terminológiát és stílust tükrözi, ami jobb, mint a szabványos fordítási rendszer. A felhasználók feltölthetik a TMX, a XLIFF, a TXT, a DOCX és az XLSX dokumentumokat.  

A rendszer emellett a dokumentum szintjén párhuzamosan megjelenő olyan adattípusokat is elfogadja, amelyek még nem igazodnak a mondatok szintjéhez. Ha a felhasználók több nyelven is hozzáférnek ugyanahhoz a tartalomhoz, de a különálló dokumentumok egyéni fordítója automatikusan meg tudja feleltetni a mondatokat a dokumentumok között.  A rendszerek a párhuzamos betanítási adatgyűjtést a fordítások tökéletesítése érdekében akár mindkét nyelven is használhatják.

A testreszabott rendszer ezután a Translator paraméter használatával, a fordító rendszeres hívásával érhető el.

A megfelelő típusú és mennyiségű betanítási adat miatt nem ritka, hogy 5 és 10 közötti nyereséget vár, vagy akár több BLEU-pontot is a fordítási minőséghez egyéni Translator használatával.

Az elérhető adatok alapján történő Testreszabás különböző szintjeiről további részleteket az [Egyéni Translator felhasználói útmutatójában](./custom-translator/overview.md)találhat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Testreszabott nyelvi rendszer beállítása egyéni Translator használatával](./custom-translator/overview.md)
