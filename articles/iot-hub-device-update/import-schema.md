---
title: Frissítések importálása az eszköz frissítése IoT Hub-Schema és egyéb információkhoz | Microsoft Docs
description: Séma és egyéb kapcsolódó információ (beleértve az objektumokat is), amelyeket a rendszer a frissítések importálásakor használ a IoT Hub eszköz frissítése során.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 989535d0bd6f514e63c7cea9e5fd71912f8fb08b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780157"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Frissítések importálása az eszköz frissítése IoT Hub-Schema és egyéb információk
Ha frissíteni szeretné a IoT Hub eszköz frissítését, először tekintse át a [fogalmakat](import-concepts.md) és az [útmutatókat](import-update.md) . Ha érdekli az importálási jegyzék létrehozásakor használt séma részletei, valamint a kapcsolódó objektumokkal kapcsolatos információk, lásd alább.

## <a name="import-manifest-schema"></a>Manifest-séma importálása

| Név | Típus | Leírás | Korlátozások |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` objektum | Identitás frissítése. |
| Frissítés típusa | sztring | Frissítés típusa: <br/><br/> * Itt adhatja meg, `microsoft/apt:1` Mikor hajtson végre egy Package-alapú frissítést a Reference Agent használatával.<br/> * Itt adhatja meg, `microsoft/swupdate:1` hogy mikor hajtson végre rendszerkép-alapú frissítést a Reference Agent használatával.<br/> * Itt adhatja meg `microsoft/simulator:1` a minta ügynök szimulátor használatát.<br/> * Egyéni típus megadásával egyéni ügynököt fejleszthet. | Formátum: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Maximális 32 karakter összesen |
| InstalledCriteria | sztring | Az ügynök által értelmezett karakterlánc annak megállapítására, hogy a frissítés sikeresen alkalmazva lett-e:  <br/> * Adja meg a SWVersion **értékét** a frissítési típushoz `microsoft/swupdate:1` .<br/> * Adja meg `{name}-{version}` a frissítési típust `microsoft/apt:1` , amelynek a nevét és verzióját az apt-fájlból kell beolvasni.<br/> * Egyéni karakterláncot egyedi ügynök létrehozásakor kell megadni.<br/> | Legfeljebb 64 karakter |
| Kompatibilitás | `CompatibilityInfo` [Objektumok](#compatibilityinfo-object) tömbje | A frissítéssel kompatibilis eszköz kompatibilitási információi. | Legfeljebb 10 elem |
| CreatedDateTime | dátum és idő | A frissítés létrehozásának dátuma és időpontja. | Tagolt ISO 8601 dátum és idő formátuma (UTC) |
| ManifestVersion | sztring | A jegyzékfájl-séma verziójának importálása. Megadható `2.0` , amely kompatibilis lesz a `urn:azureiot:AzureDeviceUpdateCore:1` csatolóval és az `urn:azureiot:AzureDeviceUpdateCore:4` adapterrel. | Kötelező `2.0` |
| Fájlok | Objektumok tömbje `File` | Hasznos adatok fájljainak frissítése | Legfeljebb 5 fájl |

## <a name="updateid-object"></a>UpdateId objektum

| Név | Típus | Leírás | Korlátozások |
| --------- | --------- | --------- | --------- |
| Szolgáltató | sztring | A frissítés identitásának szolgáltatói része. | 1-64 karakter, alfanumerikus, pont és kötőjel. |
| Name | sztring | A frissítés identitásának neve. | 1-64 karakter, alfanumerikus, pont és kötőjel. |
| Verzió | version | A frissítés identitásának verziója. | 2 – 4 rész, ponttal elválasztott verziószám 0 és 2147483647 között. A rendszer elveti a vezető nullákat. |

## <a name="file-object"></a>Fájl objektum

| Név | Típus | Leírás | Korlátozások |
| --------- | --------- | --------- | --------- |
| Filename | sztring | Fájl neve | Egyedinek kell lennie egy frissítésen belül |
| SizeInBytes | Int64 | A fájl mérete bájtban. | Legfeljebb 800 MB/egyedi fájl, vagy 800 MB, együttesen frissítés |
| Kivonatok | `Hashes` objektum | A fájl kivonatát (i) tartalmazó JSON-objektum |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo objektum

| Név | Típus | Leírás | Korlátozások |
| --- | --- | --- | --- |
| DeviceManufacturer | sztring | A frissítéssel kompatibilis eszköz gyártója. | 1-64 karakter, alfanumerikus, pont és kötőjel. |
| DeviceModel | sztring | Annak az eszköznek a modellje, amely kompatibilis a frissítéssel. | 1-64 karakter, alfanumerikus, pont és kötőjel. |

## <a name="hashes-object"></a>Kivonatok objektum

| Name | Kötelező | Típus | Leírás |
| --------- | --------- | --------- | --------- |
| Sha256 | True (Igaz) | sztring | A fájl Base64 kódolású kivonata az SHA-256 algoritmus használatával. |

## <a name="next-steps"></a>Következő lépések

További információ az [importálási fogalmakról](./import-concepts.md).

Ha elkészült, próbálja ki az [importálási How-To útmutatót](./import-update.md), amely végigvezeti az importálási folyamat lépésein.
