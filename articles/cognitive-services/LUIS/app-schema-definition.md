---
title: Alkalmazás sémájának definíciója
description: A LUIS-alkalmazás a vagy a vagy a, `.json` `.lu` és tartalmazza az összes szándékot, az entitásokat, például a hosszúságú kimondott szöveg, a funkciókat és a beállításokat.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327323"
---
# <a name="app-schema-definition"></a>Alkalmazás sémájának definíciója

A LUIS-alkalmazás a vagy a vagy a, `.json` `.lu` és tartalmazza az összes szándékot, az entitásokat, például a hosszúságú kimondott szöveg, a funkciókat és a beállításokat.

## <a name="format"></a>Formátum

Az alkalmazás importálásakor és exportálásakor válassza a vagy a lehetőséget `.json` `.lu` .

|Formátum|Tájékoztatás|
|--|--|
|`.json`| Szabványos programozási formátum|
|`.lu`|A bot Framework [bot Builder eszközei](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)támogatják.|

## <a name="version-7x"></a>7-es verzió. x

* A 7. x verzióra való áttéréskor az entitások beágyazott gépi tanulási entitásként jelennek meg.
* Beágyazott gépi tanulási entitások létrehozása `enableNestedChildren` a következő szerzői API-kkal:
    * [Címke hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Batch-címke hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Címkék áttekintése](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Az entitások végponti lekérdezésének javaslata](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Végponti lekérdezések javaslata a leképezésekhez](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| elem                  | Megjegyzés                              |
|--------------------------|--------------------------------------|
| "hierarchikusak": [],     | Elavult, [gépi tanulási entitások](luis-concept-entity-types.md)használata.   |
| "kompozitok": [],        | Elavult, [gépi tanulási entitások](luis-concept-entity-types.md)használata. [Összetett entitás](reference-entity-composite.md) referenciája. |
| "closedLists": [],       | [Sorolja fel az entitások](reference-entity-list.md) hivatkozását, amelyet elsősorban az entitások szolgáltatásként használ.    |
| "versionId": "0,1",      | A LUIS-alkalmazás verziója.|
| "név": "example-app",   | A LUIS-alkalmazás neve. |
| "desc": "",              | A LUIS-alkalmazás opcionális leírása.  |
| "kulturális környezet": "en-us",      | Az alkalmazás [nyelve](luis-language-support.md) a mögöttes funkciók, például az előre elkészített entitások, a gépi tanulás és a tokenizer hatása.  |
| "tokenizerVersion": "1.0.0", | [Tokenizer](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Pattern.any entitás](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [Reguláriskifejezés-entitás](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [Kifejezések listája (funkció)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  Elavult, [gépi tanulási entitások](luis-concept-entity-types.md)használata. |
| "minták": [],          |  Minták [szintaxisa](reference-pattern-syntax.md) [javítja az előrejelzés pontosságát](luis-concept-patterns.md)   |
| "beállítások": []           | [Alkalmazásbeállítások](luis-reference-application-settings.md)|

## <a name="version-6x"></a>6-os verzió. x

* A 6. x verzióra való áttéréssel az új [gépi tanulási entitást](reference-entity-machine-learned-entity.md) használhatja az entitások ábrázolásához.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>4. x verzió

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Következő lépések

* Migrálás a [v3 authoring API](luis-migration-authoring-entities.md) -kra