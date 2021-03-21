---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-hoz – modell utasítások a parancssori oktatóanyaghoz
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463785"
---
1. **Frissítse a verziószámot**, hogy jelezze, hogy a modell egy frissített verzióját biztosítja. Ezt úgy teheti meg, hogy az érték végén lévő *1* `@id` értéket egy *2* értékre módosítja. A jelenlegi verziószámnál nagyobb szám is fog működni.
1. **Tulajdonság szerkesztése**. Módosítsa a `Humidity` tulajdonság nevét *HumidityLevel* értékre (vagy valami más, ha szeretné. Ha más, mint a *HumidityLevel*-t használja, jegyezze fel, hogy mit használt, és folytatja a használatát a *HumidityLevel* helyett az oktatóanyag során.
1. **Adjon hozzá egy tulajdonságot**. A `HumidityLevel` 15. sorban végződő tulajdonság alatt illessze be a következő kódot, és adjon hozzá egy `RoomName` tulajdonságot a helyiséghez:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Kapcsolat hozzáadása**. Az `RoomName` imént hozzáadott tulajdonság alatt illessze be a következő kódot, hogy az ilyen típusú Twin formátumhoz való képességet a más  ikrekkel való kapcsolatokkal együtt adja hozzá:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Ha elkészült, a frissített modellnek meg kell egyeznie a következővel:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Mielőtt továbblép, ügyeljen arra, hogy mentse a fájlt.