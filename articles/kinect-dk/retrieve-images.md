---
title: Azure-beli Kinect-képadatok beolvasása
description: Ismerje meg, hogyan kérhet le Azure Kinect képadatokat a Kinect Sensor SDK használatával.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, beolvasás, érzékelő, kamera, SDK, mélység, RGB, képek, szín, rögzítés, felbontás, puffer
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "87538914"
---
# <a name="retrieve-azure-kinect-image-data"></a>Azure-beli Kinect-képadatok beolvasása

Ez az oldal részletesen ismerteti, hogyan lehet lemezképeket beolvasni az Azure Kinect-ből. A cikk bemutatja, hogyan rögzítheti és érheti el az eszköz színét és mélységét koordináló képeket. A lemezképek eléréséhez először meg kell nyitnia és konfigurálnia kell az eszközt, majd rögzíthet képeket.
A rendszerkép konfigurálása és rögzítése előtt meg kell [keresnie és meg kell nyitnia az eszközt](find-then-open-device.md).

Az [SDK streaming példáját](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) is megtekintheti, amely bemutatja, hogyan használható a jelen cikkben szereplő függvények használata.

A következő függvények tartoznak ide:

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>Az eszköz konfigurálása és elindítása

A Kinect-eszközön elérhető két kamera több módot, felbontást és kimeneti formátumot is támogat. Teljes listát az Azure Kinect Development Kit [hardveres specifikációi](hardware-specification.md)című témakörben talál.

A folyamatos átviteli konfiguráció a struktúra értékeinek használatával van beállítva [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

A kamerák elindítása után a rendszer továbbra is rögzíti az adatrögzítést, amíg meg [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) nem hívja az eszközt, vagy az eszköz le nem zárul.

## <a name="stabilization"></a>Videóstabilizálási

Ha az eszközöket a többeszközes szinkronizálás funkció használatával indítja el, erősen ajánlott a rögzített expozíciós beállítás használata.
Manuális expozíciós készlet esetén a képek és a frameráta stabilizálása előtt akár nyolc rögzítést is igénybe vehet az eszközről. Az automatikus expozícióval akár 20 rögzítést is eltarthat a képek és a frameráta stabilizálása előtt.

## <a name="get-a-capture-from-the-device"></a>Rögzítés beolvasása az eszközről

A rendszer a képeket korrelált módon rögzíti az eszközről. Minden rögzített kép tartalmaz egy mélységi képet, egy IR-képet, egy színes képet vagy egy kép kombinációját.

Alapértelmezés szerint az API csak akkor ad vissza rögzítést, ha az összes kért lemezképet megkapta a folyamatos átviteli módhoz. Úgy is beállíthatja az API-t, hogy a részleges rögzítéseket csak a mélységi vagy színes képekkel adja vissza, amint az elérhetővé válik a [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) paraméterének törlésével [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

Miután az API sikeresen visszaadott egy rögzítést, meg kell hívnia, [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) Amikor befejezte a rögzítési objektum használatával.

## <a name="get-an-image-from-the-capture"></a>Rendszerkép beolvasása a rögzítésből

A rögzített lemezképek lekéréséhez hívja meg az egyes képtípusok megfelelő függvényét. Az egyik:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

Ha [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) végzett a rendszerkép használatával, a függvények által visszaadott összes leírót meg kell hívnia.

## <a name="access-image-buffers"></a>Hozzáférés a képpufferekhez

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) számos hozzáférő funkcióval rendelkezik a rendszerkép tulajdonságainak lekéréséhez.

A rendszerkép memória-pufferének eléréséhez használja a [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e).

Az alábbi példa azt szemlélteti, hogyan lehet hozzáférni egy rögzített mélységű képhez. Ugyanez az elv más képtípusokra is vonatkozik. Azonban ügyeljen arra, hogy a rendszerkép típusú változót a megfelelő képtípussal cserélje le, például: IR vagy Color.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>Következő lépések

Most már tudja, hogyan rögzítheti és koordinálhatja a kamerák képeit a szín és a mélység között az Azure Kinect-eszköz használatával. A következőket is teheti:

>[!div class="nextstepaction"]
>[IMU-minták lekérése](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[Telefonos hozzáférés](access-mics.md)
