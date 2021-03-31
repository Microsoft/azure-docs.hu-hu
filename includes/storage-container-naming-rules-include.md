---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3e5afeffa6f47ff79e31f189abc6d5e26d8f2586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050355"
---
Az Azure Storage összes blobjának egy tárolóban kell lennie. A blob nevének egy részét a tároló adja meg. Például ezekben a blob URI-mintákban a `mycontainer` a tároló neve:

```http
https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
```

A tároló nevének egy érvényes DNS-névnek kell lennie, amely megfelel az alábbi elnevezési szabályoknak:

1. A tároló nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.
2. Minden kötőjel előtt és után közvetlenül egy betűnek vagy számnak kell állnia. A tárolók nevében nem szerepelhetnek egymást követő kötőjelek.
3. A tároló nevében szereplő összes betűnek kisbetűnek kell lennie.
4. A tároló nevének 3–63 karakter hosszúságúnak kell lennie.

> [!IMPORTANT]
> Ne feledje, hogy a tároló nevének minden esetben kisbetűsnek kell lennie. Ha a tárolónévbe nagybetűt ír, vagy más módon megsérti a tároló elnevezési szabályait, akkor a rendszer 400-as hibát adhat vissza (Hibás kérés). 
> 
> 

