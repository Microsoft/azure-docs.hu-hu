---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832069"
---
### <a name="publish-the-device-template"></a>Az eszközsablon közzététele

Ahhoz, hogy hozzá lehessen adni egy eszközt az alkalmazáshoz, közzé kell tennie az eszköz sablonját:

1. Az **LVA Edge Gateway v2** -eszköz sablonjában válassza a **Közzététel** lehetőséget.

1. Az **eszköz sablonjának közzététele az alkalmazás** lapon válassza a **Közzététel** lehetőséget.

Az **LVA Edge Gateway v2** mostantól eszköz típusaként használható az alkalmazás **eszközök** lapján.

## <a name="migrate-the-gateway-device"></a>Átjáró eszköz migrálása

A meglévő **Gateway-001-** eszköz az **LVA Edge Gateway** -eszköz sablonját használja. Az új üzembe helyezési jegyzékfájl használatához telepítse át az eszközt az új eszköz sablonba:

Az **átjáró-001** eszköz áttelepíthető:

1. Navigáljon az **eszközök** lapra, és válassza ki az **átjáró-001** eszközt, hogy kiemelje azt a listában.

1. Válassza a **Migrate** (Migrálás) lehetőséget. Ha az **áttelepítési** ikon nem látható, válassza a **...** lehetőséget a további beállítások megjelenítéséhez.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Az átjáró eszköz migrálása egy új verzióra":::

1. Az **áttelepítés** párbeszédpanelen válassza az **LVA Edge Gateway v2** elemet, majd kattintson az **áttelepítés** elemre.

Néhány másodperc elteltével az áttelepítés befejeződik. Az eszköz mostantól az **LVA Edge Gateway v2** -eszköz sablonját használja a testreszabott telepítési jegyzékfájl használatával.

Mostantól nem használhatók az eredeti **LVA Edge Gateway** -eszközök sablonja. Az eszköz sablonjának törlése:

1. Navigáljon az **eszközbeállítások** lapra, és válassza ki az **LVA Edge Gateway** -eszköz sablonját.

1. Válassza a **Törlés** lehetőséget az eszköz sablonjának törléséhez.

### <a name="get-the-device-credentials"></a>Az eszköz hitelesítő adatainak beolvasása

Szüksége lesz a hitelesítő adatokra, amelyek lehetővé teszik az eszköz számára a IoT Central alkalmazáshoz való kapcsolódást. Az eszköz hitelesítő adatainak lekérése:

1. Az **eszközök** lapon válassza ki az **átjáró-001** eszközt.

1. Válassza a **Kapcsolódás** lehetőséget.

1. Az **eszköz csatlakoztatása** lapon jegyezze fel az **azonosító hatókörének** *scratchpad.txt* fájlját, az **eszköz azonosítóját** és az eszköz **elsődleges kulcsát**. Ezeket az értékeket később használhatja.

1. Győződjön meg arról, hogy a kapcsolati módszer a **közös hozzáférési aláírásra** van beállítva.

1. Válassza a **Bezárás** gombot.

