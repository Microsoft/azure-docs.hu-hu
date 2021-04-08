---
title: Azure Kinect – a törzs nyomon követésének eredményei
description: Ismerje meg, hogyan érheti el a törzs nyomon követésének eredményeit az Azure Kinect Body Tracking SDK használatával.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, szenzor, SDK, szövegtörzs, nyomon követés, közös
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277560"
---
# <a name="get-body-tracking-results"></a>Testkövetési eredmények lekérése

A Body Tracking SDK egy szövegtörzs-követési objektumot használ az Azure Kinect DK rögzítéséhez és a törzs nyomon követési eredményeinek létrehozásához. Emellett a Szemléző globális állapotát, a feldolgozási várólistákat és a kimeneti várólistát is fenntartja. A Body Tracker használatának három lépése van:

- Követő létrehozása
- Részletes és IR-lemezképek rögzítése az Azure Kinect DK használatával
- Sorba helyezni a rögzítést, és ugorja át az eredményeket.

## <a name="create-a-tracker"></a>Követő létrehozása


A törzs nyomon követésének első lépése egy követő követése, amely az érzékelő kalibrációs [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) struktúrájának átadását igényli. Az érzékelő kalibrálása az Azure Kinect Sensor SDK [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) függvény használatával kérdezhető le.

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Részletes és IR-képek rögzítése

A lemezképek rögzítése az Azure Kinect DK használatával a [lemezképek lekérése](retrieve-images.md) oldalon található.

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED``K4A_DEPTH_MODE_WFOV_2X2BINNED`a legjobb teljesítmény és pontosság érdekében ajánlott a módok használata. Ne használja a `K4A_DEPTH_MODE_OFF` vagy a `K4A_DEPTH_MODE_PASSIVE_IR` módot.

A támogatott Azure Kinect DK-módokat az Azure Kinect DK [hardver-specifikációjának](hardware-specification.md) és [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) enumerálásának leírásában találja.

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

## <a name="enqueue-the-capture-and-pop-the-results"></a>A rögzítés sorba helyezni és a találatok beugró ablakának megjelenítése

A Szemléző belsőleg karbantart egy bemeneti várólistát és egy kimeneti várólistát az Azure Kinect DK-rögzítések aszinkron feldolgozásához. A [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) függvény használatával új rögzítést adhat hozzá a bemeneti sorhoz. Az o pop a [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) függvényt használja a kimeneti várólistából. Az időtúllépési érték használata az alkalmazástól függ, és a várakozási időt vezérli.

### <a name="real-time-processing"></a>Valós idejű feldolgozás
Ezt a mintát olyan egyszálas alkalmazásokhoz használja, amelyeknek valós idejű eredményekre van szükségük, és el tudják fogadni az eldobott kereteket. A `simple_3d_viewer` [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) példa a valós idejű feldolgozásra szolgál.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Szinkron feldolgozás
Használja ezt a mintát olyan alkalmazásokhoz, amelyek nem igényelnek valós idejű eredményeket, vagy az eldobott keretek nem helyezhetők el.

A feldolgozási sebesség korlátozott lehet.

A `simple_sample.exe` [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) példa a szinkron feldolgozásra szolgál.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Adatok elérése a testkeretben](access-data-body-frame.md)
