---
title: Azure Kinect IMU-minták beolvasása
description: Ismerje meg, hogyan kérhet le Azure Kinect IMU-mintákat az Azure Kinect SDK használatával.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: Kinect, Azure, konfigurálás, mélység, szín, RBG, kamera, érzékelő, SDK, IMU, mozgásérzékelő, Motion, giroszkóp, giroszkóp, gyorsulásmérő, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277291"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Azure Kinect IMU-minták beolvasása

Az Azure Kinect-eszköz hozzáférést biztosít a tehetetlenségi mozgási egységekhez (IMUs), beleértve a gyorsulásmérő és a giroszkóp típusát is. A IMUs-minták eléréséhez először meg kell nyitnia és konfigurálnia kell az eszközt, majd rögzítenie kell a IMU adatait. További információ: [eszköz keresése és megnyitása](find-then-open-device.md).

A IMU-minták sokkal nagyobb gyakorisággal jönnek létre, mint a képek. A mintákat a rendszer a mintavételnél alacsonyabb sebességgel jelenti a gazdagépnek. Egy IMU-minta várakozásakor több minta is gyakran elérhetővé válik egyszerre.

A IMU jelentéskészítési sebességével kapcsolatos részletekért tekintse meg az Azure Kinect DK [hardveres specifikációját](hardware-specification.md) .

## <a name="configure-and-start-cameras"></a>Fényképezőgépek konfigurálása és elindítása

> [!NOTE]
> A IMU érzékelők csak akkor működhetnek, ha a szín és/vagy a mélységmérő kamerák futnak. A IMU érzékelők nem működnek egyedül.

A kamerák elindításához használja a [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)parancsot.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>Hozzáférés IMU-mintákhoz

 Minden [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) tartalmaz egy gyorsulásmérőt és egy giroszkópos olvasót, amely közel azonos időben rögzített.

A IMU-mintákat ugyanazon a szálon szerezheti be, amelyet képrögzítéssel vagy külön szálon is használhat.

Ha a IMU mintákat azonnal le szeretné kérni, érdemes lehet [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) saját szálat meghívni. Az API-nak megfelelő belső Üzenetsor-kezeléssel is rendelkezik, amely lehetővé teszi, hogy csak az egyes képrögzítések visszaadását követően keressen mintákat.

Mivel a IMU-minták belső várólistán vannak, a következő mintát használhatja az adatvesztés nélkül:

1. Várjon egy rögzítést bármilyen képkockán.
2. Dolgozza fel a rögzítést.
3. Az összes aszinkron IMU-minta beolvasása.
4. Ismételje meg a műveletet a következő rögzítésre való várakozáskor.

Az összes jelenleg várólistára vett IMU-minta lekéréséhez a [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) függvényben 0 értéket hívhat meg, `timeout_in_ms` amíg vissza nem tér a függvény `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT` azt jelzi, hogy nincsenek várólistán lévő minták, és egyik sem érkezett meg a megadott időkorlát alatt.

## <a name="usage-example"></a>Használati példa

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan dolgozhat a IMU-mintákkal, lehetősége van arra is, hogy
>[!div class="nextstepaction"]
>[A mikrofon bemeneti adatok elérése](access-mics.md)
