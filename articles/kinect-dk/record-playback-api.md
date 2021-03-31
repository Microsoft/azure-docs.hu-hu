---
title: Azure Kinect lejátszási API
description: Megtudhatja, hogyan nyithat meg egy rögzítő fájlt a lejátszási API használatával az Azure Kinect Sensor SDK használatával.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, szenzor, SDK, mélység, RGB, rekord, lejátszás, Matroska, MKV
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277271"
---
# <a name="the-azure-kinect-playback-api"></a>Az Azure Kinect lejátszási API-ját

Az Sensor SDK egy API-t biztosít az eszköz-és a Matroska (. mkv) fájlba való rögzítéshez. A Matroska tároló formátuma a video tracks, a IMU-mintákat és az eszközök kalibrálását tárolja. A felvételek a megadott [k4arecorder](record-sensor-streams-file.md) parancssori segédprogram használatával hozhatók létre. A felvételek testreszabhatók, és közvetlenül a Record API használatával is rögzíthetők.

A rögzítési API-val kapcsolatos további információkért lásd: [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

A Matroska fájlformátumokra vonatkozó specifikációkkal kapcsolatos további információkért lásd a [fájl rögzítése](record-file-format.md) lapot.

## <a name="use-the-playback-api"></a>A lejátszási API használata

A rögzítési fájlok megnyithatók a lejátszási API használatával. A lejátszási API az érzékelői adatokhoz ugyanolyan formátumú hozzáférést biztosít, mint a többi érzékelő SDK-val.

### <a name="open-a-record-file"></a>Fájl megnyitása

Az alábbi példában megnyitunk egy felvételt a használatával [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) , kinyomtatjuk a felvétel hosszát, majd a fájlt a következővel zárjuk be: [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>Rögzítések olvasása

A fájl megnyitása után megkezdheti a rögzítések olvasását. A következő példa a fájlban lévő összes rögzítést beolvassa.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>Keresés a rögzítésen belül

Ha elérte a fájl végét, érdemes lehet visszalépnie, és újra beolvasni. Ezt a folyamatot a visszafelé való olvasással lehet elvégezni [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) , de a rögzítés hosszától függően nagyon lassú lehet.
Ehelyett használhatja a [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) függvényt a fájl egy adott pontjára való ugráshoz.

Ebben a példában a másodpercenkénti időbélyegzőket adjuk meg a fájl különböző pontjainak kereséséhez.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>Címke információinak olvasása

A felvételek különböző metaadatokat is tartalmazhatnak, például az eszköz sorozatszámát és a belső vezérlőprogram verzióit. A metaadatokat a rendszer a függvény használatával elérő címkékben tárolja [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) .

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>Rekordok listájának rögzítése

Az alábbi lista felsorolja az összes olyan alapértelmezett címkét, amely szerepelhet a rögzítési fájlban. Ezen értékek közül sok elérhető a struct részeként [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) , és a függvénnyel is olvasható [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) .

Ha a címke nem létezik, a rendszer azt feltételezi, hogy az alapértelmezett értékkel rendelkezik.

| Címke neve                     | Alapértelmezett érték      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) Mező | Jegyzetek     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | KIKAPCSOLÁSA              | `color_format` / `color_resolution`  | Lehetséges értékek: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P" stb.                                      |
| `K4A_DEPTH_MODE`             | KIKAPCSOLÁSA              | `depth_mode` / `depth_track_enabled` | Lehetséges értékek: "OFF", "NFOV_UNBINNED", "PASSIVE_IR" stb.                                                |
| `K4A_IR_MODE`                | KIKAPCSOLÁSA              | `depth_mode` / `ir_track_enabled`    | Lehetséges értékek: "OFF", "ACTIVE", "passzív"                                                                    |
| `K4A_IMU_MODE`               | KIKAPCSOLÁSA              | `imu_track_enabled`                  | Lehetséges értékek: "ON", "OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.jsbekapcsolva" | N/A                                  | Lásd [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | 0                | `depth_delay_off_color_usec`         | A nanoszekundumban-ben tárolt érték (API) másodperceket biztosít.                                                        |
| `K4A_WIRED_SYNC_MODE`        | ÖNÁLLÓ       | `wired_sync_mode`                    | Lehetséges értékek: "STANDALONE", "MASTER", "ALÁRENDELT"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | 0                | `subordinate_delay_off_master_usec`  | A nanoszekundumban-ben tárolt érték (API) másodperceket biztosít.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | N/A                                  | Eszköz színének belső vezérlőprogram-verziója, például "1. x. xx"                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | N/A                                  | Az eszköz részletes belső vezérlőprogram-verziója, például "1. x. xx"                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | N/A                                  | Eszköz sorozatszámának rögzítése                                                                                 |
| `K4A_START_OFFSET_NS`        | 0                | `start_timestamp_offset_usec`        | Lásd alább az [időbélyeg-szinkronizálást](record-playback-api.md#timestamp-synchronization) .                       |
| `K4A_COLOR_TRACK`            | Nincsenek               | N/A                                  | Lásd: a [fájl formátumának rögzítése – számok azonosítása](record-file-format.md#identifying-tracks).                     |
| `K4A_DEPTH_TRACK`            | Nincsenek               | N/A                                  | Lásd: a [fájl formátumának rögzítése – számok azonosítása](record-file-format.md#identifying-tracks).                     |
| `K4A_IR_TRACK`               | Nincsenek               | N/A                                  | Lásd: a [fájl formátumának rögzítése – számok azonosítása](record-file-format.md#identifying-tracks).                     |
| `K4A_IMU_TRACK`              | Nincsenek               | N/A                                  | Lásd: a [fájl formátumának rögzítése – számok azonosítása](record-file-format.md#identifying-tracks).                     |

## <a name="timestamp-synchronization"></a>Időbélyeg-szinkronizálás

A Matroska formátumához a felvételeknek nulla időbélyeggel kell kezdődnie. A [kamerák külsőlegi szinkronizálásakor](record-external-synchronized-units.md)az egyes eszközök első időbélyege nem lehet nulla.

Az eszközökről a rögzítés és a lejátszás között az eredeti időbélyegek megőrzése érdekében a fájl egy eltolást tárol az időbélyegek esetében.

A `K4A_START_OFFSET_NS` címke egy időbélyeg-eltolás megadására szolgál, hogy a fájlok a rögzítés után újraszinkronizálhatók legyenek. Ez az időbélyeg-eltolás a fájl minden időbélyegéhez hozzáadható az eredeti eszköz időbélyegének újralétrehozásához.

A kezdeti eltolás a struct-ban is elérhető [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) .
