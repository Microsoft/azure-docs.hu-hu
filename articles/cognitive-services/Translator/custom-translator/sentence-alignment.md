---
title: Mondatok párosítása és igazítása – Custom Translator
titleSuffix: Azure Cognitive Services
description: A betanítás végrehajtása során a párhuzamos dokumentumokban jelenlendő mondatok párosítva vagy igazítva vannak. Custom Translator egy mondatban tanulja meg a fordításokat egy mondat, ennek a mondatnak a fordítása alapján. Ezután igazítja egymáshoz a szavakat és kifejezéseket ebben a két mondatban.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 04/19/2021
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 43268afccbe66a21d2ce78709ba372a8a6682444
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727149"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Mondatpározás és igazítás párhuzamos dokumentumokban

A dokumentumok feltöltése után a párhuzamos dokumentumokban jelenlendő mondatok összepárosodnak vagy igazítva vannak. Custom Translator az egyes adatkészletek igazított mondataiként párosított mondatok számát jelenti.

## <a name="pairing-and-alignment-process"></a>Párosítási és igazítási folyamat

Custom Translator mondatok fordítását tanulja meg egyszerre. Beolvas egy mondatot a forrásszövegből, majd ennek a mondatnak a fordítását a célszövegből. Ezután igazítja egymáshoz a szavakat és kifejezéseket ebben a két mondatban. Ez a folyamat lehetővé teszi, hogy egyetlen mondatban létrehozza a szavak és kifejezések térképét a mondat fordításában található egyenértékű szavakra és kifejezésekre. Az igazítás megkísérli biztosítani, hogy a rendszer olyan mondatokkal edz, amelyek egymás fordításai.

## <a name="pre-aligned-documents"></a>Előre igazított dokumentumok

Ha tudja, hogy párhuzamos dokumentumokkal is van, felülbírálhatja a mondat igazítását az előre igazított szövegfájlok megszüntetésével. Mindkét dokumentumból kinyerheti az összes mondatot szövegfájlba, soronként egy mondatot rendezhet, és feltöltheti egy `.align` kiterjesztéssel. A `.align` bővítmény jelzi Custom Translator hogy kihagyja a mondatok igazítását.

A legjobb eredmény érdekében győződjön meg arról, hogy soronként egy mondat található a fájlokban. Ne tartalmaznak újsor karaktereket egy mondaton belül, mert ez rossz igazítást okoz.

## <a name="suggested-minimum-number-of-sentences"></a>Mondatok javasolt minimális száma

Ahhoz, hogy egy betanítás sikeres legyen, az alábbi táblázat az egyes dokumentumtípusokkal megkövetelt mondatok minimális számát mutatja.Ez a korlátozás egy biztonsági háló, amely biztosítja, hogy a párhuzamos mondatok elegendő egyedi szókészletet tartalmazzanak a fordítási modell sikeres betanítását. Az általános útmutató szerint az emberi fordítás minőségére vonatkozó, tartományon elérhető párhuzamos mondatok jobb minőségű modelleket hoznak létre.

| Dokumentumtípus   | Javasolt mondatok minimális száma | Mondatok maximális száma |
|------------|--------------------------------------------|--------------------------------|
| Oktatás   | 10,000                                     | Nincs felső korlát                 |
| Finomhangolás     | 500                                      | 2500       |
| Tesztelés    | 500                                      | 2500  |
| Szótár | 0                                          | 250,000                 |

> [!NOTE]
>
> - A betanítás nem indul el, és sikertelen lesz, ha a betanítás 10 000 minimális mondatszáma nem teljesül.
> - A finomhangolás és a tesztelés nem kötelező. Ha nem adja meg őket, a rendszer eltávolítja a megfelelő százalékot a Betanításból az ellenőrzéshez és teszteléshez.
> - A modelleket csak szótáradatokkal betaníthatja. Tekintse meg a [Mi az a szótár?](./what-is-dictionary.md)
> - Ha a szótár több mint 250 000 mondatot tartalmaz, a **[Document Translator](https://docs.microsoft.com/azure/cognitive-services/translator/document-translation/overview)** valószínűleg jobb választás.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan használhatja a [szótárakat](what-is-dictionary.md) a Custom Translator.
