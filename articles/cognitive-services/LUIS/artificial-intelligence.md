---
title: Mesterséges intelligencia (MI)
description: A LUIS mesterséges intelligenciát (AI) használ, hogy az Ön által megadott séma alapján nyelvi megértést nyújtson az adatokhoz.
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 39f73dd002091451ae832516d525499eae98564d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021371"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Mesterséges intelligencia a Language Understandingban (LUIS)

A LUIS mesterséges intelligencia (AI) használatával biztosítja az adatok természetes nyelvi megismerését (NLU) az Ön által megadott séma alapján.

## <a name="natural-language-processing-nlp"></a>Természetes nyelvi feldolgozás (NLP)

A Natural Language Understanding (NLU) a természetes nyelvi feldolgozás (NLP) adott altémaköre.

A természetes nyelvi feldolgozás egy szélesebb körű koncepció, amely a szöveges adatfeldolgozás bármilyen formáját kezeli, így például a következőkre:

* Jogkivonatok létrehozása
* A Speech (POS) címkézésének része
* Szegmentálás
* Morfológiai elemzés
* Szemantikai hasonlóság
* Megbeszélés (Discourse)
* Fordítás

## <a name="natural-language-processing-in-luis"></a>Természetes nyelvi feldolgozás a LUIS-ben

A természetes nyelvi feldolgozás a következő módokon érhető el a LUIS-alkalmazás számára:
* [Természetes nyelvi megértés](#natural-language-processing-nlp) (Luis)
* Konfigurálható NLP-szempontok a LUIS-ben:
    * [Jogkivonatok létrehozása](luis-language-support.md#tokenization)
    * Mellékjelek, írásjelek és Word Forms [API-beállítások](luis-reference-application-settings.md) használatával történő morfológia
* Az egyéb [Cognitive Services](../what-are-cognitive-services.md) által nyújtott lekérdezés előzetes vagy utólagos feldolgozása, például:
    * [Fordítás](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>Természetes nyelvi megértés (NLU)

A NLU lehetővé teszi, hogy egy nyelvi utasítást olyan képviseletre _alakítsanak át_ , amelynek segítségével természetesen megismerheti a felhasználókat. A természetes nyelvi megértés rendkívül nagy kihívást jelent, és _AI-Hard_ problémaként van meghatározva.

A LUIS célja, hogy a szándékra és a kinyerésre összpontosítson, így a következőket is képes azonosítani:
* A felhasználó kívánsága
* Mit beszélnek.

LUIS kevéssé vagy egyáltalán nem ismeri a szélesebb körű _NLP_ -szempontokat, például a szemantikai hasonlóságot, a példákban kifejezett azonosítás nélkül. Például a következő jogkivonatok (szavak) három különböző dolog, amíg nem használják őket hasonló kontextusban a megadott példákban:

* venni
* vásárlása
* vásárolta

## <a name="next-steps"></a>Következő lépések

* Ismerje meg a LUIS-alkalmazás [fejlesztési életciklusát](luis-concept-app-iteration.md)