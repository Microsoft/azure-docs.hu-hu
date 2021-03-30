---
title: FileUpload FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. FileUpload felhasználói felületi elemének ismertetése. Lehetővé teszi, hogy a felhasználók fájlokat töltsenek fel a felügyelt alkalmazások telepítésekor.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: c4eb3972be85ac7e291380f64127c20424b0d0f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004280"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft. Common. FileUpload felhasználói felületi elem

Olyan vezérlőelem, amely lehetővé teszi, hogy a felhasználó megadhat egy vagy több feltölteni kívánt fájlt.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft-common-fileupload.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

Ha a options. Multiple érték hamis, és a options. uploadMode fájl, akkor a kimenet JSON-sztringként a fájl tartalma:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Ha a options. Multiple érték igaz, a and'options. uploadMode fájl, a kimenet JSON-tömbként jeleníti meg a fájlok tartalmát:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Ha a options. Multiple érték hamis, és a options. uploadMode URL-cím, akkor a kimenet JSON-sztringként URL-címmel rendelkezik:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Ha a options. Multiple értéke igaz, és a options. uploadMode URL-cím, akkor a kimenet JSON-tömbként tartalmazza az URL-címek listáját:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Egy CreateUiDefinition tesztelésekor egyes böngészők (például a Google Chrome) a Microsoft. Common. FileUpload elem által generált URL-címeket csonkítják a böngésző konzolján. Előfordulhat, hogy a teljes URL-címek másolásához a jobb gombbal az egyes hivatkozásokra kell kattintania.

## <a name="remarks"></a>Megjegyzések

- `constraints.accept` meghatározza a böngésző fájljának párbeszédpanelén megjelenő fájlok típusait. Tekintse meg a [HTML5-specifikációt](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) az engedélyezett értékekhez. Az alapértelmezett érték **Null**.
- Ha a `options.multiple` értéke **true (igaz**), a felhasználó több fájlt is kijelölhet a böngésző fájljának párbeszédpanelén. Az alapértelmezett érték: **hamis**.
- Ez az elem támogatja a fájlok a (z) érték alapján történő feltöltését két módban `options.uploadMode` . Ha a **fájl** meg van adva, a kimenet blobként jeleníti meg a fájl tartalmát. Ha meg van adva az **URL-cím** , a rendszer feltölti a fájlt egy ideiglenes helyre, a kimenet pedig a blob URL-címével. Az ideiglenes Blobok 24 óra elteltével törlődnek. Az alapértelmezett érték a **fájl**.
- A feltöltött fájlok védettek. A kimeneti URL-cím tartalmaz egy [sas-tokent](../../storage/common/storage-sas-overview.md?toc=/azure/storage/blobs/toc.json) , amely a fájlnak az üzembe helyezés során való eléréséhez szükséges
- Az érték `options.openMode` határozza meg a fájl olvasásának módját. Ha a fájlnak egyszerű szövegnek kell lennie, **szöveget** kell megadnia; Máskülönben a **bináris** fájlt kell megadnia. Az alapértelmezett érték a **text (szöveg**).
- Ha a `options.uploadMode` értéke **fájl** , és `options.openMode` **bináris** értékre van beállítva, a kimenet Base64 kódolású.
- `options.encoding` Megadja a fájl olvasásakor használandó kódolást. Az alapértelmezett érték az **UTF-8**, és csak akkor használható, ha a értéke `options.openMode` **text (szöveg**).

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
