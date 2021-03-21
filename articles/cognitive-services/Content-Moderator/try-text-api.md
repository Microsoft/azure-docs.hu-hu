---
title: Mérsékelt szöveg a szöveges moderálási API használatával – Content Moderator
titleSuffix: Azure Cognitive Services
description: Tesztelje a szöveg moderálását a Text moderációs API használatával az online konzolon.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002000"
---
# <a name="moderate-text-from-the-api-console"></a>Mérsékelt szöveg az API-konzolból

Az Azure Content Moderator [szöveges moderálási API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) -ját használva megvizsgálhatja a szöveges tartalmakat, és összehasonlíthatja azokat az egyéni és a megosztott listával.

## <a name="get-your-api-key"></a>Az API-kulcs beszerzése

Mielőtt tesztelni tudja az API-t az online konzolon, szüksége lesz az előfizetési kulcsra. Ez a **Beállítások** lap **OCP-APIM-előfizetés-Key** mezőjében található. További információkért lásd az [Áttekintést](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navigáljon az API-referenciához

Nyissa meg a [szöveges MODERÁLÁS API-referenciáját](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Megnyílik a **szöveges képernyő** oldal.

## <a name="open-the-api-console"></a>Az API-konzol megnyitása

Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

  ![Szöveg-képernyő oldal régió kiválasztása](images/test-drive-region.png)

  Megnyílik a **text-Screen** API-konzol.

## <a name="select-the-inputs"></a>A bemenetek kiválasztása

### <a name="parameters"></a>Paraméterek

Válassza ki a szöveges képernyőn használni kívánt lekérdezési paramétereket. Ebben a példában az alapértelmezett értéket használja a **Language (nyelv**) beállításnál. Azt is üresen hagyhatja, mert a művelet végrehajtása során a rendszer automatikusan felismeri a valószínűsíthető nyelvet.

> [!NOTE]
> A **Language** paraméterhez rendelje hozzá, `eng` vagy hagyja üresen, hogy megjelenjen a gép által támogatott **besorolási** válasz (előzetes verzió funkció). **Ez a funkció csak az angol nyelvet támogatja**.
>
> A **trágár kifejezések** észleléséhez használja a cikkben felsorolt támogatott nyelvek [ISO 639-3 kódját](http://www-01.sil.org/iso639-3/codes.asp) , vagy hagyja üresen.

Az **Automatikus javítás** **, a** személyes adatok és a **besorolás (előzetes verzió)** beállításnál válassza az **igaz** lehetőséget. Hagyja üresen a **ListId** mezőt.

  ![Szöveges képernyős konzol lekérdezési paraméterei](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tartalomtípus

A **Content-Type (tartalom típusa**) mezőben válassza ki a képernyőn megjelenő tartalom típusát. Ebben a példában az alapértelmezett **text/plain** tartalomtípust használjuk. Az **OCP-APIM-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

### <a name="sample-text-to-scan"></a>Vizsgálandó mintaszöveg

A **kérelem törzse** mezőbe írjon be egy szöveget. Az alábbi példa egy szándékos elírást mutat be a szövegben.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>A válasz elemzése

Az alábbi válasz az API különböző bepillantást jeleníti meg. A szolgáltatás tartalmazhatja a lehetséges trágár, személyes és besorolást (előzetes verzió) és az automatikusan javított verziót.

> [!NOTE]
> A géppel támogatott "besorolás" funkció előzetes verzióban érhető el, és csak az angol nyelvet támogatja.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

A JSON-válasz összes részének részletes ismertetését a [szöveges moderálás](text-moderation-api.md) fogalmi útmutatójában találja.

## <a name="next-steps"></a>Következő lépések

Használja a kódban szereplő REST API, vagy kövesse a [.net SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) gyors üzembe helyezését az alkalmazással való integráláshoz.