---
title: Fordítás konfigurálása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan konfigurálhatja a fordítás különböző lehetőségeit.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: bb90cb3a86acb6a94fa92c33d78ec596659e105f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608699"
---
# <a name="how-to-configure-translation"></a>A fordítás konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a különböző fordítási lehetőségeket a teljes olvasóban.

## <a name="configure-translation-language"></a>Fordítási nyelv konfigurálása

A `options` paraméter tartalmazza az összes olyan jelzőt, amely a fordítás konfigurálására használható. Állítsa be a `language` paramétert arra a nyelvre, amelyet le szeretne fordítani. A támogatott nyelvek teljes listájáért tekintse meg a [nyelvi támogatást](./language-support.md) .

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>A dokumentum automatikus lefordítása betöltéskor

A értékre állítva `autoEnableDocumentTranslation` `true` engedélyezheti a teljes dokumentum automatikus lefordítását, amikor az olvasó betöltődik.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>A Word fordítás automatikus engedélyezése

Állítsa be az lehetőséget `autoEnableWordTranslation` `true` az egyszavas fordítás engedélyezéséhez.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)