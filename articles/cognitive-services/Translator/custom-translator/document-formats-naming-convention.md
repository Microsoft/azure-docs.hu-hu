---
title: A dokumentum-formátumok és elnevezési konvenciók – egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Ez az útmutató, dokumentum-formátumok és egyéni a fordítót az elnevezési konvenciót. A fogalom segítségével kezelheti a dokumentumok nevek jobb abd elnevezések ütközésének elkerülése érdekében.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 0959be545f541da2f0efd5bd1e8cc209ab5a0996
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275414"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Dokumentum-formátumok és -elnevezési konvenciót útmutató

Bármilyen egyéni fordítási használt fájlt kell, hogy **négy** karakternél hosszabb.

Ez a táblázat összes támogatott fájlformátum segítségével hozhat létre a fordítási rendszerét tartalmazza:

| Formátum            | Bővítmények   | Leírás                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF, .XLIFF | Egy párhuzamos formát, exportálás fordítási memória rendszerek. A használt nyelvek a fájlban vannak definiálva.                                                                                                                                                              |
| TMX               | .TMX         | Egy párhuzamos formát, exportálás fordítási memória rendszerek. A használt nyelvek a fájlban vannak definiálva.                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP-archívum fájlformátum.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | A Microsoft formátuma párhuzamos dokumentumok                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | A Microsoft Word-dokumentum                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Az Adobe Acrobat portable dokumentum                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | HTML-dokumentum                                                                                                                                                                                                                                                                  |
| Szövegfájl         | .TXT         | UTF-16 vagy UTF-8 kódolású szöveges fájlok. A fájl neve nem tartalmazhat japán karaktereket.                                                                                                                                                                                        |
| Igazított szövegfájl | . IGAZÍTÁS       | A bővítmény `.ALIGN` egy speciális bővítmény, amelyet használhat, ha tudja, hogy a dokumentum pár a mondatok tökéletesen igazított. Ha megad egy `.ALIGN` fájlban, egyéni a fordítót nem igazodnak a mondatok az Ön számára. |
| Excel-fájl        | .XLSX        | Excel-fájlt (2013 vagy újabb). Az első sor / sorát a táblázatban nyelvkód kell lennie.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Szótár formátumok

A szótárak egyéni a fordítót támogatja a oktatási csoportok támogatottak minden fájlformátum. Ha egy Excel-szótár, az első sor használata, illetve a táblázat sora nyelvkódokról kell lennie.

## <a name="zip-file-formats"></a>Zip-formátumok

Dokumentumok egyetlen zip-fájlban vannak csoportosítva, és feltöltött is. Az egyéni Translator által támogatott formátumok (ZIP GZ és TGZ) zip.

Minden egyes beküldött dokumentumhoz a zip-fájl kiterjesztése a TXT, HTML, HTM-, PDF-, DOCX, IGAZÍTÁS ezt az elnevezési konvenciót kell követnie:

{a dokumentum neve} \_{nyelvkód} ahol a dokumentum neve {dokumentum neve}, {nyelvkód}-e az ISO LanguageID (kettőt), amely azt jelzi, hogy a dokumentum tartalmaz-e azon a nyelven mondatokat. A nyelvi kód előtt aláhúzásjelet (_) kell lennie.

Ha például egy zip belül két párhuzamos dokumentumok feltöltése egy angol, spanyol rendszerre, a fájlok neve "data_en" és "data_es".

Fordítási memória fájlok (TMX, XLF, XLIFF, LCL, XLSX) nem szükségesek az adott nyelv az elnevezési konvenciót követi.  

## <a name="next-steps"></a>További lépések

- Olvassa el a [projekt](workspace-and-project.md#what-is-a-custom-translator-project) hozhat létre és kezelheti azokat.
