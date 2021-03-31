---
title: Azure Kinect DK-beli információ elérése szövegtörzsben
description: Ismerje meg a törzs keretén belüli és az adatoknak az Azure Kinect DK-ben való eléréséhez szükséges függvényeket.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: törzs, keret, Azure, Kinect, Body, Tracking, tippek
ms.openlocfilehash: be44f59cb84e99129bf526575293eee69ca64598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277566"
---
# <a name="access-data-in-body-frame"></a>Adatok elérése a testkeretben

Ez a cikk a törzs keretén belül található és az adatok eléréséhez szükséges függvényeket ismerteti.

A következő függvények tartoznak ide:

- [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)
- [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)
- [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)
- [k4abt_frame_get_device_timestamp_usec ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>A szövegtörzs legfontosabb összetevői

Minden törzs kerete tartalmaz egy szövegtörzsből álló gyűjteményt, egy 2D szövegtörzs-index térképet és az eredményt generáló bemeneti rögzítést.

![Body frame-összetevők](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>Hozzáférés a törzsi struktúrák gyűjteményéhez

Egyetlen rögzítésben több törzs is észlelhető. A [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3) függvény meghívásával kérdezheti le a törzsek számát.

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

A [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) és a [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb) függvény használatával megismételheti az egyes törzs-INDEXeket a törzs azonosítójának és a közös pozíció/tájolás információinak megkereséséhez.

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>A szövegtörzs-index megfeleltetésének elérése

A [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6) függvény használatával férhet hozzá a szövegtörzs-index térképhez. A szövegtörzs-index Térkép részletes ismertetését a [törzs indexének térképében](body-index-map.md) találja. Ha már nincs rá szükség, győződjön meg arról, hogy felszabadítja a szövegtörzs-indexelési térképet.

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>Hozzáférés a bemeneti rögzítéshez

A Body tracker egy aszinkron API. Lehetséges, hogy az eredeti rögzítés már megjelent az eredmény beírásának időpontjában. Használja a [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a) függvényt a törzs követési eredményének létrehozásához használt bemeneti rögzítés lekérdezéséhez. A rendszer minden alkalommal megnöveli a k4a_capture_t hivatkozási darabszámát. Ha már nincs szükség a rögzítésre, használja a [k4a_capture_release ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) függvényt.

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Azure Kinect Body Tracking SDK](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
