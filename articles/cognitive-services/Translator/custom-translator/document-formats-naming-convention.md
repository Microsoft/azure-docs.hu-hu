---
title: Dokumentumok formátumai és elnevezési konvenciói – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez egy útmutató a dokumentumok formátumáról és elnevezési konvencióról az egyéni fordítóban. Ez a fogalom segíti a dokumentumok nevének jobb kezelését, és az ütközések elkerülését.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9598d3c1d339d1c37b02892cc83164acae447434
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895849"
---
# <a name="document-formats-and-naming-convention-guidance"></a>A dokumentumok formátuma és elnevezési konvenció útmutatója

Az egyéni fordításhoz használt fájloknak legalább **négy** karakter hosszúnak kell lenniük.

Ez a táblázat a fordítási rendszer összeállításához használható összes támogatott fájlformátumot tartalmazza:

| Formátum            | Bővítmények   | Description                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF. XLIFF | Párhuzamos dokumentum formátuma, fordítási memória-rendszerek exportálása. A használt nyelvek definiálva vannak a fájlon belül.                                                                                                                                                              |
| TMX               | . TMX         | Párhuzamos dokumentum formátuma, fordítási memória-rendszerek exportálása. A használt nyelvek definiálva vannak a fájlon belül.                                                                                                                                                              |
| ZIP               | . ZIP         | A ZIP egy Archív fájlformátum.                                                                                                                                                                                                        |
| Locstudio         | . LCL         | A párhuzamos dokumentumok Microsoft-formátuma                                                                                                                                                                                                                                      |
| Microsoft Word    | . DOCX        | Microsoft Word-dokumentum                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . PDF         | Adobe Acrobat Portable-dokumentum                                                                                                                                                                                                                                                |
| HTML              | . HTML,. HTM  | HTML-dokumentum                                                                                                                                                                                                                                                                  |
| Szövegfájl         | . TXT         | UTF-16 vagy UTF-8 kódolású szövegfájlok. A fájl neve nem tartalmazhat japán karaktereket.                                                                                                                                                                                        |
| Igazított szövegfájl | . IGAZÍTÁS       | A bővítmény `.ALIGN` egy speciális bővítmény, amelyet akkor használhat, ha tudja, hogy a dokumentum pár mondata tökéletesen van igazítva. Ha egy fájlt ad meg `.ALIGN` , az egyéni fordító nem fogja összehangolni a mondatokat. |
| Excel-fájl        | . XLSX        | Excel-fájl (2013 vagy újabb). A táblázat első sorának/sorának nyelvi kódnak kell lennie.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Szótár formátuma

A szótárak esetében az egyéni fordító a betanítási csoportok által támogatott fájlformátumokat támogatja. Ha Excel-szótárt használ, a táblázat első sorának/sorának nyelvi kódoknak kell lennie.

## <a name="zip-file-formats"></a>Zip-fájlformátumok

A dokumentumok egyetlen zip-fájlba csoportosíthatók, és fel is tölthetők. Az egyéni fordító támogatja a zip-fájlformátumokat (ZIP, GZ és TGZ).

A zip-fájlban a TXT, HTML, HTM, PDF, DOCX kiterjesztésű szövegfájlba tartozó minden dokumentumnak követnie kell ezt az elnevezési konvenciót:

{dokumentum neve} \_ {nyelvkód} ahol a (z) {Document name} a dokumentum neve, a (z) {Language Code} az ISO-nyelvazonosítóval (két karakter), amely azt jelzi, hogy a dokumentum mondatokat tartalmaz az adott nyelven. A nyelvi kód előtt aláhúzás (_) értéknek kell szerepelnie.

Ha például két párhuzamos dokumentumot szeretne feltölteni egy zip-fájlon belül egy angolról spanyol rendszerre, a fájlokat "data_en" és "data_es" névvel kell ellátni.

A fordítási memória fájljai (TMX, XLF, XLIFF, LCL, XLSX) nem szükségesek az adott nyelvi elnevezési konvenció végrehajtásához.  

## <a name="next-steps"></a>Következő lépések

- További információ a létrehozásáról és kezeléséről a [projektről](workspace-and-project.md#what-is-a-custom-translator-project) .
