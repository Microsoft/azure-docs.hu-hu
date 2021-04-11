---
title: Honosítás Azure Media Player
description: Többnyelvű támogatás a nem angol nyelvű felhasználók felhasználói számára.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: e78ff237fb488a995d9161814fe61590fb1c6d5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449840"
---
# <a name="azure-media-player-localization"></a>Honosítás Azure Media Player #

A többnyelvű támogatás lehetővé teszi, hogy a nem angol nyelvű felhasználók natív módon használhassák a lejátszót. A Azure Media Player a nyelvek globális szótárát fogja létrehozni, amely az oldal nyelvén alapuló hibaüzeneteket fogja honosítani. Egy fejlesztő létrehozhat egy kényszerített beállított nyelvet tartalmazó Player-példányt is. Az alapértelmezett nyelv az angol (en).

> [!NOTE]
> Ez a funkció továbbra is egy tesztelésen megy keresztül, és a hibákra is vonatkozik.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

A Azure Media Player jelenleg a következő nyelveket támogatja a megfelelő nyelvi kódokkal:

| Nyelv            | Code | Nyelv                | Code   | Nyelv                | Code         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Angol {default}   | hu   | Horvát                | ó     | Román                | ro           |
| Arab              | AR   | Magyar               | hu     | Szlovák                  | sk           |
| Bolgár           | BG   | Indonéz              | id     | Szlovén                 | SL           |
| Katalán             | CA   | Izlandi               | is     | Szerb – cirillbetűs      | SR-Cyrl-cs   |
| Cseh               | CS   | Olasz                 | Ez     | Szerb – latinbetűs         | sr-latn-rs   |
| Dán              | da   | Japán                | Japán     | Orosz                 | ru           |
| Német              | de   | Kazak                  | KK     | svéd                 | Sv           |
| Görög               | el   | Koreai                  | Ko     | Thai                    | adik           |
| spanyol             | es   | Litván              | lt     | Tagalog                 | TL           |
| Észt            | et   | Lett                 | lv     | Török                 | TR           |
| Baszk              | EU   | Malajziai               | MS     | Ukrán               | uk           |
| Perzsa               | h   | Norvég – BokmÃ ¥ l     | NB     | urdu                    | a           |
| finn             | Fi   | Holland                   | nl     | Vietnámi              | VI           |
| Francia              | fr   | Norvég – nynorsk     | pp     | Egyszerűsített kínai    | zh-Hans      |
| Gallego            | GL   | Lengyel                  | pl     | Kínai – hagyományos   | zh-Hant      |
| héber              | ő   | Portugál - Brazília     | pt-br  |                         |              |
| Hindi               | szia   | Portugál - Portugália   | pt-pt  |                         |              |


> [!NOTE]
> Ha nem szeretné, hogy a honosítása ne történjen meg, az angol nyelvre kell kényszeríteni a nyelvet

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
