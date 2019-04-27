---
title: Translator Text API szótár példák metódus
titlesuffix: Azure Cognitive Services
description: A Translator Text API szótár példák módszert használja.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: d48349b802023d9a05bf14898440837b7793715d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605134"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: Szótár – példák

Példákat talál, amelyek megmutatják, hogyan feltételeket a szótár környezetben használják. Ezzel a művelettel területtel együtt [szótár keresési](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Kérés URL-címe

Küldjön egy `POST` kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A kérelem lekérdezési karakterláncot az átadott paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td>*Kötelező paraméter*.<br/>Az ügyfél által kért API-verzió. Az érték lehet `3.0`.</td>
  </tr>
  <tr>
    <td>forrás:</td>
    <td>*Kötelező paraméter*.<br/>Meghatározza a bemeneti szöveg nyelvét. A Forrásnyelv egyikének kell lennie a [támogatott nyelvek](./v3-0-languages.md) szerepel a `dictionary` hatókör.</td>
  </tr>
  <tr>
    <td>erre:</td>
    <td>*Kötelező paraméter*.<br/>Meghatározza a kimeneti szöveg nyelvét. A Célnyelv egyikének kell lennie a [támogatott nyelvek](./v3-0-languages.md) szerepel a `dictionary` hatókör.</td>
  </tr>
</table>

Kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Hitelesítési fejléc(ek)</td>
    <td><em>Szükséges kérelem fejléce</em>.<br/>Lásd: <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">elérhető lehetőségek a hitelesítéshez</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Szükséges kérelem fejléce*.<br/>Megadja a hasznos tartalom típusát. Lehetséges értékek a következők: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Szükséges kérelem fejléce*.<br/>A kérelem törzsében mennyi.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Választható*.<br/>Egy ügyfél által létrehozott GUID egyedi azonosítására szolgál a kérelmet. Kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban nevű lekérdezési paraméter használatával adja meg a nyomkövetési azonosító `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzsében egy JSON-tömböt. Egyes tömbelemeken egy JSON-objektum a következő tulajdonságokkal:

  * `Text`: Egy olyan karakterláncot kell a keresési kifejezést. Ez az érték legyen egy `normalizedText` vissza a korábbi fordítások a mezőt [szótár keresési](./v3-0-dictionary-lookup.md) kérelmet. Az érték lehet is a `normalizedSource` mező.

  * `Translation`: A lefordított szöveg által korábban visszaadott megadó karakterlánc a [szótár keresési](./v3-0-dictionary-lookup.md) műveletet. Ez legyen az értékét a `normalizedTarget` mezőbe a `translations` listája a [szótár keresési](./v3-0-dictionary-lookup.md) választ. A szolgáltatás visszaadja a megadott forrás-cél word-pár példákat.

A következő egy példa:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 10 elemet is rendelkezhet.
* Egy tömbelem szöveg értéke nem lehet 100 karakternél, beleértve a szóközöket.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömböt az egyes sztringek a bemeneti számtömbből egy eredmény. Egy objektumra a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: Egy karakterlánc, így a forrás kifejezés normalizált formájában. Általában ez az érték azonos legyen a `Text` mezőt a kérelem törzsében a megfelelő listában indexnél.
    
  * `normalizedTarget`: Jogosultságot ad a célként megadott kifejezés normalizált formájában karakterlánc. Általában ez az érték azonos legyen a `Translation` mezőt a kérelem törzsében a megfelelő listában indexnél.
  
  * `examples`: Példák a (forrás időszakra, célként megadott kifejezés) pár. A lista minden eleme egy olyan objektum, a következő tulajdonságokkal:

    * `sourcePrefix`: A karakterlánc összefűzésére _előtt_ értékét `sourceTerm` kialakításához arra az egy teljes példát. Ne adjon hozzá egy szóköz karakter, mert már létezik amikor kell lennie. Ez az érték lehet üres karakterlánc.

    * `sourceTerm`: Egy karakterlánc egyenlő a tényleges kifejezést keresi. A karakterlánc adnak `sourcePrefix` és `sourceSuffix` teljes példa kialakításához. Félkövér stílus szerint, megjelölve egy felhasználói felületen, például hogy az értékét választja el azt.

    * `sourceSuffix`: A karakterlánc összefűzésére _után_ értékét `sourceTerm` kialakításához arra az egy teljes példát. Ne adjon hozzá egy szóköz karakter, mert már létezik amikor kell lennie. Ez az érték lehet üres karakterlánc.

    * `targetPrefix`: Egy karakterlánc hasonló `sourcePrefix` , de a cél.

    * `targetTerm`: Egy karakterlánc hasonló `sourceTerm` , de a cél.

    * `targetSuffix`: Egy karakterlánc hasonló `sourceSuffix` , de a cél.

    > [!NOTE]
    > Ha nincs példák a szótárban, a válasz-e a 200 (OK), de a `examples` listája üres lista lesz.

## <a name="examples"></a>Példák

Ez a példa bemutatja, hogyan példák a pár áll az angol nyelvű kifejezést talált `fly` és a spanyol fordítás `volar`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

A válasz törzsében (rövidítése az átláthatóság érdekében) a következő:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
