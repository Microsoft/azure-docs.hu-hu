---
title: Szöveg fordítása – kognitív képességek
titleSuffix: Azure Cognitive Search
description: Kiértékeli a szöveget, és minden egyes rekord esetében visszaadja a megadott nyelvre lefordított szöveget az Azure Cognitive Search AI-dúsítási folyamatában.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0953d750ee8b59e9889512bb64cfd276a0bbeb53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654864"
---
#   <a name="text-translation-cognitive-skill"></a>Szöveg fordítása – kognitív képességek

A **szöveges fordítási** készség kiértékeli a szöveget, és minden egyes rekord esetében a megadott nyelvre lefordított szöveget adja vissza. Ez a képesség a Cognitive Services elérhető [Translator Text API v 3.0](../cognitive-services/translator/reference/v3-0-translate.md) -s verzióját használja.

Ez a funkció akkor hasznos, ha úgy látja, hogy a dokumentumok nem mindegyike egy nyelven, ebben az esetben a szöveget egyetlen nyelvre is kihasználhatja, mielőtt lefordítja a keresést.  Hasznos lehet a honosítási használati esetekben is, ahol előfordulhat, hogy ugyanaz a szöveg több nyelven is elérhető.

A [Translator Text API v 3.0](../cognitive-services/translator/reference/v3-0-reference.md) egy nem regionális kognitív szolgáltatás, ami azt jelenti, hogy az adatai nem garantáltak abban, hogy az Azure Cognitive Search vagy a csatolt Cognitive Services erőforrással azonos régióban maradjon.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://azure.microsoft.com/pricing/details/search/)találja.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. TranslationSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie, a következőképpen mérve: [`String.Length`](/dotnet/api/system.string.length) . Ha meg kell szüntetnie az adatokat, mielőtt elküldené a szöveg fordítási képességeinek, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bevitelek | Description |
|---------------------|-------------|
| defaultToLanguageCode | Szükséges A dokumentumok olyan dokumentumokhoz való fordítására szolgáló nyelvkód, amelyek nem adják meg explicit módon a nyelvet. <br/> [A támogatott nyelvek teljes listáját](../cognitive-services/translator/language-support.md)itt tekintheti meg. |
| defaultFromLanguageCode | Választható A dokumentumok olyan dokumentumokból való fordítására szolgáló nyelvkód, amelyek nem határozzák meg explicit módon a feladó nyelvét.  Ha a defaultFromLanguageCode nincs megadva, a rendszer az Translator Text API által megadott automatikus nyelvfelismerés alapján határozza meg a feladó nyelvét. <br/> [A támogatott nyelvek teljes listáját](../cognitive-services/translator/language-support.md)itt tekintheti meg. |
| suggestedFrom | Választható A dokumentumok fordításának nyelve, ha sem a fromLanguageCode, sem a defaultFromLanguageCode paraméter nincs megadva, és az automatikus nyelvfelismerés nem sikerült.  Ha a suggestedFrom nyelve nincs megadva, a rendszer az angol (en) nyelvet használja suggestedFrom nyelvként. <br/> [A támogatott nyelvek teljes listáját](../cognitive-services/translator/language-support.md)itt tekintheti meg. |

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név     | Description |
|--------------------|-------------|
| szöveg | A lefordítani kívánt szöveg.|
| toLanguageCode    | Egy karakterlánc, amely azt jelzi, hogy a szöveget milyen nyelven kell lefordítani. Ha nincs megadva ez a bemenet, a rendszer a defaultToLanguageCode fogja használni a szöveg fordításához. <br/>[A támogatott nyelvek teljes listája](../cognitive-services/translator/language-support.md)|
| fromLanguageCode  | A szöveg aktuális nyelvét jelző sztring. Ha ez a paraméter nincs megadva, a rendszer a defaultFromLanguageCode (vagy az automatikus nyelvfelismerés, ha a defaultFromLanguageCode nincs megadva) a szöveg fordítására szolgál. <br/>[A támogatott nyelvek teljes listája](../cognitive-services/translator/language-support.md)|

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve    | Description |
|--------------------|-------------|
| translatedText | A translatedFromLanguageCode és a translatedToLanguageCode közötti szöveg-fordítás karakterláncának eredménye.|
| translatedToLanguageCode  | Egy karakterlánc, amely azt jelzi, hogy a szöveg melyik nyelvkódot lett lefordítva. Akkor hasznos, ha több nyelvre végez fordítást, és szeretné nyomon követni, hogy melyik szöveg milyen nyelven van.|
| translatedFromLanguageCode    | Karakterlánc, amely azt jelzi, hogy a szöveg melyik nyelvkódot lett lefordítva. Akkor hasznos, ha az automatikus nyelvfelismerés beállítást választotta, mivel ez a kimenet az észlelés eredményét biztosítja.|

##  <a name="sample-definition"></a>Minta definíciója

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>Példabemenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>Példakimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések
Ha a from vagy a nyelvhez nem támogatott nyelvi kódot ad meg, a rendszer hibát generál, és a szöveg nincs lefordítva.
Ha a szöveg üres, a rendszer figyelmeztetést készít.
Ha a szöveg 50 000 karakternél nagyobb, akkor csak az első 50 000 karakter lesz lefordítva, és a rendszer figyelmeztetést ad ki.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)