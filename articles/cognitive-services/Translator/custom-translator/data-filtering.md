---
title: Adatszűrés – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ha egy egyéni rendszer betanítására szolgáló dokumentumokat küld be, a dokumentumok a betanításra való felkészüléshez szükséges feldolgozási és szűrési lépések sorozatán mennek keresztül.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 53dea20e356f735a521dec8c22edf8cb2aa7122d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895866"
---
# <a name="data-filtering"></a>Adatszűrés

Ha egy egyéni rendszer betanítására szolgáló dokumentumokat küld be, a dokumentumok a betanításra való felkészüléshez szükséges feldolgozási és szűrési lépések sorozatán mennek keresztül. Ezeket a lépéseket itt találja. A szűrés ismerete segíthet megérteni az egyéni fordítóban megjelenő mondatok számát, valamint azokat a lépéseket, amelyeket a dokumentumok előkészítéséhez használhat egyéni fordítóval.

## <a name="sentence-alignment"></a>Mondatok igazítása
Ha a dokumentum nem XLIFF-, TMX-vagy IGAZÍTÁSi formátummal rendelkezik, az egyéni fordító a forrás-és a dokumentumok mondatait a mondatok szerint rendezi. Az egyéni fordító nem végez dokumentum-igazítást – a dokumentumok elnevezését követi, hogy megtalálja a többi nyelv megfelelő dokumentumát. A dokumentumon belül az egyéni fordító megpróbálja megkeresni a megfelelő mondatot a másik nyelven. A dokumentum-jelölés, például a beágyazott HTML-címkék használatával segíti az igazítást.  

Ha a forrás-és a cél oldali dokumentumokban a mondatok száma között nagy eltérés tapasztalható, akkor előfordulhat, hogy a dokumentum nem párhuzamos az első helyen, vagy más okok miatt nem lehetett igazítani. A dokumentum párok nagy különbséggel (>10%) mindkét oldalon a mondatok egy második pillantással gondoskodnak arról, hogy valóban párhuzamosak legyenek. Ha a mondatok száma gyanús, a Custom Translator egy figyelmeztetést jelenít meg a dokumentum mellett.  


## <a name="deduplication"></a>Deduplikáció
Az egyéni fordító eltávolítja azokat a mondatokat, amelyek jelen vannak a betanítási adatokból a tesztek és a dokumentumok finomhangolása során.Az Eltávolítás a betanítási folyamaton belül dinamikusan történik, nem az adatfeldolgozási lépésben. Az egyéni fordító az Eltávolítás előtt jelentést készít a mondatok számáról a projekt áttekintésében.  

## <a name="length-filter"></a>Hossz szűrő
* A mondatokat csak egyetlen szóhoz távolíthatja el mindkét oldalon.
* Mindkét oldalon több mint 100 szót tartalmazó mondatokat távolíthat el.A kínai, Japán és koreai nyelvekre nem vonatkozik kivétel.
* 3 karakternél rövidebb mondatok eltávolítása. A kínai, Japán és koreai nyelvekre nem vonatkozik kivétel.
* Több mint 2000 karakterből álló mondatok eltávolítása kínai, Japán és koreai nyelveken.
* Távolítsa el a mondatokat 1%-nál kevesebb alfanumerikus karakternél.
* Távolítsa el a több mint 50 szót tartalmazó szótár-bejegyzéseket.

## <a name="white-space"></a>Térköz
* Cserélje le a fehér szóközök tetszőleges sorozatát, beleértve a tabulátorokat és a CR/LF-sorozatokat egyetlen szóköz karakterrel.
* A mondat kezdő vagy záró területének eltávolítása

## <a name="sentence-end-punctuation"></a>Mondat záró központozás
Több mondatos záró írásjelet cseréljen le egyetlen példánnyal.  

## <a name="japanese-character-normalization"></a>Japán karakterek normalizálása
Teljes szélességű betűk és számjegyek konvertálása félszélességű karakterekké

## <a name="unescaped-xml-tags"></a>Nem kimaradt XML-címkék
A szűrés a nem kimaradt címkéket Escape-címkékre alakítja át:
* `&lt;` válik `&amp;lt;`
* `&gt;` válik `&amp;gt;`
* `&amp;` válik `&amp;amp;`

## <a name="invalid-characters"></a>Érvénytelen karakterek
Az egyéni fordító eltávolítja az U + FFFD Unicode-karaktert tartalmazó mondatokat. Az U + FFFD karakter nem megfelelő kódolási konverziót jelez.

## <a name="next-steps"></a>Következő lépések

- [Modell betanítása](how-to-train-model.md) egyéni fordítóban.
