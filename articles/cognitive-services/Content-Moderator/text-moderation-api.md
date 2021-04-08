---
title: Szöveges moderálás – Content Moderator
titleSuffix: Azure Cognitive Services
description: A szöveges moderálás használata a lehetséges nemkívánatos szövegekhez, személyes adatsorokhoz és a kifejezések egyéni listájához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: ae49a8738ba711ac6c77f2e299852ad61f70be56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "92912905"
---
# <a name="learn-text-moderation-concepts"></a>A szöveg moderálásával kapcsolatos fogalmak megismerése

A szöveges tartalom elemzéséhez használja a Content Moderator szövegének moderálási modelljeit.

Megtilthatja, jóváhagyhatja vagy áttekintheti a tartalmakat a szabályzatok és a küszöbértékek alapján (lásd: [értékelések, munkafolyamatok és feladatok,](./review-api.md) amelyekből megtudhatja, hogyan állíthatja be az emberi felülvizsgálatokat). A szöveges moderálási modellek segítségével kibővítheti az olyan környezetek emberi moderálását, ahol a partnerek, az alkalmazottak és a felhasználók szöveges tartalmat hoznak. Ilyen környezetnek minősülnek a csevegőszobák, a vitafórumok, a csevegőrobotok, az e-kereskedelmi katalógusok és a dokumentumok.

A szolgáltatástól érkező válaszban az alábbi információk szerepelnek:

- Káromkodás: a kifejezésen alapuló egyeztetés a különböző nyelveken található, a profán kifejezések beépített listájával
- Besorolás: gépi támogatású besorolás három kategóriába
- Személyes adatok
- Automatikusan kijavított szöveg
- Eredeti szöveg
- Nyelv

## <a name="profanity"></a>Trágár kifejezések

Ha az API bármely [támogatott nyelven](./language-support.md)észleli a profán kifejezéseket, a válasz tartalmazza ezeket a kifejezéseket. A válasz `Index` az eredeti szöveg helyét () is tartalmazza. A `ListId` következő példában szereplő JSON az [Egyéni kifejezési listában](try-terms-list-api.md) található kifejezésekre vonatkozik, ha vannak ilyenek.

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }
```

> [!NOTE]
> A **Language** paraméterhez rendelje hozzá, `eng` vagy hagyja üresen, hogy megjelenjen a gép által támogatott **besorolási** válasz (előzetes verzió funkció). **Ez a funkció csak az angol nyelvet támogatja**.
>
> A **trágár kifejezések** észleléséhez használja a cikkben felsorolt támogatott nyelvek [ISO 639-3 kódját](http://www-01.sil.org/iso639-3/codes.asp) , vagy hagyja üresen.

## <a name="classification"></a>Osztályozás

Content Moderator géppel támogatott **szöveg besorolási funkciója** **csak az angol nyelvet** támogatja, és segít felderíteni a vélhetően nemkívánatos tartalmakat. A megjelölt tartalmat a környezettől függően nem megfelelőként lehet értékelni. Ez az egyes kategóriák valószínűségét közvetíti, és az emberi felülvizsgálatot is javasolhatja. A szolgáltatás egy betanított modellt használ a lehetséges visszaélésszerű, különbözeti vagy diszkriminatív nyelv azonosítására. Ez magában foglalja a szlenget, a rövidített szavakat, a sértőt és a szándékosan hibásan írt szavakat véleményezésre. 

A JSON-kivonat következő kivonata egy példa kimenetet mutat be:

```json
"Classification": {
    "ReviewRecommended": true,
    "Category1": {
        "Score": 1.5113095059859916E-06
    },
    "Category2": {
        "Score": 0.12747249007225037
    },
    "Category3": {
        "Score": 0.98799997568130493
    }
}
```

### <a name="explanation"></a>Magyarázat

- `Category1` olyan nyelv lehetséges jelenlétét jelenti, amely bizonyos helyzetekben szexuálisan explicit vagy felnőttnek tekinthető.
- `Category2` olyan nyelv lehetséges jelenlétére utal, amely bizonyos helyzetekben szexuálisan szuggesztív vagy érett lehet.
- `Category3` olyan nyelv lehetséges jelenlétére utal, amely bizonyos helyzetekben sértőnek minősülhet.
- `Score` 0 és 1 között van. Minél magasabb a pontszám, annál nagyobb a modell, ami azt jelzi, hogy a kategória is alkalmazható. Ez a funkció egy statisztikai modellre támaszkodik, nem pedig manuálisan kódolt eredményeket. Javasoljuk, hogy tesztelje a saját tartalmait annak meghatározásához, hogy az egyes kategóriák hogyan illeszkednek a követelményekhez.
- `ReviewRecommended` értéke igaz vagy hamis, a belső pontszám küszöbértékének függvényében. Az ügyfeleknek fel kell mérniük, hogy ezt az értéket használják-e, vagy egyéni küszöbértékeket határoznak meg a tartalmi szabályzatok alapján.

## <a name="personal-data"></a>Személyes adatok

A személyes adatok funkció észleli az adatok lehetséges jelenlétét:

- E-mail-cím
- USA levelezési címe
- IP-cím
- Egyesült államokbeli telefonszám

A következő példa egy példaként szolgáló választ mutat be:

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>Automatikus javítás

Tegyük fel, hogy a bemeneti szöveg (a "lzay" és a "f0x" szándékos):

> A Qu! CK Brown f0x felugrik a lzay-kutyára.

Ha automatikus javítást kér, a válasz tartalmazza a szöveg javított verzióját:

> A gyors barna róka a lusta kutya fölé ugrik.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>A kifejezések egyéni listájainak létrehozása és kezelése

Habár a kifejezések alapértelmezett globális listája a legtöbb esetben remekül működik, érdemes lehet az üzleti igényeknek megfelelő kifejezéseket használni. Előfordulhat például, hogy a felhasználók által létrehozott bejegyzésekből ki szeretné szűrni a versenytárs márkaneveit.

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

A következő példa a megfelelő lista AZONOSÍTÓját mutatja be:

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }
```

A Content Moderator egy [kifejezés-lista API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) -t biztosít az egyéni kifejezések listáinak kezelésére szolgáló műveletekkel. Kezdje a [kifejezést a lists API-konzolra](try-terms-list-api.md) , és használja a REST API kódot. Tekintse meg a [.net](term-lists-quickstart-dotnet.md) gyors útmutató listáját is, ha ismeri a Visual studiót és a C#-ot.

## <a name="next-steps"></a>Következő lépések

Tesztelje az API-kat a [text moderációs API-konzollal](try-text-api.md). Tekintse meg az [értékeléseket, a munkafolyamatokat és a feladatokat](./review-api.md) , amelyekből megtudhatja, hogyan állíthatja be az emberi felülvizsgálatokat.